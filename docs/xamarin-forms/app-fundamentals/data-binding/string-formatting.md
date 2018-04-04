---
title: 字符串格式设置
description: 使用数据绑定进行格式化和显示为字符串的对象
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 4e143f650c3cde7577def1a95e53b207608a088a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="string-formatting"></a>字符串格式设置

有时很方便使用数据绑定来显示的字符串表示形式的对象或值。 例如，你可能想要使用`Label`要显示的当前值`Slider`。 在此数据绑定，`Slider`是源，且目标为`Text`属性`Label`。

在代码中显示字符串，该功能最强大的工具时，静态[ `String.Format` ](https://developer.xamarin.com/api/member/System.String.Format/p/System.String/System.Object/)方法。 格式设置字符串包含格式设置代码特定于各种类型的对象，并且可以包含要设置格式的值以及其他文本。 请参阅[.NET 中的格式设置类型](/dotnet/standard/base-types/formatting-types/)字符串格式设置的详细信息的文章。

## <a name="the-stringformat-property"></a>StringFormat 属性

此工具被传送到数据绑定： 你设置[ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.StringFormat/)属性`Binding`(或[ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.StringFormat/)属性`Binding`标记扩展) 到使用一个占位符的字符串设置格式的标准.NET:

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

请注意，由单引号 （撇号） 字符，以帮助避免将视为另一个 XAML 标记扩展的大括号的 XAML 分析器分隔的格式设置字符串。 否则，该字符串没有单引号字符是相同的字符串将用于对的调用中显示的浮点值`String.Format`。 格式规范`F2`会导致值会显示两位小数。

`StringFormat`属性只在类型的目标属性时有意义`string`，绑定模式且`OneWay`或`TwoWay`。 对于双向绑定，`StringFormat`才适用于将其从源传递到目标的值。

如你将看到在下一篇文章中有关[绑定路径](binding-path.md)，数据绑定可能会变得非常复杂且复杂。 在调试时这些数据绑定，可以添加`Label`到 XAML 文件中，与`StringFormat`以显示某些中间结果。 即使你可以使用它只是为了显示对象的类型，这可能非常有用。

**字符串格式设置**页演示的几种用法`StringFormat`属性：

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

上的绑定`Slider`和`TimePicker`显示的格式规范使用特定于`double`和`TimeSpan`数据类型。 `StringFormat`显示中的文本`Entry`视图演示如何使用的格式设置字符串中指定两个双引号`&quot;`HTML 实体。

XAML 文件中的下一部分是`StackLayout`与`BindingContext`设置为`x:Static`引用静态的标记扩展`DateTime.Now`属性。 第一个绑定具有任何属性：

```xaml
<Label Text="{Binding}" />
```

这只是显示`DateTime`值`BindingContext`与默认格式设置。 第二个绑定显示`Ticks`属性`DateTime`，而在其他两个绑定显示`DateTime`自行向特定的格式。 请注意这`StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

如果你需要在格式设置字符串中显示向左或右大括号，只需使用一对。

最后一个部分集`BindingContext`为的值`Math.PI`并将其显示使用默认格式设置和两个不同类型的数字的格式化。

下面是在所有三个平台上运行的程序：

[![字符串格式设置](string-formatting-images/stringformatting-small.png "字符串格式设置")](string-formatting-images/stringformatting-large.png#lightbox "字符串格式设置")

## <a name="viewmodels-and-string-formatting"></a>Viewmodel 和字符串格式设置

当你使用`Label`和`StringFormat`若要显示的视图，也是 ViewModel 的目标值，可以定义从视图到绑定`Label`或从到 ViewModel `Label`。 通常情况下，第二种方法是最佳的因为它验证的视图和视图模型之间的绑定都正常工作。

此方法所示**更好的颜色选择器**示例，后者使用作为相同 ViewModel**简单颜色选择器**中显示程序[**绑定模式**](binding-mode.md)文章：

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

现在有三对`Slider`和`Label`绑定到相同的元素的源中的属性`HslColorViewModel`对象。 唯一的区别是`Label`具有`StringFormat`属性，以显示每个`Slider`值。

[![更好地颜色选择器](string-formatting-images/bettercolorselector-small.png "更好地颜色选择器")](string-formatting-images/bettercolorselector-large.png#lightbox "更好地颜色选择器")

你可能想知道如何无法传统两位数的十六进制格式显示 RGB （红色、 绿色、 蓝色） 值。 这些整数值不是直接从可用`Color`结构。 一种解决方案是计算整数值的视图模型中的颜色组件并将它们作为属性公开。 无法然后格式化它们使用`X2`格式规范。

另一种方法是更多常规： 可以编写*绑定值转换器*更高版本的文章中所述[**绑定值转换器**](converters.md)。

下一篇文章中，但是，探讨[**绑定路径**](binding-path.md)更多详细信息，并显示了如何使用它来引用子属性和集合中的项。


## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [从 Xamarin.Forms 簿的数据绑定章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
