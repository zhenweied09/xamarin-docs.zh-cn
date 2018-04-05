---
title: 路径基础知识
description: 浏览组合连接的直线和曲线的 SkiaSharp SKPath 对象
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: b2881148631435c9082b42cad0e784100b010b46
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="path-basics"></a>路径基础知识

_浏览组合连接的直线和曲线的 SkiaSharp SKPath 对象_

图形路径的最重要之一是功能的能够定义当应连接多个行和当他们不应该连接。 如这些两个三角形顶端所示的差异可能是非常可见：

![](paths-images/connectedlinesexample.png "显示连接和断开连接的行之间的区别的两个三角形")

图形路径包装[ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/)对象。 路径是一个或多个集合*轮廓*。 每个分布是一套*连接*直线和曲线。 轮廓未连接到相互但它们以可视方式可能重叠。 有时单一 contour 可以自身重叠。

通过以下方法的调用通常开始轮廓`SKPath`:

- `MoveTo` 若要开始新的轮廓

该方法的自变量是一个单一的点，您可以表示为`SKPoint`值或作为单独的 X 和 Y 坐标。 `MoveTo`调用建立轮廓和一台初始的点开头*当前点*。 你可以调用以下方法以继续使用行或从当前的点到点方法，然后将成为新的当前点中指定的曲线具有轮廓：

- `LineTo` 向路径添加一条直线
- `ArcTo` 若要添加一段弧线，它是上一个圆或椭圆的圆的周长线
- `CubicTo` 若要添加的三次方贝塞尔样条
- `QuadTo` 若要添加的二次贝塞尔样条
- `ConicTo` 若要添加的合理二次贝塞尔样条，这可以准确地呈现圆锥部分 （省略号、 parabolas 和 hyperbolas）

这些五个方法均包含描述行或曲线所需的所有信息。 上述每种五个方法结合使用与当前点建立它前面的方法调用。 例如，`LineTo`方法将添加到具有轮廓的直线基于当前的点，因此的参数`LineTo`是单点。

`SKPath`类还定义了具有相同的名称与这些六个方法的方法`R`开头：

- `RMoveTo`
- `RLineTo`
- `RArcTo`
- `RCubicTo`
- `RQuadTo`
- `RConicTo`

`R`代表*相对*。 它们具有与不带相应的方法相同的语法`R`但相对于当前点。 这些是路径的便于绘制类似的部件中多次调用的方法。

通过再次调用结束的轮廓`MoveTo`或`RMoveTo`，，从此处开始，新的轮廓线或调用`Close`，用于关闭轮廓。 `Close`方法自动追加一条直线从当前点具有轮廓的第一个点并将路径标记为已关闭，这意味着它将呈现而无需任何笔划端头。

打开和关闭轮廓之间的区别所示**两个三角形轮廓**页上，使用`SKPath`对象具有两个轮廓呈现两个三角形。 第一个轮廓线是打开和关闭第二个。 下面是[ `TwoTriangleContours` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/TwoTriangleContoursPage.cs)类：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create the path
    SKPath path = new SKPath();

    // Define the first contour
    path.MoveTo(0.5f * info.Width, 0.1f * info.Height);
    path.LineTo(0.2f * info.Width, 0.4f * info.Height);
    path.LineTo(0.8f * info.Width, 0.4f * info.Height);
    path.LineTo(0.5f * info.Width, 0.1f * info.Height);

    // Define the second contour
    path.MoveTo(0.5f * info.Width, 0.6f * info.Height);
    path.LineTo(0.2f * info.Width, 0.9f * info.Height);
    path.LineTo(0.8f * info.Width, 0.9f * info.Height);
    path.Close();

    // Create two SKPaint objects
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Magenta,
        StrokeWidth = 50
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    // Fill and stroke the path
    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

第一个轮廓包含调用[ `MoveTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.MoveTo/p/System.Single/System.Single/)使用 X 和 Y 坐标而非`SKPoint`值后, 跟三个调用[ `LineTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.LineTo/p/System.Single/System.Single/)要绘制的三个四条边三角形。 第二个轮廓具有只有两个调用`LineTo`但完成通过调用具有轮廓[ `Close` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Close()/)，用于关闭轮廓。 差异非常重要：

[![](paths-images/twotrianglecontours-small.png "三重的两个三角形轮廓页面屏幕截图")](paths-images/twotrianglecontours-large.png#lightbox "三倍的两个三角形轮廓页面屏幕截图")

如你所见，第一个轮廓显然是一系列的三个连接的直线，但最终不会将连接的开头。 在顶部重叠的两行。 第二个轮廓线将显然关闭，并完成了一个更少`LineTo`调用，因为`Close`方法会自动添加的最后一行以关闭具有轮廓。

`SKCanvas` 只定义一个[ `DrawPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawPath/p/SkiaSharp.SKPath/SkiaSharp.SKPaint/)方法，该方法在此演示中调用两次以填充并路径进行描边。 所有轮廓将都填充，甚至包括那些未关闭。 为了填充闭合的路径，一条直线假设在开始和轮廓的终结点之间存在。 如果删除最后一个`LineTo`从第一个轮廓线或删除`Close`从第二个轮廓线，每个分布的调用将具有只有两个边但就像它是一个三角形填充。

`SKPath` 定义了许多其他方法和属性。 以下方法将整个分布类型添加到路径，这可能会关闭或未关闭根据的方法：

- `AddRect`
- [`AddRoundedRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddRoundedRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddCircle`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddCircle/p/System.Single/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddOval`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddOval/p/SkiaSharp.SKRect/SkiaSharp.SKPathDirection/)
- [`AddArc`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddArc/p/SkiaSharp.SKRect/System.Single/System.Single/) 若要添加一条曲线的椭圆的圆的周长上
- `AddPath` 若要将另一路径添加到当前路径
- [`AddPathReverse`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddPathReverse/p/SkiaSharp.SKPath/) 若要按相反的顺序添加另一个路径

请记住，`SKPath`对象定义仅几何图形&mdash;一系列点和连接。 仅当`SKPath`与结合`SKPaint`对象是呈现特定颜色、 描边宽度等使用的路径。 此外，请记住，`SKPaint`对象传递给`DrawPath`方法定义的整条路径的特征。 如果你想要绘制内容需要多个颜色，必须为每种颜色来使用一个单独的路径。

就像由描边端点定义的开始和结束的行的外观，通过定义两行之间的连接的外观*描边联接*。 通过设置指定此[ `StrokeJoin` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeJoin/)属性`SKPaint`指向成员的[ `SKStrokeJoin` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeJoin/)枚举：

- [`Miter`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Miter/) 尖角联接
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Round/) 用于圆角联接
- [`Bevel`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Bevel/) 已被截关闭联接

**描边联接**页面的显示了这三个描边，则类似的代码的联接**笔划端头**页。 这是`PaintSurface`中的事件处理程序[ `StrokeJoinsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeJoinsPage.cs)类：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Right
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width - 100;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = 2 * textPaint.FontSpacing;
    string[] strStrokeJoins = { "Miter", "Round", "Bevel" };

    foreach (string strStrokeJoin in strStrokeJoins)
    {
        // Display text
        canvas.DrawText(strStrokeJoin, xText, y, textPaint);

        // Get stroke-join value
        SKStrokeJoin strokeJoin;
        Enum.TryParse(strStrokeJoin, out strokeJoin);

        // Create path
        SKPath path = new SKPath();
        path.MoveTo(xLine1, y - 80);
        path.LineTo(xLine1, y + 80);
        path.LineTo(xLine2, y + 80);

        // Display thick line
        thickLinePaint.StrokeJoin = strokeJoin;
        canvas.DrawPath(path, thickLinePaint);

        // Display thin line
        canvas.DrawPath(path, thinLinePaint);
        y += 3 * textPaint.FontSpacing;
    }
}
```

此处是三个平台上运行的程序：

[![](paths-images/strokejoins-small.png "三重的描边联接页的屏幕截图")](paths-images/strokejoins-large.png#lightbox "三重的描边联接页的屏幕截图")

斜接联接包含，其中行连接是尖角。 当在小角度加入两行时，该斜接联接可以变得相当长。 若要防止过长斜接联接，长度斜接联接的受限制的值[ `StrokeMiter` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeMiter/)属性`SKPaint`。 超出了该长度的斜接联接是已被截掉成为凹凸效果联接。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
