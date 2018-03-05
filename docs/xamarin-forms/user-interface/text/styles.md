---
title: "样式"
description: "Xamarin.Forms 中的样式文本"
ms.topic: article
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 764fb77bedf9e00427348e95b4ecf029ae94741f
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="styles"></a>样式

_Xamarin.Forms 中的样式文本_


样式可以用于调整标签、 条目和编辑器的外观。 可以定义一次样式，并将其使用的许多视图，但样式只能具有一种类型的视图。
可以提供样式`Key`有选择地使用特定的控件和应用`Style`属性。

本文介绍了以下主题：

- **[内置样式](#Built-In_Styles)** &ndash;使用内置到在你的应用程序整个样式基于文本的视图的样式。
- **[自定义样式](#Custom_Styles)** &ndash;不够用的内置选项时定义自定义样式。
- **[应用样式](#Applying_Styles)** &ndash;将自定义和内置样式应用于您的视图。
- **[可访问性](#Accessibility)** &ndash;确保文本遵循辅助功能设置。

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>内置样式

Xamarin.Forms 包括几个[内置](http://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)样式常见方案：

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

若要将其中一个内置样式应用，使用`DynamicResource`标记扩展以指定的样式：

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

在 C# 中，内置样式中处于选中状态`Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

![](styles-images/builtinstyles.png "设备样式示例")

<a name="Custom_Styles" />

## <a name="custom-styles"></a>自定义样式

样式包含 setter 和 setter 包含属性和属性的值将设置为。

在 C# 中，将按以下方式定义具有大小 30 的红色文本的标签的自定义样式：

```csharp
var LabelStyle = new Style (typeof(Label)) {
    Setters = {
        new Setter {Property = Label.TextColorProperty, Value = Color.Red},
        new Setter {Property = Label.FontSizeProperty, Value = 30}
    }
};

var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

在 XAML 中：

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style x:Key="LabelStyle" TargetType="Label">
            <Setter Property="TextColor" Value="Red"/>
            <Setter Property="FontSize" Value="30"/>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>

<ContentPage.Content>
    <StackLayout>
        <Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
    </StackLayout>
</ContentPage.Content>
```

请注意，在中定义资源 （包括所有样式） `ContentPage.Resources`，这是同级越熟悉`ContentPage.Content`元素。

![](styles-images/customstyle.png "自定义样式示例")

<a name="Applying_Styles" />

## <a name="applying-styles"></a>应用样式

一旦创建一种样式后，它可以应用于任何视图匹配其`TargetType`。

在 XAML 中，自定义样式应用到视图通过提供其`Style`具有属性`StaticResource`引用所需的样式的标记扩展：

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

在 C# 中，样式可以是直接应用到一个视图或添加到和从页中检索`ResourceDictionary`。 若要直接添加：

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

若要添加和检索从页面的`ResourceDictionary`:

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

因为它们需要以响应的辅助功能设置，将以不同方式，应用内置样式。 若要将在 XAML 中，内置样式应用`DynamicResource`使用标记扩展：

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

在 C# 中，内置样式中处于选中状态`Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>可访问性

内置样式存在要更加轻松地采用可访问性首选项。 在使用任何内置样式时，如果用户将相应地设置其辅助工具首选项将自动增加字体大小。

请考虑使用辅助功能设置启用和禁用了内置样式风格的视图相同页的下面的示例：

已禁用：

![](styles-images/pre-access.png "使用辅助功能已禁用的设备样式")

已启用:

![](styles-images/post-access.png "具有可访问性启用的设备样式")

若要确保可访问性，请确保内置样式用作你的应用，在任何文本相关样式的基础，并且你正在一致地使用样式。 请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)扩展和一般情况下使用样式有关的详细信息。


## <a name="related-links"></a>相关链接

- [借助 Xamarin.Forms，第 12 章创建移动应用](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [样式](~/xamarin-forms/user-interface/styles/index.md)
- [文本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [样式](http://developer.xamarin.comhttps://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
