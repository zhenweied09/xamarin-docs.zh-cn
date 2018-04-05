---
title: 将文本和图形集成
description: 请参阅如何确定要将文本与 SkiaSharp 图形集成的呈现的文本字符串的大小
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 4ef9f1b634d2ecfa73a94bfd562a68593dfdc575
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="integrating-text-and-graphics"></a>将文本和图形集成

_请参阅如何确定要将文本与 SkiaSharp 图形集成的呈现的文本字符串的大小_

本文演示如何测量文本、 可能缩放到特定的大小，文本和与其他图形集成文本：

![](text-images/textandgraphicsexample.png "通过矩形括起来的文本")

SkiaSharp`Canvas`类也包括方法，用于绘制矩形 ([`DrawRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRect/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/)) 以及具有圆角矩形 ([`DrawRoundRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRoundRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPaint/))。 这些方法需要要定义为的矩形`SKRect`值。

**确定框架，文本**页中心上的页和环绕它带有框架的圆角矩形一对构成的短文本字符串。 [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs)类演示如何执行。

在 SkiaSharp 你使用`SKPaint`类设置文本和字体属性，但你可以还使用它来获取文本的呈现的大小。 以下的开头`PaintSurface`事件处理程序调用两个不同`MeasureText`方法。 第一个[ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/)调用端一个简单`string`自变量和返回文本的像素宽度基于当前的字体特性。 然后程序将计算新`TextSize`属性`SKPaint`对象基于该呈现的宽度，当前`TextSize`属性，并显示区域的宽度。 这为了设置`TextSize`以便要呈现在屏幕的宽度的 90%的文本字符串：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string str = "Hello SkiaSharp!";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Chocolate
    };

    // Adjust TextSize property so text is 90% of screen width
    float textWidth = textPaint.MeasureText(str);
    textPaint.TextSize = 0.9f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds;
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

第二个[ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/SkiaSharp.SKRect@/)调用具有`SKRect`自变量，因此它获取的宽度和高度的呈现的文本。 `Height`此属性`SKRect`值取决于是否存在大写字母、 上行和中的文本字符串的下行字母。 不同`Height`文本字符串"mom"、"cat"和"dog"，例如报告值。

`Left`和`Top`属性`SKRect`结构指示呈现文本的左上角的坐标，如果通过显示的文本`DrawText`调用并为 0 的 X 和 Y 位置。 例如，当 iPhone 7 模拟器上运行此程序`TextSize`分配后首次调用的计算结果的值 90.6254 `MeasureText`。 `SKRect`从第二次调用获取值`MeasureText`具有以下属性值：

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88;

请记住的 X 和 Y 坐标你传递给`DrawText`方法指定的基线在文本左侧。 `Top`值指明文本扩展该基线和 （减去从 88 68 it） 上方 68 像素基线下方的 20 像素。 `Left` 6 的值指示文本以 6 个像素中的 X 值右侧`DrawText`调用。 这使得正常的内部字符间距。 如果你想要显示的左上角紧紧插入显示文本，将传递这些假负`Left`和`Top`值的 X 和 Y 坐标的`DrawText`，在此示例中， &ndash;6 和 68。

`SKRect`结构定义多个方便的属性和方法，其中一些的其余部分中使用`PaintSurface`处理程序。 `MidX`和`MidY`值指示中心的矩形的坐标。 (在 iPhone 7 示例中，这些值是 338.4107 和&ndash;24。)下面的代码使用这些值进行最简单的坐标计算中心上显示的文本：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(str, xText, yText, textPaint);
    ...
}
```

`PaintSurface`处理程序完成后将两个调用`DrawRoundRect`，这两种需要的参数`SKRect`。 这`SKRect`值是当然类似于`SKRect`从获取值`MeasureText`方法，但它不能相同。 首先，它需要稍大，以便通过边缘的文本，不绘制圆角的矩形和其次，它需要在空间中移动以便`Left`和`Top`值对应于矩形将是左上角定位。 这两个作业可以实现`Offset`和`Inflate`由定义方法`SKRect`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Create a new SKRect object for the frame around the text
    SKRect frameRect = textBounds;
    frameRect.Offset(xText, yText);
    frameRect.Inflate(10, 10);

    // Create an SKPaint object to display the frame
    SKPaint framePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5,
        Color = SKColors.Blue
    };

    // Draw one frame
    canvas.DrawRoundRect(frameRect, 20, 20, framePaint);

    // Inflate the frameRect and draw another
    frameRect.Inflate(10, 10);
    framePaint.Color = SKColors.DarkBlue;
    canvas.DrawRoundRect(frameRect, 30, 30, framePaint);
}
```

接下来，该方法的其余部分很简单。 它将创建另一个`SKPaint`对象的边框和调用`DrawRoundRect`两次。 第二个调用使用放大另一个 10 个像素的矩形。 第一次调用指定一个角半径为 20 个像素;第二个具有圆角半径 30 个像素，因此它们看起来要并行：

 [![](text-images/framedtext-small.png "确定框架，文本页面的三个屏幕截图")](text-images/framedtext-large.png#lightbox "确定框架，文本页面的三个屏幕截图")

你可以启用你的手机或侧向模拟器，若要查看的文本和大小的增加的帧。

如果你只需以中心在屏幕上的一些文本，你可以不它大约通过设置测量文本`TextAlign`属性`SKPaint`到`SKTextAlign.Center`。 在指定的 X 坐标`DrawText`方法然后指示放置文本的水平居中的位置。 如果传递到屏幕的中点`DrawText`方法，将水平居中对齐文本和*几乎*垂直居中因为基线将垂直居中。

文本本身得多都可以被视为一个图形选项。 一种简单的选择是显示的文本字符而不是普通实心的显示大纲：

[![](text-images/outlinedtext-small.png "仅使用三重概述文本页的屏幕截图")](text-images/outlinedtext-large.png#lightbox "三倍的所述的文本页的屏幕截图")

只需通过更改正常完成此操作`Style`属性`SKPaint`对象从其默认设置`SKPaintStyle.Fill`到`SKPaintStyle.Stroke`以及通过指定描边宽度。 `PaintSurface`处理程序**概述文本**页显示它的工作原理：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "OUTLINE";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 1,
        FakeBoldText = true,
        Color = SKColors.Blue
    };

    // Adjust TextSize property so text is 95% of screen width
    float textWidth = textPaint.MeasureText(text);
    textPaint.TextSize = 0.95f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds;
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

 在过去的几个文章中，你必须了解如何使用 SkiaSharp 来绘制文本，现在圆圈，省略号和圆角的矩形。 下一步是[SkiaSharp 行和路径](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)在其中你将学习如何绘制图形路径中的连接的直线。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
