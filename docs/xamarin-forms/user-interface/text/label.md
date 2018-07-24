---
title: Xamarin.Forms 标签
description: 本文介绍如何使用 Xamarin.Forms 标签类在应用程序中显示单个和多行文本。
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/16/2018
ms.openlocfilehash: b5069381126db0859508480df5596ed5ec85686f
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39203031"
---
# <a name="xamarinforms-label"></a>Xamarin.Forms 标签

_在 Xamarin.Forms 中显示文本_

[ `Label` ](xref:Xamarin.Forms.Label)视图用于显示文本、 单个和多行。 自定义字体 （系列、 大小和选项） 和彩色的文本，可以有标签。

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>截断和换行

可以设置标签来处理不适合在以下几种方式，通过公开的一行文本`LineBreakMode`属性。 [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) 是一个枚举，使用以下值：

- **HeadTruncation** &ndash;将截断的文本，其中显示最终的开头。
- **CharacterWrap** &ndash;到新行上的文本进行换行字符边界处。
- **MiddleTruncation** &ndash;显示开头和末尾的文本，与通过省略号中间的替换。
- **NoWrap** &ndash;不换行文本，仅显示可以为任意数量的文本适合某个行。
- **TailTruncation** &ndash;显示文本，截断结束的开头。
- **换行**&ndash;使文本在单词边界处换行。

## <a name="fonts"></a>字体

有关详细信息，有关上指定字体`Label`，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="colors"></a>颜色

`Label`s 可以设置为使用通过可绑定的自定义文本颜色[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)属性。

特别注意有必要确保将每个平台上可用的颜色。 因为每个平台都有不同的文本和背景颜色的默认值，将需要谨慎地选择适用于每个默认值。

使用以下 XAML 设置标签的文本颜色：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a label." />
    </StackLayout>
</ContentPage>
```

等效的 C# 代码是：

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();

        var layout = new StackLayout { Padding = new Thickness(5,10) };
        var label = new Label { Text="This is a label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

以下屏幕截图显示的设置结果`TextColor`属性：

![](label-images/textcolor.png "标签 TextColor 示例")

有关颜色的详细信息，请参阅[颜色](~/xamarin-forms/user-interface/colors.md)。

<a name="Formatted_Text" />

## <a name="formatted-text"></a>带格式的文本

标签公开[ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText)这允许文本呈现使用多种字体和颜色在同一视图中的属性。

`FormattedText`属性属于类型[ `FormattedString` ](xref:Xamarin.Forms.FormattedString)，其中包含一个或多个[ `Span` ](xref:Xamarin.Forms.Span)情况下，通过设置[ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans)属性. 每个`Span`拥有以下属性：

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) -s p a n 背景的颜色。
- [`Font`](xref:Xamarin.Forms.Span.Font) – 在范围中的文本的字体。
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) – 在范围中的文本的字体属性。
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) – 属于该范围中的文本的字体的字体系列。
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) – 范围中文本的字体的大小。
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) – 范围中文本的颜色。 此属性已过时，已由`TextColor`属性。
- [`Style`](xref:Xamarin.Forms.Span.Style) – 要将应用于跨度的样式。
- [`Text`](xref:Xamarin.Forms.Span.Text) – 跨度的文本。
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) – 范围中文本的颜色。

下面的 XAML 示例演示`FormattedText`由三个属性`Span`实例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}" />
                    <Span Text="italic small." FontAttributes="Italic" FontSize="Small" />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

等效的 C# 代码是：

```csharp
public class LabelPageCode : ContentPage
{
    public LabelPageCode ()
    {
        var layout = new StackLayout{ Padding = new Thickness (5, 10) };
        ...
        var formattedString = new FormattedString ();
        formattedString.Spans.Add (new Span{ Text = "Red bold, ", ForegroundColor = Color.Red, FontAttributes = FontAttributes.Bold });
        formattedString.Spans.Add (new Span { Text = "default, ", Style = Device.Styles.BodyStyle });
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!NOTE]
> [ `Text` ](xref:Xamarin.Forms.Span.Text)属性的`Span`可以通过数据绑定设置。 有关详细信息，请参阅[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

以下屏幕截图显示设置的结果`FormattedString`属性设置为三个`Span`实例：

![](label-images/formattedtext.png "标签 FormattedText 示例")

## <a name="styling-a-label"></a>设置标签的样式

前面几节介绍设置[ `Label` ](xref:Xamarin.Forms.Label)根据每个实例的属性。 但是，属性集可以分组为一致地应用于一个或多个视图的一种样式。 这可以提高代码的可读性，并轻松地实现设计更改。 有关详细信息，请参阅[样式](~/xamarin-forms/user-interface/text/styles.md)。

## <a name="related-links"></a>相关链接

- [借助 Xamarin.Forms，第 3 章创建移动应用](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [文本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [标签 API](xref:Xamarin.Forms.Label)
