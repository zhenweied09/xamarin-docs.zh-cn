---
title: 路径信息和枚举
description: 本文章介绍如何获取有关 SkiaSharp 路径的信息和枚举的内容，并演示这一替换示例代码。
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 09/12/2017
ms.openlocfilehash: 53d1fce20a0e3bc75ba34ab84b2549211567e222
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243787"
---
# <a name="path-information-and-enumeration"></a>路径信息和枚举

_获取有关路径的信息和枚举的内容_

[ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/)类定义一些属性和方法，可用于获取有关路径的信息。 [ `Bounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.Bounds/)和[ `TightBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.TightBounds/)属性 （和相关的方法） 获取路径的度量尺寸。 [ `Contains` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Contains/p/System.Single/System.Single/)方法可让你确定某个特定点是否在路径。

有时它可用于确定所有直线和曲线构成路径的总长度。 这不是通过算法简单的任务，因此整个类名为[ `PathMeasure` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasure/)专门用于访问它。

它也是有时有用若要获取所有绘制操作和构成路径点。 首先，此工具可能似乎没有必要： 如果你的程序创建了路径，该程序已知道的内容。 但是，你已了解路径还可以通过创建[路径效果](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)又将转换[为路径的文本字符串](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)。 你还可以获取所有的绘制操作和构成这些路径点。 一种可能性是要应用于所有点的算法的转换。 这样，如文字环绕半球的技术：

![](information-images/pathenumerationsample.png "包装在半球上的文本")

## <a name="getting-the-path-length"></a>获取路径长度

本文[**路径和文本**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)您了解到如何使用[ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/)方法，以便绘制其基线遵循路径的过程的文本字符串。 但是，如果你想要调整文本大小，以便它精确适合路径？ 对于圆环绘制文本，这很容易，因为圆的周长很容易计算。 但的椭圆的圆的周长或的贝塞尔曲线的长度并不那么简单。

[ `SKPathMeasure` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasure/)类可帮助。 [构造函数](https://developer.xamarin.com/api/constructor/SkiaSharp.SKPathMeasure.SKPathMeasure/p/SkiaSharp.SKPath/System.Boolean/System.Single/)接受`SKPath`自变量，与[ `Length` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPathMeasure.Length/)属性显示其长度。

此进行了演示**路径长度**示例，后者基于**贝塞尔曲线**页。 [ **PathLengthPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml)文件派生自`InteractivePage`并包括触摸接口：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Curves.PathLengthPage"
                       Title="Path Length">
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</local:InteractivePage>
```

[ **PathLengthPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs)代码隐藏文件可以移动四个触摸点来定义终结点和控制的三次方贝塞尔曲线的点。 三个字段定义文本字符串，`SKPaint`对象，并计算所得的文本宽度：

```csharp
public partial class PathLengthPage : InteractivePage
{
    const string text = "Compute length of path";

    static SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Black,
        TextSize = 10,
    };

    static readonly float baseTextWidth = textPaint.MeasureText(text);
    ...
}
```

`baseTextWidth`字段是基于文本的宽度`TextSize`设置为 10。

`PaintSurface`处理程序绘制贝塞尔曲线，然后调整大小以适应其整个长度的文本：

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

        // Get path length
        SKPathMeasure pathMeasure = new SKPathMeasure(path, false, 1);

        // Find new text size
        textPaint.TextSize = pathMeasure.Length / baseTextWidth * 10;

        // Draw text on path
        canvas.DrawTextOnPath(text, path, 0, 0, textPaint);
    }
    ...
}
```

`Length`的新创建的属性`SKPathMeasure`对象获取路径的长度。 这除以`baseTextWidth`值 （这是基于文本大小为 10 的文本的宽度） 和再乘以 10 基的文本大小。 结果为新的文本大小显示沿该路径的文本：

[![](information-images/pathlength-small.png "三重的路径长度页面屏幕截图")](information-images/pathlength-large.png#lightbox "三倍的路径长度页面屏幕截图")

因为贝塞尔曲线变得更长或较短，你可以查看更改的文本大小。

## <a name="traversing-the-path"></a>遍历路径

`SKPathMeasure` 可以执行操作以外的其他度量值的路径长度。 对于任何值介于 0 与路径长度之间`SKPathMeasure`对象可以在该点获取上的路径，以及为路径曲线的正切值的位置。 的正切值是可用作一个向量中的窗体`SKPoint`对象，或为旋转封装在`SKMatrix`对象。 此处列出的方法`SKPathMeasure`，获取此信息以可变和灵活的方式：

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

[ `SKPathMeasureMatrixFlags` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasureMatrixFlags/)是：

- [`GetPosition`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPosition/)
- [`GetTangent`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPositionAndTangent/)
- [`GetPositionAndTangent`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPositionAndTangent/)

**脚踏车半管道**页进行动画处理似乎沿三次方贝塞尔曲线来回写脚踏车上一个简图：

[![](information-images/unicyclehalfpipe-small.png "三重的脚踏车半管道页的屏幕截图")](information-images/unicyclehalfpipe-large.png#lightbox "脚踏车半管道页面的三个屏幕截图")

`SKPaint`用于描边半管道和脚踏车对象定义中的一个字段为[ `UnicycleHalfPipePage` ]()类。 此外定义是`SKPath`脚踏车对象：

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };

    SKPath unicyclePath = SKPath.ParseSvgPathData(
        "M 0 0" +
        "A 25 25 0 0 0 0 -50" +
        "A 25 25 0 0 0 0 0 Z" +
        "M 0 -25 L 0 -100" +
        "A 15 15 0 0 0 0 -130" +
        "A 15 15 0 0 0 0 -100 Z" +
        "M -25 -85 L 25 -85");
    ...
}
```

类包含的标准重写之一`OnAppearing`和`OnDisappearing`动画的方法。 `PaintSurface`处理程序创建半管道的路径，然后绘制它。 `SKPathMeasure`对象然后创建基于此路径上：

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath pipePath = new SKPath())
        {
            pipePath.MoveTo(50, 50);
            pipePath.CubicTo(0, 1.25f * info.Height,
                             info.Width - 0, 1.25f * info.Height,
                             info.Width - 50, 50);

            canvas.DrawPath(pipePath, strokePaint);

            using (SKPathMeasure pathMeasure = new SKPathMeasure(pipePath))
            {
                float length = pathMeasure.Length;

                // Animate t from 0 to 1 every three seconds
                TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
                float t = (float)(timeSpan.TotalSeconds % 5 / 5);

                // t from 0 to 1 to 0 but slower at beginning and end
                t = (float)((1 - Math.Cos(t * 2 * Math.PI)) / 2);

                SKMatrix matrix;
                pathMeasure.GetMatrix(t * length, out matrix,
                                      SKPathMeasureMatrixFlags.GetPositionAndTangent);

                canvas.SetMatrix(matrix);
                canvas.DrawPath(unicyclePath, strokePaint);
            }
        }
    }
}
```

`PaintSurface`处理程序计算的值`t`，范围为 0 到 1 每五秒。 然后，它使用`Math.Cos`函数以将其转换为的值`t`，范围从 0 到 1 并返回到 0，其中 0 对应于在左上角，开头脚踏车虽然 1 对应于脚踏车右上角。 余弦函数会导致要慢管道顶部和底部最快的速度。

请注意，此值的`t`必须相乘的第一个参数的路径长度`GetMatrix`。 矩阵随后会应用于`SKCanvas`用于进行绘制脚踏车路径对象。

## <a name="enumerating-the-path"></a>枚举路径

两个嵌入的类`SKPath`可用于枚举路径的内容。 这些类是[ `SKPath.Iterator` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath+Iterator/)和[ `SKPath.RawIterator` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath+RawIterator/)。 两个类是非常相似，但`SKPath.Iterator`可以消除与长度为零，或接近零长度的路径中的元素。 `RawIterator`在下面的示例中使用。

你可以获取类型的对象`SKPath.RawIterator`通过调用[ `CreateRawIterator` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CreateRawIterator()/)方法`SKPath`。 枚举整个路径通过重复调用来实现[ `Next` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath+RawIterator.Next/p/SkiaSharp.SKPoint[]/)方法。 将四个数组传递给它`SKPoint`值：

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

`Next`方法返回的成员[ `SKPathVerb` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathVerb/)枚举。 这些值指示路径中的特定绘图命令。 在数组中插入的有效点数目取决于此谓词：

- [`Move`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Move/) 单点
- [`Line`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Line/) 使用两个点
- [`Cubic`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Cubic/) 具有四个点
- [`Quad`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Quad/) 使用三个点
- [`Conic`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Conic/) 使用三个点 (和还调用[ `ConicWeight` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath+RawIterator.ConicWeight/)的权重的方法)
- [`Close`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Close/) 与一个点
- [`Done`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Done/)

`Done`谓词指示枚举已完成。

请注意，有没有`Arc`谓词。 这表示所有弧将都转换为贝塞尔曲线时添加到的路径。

中的信息的一些`SKPoint`是多余的数组。 例如，如果`Move`谓词后跟`Line`谓词，则伴随的两个点的第一个`Line`相同`Move`点。 在实践中，此冗余是非常有帮助。 在你将获得`Cubic`谓词，它伴随定义三次方贝塞尔曲线的所有四个点。 你不需要保留以前的谓词所建立的当前位置。

有问题的谓词，但是，是`Close`。 此命令之前由建立的轮廓的开头到从当前位置绘制一条直线`Move`命令。 理想情况下，`Close`谓词应提供这两个点，而不是一个点。 更糟的是在于点伴随`Close`谓词始终是 （0，0）。 这意味着，当枚举通过路径时，操作时你将可能需要保留`Move`点和当前的位置。

静态[ `PathExtensions` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs)类包含将三种类型的贝塞尔曲线转换为一系列的微小近似曲线的直线的几种方法。 (文章中提供的参数的公式就好像[**三个类型的贝塞尔曲线**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)。)`Interpolate`方法将一条直线分成是只有一个单位的长度的许多短线条分解：

```csharp
static class PathExtensions
{
    ...
    static SKPoint[] Interpolate(SKPoint pt0, SKPoint pt1)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * pt0.X + t * pt1.X;
            float y = (1 - t) * pt0.Y + t * pt1.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenCubic(SKPoint pt0, SKPoint pt1, SKPoint pt2, SKPoint pt3)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2) + Length(pt2, pt3));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * (1 - t) * pt0.X +
                        3 * t * (1 - t) * (1 - t) * pt1.X +
                        3 * t * t * (1 - t) * pt2.X +
                        t * t * t * pt3.X;
            float y = (1 - t) * (1 - t) * (1 - t) * pt0.Y +
                        3 * t * (1 - t) * (1 - t) * pt1.Y +
                        3 * t * t * (1 - t) * pt2.Y +
                        t * t * t * pt3.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenQuadratic(SKPoint pt0, SKPoint pt1, SKPoint pt2)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenConic(SKPoint pt0, SKPoint pt1, SKPoint pt2, float weight)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float denominator = (1 - t) * (1 - t) + 2 * weight * t * (1 - t) + t * t;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * weight * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * weight * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            x /= denominator;
            y /= denominator;
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

所有这些方法从该扩展方法中引用`CloneWithTransform`如下所示。 此方法通过枚举路径命令和构造基于的数据的新路径克隆路径。 但是，新路径仅包含`MoveTo`和`LineTo`调用。 所有直线和曲线会都减小为一系列的微小的行。

在调用时`CloneWithTransform`，传递给方法`Func<SKPoint, SKPoint>`，它是使用函数`SKPaint`返回的参数`SKPoint`值。 为每个点将自定义算法转换应用调用此函数：

```csharp
static class PathExtensions
{
    public static SKPath CloneWithTransform(this SKPath pathIn, Func<SKPoint, SKPoint> transform)
    {
        SKPath pathOut = new SKPath();

        using (SKPath.RawIterator iterator = pathIn.CreateRawIterator())
        {
            SKPoint[] points = new SKPoint[4];
            SKPathVerb pathVerb = SKPathVerb.Move;
            SKPoint firstPoint = new SKPoint();
            SKPoint lastPoint = new SKPoint();

            while ((pathVerb = iterator.Next(points)) != SKPathVerb.Done)
            {
                switch (pathVerb)
                {
                    case SKPathVerb.Move:
                        pathOut.MoveTo(transform(points[0]));
                        firstPoint = lastPoint = points[0];
                        break;

                    case SKPathVerb.Line:
                        SKPoint[] linePoints = Interpolate(points[0], points[1]);

                        foreach (SKPoint pt in linePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[1];
                        break;

                    case SKPathVerb.Cubic:
                        SKPoint[] cubicPoints = FlattenCubic(points[0], points[1], points[2], points[3]);

                        foreach (SKPoint pt in cubicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[3];
                        break;

                    case SKPathVerb.Quad:
                        SKPoint[] quadPoints = FlattenQuadratic(points[0], points[1], points[2]);

                        foreach (SKPoint pt in quadPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Conic:
                        SKPoint[] conicPoints = FlattenConic(points[0], points[1], points[2], iterator.ConicWeight());

                        foreach (SKPoint pt in conicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Close:
                        SKPoint[] closePoints = Interpolate(lastPoint, firstPoint);

                        foreach (SKPoint pt in closePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        firstPoint = lastPoint = new SKPoint(0, 0);
                        pathOut.Close();
                        break;
                }
            }
        }
        return pathOut;
    }
    ...
}
```

由于克隆的路径减至小直线时，转换函数具有将直线转换为曲线的功能。

请注意该方法将保留在调用变量中的每个轮廓线的第一个点`firstPoint`并在每行后当前的位置绘制命令在变量`lastPoint`。 这些是必需构造最后一个右行时`Close`遇到谓词。

**GlobularText**示例使用此扩展方法看似将文字环绕半球包装在 3D 效果：

[![](information-images/globulartext-small.png "三重的 Globular 文本页的屏幕截图")](information-images/globulartext-large.png#lightbox "Globular 文本页面的三个屏幕截图")

[ `GlobularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs)类构造函数执行此转换。 它将创建`SKPaint`对象的文本，然后，获取`SKPath`对象`GetTextPath`方法。 这是传递给的路径`CloneWithTransform`以及转换函数的扩展方法：

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;

    public GlobularTextPage()
    {
        Title = "Globular Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.Typeface = SKTypeface.FromFamilyName("Times New Roman");
            textPaint.TextSize = 100;

            using (SKPath textPath = textPaint.GetTextPath("HELLO", 0, 0))
            {
                SKRect textPathBounds;
                textPath.GetBounds(out textPathBounds);

                globePath = textPath.CloneWithTransform((SKPoint pt) =>
                {
                    double longitude = (Math.PI / textPathBounds.Width) *
                                            (pt.X - textPathBounds.Left) - Math.PI / 2;
                    double latitude = (Math.PI / textPathBounds.Height) *
                                            (pt.Y - textPathBounds.Top) - Math.PI / 2;

                    longitude *= 0.75;
                    latitude *= 0.75;

                    float x = (float)(Math.Cos(latitude) * Math.Sin(longitude));
                    float y = (float)Math.Sin(latitude);

                    return new SKPoint(x, y);
                });
            }
        }
    }
    ...
}
```

转换函数首先计算两个名为的值`longitude`和`latitude`，频率范围介于-π/2 在顶部和左侧的文本，到 π/2 右侧和底部的文本。 这些值的范围不直观地令人满意的因此它们通过乘以 0.75 减少。 （请尝试不使用这些调整代码。 文本变得太晦涩，在北部和南极地，而且在两侧太精简。）这些三维球坐标将转换为二维`x`和`y`通过标准的公式的坐标。

新的路径存储为字段。 `PaintSurface`处理程序，则只需要居中和缩放要在屏幕上显示的路径：

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint pathPaint = new SKPaint())
        {
            pathPaint.Style = SKPaintStyle.Fill;
            pathPaint.Color = SKColors.Blue;
            pathPaint.StrokeWidth = 3;
            pathPaint.IsAntialias = true;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(0.45f * Math.Min(info.Width, info.Height));     // radius
            canvas.DrawPath(globePath, pathPaint);
        }
    }
}
```

这是各种各样的技术。 如果路径效果的数组中所述[**路径效果**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)文章非常不包含内容认为应将包括在内，这是一种方法来弥补缺陷。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
