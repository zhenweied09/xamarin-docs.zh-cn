---
title: 第 5 部分。 从数据绑定到 MVVM
description: MVVM 模式强制执行三个软件层之间的分隔-XAML 用户界面，称为视图;基础数据，称为模型;并且在视图和模型之间的媒介调用视图模型。
ms.prod: xamarin
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: e9f38377299e8094162dd2d2d73174e6f2b40369
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245766"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>第 5 部分。 从数据绑定到 MVVM

_记住，模型-视图-视图模型 (MVVM) 体系结构模式就是使用 XAML。模式强制执行三个软件层之间的分隔-XAML 用户界面，称为视图;基础数据，称为模型;并且在视图和模型之间的媒介调用视图模型。视图和视图模型，以及经常连接通过在 XAML 文件中定义的数据绑定中。为视图 BindingContext 通常是 ViewModel 的实例。_

## <a name="a-simple-viewmodel"></a>简单视图模型

作为 Viewmodel 的简介，让我们先来看一下没有程序。
前面你已了解如何定义新的 XML 命名空间声明，以允许的 XAML 文件，以便在其他程序集的引用类。 此处是一个程序，定义的 XML 命名空间声明`System`命名空间：

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

此程序可以使用`x:Static`以获取当前日期和时间从静态`DateTime.Now`属性和设置，`DateTime`值赋给`BindingContext`上`StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` 是一个非常特殊属性： 当你将设置`BindingContext`在元素上，它由该元素的所有子项中继承。 这意味着所有的子级`StackLayout`具有此相同的`BindingContext`，并且它们可以包含对该对象的属性的简单绑定。

在**One-Shot DateTime**程序中，两个子级包含到该属性的绑定`DateTime`值，但两个其他的子级包含似乎缺少绑定路径的绑定。 这意味着，`DateTime`本身的值用于`StringFormat`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="XamlSamples.OneShotDateTimePage"
             Title="One-Shot DateTime Page">

    <StackLayout BindingContext="{x:Static sys:DateTime.Now}"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">

        <Label Text="{Binding Year, StringFormat='The year is {0}'}" />
        <Label Text="{Binding StringFormat='The month is {0:MMMM}'}" />
        <Label Text="{Binding Day, StringFormat='The day is {0}'}" />
        <Label Text="{Binding StringFormat='The time is {0:T}'}" />

    </StackLayout>
</ContentPage>
```

当然，大的问题是，日期和时间设置了后第一次生成页面时，以及永远不会更改：

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "视图显示日期和时间")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "视图显示日期和时间")

XAML 文件可以显示时钟始终会显示当前时间，但它需要一些代码以帮助解决问题。当考虑 MVVM、 模型和视图模型是完全用代码编写的类。 通常，视图不引用通过数据绑定在视图模型中定义的属性的 XAML 文件。

正确的模型并不了解视图模型，并正确 ViewModel 是未知的视图。 但是，程序员非常频繁修改了到特定的用户界面与关联的数据类型 ViewModel 公开的数据类型。 例如，如果模型访问数据库，其中包含 8 位字符的 ASCII 字符串时，视图模型将需要这些字符串转换为 Unicode 字符串，以适应独占使用的用户界面中的 Unicode 之间转换。

在简单示例中的 MVVM （例如，此处所示），通常没有模型，和的模式涉及不仅可以看到和 ViewModel 链接使用数据绑定。

下面是与只是名为的单个属性时钟 ViewModel `DateTime`，这样可以更新，但`DateTime`属性每秒：

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    class ClockViewModel : INotifyPropertyChanged
    {
        DateTime dateTime;

        public event PropertyChangedEventHandler PropertyChanged;

        public ClockViewModel()
        {
            this.DateTime = DateTime.Now;

            Device.StartTimer(TimeSpan.FromSeconds(1), () =>
                {
                    this.DateTime = DateTime.Now;
                    return true;
                });
        }

        public DateTime DateTime
        {
            set
            {
                if (dateTime != value)
                {
                    dateTime = value;

                    if (PropertyChanged != null)
                    {
                        PropertyChanged(this, new PropertyChangedEventArgs("DateTime"));
                    }
                }
            }
            get
            {
                return dateTime;
            }
        }
    }
}
```

通常实现 Viewmodel`INotifyPropertyChanged`接口，这意味着在类触发`PropertyChanged`事件的一个属性发生更改时。 Xamarin.Forms 中的数据绑定机制将处理程序附加到此`PropertyChanged`事件，它可以向其通报属性变化时并保留目标具有新值更新。

基于此 ViewModel 时钟可以是简单，如下：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ClockPage"
             Title="Clock Page">

    <Label Text="{Binding DateTime, StringFormat='{0:T}'}"
           FontSize="Large"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Label.BindingContext>
            <local:ClockViewModel />
        </Label.BindingContext>
    </Label>
</ContentPage>
```

请注意如何`ClockViewModel`设置为`BindingContext`的`Label`使用属性元素标记。 或者，你可以实例化`ClockViewModel`中`Resources`集合并将其设置为`BindingContext`通过`StaticResource`标记扩展。 或者，代码隐藏文件可以实例化视图模型。

`Binding`上的标记扩展`Text`属性`Label`格式`DateTime`属性。 下面是显示：

[![](data-bindings-to-mvvm-images/clock.png "视图显示日期和时间通过 ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "视图显示日期和时间通过视图模型")

还有可能访问各个属性`DateTime`ViewModel 用句点分隔各个属性的属性：

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>交互式 MVVM

MVVM 常常用于基于基础数据模型的交互式视图的双向数据绑定。

下面是一个名为类`HslViewModel`，用于将转换`Color`值到`Hue`， `Saturation`，和`Luminosity`值，反之亦然：

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    public class HslViewModel : INotifyPropertyChanged
    {
        double hue, saturation, luminosity;
        Color color;

        public event PropertyChangedEventHandler PropertyChanged;

        public double Hue
        {
            set
            {
                if (hue != value)
                {
                    hue = value;
                    OnPropertyChanged("Hue");
                    SetNewColor();
                }
            }
            get
            {
                return hue;
            }
        }

        public double Saturation
        {
            set
            {
                if (saturation != value)
                {
                    saturation = value;
                    OnPropertyChanged("Saturation");
                    SetNewColor();
                }
            }
            get
            {
                return saturation;
            }
        }

        public double Luminosity
        {
            set
            {
                if (luminosity != value)
                {
                    luminosity = value;
                    OnPropertyChanged("Luminosity");
                    SetNewColor();
                }
            }
            get
            {
                return luminosity;
            }
        }

        public Color Color
        {
            set
            {
                if (color != value)
                {
                    color = value;
                    OnPropertyChanged("Color");

                    Hue = value.Hue;
                    Saturation = value.Saturation;
                    Luminosity = value.Luminosity;
                }
            }
            get
            {
                return color;
            }
        }

        void SetNewColor()
        {
            Color = Color.FromHsla(Hue, Saturation, Luminosity);
        }

        protected virtual void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

更改为`Hue`， `Saturation`，和`Luminosity`属性原因`Color`属性可以更改，并对更改`Color`会导致其他三个属性来更改。 这可能看起来像一个无限循环，只不过类不调用`PropertyChanged`事件除非实际上已更改的属性。 这会将否则为无法控制的反馈循环结束。

下面的 XAML 文件包含`BoxView`其`Color`属性绑定到`Color`ViewModel 和第三个属性`Slider`和三个`Label`视图绑定到`Hue`， `Saturation`，和`Luminosity`属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.HslColorScrollPage"
             Title="HSL Color Scroll Page">
    <ContentPage.BindingContext>
        <local:HslViewModel Color="Aqua" />
    </ContentPage.BindingContext>

    <StackLayout Padding="10, 0">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Hue, Mode=TwoWay}" />

        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Saturation, Mode=TwoWay}" />

        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Luminosity, Mode=TwoWay}" />
    </StackLayout>
</ContentPage>
```

在每个绑定`Label`是默认设置`OneWay`。 它只需显示的值。 但在每个绑定`Slider`是`TwoWay`。 这允许`Slider`从 ViewModel 初始化。 请注意，`Color`属性设置为`Blue`ViewModel 实例化时。 但在更改`Slider`还需要在 ViewModel，然后计算一种新颜色中设置属性的新值。

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "使用双向数据绑定的 MVVM")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM 使用双向数据绑定")

## <a name="commanding-with-viewmodels"></a>与 Viewmodel 命令

在许多情况下，MVVM 模式被限制为数据项的操作： 在视图中的用户界面对象的并行视图模型中的数据对象。

但是，有时需要视图包含触发视图模型中的各种操作的按钮。 但不是能包含 ViewModel`Clicked`按钮处理程序，将绑定到特定的用户界面范例 ViewModel 因为。

若要允许 Viewmodel 更独立于特定的用户界面对象，但仍允许方法内 ViewModel，调用*命令*接口存在。 此命令界面受 Xamarin.Forms 中的下列元素：

-  `Button`
-  `MenuItem`
-  `ToolbarItem`
-  `SearchBar`
-  `TextCell` (因此也`ImageCell`)
-  `ListView`
-  `TapGestureRecognizer`

除`SearchBar`和`ListView`元素，这些元素定义两个属性：

-  `Command` 类型  `System.Windows.Input.ICommand`
-  `CommandParameter` 类型  `Object`

`SearchBar`定义`SearchCommand`和`SearchCommandParameter`属性，而`ListView`定义`RefreshCommand`类型的属性`ICommand`。

`ICommand`接口定义两个方法和一个事件：

-  `void Execute(object arg)`
-  `bool CanExecute(object arg)`
-  `event EventHandler CanExecuteChanged`

视图模型可以定义类型的属性`ICommand`。 然后可以将绑定到这些属性`Command`每个属性`Button`或其他元素或可能的实现此接口的自定义的视图。 你可以选择性地设置`CommandParameter`属性来标识各个`Button`对象 （或其他元素），绑定到此视图模型属性。 在内部，`Button`调用`Execute`方法每当用户点击`Button`，并传递到`Execute`方法其`CommandParameter`。

`CanExecute`方法和`CanExecuteChanged`事件的情况下使用其中`Button`tap 也可能是当前无效，在这种情况下`Button`应禁用其自身。 `Button`调用`CanExecute`时`Command`首先设置属性和每当`CanExecuteChanged`激发事件。 如果`CanExecute`返回`false`、`Button`禁用其自身并不会生成`Execute`调用。

对于添加到你 Viewmodel 命令的帮助，Xamarin.Forms 定义两个类实现`ICommand`:`Command`和`Command<T>`其中`T`是函数的参数类型`Execute`和`CanExecute`。 这两个类定义多个构造函数加上`ChangeCanExecute`方法视图模型可以调用以强制`Command`对象激发`CanExecuteChanged`事件。

下面是一个简单的键盘，它用于输入电话号码 ViewModel。 请注意，`Execute`和`CanExecute`方法被定义为构造函数中的 lambda 函数权限：

```csharp
using System;
using System.ComponentModel;
using System.Windows.Input;
using Xamarin.Forms;

namespace XamlSamples
{
    class KeypadViewModel : INotifyPropertyChanged
    {
        string inputString = "";
        string displayText = "";
        char[] specialChars = { '*', '#' };

        public event PropertyChangedEventHandler PropertyChanged;

        // Constructor
        public KeypadViewModel()
        {
            AddCharCommand = new Command<string>((key) =>
                {
                    // Add the key to the input string.
                    InputString += key;
                });

            DeleteCharCommand = new Command(() =>
                {
                    // Strip a character from the input string.
                    InputString = InputString.Substring(0, InputString.Length - 1);
                },
                () =>
                {
                    // Return true if there's something to delete.
                    return InputString.Length > 0;
                });
        }

        // Public properties
        public string InputString
        {
            protected set
            {
                if (inputString != value)
                {
                    inputString = value;
                    OnPropertyChanged("InputString");
                    DisplayText = FormatText(inputString);

                    // Perhaps the delete button must be enabled/disabled.
                    ((Command)DeleteCharCommand).ChangeCanExecute();
                }
            }

            get { return inputString; }
        }

        public string DisplayText
        {
            protected set
            {
                if (displayText != value)
                {
                    displayText = value;
                    OnPropertyChanged("DisplayText");
                }
            }
            get { return displayText; }
        }

        // ICommand implementations
        public ICommand AddCharCommand { protected set; get; }

        public ICommand DeleteCharCommand { protected set; get; }

        string FormatText(string str)
        {
            bool hasNonNumbers = str.IndexOfAny(specialChars) != -1;
            string formatted = str;

            if (hasNonNumbers || str.Length < 4 || str.Length > 10)
            {
            }
            else if (str.Length < 8)
            {
                formatted = String.Format("{0}-{1}",
                                          str.Substring(0, 3),
                                          str.Substring(3));
            }
            else
            {
                formatted = String.Format("({0}) {1}-{2}",
                                          str.Substring(0, 3),
                                          str.Substring(3, 3),
                                          str.Substring(6));
            }
            return formatted;
        }

        protected void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

此视图模型假定`AddCharCommand`属性绑定到`Command`属性的多个按钮 （或任何其他内容具有命令界面），其中每个由标识`CommandParameter`。 这些按钮添加到字符`InputString`属性，然后设置为用于的电话号码的格式`DisplayText`属性。

另外，还有第二个类型的属性`ICommand`名为`DeleteCharCommand`。 这绑定到的后间距按钮，但如果没有要删除的字符，则应禁用按钮。

不是为直观地复杂以下键盘，因为它可能是。 相反，标记已减至最小值来演示更清楚地了解命令界面的使用：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.KeypadPage"
             Title="Keypad Page">

    <Grid HorizontalOptions="Center"
          VerticalOptions="Center">
        <Grid.BindingContext>
            <local:KeypadViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
        </Grid.ColumnDefinitions>

        <!-- Internal Grid for top row of items -->
        <Grid Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="Auto" />
            </Grid.ColumnDefinitions>

            <Frame Grid.Column="0"
                   OutlineColor="Accent">
                <Label Text="{Binding DisplayText}" />
            </Frame>

            <Button Text="&#x21E6;"
                    Command="{Binding DeleteCharCommand}"
                    Grid.Column="1"
                    BorderWidth="0" />
        </Grid>

        <Button Text="1"
                Command="{Binding AddCharCommand}"
                CommandParameter="1"
                Grid.Row="1" Grid.Column="0" />

        <Button Text="2"
                Command="{Binding AddCharCommand}"
                CommandParameter="2"
                Grid.Row="1" Grid.Column="1" />

        <Button Text="3"
                Command="{Binding AddCharCommand}"
                CommandParameter="3"
                Grid.Row="1" Grid.Column="2" />

        <Button Text="4"
                Command="{Binding AddCharCommand}"
                CommandParameter="4"
                Grid.Row="2" Grid.Column="0" />

        <Button Text="5"
                Command="{Binding AddCharCommand}"
                CommandParameter="5"
                Grid.Row="2" Grid.Column="1" />

        <Button Text="6"
                Command="{Binding AddCharCommand}"
                CommandParameter="6"
                Grid.Row="2" Grid.Column="2" />

        <Button Text="7"
                Command="{Binding AddCharCommand}"
                CommandParameter="7"
                Grid.Row="3" Grid.Column="0" />

        <Button Text="8"
                Command="{Binding AddCharCommand}"
                CommandParameter="8"
                Grid.Row="3" Grid.Column="1" />

        <Button Text="9"
                Command="{Binding AddCharCommand}"
                CommandParameter="9"
                Grid.Row="3" Grid.Column="2" />

        <Button Text="*"
                Command="{Binding AddCharCommand}"
                CommandParameter="*"
                Grid.Row="4" Grid.Column="0" />

        <Button Text="0"
                Command="{Binding AddCharCommand}"
                CommandParameter="0"
                Grid.Row="4" Grid.Column="1" />

        <Button Text="#"
                Command="{Binding AddCharCommand}"
                CommandParameter="#"
                Grid.Row="4" Grid.Column="2" />
    </Grid>
</ContentPage>
```

`Command`属性的第一个`Button`出现在此标记绑定到`DeleteCharCommand`; rest 绑定到`AddCharCommand`与`CommandParameter`显示的字符，它是相同`Button`表面。 下面是在操作中的程序：

[![](data-bindings-to-mvvm-images/keypad.png "使用 MVVM 和命令的计算器")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "计算器使用 MVVM 和命令")

### <a name="invoking-asynchronous-methods"></a>调用异步方法

命令也可以调用异步方法。 这通过使用`async`和`await`关键字指定时`Execute`方法：

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

这指示`DownloadAsync`方法是`Task`和应等待：

```csharp
async Task DownloadAsync ()
{
    await Task.Run (() => Download ());
}

void Download ()
{
    ...
}
```

## <a name="implementing-a-navigation-menu"></a>实现导航菜单

[XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)包含此系列文章中的所有源代码的程序将视图模型用于其主页。 此视图模型是短类具有名为的三个属性的定义`Type`， `Title`，和`Description`的包含类型的每个示例页、 标题和简短说明。 此外，ViewModel 定义名为的静态属性`All`，它是在程序中的所有页的集合：

```csharp
public class PageDataViewModel
{
    public PageDataViewModel(Type type, string title, string description)
    {
        Type = type;
        Title = title;
        Description = description;
    }

    public Type Type { private set; get; }

    public string Title { private set; get; }

    public string Description { private set; get; }

    static PageDataViewModel()
    {
        All = new List<PageDataViewModel>
        {
            // Part 1. Getting Started with XAML
            new PageDataViewModel(typeof(HelloXamlPage), "Hello, XAML",
                                  "Display a Label with many properties set"),

            new PageDataViewModel(typeof(XamlPlusCodePage), "XAML + Code",
                                  "Interact with a Slider and Button"),

            // Part 2. Essential XAML Syntax
            new PageDataViewModel(typeof(GridDemoPage), "Grid Demo",
                                  "Explore XAML syntax with the Grid"),

            new PageDataViewModel(typeof(AbsoluteDemoPage), "Absolute Demo",
                                  "Explore XAML syntax with AbsoluteLayout"),

            // Part 3. XAML Markup Extensions
            new PageDataViewModel(typeof(SharedResourcesPage), "Shared Resources",
                                  "Using resource dictionaries to share resources"),

            new PageDataViewModel(typeof(StaticConstantsPage), "Static Constants",
                                  "Using the x:Static markup extensions"),

            new PageDataViewModel(typeof(RelativeLayoutPage), "Relative Layout",
                                  "Explore XAML markup extensions"),

            // Part 4. Data Binding Basics
            new PageDataViewModel(typeof(SliderBindingsPage), "Slider Bindings",
                                  "Bind properties of two views on the page"),

            new PageDataViewModel(typeof(SliderTransformsPage), "Slider Transforms",
                                  "Use Sliders with reverse bindings"),

            new PageDataViewModel(typeof(ListViewDemoPage), "ListView Demo",
                                  "Use a ListView with data bindings"),

            // Part 5. From Data Bindings to MVVM
            new PageDataViewModel(typeof(OneShotDateTimePage), "One-Shot DateTime",
                                  "Obtain the current DateTime and display it"),

            new PageDataViewModel(typeof(ClockPage), "Clock",
                                  "Dynamically display the current time"),

            new PageDataViewModel(typeof(HslColorScrollPage), "HSL Color Scroll",
                                  "Use a view model to select HSL colors"),

            new PageDataViewModel(typeof(KeypadPage), "Keypad",
                                  "Use a view model for numeric keypad logic")
        };
    }

    public static IList<PageDataViewModel> All { private set; get; }
}
```

XAML 文件中的为`MainPage`定义`ListBox`其`ItemsSource`属性设置为，`All`属性，它包含`TextCell`用于显示`Title`和`Description`每一页的属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage"
             Padding="5, 0"
             Title="XAML Samples">

    <ListView ItemsSource="{x:Static local:PageDataViewModel.All}"
              ItemSelected="OnListViewItemSelected">
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding Title}"
                          Detail="{Binding Description}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

页可滚动列表所示：

[![](data-bindings-to-mvvm-images/mainpage.png "可滚动的页列表")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "的页的可滚动列表")

当用户选择某个项时触发的代码隐藏文件中的处理程序。 处理程序集`SelectedItem`属性`ListBox`回`null`，实例化所选的页面，然后导航到它：

```csharp
private async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
{
    (sender as ListView).SelectedItem = null;

    if (args.SelectedItem != null)
    {
        PageDataViewModel pageData = args.SelectedItem as PageDataViewModel;
        Page page = (Page)Activator.CreateInstance(pageData.Type);
        await Navigation.PushAsync(page);
    }
}
```

## <a name="video"></a>视频

> [!VIDEO https://youtube.com/embed/DYRLcqG2BAY]

**可以轻松地 Xamarin.Forms 和 Prism Xamarin 发展 2016年: MVVM**

## <a name="summary"></a>总结

XAML 是一个强大的工具，用于定义在 Xamarin.Forms 应用程序，特别是在数据绑定中的用户界面和使用 MVVM。 结果是在代码中的所有后台支持用户界面表示干净、 简洁，并且可能非常有用。


## <a name="related-links"></a>相关链接

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 2 部分：基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 3 部分：XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 4 部分：数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
