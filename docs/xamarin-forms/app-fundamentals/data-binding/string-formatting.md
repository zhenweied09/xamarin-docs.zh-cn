---
title: Xamarin.Forms 字符串格式设置
description: 本文介绍如何使用 Xamarin.FOrms 数据绑定进行格式化和显示为字符串的对象。 这被通过将绑定的 StringFormat 设置为与占位符的标准.NET 格式设置字符串。
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: a876e81c67b6ec61a2cb29143cb001a7d6160032
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998142"
---
# <a name="xamarinforms-string-formatting"></a>Xamarin.Forms 字符串格式设置

有时很方便地使用数据绑定来显示对象或值的字符串表示形式。 例如，你可能想要使用`Label`若要显示的当前值`Slider`。 在此数据绑定中，`Slider`是源和目标数据库中均`Text`属性的`Label`。

在代码中显示字符串，该功能最强大的工具时，静态[ `String.Format` ](xref:System.String.Format(System.String,System.Object))方法。 格式设置字符串包含格式设置代码特定于不同类型的对象，并可包含要设置格式的值以及其他文本。 请参阅[.NET 中的格式设置类型](/dotnet/standard/base-types/formatting-types/)一文，了解字符串格式设置的详细信息。

## <a name="the-stringformat-property"></a>StringFormat 属性

此功能被传送到数据绑定： 您设置[ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat)的属性`Binding`(或[ `StringFormat` ](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat)属性`Binding`标记扩展) 到标准.NET 格式设置字符串包含一个占位符：

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

请注意，由以帮助避免将大括号视为另一个 XAML 标记扩展的 XAML 分析器的单引号 （撇号） 字符分隔格式设置字符串。 否则，此字符串没有单引号字符是相同的字符串将显示一个浮点值对的调用中`String.Format`。 格式规范`F2`导致带两位小数显示的值。

`StringFormat`属性仅在如果目标属性的类型有意义`string`，并且绑定模式是`OneWay`或`TwoWay`。 对于双向绑定，`StringFormat`是仅适用于从源向目标传递的值。

正如您将在下一篇文章中看到上[绑定路径](binding-path.md)，数据绑定可能会变得非常复杂而又费解。 在调试时这些数据绑定，可以添加`Label`到 XAML 文件与`StringFormat`显示某些中间结果。 即使使用仅用于显示对象的类型，可帮助你。

**字符串格式设置**页说明了几种用法的`StringFormat`属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="DataBindingDemos.StringFormattingPage"
             Title="String Formatting">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Blue" />
                <Setter Property="HeightRequest" Value="2" />
                <Setter Property="Margin" Value="0, 5" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <Slider x:Name="slider" />
        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The slider value is {0:F2}'}" />

        <BoxView />

        <TimePicker x:Name="timePicker" />
        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time,
                              StringFormat='The TimeSpan is {0:c}'}" />

        <BoxView />

        <Entry x:Name="entry" />
        <Label Text="{Binding Source={x:Reference entry},
                              Path=Text,
                              StringFormat='The Entry text is &quot;{0}&quot;'}" />

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:DateTime.Now}">
            <Label Text="{Binding}" />
            <Label Text="{Binding Path=Ticks,
                                  StringFormat='{0:N0} ticks since 1/1/1'}" />
            <Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
            <Label Text="{Binding StringFormat='The long date is {0:D}'}" />
        </StackLayout>

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:Math.PI}">
            <Label Text="{Binding}" />
            <Label Text="{Binding StringFormat='PI to 4 decimal points = {0:F4}'}" />
            <Label Text="{Binding StringFormat='PI in scientific notation = {0:E7}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

上的绑定`Slider`并`TimePicker`显示了如何使用格式规范的特定于`double`和`TimeSpan`数据类型。 `StringFormat`显示从文本`Entry`视图演示如何通过使用在格式设置字符串中指定两个双引号`&quot;`HTML 实体。

XAML 文件中的下一个部分是`StackLayout`与`BindingContext`设置为`x:Static`标记扩展引用静态`DateTime.Now`属性。 第一个绑定具有任何属性：

```xaml
<Label Text="{Binding}" />
```

这只是显示`DateTime`的值`BindingContext`使用默认格式设置。 第二个绑定显示`Ticks`的属性`DateTime`，而其他两个绑定显示`DateTime`本身具有特定格式设置。 请注意，这`StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

如果需要显示在格式设置字符串中的向左或右大括号，只需使用其中一对。

最后一个部分集`BindingContext`的值`Math.PI`，并使用默认格式设置和两个不同类型的数字格式显示。

下面是在所有三个平台上运行的程序：

[![字符串格式设置](string-formatting-images/stringformatting-small.png "字符串格式设置")](string-formatting-images/stringformatting-large.png#lightbox "字符串格式设置")

## <a name="viewmodels-and-string-formatting"></a>Viewmodel 和字符串格式设置

当使用`Label`并`StringFormat`若要显示的一个视图，它也是 ViewModel 的目标值，可以定义从视图到绑定`Label`或从 ViewModel 到达`Label`。 一般情况下，第二种方法是最佳的因为它将验证的视图和 ViewModel 之间的绑定都正常工作。

这种方法所示**更好的颜色选择器**示例中，使用同一个 ViewModel 作为**简单颜色选择器**中所示的程序[**绑定模式**](binding-mode.md)文章：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.BetterColorSelectorPage"
             Title="Better Color Selector">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

现在有三对`Slider`并`Label`元素绑定到相同的源中的属性`HslColorViewModel`对象。 唯一的区别在于`Label`已`StringFormat`属性来显示每个`Slider`值。

[![更好地颜色选择器](string-formatting-images/bettercolorselector-small.png "更好地颜色选择器")](string-formatting-images/bettercolorselector-large.png#lightbox "更好地颜色选择器")

您可能想知道如何可以在传统两位数字的十六进制格式中显示 RGB （红色、 绿色、 蓝色） 值。 这些整数值不从直接可用`Color`结构。 一种解决方案是计算颜色组件是在 ViewModel 中的整数值并将其作为属性公开。 你可以然后格式化它们使用`X2`格式规范。

另一种方法是更多常规： 你可以编写*绑定值转换器*更高版本的文章中所述[**绑定值转换器**](converters.md)。

下一篇文章中，但是，探讨[**绑定路径**](binding-path.md)更详细介绍，并且显示了如何使用它来引用子属性和集合中的项。


## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [数据绑定 Xamarin.Forms 书籍章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
