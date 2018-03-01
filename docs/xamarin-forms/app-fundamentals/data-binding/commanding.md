---
title: "命令界面"
description: "实现`Command`具有数据绑定属性"
ms.topic: article
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 0bc039385a6b2077c3b5fa5114b35b586a14a150
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="the-command-interface"></a>命令界面

模型-视图-视图模型 (MVVM) 体系结构，在数据绑定定义中视图模型，它通常是派生自的类属性之间`INotifyPropertyChanged`，和在视图中，这是通常的 XAML 文件的属性。 有时应用程序都有通过要求用户能够启动影响 ViewModel 中某项内容的命令超出这些属性绑定的需求。 这些命令通常由按钮单击事件发送信号或手指次点击，并且在处理程序中的代码隐藏文件中进行处理传统上`Clicked`事件`Button`或`Tapped`事件`TapGestureRecognizer`。 

命令的接口提供了一种方法来实现命令得更好地适用于 MVVM 体系结构。 ViewModel 本身可以包含命令，如在视图中的特定活动的反应中执行的方法`Button`单击。 这些命令之间定义数据绑定和`Button`。

若要允许之间的数据绑定`Button`和 ViewModel，`Button`定义两个属性：

- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Command/) 类型 [`ICommand`](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/)
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.CommandParameter/) 类型 `Object`

若要使用命令接口，您定义数据绑定面向`Command`属性`Button`其中源是中的类型视图模型的属性`ICommand`。 视图模型包含与该关联代码`ICommand`时单击该按钮时执行的属性。 你可以设置`CommandParameter`到任意数据来区分多个按钮，如果它们是所有绑定到同一`ICommand`视图模型中的属性。

`Command`和`CommandParameter`属性也定义由以下类：

- [`MenuItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/) 因此， [ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/)，它派生自 `MenuItem`
- [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) 因此， [ `ImageCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/)，它派生自 `TextCell`
- [`TapGestureRecognizer`](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)

[`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) 定义[ `SearchCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommand/)类型的属性`ICommand`和[ `SearchCommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommandParameter/)属性。 [ `RefreshCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RefreshCommand/)属性[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)的类型也是`ICommand`。 

可以在不依赖于在视图中的特定的用户界面对象的方式 ViewModel 内处理所有这些命令。

## <a name="the-icommand-interface"></a>ICommand 接口

[ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/)接口不属于 Xamarin.Forms。 在中改为定义[ `System.Windows.Input` ](https://developer.xamarin.com/api/namespace/System.Windows.Input/)命名空间，它包括两个方法和一个事件：

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

若要使用命令界面，你 ViewModel 包含类型的属性`ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

视图模型还必须引用一个类以实现`ICommand`接口。 此类将很快所述。 在视图中，`Command`属性`Button`绑定到该属性： 

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

当用户按`Button`、`Button`调用`Execute`中的方法`ICommand`对象绑定到其`Command`属性。 即命令界面的最简单的一部分。

`CanExecute`方法是更复杂。 绑定上的第一次定义当`Command`属性`Button`，以某种方式更改数据绑定时`Button`调用`CanExecute`中的方法`ICommand`对象。 如果`CanExecute`返回`false`，则`Button`禁用其自身。 这表示特定命令目前不可用或无效。

`Button`还会将处理程序附加上`CanExecuteChanged`事件`ICommand`。 从视图模型中激发事件。 当触发该事件时，`Button`调用`CanExecute`试。 `Button`如果使本身`CanExecute`返回`true`和禁用它自己`CanExecute`返回`false`。

> [!IMPORTANT]
> 不要使用`IsEnabled`属性`Button`如果你使用的命令界面。  

## <a name="the-command-class"></a>命令类

当你 ViewModel 定义的类型 propety `ICommand`，ViewModel 必须还包含或引用类实现`ICommand`接口。 此类必须包含或引用`Execute`和`CanExecute`方法和激发`CanExecuteChanged`事件每当`CanExecute`方法可能返回不同值。

你可以编写这样的类自己，也可以使用其他人已写入某个类。 因为`ICommand`是一部分的 Microsoft Windows 中，它已使用多年与 Windows MVVM 应用程序。 使用的 Windows 类，实现`ICommand`可以共享你 Viewmodel Windows 应用程序和 Xamarin.Forms 应用程序之间。

如果共享 Viewmodel Windows 和 Xamarin.Forms 之间不是关键因素，则可以使用[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)或[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/)类包含在 Xamarin.Forms 来实现`ICommand`接口。 这些类允许你指定的正文`Execute`和`CanExecute`类构造函数中的方法。 使用`Command<T>`当你使用`CommandParameter`属性来区分多个视图绑定到同一`ICommand`属性，并且更简单`Command`类时，不是必需的。

## <a name="basic-commanding"></a>基本命令

**人的项**页面[**数据绑定演示**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)程序演示一些简单的命令在视图模型中实现。

`PersonViewModel`定义名为的三个属性`Name`， `Age`，和`Skills`，用于定义个人。 此类可进行*不*包含任何`ICommand`属性：

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

`PersonCollectionViewModel`显示下面创建新对象类型的`PersonViewModel`，并允许用户以填充数据。 出于这个目的，类定义属性`IsEditing`类型的`bool`和`PersonEdit`类型的`PersonViewModel`。 此外，类定义的类型的三个属性`ICommand`和一个名为属性`Persons`类型的`IList<PersonViewModel>`: 

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

此缩写的列表不包括类的构造函数，该类型的三个属性`ICommand`定义，这将会显示。 请注意，更改类型的三个属性为`ICommand`和`Persons`属性不会导致`PropertyChanged`在激发的事件。 这些属性都设置首次创建类时，此后不会更改。

在检查的构造函数之前`PersonCollectionViewModel`类中，让我们看一下的 XAML 文件**人的项**程序。 这包含`Grid`与其`BindingContext`属性设置为`PersonCollectionViewModel`。 `Grid`包含`Button`与文本**新建**与其`Command`属性绑定到`NewCommand`视图模型中的属性，使用属性的输入表单绑定到`IsEditing`属性，作为以及属性`PersonViewModel`，并将其绑定到的两个按钮`SubmitCommand`和`CancelCommand`ViewModel 的属性。 最后一个`ListView`显示已经输入的人员的集合：

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

以下是它的工作原理： 用户的第一个按**新建**按钮。 这使输入窗体，但会禁用**新建**按钮。 用户然后输入一个名称、 年龄和技能。 任何时候在编辑期间，用户可以按**取消**按钮重新开始。 仅当已输入一个名称和有效期限是**提交**按钮已启用。 按此**提交**按钮将人员移交给由集合`ListView`。 后**取消**或**提交**按下按钮、 清除条目窗体和**新建**再次启用按钮。

输入有效的期限之前，在左侧的 iOS 屏幕显示的布局。 Android 和 UWP 屏幕显示**提交**启用已设置了年龄后的按钮：

[![人的项](commanding-images/personentry-small.png "人的项")](commanding-images/personentry-large.png "人的项")

程序不具有任何功能可用于编辑现有条目，并不会保存条目的页面离开时。

所有的逻辑**新建**，**提交**，和**取消**中处理按钮`PersonCollectionViewModel`通过定义`NewCommand`， `SubmitCommand`，和`CancelCommand`属性。 构造函数`PersonCollectionViewModel`将这三个属性设置为类型的对象`Command`。  

A[构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/System.Func%7BSystem.Boolean%7D/)的`Command`类使您可以将自变量的类型传递`Action`和`Func<bool>`对应于`Execute`和`CanExecute`方法。 它是最简单的方法将这些操作和函数定义为 lamda 函数直接在`Command`构造函数。 下面是定义的`Command`对象`NewCommand`属性：

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

当用户单击**新建**按钮，`execute`函数传递给`Command`执行构造函数。 这将创建一个新`PersonViewModel`对象，该对象上设置一个处理程序`PropertyChanged`事件，设置`IsEditing`到`true`，并调用`RefreshCanExecutes`后构造函数定义的方法。

除了实现`ICommand`接口，`Command`类还定义一个名为方法`ChangeCanExecute`。 应调用你 ViewModel`ChangeCanExecute`为`ICommand`属性只要发生了什么情况，可能会更改的返回值`CanExecute`方法。 调用`ChangeCanExecute`导致`Command`类激发`CanExecuteChanged`方法。 `Button`附加了该事件的处理程序，并通过调用响应`CanExecute`试，然后启用本身基于该方法的返回值。

当`execute`方法`NewCommand`调用`RefreshCanExecutes`、`NewCommand`属性获取调用`ChangeCanExecute`，和`Button`调用`canExecute`方法，现在可返回`false`因为`IsEditing`属性现在是`true`。

`PropertyChanged`新处理程序`PersonViewModel`对象调用`ChangeCanExecute`方法`SubmitCommand`。 下面是如何实现该命令属性：


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

`canExecute`函数`SubmitCommand`每次在中更改某个属性时调用`PersonViewModel`对象处于编辑状态。 它将返回`true`仅当`Name`属性是至少包含一个字符和`Age`大于 0。 在那时，**提交**按钮将变为启用状态。 

`execute`函数**提交**移除的属性更改处理程序，从`PersonViewModel`，将对象添加到`Persons`集合，并将所有内容返回到初始条件。

`execute`函数**取消**按钮的作用的所有内容，**提交**按钮执行 execept 将该对象添加到集合：

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

`canExecute`方法返回`true`随时`PersonViewModel`正在编辑。

这些方法可能适用于更复杂的方案： 中的属性`PersonCollectionViewModel`无法绑定到`SelectedItem`属性`ListView`以进行编辑现有项和**删除**无法添加按钮以删除这些项。

无需定义`execute`和`canExecute`作为 lambda 函数的方法。 你可以编写它们作为常规私有方法 ViewModel 并引用在`Command`构造函数。 但是，此方法未往往会导致大量的引用仅一次在视图模型中的方法。

## <a name="using-command-parameters"></a>使用命令参数

这有时将为一个或多个按钮 （或其他用户界面对象） 共享同一个方便`ICommand`视图模型中的属性。 在这种情况下，使用`CommandParameter`属性来区分按钮。 

你可以继续使用`Command`类对于这些共享`ICommand`属性。 类定义[备用构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action%7BSystem.Object%7D/System.Func%7BSystem.Object,System.Boolean%7D/)接受`execute`和`canExecute`具有类型参数的方法`Object`。 这是如何`CommandParameter`传递给这些方法。

但是，当使用`CommandParameter`，它是最简单的方法使用泛型[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/)类，以指定的设置为的对象类型`CommandParameter`。 `execute`和`canExecute`你指定的方法有该类型的参数。

**十进制键盘**页演示此方法通过显示如何实现一个键盘，输入十进制数字。 `BindingContext`为`Grid`是`DecimalKeypadViewModel`。 `Entry`此 ViewModel 属性绑定到`Text`属性`Label`。 所有`Button`对象绑定到视图模型中的各种命令： `ClearCommand`， `BackspaceCommand`，和`DigitCommand`:

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

十进制数字和小数点的 11 按钮共享绑定到`DigitCommand`。 `CommandParameter`区分这些按钮。 其值设置为`CommandParameter`通常是由除外小数点，与中间点字符显示为了清楚起见该按钮显示的文本相同。

下面是在操作中的程序：

[![十进制键盘](commanding-images/decimalkeyboard-small.png "十进制键盘")](commanding-images/decimalkeyboard-large.png "十进制键盘")

请注意，所有三个屏幕截图中的十进制点按钮被禁用，因为输入的数已包含小数点。 

`DecimalKeypadViewModel`定义`Entry`类型的属性`string`(这是将触发的唯一属性`PropertyChanged`事件) 和三个属性的类型`ICommand`:

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

对应于的按钮`ClearCommand`始终处于启用状态并只需设置回"0"的条目：

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

因为按钮始终处于启用状态，不需要指定`canExecute`中的参数`Command`构造函数。

输入数字和退格逻辑是一些技巧，因为如果已输入的数字，则`Entry`属性是字符串"0"。 如果用户键入详细零，则`Entry`仍包含仅仅一个零。 如果用户键入任何其他数字，该数字将替换零。 但是，如果用户键入之前任何其他数字，小数点然后`Entry`为字符串"0"。

**退格符**按钮已启用，仅当项的长度大于 1，或如果`Entry`是否不等于"0"的字符串：

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

逻辑`execute`函数**退格符**按钮可确保`Entry`至少是"0"的字符串。

`DigitCommand`属性绑定到 11 按钮，其中每个标识自行向`CommandParameter`属性。 `DigitCommand`无法设置为实例的常规`Command`类，但它的使用起来更为简便`Command<T>`泛型类。 当使用命令的接口时使用 XAML，`CommandParameter`属性通常是字符串，而这是泛型自变量的类型。 `execute`和`canExecute`函数然后具有类型自变量`string`:

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

`execute`方法追加的字符串自变量`Entry`属性。 但是，如果结果开头零 （但不是零和小数点） 然后该初始零必须删除使用`Substring`函数。

`canExecute`方法返回`false`仅当参数为 （指示小数点是否已按下） 的小数点和`Entry`已包含小数点。 

所有`execute`方法调用`RefreshCanExecutes`，后者随后调用`ChangeCanExecute`两个`DigitCommand`和`ClearCommand`。 这可确保小数点和退格符按钮已启用或禁用基于当前的输入的数字序列。

## <a name="adding-commands-to-existing-views"></a>将命令添加到现有视图

如果你想要使用的命令的接口与不支持它的视图，则可以使用 Xamarin.Forms 行为，将事件转换为命令。 此文章中介绍了[**可重用 EventToCommandBehavior**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md)。

## <a name="asynchronous-commanding-for-navigation-menus"></a>异步导航菜单命令

命令适用于实现导航菜单，例如，在[**数据绑定演示**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)程序本身。 下面是属于**MainPage.xaml**:


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

使用 XAML 中，使用命令时`CommandParameter`属性通常设置为字符串。 在这种情况下，但是，使用 XAML 标记扩展是以便`CommandParameter`属于类型`System.Type`。

每个`Command`属性绑定到名为的属性`NavigateCommand`。 属性在隐藏代码文件中，定义**MainPage.xaml.cs**:

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

构造函数集`NavigateCommand`属性`execute`方法实例化`System.Type`参数，然后导航到它。 因为`PushAsync`调用需要`await`运算符，`execute`方法必须被标记为异步。 这实现的`async`在参数列表前的关键字。 

构造函数还将设置`BindingContext`到其自身的页，以便绑定引用`NavigateCommand`中此类。

此构造函数中的代码的顺序具有重要意义：`InitializeComponent`调用均会导致 XAML 以分析，但在该时间绑定到属性名为`NavigateCommand`无法解析，因为`BindingContext`设置为`null`。 如果`BindingContext`构造函数中设置*之前*`NavigateCommand`设置，则绑定可以解决当`BindingContext`设置，但在那时，`NavigateCommand`仍`null`。 设置`NavigateCommand`后`BindingContext`将没有在绑定上的任何效果，因为对更改`NavigateCommand`，不会激发`PropertyChanged`事件，并绑定并不知道这`NavigateCommand`现在无效。

同时设置`NavigateCommand`和`BindingContext`（按任何顺序） 到在调用前`InitializeComponent`将运行，因为当 XAML 分析器遇到绑定定义设置绑定的两个组件。 

数据绑定有时会很棘手，但是，如你所见本系列文章中，功能强大且灵活，而且极大地有助于通过将从用户界面的基础逻辑中分离，来组织你的代码。



## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [从 Xamarin.Forms 簿的数据绑定章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
