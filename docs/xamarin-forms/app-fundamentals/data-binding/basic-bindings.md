---
title: "基本绑定"
description: "数据绑定目标、 源和绑定上下文"
ms.topic: article
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: b82c0471985306962133c3bf7b084b49d5588bb6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="basic-bindings"></a>基本绑定

Xamarin.Forms 数据绑定链接的成对的两个对象，至少一个通常是用户界面对象之间的属性。 这两个对象被称为*目标*和*源*:

- *目标*位于对象 （和属性） 设置数据绑定。
- *源*是引用的数据绑定的对象 （和属性）。

这一区别有时可能有点令人困惑： 在最简单的情况下，数据流动从源到目标，这意味着，从源属性的值设置目标属性的值。 但是，在某些情况下，可以或者数据流从目标到源，或在两个方向。 为了避免混淆，请记住，目标始终是即使它是提供数据，而非接收数据在其设置数据绑定的对象。

## <a name="bindings-with-a-binding-context"></a>与绑定上下文的绑定

尽管通常完全在 XAML 中指定数据绑定，但是很有益若要查看代码中的数据绑定。 **基本代码绑定**页包含一个具有的 XAML 文件`Label`和`Slider`:

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

`Slider`设置为 0 到 360 范围。 此程序的目的是旋转`Label`通过操作`Slider`。

不带数据绑定，你将设置`ValueChanged`事件`Slider`访问的事件处理程序`Value`属性`Slider`并将该值设置为`Rotation`属性`Label`。 数据绑定自动执行该作业;事件处理程序和其中的代码不再需要。 

你可以在任何派生自的类的实例上设置绑定[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)，其中包括`Element`， `VisualElement`， `View`，和`View`衍生产品。  始终对目标对象设置绑定。 绑定引用的源对象。 若要设置数据绑定，使用目标类的以下两个成员：

- [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)属性指定的源对象。
- [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)方法指定的目标属性和源属性。

在此示例中，`Label`绑定目标和`Slider`绑定源。 中的更改`Slider`源影响的旋转`Label`目标。 数据流动从源到目标。

`SetBinding`方法由定义`BindableObject`具有类型自变量[ `BindingBase` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingBase/)从中[ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/)类派生，但有其他`SetBinding`方法通过定义[ `BindableObjectExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObjectExtensions/)类。 中的代码隐藏文件**基本代码绑定**示例使用更简单[ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObjectExtensions.SetBinding/p/Xamarin.Forms.BindableObject/Xamarin.Forms.BindableProperty/System.String/)自此类的扩展方法。

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

`Label`对象是绑定目标，因此这是上设置此属性和对其调用该方法的对象。 `BindingContext`属性指示绑定源，这是`Slider`。

`SetBinding`方法调用在绑定目标系统上，但指定的目标属性和源属性。 目标属性指定为`BindableProperty`对象： `Label.RotationProperty`。 源属性指定为一个字符串，并指示`Value`属性`Slider`。 

`SetBinding`方法显示数据绑定的最重要规则之一：

*必须由可绑定的属性支持的目标属性。*

此规则意味着目标对象必须是派生自的类实例`BindableObject`。 请参阅[**可绑定属性**](~/xamarin-forms/xaml/bindable-properties.md)的概述以及可绑定的对象可绑定属性的文章。

不存在此类规则的源属性，以字符串形式指定。 在内部，使用反射来访问实际的属性。 在此特定情况，但是，`Value`由可绑定的属性还支持属性。

该代码可以分别某种程度上简化：`RotationProperty`可绑定的属性定义由`VisualElement`，并由继承`Label`和`ContentPage`，因此不需要此类名称中`SetBinding`调用：

```csharp
label.SetBinding(RotationProperty, "Value");
```

但是，包括类名称是很好的目标对象提醒。

在操作`Slider`、`Label`相应地旋转：

[![绑定的 Basice 代码](basic-bindings-images/basiccodebinding-small.png "基本代码绑定")](basic-bindings-images/basiccodebinding-large.png#lightbox "基本代码绑定")

**基本 Xaml 绑定**页等同于**基本代码绑定**之处在于它在 XAML 中定义的整个数据绑定：

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

对目标对象，这是一样的代码中，设置数据绑定`Label`。 涉及两个 XAML 标记扩展。 这些是立即可以识别的大括号分隔符：

- `x:Reference`引用源对象，它是所需的标记扩展`Slider`名为`slider`。
- `Binding`标记扩展链接`Rotation`属性`Label`到`Value`属性`Slider`。 

请参阅文章[XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)XAML 标记扩展有关的详细信息。 `x:Reference`标记扩展支持的[ `ReferenceExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/)类;`Binding`受[ `BindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/)类。 以 XML 命名空间前缀指示，`x:Reference`是 XAML 2009 规范的一部分时`Binding`属于 Xamarin.Forms。 请注意，没有引号出现在大括号内。

很容易忘记`x:Reference`标记扩展设置时`BindingContext`。 这是常见错误地将属性设置直接为如下的绑定源的名称：

```xaml
BindingContext="slider"
```

但不正确。 该标记将设置`BindingContext`属性`string`其字符拼写"slider"的对象 ！

请注意，使用指定的源属性[ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Path/)属性`BindingExtension`，与对应[ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/)属性[ `Binding`](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/)类。

上显示的标记**基本 XAML 绑定**可以简化页： XAML 标记扩展，例如`x:Reference`和`Binding`可以*内容属性*属性定义，对于 XAML标记扩展表示的属性名称不需要出现。 `Name`属性是内容的属性`x:Reference`，和`Path`属性是内容的属性`Binding`，这意味着它们可以消除从表达式：

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>没有绑定上下文的绑定

`BindingContext`属性是数据绑定的一个重要组成部分，但它不是始终必要。 而是可以中指定的源对象`SetBinding`调用或`Binding`标记扩展。

此进行了演示**替代代码绑定**示例。 XAML 文件是类似于**基本代码绑定**示例只不过`Slider`为控件定义`Scale`属性`Label`。 为此，`Slider`为一系列设置&ndash;2 到 2:

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

代码隐藏文件设置的绑定[ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)方法由定义`BindableObject`。 此自变量是[构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Binding.Binding/p/System.String/Xamarin.Forms.BindingMode/Xamarin.Forms.IValueConverter/System.Object/System.String/System.Object/)为[ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/)类：

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

`Binding`构造函数具有 6 参数，因此`source`命名自变量与指定参数。 此自变量是`slider`对象。 

运行此程序可能有点令人惊讶：

[![替代代码绑定](basic-bindings-images/alternativecodebinding-small.png "替代代码绑定")](basic-bindings-images/alternativecodebinding-large.png#lightbox "替代代码绑定")

在左侧的 iOS 屏幕显示屏幕时将首先显示的页的外观。 其中是`Label`？ 

问题在于`Slider`的初始值为 0。 这将导致`Scale`属性`Label`也设置为 0，重写其默认值为 1。 这会导致`Label`正在最初不可见。 如 Android 和通用 Windows 平台 (UWP) 的屏幕截图所示，你可以操控`Slider`以便`Label`再次出现，但其初始消失带来不便。

您将在中发现[下一篇文章](binding-mode.md)如何通过初始化避免此问题`Slider`的默认值从`Scale`属性。

**的替代项 XAML 绑定**页完全在 XAML 中显示等效的绑定：

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

现在`Binding`标记扩展有两个属性设置，`Source`和`Path`、 用逗号分隔。 它们可以依据你喜好而定，在同一行上显示：

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

`Source`属性设置为一个嵌入`x:Reference`标记扩展，否则，具有与设置相同的语法`BindingContext`。 请注意没有引号出现在大括号中，并且必须用逗号进行分隔两个属性。

内容属性`Binding`标记扩展的`Path`，但`Path=`可以仅消除标记扩展的一部分，如果它是在表达式中的第一个属性。 若要消除`Path=`一部分，你需要交换两个属性：

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

尽管 XAML 标记扩展通常由大括号分隔，但它们还可以表示作为对象元素：

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

现在`Source`和`Path`属性是 XAML 的常规属性： 显示在引号内的值以及属性没有用逗号分隔。 `x:Reference`标记扩展也可能会变为对象元素：

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

此语法不常见，但有时很必要时涉及复杂的对象。

到目前为止所示的示例设置`BindingContext`属性和`Source`属性`Binding`到`x:Reference`标记扩展，以引用页面上的另一个视图。 这两个属性属于类型`Object`，并且它们可以设置为任何包含适合于绑定源的属性的对象。 

在继续操作的文章中，将发现你可以设置`BindingContext`或`Source`属性`x:Static`要引用的静态属性或字段的值的标记扩展或`StaticResource`标记扩展，以引用存储中的对象资源字典中，或直接为一个对象，这是通常 （但不是总是） 视图模型的实例。 

`BindingContext`属性还可以设置为`Binding`对象以便`Source`和`Path`属性`Binding`定义的绑定上下文。

## <a name="binding-context-inheritance"></a>绑定上下文继承

在本文中，你已了解，你可以指定源对象使用`BindingContext`属性或`Source`属性`Binding`对象。 如果两者都设置`Source`属性`Binding`优先于`BindingContext`。

`BindingContext`属性具有的极重要特征：

*设置`BindingContext`属性继承在可视化树。*

随着你将看到的这可能非常方便，用于简化绑定表达式，并在某些情况下&mdash;尤其是在模型-视图-视图模型 (MVVM) 方案&mdash;很重要。

**绑定上下文继承**示例是简单演示的绑定上下文的继承：

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

`BindingContext`属性`StackLayout`设置为`slider`对象。 此绑定上下文继承两个`Label`和`BoxView`，这两个都没有其`Rotation`属性设置为`Value`属性`Slider`: 

[![绑定上下文继承](basic-bindings-images/bindingcontextinheritance-small.png "绑定上下文继承")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "绑定上下文继承")

在[下一篇文章](binding-mode.md)，你将看到如何*绑定模式*可以更改目标和源对象之间的数据流。


## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [从 Xamarin.Forms 簿的数据绑定章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
