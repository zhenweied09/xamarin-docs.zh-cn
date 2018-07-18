---
title: Xamarin.Forms 标签
description: 本文介绍如何使用 Xamarin.Forms 标签类在应用程序中显示单个和多行文本。
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: ce602a84ea1024dc22298a3ec1567a9a34ad4a82
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995961"
---
# <a name="xamarinforms-label"></a>Xamarin.Forms 标签

_在 Xamarin.Forms 中显示文本_

`Label`视图用于显示文本、 单个和多行。 自定义字体 （系列、 大小和选项） 和彩色的文本，可以有标签。 本文介绍了以下主题：

- **[截断和换行](#Truncation_and_Wrapping)** &ndash;截断和处理文本不能位于同一行的情况下的换行选项。
- **[字体](#Font)** &ndash;的字体选项。
- **[颜色](#Color)** &ndash;标签文本颜色选项。
- **[格式化文本](#Formatted_Text)** &ndash;用于显示具有多个格式/样式内联的文本的选项。

## <a name="styling-label"></a>样式标签

以下部分介绍了设置属性的`Label`手动在每个实例基础上。 请注意，属性集可以分组为一致地应用于一个或多个视图的一种样式。 这可以提高代码的可读性，并轻松地实现设计更改。 请参阅[样式](~/xamarin-forms/user-interface/text/styles.md)有关详细信息。

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>截断和换行

可以设置标签来处理不适合在以下几种方式，通过公开的一行文本`LineBreakMode`属性。 [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) 是一个枚举，下列选项：

- **HeadTruncation** &ndash;将截断的文本，其中显示最终的开头。
- **CharacterWrap** &ndash;到新行上的文本进行换行字符边界处。
- **MiddleTruncation** &ndash;显示开头和末尾的文本，与通过省略号中间的替换。
- **NoWrap** &ndash;不换行文本，仅显示可以为任意数量的文本适合某个行。
- **TailTruncation** &ndash;显示文本，截断结束的开头。
- **换行**&ndash;使文本在单词边界处换行。

## <a name="font"></a>字体

请参阅[使用字体](~/xamarin-forms/user-interface/text/fonts.md)有关详细信息。

## <a name="color"></a>颜色

`Label`s 可以设置为使用通过可绑定的自定义文本颜色`TextColor`属性。

特别注意有必要确保将每个平台上可用的颜色。 因为每个平台都有不同的文本和背景颜色的默认值，将需要谨慎地选择适用于每个默认值。

使用以下代码设置标签的文本颜色：

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

在 XAML:

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

## <a name="formatted-text"></a>带格式的文本

标签公开`FormattedText`这允许你以使用多个字体呈现文本和颜色在同一视图中的属性。

`FormattedText`属性属于类型[ `FormattedString` ](xref:Xamarin.Forms.FormattedString)。 格式的字符串组成的一个或多个`Span`s，每个具有以下属性：

- **BackgroundColor** &ndash;可以用于设置背景色，例如若要实现荧光笔的效果。
- **FontAttributes** &ndash;可以是设置为粗体、 斜体，还是两者皆否。
- **FontFamily** &ndash;设置要使用的字体。
- **FontSize** &ndash;设置文本的大小。
- **ForegroundColor** &ndash;设置文本的颜色。
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

在 XAML:

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
- [标签 API](xref:Xamarin.Forms.Label)
