---
title: "绑定模式"
description: "控制源和目标之间的信息流"
ms.topic: article
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: e85bc98b5da4c6e529f150c6e7945a8556e2c60f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="binding-mode"></a>绑定模式

在[上一篇文章](basic-bindings.md)、**替代代码绑定**和**的替代项 XAML 绑定**特色的页`Label`与其`Scale`属性绑定到`Value`属性`Slider`。 因为`Slider`初始值为 0，这导致`Scale`属性`Label`将设置为 0，而不是 1，与`Label`消失。

在[ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)示例中，**反向绑定**页是类似于以前的文章中的程序，只不过在上定义的数据绑定`Slider`而不是在`Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.ReverseBindingPage"
             Title="Reverse Binding">
    <StackLayout Padding="10, 0">

        <Label x:Name="label" 
               Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                VerticalOptions="CenterAndExpand"
                Value="{Binding Source={x:Reference label},
                                Path=Opacity}" />
    </StackLayout>
</ContentPage>
```

首先，这看起来可能向后： 现在`Label`是数据绑定源中，与`Slider`目标。 绑定引用`Opacity`属性`Label`，具有默认值为 1。

如你所料，`Slider`从初始初始化为值 1`Opacity`值`Label`。 在左侧的 iOS 屏幕快照所示：

[![反向绑定](binding-mode-images/reversebinding-small.png "反向绑定")](binding-mode-images/reversebinding-large.png "反向绑定")

但你可能会对感到惊讶，`Slider`仍将有效，如 Android 和 UWP 屏幕截图所示。 这看起来来建议数据绑定更好时起作用`Slider`是绑定目标而不是`Label`因为初始化工作方式与我们所料。

之间的差异**反向绑定**示例和前面的示例包括*绑定模式*。

## <a name="the-default-binding-mode"></a>默认绑定模式

绑定模式指定的成员[ `BindingMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingMode/)枚举： 

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.Default/) 
- [`TwoWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/) &ndash; 数据源和目标之间将这两种方式
- [`OneWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWay/) &ndash; 数据源从转到目标
- [`OneWayToSource`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWayToSource/) &ndash; 从目标转到源数据

每个可绑定属性具有默认绑定模式创建可绑定的属性时，设置以及可从[ `DefaultBindingMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableProperty.DefaultBindingMode/)属性`BindableProperty`对象。 当该属性是数据绑定目标时，此默认绑定模式将实际上指示的模式。

大多数属性，如的默认绑定模式`Rotation`， `Scale`，和`Opacity`是`OneWay`。 这些属性时数据绑定目标，则将目标属性设置从源。

但是的默认绑定模式`Value`属性`Slider`是`TwoWay`。 这意味着当`Value`属性是数据绑定目标，则目标 （像往常一样） 设置从源但源也将设置从目标。 这样做可允许`Slider`从初始设置`Opacity`值。

此双向绑定可能看起来创建无限循环，但是，也不会发生。 可绑定属性不发信号属性更改，除非该属性实际发生变化。 这样可以防止无限循环。

### <a name="two-way-bindings"></a>双向绑定

最多可绑定属性具有的默认绑定模式`OneWay`但以下属性具有的默认绑定模式`TwoWay`:

- `Date` 属性 `DatePicker`
- `Text` 属性`Editor`， `Entry`， `SearchBar`，和 `EntryCell`
- `IsRefreshing` 属性 `ListView`
- `SelectedItem` 属性 `MultiPage`
- `SelectedIndex` 和`SelectedItem`的属性 `Picker`
- `Value` 属性`Slider`和 `Stepper`
- `IsToggled` 属性 `Switch` 
- `On` 属性 `SwitchCell`
- `Time` 属性 `TimePicker`

这些特定属性定义为`TwoWay`非常好的原因： 

模型-视图-视图模型 (MVVM) 应用程序体系结构用于数据绑定，ViewModel 类时，数据绑定源中，和视图中，如组成视图`Slider`，数据绑定目标。 MVVM 绑定类似于**反向绑定**多个前面的示例中的绑定的示例。 它是很有可能，您想要使用的 ViewModel 中的相应属性的值进行初始化的页上的每个视图，但在视图中的更改还应影响 ViewModel 属性。

与默认绑定模式的属性`TwoWay`是最有可能在 MVVM 方案中使用这些属性。

### <a name="one-way-to-source-bindings"></a>一次性单向到源绑定

只读可绑定属性具有的默认绑定模式`OneWayToSource`。 只有一个读/写可绑定属性具有的默认绑定模式`OneWayToSource`:

- `SelectedItem` 属性 `ListView`

基本原理是上的绑定`SelectedItem`属性应导致绑定源设置。 在本文后面的示例重写该行为。

## <a name="viewmodels-and-property-change-notifications"></a>Viewmodel 和属性更改通知

**简单颜色选择器**页演示如何将简单视图模型。 数据绑定允许用户选择一种颜色使用三个`Slider`色调、 饱和度和亮度的元素。

视图模型是数据绑定源。 ViewModel 未*不*定义可绑定属性，但它实现一种通知机制，它允许绑定基础结构以属性的值更改时得到通知。 此通知机制是[ `INotifyPropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/)接口，定义名为的单个属性[ `PropertyChanged` ](https://developer.xamarin.com/api/event/System.ComponentModel.INotifyPropertyChanged.PropertyChanged/)。 通常实现此接口的类激发事件时的一个公共属性值更改。 事件不需要如果属性永远不会更改激发。 (`INotifyPropertyChanged`接口还由实现`BindableObject`和`PropertyChanged`可绑定的属性发生更改的值时触发事件。)

`HslColorViewModel`类定义五个属性： `Hue`， `Saturation`， `Luminosity`，和`Color`属性相互关联。 在任何一种这三种颜色组件更改值时`Color`重新计算属性，和`PropertyChanged`事件触发的所有四个属性：

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name; 

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get 
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

当`Color`属性更改、 静态`GetNearestColorName`中的方法`NamedColor`类 (也包括在**DataBindingDemos**解决方案) 获取最接近的命名的颜色并设置`Name`属性。 这`Name`属性具有私有`set`访问器，因此它无法从设置，在类外部。

当视图模型设置用作绑定源时，绑定基础结构将附加的处理程序`PropertyChanged`事件。 这种方式，绑定可以对属性的更改通知，然后再设置从更改后的值的目标属性。

**简单颜色选择器**XAML 文件实例化`HslColorViewModel`中页面的资源字典并初始化`Color`属性。 `BindingContext`属性`Grid`设置为`StaticResource`绑定扩展来引用该资源：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SimpleColorSelectorPage">

    <ContentPage.Resources>
        <ResourceDictionary>
            <local:HslColorViewModel x:Key="viewModel" 
                                     Color="MediumTurquoise" />

            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
        
    <Grid BindingContext="{StaticResource viewModel}">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <BoxView Color="{Binding Color}"
                 Grid.Row="0" />

        <StackLayout Grid.Row="1"
                     Margin="10, 0">

            <Label Text="{Binding Name}"
                   HorizontalTextAlignment="Center" />

            <Slider Value="{Binding Hue}" />
    
            <Slider Value="{Binding Saturation}" />

            <Slider Value="{Binding Luminosity}" />
        </StackLayout>
    </Grid>
</ContentPage>
```

`BoxView`， `Label`，和三个`Slider`视图继承从绑定上下文`Grid`。 这些视图是引用视图模型中的源属性的所有绑定目标。 有关`Color`属性`BoxView`，和`Text`属性`Label`，数据绑定是`OneWay`： 在视图中的属性将设置视图模型中的属性中。

`Value`属性`Slider`，但是， `TwoWay`。 这允许每台`Slider`从视图模型，以及用于 ViewModel 若要设置从每个要设置`Slider`。 

当首次运行该程序时， `BoxView`， `Label`，和三个`Slider`元素都设置为从基于初始 ViewModel`Color`时实例化视图模型设置的属性。 在左侧的 iOS 屏幕快照所示：

[![简单颜色选择器](binding-mode-images/simplecolorselector-small.png "简单颜色选择器")](binding-mode-images/simplecolorselector-large.png "简单颜色选择器")

在操作滑块，`BoxView`和`Label`相应地，Android 和 UWP 屏幕截图所示更新。

实例化资源字典中的 ViewModel 是一个常见的方法。 它也是可以实例化中的属性元素标记 ViewModel`BindingContext`属性。 在**简单颜色选择器**XAML 文件中，请尝试删除`HslColorViewModel`从资源字典并将其设置为`BindingContext`属性`Grid`如下所示：

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>
        
    ···

</Grid>
```

可以在各种不同的方式设置的绑定上下文。 有时，代码隐藏文件实例化视图模型，并将其设置为`BindingContext`页属性。 这些是所有有效的方法。

## <a name="overriding-the-binding-mode"></a>重写的绑定模式

如果在目标属性上的默认绑定模式不适合特定的数据绑定，则可能可以通过设置重写它[ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.Mode/)属性`Binding`(或[ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Mode/)属性`Binding`标记扩展) 的成员之一`BindingMode`枚举。

但是，将设置`Mode`属性`TwoWay`始终不起作用，正如所料。 例如，请尝试修改**的替代项 XAML 绑定**XAML 文件将其包括`TwoWay`绑定定义中：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

它是预期的`Slider`将初始化为的初始值`Scale`属性，它是 1，但，也不会发生。 当`TwoWay`初始化绑定，目标设置从源首先，这意味着，`Scale`属性设置为`Slider`默认值为 0。 当`TwoWay`上设置绑定`Slider`，则`Slider`从源最初会对其进行设置。

你可以将绑定模式设置为`OneWayToSource`中**的替代项 XAML 绑定**示例：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

现在`Slider`初始化为 1 (默认值的`Scale`) 但操作`Slider`不会影响`Scale`属性，因此这不是非常有用。

一个非常有用的应用程序的重写的默认绑定模式`TwoWay`涉及`SelectedItem`属性`ListView`。 默认绑定模式是`OneWayToSource`。 当上设置数据绑定`SelectedItem`属性来引用视图模型中的源属性，则该源属性设置从`ListView`选择。 但是，在某些情况下，你可能还想`ListView`从 ViewModel 初始化。

**示例设置**页演示这种方法。 此页表示经常在视图模型，这样中定义的应用程序设置的简单实现`SampleSettingsViewModel`文件：

```csharp
public class SampleSettingsViewModel : INotifyPropertyChanged
{
    string name;
    DateTime birthDate;
    bool codesInCSharp;
    double numberOfCopies;
    NamedColor backgroundNamedColor;

    public event PropertyChangedEventHandler PropertyChanged;

    public SampleSettingsViewModel(IDictionary<string, object> dictionary)
    {
        Name = GetDictionaryEntry<string>(dictionary, "Name");
        BirthDate = GetDictionaryEntry(dictionary, "BirthDate", new DateTime(1980, 1, 1));
        CodesInCSharp = GetDictionaryEntry<bool>(dictionary, "CodesInCSharp");
        NumberOfCopies = GetDictionaryEntry(dictionary, "NumberOfCopies", 1.0);
        BackgroundNamedColor = NamedColor.Find(GetDictionaryEntry(dictionary, "BackgroundNamedColor", "White"));
    }

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public DateTime BirthDate
    {
        set { SetProperty(ref birthDate, value); }
        get { return birthDate; }
    }

    public bool CodesInCSharp
    {
        set { SetProperty(ref codesInCSharp, value); }
        get { return codesInCSharp; }
    }

    public double NumberOfCopies
    {
        set { SetProperty(ref numberOfCopies, value); }
        get { return numberOfCopies; }
    }

    public NamedColor BackgroundNamedColor
    {
        set
        {
            if (SetProperty(ref backgroundNamedColor, value))
            {
                OnPropertyChanged("BackgroundColor");
            }
        }
        get { return backgroundNamedColor; }
    }

    public Color BackgroundColor
    {
        get { return BackgroundNamedColor?.Color ?? Color.White; }
    }

    public void SaveState(IDictionary<string, object> dictionary)
    {
        dictionary["Name"] = Name;
        dictionary["BirthDate"] = BirthDate;
        dictionary["CodesInCSharp"] = CodesInCSharp;
        dictionary["NumberOfCopies"] = NumberOfCopies;
        dictionary["BackgroundNamedColor"] = BackgroundNamedColor.Name;
    }

    T GetDictionaryEntry<T>(IDictionary<string, object> dictionary, string key, T defaultValue = default(T))
    {
        return dictionary.ContainsKey(key) ? (T)dictionary[key] : defaultValue;
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

每个应用程序设置是一个属性，保存到一个名为方法中的 Xamarin.Forms properties 字典`SaveState`和从构造函数中该字典加载。 类的底部是帮助简化 Viewmodel，使它们更不容易出错的两种方法。 `OnPropertyChanged`底部的方法有一个设置为调用属性的可选参数。 字符串形式指定属性的名称时，这样可以避免拼写错误。 

`SetProperty`类中的方法执行更多： 它与为字段，存储的值设置为该属性的值进行比较，并仅调用`OnPropertyChanged`当两个值是否不相等。 

`SampleSettingsViewModel`类定义的背景色为两个属性：`BackgroundNamedColor`属性属于类型`NamedColor`，其中一个类中也包含了**DataBindingDemos**解决方案。 `BackgroundColor`属性属于类型`Color`，并从获取`Color`属性`NamedColor`对象。

`NamedColor`类使用.NET 反射枚举 Xamarin.Forms 中的所有静态公共字段`Color`结构，并将其存储具有可从静态访问集合中其名`All`属性：

```csharp
public class NamedColor : IEquatable<NamedColor>, IComparable<NamedColor>
{
    // Instance members
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    public bool Equals(NamedColor other)
    {
        return Name.Equals(other.Name);
    }

    public int CompareTo(NamedColor other)
    {
        return Name.CompareTo(other.Name);
    }

    // Static members
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof(Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                                (int)(255 * color.R),
                                                (int)(255 * color.G),
                                                (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        all.Sort();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }

    public static NamedColor Find(string name)
    {
        return ((List<NamedColor>)All).Find(nc => nc.Name == name);
    }

    public static string GetNearestColorName(Color color)
    {
        double shortestDistance = 1000;
        NamedColor closestColor = null;

        foreach (NamedColor namedColor in NamedColor.All)
        {
            double distance = Math.Sqrt(Math.Pow(color.R - namedColor.Color.R, 2) +
                                        Math.Pow(color.G - namedColor.Color.G, 2) +
                                        Math.Pow(color.B - namedColor.Color.B, 2));

            if (distance < shortestDistance)
            {
                shortestDistance = distance;
                closestColor = namedColor;
            }
        }
        return closestColor.Name;
    }
}
```

`App`类**DataBindingDemos**项目定义一个名为属性`Settings`类型的`SampleSettingsViewModel`。 此属性将初始化时`App`实例化类时，与`SaveState`方法调用时`OnSleep`调用方法：

```csharp
public partial class App : Application
{
    public App()
    {
        InitializeComponent();

        Settings = new SampleSettingsViewModel(Current.Properties);

        MainPage = new NavigationPage(new MainPage());
    }

    public SampleSettingsViewModel Settings { private set; get; }

    protected override void OnStart()
    {
        // Handle when your app starts
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Settings.SaveState(Current.Properties);
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
    }
}
```

应用程序生命周期方法的详细信息，请参阅文章[**应用生命周期**](~/xamarin-forms/app-fundamentals/app-lifecycle.md)。

在中处理的几乎所有操作都其他**SampleSettingsPage.xaml**文件。 `BindingContext`页的设置使用`Binding`标记扩展： 绑定源是静态`Application.Current`属性，它是实例的`App`在项目中，类和`Path`设置为`Settings`属性，它是`SampleSettingsViewModel`对象：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SampleSettingsPage"
             Title="Sample Settings"
             BindingContext="{Binding Source={x:Static Application.Current},
                                      Path=Settings}">

    <StackLayout BackgroundColor="{Binding BackgroundColor}"
                 Padding="10"
                 Spacing="10">

        <StackLayout Orientation="Horizontal">
            <Label Text="Name: "
                   VerticalOptions="Center" />

            <Entry Text="{Binding Name}"
                   Placeholder="your name"
                   HorizontalOptions="FillAndExpand"
                   VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Birth Date: "
                   VerticalOptions="Center" />

            <DatePicker Date="{Binding BirthDate}"
                        HorizontalOptions="FillAndExpand"
                        VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Do you code in C#? "
                   VerticalOptions="Center" />

            <Switch IsToggled="{Binding CodesInCSharp}"
                    VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Number of Copies: "
                   VerticalOptions="Center" />

            <Stepper Value="{Binding NumberOfCopies}"
                     VerticalOptions="Center" />

            <Label Text="{Binding NumberOfCopies}"
                   VerticalOptions="Center" />
        </StackLayout>

        <Label Text="Background Color:" />

        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
                  VerticalOptions="FillAndExpand"
                  RowHeight="40">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     HeightRequest="32"
                                     WidthRequest="32"
                                     VerticalOptions="Center" />

                            <Label Text="{Binding FriendlyName}"
                                   FontSize="24"
                                   VerticalOptions="Center" />
                        </StackLayout>                        
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

页的所有子级都继承绑定上下文。 大部分此页上的其他绑定到中的属性都是`SampleSettingsViewModel`。 `BackgroundColor`属性用于设置`BackgroundColor`属性`StackLayout`，和`Entry`， `DatePicker`， `Switch`，和`Stepper`属性被绑定到视图模型中的其他属性。

`ItemsSource`属性`ListView`设置为静态`NamedColor.All`属性。 这样就会填写`ListView`所有`NamedColor`实例。 中每个项`ListView`，项的绑定上下文设置为`NamedColor`对象。 `BoxView`和`Label`中`ViewCell`绑定到中的属性`NamedColor`。

`SelectedItem`属性`ListView`属于类型`NamedColor`，并将其绑定到`BackgroundNamedColor`属性`SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

默认绑定模式`SelectedItem`是`OneWayToSource`，这会从所选的项设置的视图模型属性。 `TwoWay`模式允许`SelectedItem`从 ViewModel 初始化。 

但是，当`SelectedItem`设置这种方式，`ListView`不会自动滚动以显示所选的项。 代码隐藏文件中的少量代码不需要：

```csharp
public partial class SampleSettingsPage : ContentPage
{
    public SampleSettingsPage()
    {
        InitializeComponent();

        if (colorListView.SelectedItem != null)
        {
            colorListView.ScrollTo(colorListView.SelectedItem, 
                                   ScrollToPosition.MakeVisible, 
                                   false);
        }
    }
}
``` 

当首次运行时，在左侧的 iOS 屏幕快照显示的程序。 中的构造函数`SampleSettingsViewModel`初始化的背景色为白色，并且这是中选定的内容`ListView`:

[![示例设置](binding-mode-images/samplesettings-small.png "示例设置")](binding-mode-images/samplesettings-large.png "示例设置")

其他两个屏幕快照显示更改的设置。 在试验的这一页，请记得将该程序进入睡眠状态或在设备或正在运行的仿真程序上终止它。 终止程序从 Visual Studio 调试器将不会导致`OnSleep`中重写`App`类调用。

在下一篇文章中，你将看到如何指定[**字符串格式设置**](string-formatting.md)上设置的数据绑定`Text`属性`Label`。


## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [从 Xamarin.Forms 簿的数据绑定章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
