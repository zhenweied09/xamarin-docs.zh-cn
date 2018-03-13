---
title: "第 3 章的摘要。 更深入地成文本"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 7dbcc093bc467e633f9333bb129adc25372832f3
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>第 3 章的摘要。 更深入地成文本

此章节介绍[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)中更深入，包括颜色、 字体和格式设置的视图。

## <a name="wrapping-paragraphs"></a>包装段落

当[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)属性`Label`包含长文本`Label`自动将其包装到多个行所示[ **Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles)示例。 你可以嵌入 Unicode 编码，例如 \u2014 em dash 或 C# 字符，如 '\r' 到一个新行。

当[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)和[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性`Label`设置为`LayoutOptions.Fill`，则的总体大小`Label`受到的空间，其容器使可。 `Label`被认为是*约束*。 大小`Label`是其容器的大小。

当`HorizontalOptions`和`VerticalOptions`属性设置为值以外`LayoutOptions.Fill`，大小为`Label`受呈现的文本，直到达到其容器使可用于的大小所需的空间`Label`。 `Label`被认为是*不受约束*，并确定其自身的大小。

(注意： 条款*约束*和*不受约束*可能不直观的因为不受约束的视图是通常小于约束的视图。 此外，这些条款不使用一致地早期书籍的章节中。）

如视图`Label`可以限制于一个维度和另一部分中不受约束。 A`Label`将只能在多个行上环绕文本，如果它水平约束。

如果`Label`是约束，它可能占用大得多的空间比所需的文本。 可以在总体区域中放置文本`Label`。 设置[ `HorizontalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.HorizontalTextAlignment/)属性的成员[ `TextAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/)枚举 ([`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Start/)， [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/)，或[ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/)) 来控制段落的所有行的对齐方式。 默认值是`Start`和左对齐文本。

设置[ `VerticalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/)属性的成员`TextAlignment`枚举放置在顶部、 中心，还是底部占用的区域文本`Label`。

设置[ `LineBreakMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.LineBreakMode/)属性的成员[ `LineBreakMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LineBreakMode/)枚举 ([`WordWrap`](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.WordWrap/)， [ `CharacterWrap` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.CharacterWrap/)， [ `NoWrap` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.NoWrap/)， [ `HeadTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.HeadTruncation/)， [ `MiddleTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.MiddleTruncation/)，或[ `TailTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.TailTruncation/)) 到控制如何多个段落中断中的行，或将被截断。

## <a name="text-and-background-colors"></a>文本和背景色

设置[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/)和[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)属性`Label`到[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)值，以便控制的文本和背景的颜色。

`BackgroundColor`适用于占用的整个区域的背景`Label`。 具体取决于`HorizontalOptions`和`VerticalOptions`属性、 大小可能远远大于需显示文本的区域。 你可以使用颜色进行实验，以使用的各种值`HorizontalOptions`， `VerticalOptions`， `HorizontalExeAlignment`，和`VerticalTextAlignment`以查看它们如何影响的大小和位置`Label`，和的大小和内的文本的位置`Label`。

## <a name="the-color-structure"></a>颜色结构

[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)结构可供您指定的颜色，作为红-绿、 蓝 (RGB) 值或色调饱和度亮度 (HSL) 值，或包含颜色名称。 Alpha 通道也是可用于指示透明度的。

使用`Color`构造函数来指定：

- [灰色阴影](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/)
- [RGB 值](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/)
- [的透明度的 RGB 值](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/System.Double/)

参数是`double`范围从 0 到 1 的值。

此外可以使用几个静态方法以创建`Color`值：

- [`Color.FromRgb`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Double/System.Double/System.Double/) 有关`double`RGB 值从 0 到 1
- [`Color.FromRgb`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Int32/System.Int32/System.Int32/) 为从 0 到 255 之间的整数 RGB 值
- [`Color.FromRgba`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Double/System.Double/System.Double/System.Double/) 有关`double`的透明度的 RGB 值
- [`Color.FromRgba`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Int32/System.Int32/System.Int32/System.Int32/) 对于整数 RGB 值的透明度
- [`Color.FromHsla`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/) 有关`double`HSL 值的透明度
- [`Color.FromUint`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromUint/p/System.UInt32/) 有关`uint`值计算方式为 (B + 256 * (G + 256 * (R + 256 * A)))
- [`Color.FromHex`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHex/p/System.String/) 有关`string`的十六进制数字构成，采用格式"#AARRGGBB"或"#RRGGBB"或"#ARGB"或"#RGB"，其中每个字母对应于十六进制数字的 alpha、 红色、 绿色和蓝色通道。 此方法是主要用于 XAML 颜色转换中所述[第 7 章，与代码的 XAML](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md)。

一旦创建，`Color`值不可变。 可以从下列属性中获得的颜色的特征：

- [`R`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.R/)
- [`G`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.G/)
- [`B`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.B/)
- [`A`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.A/)
- [`Hue`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Hue/)
- [`Saturation`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Saturation/)
- [`Luminosity`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Luminosity/)

这些是所有`double`范围从 0 到 1 的值。

`Color` 此外定义 240 公共静态只读字段常用颜色的。 在编写书籍时，仅 17 常用颜色都不可用。

另一个的公共静态只读字段设置为零的所有颜色通道与定义的颜色：

- [`Color.Transparent`](https://developer.xamarin.com/api/field/Xamarin.Forms.Color.Transparent/)

多个实例方法允许修改现有的颜色若要创建新的颜色：

- [`AddLuminosity`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.AddLuminosity/p/System.Double/)
- [`MultiplyAlpha`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.MultiplyAlpha/p/System.Double/)
- [`WithHue`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithHue/p/System.Double/)
- [`WithLuminosity`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithLuminosity/p/System.Double/)
- [`WithSaturation`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithSaturation/p/System.Double/)

最后，两个静态只读属性可以定义特殊的颜色值：

- [`Color.Default`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/)所有通道设置为 （&) #x 2013年; 1
- [`Color.Accent`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Accent/)

`Color.Default` 用于强制实施该平台的配色方案，并且因此在不同的平台上的不同上下文中具有不同的含义。 默认情况下平台配色方案是：

- iOS： 浅色背景上的深文本
- Android： 浅深色背景 （在工作簿） 上的文本或深文本浅色背景上的 (通过在 AppCompat 材料设计**master**的示例代码存储库分支)
- 浅色背景上的 UWP： 深文本
- 深色背景上的 Windows 8.1： 浅文本
- 深色背景上的 Windows Phone 8.1： 浅文本

`Color.Accent`值特定于平台的 （和有时用户可选择） 的颜色的颜色深色或浅色背景上可见的结果。

## <a name="changing-the-application-color-scheme"></a>更改应用程序的配色方案

各种平台都具有默认配色方案，如上面的列表中所示。

如果目标 Android，就可以通过指定浅色主题在 Android.Manifest.xml 文件中，或通过切换到深上浅方案[添加 AppCompat 和材料设计](~/xamarin-forms/platform/android/appcompat.md)。

对于 Windows 平台中，颜色主题通常选择用户，但你可以添加`RequestedTheme`属性设置为`Light`或`Dark`平台的 App.xaml 文件中。 默认情况下，对 UWP 项目中的 App.xaml 文件包含`RequestedTheme`属性设置为`Light`。

## <a name="font-sizes-and-attributes"></a>字体大小和属性

设置[ `FontFamily` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontFamily/)属性`Label`为字符串，如"Times Roman"以选择字体系列。 但是，你需要指定在特定平台上，支持的字体系列和平台是在这方面不一致。

设置[ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontSize/)属性`Label`到`double`用于指定的字体的大致高度。 请参阅[第 5 章处理大小](chapter05.md)，有关智能地选择字体大小的详细信息。

或者，你可以获取多个预设的平台相关字体大小之一。 静态[ `Device.GetNamedSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/System.Type/)方法和[重载](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/Xamarin.Forms.Element/)两者都返回`double`适合于平台的字体大小值基于成员的[ `NamedSize` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NamedSize/)枚举 ([`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Default/)， [ `Micro` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Micro/)， [ `Small` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Small/)， [ `Medium` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Medium/)， 和[ `Large` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Large/))。 从返回的值`Medium`成员不一定相同`Default`。 [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)示例使用这些名为大小显示文本。

设置[ `FontAttributes` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontAttributes/)属性`Label`给这些成员[ `FontAttributes` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FontAttributes/)枚举， [ `Bold` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.Bold/)， [ `Italic`](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.Italic/)，或[ `None` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.None/)。 你可以组合`Bold`和`Italic`使用 C# 按位 OR 运算符的成员。

## <a name="formatted-text"></a>带格式文本

在所有到目前为止的示例中，通过将显示整个文本`Label`统一尚未格式化。 建立变化的文本字符串内格式，未设置`Text`属性`Label`。 与此相反，设置[ `FormattedText` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FormattedText/)类型的对象属性[ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/)。

`FormattedString` 具有[ `Spans` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FormattedString.Spans/)是一个集合的属性[ `Span` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Span/)对象。 每个`Span`对象都具有其自己[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.Text/)， [ `FontFamily` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontFamily/)， [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontSize/)， [ `FontAttributes` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontAttributes/)， [ `ForegroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.ForegroundColor/)，和[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.BackgroundColor/)属性。

[ **VariableFormattedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText)示例演示如何使用`FormattedText`单行文本，属性和[ **VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara)演示整个段落的技术，如下所示：

[![变量的三个屏幕截图格式化段落](images/ch03fg06-small.png "变量格式的标签文本")](images/ch03fg06-large.png#lightbox "变量格式的标签文本")

[ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)程序使用单个`Label`和`FormattedString`要显示每个平台的已命名的字体大小的所有对象。



## <a name="related-links"></a>相关链接

- [第 3 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [第 3 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [第 3 章 F # 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [标签](~/xamarin-forms/user-interface/text/label.md)
- [处理颜色](~/xamarin-forms/user-interface/colors.md)
