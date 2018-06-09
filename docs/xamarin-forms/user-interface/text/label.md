---
title: Xamarin.Forms 标签
description: 此文章介绍了如何使用 Xamarin.Forms 标签类在应用程序中显示单个和多行文本。
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: e9f99ace64e013dfa681e497b9d33376d79555ed
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245493"
---
# <a name="xamarinforms-label"></a>Xamarin.Forms 标签

_Xamarin.Forms 中的显示文本_

`Label`视图用于显示文本，单个和多行。 自定义字体 （系列、 大小和选项） 和彩色的文本，可以有标签。 本文介绍了以下主题：

- **[截断和包装](#Truncation_and_Wrapping)** &ndash;截断和包装用于处理一行文本无法容纳其中的情况下的选项。
- **[字体](#Font)** &ndash;字体选项。
- **[颜色](#Color)** &ndash;标签文本颜色选项。
- **[格式化文本](#Formatted_Text)** &ndash;用于显示文本与多个格式/样式内联选项。

## <a name="styling-label"></a>样式标签

以下部分介绍的设置属性`Label`手动基于每个实例。 请注意，设置的属性可以分组为一个一致地应用到一个或多个视图的样式。 这可以提高代码的可读性，并可以进行设计更改更加轻松地实现。 请参阅[样式](~/xamarin-forms/user-interface/text/styles.md)有关详细信息。

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>截断和换行

可以设置标签来处理无法容纳在以下几种方式，通过公开的一行文本`LineBreakMode`属性。 [`LineBreakMode`](https://developer.xamarin.com/api/type/Xamarin.Forms.LineBreakMode/) 是一个枚举的以下选项：

- **HeadTruncation** &ndash;截断的文本，显示最终的开头。
- **CharacterWrap** &ndash;使到新的行的文本字符边界处换行。
- **MiddleTruncation** &ndash;显示的开头和末尾的文本，由省略号中间的替换。
- **NoWrap** &ndash;不会包装文本，仅显示任意数量的文本作为可调整到一行。
- **TailTruncation** &ndash;显示截断末尾的文本的开头。
- **自动换行**&ndash;使文本在单词边界处换行。

## <a name="font"></a>字体

请参阅[使用字体](~/xamarin-forms/user-interface/text/fonts.md)有关详细信息。

## <a name="color"></a>颜色

`Label`s 可以设置要使用通过可绑定的自定义的文本颜色`TextColor`属性。

特别注意，有必要确保颜色将每个平台上可用。 因为每个平台都有不同的文本和背景颜色的默认值，你将需要小心地将其选取适用于每个的默认值。

下面的代码用于设置标签的文本颜色：

在代码中：

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var label = new Label { Text="This is a label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
    }
}
```

在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.LabelPage"
Title="Label Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a label." />
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

![](label-images/textcolor.png "标签 TextColor 示例")

<a name="Formatted_Text" />

## <a name="formatted-text"></a>格式化的文本

标签公开`FormattedText`这允许你以使用多个字体呈现文本和颜色在同一视图的属性。

`FormattedText`属性属于类型[ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/)。 格式化的字符串组成一个或多个`Span`s，每个具有以下属性：

- **BackgroundColor** &ndash;可以用来设置背景色，例如，可获得突出显示效果。
- **FontAttributes** &ndash;可以是设置为粗体、 斜体，还是两者皆否。
- **FontFamily** &ndash;设置要使用的字体。
- **FontSize** &ndash;设置的文本大小。
- **ForegroundColor** &ndash;设置的文本颜色。
- **文本**&ndash;要显示的文本。

下面的 C# 代码演示的第一个单词以粗体显示，最后一个单词为红色的标签：

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
    var label = new Label { FontSize = 20 };
    var s = new FormattedString ();
    s.Spans.Add (new Span{ Text = "Red Bold", FontAttributes = FontAttributes.Bold });
    s.Spans.Add (new Span{ Text = "Default" });
    s.Spans.Add (new Span{ Text = "italic small", FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)), FontAttributes = FontAttributes.Italic});
    label.FormattedText = s;
        layout.Children.Add(label);
    }
}
```

在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.LabelPage"
Title="Label Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
      <Label FontSize=20>
        <Label.FormattedText>
          <FormattedString>
            <Span Text="Red Bold" ForegroundColor="Red" FontAttributes="Bold" />
            <Span Text="Default" />
            <Span Text="italic small" FontAttributes="Italic" FontSize="Small" />
          </FormattedString>
        </Label.FormattedText>
      </Label>
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

![](label-images/formattedtext.png "标签 FormattedText 示例")


## <a name="related-links"></a>相关链接

- [借助 Xamarin.Forms，第 3 章创建移动应用](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [文本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [标签 API](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)
