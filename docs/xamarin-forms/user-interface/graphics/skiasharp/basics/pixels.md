---
title: "像素和设备无关的单位"
description: "浏览 SkiaSharp 坐标和 Xamarin.Forms 坐标之间的差异"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: 5e07377584996694aa8597af79317957c51050ec
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="pixels-and-device-independent-units"></a>像素和设备无关的单位

_浏览 SkiaSharp 坐标和 Xamarin.Forms 坐标之间的差异_

本文探讨 SkiaSharp 和 Xamarin.Forms 中使用的坐标系统中的差异。 你可以获取两个坐标系统之间进行转换和还绘制填充特定区域的图形的信息：

![](pixels-images/screenfillexample.png "填充屏幕一个椭圆")

如果你已编程 Xamarin.Forms 中一段时间，你可能必须获得感觉 Xamarin.Forms 坐标和大小。 在两个先前的文章中绘制圆形看起来可能有点小给您。

这些圆圈*是*Xamarin.Forms 大小很小。 默认情况下，SkiaSharp 绘制以像素为单位时 Xamarin.Forms 基坐标和大小均由基础平台的独立于设备的单元上。(在找不到 Xamarin.Forms 坐标系统的详细信息[第 5 章。处理大小](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md)书籍的*具有 Xamarin.Forms 创建移动应用*。)

中的页[ **SkewSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)程序标题为**面大小**使用 SkiaSharp 文本输出以显示三个不同的源的显示界面的大小：

- 正常 Xamarin.Forms [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/)和[ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/)属性`SKCanvasView`对象。
- [ `CanvasSize` ](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKCanvasView.CanvasSize/)属性`SKCanvasView`对象。
- [ `Size` ](https://developer.xamarin.com/api/property/SkiaSharp.SKImageInfo.Size/)属性`SKImageInfo`值，该值是与一致`Width`和`Height`在两个前面的页中使用的属性。

[ `SurfaceSizePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs)类演示如何显示这些值。 构造函数将保存`SKCanvasView`作为字段，以便它可以在中访问的对象`PaintSurface`事件处理程序：

```csharp
SKCanvasView canvasView;

public SurfaceSizePage()
{
    Title = "Surface Size";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvas` 包括六个不同`DrawText`方法，但此[ `DrawText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawText/p/System.String/System.Single/System.Single/SkiaSharp.SKPaint/)最简单方法是：

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

你指定的文本字符串的 X 和 Y 坐标位置的文本是若要开始，和`SKPaint`对象。 X 坐标指定文本的左侧放置的位置，但当心： 的 Y 坐标指定的位置*基线*的文本。 如果您曾经编写了手动在加下划线纸上，在基线将是哪些字符 sit，及其下方的下行字母 （例如上字母 g、 p、 q 和 y） 时向下查看的行。

`SKPaint`对象允许你指定的文本、 字体系列，以及文本大小的颜色。 默认情况下， [ `TextSize` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.TextSize/)属性具有其值为 12，这会导致很小手机等的高分辨率设备上的文本。 中包含的最简单的应用程序，你还需要的大小要显示的文本的一些信息。 `SKPaint`类定义[ `FontMetrics` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.FontMetrics/)属性并在几个[ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/)方法，但小于特别需要[ `FontSpacing` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.FontSpacing/)属性为间距的文本的连续行提供一个建议的值。

以下`PaintSurface`处理程序创建`SKPaint`对象`TextSize`的 40 个像素，这是所需的下行字母底部到顶部的上行字母文本垂直高度。 `FontSpacing`值，`SKPaint`对象返回大于有点所，大约 47 像素。

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 40
    };

    float fontSpacing = paint.FontSpacing;
    float x = 20;               // left margin
    float y = fontSpacing;      // first baseline
    float indent = 100;

    canvas.DrawText("SKCanvasView Height and Width:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(String.Format("{0:F2} x {1:F2}",
                                  canvasView.Width, canvasView.Height),
                    x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKCanvasView CanvasSize:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(canvasView.CanvasSize.ToString(), x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKImageInfo Size:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(info.Size.ToString(), x + indent, y, paint);
}
```

该方法的开头使用 20 （对于很少左侧边距） X 坐标和 Y 坐标的文本的第一行`fontSpacing`，即略高于必要的操作以显示图面顶部显示完整的第一个文本行的高度。 每次调用后`DrawText`，按一个或两个增量的增加的 Y 坐标`fontSpacing`。

下面是在所有三个平台上运行的程序：

[![](pixels-images/surfacesize-small.png "三重的面大小页面的屏幕截图")](pixels-images/surfacesize-large.png#lightbox "面大小页面的三个屏幕截图")

如你所见，`CanvasSize`属性`SKCanvasView`和`Size`属性`SKImageInfo`值是在报告的像素大小中保持一致。 `Height`和`Width`属性`SKCanvasView`是 Xamarin.Forms 属性，并报告定义平台的独立于设备的单元中的视图的大小。

在左侧的 iOS 7 模拟器具有 2 个像素，每个独立于设备的单位、 中心中 Android Nexus 5 包含 3 个像素，每个单位，并且 Nokia Lumia 925 右侧 2.25 像素，每个单位。 为什么简单 circle 显示有关在 iPhone 上的相同大小的更早版本如下所示和 Windows phone，但会在 Android 手机上小一些。

如果你希望完全在独立于设备的单元工作，你可以做到这一点设置`IgnorePixelScaling`属性`SKCanvasView`到`true`。 但是，您可能不喜欢结果。 SkiaSharp 呈现在较小的设备图面，与设备无关的单位中的视图的大小相等的像素大小上的图形。 （例如，SkiaSharp 应该使用 360 x 512 像素显示图的面上 Nexus 5。）它然后可内扩展，该映像的大小，从而导致明显的位图 jaggies。

若要维护相同的图像分辨率，更好的解决方案是编写您自己两个坐标系统之间进行转换的简单函数。

除了`DrawCircle`方法，`SKCanvas`还定义了两个`DrawOval`绘制椭圆的方法。 由两个 radii 而不是单个 radius 定义椭圆。 这些被称为*主要 radius*和*次要 radius*。 `DrawOval`方法绘制一个具有两个半径与 X 和 Y 轴平行。 可以与转换或使用的图形路径 （在后面说明），超过该限制，但[这`DrawOval`方法](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawOval/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/)名称的两个 radii 参数`rx`和`ry`以指示它们是并行到X 和 Y 轴：

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

是否可以绘制填充显示图面的椭圆？ **椭圆填充**页演示如何。 `PaintSurface`中的事件处理程序[ **EllipseFillPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs)类减去从半描边宽度`xRadius`和`yRadius`以适应整个椭圆的值并将其大纲显示面中：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float strokeWidth = 50;
    float xRadius = (info.Width - strokeWidth) / 2;
    float yRadius = (info.Height - strokeWidth) / 2;

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = strokeWidth
    };
    canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
}
```

此处三个平台上运行它：

[![](pixels-images/ellipsefill-small.png "三重的面大小页面的屏幕截图")](pixels-images/ellipsefill-large.png#lightbox "面大小页面的三个屏幕截图")

[其他`DrawOval`方法](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawOval/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/)具有[ `SGRect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRect/)自变量，这是根据其左上角和右下角的 X 和 Y 坐标定义一个矩形。 Oval 填充表明它可能是可以使用在该矩形**椭圆填充**页如下：

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

但是，程序将截断四个边的椭圆的轮廓的所有的边缘。 你需要调整所有`SKRect`构造函数自变量基于`strokeWidth`以便右正常工作：

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
