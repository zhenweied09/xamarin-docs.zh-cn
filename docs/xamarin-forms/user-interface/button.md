---
title: Xamarin.Forms 按钮
description: 按钮响应点击或单击指示应用程序来执行特定任务。
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/01/2018
ms.openlocfilehash: 095736e77b2f502261f9b85ab73c45dce74309b9
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34734123"
---
# <a name="xamarinforms-button"></a>Xamarin.Forms 按钮

_按钮响应点击或单击指示应用程序来执行特定任务。_ 

[ `Button` ](xref:Xamarin.Forms.Button)是最基本的交互式控件中所有 Xamarin.Forms。 `Button`通常显示短文本字符串，该值指示一个命令，但它还可以显示位图图像，或组合文本和图像。 用户按可实现`Button`用手指或单击使用鼠标来启动该命令。

大部分下面讨论的主题中的页面对应[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)示例。

## <a name="handling-button-clicks"></a>处理按钮单击

`Button` 定义[ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)当用户点击时触发的事件`Button`通过手指或鼠标指针。 图面中释放手指或鼠标按钮时激发事件`Button`。 `Button`必须具有其[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)属性设置为`true`才能到 tap 做出响应。 

**基本按钮单击**页面[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)示例演示如何实例化`Button`XAML 和句柄中其`Clicked`事件。 **BasicButtonClickPage.xaml**文件包含`StackLayout`两种版本同时`Label`和`Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.BasicButtonClickPage"
             Title="Basic Button Click">
    <StackLayout>
        
        <Label x:Name="label"
               Text="Click the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand" 
               HorizontalOptions="Center" />

        <Button Text="Click to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Clicked="OnButtonClicked" />
     
    </StackLayout>
</ContentPage>
```

`Button`倾向于占用所有为它所允许的空间。 例如，如果未设置`HorizontalOptions`属性`Button`到以外的其他`Fill`、`Button`占用其父级的整个宽度。

默认情况下，`Button`是矩形，但可以通过使用进行舍入的 it 角[ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius)属性，如下所述的部分中[**按钮外观**](#button-appearance).

[ `Text` ](xref:Xamarin.Forms.Button.Text)属性指定在显示的文本`Button`。 [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)事件设置事件处理程序命名为`OnButtonClicked`。 在代码隐藏文件中，找到此处理程序**BasicButtonClickPage.xaml.cs**:

```csharp
public partial class BasicButtonClickPage : ContentPage
{
    public BasicButtonClickPage ()
    {
        InitializeComponent ();
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        await label.RelRotateTo(360, 1000);
    }
}
```

当`Button`点击，`OnButtonClicked`方法执行。 `sender`自变量是`Button`负责此事件的对象。 可以使用此访问`Button`对象，或来区分多个`Button`对象共享相同`Clicked`事件。

此特定`Clicked`处理程序调用旋转动画函数`Label`1000年毫秒的 360 度。 下面是在 Windows 10 桌面版上运行 iOS 和 Android 设备上和作为通用 Windows 平台 (UWP) 应用程序的程序：

[![基本按钮单击](button-images/BasicButtonClick.png "基本按钮单击")](button-images/BasicButtonClick-Large.png#lightbox "基本按钮单击")

请注意，`OnButtonClicked`方法包括`async`修饰符因为`await`在事件处理程序内使用。 A`Clicked`事件处理程序需要`async`修饰符仅当处理程序的主体使用`await`。

每个平台呈现`Button`自己特定的方式。 在[**按钮外观**](#button-appearance)部分中，你将了解如何设置颜色并使`Button`边框可见的更多自定义外观。 `Button` 实现[ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement)接口，使其包含[ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily)， [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize)，和[ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)属性。

## <a name="creating-a-button-in-code"></a>在代码中创建按钮

经常可以实例化`Button`在 XAML 中，但也可以创建`Button`在代码中。 这一点可能很方便，你的应用程序需要创建基于是与可枚举的数据的多个按钮时`foreach`循环。

**代码按钮单击**页演示如何创建的网页的功能上等效于**基本按钮单击**但完全在 C# 中的页：

```csharp
public class CodeButtonClickPage : ContentPage
{
    public CodeButtonClickPage ()
    {
        Title = "Code Button Click";

        Label label = new Label
        {
            Text = "Click the Button below",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };

        Button button = new Button
        {
            Text = "Click to Rotate Text!",
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };
        button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);

        Content = new StackLayout
        {
            Children =
            {
                label,
                button
            }
        };
    }
}
```

任何事情都应在类的构造函数。 因为`Clicked`处理程序长只有一条语句，则可以将它附加到的事件非常简单：

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

当然，你还可以定义事件处理程序为一个单独的方法 (就像`OnButtonClick`中的方法**基本按钮单击**)，并将该方法附加到的事件：

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>禁用按钮

有时应用程序处于特定状态的位置特定`Button`单击不是有效的操作。 在这些情况下，`Button`应禁用通过设置其`IsEnabled`属性`false`。 典型的示例是`Entry`伴随打开的文件的文件名的控件`Button`:`Button`键入一些文本的情况下，才应启用`Entry`。 你可以使用`DataTrigger`对于此任务，如中所示[**数据触发器**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers)文章。

## <a name="using-the-command-interface"></a>使用命令界面

可以为应用程序响应`Button`而无需处理的分流`Clicked`事件。 `Button`实现调用了备用通知机制_命令_或_命令_接口。 这包含两个属性：

- [`Command`](xref:Xamarin.Forms.Button.Command) 类型的[ `ICommand` ](xref:System.Windows.Input.ICommand)，接口中定义[ `System.Windows.Input` ](xref:System.Windows.Input)命名空间。
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) 类型的属性[ `Object` ](xref:System.Object)。

尤其是在实现模型-视图-视图模型 (MVVM) 体系结构时，此方法很与数据绑定和特别适用。 文章中讨论了这些主题[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)，[从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)，和[MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)。

MVVM 应用程序，在视图模型定义类型的属性`ICommand`，然后连接到 XAML`Button`使用数据绑定的元素。 Xamarin.Forms 还定义[ `Command` ]((xref:Xamarin.Forms.Command`1))和[ `Command<T>` ](xref:Xamarin.Forms.Command`1)的类，该实现`ICommand`接口，并定义属性类型帮助ViewModel`ICommand`.

中的文章中的更详细地介绍了命令[**命令界面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)但**基本按钮命令**页面[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)示例演示基本的方法。

`CommandDemoViewModel`类是定义类型的属性非常简单 ViewModel`double`名为`Number`，和类型的两个属性`ICommand`名为`MultiplyBy2Command`和`DivideBy2Command`:

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    double number = 1;

    public event PropertyChangedEventHandler PropertyChanged;

    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(() => Number *= 2);

        DivideBy2Command = new Command(() => Number /= 2);
    }

    public double Number
    {
        set
        {
            if (number != value)
            {
                number = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Number"));
            }
        }
        get
        {
            return number;
        }
    }

    public ICommand MultiplyBy2Command { private set; get; }

    public ICommand DivideBy2Command { private set; get; }
}
```

这两个`ICommand`与类型的两个对象的类的构造函数中初始化属性`Command`。 `Command`构造函数包括一些函数 (称为`execute`构造函数自变量) 的两倍，或减半`Number`属性。

**BasicButtonCommand.xaml**文件中设置其`BindingContext`到实例`CommandDemoViewModel`。 `Label`元素和第二个`Button`元素包含到中的三个属性的绑定`CommandDemoViewModel`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.BasicButtonCommandPage"
             Title="Basic Button Command">
    
    <ContentPage.BindingContext>
        <local:CommandDemoViewModel />
    </ContentPage.BindingContext>
    
    <StackLayout>
        <Label Text="{Binding Number, StringFormat='Value is now {0}'}"
               FontSize="Large"
               VerticalOptions="CenterAndExpand" 
               HorizontalOptions="Center" />

        <Button Text="Multiply by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding MultiplyBy2Command}" />

        <Button Text="Divide by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding DivideBy2Command}" />
    </StackLayout>
</ContentPage>
```

两`Button`点击元素、 执行命令，并更改值的数量：

[![基本按钮命令](button-images/BasicButtonCommand.png "基本按钮命令")](button-images/BasicButtonCommand-Large.png#lightbox)

通过此方法的优点`Clicked`处理程序是，涉及此页的功能的所有逻辑都位于在视图模型，而不是代码隐藏文件中，实现更好的隔离的用户界面与业务逻辑。

也可能是`Command`对象以控制启用和禁用`Button`元素。 例如，假设你想要限制 2 之间的数字值的范围<sup>10</sup>和第 2<sup>&ndash;10</sup>。 你可以将另一个函数添加到构造函数 (称为`canExecute`自变量)，该属性返回`true`如果`Button`应启用。 下面是对修改`CommandDemoViewModel`构造函数：

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    ···
    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(
            execute: () =>
            {
                Number *= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number < Math.Pow(2, 10));

        DivideBy2Command = new Command(
            execute: () =>
            {
                Number /= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number > Math.Pow(2, -10));
    }
    ···
}
```

对的调用`ChangeCanExecute`方法`Command`有必须使用以便`Command`方法可以调用`canExecute`方法，并确定是否`Button`或不应禁用。 此代码的更改，为数达到限制时，`Button`处于禁用状态：

[![基本按钮命令-修改](button-images/BasicButtonCommandModified.png "基本按钮命令-修改")](button-images/BasicButtonCommandModified-Large.png#lightbox)

可以为两个或多个`Button`元素绑定到相同`ICommand`属性。 `Button`可以使用可分辨元素[ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter)属性`Button`。 在这种情况下，你将想要使用泛型[ `Command<T>` ](xref:Xamarin.Forms.Command`1)类。 `CommandParameter`对象随后会传递的自变量作为`execute`和`canExecute`方法。 中详细地演示此技术[**基本命令**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)部分[**命令界面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)文章。

[ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)示例还使用此方法在其`MainPage`类。 **MainPage.xaml**文件包含`Button`示例的每一页：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.MainPage"
             Title="Button Demos">
    <ScrollView>
        <FlexLayout Direction="Column"
                    JustifyContent="SpaceEvenly"
                    AlignItems="Center">

            <Button Text="Basic Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonClickPage}" />

            <Button Text="Code Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:CodeButtonClickPage}" />

            <Button Text="Basic Button Command"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonCommandPage}" />

            <Button Text="Press and Release Button"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:PressAndReleaseButtonPage}" />

            <Button Text="Button Appearance"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ButtonAppearancePage}" />

            <Button Text="Toggle Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ToggleButtonDemoPage}" />

            <Button Text="Image Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ImageButtonDemoPage}" />

        </FlexLayout>
    </ScrollView>
</ContentPage>
```

每个`Button`具有其`Command`属性绑定到名为的属性`NavigateCommand`，和`CommandParameter`设置为[ `Type` ](xref:System.Type)对应于一个项目中的页类的对象。

`NavigateCommand`属性属于类型`ICommand`和代码隐藏文件中定义：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

构造函数初始化`NavigateCommand`属性`Command<Type>`对象，因为`Type`是一种`CommandParameter`在 XAML 文件中设置的对象。 这意味着，`execute`方法具有类型自变量`Type`，对应于此`CommandParameter`对象。 该函数实例化页，然后导航到它。

请注意，构造函数到结束通过设置其`BindingContext`到其自身。 这对于将绑定到 XAML 文件中的属性是必需`NavigateCommand`属性。

## <a name="pressing-and-releasing-the-button"></a>按下和松开按钮

除了`Clicked`事件，`Button`还定义[ `Pressed` ](xref:Xamarin.Forms.Button.Pressed)和[ `Released` ](xref:Xamarin.Forms.Button.Released)事件。 `Pressed`事件发生时按下上的手指`Button`，或使用指针定位在按下鼠标按钮`Button`。 `Released`释放手指或鼠标按钮时发生事件。 通常情况下，`Clicked`事件还在同一时间时引发`Released`事件，但如果手指或鼠标指针滑离开的图面`Button`之前被释放，`Clicked`事件可能会发生。

`Pressed`和`Released`事件不常使用，但它们可以用于执行的特殊目的，如中所示**按下并释放按钮**页。 XAML 文件中包含`Label`和`Button`为附加处理程序与`Pressed`和`Released`事件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.PressAndReleaseButtonPage"
             Title="Press and Release Button">
    <StackLayout>

        <Label x:Name="label"
               Text="Press and hold the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand" 
               HorizontalOptions="Center" />

        <Button Text="Press to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Pressed="OnButtonPressed"
                Released="OnButtonReleased" />

    </StackLayout>
</ContentPage>
```

代码隐藏文件进行动画处理`Label`时`Pressed`事件发生，但将挂起旋转时`Released`发生事件：

```csharp
public partial class PressAndReleaseButtonPage : ContentPage
{
    bool animationInProgress = false;
    Stopwatch stopwatch = new Stopwatch();

    public PressAndReleaseButtonPage ()
    {
        InitializeComponent ();
    }

    void OnButtonPressed(object sender, EventArgs args)
    {
        stopwatch.Start();
        animationInProgress = true;

        Device.StartTimer(TimeSpan.FromMilliseconds(16), () =>
        {
            label.Rotation = 360 * (stopwatch.Elapsed.TotalSeconds % 1);

            return animationInProgress;
        });
    }

    void OnButtonReleased(object sender, EventArgs args)
    {
        animationInProgress = false;
        stopwatch.Stop();
    }
}
```

结果是，`Label`仅旋转手指时与联系`Button`，并在发布上方的手指时停止：

[![按下并释放按钮](button-images/PressAndReleaseButton.png "按下并释放按钮")](button-images/PressAndReleaseButton-Large.png)

这种行为不包含应用程序针对的游戏： 上保留的手指`Button`可能会使在屏幕对象按特定方向移动。 

<a name="button-appearance" />

## <a name="button-appearance"></a>按钮外观

`Button`继承或定义会影响其外观的多个属性：

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) 是的颜色`Button`文本
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 是对该文本的颜色
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) 为区域周围的颜色 `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) 使用文本的字体系列
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) 是大小
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) 指示文本是否为斜体或加粗
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) 是宽度 
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) 为圆角

六个这些属性的效果 (不包括`FontFamily`和`FontAttributes`) 所示**按钮外观**页。 另一个属性， [ `Image` ](xref:Xamarin.Forms.Button.Image)，节中讨论[**位图使用按钮**](#image-button)。

中的视图和数据绑定的所有**按钮外观**XAML 文件中定义页：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ButtonAppearancePage"
             Title="Button Appearance">
    <StackLayout>
        <Button x:Name="button"
                Text="Button"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                TextColor="{Binding Source={x:Reference textColorPicker},
                                    Path=SelectedItem.Color}"
                BackgroundColor="{Binding Source={x:Reference backgroundColorPicker},
                                          Path=SelectedItem.Color}"
                BorderColor="{Binding Source={x:Reference borderColorPicker},
                                      Path=SelectedItem.Color}" />

        <StackLayout BindingContext="{x:Reference button}"
                     Padding="10">
            
            <Slider x:Name="fontSizeSlider"
                    Maximum="48"
                    Minimum="1"
                    Value="{Binding FontSize}" />

            <Label Text="{Binding Source={x:Reference fontSizeSlider},
                                  Path=Value,
                                  StringFormat='FontSize = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="borderWidthSlider"
                    Minimum="-1"
                    Maximum="12"
                    Value="{Binding BorderWidth}" />
            
            <Label Text="{Binding Source={x:Reference borderWidthSlider}, 
                                  Path=Value,
                                  StringFormat='BorderWidth = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="cornerRadiusSlider"
                    Minimum="-1"
                    Maximum="24"
                    Value="{Binding CornerRadius}" />

            <Label Text="{Binding Source={x:Reference cornerRadiusSlider}, 
                                  Path=Value,
                                  StringFormat='CornerRadius = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Grid.Resources>
                    <Style TargetType="Label">
                        <Setter Property="VerticalOptions" Value="Center" />
                    </Style>
                </Grid.Resources>

                <Label Text="Text Color:"
                       Grid.Row="0" Grid.Column="0" />

                <Picker x:Name="textColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="0" Grid.Column="1" />

                <Label Text="Background Color:"
                       Grid.Row="1" Grid.Column="0" />

                <Picker x:Name="backgroundColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="1" Grid.Column="1" />

                <Label Text="Border Color:"
                       Grid.Row="2" Grid.Column="0" />

                <Picker x:Name="borderColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="2" Grid.Column="1" />
            </Grid>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

`Button`时页面顶部有其三个`Color`属性绑定到`Picker`在页面底部的元素。 中的项`Picker`元素是中的颜色`NamedColor`项目中包含的类。 三个`Slider`元素包含到双向绑定`FontSize`， `BorderWidth`，和`CornerRadius`属性`Button`。

此程序可以尝试使用所有这些属性的组合：

[![按钮外观](button-images/ButtonAppearance.png "按钮外观")](button-images/ButtonAppearance-Large.png)

若要查看`Button`边框，你将需要设置`BorderColor`到以外的其他`Default`，和`BorderWidth`设为正值。

在 iOS 上你会注意到大型的边框宽度入侵到的内部`Button`，会影响的文本的显示。 如果你选择要用于 iOS 的边框`Button`，你将可能想要开始和结束`Text`与空间结合可保留其可见性属性。

在 UWP，选择`CornerRadius`超过高度的一半`Button`引发异常。

## <a name="creating-a-toggle-button"></a>创建切换按钮

可以子类化`Button`，以便其工作原理类似的打开-关闭开关： 点击按钮一次以上切换按钮，然后点击它再次以它关闭。

以下`ToggleButton`类派生自`Button`并定义一个名为的新事件`Toggled`和名为的布尔属性`IsToggled`。 这些是相同的两个属性定义 Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

```csharp
class ToggleButton : Button
{
    public event EventHandler<ToggledEventArgs> Toggled;

    public static BindableProperty IsToggledProperty =
        BindableProperty.Create("IsToggled", typeof(bool), typeof(ToggleButton), false,
                                propertyChanged: OnIsToggledChanged);

    public ToggleButton()
    {
        Clicked += (sender, args) => IsToggled ^= true;
    }

    public bool IsToggled
    {
        set { SetValue(IsToggledProperty, value); }
        get { return (bool)GetValue(IsToggledProperty); }
    }

    protected override void OnParentSet()
    {
        base.OnParentSet();
        VisualStateManager.GoToState(this, "ToggledOff");
    }

    static void OnIsToggledChanged(BindableObject bindable, object oldValue, object newValue)
    {
        ToggleButton toggleButton = (ToggleButton)bindable;
        bool isToggled = (bool)newValue;

        // Fire event
        toggleButton.Toggled?.Invoke(toggleButton, new ToggledEventArgs(isToggled));

        // Set the visual state
        VisualStateManager.GoToState(toggleButton, isToggled ? "ToggledOn" : "ToggledOff");
    }
}
```

`ToggleButton`构造函数将附加的处理程序`Clicked`事件，以便它可以更改的值`IsToggled`属性。 `OnIsToggledChanged`方法激发`Toggled`事件。 

最后一行`OnIsToggledChanged`方法调用静态`VisualStateManager.GoToState`方法具有两个文本字符串"ToggledOn"和"ToggledOff"。 你可以阅读有关此方法，你的应用程序可以如何响应的文章中的可视状态[ **Xamarin.Forms 视觉状态管理器**](~/xamarin-forms/user-interface/visual-state-manager.md)。 

因为`ToggleButton`调用`VisualStateManager.GoToState`，类本身不需要包括任何其他功能，可以更改按钮的外观基于其`IsToggled`状态。 它是承载 XAML 的责任`ToggleButton`。 

**切换按钮演示**页包含两个实例`ToggleButton`，其中包括设置的视觉状态管理器标记`Text`， `BackgroundColor`，和`TextColor`基于可视状态的按钮： 

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ToggleButtonDemoPage"
             Title="Toggle Button Demo">
    
    <ContentPage.Resources>
        <Style TargetType="local:ToggleButton">
            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            <Setter Property="HorizontalOptions" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <local:ToggleButton Toggled="OnItalicButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Italic Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>
                    
                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Italic On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <local:ToggleButton Toggled="OnBoldButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Bold Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>
                    
                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Bold On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <Label x:Name="label"
               Text="Just a little passage of some sample text that can be formatted in italic or boldface by toggling the two buttons."
               FontSize="Large"
               HorizontalTextAlignment="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

`Toggled`事件处理程序的代码隐藏文件中。 他们负责设置`FontAttributes`属性`Label`根据按钮的状态：

```csharp
public partial class ToggleButtonDemoPage : ContentPage
{
    public ToggleButtonDemoPage ()
    {
        InitializeComponent ();
    }

    void OnItalicButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Italic;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Italic;
        }
    }

    void OnBoldButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Bold;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Bold;
        }
    }
}
```

此处是在 iOS、 Android 和 UWP 上运行的程序：

[![切换按钮演示](button-images/ToggleButtonDemo.png "切换按钮演示")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>位图使用按钮

`Button`类定义[ `Image` ](xref:Xamarin.Forms.Button.Image)属性，它允许你在上显示位图图像`Button`，单独使用或与文本结合使用。 你还可以指定如何排列的文本和图像。

`Image`属性属于类型[ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource)，这意味着，位图必须存储为在单个平台项目中，和.NET 标准库项目中不存在的资源。 

通过 Xamarin.Forms 支持每个平台允许映像存储在针对不同的像素分辨率的各种设备的应用程序可能在上运行的多个大小。 这些是名为多个位图或将其存储在为设备的视频，操作系统可以选取最佳匹配项的方式显示分辨率。 

对于上位图`Button`的最佳大小通常、 之间 32 和 64 设备无关的单位，具体取决于多大你希望如此。 此示例中使用的映像基于 48 设备无关的单位的大小。

在 iOS 项目中，**资源**文件夹包含此图像的三种大小：

- 存储为 48 像素正方形位图 **/Resources/MonkeyFace.png**
- 存储为 96 像素正方形位图 **/Resource/MonkeyFace@2x.png**
- 存储为 144 像素正方形位图 **/Resource/MonkeyFace@3x.png**

提供给所有三个位图**生成操作**的**BundleResource**。

针对 Android 项目中，所有的位图具有相同的名称，但它们存储在不同的子文件夹的**资源**文件夹：

- 存储为 72 像素正方形位图 **/Resources/drawable-hdpi/MonkeyFace.png**
- 存储为 96 像素正方形位图 **/Resources/drawable-xhdpi/MonkeyFace.png**
- 存储为 144 像素正方形位图 **/Resources/drawable-xxhdpi/MonkeyFace.png**
- 存储为 192 像素正方形位图 **/Resources/drawable-xxxhdpi/MonkeyFace.png**

这些已为其授予**生成操作**的**AndroidResource**。

在 UWP 项目中，可以在项目中，任何位置存储位图，但它们通常存储在自定义文件夹或**资产**现有文件夹。 UWP 项目包含这些位图：

- 存储为 48 像素正方形位图 **/Assets/MonkeyFace.scale-100.png**
- 存储为 96 像素正方形位图 **/Assets/MonkeyFace.scale-200.png**
- 存储为 192 像素正方形位图 **/Assets/MonkeyFace.scale-400.png**

它们已对所有指定**生成操作**的**内容**。

你可以指定如何`Text`和`Image`上排列属性`Button`使用[ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout)属性`Button`。 此属性是类型[ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout)，这是中的嵌入的类`Button`。 [构造函数](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double))包含两个参数：

- 成员[ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition)枚举： `Left`， `Top`， `Right`，或`Bottom`，该值指示位图相对于文本的显示方式。
- A`double`位图和文本之间的间距的值。

默认值为`Left`和 10 个单位。 两个只读属性`ButtonContentLayout`名为[ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position)和[ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing)提供这些属性的值。

在代码中，你可以创建`Button`并设置`ContentLayout`如下属性：

```csharp
Button button = new Button
{
    Text = "button text",
    Image = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

在 XAML 中，你需要指定仅枚举成员或间距，或同时按任意顺序用逗号分隔：

```xaml
<Button Text="button text"
        Image="image filename"
        ContentLayout="Right, 20" />
```

**图像按钮演示**页上使用`OnPlatform`若要为 iOS、 Android 和 UWP 位图文件指定不同的文件名。 如果你想要对所有三个平台使用相同的文件名和避免使用`OnPlatform`，你将需要项目的根目录中存储的 UWP 位图。

第一个`Button`上**图像按钮演示**页上设置`Image`属性但不是`Text`属性：

```xaml
<Button>
    <Button.Image>
        <OnPlatform x:TypeArguments="FileImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.Image>
</Button>
```

如果 UWP 位图存储在项目的根目录中，可以显著地简化此标记：

```xaml
<Button Image="MonkeyFace.png" />
```

若要避免大量的重复标记中**ImageButtonDemo.xaml**文件，一种隐式`Style`还定义以设置`Image`属性。 这`Style`自动应用于其他五个`Button`元素。 下面是完整的 XAML 文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">
        
        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="Image">
                    <OnPlatform x:TypeArguments="FileImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.Image>
                <OnPlatform x:TypeArguments="FileImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.Image>
        </Button>

        <Button Text="Default" />

        <Button Text="Left - 10"
                ContentLayout="Left, 10" />

        <Button Text="Top - 10"
                ContentLayout="Top, 10" />

        <Button Text="Right - 20"
                ContentLayout="Right, 20" />

        <Button Text="Bottom - 20" 
                ContentLayout="Bottom, 20" />
    </FlexLayout>
</ContentPage>
```

最终的四个`Button`元素会使用`ContentLayout`属性来指定的位置和间距的文本和位图：

[![图像按钮演示](button-images/ImageButtonDemo.png "图像按钮演示")](button-images/ImageButtonDemo-Large.png#lightbox)

现在，你已了解你可以处理的各种方法`Button`事件和更改`Button`外观。

## <a name="related-links"></a>相关链接

- [ButtonDemos 示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)
- [按钮 API](xref:Xamarin.Forms.Button)
