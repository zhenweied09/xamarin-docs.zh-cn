---
title: Xamarin.Forms 基本绑定
description: 本文介绍如何使用 Xamarin.Forms 的数据绑定至少链接的两个对象之间的属性对其中之一通常是用户界面对象。 这两个对象称为目标和源。
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: e31cba5c61624b0bca03443262b95d7497564750
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675193"
---
# <a name="xamarinforms-basic-bindings"></a>Xamarin.Forms 基本绑定

Xamarin.Forms 数据绑定链接一对两个对象，在至少一个通常是用户界面对象之间的属性。 这两个对象调用*目标*并*源*:

- *目标*位于对象 （或属性） 设置数据绑定。
- *源*是由数据绑定引用的对象 （和属性）。

这种区别有时可能有点令人困惑： 在最简单的情况下，数据流从源到目标，这意味着，来自源属性的值设置目标属性的值。 但是，在某些情况下，可以或者数据流从目标到源，或在两个方向。 为避免混淆，请记住，目标始终是即使它是提供数据，而非接收数据在其设置数据绑定的对象。

## <a name="bindings-with-a-binding-context"></a>与绑定上下文的绑定

尽管完全在 XAML 中通常指定数据绑定，很有意义，若要查看代码中的数据绑定。 **基本代码绑定**页包含的 XAML 文件`Label`和一个`Slider`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicCodeBindingPage"
             Title="Basic Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="48"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Slider`设置 0 到 360 之间的范围。 此计划的目的是旋转`Label`通过操作`Slider`。

无数据绑定，则会将设置`ValueChanged`的事件`Slider`事件处理程序访问`Value`的属性`Slider`并将该值设置为`Rotation`属性`Label`。 数据绑定自动执行该作业;事件处理程序和在其中的代码不再是必需的。

可以在任何派生的类的实例上设置绑定[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)，其中包括`Element`， `VisualElement`， `View`，和`View`派生类。  始终在目标对象上设置绑定。 绑定引用的源对象。 若要设置数据绑定，请使用以下两个成员的目标类：

- [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)属性指定的源对象。
- [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))方法指定的目标属性和源属性。

在此示例中，`Label`是绑定目标和`Slider`是绑定源。 中的更改`Slider`源影响的旋转`Label`目标。 数据源中的流复制到目标。

`SetBinding`方法定义的`BindableObject`具有一个类型的参数[ `BindingBase` ](xref:Xamarin.Forms.BindingBase)从中[ `Binding` ](xref:Xamarin.Forms.Binding)类派生而来，但有其他`SetBinding`方法定义由[ `BindableObjectExtensions` ](xref:Xamarin.Forms.BindableObjectExtensions)类。 中的代码隐藏文件**基本代码绑定**示例使用更简单[ `SetBinding` ](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*)从此类的扩展方法。

```csharp
public partial class BasicCodeBindingPage : ContentPage
{
    public BasicCodeBindingPage()
    {
        InitializeComponent();

        label.BindingContext = slider;
        label.SetBinding(Label.RotationProperty, "Value");
    }
}
```

`Label`对象是绑定目标，因此这是在设置此属性，在调用该方法的对象。 `BindingContext`属性指示绑定源，即`Slider`。

`SetBinding`方法对绑定目标调用，但指定的目标属性和源属性。 目标属性被指定为`BindableProperty`对象： `Label.RotationProperty`。 源属性指定为字符串，并指示`Value`属性的`Slider`。

`SetBinding`方法揭示了其中一个数据绑定的最重要规则：

*目标属性必须受可绑定的属性。*

此规则表示目标对象必须是派生的类的实例`BindableObject`。 请参阅[**可绑定属性**](~/xamarin-forms/xaml/bindable-properties.md)一文，了解概述的可绑定对象并可绑定属性。

没有此类规则的源属性，指定为字符串。 在内部，使用反射来访问实际的属性。 在此特定情况，但是，`Value`属性还受可绑定的属性。

代码可以是某种程度上简化：`RotationProperty`通过定义可绑定属性`VisualElement`，并由继承`Label`和`ContentPage`，因此不需要的类名中`SetBinding`调用：

```csharp
label.SetBinding(RotationProperty, "Value");
```

但是，包括类名称是很好的目标对象的提醒。

在操作`Slider`，则`Label`相应地旋转：

[![基本代码绑定](basic-bindings-images/basiccodebinding-small.png "基本代码绑定")](basic-bindings-images/basiccodebinding-large.png#lightbox "基本代码绑定")

**基本 Xaml 绑定**页等同于**基本代码绑定**不同之处在于它在 XAML 中定义的整个数据绑定：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicXamlBindingPage"
             Title="Basic XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

只需如下所示的代码中，是目标对象上设置数据绑定`Label`。 涉及两个 XAML 标记扩展。 这些是立即认出由大括号分隔符：

- `x:Reference`需要标记扩展引用的源对象，即`Slider`名为`slider`。
- `Binding`标记扩展链接`Rotation`的属性`Label`到`Value`属性`Slider`。

请参阅文章[XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)XAML 标记扩展有关的详细信息。 `x:Reference`标记扩展受[ `ReferenceExtension` ](xref:Xamarin.Forms.Xaml.ReferenceExtension)类;`Binding`受[ `BindingExtension` ](xref:Xamarin.Forms.Xaml.BindingExtension)类。 因为 XML 命名空间前缀指示，`x:Reference`是 XAML 2009 规范的一部分而`Binding`是 Xamarin.Forms 的一部分。 请注意，在大括号内显示没有引号。

它很容易忘记`x:Reference`标记扩展设置时`BindingContext`。 常见错误地将属性设置为此类的绑定源的名称直接的是：

```xaml
BindingContext="slider"
```

但这不正确。 该标记将设置`BindingContext`属性设置为`string`其字符拼写"slider"的对象 ！

请注意，使用指定的源属性[ `Path` ](xref:Xamarin.Forms.Xaml.BindingExtension.Path)的属性`BindingExtension`，这对应于[ `Path` ](xref:Xamarin.Forms.Binding.Path)属性[ `Binding`](xref:Xamarin.Forms.Binding)类。

上显示的标记**基本 XAML 绑定**页可以简化： XAML 标记扩展，例如`x:Reference`并`Binding`可以*内容属性*属性定义，这对于 XAML标记扩展表示的属性名称不需要出现。 `Name`属性是内容的属性`x:Reference`，并`Path`属性是 content 属性`Binding`，这意味着它们可以消除从表达式：

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>无需绑定上下文的绑定

`BindingContext`属性是数据绑定的一个重要组成部分，但它并不总是需要。 可以改为按指定的源对象`SetBinding`调用或`Binding`标记扩展。

了这一点**替代方法代码绑定**示例。 XAML 文件是类似于**基本代码绑定**示例不同之处在于`Slider`为控件定义`Scale`属性`Label`。 因此，`Slider`为一系列设置&ndash;2 到 2:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeCodeBindingPage"
             Title="Alternative Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件设置的绑定[ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))方法定义的`BindableObject`。 参数是[构造函数](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object))有关[ `Binding` ](xref:Xamarin.Forms.Binding)类：

```csharp
public partial class AlternativeCodeBindingPage : ContentPage
{
    public AlternativeCodeBindingPage()
    {
        InitializeComponent();

        label.SetBinding(Label.ScaleProperty, new Binding("Value", source: slider));
    }
}
```

`Binding`构造函数具有 6 参数，因此`source`参数指定了命名参数。 参数是`slider`对象。

运行此程序可能会感到有点惊讶：

[![替代代码绑定](basic-bindings-images/alternativecodebinding-small.png "替代代码绑定")](basic-bindings-images/alternativecodebinding-large.png#lightbox "替代代码绑定")

在左侧的 iOS 屏幕显示屏幕页首次出现时的外观。 其中是`Label`？

问题在于`Slider`初始值为 0。 这将导致`Scale`属性的`Label`也设置为 0，重写其默认值为 1。 这会导致`Label`正在最初不可见。 如 Android 和通用 Windows 平台 (UWP) 的屏幕截图所示，你能够`Slider`使`Label`再次出现，但其初始的亮点是令其不安。

您会发现在[下一篇文章](binding-mode.md)如何避免此问题通过初始化`Slider`默认值为`Scale`属性。

> [!NOTE]
> [ `VisualElement` ](xref:Xamarin.Forms.VisualElement)类还定义[ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX)并[ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY)属性，可以缩放`VisualElement`中按不同方式水平和垂直方向。

**替代 XAML 绑定**页完全在 XAML 中显示等效的绑定：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeXamlBindingPage"
             Title="Alternative XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="{Binding Source={x:Reference slider},
                               Path=Value}" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

现在`Binding`标记扩展有两个属性设置，请`Source`和`Path`，由逗号分隔。 这些可以如果您愿意，在同一行上显示：

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

`Source`属性设置为嵌入`x:Reference`否则具有相同的语法与设置的标记扩展`BindingContext`。 请注意，大括号出现没有引号，必须用逗号分隔的两个属性。

内容属性的`Binding`标记扩展`Path`，但`Path=`可以仅取消标记扩展的一部分，如果它是在表达式中的第一个属性。 若要消除`Path=`过程中，您需要来交换两个属性：

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

尽管 XAML 标记扩展通常由大括号分隔，但它们还可以表示为对象元素：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Source="{x:Reference slider}"
                 Path="Value" />
    </Label.Scale>
</Label>
```

现在`Source`和`Path`属性是常规 XAML 属性： 显示在引号内的值以及属性不由逗号分隔。 `x:Reference`标记扩展可以成为对象元素：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Path="Value">
            <Binding.Source>
                <x:Reference Name="slider" />
            </Binding.Source>
        </Binding>
    </Label.Scale>
</Label>
```

此语法并不常见，但有时有必要时涉及到复杂的对象。

到目前为止所示的示例设置`BindingContext`属性和`Source`的属性`Binding`到`x:Reference`标记扩展引用的页上的另一个视图。 这两个属性属于类型`Object`，并且它们可以设置为包括适用于绑定源的属性的任何对象。

在继续操作文章中，您会发现，可以设置`BindingContext`或`Source`属性设置为`x:Static`标记扩展引用的静态属性或字段中，值或`StaticResource`标记扩展引用中存储的对象资源字典中，或直接到对象，这是通常 （但并非总是如此） 的 ViewModel 实例。

`BindingContext`属性也设置为`Binding`对象，以便`Source`并`Path`的属性`Binding`定义的绑定上下文。

## <a name="binding-context-inheritance"></a>绑定上下文继承

在本文中，你已了解，你可以指定源对象使用`BindingContext`属性或`Source`属性的`Binding`对象。 如果两者都设置`Source`的属性`Binding`优先于`BindingContext`。

`BindingContext`属性具有非常重要的特征：

*设置`BindingContext`通过可视树继承属性。*

正如您将看到，这会非常方便，用于简化绑定表达式，并在某些情况下&mdash;尤其是在模型-视图-视图模型 (MVVM) 方案&mdash;很重要。

**绑定上下文继承**示例是简单的绑定上下文的继承演示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BindingContextInheritancePage"
             Title="BindingContext Inheritance">
    <StackLayout Padding="10">

        <StackLayout VerticalOptions="FillAndExpand"
                     BindingContext="{x:Reference slider}">

            <Label Text="TEXT"
                   FontSize="80"
                   HorizontalOptions="Center"
                   VerticalOptions="EndAndExpand"
                   Rotation="{Binding Value}" />

            <BoxView Color="#800000FF"
                     WidthRequest="180"
                     HeightRequest="40"
                     HorizontalOptions="Center"
                     VerticalOptions="StartAndExpand"
                     Rotation="{Binding Value}" />
        </StackLayout>

        <Slider x:Name="slider"
                Maximum="360" />

    </StackLayout>
</ContentPage>
```

`BindingContext`的属性`StackLayout`设置为`slider`对象。 由已继承此绑定上下文`Label`并`BoxView`，这两个都没有其`Rotation`属性设置为`Value`属性`Slider`:

[![绑定上下文继承](basic-bindings-images/bindingcontextinheritance-small.png "绑定上下文继承")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "绑定上下文继承")

在中[下一篇文章](binding-mode.md)，你将看到如何*绑定模式*可以更改目标和源对象之间的数据流。


## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [数据绑定 Xamarin.Forms 书籍章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
