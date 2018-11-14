---
title: Xamarin.Forms 绑定模式
description: 此文章介绍了如何控制流的源和目标使用的绑定模式，这指定与 BindingMode 枚举的成员之间的信息。 每个可绑定属性具有默认绑定模式，当该属性是数据绑定目标时有效指示的模式。
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/01/2018
ms.openlocfilehash: 03dbaa36cc1fa4a6a169f9456e0fd5b0fdc0d295
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563935"
---
# <a name="xamarinforms-binding-mode"></a>Xamarin.Forms 绑定模式

中[前一篇文章](basic-bindings.md)，则**替代方法代码绑定**并**替代 XAML 绑定**特色的页`Label`使用其`Scale`属性绑定到`Value`属性的`Slider`。 因为`Slider`初始值为 0，这导致`Scale`的属性`Label`设置为 0，而不是 1，和`Label`消失。

在中[ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)示例中，**反向绑定**页是类似于前一篇文章中的程序，只不过上定义的数据绑定`Slider`而不是在`Label`:

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

首先，这看起来可能向后： 现在`Label`是数据绑定源和`Slider`是目标。 绑定引用`Opacity`属性的`Label`，其中包含默认值为 1。

正如您所料，`Slider`初始化为值 1 从初始`Opacity`的值`Label`。 在左侧 iOS 屏幕截图所示：

[![反向绑定](binding-mode-images/reversebinding-small.png "反向绑定")](binding-mode-images/reversebinding-large.png#lightbox "反向绑定")

但您可能会很吃惊，`Slider`仍将有效，如 Android 和 UWP 的屏幕截图所示。 这似乎表明，数据绑定的工作方式更好时`Slider`是绑定目标而不是`Label`因为初始化工作方式与我们所料。

之间的差异**反向绑定**示例和更早的示例涉及*绑定模式*。

## <a name="the-default-binding-mode"></a>默认绑定模式

绑定模式指定的成员[ `BindingMode` ](xref:Xamarin.Forms.BindingMode)枚举：

- [`Default`](xref:Xamarin.Forms.BindingMode.Default)
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) &ndash; 数据源和目标之间出现这两种方式
- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) &ndash; 数据源中发送到目标
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; 数据从目标发送到源
- [`OneTime`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; 数据源从转到目标，但仅当`BindingContext`更改 （新 Xamarin.Forms 3.0）

每个可绑定属性具有默认值绑定时创建的可绑定属性，设置的模式和可以在中找到[ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode)属性的`BindableProperty`对象。 当该属性是数据绑定目标时实际上这一默认绑定模式指示的模式。

如大多数属性的默认绑定模式`Rotation`， `Scale`，并`Opacity`是`OneWay`。 当这些属性是数据绑定目标时，则目标属性设置从源。

但是的默认绑定模式`Value`的属性`Slider`是`TwoWay`。 这意味着，当`Value`属性是数据绑定目标，则目标 （像往常一样） 设置从源但从目标还设置源。 这样做可允许`Slider`若要从初始设置`Opacity`值。

此双向绑定可能看起来创建无限循环，但未按预期进行。 可绑定属性不发出信号属性更改，除非该属性实际上发生变化。 这可以防止无限循环。

### <a name="two-way-bindings"></a>双向绑定

最可绑定属性具有的默认绑定模式`OneWay`但以下属性具有默认绑定模式的`TwoWay`:

- `Date` 属性 `DatePicker`
- `Text` 属性的`Editor`， `Entry`， `SearchBar`，和 `EntryCell`
- `IsRefreshing` 属性 `ListView`
- `SelectedItem` 属性 `MultiPage`
- `SelectedIndex` 和`SelectedItem`的属性 `Picker`
- `Value` 属性的`Slider`和 `Stepper`
- `IsToggled` 属性 `Switch`
- `On` 属性 `SwitchCell`
- `Time` 属性 `TimePicker`

这些特定的属性定义为`TwoWay`非常充分的理由：

当使用模型-视图-视图模型 (MVVM) 应用程序体系结构中使用数据绑定时，ViewModel 类是数据绑定源和视图，其中包含视图如`Slider`，是数据绑定的目标。 MVVM 绑定类似于**反向绑定**大于上一示例中的绑定的示例。 它是属性的很有可能您想要使用在 ViewModel 中，相应的值进行初始化的页面上的每个视图，但在视图中的更改应还影响 ViewModel 属性。

使用默认绑定模式的属性`TwoWay`是最有可能在 MVVM 方案中使用这些属性。

### <a name="one-way-to-source-bindings"></a>一个单向到源绑定

只读的可绑定属性具有默认绑定模式的`OneWayToSource`。 设置了默认绑定模式的仅一个读/写可绑定属性`OneWayToSource`:

- `SelectedItem` 属性 `ListView`

基本原理是，在绑定`SelectedItem`属性应导致绑定源设置。 在本文后面的示例重写该行为。

### <a name="one-time-bindings"></a>一次性绑定

多个属性具有默认绑定模式的`OneTime`。 这些是：

- `IsTextPredictionEnabled` 属性 `Entry`
- `Text``BackgroundColor`，并`Style`的属性`Span`。

绑定模式的属性为目标`OneTime`仅绑定上下文更改时，会更新。 对于这些目标属性的绑定，这将简化绑定基础结构，因为不需要监视的源属性中的更改。

## <a name="viewmodels-and-property-change-notifications"></a>Viewmodel 和属性更改通知

**简单颜色选择器**页演示如何使用简单的 ViewModel。 数据绑定允许用户选择颜色使用三个`Slider`元素的色调、 饱和度和亮度。

ViewModel 是数据绑定源。 ViewModel does*不*来定义可绑定属性，但它实现一种通知机制，允许绑定基础结构的属性值更改时得到通知。 此通知机制[ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged)接口，定义一个名为的单个属性[ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged)。 通常实现此接口的类激发事件时的一个公共属性值的改变。 事件不需要，如果该属性永远不会更改触发。 (`INotifyPropertyChanged`也会实现接口`BindableObject`和一个`PropertyChanged`可绑定属性值更改时触发事件。)

`HslColorViewModel`类定义了五个属性： `Hue`， `Saturation`， `Luminosity`，和`Color`相互关联的属性。 当任何一个的三个颜色组件更改值时`Color`属性将重新计算，和`PropertyChanged`的事件触发的所有四个属性：

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

当`Color`属性更改、 静态`GetNearestColorName`中的方法`NamedColor`类 (也包含在**DataBindingDemos**解决方案) 获取最接近已命名的颜色，并设置`Name`属性。 这`Name`属性有一个私有`set`访问器，因此它不能将设置从类外部的。

当 ViewModel 设置用作绑定源时，绑定基础结构附加到一个处理程序`PropertyChanged`事件。 这样一来的绑定可以对属性的更改的通知和能够更改后的值中设置目标属性。

但是，当目标属性 (或`Binding`目标属性上的定义) 具有`BindingMode`的`OneTime`，则没有必要的绑定基础结构上附加一个处理程序`PropertyChanged`事件。 更新目标属性时，才`BindingContext`更改，并不在源属性本身更改。

**简单颜色选择器**XAML 文件实例化`HslColorViewModel`中页面的资源字典并将初始化`Color`属性。 `BindingContext`的属性`Grid`设置为`StaticResource`绑定扩展来引用该资源：

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

`BoxView`， `Label`，和三个`Slider`视图继承从绑定上下文`Grid`。 这些视图是引用 ViewModel 中的源属性的所有绑定目标。 有关`Color`的属性`BoxView`，和`Text`属性`Label`，数据绑定是`OneWay`： 从 ViewModel 中的属性设置视图中的属性。

`Value`的属性`Slider`，，但`TwoWay`。 这样，每个`Slider`若要从 ViewModel 中，以及若要设置从每个 viewmodel 设置`Slider`。

首次运行该程序时， `BoxView`， `Label`，和三个`Slider`元素是从基于初始 ViewModel 的所有组`Color`时实例化 ViewModel 设置的属性。 在左侧 iOS 屏幕截图所示：

[![简单的颜色选择器](binding-mode-images/simplecolorselector-small.png "简单颜色选择器")](binding-mode-images/simplecolorselector-large.png#lightbox "简单颜色选择器")

操控滑块`BoxView`和`Label`相应地，Android 和 UWP 的屏幕截图所示更新。

实例化 ViewModel 中的资源字典是一个常用方法。 它也是可以实例化 ViewModel 中的属性元素标记`BindingContext`属性。 在中**简单颜色选择器**XAML 文件中，请尝试删除`HslColorViewModel`从资源字典并将其设置为`BindingContext`属性`Grid`如下所示：

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>

    ···

</Grid>
```

可以通过多种方式中设置的绑定上下文。 有时，代码隐藏文件实例化 ViewModel 并将其设置为`BindingContext`页属性。 这些是所有有效的方法。

## <a name="overriding-the-binding-mode"></a>重写绑定模式

如果在目标属性上的默认绑定模式不适用于特定的数据绑定，则可以通过设置替代[ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode)的属性`Binding`(或[ `Mode` ](xref:Xamarin.Forms.Xaml.BindingExtension.Mode)的属性`Binding`标记扩展) 的成员之一`BindingMode`枚举。

但是，将设置`Mode`属性设置为`TwoWay`始终无法按您所料。 例如，请尝试修改**替代 XAML 绑定**XAML 文件以包括`TwoWay`绑定定义中：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

它可能会发生的`Slider`将初始化为初始值的`Scale`属性，它是 1，但未按预期进行。 当`TwoWay`初始化绑定，目标从源中设置第一次，这意味着`Scale`属性设置为`Slider`默认值为 0。 当`TwoWay`上设置绑定`Slider`，则`Slider`从源最初设置。

您可以将绑定模式设置为`OneWayToSource`中**替代 XAML 绑定**示例：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

现在`Slider`初始化为 1 (默认值`Scale`) 但操作`Slider`不会影响`Scale`属性，因此这不是很有用。

> [!NOTE]
> [ `VisualElement` ](xref:Xamarin.Forms.VisualElement)类还定义[ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX)并[ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY)属性，可以缩放`VisualElement`中按不同方式水平和垂直方向。

重写的默认绑定模式非常有用的应用程序`TwoWay`涉及`SelectedItem`属性的`ListView`。 默认绑定模式是`OneWayToSource`。 如果在设置数据绑定`SelectedItem`属性来引用 ViewModel 中的源属性，则该 source 属性将设置从`ListView`所选内容。 但是，在某些情况下，你可能还想`ListView`从 ViewModel 进行初始化。

**的示例设置**页演示此技术。 此页表示应用程序设置，经常在 ViewModel 中，这种中定义的简单实现`SampleSettingsViewModel`文件：

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

每个应用程序设置是一个属性，将保存到一个名为方法中的 Xamarin.Forms 属性字典`SaveState`以及从该字典的构造函数中加载。 类的底部是两个方法，可以帮助简化 Viewmodel，使它们更不易出错。 `OnPropertyChanged`底部方法具有一个可选参数，设置为调用属性。 以字符串形式指定的属性名称时，这可以避免拼写错误。

`SetProperty`类中的方法执行更多： 它将被存储为一个字段的值设置为属性值进行比较，并仅调用`OnPropertyChanged`两个值不相等。

`SampleSettingsViewModel`类定义的背景颜色的两个属性：`BackgroundNamedColor`属性属于类型`NamedColor`，其中一个类也包含在**DataBindingDemos**解决方案。 `BackgroundColor`属性属于类型`Color`，并从获取`Color`属性的`NamedColor`对象。

`NamedColor`类使用.NET 反射来枚举在 Xamarin.Forms 中的所有静态公共字段`Color`结构，并将其存储在可从静态访问集合中其名称与`All`属性：

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

`App`类中**DataBindingDemos**项目定义一个名为属性`Settings`类型的`SampleSettingsViewModel`。 初始化此属性时`App`类实例化，并`SaveState`时调用方法`OnSleep`调用方法：

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

应用程序生命周期方法的详细信息，请参阅文章[**应用程序生命周期**](~/xamarin-forms/app-fundamentals/app-lifecycle.md)。

几乎所有其他项中处理**SampleSettingsPage.xaml**文件。 `BindingContext`页的设置使用`Binding`标记扩展： 绑定源是静态`Application.Current`属性，它是该实例的`App`类在项目中，并且`Path`设置为`Settings`属性，它是`SampleSettingsViewModel`对象：

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

页的所有子项都继承的绑定上下文。 大部分此页上的其他绑定到中的属性都是`SampleSettingsViewModel`。 `BackgroundColor`属性用于设置`BackgroundColor`的属性`StackLayout`，和`Entry`， `DatePicker`， `Switch`，和`Stepper`属性被绑定到 ViewModel 中的其他属性。

`ItemsSource`的属性`ListView`设置为静态`NamedColor.All`属性。 这样就会填写`ListView`所有`NamedColor`实例。 中每一项`ListView`，该项目的绑定上下文设置为`NamedColor`对象。 `BoxView`并`Label`中`ViewCell`绑定到属性中`NamedColor`。

`SelectedItem`的属性`ListView`属于类型`NamedColor`，并将其绑定到`BackgroundNamedColor`属性`SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

默认绑定模式`SelectedItem`是`OneWayToSource`，用于从所选的项设置的 ViewModel 属性。 `TwoWay`模式允许`SelectedItem`从 ViewModel 进行初始化。

但是，当`SelectedItem`这种方式，设置`ListView`不会自动滚动以显示所选的项。 在代码隐藏文件中的一小段代码，有必要：

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

当首次运行时，在左侧的 iOS 屏幕快照显示了的程序。 中的构造函数`SampleSettingsViewModel`初始化的背景色为白色，并且在选择的内容`ListView`:

[![示例设置](binding-mode-images/samplesettings-small.png "示例设置")](binding-mode-images/samplesettings-large.png#lightbox "示例设置")

其他两个屏幕快照显示更改后的设置。 在与此页进行试验，记得将进入睡眠状态或终止在设备或仿真程序认为其正在运行的程序。 终止该程序从 Visual Studio 调试器将不会导致`OnSleep`重写中`App`类来调用。

将在下一篇文章中了解如何指定[**字符串格式设置**](string-formatting.md)上设置的数据绑定`Text`属性`Label`。


## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [数据绑定 Xamarin.Forms 书籍章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
