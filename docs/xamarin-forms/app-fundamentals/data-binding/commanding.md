---
title: Xamarin.Forms 命令界面
description: 本文介绍如何使用 Xamarin.Forms 数据绑定实现的命令属性。 命令接口提供对 MVVM 体系结构更适合一种方法来实现命令。
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: b18d042e34146a72b488da9017648a430c9cd353
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996368"
---
# <a name="the-xamarinforms-command-interface"></a>Xamarin.Forms 命令界面

在模型-视图-视图模型 (MVVM) 体系结构中，定义数据绑定的 ViewModel，这通常是派生的类中属性之间`INotifyPropertyChanged`，并在视图中，这通常是 XAML 文件的属性。 有时应用程序能够通过要求用户若要启动命令会影响在 ViewModel 中的某些内容超越这些属性绑定的需求。 这些命令通常通过单击按钮来发出信号或手指点击，以及传统上在代码隐藏文件中的处理程序中处理这些请求`Clicked`的事件`Button`或`Tapped`事件的`TapGestureRecognizer`。

命令接口提供对 MVVM 体系结构更适合一种方法来实现命令。 ViewModel 本身可以包含命令，如在视图中的特定活动的反应中执行的方法`Button`单击。 这些命令之间定义数据绑定和`Button`。

若要允许之间的数据绑定`Button`和 ViewModel，`Button`定义两个属性：

- [`Command`](xref:Xamarin.Forms.Button.Command) 类型 <xref:System.Windows.Input.ICommand>
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) 类型 `Object`

若要使用命令接口，您可以定义数据绑定面向`Command`的属性`Button`其中源是类型的 ViewModel 中的属性`ICommand`。 ViewModel 包含与该代码`ICommand`时单击该按钮时执行的属性。 可以设置`CommandParameter`到同一个绑定到任意数据来区分多个按钮，如果所有`ICommand`ViewModel 中的属性。

`Command`和`CommandParameter`属性也由以下类定义：

- [`MenuItem`](xref:Xamarin.Forms.MenuItem) 因此， [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)，又派生自 `MenuItem`
- [`TextCell`](xref:Xamarin.Forms.TextCell) 因此， [ `ImageCell` ](xref:Xamarin.Forms.ImageCell)，又派生自 `TextCell`
- [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)

[`SearchBar`](xref:Xamarin.Forms.SearchBar) 定义[ `SearchCommand` ](xref:Xamarin.Forms.SearchBar.SearchCommand)类型的属性`ICommand`和一个[ `SearchCommandParameter` ](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)属性。 [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand)的属性[ `ListView` ](xref:Xamarin.Forms.ListView)类型的也是`ICommand`。

在中的 ViewModel 不依赖于在视图中的特定用户界面对象的方式处理所有这些命令。

## <a name="the-icommand-interface"></a>ICommand 接口

<xref:System.Windows.Input.ICommand>接口不是 Xamarin.Forms 的一部分。 而定义在[System.Windows.Input](xref:System.Windows.Input)命名空间，它包括两个方法和一个事件：

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

若要使用命令接口，将 ViewModel 包含类型的属性`ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

ViewModel 还必须引用实现的类`ICommand`接口。 此类将稍后所述。 在视图中，`Command`属性的`Button`绑定到该属性：

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

当用户按`Button`，则`Button`调用`Execute`中的方法`ICommand`对象绑定到其`Command`属性。 是接口的最简单的命令的一部分。

`CanExecute`方法是更复杂。 绑定上的第一个定义当`Command`的属性`Button`，并以某种方式更改数据绑定时`Button`调用`CanExecute`中的方法`ICommand`对象。 如果`CanExecute`将返回`false`，则`Button`禁用其自身。 这表示特定命令目前不可用或无效。

`Button`还会将一个处理程序附加上`CanExecuteChanged`事件的`ICommand`。 从在 ViewModel 中激发事件。 当触发该事件时，`Button`调用`CanExecute`试。 `Button`情况下启用自身`CanExecute`返回`true`和禁用它自己`CanExecute`返回`false`。

> [!IMPORTANT]
> 不要使用`IsEnabled`属性的`Button`如果使用的命令接口。  

## <a name="the-command-class"></a>命令类

如果将 ViewModel 定义类型的属性`ICommand`，ViewModel 也必须包含或引用实现的类`ICommand`接口。 此类必须包含或引用`Execute`并`CanExecute`方法和激发`CanExecuteChanged`事件每当`CanExecute`方法可能返回不同的值。

可以自己，编写此类也可以使用其他人编写了一个类。 因为`ICommand`是一部分的 Microsoft Windows，它具有已使用多年与 Windows MVVM 应用程序。 使用的 Windows 类，实现`ICommand`使你可共享 Windows 应用程序和 Xamarin.Forms 应用程序之间将 Viewmodel。

如果共享 Viewmodel 之间 Windows 和 Xamarin.Forms 不是关键因素，则可以使用[ `Command` ](xref:Xamarin.Forms.Command)或[ `Command<T>` ](xref:Xamarin.Forms.Command`1)类包含在 Xamarin.Forms 中实现`ICommand`接口。 这些类允许您指定的正文`Execute`和`CanExecute`类构造函数中的方法。 使用`Command<T>`使用时`CommandParameter`属性来区分多个视图绑定到同一`ICommand`属性，且更简单`Command`类不需要时。

## <a name="basic-commanding"></a>基本命令

**人的项**页面[**数据绑定演示**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)程序演示了一些简单的命令在 ViewModel 中实现。

`PersonViewModel`定义名为三个属性`Name`， `Age`，和`Skills`定义一个人。 此类可进行*不*包含任何`ICommand`属性：

```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    string name;
    double age;
    string skills;

    public event PropertyChangedEventHandler PropertyChanged;

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public double Age
    {
        set { SetProperty(ref age, value); }
        get { return age; }
    }

    public string Skills
    {
        set { SetProperty(ref skills, value); }
        get { return skills; }
    }

    public override string ToString()
    {
        return Name + ", age " + Age;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

`PersonCollectionViewModel`所示下面创建新对象的类型`PersonViewModel`，并允许用户以填充数据。 为此，该类定义了属性`IsEditing`类型的`bool`并`PersonEdit`类型的`PersonViewModel`。 此外，该类定义了三个属性的类型`ICommand`和一个名为属性`Persons`类型的`IList<PersonViewModel>`:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{
    PersonViewModel personEdit;
    bool isEditing;

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public bool IsEditing
    {
        private set { SetProperty(ref isEditing, value); }
        get { return isEditing; }
    }

    public PersonViewModel PersonEdit
    {
        set { SetProperty(ref personEdit, value); }
        get { return personEdit; }
    }

    public ICommand NewCommand { private set; get; }

    public ICommand SubmitCommand { private set; get; }

    public ICommand CancelCommand { private set; get; }

    public IList<PersonViewModel> Persons { get; } = new ObservableCollection<PersonViewModel>();

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

此缩写的列表不包含类的构造函数，该类型的三个属性`ICommand`定义，这将会显示。 请注意，更改为三种类型的属性`ICommand`并`Persons`属性不会导致`PropertyChanged`所激发事件。 这些属性都设置为，首次创建类时，并且此后不会更改。

之前检查的构造函数`PersonCollectionViewModel`类中，让我们看一下 XAML 文件**人的项**程序。 此文件包含`Grid`使用其`BindingContext`属性设置为`PersonCollectionViewModel`。 `Grid`包含`Button`包含文本**新建**使用其`Command`属性绑定到`NewCommand`ViewModel 中的属性，使用属性的输入窗体绑定到`IsEditing`属性，为属性以及`PersonViewModel`，并将其绑定到的两个按钮`SubmitCommand`和`CancelCommand`ViewModel 的属性。 最后一个`ListView`显示人员已经输入的集合：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.PersonEntryPage"
             Title="Person Entry">
    <Grid Margin="10">
        <Grid.BindingContext>
            <local:PersonCollectionViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <!-- New Button -->
        <Button Text="New"
                Grid.Row="0"
                Command="{Binding NewCommand}"
                HorizontalOptions="Start" />

        <!-- Entry Form -->
        <Grid Grid.Row="1"
              IsEnabled="{Binding IsEditing}">

            <Grid BindingContext="{Binding PersonEdit}">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Label Text="Name: " Grid.Row="0" Grid.Column="0" />
                <Entry Text="{Binding Name}"
                       Grid.Row="0" Grid.Column="1" />

                <Label Text="Age: " Grid.Row="1" Grid.Column="0" />
                <StackLayout Orientation="Horizontal"
                             Grid.Row="1" Grid.Column="1">
                    <Stepper Value="{Binding Age}"
                             Maximum="100" />
                    <Label Text="{Binding Age, StringFormat='{0} years old'}"
                           VerticalOptions="Center" />
                </StackLayout>

                <Label Text="Skills: " Grid.Row="2" Grid.Column="0" />
                <Entry Text="{Binding Skills}"
                       Grid.Row="2" Grid.Column="1" />

            </Grid>
        </Grid>

        <!-- Submit and Cancel Buttons -->
        <Grid Grid.Row="2">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <Button Text="Submit"
                    Grid.Column="0"
                    Command="{Binding SubmitCommand}"
                    VerticalOptions="CenterAndExpand" />

            <Button Text="Cancel"
                    Grid.Column="1"
                    Command="{Binding CancelCommand}"
                    VerticalOptions="CenterAndExpand" />
        </Grid>

        <!-- List of Persons -->
        <ListView Grid.Row="3"
                  ItemsSource="{Binding Persons}" />
    </Grid>
</ContentPage>
```

下面是它的工作原理： 用户首次按下**新建**按钮。 这使输入窗体，但禁用**新建**按钮。 用户然后输入一个名称、 年龄和技能。 在编辑时，用户可以按**取消**按钮以重新开始。 仅当已输入的名称和有效期限**提交**按钮已启用。 按这**提交**按钮将此人传输到由显示的集合`ListView`。 之后可以**取消**或**提交**按下按钮，清除输入窗体并**新建**按钮已再次启用。

输入有效的期限之前，在左侧的 iOS 屏幕显示的布局。 Android 和 UWP 屏幕显示**提交**设置年龄后启用按钮：

[![人的项](commanding-images/personentry-small.png "人的项")](commanding-images/personentry-large.png#lightbox "人的项")

该程序没有任何工具，以便编辑现有条目，并在导航离开页面时不保存这些条目。

所有的逻辑**新建**，**提交**，并**取消**中处理按钮`PersonCollectionViewModel`通过定义`NewCommand`， `SubmitCommand`，和`CancelCommand`属性。 构造函数`PersonCollectionViewModel`将这三个属性设置为类型的对象`Command`。  

一个[构造函数](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean}))的`Command`类，可将类型的自变量传递`Action`并`Func<bool>`对应于`Execute`和`CanExecute`方法。 它是最简单的方法将这些操作和函数定义为 lamda 函数直接在`Command`构造函数。 下面是定义`Command`对象`NewCommand`属性：

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {
        NewCommand = new Command(
            execute: () =>
            {
                PersonEdit = new PersonViewModel();
                PersonEdit.PropertyChanged += OnPersonEditPropertyChanged;
                IsEditing = true;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return !IsEditing;
            });

        ···

    }

    void OnPersonEditPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        (SubmitCommand as Command).ChangeCanExecute();
    }

    void RefreshCanExecutes()
    {
        (NewCommand as Command).ChangeCanExecute();
        (SubmitCommand as Command).ChangeCanExecute();
        (CancelCommand as Command).ChangeCanExecute();
    }

    ···

}
```

当用户单击**新建**按钮，`execute`函数传递给`Command`执行构造函数。 这将创建一个新`PersonViewModel`对象，该对象上设置的处理程序`PropertyChanged`事件，设置`IsEditing`到`true`，并调用`RefreshCanExecutes`后构造函数定义的方法。

除了实现`ICommand`接口，`Command`类还定义了一个名为方法`ChangeCanExecute`。 应调用到 ViewModel`ChangeCanExecute`有关`ICommand`属性时，发生任何可能会更改的返回值`CanExecute`方法。 调用`ChangeCanExecute`会导致`Command`类，以触发`CanExecuteChanged`方法。 `Button`已附加该事件的处理程序并响应通过调用`CanExecute`同样，然后启用本身基于该方法的返回值。

时`execute`方法`NewCommand`调用`RefreshCanExecutes`，则`NewCommand`属性获取调用`ChangeCanExecute`，和`Button`调用`canExecute`方法，现在返回`false`因为`IsEditing`属性现`true`。

`PropertyChanged`处理程序的新`PersonViewModel`对象调用`ChangeCanExecute`方法的`SubmitCommand`。 下面是该命令属性的实现方式：


```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        SubmitCommand = new Command(
            execute: () =>
            {
                Persons.Add(PersonEdit);
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return PersonEdit != null &&
                       PersonEdit.Name != null &&
                       PersonEdit.Name.Length > 1 &&
                       PersonEdit.Age > 0;
            });

        ···
    }

    ···

}
```

`canExecute`函数`SubmitCommand`每次在中更改某个属性调用`PersonViewModel`对象正在编辑。 它将返回`true`仅当`Name`属性是至少包含一个字符和`Age`大于 0。 此时，**提交**按钮将变为启用状态。

`execute`函数**提交**移除属性更改处理程序从`PersonViewModel`，将对象添加到`Persons`集合，并返回到初始条件的所有内容。

`execute`函数**取消**按钮执行所有操作的**提交**按钮执行 execept 将对象添加到集合：

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        CancelCommand = new Command(
            execute: () =>
            {
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return IsEditing;
            });
    }

    ···

}
```

`canExecute`方法将返回`true`随时`PersonViewModel`正在编辑。

这些技术可能是适用于更复杂的方案： 中的属性`PersonCollectionViewModel`无法绑定到`SelectedItem`的属性`ListView`用于编辑现有项和一个**删除**无法添加按钮以删除这些项。

但并不需要定义`execute`和`canExecute`作为 lambda 函数的方法。 您可以将其写入 ViewModel 中为常规的私有方法以及中引用它们`Command`构造函数。 但是，这种方法 does 往往会导致大量的 ViewModel 中只有一次引用的方法。

## <a name="using-command-parameters"></a>使用命令参数

可以很方便的一个或多个按钮 （或其他用户界面对象） 来共用同一个`ICommand`ViewModel 中的属性。 在这种情况下，使用`CommandParameter`属性来区分按钮。

你可以继续使用`Command`类，这些共享`ICommand`属性。 类定义[备用构造函数](xref:Xamarin.Forms.Command.%23ctor(System.Action{System.Object},System.Func{System.Object,System.Boolean}))接受`execute`并`canExecute`带参数的类型的方法`Object`。 这是如何`CommandParameter`传递给这些方法。

但是，使用时`CommandParameter`，它是最容易使用泛型[ `Command<T>` ](xref:Xamarin.Forms.Command`1)类，以指定的对象设置为类型`CommandParameter`。 `execute`和`canExecute`您指定的方法具有该类型的参数。

**十进制键盘**页通过显示如何实现用于输入十进制数字键盘来演示此方法。 `BindingContext`有关`Grid`是`DecimalKeypadViewModel`。 `Entry`此 ViewModel 属性绑定到`Text`属性的`Label`。 所有`Button`对象绑定到 ViewModel 中的各种命令： `ClearCommand`， `BackspaceCommand`，和`DigitCommand`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.DecimalKeypadPage"
             Title="Decimal Keyboard">

    <Grid WidthRequest="240"
          HeightRequest="480"
          ColumnSpacing="2"
          RowSpacing="2"
          HorizontalOptions="Center"
          VerticalOptions="Center">

        <Grid.BindingContext>
            <local:DecimalKeypadViewModel />
        </Grid.BindingContext>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Button">
                    <Setter Property="FontSize" Value="32" />
                    <Setter Property="BorderWidth" Value="1" />
                    <Setter Property="BorderColor" Value="Black" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Label Text="{Binding Entry}"
               Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3"
               FontSize="32"
               LineBreakMode="HeadTruncation"
               VerticalTextAlignment="Center"
               HorizontalTextAlignment="End" />

        <Button Text="CLEAR"
                Grid.Row="1" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding ClearCommand}" />

        <Button Text="&#x21E6;"
                Grid.Row="1" Grid.Column="2"
                Command="{Binding BackspaceCommand}" />

        <Button Text="7"
                Grid.Row="2" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="7" />

        <Button Text="8"
                Grid.Row="2" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="8" />

        <Button Text="9"
                Grid.Row="2" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="9" />

        <Button Text="4"
                Grid.Row="3" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="4" />

        <Button Text="5"
                Grid.Row="3" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="5" />

        <Button Text="6"
                Grid.Row="3" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="6" />

        <Button Text="1"
                Grid.Row="4" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="1" />

        <Button Text="2"
                Grid.Row="4" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="2" />

        <Button Text="3"
                Grid.Row="4" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="3" />

        <Button Text="0"
                Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding DigitCommand}"
                CommandParameter="0" />

        <Button Text="&#x00B7;"
                Grid.Row="5" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="." />
    </Grid>
</ContentPage>
```

10 位数字和小数点的 11 按钮共享绑定到`DigitCommand`。 `CommandParameter`区分这些按钮。 将值设置为`CommandParameter`通常是与除小数点，它为清晰起见使用中间的点字符显示的按钮显示的文本相同。

下面是该程序的操作：

[![十进制键盘](commanding-images/decimalkeyboard-small.png "十进制键盘")](commanding-images/decimalkeyboard-large.png#lightbox "十进制键盘")

请注意，所有三个屏幕截图中的十进制点按钮被禁用，因为输入的数字中已包含小数点。

`DecimalKeypadViewModel`定义`Entry`类型的属性`string`(这是唯一的属性触发`PropertyChanged`事件) 和类型的三个属性`ICommand`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{
    string entry = "0";

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public string Entry
    {
        private set
        {
            if (entry != value)
            {
                entry = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Entry"));
            }
        }
        get
        {
            return entry;
        }
    }

    public ICommand ClearCommand { private set; get; }

    public ICommand BackspaceCommand { private set; get; }

    public ICommand DigitCommand { private set; get; }
}
```

按钮对应于`ClearCommand`始终处于启用状态，并只需设置回"0"的条目：

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {
        ClearCommand = new Command(
            execute: () =>
            {
                Entry = "0";
                RefreshCanExecutes();
            });

        ···

    }

    void RefreshCanExecutes()
    {
        ((Command)BackspaceCommand).ChangeCanExecute();
        ((Command)DigitCommand).ChangeCanExecute();
    }

    ···

}
```

由于始终启用按钮，因此不需要指定`canExecute`中的参数`Command`构造函数。

输入数字和退格的逻辑是有些棘手，因为如果已不输入任何数字，则`Entry`属性是字符串"0"。 如果用户键入更多的零，则`Entry`仍包含只是一个零。 如果用户键入任何其他数字，该数字将替换为零。 但是，如果用户键入小数点前任何其他数字，然后`Entry`是字符串"0。

**退格符**仅当项的长度大于 1，或如果启用按钮`Entry`不等于字符串"0":

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        BackspaceCommand = new Command(
            execute: () =>
            {
                Entry = Entry.Substring(0, Entry.Length - 1);
                if (Entry == "")
                {
                    Entry = "0";
                }
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return Entry.Length > 1 || Entry != "0";
            });

        ···

    }

    ···

}
```

逻辑`execute`函数**退格符**按钮可确保`Entry`是至少一个"0"的字符串。

`DigitCommand`属性将绑定到 11 按钮，其中每个标识本身与`CommandParameter`属性。 `DigitCommand`可以将设置为常规的实例`Command`，但它的更轻松地使用`Command<T>`泛型类。 使用 XAML，命令接口时`CommandParameter`属性通常是字符串，并且这是泛型参数的类型。 `execute`并`canExecute`函数然后具有类型的自变量`string`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        DigitCommand = new Command<string>(
            execute: (string arg) =>
            {
                Entry += arg;
                if (Entry.StartsWith("0") && !Entry.StartsWith("0."))
                {
                    Entry = Entry.Substring(1);
                }
                RefreshCanExecutes();
            },
            canExecute: (string arg) =>
            {
                return !(arg == "." && Entry.Contains("."));
            });
    }

    ···

}
```

`execute`方法将追加到字符串自变量`Entry`属性。 但是，如果结果开头零 （但不是零和小数点） 然后该初始零必须删除使用`Substring`函数。

`canExecute`方法将返回`false`仅当参数为 （指示十进制点是否已按下） 的小数点和`Entry`已包含小数点。

所有`execute`方法将调用`RefreshCanExecutes`，后者随后调用`ChangeCanExecute`两个`DigitCommand`和`ClearCommand`。 这可确保小数点和退格符按钮已启用或禁用基于当前的输入的数字序列。

## <a name="adding-commands-to-existing-views"></a>将命令添加到现有视图

如果你想要使用的命令的接口与不支持它的视图，则可以使用 Xamarin.Forms 行为，可将事件转换为命令。 这本文所述[**可重用 EventToCommandBehavior**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md)。

## <a name="asynchronous-commanding-for-navigation-menus"></a>异步导航菜单的命令

命令是用于实现导航菜单，例如，在方便[**数据绑定演示**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)程序本身。 下面是部分**MainPage.xaml**:


```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MainPage"
             Title="Data Binding Demos"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection Title="Basic Bindings">

                <TextCell Text="Basic Code Binding"
                          Detail="Define a data-binding in code"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicCodeBindingPage}" />

                <TextCell Text="Basic XAML Binding"
                          Detail="Define a data-binding in XAML"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicXamlBindingPage}" />

                <TextCell Text="Alternative Code Binding"
                          Detail="Define a data-binding in code without a BindingContext"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:AlternativeCodeBindingPage}" />

                ···

            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

使用与 XAML，发出命令时`CommandParameter`属性通常设置为字符串。 在这种情况下，但是，XAML 标记扩展使用，以便`CommandParameter`属于类型`System.Type`。

每个`Command`属性绑定到一个名为属性`NavigateCommand`。 属性定义在代码隐藏文件中， **MainPage.xaml.cs**:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(
            async (Type pageType) =>
            {
                Page page = (Page)Activator.CreateInstance(pageType);
                await Navigation.PushAsync(page);
            });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

构造函数设置`NavigateCommand`属性设置为`execute`方法实例化`System.Type`参数，然后转到它。 因为`PushAsync`调用需要`await`运算符，`execute`方法必须被标记为异步。 这通过实现`async`参数列表前面的关键字。

该构造函数还设置`BindingContext`到其自身的页，以便绑定引用`NavigateCommand`此类中。

此构造函数中的代码的顺序具有重要意义：`InitializeComponent`调用会导致 XAML 进行分析，但在该时间绑定到属性命名`NavigateCommand`不能解析，因为`BindingContext`设置为`null`。 如果`BindingContext`构造函数中设置*之前*`NavigateCommand`设置，则绑定时，可能会解决`BindingContext`设置，但此时，`NavigateCommand`仍是`null`。 设置`NavigateCommand`后`BindingContext`将不在绑定上的没有影响，因为对更改`NavigateCommand`不会激发`PropertyChanged`事件，并绑定并不知道这`NavigateCommand`现在是否有效。

将两者都设置`NavigateCommand`并`BindingContext`（按任何顺序） 到在调用前先`InitializeComponent`正常工作，因为 XAML 分析器遇到绑定定义时设置的绑定的这两个组件。

数据绑定有时会很棘手，但是，如您所见本系列的文章中，功能强大且用途广泛，而且极大地帮助来分隔从用户界面的基本逻辑来组织你的代码。



## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [数据绑定 Xamarin.Forms 书籍章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
