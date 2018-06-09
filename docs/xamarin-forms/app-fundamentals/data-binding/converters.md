---
title: Xamarin.Forms 绑定值转换器
description: 此文章介绍了如何强制转换或通过实现值转换器 （这是也称为绑定转换器或绑定值转换器） 转换 Xamarin.Forms 数据绑定中的值。
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: a5bd52d43ef93013537f30c7d5e0c31cbf336d07
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241824"
---
# <a name="xamarinforms-binding-value-converters"></a>Xamarin.Forms 绑定值转换器

数据绑定到目标属性，并在某些情况下从目标属性的源属性，通常将数据传输源属性中。 源和目标属性时相同的类型，或一个类型可以转换为另一种类型通过隐式转换时，此传输非常简单。 在不是这种情况，类型转换必须进行。

在[**字符串格式设置**](string-formatting.md)篇文章中，你已了解如何使用`StringFormat`数据绑定来转换为字符串的任何类型的属性。 对于其他类型的转换，你需要中实现的类编写专用的代码[ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/)接口。 (通用 Windows 平台包含一个名为的类似类[ `IValueConverter` ](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/)中`Windows.UI.Xaml.Data`命名空间，但这`IValueConverter`处于`Xamarin.Forms`命名空间。)类实现`IValueConverter`称为*值转换器*，但它们通常还称为*绑定转换器*或*绑定值转换器*。

## <a name="the-ivalueconverter-interface"></a>IValueConverter 接口

假设你想要定义数据绑定源属性所在的类型`int`但 target 属性不`bool`。 你想要生成此数据绑定`false`值等于 0，整数源时和`true`否则为。  

可以使用实现的类来执行此操作`IValueConverter`接口：

```csharp
public class IntToBoolConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value != 0;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? 1 : 0;
    }
}
```

此类的实例设置[ `Converter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Converter/)属性`Binding`类或[ `Converter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Converter/)属性`Binding`标记扩展。 此类将成为数据绑定的一部分。

`Convert`时数据从源移动到目标中调用方法`OneWay`或`TwoWay`绑定。 `value`参数是对象或从数据绑定源的值。 该方法必须返回的数据绑定目标类型的值。 此处显示强制转换的方法`value`参数`int`然后将它与 0 进行比较`bool`返回值。

`ConvertBack`数据从目标移动到中的源时，调用方法`TwoWay`或`OneWayToSource`绑定。 `ConvertBack` 执行相反的转换： 它假定`value`参数是`bool`目标，并将其转换为`int`返回源值。

如果数据绑定还包括`StringFormat`设置，值转换器之前调用的结果将格式化为字符串。

**启用按钮**页面[**数据绑定演示**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)示例演示如何在数据绑定中使用此值转换器。 `IntToBoolConverter`页面的资源字典中实例化。 它然后使用引用`StaticResource`要设置的标记扩展`Converter`在两个数据绑定中的属性。 它是很常见，共享数据的页上的多个数据绑定中的转换器：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.EnableButtonsPage"
             Title="Enable Buttons">
    <ContentPage.Resources>
        <ResourceDictionary>
            <local:IntToBoolConverter x:Key="intToBool" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <Entry x:Name="entry1"
               Text=""
               Placeholder="enter search term"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Search"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry1},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />

        <Entry x:Name="entry2"
               Text=""
               Placeholder="enter destination"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Submit"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry2},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />
    </StackLayout>
</ContentPage>
```

如果你的应用程序的多个页中使用的值转换器，则可以在资源字典中化**App.xaml**文件。

**启用按钮**页演示一个常见需要时`Button`执行基于用户键入的文本操作`Entry`视图。 如果执行任何操作已键入到`Entry`、`Button`应禁用。 每个`Button`包含数据绑定中的其`IsEnabled`属性。 数据绑定源是`Length`属性`Text`的相应属性`Entry`。 如果该`Length`属性不是 0，值转换器返回`true`和`Button`启用：

[![启用按钮](converters-images/enablebuttons-small.png "启用按钮")](converters-images/enablebuttons-large.png#lightbox "启用按钮")

请注意，`Text`的每个`Entry`初始化为空字符串。 `Text`属性是`null`默认情况下，数据绑定将不起作用在这种情况下。

某些值转换器是专门针对特定应用程序、 编写的时其他人已通用化。 如果你知道值转换器将只应用于`OneWay`绑定，则`ConvertBack`方法可以只返回`null`。

`Convert`隐式上面所示方法假定`value`自变量为类型`int`和返回值必须为类型`bool`。 同样，`ConvertBack`方法假设`value`自变量为类型`bool`和返回值是`int`。 如果不是这样，将发生运行时异常。

你可以编写值转换器具有更普遍并接受多种不同类型的数据。 `Convert`和`ConvertBack`方法可以使用`as`或`is`运算符`value`参数，也可以调用`GetType`上该参数是确定其类型，然后再执行适当的。 每个方法的返回值的预期的类型由`targetType`参数。 有时，值转换器用于数据绑定的不同的目标类型;值转换器可以使用`targetType`参数，以执行正确的类型的转换。

如果正在执行的转换是不同的不同区域性的使用`culture`为此目的的参数。 `parameter`参数`Convert`和`ConvertBack`更高版本在本文中讨论。

## <a name="binding-converter-properties"></a>绑定转换器属性

值转换器类可具有属性和泛型参数。 此特定值转换器转换`bool`将源类型的对象与从`T`目标：

```csharp
public class BoolToObjectConverter<T> : IValueConverter
{
    public T TrueObject { set; get; }

    public T FalseObject { set; get; }

    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? TrueObject : FalseObject;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return ((T)value).Equals(TrueObject);
    }
}
```

**交换机指示器**页演示如何使用它以显示的值`Switch`视图。 尽管很普遍实例化为资源字典中的资源的值转换器，此页演示了一种替代方法： 每个值转换器实例化之间`Binding.Converter`属性元素标记。 `x:TypeArguments`指示泛型自变量，和`TrueObject`和`FalseObject`都设置为该类型的对象：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SwitchIndicatorsPage"
             Title="Switch Indicators">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>

            <Style TargetType="Switch">
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Subscribe?" />
            <Switch x:Name="switch1" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch1}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Of course!"
                                                         FalseObject="No way!" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Allow popups?" />
            <Switch x:Name="switch2" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Yes"
                                                         FalseObject="No" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
                <Label.TextColor>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Color"
                                                         TrueObject="Green"
                                                         FalseObject="Red" />
                        </Binding.Converter>
                    </Binding>
                </Label.TextColor>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Learn more?" />
            <Switch x:Name="switch3" />
            <Label FontSize="18"
                   VerticalOptions="Center">
                <Label.Style>
                    <Binding Source="{x:Reference switch3}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Style">
                                <local:BoolToObjectConverter.TrueObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Indubitably!" />
                                        <Setter Property="FontAttributes" Value="Italic, Bold" />
                                        <Setter Property="TextColor" Value="Green" />
                                    </Style>                                    
                                </local:BoolToObjectConverter.TrueObject>

                                <local:BoolToObjectConverter.FalseObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Maybe later" />
                                        <Setter Property="FontAttributes" Value="None" />
                                        <Setter Property="TextColor" Value="Red" />
                                    </Style>
                                </local:BoolToObjectConverter.FalseObject>
                            </local:BoolToObjectConverter>
                        </Binding.Converter>
                    </Binding>
                </Label.Style>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

在过去的三个`Switch`和`Label`对，泛型自变量设置为`Style`，和整个`Style`对象提供的值`TrueObject`和`FalseObject`。 这些重写的隐式样式`Label`设置资源字典中，因此该样式中的属性显式分配给`Label`。 切换`Switch`导致相应`Label`以反映此更改：

[![切换指示器](converters-images/switchindicators-small.png "切换指示器")](converters-images/switchindicators-large.png#lightbox "切换指示器")

还有可能要使用[ `Triggers` ](~/xamarin-forms/app-fundamentals/triggers.md)基于其他视图的用户界面中实现类似的更改。

## <a name="binding-converter-parameters"></a>绑定转换器参数

`Binding`类定义[ `ConverterParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.ConverterParameter/)属性，与`Binding`标记扩展还定义[ `ConverterParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.ConverterParameter/)属性。 如果设置此属性，则该值传递到`Convert`和`ConvertBack`方法作为`parameter`自变量。 即使在多个数据绑定，之间共享的值转换器实例`ConverterParameter`可以不同来执行稍有不同的转换。

使用`ConverterParameter`通过颜色选择程序演示了。 在这种情况下，`RgbColorViewModel`具有三个属性的类型`double`名为`Red`， `Green`，和`Blue`，使用它来构造`Color`值：

```csharp
public class RgbColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Red
    {
        set
        {
            if (color.R != value)
            {
                Color = new Color(value, color.G, color.B);
            }
        }
        get
        {
            return color.R;
        }
    }

    public double Green
    {
        set
        {
            if (color.G != value)
            {
                Color = new Color(color.R, value, color.B);
            }
        }
        get
        {
            return color.G;
        }
    }

    public double Blue
    {
        set
        {
            if (color.B != value)
            {
                Color = new Color(color.R, color.G, value);
            }
        }
        get
        {
            return color.B;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Red"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Green"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Blue"));
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

`Red`， `Green`，和`Blue`属性范围介于 0 和 1 之间。 但是，你可能希望，组件显示为两个数字的十六进制值。

若要在 XAML 中显示为十六进制值，它们必须乘以 255，转换为整数，而然后使用"X2"的规范格式中`StringFormat`属性。 可以由值转换器处理的前两个任务 （乘以 255 和将转换为整数）。 若要使值转换器如尽可能通用化，乘数可以指定与`ConverterParameter`属性，这意味着它进入`Convert`和`ConvertBack`方法作为`parameter`自变量：

```csharp
public class DoubleToIntConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)Math.Round((double)value * GetParameter(parameter));
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value / GetParameter(parameter);
    }

    double GetParameter(object parameter)
    {
        if (parameter is double)
            return (double)parameter;

        else if (parameter is int)
            return (int)parameter;

        else if (parameter is string)
            return double.Parse((string)parameter);

        return 1;
    }
}
```

`Convert`将从转换`double`到`int`时乘以`parameter`值;`ConvertBack`将划分整数`value`自变量`parameter`并返回`double`结果。 (在程序中如下所示，值转换器仅用于字符串格式设置，因此`ConvertBack`未使用。)

一种`parameter`自变量是可能不同，具体取决于是否在代码或 XAML 中定义的数据绑定。 如果`ConverterParameter`属性`Binding`设置在代码中，很可能要设置为数字值：

```csharp
binding.ConverterParameter = 255;
```

`ConverterParameter`属性属于类型`Object`，因此 C# 编译器将文本 255 解释为一个整数，并将属性设置为该值。

在 XAML 中，但是，`ConverterParameter`可能以如下设置：

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

255 的如下所示数字，但因为`ConverterParameter`属于类型`Object`，XAML 分析器将 255 视为一个字符串。

因此，如上所示的值转换器包括单独`GetParameter`处理的用例的方法`parameter`类型`double`， `int`，或`string`。  

**RGB 颜色选择器**页实例化`DoubleToIntConverter`以下两种隐式样式的定义其资源字典中：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.RgbColorSelectorPage"
             Title="RGB Color Selector">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <local:DoubleToIntConverter x:Key="doubleToInt" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:RgbColorViewModel Color="Gray" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Red}" />
            <Label Text="{Binding Red,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0:X2}'}" />

            <Slider Value="{Binding Green}" />
            <Label Text="{Binding Green,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0:X2}'}" />

            <Slider Value="{Binding Blue}" />
            <Label>
                <Label.Text>
                    <Binding Path="Blue"
                             StringFormat="Blue = {0:X2}"
                             Converter="{StaticResource doubleToInt}">
                        <Binding.ConverterParameter>
                            <x:Double>255</x:Double>
                        </Binding.ConverterParameter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

值`Red`和`Green`属性将显示与`Binding`标记扩展。 `Blue`属性，但是，实例化`Binding`类以演示如何显式`double`值可以设置为`ConverterParameter`属性。

下面是结果：

[![RGB 颜色选择器](converters-images/rgbcolorselector-small.png "RGB 颜色选择器")](converters-images/rgbcolorselector-large.png#lightbox "RGB 颜色选择器")


## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [从 Xamarin.Forms 簿的数据绑定章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
