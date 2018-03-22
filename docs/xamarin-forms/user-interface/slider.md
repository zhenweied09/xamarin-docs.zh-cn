---
title: "使用滑块"
description: "使用滑块选择来自一个连续值的范围。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/16/2018
ms.openlocfilehash: ce5d8c46282d3831edcf58af63b66d1f6292f75b
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="using-slider"></a>使用滑块

_使用滑块选择来自一个连续值的范围。_

Xamarin.Forms [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)是可操作的用户选择的水平条`double`连续范围中的值。 

`Slider`定义的类型的三个属性`double`:

- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) 是的范围的最低默认值为 0。
- [`Maximum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) 是默认值为 1 的最大的范围。
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Value/) 是滑块的值，范围介于之间`Minimum`和`Maximum`和默认值为 0。

所有三个属性由`BindableProperty`对象。 `Value`属性具有的默认绑定模式`BindingMode.TwoWay`，这意味着它很适合作为中使用的应用程序的绑定源[模型-视图-视图模型 (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构。 

> [!WARNING]
> 在内部，`Slider`确保`Minimum`是小于`Maximum`。 如果`Minimum`或`Maximum`曾经设置以便`Minimum`是不小于`Maximum`，将引发异常。 请参阅[**预防措施**](#precautions)节设置的详细信息`Minimum`和`Maximum`属性。

`Slider`将强制`Value`属性使之之间`Minimum`和`Maximum`(含） 之间。 如果`Minimum`属性设置为一个值大于`Value`属性，`Slider`设置`Value`属性`Minimum`。 同样，如果`Maximum`设置为一个值小于`Value`，然后`Slider`设置`Value`属性`Maximum`。 

`Slider` 定义[ `ValueChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Slider.ValueChanged/)时激发的事件`Value`更改，不论是通过用户操作的`Slider`或当程序集`Value`直接属性。 A`ValueChanged`时，也会激发事件`Value`属性被强制转换，如前面的段落中所述。 

[ `ValueChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ValueChangedEventArgs/)附带的对象`ValueChanged`事件具有两个属性，这两个类型`double`: [ `OldValue` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ValueChangedEventArgs.OldValue/)和[ `NewValue` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ValueChangedEventArgs.NewValue/). 次激发事件时，值`NewValue`相同`Value`属性`Slider`对象。

> [!WARNING]
> 不要使用不受约束的水平布局选项`Center`， `Start`，或`End`与`Slider`。 在 Android 和 UWP 上,`Slider`到栏的长度为零，并在 iOS，栏上的折叠是很短。 保留默认值`HorizontalOptions`设置`Fill`，并且不使用的宽度为`Auto`时将`Slider`中`Grid`布局。

## <a name="basic-slider-code-and-markup"></a>基本滑块代码和标记

[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)示例开始功能相同，但不同的方式实现的三个页。 第一页仅使用 C# 代码、 第二个与事件处理程序在代码中，使用 XAML 和第三个是能够通过使用 XAML 文件中的数据绑定来避免事件处理程序。

### <a name="creating-a-slider-in-code"></a>在代码中创建一个滑块

**基本滑块代码**页面[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)示例演示显示创建`Slider`和两个`Label`代码中的对象：

```csharp
public class BasicSliderCodePage : ContentPage
{
    public BasicSliderCodePage()
    {
        Label rotationLabel = new Label
        {
            Text = "ROTATING TEXT",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Label displayLabel = new Label
        {
            Text = "(uninitialized)",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Slider slider = new Slider
        {
            Maximum = 360
        };
        slider.ValueChanged += (sender, args) =>
        {
            rotationLabel.Rotation = slider.Value;
            displayLabel.Text = String.Format("The Slider value is {0}", args.NewValue);
        };

        Title = "Basic Slider Code";
        Padding = new Thickness(10, 0);
        Content = new StackLayout
        {
            Children =
            {
                rotationLabel,
                slider,
                displayLabel
            }
        };
    }
}
```

`Slider`初始化具有`Maximum`为 360 的属性。 `ValueChanged`处理程序`Slider`使用`Value`属性`slider`对象以设置`Rotation`属性的第一个`Label`并使用`String.Format`方法替换`NewValue`属性事件自变量以设置`Text`第二个属性`Label`。 这两种方法来获取的当前值`Slider`是可互换。 

此处是在 iOS、 Android 和通用 Windows 平台 (UWP) 上运行的设备的程序：

[![基本滑块代码](slider-images/BasicSliderCode.png "基本滑块代码")](slider-images/BasicSliderCode-Large.png#lightbox)

第二个`Label`直到显示"（未初始化）"文本`Slider`操作的情况下，第一个`ValueChanged`要激发的事件。 请注意，不同的三个平台的显示的小数位数。 这些差异与相关的平台实现`Slider`和更高版本中的部分中的这篇文章讨论了[实现的平台的差异](#implementations)。

### <a name="creating-a-slider-in-xaml"></a>在 XAML 中创建一个滑块

**基本滑块 XAML**页在功能上与相同**基本滑块代码**主要在 XAML 中但实现：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderXamlPage"
             Title="Basic Slider XAML"
             Padding="10, 0">
    <StackLayout>
        <Label x:Name="rotatingLabel" 
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider Maximum="360"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件包含的处理程序`ValueChanged`事件：

```csharp
public partial class BasicSliderXamlPage : ContentPage
{
    public BasicSliderXamlPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double value = args.NewValue;
        rotatingLabel.Rotation = value;
        displayLabel.Text = String.Format("The Slider value is {0}", value);
    }
}
```

你也可为要获取的事件处理程序`Slider`，在触发事件通过`sender`自变量。 `Value`属性包含的当前值：

```csharp
double value = ((Slider)sender).Value;
```

如果`Slider`对象中包含的 XAML 文件的名称提供给`x:Name`属性 (例如，"slider")，则事件处理程序无法直接引用该对象：

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>数据绑定滑块

**基本滑块绑定**页显示如何编写一个可消除了几乎等效程序`Value`事件处理程序使用[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderBindingsPage"
             Title="Basic Slider Bindings"
             Padding="10, 0">
    <StackLayout>
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference slider}, 
                                  Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360" />

        <Label x:Name="displayLabel"
               Text="{Binding Source={x:Reference slider}, 
                              Path=Value, 
                              StringFormat='The Slider value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Rotation`属性的第一个`Label`绑定到`Value`属性`Slider`、 原样`Text`第二个属性`Label`与`StringFormat`规范。 **基本滑块绑定**页函数稍有不同的两个的前一页从： 首先显示的页面时，第二个`Label`显示值的文本字符串。 这是使用数据绑定的优点。 若要显示不带数据绑定的文本，你需要专门初始化`Text`属性`Label`或模拟的激发`ValueChanged`通过从类构造函数调用事件处理程序的事件。

<a name="precautions" />

## <a name="precautions"></a>预防措施

值`Minimum`属性必须始终为的值小于`Maximum`属性。 以下代码段会导致`Slider`以引发异常：

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

C# 编译器将生成在序列中，设置这两个属性的代码以及何时`Minimum`属性设置为 10，大于默认值`Maximum`值为 1。 你可以通过设置在此情况下避免异常`Maximum`属性第一个：

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

设置`Maximum`为 20 不是问题因为它是大于默认值`Minimum`设置为 0。 当`Minimum`设置，值是小于`Maximum`的值为 20。

在 XAML 中存在相同的问题。 设置的属性，可确保顺序`Maximum`是否始终大于`Minimum`:

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

你可以设置`Minimum`和`Maximum`值为负数字，但仅将按顺序其中`Minimum`是始终小于`Maximum`:

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

`Value`属性始终为大于或等于`Minimum`值且小于或等于`Maximum`。 如果`Value`设置为此范围外的值，该值将被强制为之间的范围内，但不会引发异常。 例如，此代码将*不*引发异常：

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

相反，`Value`属性被强制转换为`Maximum`值为 1。

以下是代码段，如上所示： 

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

当`Minimum`已设置为 10，则`Value`也设置为 10。 

如果`ValueChanged`事件处理程序已附加时，`Value`属性被强制转换为其默认值以外的 0，则`ValueChanged`激发事件。 下面是 XAML 代码段： 

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

当`Minimum`设置为 10，`Value`也设置为 10，和`ValueChanged`激发事件。 这可能是构造的网页的其余内容，并处理程序可能会尝试以引用尚未创建的页面上的其他元素之前。 你可能想要添加到某些代码`ValueChanged`检查的处理程序`null`页面上的其他元素的值。 或者，可以设置`ValueChanged`之后的事件处理程序`Slider`尚未初始化值。

<a name="implementations" />

## <a name="platform-implementation-differences"></a>实现的平台的差异

前面所示的屏幕截图显示的值`Slider`具有不同数量的位小数。 这一点与我们如何`Slider`在 Android 和 UWP 平台上实现。

### <a name="the-android-implementation"></a>Android 实现 

Android 实现`Slider`基于 Android [ `SeekBar` ](https://developer.xamarin.com/api/type/Android.Widget.SeekBar/)和始终设置[ `Max` ](https://developer.xamarin.com/api/property/Android.Widget.ProgressBar.Max/)到 1000年的属性。 这意味着，`Slider`在 Android 上具有仅另外 1001 离散值。 如果你设置`Slider`能够`Minimum`为 0 和`Maximum`5000，然后作为`Slider`操作，`Value`属性具有值 0、 5、 10、 15 和等。 

### <a name="the-uwp-implementation"></a>UWP 实现

UWP 实现`Slider`基于 UWP [ `Slider` ](/uwp/api/windows.ui.xaml.controls.slider)控件。 `StepFrequency` UWP 属性`Slider`设置到的差异`Maximum`和`Minimum`属性除以月 10 日，但是不大于 1。 

例如，对于默认范围为 0 到 1，`StepFrequency`属性设置为 0.1。 作为`Slider`操作，`Value`属性被限制为 0、 0.1、 0.2、 0.3、 0.4、 0.5、 0.6、 0.7、 0.8、 0.9 和 1.0。 (这是中的最后一页中显而易见[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)示例。)当之间的差异`Maximum`和`Minimum`属性为 10 或更高版本，则`StepFrequency`设置为 1，与`Value`属性具有整数值。 

### <a name="the-stepslider-solution"></a>StepSlider 解决方案

更通用`StepSlider`中所述[章 27。自定义呈现器](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)书籍的*具有 Xamarin.Forms 创建移动应用*。 `StepSlider`类似于`Slider`但添加了`Steps`属性指定值之间的数目`Minimum`和`Maximum`。

## <a name="sliders-for-color-selection"></a>滑块的颜色选取

中的两个页面最终[ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)示例都使用三个`Slider`颜色选择的实例。 第一页处理代码隐藏文件中的所有交互，而第二页显示如何使用视图模型的数据绑定。 

### <a name="handling-sliders-in-the-code-behind-file"></a>处理的代码隐藏文件中的滑块 

**RGB 颜色滑块**页实例化`BoxView`以显示一种颜色，三个`Slider`实例选择的颜色和第三个的红色、 绿色和蓝色组件`Label`用于显示这些颜色的元素值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.RgbColorSlidersPage"
             Title="RGB Color Sliders">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="Maximum" Value="255" />
            </Style>
            
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView x:Name="boxView"
                 Color="Black"
                 VerticalOptions="FillAndExpand" />

        <Slider x:Name="redSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="redLabel" />

        <Slider x:Name="greenSlider" 
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="greenLabel" />

        <Slider x:Name="blueSlider" 
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="blueLabel" />
    </StackLayout>
</ContentPage>
```

A`Style`提供所有三个`Slider`的 0 到 255 之间的范围的元素。 `Slider`元素共用同一个`ValueChanged`处理程序中，这在代码隐藏文件中实现：

```csharp
public partial class RgbColorSlidersPage : ContentPage
{
    public RgbColorSlidersPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (sender == redSlider)
        {
            redLabel.Text = String.Format("Red = {0:X2}", (int)args.NewValue);
        }
        else if (sender == greenSlider)
        {
            greenLabel.Text = String.Format("Green = {0:X2}", (int)args.NewValue);
        }
        else if (sender == blueSlider)
        {
            blueLabel.Text = String.Format("Blue = {0:X2}", (int)args.NewValue);
        }

        boxView.Color = Color.FromRgb((int)redSlider.Value,
                                      (int)greenSlider.Value,
                                      (int)blueSlider.Value);
    }
}
```

第一个部分集`Text`属性之一的`Label`为短文本字符串的值，该值指示实例`Slider`以十六进制表示。 然后，所有三个`Slider`实例进行访问以创建`Color`RGB 组件中的值：

[![RGB 颜色滑块](slider-images/RgbColorSliders.png "RGB 颜色滑块")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>绑定到 ViewModel 的滑块

**HSL 颜色滑块**页显示如何使用 ViewModel 执行用于创建计算`Color`从色调、 饱和度和亮度值的值。 如所有 Viewmodel`HSLColorViewModel`类实现`INotifyPropertyChanged`接口，并触发`PropertyChanged`事件的一个属性发生更改时：

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;

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
            }
        }
        get
        {
            return color;
        }
    }
}
```

Viewmodel 和`INotifyPropertyChanged`文章中讨论了接口[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

**HslColorSlidersPage.xaml**文件实例化`HslColorViewModel`并将它设置为该页面的`BindingContext`属性。 这样，要将绑定到视图模型中的属性的 XAML 文件中的所有元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SliderDemos"
             x:Class="SliderDemos.HslColorSlidersPage"
             Title="HSL Color Sliders">

    <ContentPage.BindingContext>
        <local:HslColorViewModel Color="Chocolate" />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</ContentPage> 
```

作为`Slider`操作元素，`BoxView`和`Label`从 ViewModel 更新为元素：

[![HSL 颜色滑块](slider-images/HslColorSliders.png "HSL 颜色滑块")](slider-images/HslColorSliders-Large.png#lightbox)

`StringFormat`组件的`Binding`标记扩展设置为"F2"的格式，以显示两位小数。 (在文章中讨论中数据绑定的格式设置字符串[字符串格式设置](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md)。)但是，UWP 版本的程序仅限于值为 0、 0.1、 0.2...0.9，和 1.0。 这是 UWP 的实现的直接结果`Slider`上文所述的部分中[实现的平台的差异](#implementations)。

## <a name="related-links"></a>相关链接

- [滑块演示示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)
- [滑块 API](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)
