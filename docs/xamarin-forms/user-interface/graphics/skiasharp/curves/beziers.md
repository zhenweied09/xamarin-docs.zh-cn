---
title: 三种类型的贝塞尔曲线
description: 探讨如何使用 SkiaSharp 呈现三次方、 二次、 和圆锥贝塞尔曲线
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: charlespetzold
ms.author: chape
ms.date: 05/25/2017
ms.openlocfilehash: 7b7bd83c474c7e0d32a693e06b5f12696ec5efa2
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2018
---
# <a name="three-types-of-bzier-curves"></a>三种类型的贝塞尔曲线

_探讨如何使用 SkiaSharp 呈现三次方、 二次、 和圆锥贝塞尔曲线_

圣皮埃尔贝塞尔 (1910年 – 1999)，在汽车公司 Renault，汽车正文计算机辅助设计的使用曲线的法语工程师命名贝塞尔曲线。

贝塞尔曲线已知正在适合于交互设计： 它们是很好地不当&mdash;换而言之，没有导致曲线变得无限期还是庞大的 singularities&mdash;并且它们通常是那样令人满意. 贝塞尔曲线通常定义的基于计算机的字体的字符轮廓：

![](beziers-images/beziersample.png "示例贝塞尔曲线")

上的 Wikipedia 文章[贝塞尔曲线](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)包含的一些有用信息。 术语*贝塞尔曲线*实际上指的一系列的类似的曲线。 SkiaSharp 支持三种类型的贝塞尔曲线，调用*三次方*、*二次*，和*圆锥*。 圆锥是也称为*合理 quadratic*。

## <a name="the-cubic-bzier-curve"></a>三次方贝塞尔曲线

中的三次是当贝塞尔曲线的使用者出现时，大多数开发人员认为的贝塞尔曲线的类型。

你可以添加到三次方贝塞尔曲线`SKPath`对象使用[ `CubicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CubicTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/SkiaSharp.SKPoint/)方法有三个`SKPoint`参数，或[ `CubicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CubicTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/System.Single/)带有单独重载`x`和`y`参数：

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

轮廓的当前点曲线开始。 完成三次方贝塞尔曲线由四个点定义：

- 起始点： 当前点具有轮廓，或 （0，0） 中，如果`MoveTo`尚未调用
- 第一次控制点：`point1`中`CubicTo`调用
- 第二个控制点：`point2`中`CubicTo`调用
- 终结点：`point3`中`CubicTo`调用

生成曲线起始点处开始和结束终结点处。 曲线通常不会遍历的两个控制点;改为它们的功能请求向它们曲线多 like 磁铁。

感受三次方贝塞尔曲线的最佳方法是通过试验。 这种情况下的需要**贝塞尔曲线**页上，派生自`InteractivePage`。 [ **BezierCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml)文件实例化`SKCanvasView`和`TouchEffect`。 [ **BezierCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs)隐藏代码文件创建四个`TouchPoint`其构造函数中的对象。 `PaintSurface`事件处理程序创建`SKPath`呈现基于四个的贝塞尔曲线`TouchPoint`对象，并还将从控点以点分隔的切线绘制到终结点：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with cubic Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.CubicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     touchPoints[3].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[2].Center.X,
                    touchPoints[2].Center.Y,
                    touchPoints[3].Center.X,
                    touchPoints[3].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
       touchPoint.Paint(canvas);
    }
}
```

此处所有三个平台上运行它：

[![](beziers-images/beziercurve-small.png "三重的贝塞尔曲线页面屏幕截图")](beziers-images/beziercurve-large.png#lightbox "三倍的贝塞尔曲线页面屏幕截图")

数学上，曲线是三次方多项式。 曲线相交最多三个点在一条直线。 在开始点，曲线始终是第一个控制点的切线，并在相同的方向，一条直线从一开始点。 在终结点，曲线始终是终结点的切线，并在相同的方向，一条直线从第二个管理点。

三次方贝塞尔曲线始终受一个连接的四个点的凸四边形。 这称为*凸包*。 如果控件点位于起点和终点之间的直线，然后贝塞尔曲线将呈现为一条直线。 但曲线能还与交叉本身，如第三个屏幕快照所示。

路径轮廓线可以包含多个连接三次方贝塞尔曲线，但两个三次方贝塞尔曲线之间的连接将只有以下三个点共线平滑 （即，位于一条直线）：

- 第一条曲线的第二个控制点
- 第一条曲线，这也是第二条曲线的起始点的终结点
- 第二个曲线的第一个控制点

在下一篇文章中上[ **SVG 路径数据**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)您会发现一个工具用于简化平滑连接的贝塞尔曲线的定义。

有时是有助于你了解呈现三次方贝塞尔曲线的基础参数公式。 有关*t*范围从 0 到 1，参数的等式仅，如下所示：

x(t) = (1-t) ³x₀ + 3t (1-t) ²x₁ + 3t² (1-t) x₂ + t³x₃

y(t) = (1-t) ³y₀ + 3t (1-t) ²y₁ + 3t² (1-t) y₂ + t³y₃

3 的最高指数确认这些是三次方 polynomials。 很容易地验证，当`t`等于 0，则点是 (x₀，y₀)，它是开始点，以及何时`t`等于 1，点是 (x₃，y₃)，它是终结点。 附近的起始点 (的低值`t`)，（x₁，y₁） 的第一个控制点具有强的生效，并且附近终结点 (高值 ' t ') （x₂，y₂） 的第二个控制点有重大影响。

## <a name="bzier-curve-approximation-to-circular-arcs"></a>贝塞尔曲线近似圆弧的

很方便地使用贝塞尔曲线来呈现一个圆弧。三次方贝塞尔曲线可以近似圆弧很好地最多个季度的圆，因此四个连接的贝塞尔曲线可以定义整个圆。 在发布超过 25 年前两篇文章中讨论此近似值：

> Tor Dokken、 et al"圆形曲率连续贝塞尔曲线的极佳近似值"*计算机辅助几何设计 7* (1990)，33 41。

> Michael Goldapp、"近似圆弧的立方 Polynomials"*计算机辅助几何设计 8* （1991 年） 227 238。

下图显示了四个点标记为`pto`， `pt1`， `pt2`，和`pt3`定义近似圆弧的贝塞尔曲线 （显示为红色）：

![](beziers-images/bezierarc45.png "圆弧具有贝塞尔曲线的近似")

对控件点从起点和终点的线都正切给 circle 和为贝塞尔曲线，并且它们具有的长度为*L*。上面提到的第一个文章指示贝塞尔曲线最佳近似圆弧时该长度*L*计算如下：

L = 4 × tan(α / 4) / 3

因此 L 等于 0.265 图中显示的 45 度的角度。 在代码中，此值将乘以的圆的所需半径。

**贝塞尔圆弧**页面允许您进行实验，以与定义的贝塞尔曲线来近似圆弧的角度范围最多 180 度。 [ **BezierCircularArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml)文件实例化`SKCanvasView`和`Slider`用于选择角度。 `PaintSurface`中的事件处理程序[ **BezierCircularArgPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs)代码隐藏文件使用转换来设置到画布的中心点 （0，0）。 其绘制在比较，该点上为中心的圆圈，然后计算贝塞尔曲线的两个控点：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 3;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate length of control point line
    float length = radius * 4 * (float)Math.Tan(Math.PI * angle / 180 / 4) / 3;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the end points
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point3 = new SKPoint(radius * sin, radius * cos);

    // Find the control points
    SKPoint point0Normalized = Normalize(point0);
    SKPoint point1 = point0 + new SKPoint(length * point0Normalized.Y,
                                          -length * point0Normalized.X);

    SKPoint point3Normalized = Normalize(point3);
    SKPoint point2 = point3 + new SKPoint(-length * point3Normalized.Y,
                                          length * point3Normalized.X);

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);
    canvas.DrawCircle(point3.X, point3.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point3.X, point3.Y, point2.X, point2.Y, dottedStroke);

    // Draw the Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.CubicTo(point1, point2, point3);
        canvas.DrawPath(path, redStroke);
    }
}

// Vector methods
SKPoint Normalize(SKPoint v)
{
    float magnitude = Magnitude(v);
    return new SKPoint(v.X / magnitude, v.Y / magnitude);
}

float Magnitude(SKPoint v)
{
    return (float)Math.Sqrt(v.X * v.X + v.Y * v.Y);
}

```

起点和终点 (`point0`和`point3`) 基于圆的正常参数公式计算。 因为圆的中心位于 （0，0），这些点可以也被视为径向向量从圆的中心到圆的周长。 控点位于彼此相切圆的因此它们直角到这些径向向量的行上。 向量的右角是只需具有交换的 X 和 Y 坐标和其中一个进行负的原始向量。

此处是在使用三个不同的角度的三个平台上运行的程序：

[![](beziers-images/beziercirculararc-small.png "三重的贝塞尔圆弧页面屏幕截图")](beziers-images/beziercirculararc-large.png#lightbox "贝塞尔圆弧页面的三个屏幕截图")

在第三个屏幕截图中，仔细查看，你将看到贝塞尔曲线值得注意的是偏离一个半圆，当角度为 180 度，但是 iOS 屏幕显示它看起来以适应季度圆圈正常时角度为 90 度。

计算两个控制点的坐标时相当简单的季度圆圈将面向如下：

![](beziers-images/bezierarc90.png "每个季度圆圈具有贝塞尔曲线的近似")

如果的圆的半径为 100，则*L*是 55，还是要记住的轻松号。

**相乘圆**页进行动画处理之间圆圈，圆圈和正方形图。 四个坐标此数组定义中的第一列中所示的贝塞尔曲线的近似圆[ `SquaringTheCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs)类：

```csharp
public class SquaringTheCirclePage : ContentPage
{
    SKPoint[,] points =
    {
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() },
        { new SKPoint(  55,  100), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,   55), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,    0), new SKPoint(   125,      0), new SKPoint() },
        { new SKPoint( 100,  -55), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(  55, -100), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(   0, -100), new SKPoint(     0,   -125), new SKPoint() },
        { new SKPoint( -55, -100), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,  -55), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,    0), new SKPoint(  -125,      0), new SKPoint() },
        { new SKPoint(-100,   55), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint( -55,  100), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() }
    };
    ...
}
```

第二列包含四个定义其区域大约是圆的区域相同的正方形的贝塞尔曲线的坐标。 (绘制带有的方框*确切*为给定的圆形区域是经典无法解决的几何问题[相乘圆](https://en.wikipedia.org/wiki/Squaring_the_circle)。)用于呈现带有贝塞尔曲线的方框，每条曲线的两个控点是相同的并且它们是带起始和结束点共线的因此贝塞尔曲线呈现为一条直线。

有关动画相比内, 插值是数组的第三个列。 页设置为 16 毫秒，计时器和`PaintSurface`该速率调用处理程序：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    canvas.Translate(info.Width / 2, info.Height / 2);
    canvas.Scale(Math.Min(info.Width / 300, info.Height / 300));

    // Interpolate
    TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
    float t = (float)(timeSpan.TotalSeconds % 3 / 3);   // 0 to 1 every 3 seconds
    t = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;     // 0 to 1 to 0 sinusoidally

    for (int i = 0; i < 13; i++)
    {
        points[i, 2] = new SKPoint(
            (1 - t) * points[i, 0].X + t * points[i, 1].X,
            (1 - t) * points[i, 0].Y + t * points[i, 1].Y);
    }

    // Create the path and draw it
    using (SKPath path = new SKPath())
    {
        path.MoveTo(points[0, 2]);

        for (int i = 1; i < 13; i += 3)
        {
            path.CubicTo(points[i, 2], points[i + 1, 2], points[i + 2, 2]);
        }
        path.Close();

        canvas.DrawPath(path, cyanFill);
        canvas.DrawPath(path, blueStroke);
    }
}
```

基于 sinusoidally 振荡的值在插入点`t`。 内插的点然后用于构造一系列四个连接的贝塞尔曲线。 下面是动画显示为正方形的圆圈，圆圈进度的三个平台上运行：

[![](beziers-images/squaringthecircle-small.png "Squaring 的三个屏幕截图圆圈页")](beziers-images/squaringthecircle-large.png#lightbox "Squaring 的三个屏幕截图圆圈页")

此类动画就不可能而无需通过算法灵活，可以呈现为圆弧和直线的曲线。

**贝塞尔无穷大**页还可利用的贝塞尔曲线的近似圆弧的功能。下面是`PaintSurface`处理程序[ `BezierInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs)类：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.MoveTo(0, 0);                                // Center
        path.CubicTo(  50,  -50,   95, -100,  150, -100); // To top of right loop
        path.CubicTo( 205, -100,  250,  -55,  250,    0); // To far right of right loop
        path.CubicTo( 250,   55,  205,  100,  150,  100); // To bottom of right loop
        path.CubicTo(  95,  100,   50,   50,    0,    0); // Back to center  
        path.CubicTo( -50,  -50,  -95, -100, -150, -100); // To top of left loop
        path.CubicTo(-205, -100, -250,  -55, -250,    0); // To far left of left loop
        path.CubicTo(-250,   55, -205,  100, -150,  100); // To bottom of left loop
        path.CubicTo( -95,  100,  -50,   50,    0,    0); // Back to center
        path.Close();

        SKRect pathBounds = path.Bounds;
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.9f * Math.Min(info.Width / pathBounds.Width,
                                     info.Height / pathBounds.Height));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 5;

            canvas.DrawPath(path, paint);
        }
    }
}
```

它可能是很好的锻炼要绘制在关系图纸上的这些坐标，以查看它们如何相关。 无穷大登录居中绕点 （0，0），并在两个循环使用的中心 （–150，0） 和 （150，0） 和为 100 的半径。 中的一系列`CubicTo`命令时，你可以看到 X 坐标的控制点从事 –95 和 –205 的值 （这些值是 –150 加号和减号 55），205 和 95 (150 加号和减号 55)，以及 250 和左右两侧的 –250。 在中心的无穷大登录跨越本身时唯一的例外。 在这种情况下，控制点具有 50 和 – 50 若要拉伸出中心附近曲线的组合的坐标。

下面是在所有三个平台上的无穷大登录：

[![](beziers-images/bezierinfinity-small.png "三重的贝塞尔无穷大页面屏幕截图")](beziers-images/bezierinfinity-large.png#lightbox "贝塞尔无穷大页面的三个屏幕截图")

它是模型更流畅的中心呈现的无穷大号**弧线无穷大**来自页[**绘制一段弧线，三个方法**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)文章。

## <a name="the-quadratic-bzier-curve"></a>二次贝塞尔曲线

二次贝塞尔曲线具有只有一个控制点，并且由只需三个点定义曲线： 的起始点、 控制点和终结点。 参数等式仅为三次方贝塞尔曲线，非常类似，只不过最高的指数是 2，因此曲线是二次多项式：

x(t) = (1-t) ²x₀ + 2t (1-t) x₁ + t²x₂

y(t) = (1-t) ²y₀ + 2t (1-t) y₁ + t²y₂

若要向路径添加二次贝塞尔曲线，使用[ `QuadTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.QuadTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/)方法或[ `QuadTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.QuadTo/p/System.Single/System.Single/System.Single/System.Single/)具有单独的重载`x`和`y`坐标：

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

方法从当前位置添加一条曲线`point2`与`point1`作为的控制点。

您可以尝试使用与二次贝塞尔曲线**二次曲线**页上，非常类似于**贝塞尔曲线**页上，但它具有只有三个触摸点。 下面是`PaintSurface`中的处理程序[ **QuadraticCurve.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs)代码隐藏文件：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with quadratic Bezier
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.QuadTo(touchPoints[1].Center,
                    touchPoints[2].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

并在所有三个平台上运行此处：

[![](beziers-images/quadraticcurve-small.png "三重的二次曲线页面屏幕截图")](beziers-images/quadraticcurve-large.png#lightbox "二次曲线页面的三个屏幕截图")

虚线是曲线的起始点和终结点处的切线，和满足在控点。

如果你需要的常规形状，曲线，但您喜欢的几个控件点，而不是两个方便起见，二次贝塞尔是好的。 二次贝塞尔呈现比任何其他曲线，这正是它在内部使用 Skia 呈现条椭圆弧更为高效。

但是，二次贝塞尔曲线的形状不椭圆，这就是为什么需要多个二次 Béziers 来估计一条椭圆弧。二次贝塞尔改为是出一条抛物线的一个段。

## <a name="the-conic-bzier-curve"></a>圆锥的贝塞尔曲线

圆锥的贝塞尔曲线&mdash;也称为合理的二次贝塞尔曲线&mdash;是相对较新的补充，到的贝塞尔曲线的系列。 二次贝塞尔曲线，如合理的二次贝塞尔曲线涉及起点、 终点和一个控制点。 但也需要合理的二次贝塞尔曲线*权重*值。 它称为*合理*二次，因为参数公式涉及比率。

参数的公式为 X 和 Y 是共享相同的分母的比率。 下面是有关分母的公式*t*范围从 0 到 1 和权重值为*w*:

d(t) = (1-t) ² + 2wt(1 – t) + t²

从理论上讲，合理 quadratic 可能涉及三个单独的权重值，每个这三个术语，但这些可以简化为中间术语上只有一个权重值。

区别在于中间术语还包括的权重值，并且表达式除以分母，X 和 Y 坐标的参数的公式是类似于二次贝塞尔参数的公式：

x(t) = ((1 – t) ²x₀ + 2wt (1-t) x₁ + t²x₂)) ÷ d(t)

y(t) = ((1 – t) ²y₀ + 2wt (1-t) y₁ + t²y₂)) ÷ d(t)

也称为合理二次贝塞尔曲线*conics*因为它们完全可以表示任何圆锥部分段&mdash;hyperbolas、 parabolas、 省略号和圆圈。

若要向路径添加合理的二次贝塞尔曲线，使用[ `ConicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ConicTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/System.Single/)方法或[ `ConicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ConicTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/)具有单独的重载`x`和`y`坐标：

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

请注意最后一个`weight`参数。

**圆锥曲线**页面允许您尝试使用这些曲线。 `ConicCurvePage` 类派生自 `InteractivePage`。 [ **ConicCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml)文件实例化`Slider`选择 – 2 和 2 之间的权重值。 [ **ConicCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs)隐藏代码文件创建三个`TouchPoint`对象，与`PaintSurface`处理程序只需呈现结果与对控件的切线线曲线要点：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with conic curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.ConicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     (float)weightSlider.Value);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

此处所有三个平台上运行它：

[![](beziers-images/coniccurve-small.png "三重的圆锥曲线页面屏幕截图")](beziers-images/coniccurve-large.png#lightbox "圆锥曲线页面的三个屏幕截图")

如你所见的控制点看上去已拉入更多曲线权重更高版本时。 当权重为零时，曲线将成为一条直线的起始点从终结点。

从理论上讲，负权重允许，并且导致曲线以将弯曲*消失*从的控制点。 但是中的参数的公式变得的特定值为负分母权重 – 1 或低于原因*t*。 可能出于此原因，负权重将忽略`ConicTo`方法。 **圆锥曲线**程序允许你设置负权重，但正如您可以看到通过动手试验来，负权重具有相同的效果的权重为零，并且导致要呈现的直线。

它是很容易派生的控点和权重，则使用`ConicTo`方法，以便最多绘制圆弧 （但不是包括） 一个半圆。 在下图中，开始和结束点的切线满足在控点。

![](beziers-images/conicarc.png "一个圆弧锥形弧呈现")

你可以使用三角函数来确定从圆的中心控点的距离： 是的除以半角度 α 的余弦值圆的半径。 若要在开始和终结点之间绘制圆弧，请将权重设置为半角度该相同余弦值。 请注意，是否角度为 180 度，然后切线永远不会满足并权重为零。 但对于角度小于 180 度，数学计算工作正常。

**圆锥圆弧**演示这一页。 [ **ConicCircularArc.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml)文件实例化`Slider`用于选择角度。 `PaintSurface`中的处理程序[ **ConicCircularArc.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs)代码隐藏文件计算的控制点和权重：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 4;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the points and weight
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point1 = new SKPoint(0, radius / cos);
    SKPoint point2 = new SKPoint(radius * sin, radius * cos);
    float weight = cos;

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point2.X, point2.Y, point1.X, point1.Y, dottedStroke);

    // Draw the conic
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.ConicTo(point1, point2, weight);
        canvas.DrawPath(path, redStroke);
    }
}
```

如你所见，没有任何 visual 区别之间`ConicTo`以红色显示的路径和基础圆圈显示的引用：

[![](beziers-images/coniccirculararc-small.png "三重的圆锥圆弧页面屏幕截图")](beziers-images/coniccirculararc-large.png#lightbox "圆锥圆弧页面的三个屏幕截图")

但设置为 180 度，以及将其数学故障的角度。

遗憾的是在这种情况下，`ConicTo`不支持负权重，因为在理论上 （基于以下参数方程确定），可以使用另一个调用完成圆`ConicTo`相同的点而负权重的值。 这将允许使用只使用两个创建整个圆形`ConicTo`曲线上任意角度之间 （但不是包括） 基于零度和 180 度。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
