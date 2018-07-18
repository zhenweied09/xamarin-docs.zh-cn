---
title: Xamarin.Forms 绑定值转换器
description: 此文章介绍了如何强制转换或转换 Xamarin.Forms 数据绑定中的值通过实现值转换器 （这是也称为绑定转换器或绑定值转换器）。
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 28892692133020de1fa5a6eb007bb3f9bcf2612b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997474"
---
# <a name="xamarinforms-binding-value-converters"></a>Xamarin.Forms 绑定值转换器

数据绑定到目标属性，并在某些情况下从目标属性到源属性，通常将数据传输源属性中。 当源和目标属性都是相同的类型，或一个类型可以转换为另一种类型的隐式转换通过时，此传输非常简单。 如果不是这种情况，类型转换必须执行的位置。

在中[**字符串格式设置**](string-formatting.md)文章中，你已了解如何使用`StringFormat`数据绑定来转换为字符串的任何类型的属性。 对于其他类型的转换，需要编写一些专门的代码中实现的类[ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter)接口。 (通用 Windows 平台包含一个名为的相似类[ `IValueConverter` ](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/)中`Windows.UI.Xaml.Data`命名空间，但这`IValueConverter`处于`Xamarin.Forms`命名空间。)类实现`IValueConverter`称为*值转换器*，但它们通常也称为*绑定转换器*或*绑定值转换器*。

## <a name="the-ivalueconverter-interface"></a>IValueConverter 接口

假设你想要定义其中源属性是类型的数据绑定`int`但目标属性为`bool`。 你想要生成此数据绑定`false`值等于 0，整数源时和`true`否则为。  

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

设置到此类的实例[ `Converter` ](xref:Xamarin.Forms.Binding.Converter)的属性`Binding`类或设置为[ `Converter` ](xref:Xamarin.Forms.Xaml.BindingExtension.Converter)属性`Binding`标记扩展。 此类将成为数据绑定的一部分。

`Convert`时数据将从源系统移到目标中调用方法`OneWay`或`TwoWay`绑定。 `value`参数是对象或数据绑定源中的值。 该方法必须返回的数据绑定目标类型的值。 如下所示的强制转换的方法`value`参数`int`，然后将它与 0 进行比较`bool`返回值。

`ConvertBack`时数据从目标移动到的源中调用方法`TwoWay`或`OneWayToSource`绑定。 `ConvertBack` 执行相反的转换： 它假定`value`参数是`bool`目标，并将其转换为`int`返回源值。

如果数据绑定还包括`StringFormat`设置，值转换器之前调用结果格式化为字符串。

**启用按钮**页面[**数据绑定演示**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)示例演示如何在数据绑定中使用此值转换器。 `IntToBoolConverter`在页面的资源字典中实例化。 它然后使用引用`StaticResource`标记扩展来设置`Converter`中两个数据绑定属性。 它是共享的页上的多个数据绑定中的数据转换器非常常见：

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

如果你的应用程序的多个页中使用值转换器，则您可以在实例化它中的资源字典**App.xaml**文件。

**启用按钮**页说明时需要一种常见`Button`执行一个操作基于文本的用户键入到`Entry`视图。 如果不具有已键入`Entry`，则`Button`应禁用。 每个`Button`包含数据绑定在其`IsEnabled`属性。 数据绑定源是`Length`的属性`Text`的相应属性`Entry`。 如果该`Length`属性不是 0，则返回值转换器`true`和`Button`已启用：

[![启用按钮](converters-images/enablebuttons-small.png "启用按钮")](converters-images/enablebuttons-large.png#lightbox "启用按钮")

请注意，`Text`中每个属性`Entry`初始化为空字符串。 `Text`属性是`null`默认情况下，数据绑定不会在这种情况下。

某些值转换器编写专门为特定应用程序，而其他人已通用化。 如果您知道值转换器将只应用于`OneWay`绑定，则`ConvertBack`方法可以只返回`null`。

`Convert`隐式上面所示方法假定`value`自变量的类型是`int`并且返回值必须是类型`bool`。 同样，`ConvertBack`方法假设`value`自变量的类型是`bool`和返回值是`int`。 如果这不是这种情况，将发生运行时异常。

您可以编写更通用化并接受多个不同类型的数据值转换器。 `Convert`并`ConvertBack`方法可使用`as`或`is`运算符`value`参数，也可以调用`GetType`上该参数，以确定其类型，然后再执行一些适当的。 每个方法的返回值的预期的类型由`targetType`参数。 有时，使用数据绑定的不同的目标类型; 使用值转换器可以使用值转换器`targetType`参数来执行转换为正确的类型。

如果正在执行的转换为不同的区域性不同，请使用`culture`实现此目的的参数。 `parameter`自变量`Convert`和`ConvertBack`本文稍后介绍。

## <a name="binding-converter-properties"></a>绑定转换器属性

值转换器类可以具有属性和泛型参数。 此特定值转换器转换`bool`从源类型的对象到`T`目标：

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

**交换机指示器**页说明如何使用它来显示的值`Switch`视图。 虽然通常会作为资源字典中的资源实例化的值转换器，但此页将演示一种替代方法： 之间实例化每个值转换器`Binding.Converter`属性元素标记。 `x:TypeArguments`指示泛型自变量，并`TrueObject`和`FalseObject`均设置为该类型的对象：

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

在过去三个`Switch`并`Label`对，泛型自变量设置为`Style`，和整个`Style`对象提供的值`TrueObject`和`FalseObject`。 这些重写的隐式样式`Label`在资源字典中设置，因此该样式中的属性显式分配给`Label`。 切换`Switch`会导致相应`Label`以反映此更改：

[![切换指示器](converters-images/switchindicators-small.png "切换指示器")](converters-images/switchindicators-large.png#lightbox "切换指标")

它也是可以使用[ `Triggers` ](~/xamarin-forms/app-fundamentals/triggers.md)基于其他视图的用户界面中实现类似的更改。

## <a name="binding-converter-parameters"></a>绑定转换器参数

`Binding`类定义[ `ConverterParameter` ](xref:Xamarin.Forms.Binding.ConverterParameter)属性，并且`Binding`标记扩展还定义[ `ConverterParameter` ](xref:Xamarin.Forms.Xaml.BindingExtension.ConverterParameter)属性。 如果设置此属性，则该值将传递到`Convert`并`ConvertBack`方法，例如`parameter`参数。 即使在多个数据绑定，间共享的值转换器实例`ConverterParameter`可以不同执行稍有不同的转换。

使用`ConverterParameter`演示了颜色选择程序。 在这种情况下，`RgbColorViewModel`有三个属性的类型`double`名为`Red`， `Green`，并`Blue`它用来构造`Color`值：

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

`Red`， `Green`，和`Blue`属性范围介于 0 和 1 之间。 但是，你可能倾向，组件显示为两位数字的十六进制值。

若要在 XAML 中显示为十六进制值，它们必须乘以 255，转换为整数，然后使用"X2"的规范格式中`StringFormat`属性。 值转换器可以处理的前两个任务 （乘以 255 和将转换为整数）。 若要使值转换器以尽可能通用，乘法因子可以指定与`ConverterParameter`属性，这意味着它将进入`Convert`并`ConvertBack`的方法作为`parameter`参数：

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

`Convert`将从转换`double`到`int`时乘以`parameter`值;`ConvertBack`除以整数`value`自变量`parameter`，并返回`double`结果。 (在程序中如下所示，值转换器仅用于字符串格式设置，因此`ConvertBack`不使用。)

类型`parameter`参数是可能不同，具体取决于是否在代码或 XAML 中定义的数据绑定。 如果`ConverterParameter`属性的`Binding`设置在代码中，很可能设置为数字值：

```csharp
binding.ConverterParameter = 255;
```

`ConverterParameter`属性属于类型`Object`，因此，C# 编译器将文字 255 解释为一个整数，并且将属性设置为该值。

在 XAML，但是，`ConverterParameter`很可能设置如下：

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

255 看起来像一个数字，但，因为`ConverterParameter`属于类型`Object`，XAML 分析器将 255 视为字符串。

为此，如上所示的值转换器包含一个单独`GetParameter`处理的情况下方法`parameter`类型的正在`double`， `int`，或`string`。  

**RGB 颜色选择器**页上实例化`DoubleToIntConverter`在以下两个隐式样式的定义及其资源字典中：

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

值`Red`并`Green`属性将显示与`Binding`标记扩展。 `Blue`属性，但是，实例化`Binding`类，以演示如何使用显式`double`可以将值设置为`ConverterParameter`属性。

下面是结果：

[![RGB 颜色选择器](converters-images/rgbcolorselector-small.png "RGB 颜色选择器")](converters-images/rgbcolorselector-large.png#lightbox "RGB 颜色选择器")


## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [数据绑定 Xamarin.Forms 书籍章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
