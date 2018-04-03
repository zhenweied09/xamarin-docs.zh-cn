---
title: 绘制一段弧线，三个方法
description: 了解如何使用 SkiaSharp 三个不同的方式来定义弧
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: charlespetzold
ms.author: chape
ms.date: 05/10/2017
ms.openlocfilehash: 86623886e2429cbf2f076fbe4583301edf684262
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="three-ways-to-draw-an-arc"></a>绘制一段弧线，三个方法

_了解如何使用 SkiaSharp 三个不同的方式来定义弧_

一段弧线，是椭圆的一条曲线，例如此无穷大登录的舍入部分的圆的周长上：

![](arcs-images/arcsample.png "无穷大登录")

尽管该定义的简单起见，没有方法来定义满足所有需求，弧线绘图函数，因此，在绘制一段弧线的最佳方法的图形系统之间没有共识。为此，`SKPath`类不限制本身到只是一种方法。

`SKPath` 定义`AddArc`方法时，五个不同`ArcTo`方法，且两个相对`RArcTo`方法。 这些方法分为三个类别，表示对指定一段弧线，三个非常不同的方法。使用哪一个取决于可用来定义圆弧，并且此弧线如何适应其他要绘制的图形的信息。

## <a name="the-angle-arc"></a>角度弧线

绘制弧的角度弧线方法要求您指定限定椭圆的矩形。 由进行开始弧线和其长度的椭圆的中心的不同角度指示圆弧的此椭圆的圆的周长。 两个不同的方法绘制角度弧。 这些是[ `AddArc` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddArc/p/SkiaSharp.SKRect/System.Single/System.Single/)方法和[ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKRect/System.Single/System.Single/System.Boolean/)方法：

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

这些方法都是相同 Android [ `AddArc` ](https://developer.xamarin.com/api/member/Android.Graphics.Path.AddArc/p/Android.Graphics.RectF/System.Single/System.Single/)和[ `ArcTo` ](https://developer.xamarin.com/api/member/Android.Graphics.Path.ArcTo/p/Android.Graphics.RectF/System.Single/System.Single/System.Boolean/)方法。 IOS [ `AddArc` ](https://developer.xamarin.com/api/member/CoreGraphics.CGPath.AddArc/p/System.Boolean/System.nfloat/System.nfloat/System.nfloat/System.nfloat/System.nfloat/)方法类似，但仅限于在圆的周长弧而推广到椭圆。

这两种方法开头`SKRect`值，该值定义的位置和大小的椭圆的：

![](arcs-images/anglearcoval.png "开始一段弧线，角度 oval")

圆弧属于此椭圆的圆的周长。

`startAngle`参数是以度表示相对于右侧从椭圆的中心绘制一条横线顺时针旋转角度。 `sweepAngle`自变量是相对于`startAngle`。 以下是`startAngle`和`sweepAngle`60 和 100 个度，值分别：

![](arcs-images/anglearcangles.png "定义一段弧线，角度角度")

圆弧开始的起始角度。 其长度受扫描角度：

![](arcs-images/anglearchighlight.png "突出显示的角度弧线")

为包含的路径添加曲线`AddArc`或`ArcTo`方法是只需椭圆的圆的周长，此处以红色显示的该部分：

![](arcs-images/anglearc.png "自行角度弧线")

`startAngle`或`sweepAngle`参数可以是负数： 弧是正值的顺时针旋转`sweepAngle`和负值逆时针旋转。

但是，`AddArc`未*不*定义封闭的轮廓线。 如果调用`LineTo`后`AddArc`，到中的点后面的圆弧绘制一条线`LineTo`方法，以及相同为 true 的`ArcTo`。

`AddArc` 自动启动新的轮廓线和功能上等效于调用`ArcTo`的最后一个参数与`true`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

最后一个自变量称为`forceMoveTo`，并有效地使`MoveTo`调用弧的开头。用于开始新的轮廓。 它是不是这样的最后一个参数与`false`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

此版本的`ArcTo`绘制一条从当前位置到弧线的开头。这意味着弧线可以某个位置是更大的轮廓线的中间。

**角度弧线**页允许你使用两个滑块来指定开始日期和扫描角度。 XAML 文件实例化两个`Slider`元素和`SKCanvasView`。 `PaintCanvas`中的处理程序[ **AngleArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs)文件绘制椭圆和弧线使用两个`SKPaint`定义为字段的对象：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
    float startAngle = (float)startAngleSlider.Value;
    float sweepAngle = (float)sweepAngleSlider.Value;

    canvas.DrawOval(rect, outlinePaint);

    using (SKPath path = new SKPath())
    {
        path.AddArc(rect, startAngle, sweepAngle);
        canvas.DrawPath(path, arcPaint);
    }
}
```

如你所见，开始角度和扫描角度可以执行对负值：

[![](arcs-images/anglearc-small.png "三重的角度弧线页面屏幕截图")](arcs-images/anglearc-large.png#lightbox "角度弧线页面的三个屏幕截图")

生成一段弧线，此方法是通过算法最简单而且很容易派生描述弧线的参数化公式。了解大小和位置椭圆和开始和扫描角度，开始日期和弧的终结点可以被计算使用简单 trigonometry:

x = oval.MidX + (oval.Width / 2) * cos(angle)

y = 椭圆。MidY + （椭圆。高度 / 2) * sin(angle)

`angle`值可以是`startAngle`或`startAngle + sweepAngle`。

两个角度来定义一段弧线，使用适合于情况下，如果你知道你想要绘制，例如，以使饼图弧的角度的长度。 **分离型饼图**演示这一页。 [ `ExplodedPieChartPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs)类使用的内部类定义某些虚构的数据和颜色：

```csharp
class ChartData
{
    public ChartData(int value, SKColor color)
    {
        Value = value;
        Color = color;
    }

    public int Value { private set; get; }

    public SKColor Color { private set; get; }
}

ChartData[] chartData =
{
    new ChartData(45, SKColors.Red),
    new ChartData(13, SKColors.Green),
    new ChartData(27, SKColors.Blue),
    new ChartData(19, SKColors.Magenta),
    new ChartData(40, SKColors.Cyan),
    new ChartData(22, SKColors.Brown),
    new ChartData(29, SKColors.Gray)
};

```

`PaintSurface`处理程序首先循环访问要计算的项`totalValues`数。 由此，它可以确定每个项的大小，总共的分数，并将其转换到角度：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int totalValues = 0;

    foreach (ChartData item in chartData)
    {
        totalValues += item.Value;
    }

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float explodeOffset = 50;
    float radius = Math.Min(info.Width / 2, info.Height / 2) - 2 * explodeOffset;
    SKRect rect = new SKRect(center.X - radius, center.Y - radius,
                             center.X + radius, center.Y + radius);

    float startAngle = 0;

    foreach (ChartData item in chartData)
    {
        float sweepAngle = 360f * item.Value / totalValues;

        using (SKPath path = new SKPath())
        using (SKPaint fillPaint = new SKPaint())
        using (SKPaint outlinePaint = new SKPaint())
        {
            path.MoveTo(center);
            path.ArcTo(rect, startAngle, sweepAngle, false);
            path.Close();

            fillPaint.Style = SKPaintStyle.Fill;
            fillPaint.Color = item.Color;

            outlinePaint.Style = SKPaintStyle.Stroke;
            outlinePaint.StrokeWidth = 5;
            outlinePaint.Color = SKColors.Black;

            // Calculate "explode" transform
            float angle = startAngle + 0.5f * sweepAngle;
            float x = explodeOffset * (float)Math.Cos(Math.PI * angle / 180);
            float y = explodeOffset * (float)Math.Sin(Math.PI * angle / 180);

            canvas.Save();
            canvas.Translate(x, y);

            // Fill and stroke the path
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, outlinePaint);
            canvas.Restore();
        }

        startAngle += sweepAngle;
    }
}
```

一个新`SKPath`为每个饼图扇区中创建对象。 路径由中心中的一行则`ArcTo`绘制圆弧和另一个行返回的中心结果`Close`调用。 此程序显示"分离型"饼图扇区，从而将它们全部从中心 50 个像素。 该任务需要扫描角度的中点方向向量为每个切片：

[![](arcs-images/explodedpiechart-small.png "分离型饼图页面的三个屏幕截图")](arcs-images/explodedpiechart-large.png#lightbox "分离型饼图页面的三个屏幕截图")

若要查看如下所示没有"膨胀"的情况下，只需注释掉`Translate`调用：

[![](arcs-images/explodedpiechartunexploded-small.png "三重而无需爆炸式增长的分离型饼图页面屏幕截图")](arcs-images/explodedpiechartunexploded-large.png#lightbox "不爆炸式增长的情况下分离型饼图图表页的三个屏幕快照")

## <a name="the-tangent-arc"></a>切线弧线

支持的弧的第二种类型`SKPath`是*切线弧线*，因此调用，因为弧是圆的正切值为两个连接的线条的周长。

切线弧线添加到路径上，通过调用[ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/System.Single/)具有两个方法`SKPoint`参数，或[ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/)具有单独的重载`Single`参数要点：

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

这`ArcTo`方法类似于 PostScript 是[ `arct` ](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) （PDF 文档中的页 532） 函数和 iOS [ `AddArcToPoint` ](https://developer.xamarin.com/api/member/CoreGraphics.CGPath.AddArcToPoint/p/System.nfloat/System.nfloat/System.nfloat/System.nfloat/System.nfloat/)方法。

`ArcTo`方法涉及三个点：

- 如果当前点具有轮廓或点 （0，0） 的`MoveTo`尚未调用
- 第一个点参数`ArcTo`调用的方法*角点*
- 第二个点参数`ArcTo`、 调用*目标点*:

![](arcs-images/tangentarcthreepoints.png "开始切线弧线的三个点")

这些三个点定义两个连接的直线：

![](arcs-images/tangentarcconnectinglines.png "连接三个点的切线弧线的行")

如果三个点共线&mdash;，即如果上一条直线它们位于&mdash;将绘制没有弧。

`ArcTo`方法还包括`radius`参数。 这定义的圆的半径：

![](arcs-images/tangentarccircle.png "圆的切线弧线")

切线弧线未通用化的椭圆。

如果在任何角度满足两个行，可将该圆圈插入这些行之间以便于这两个行的正切值：

![](arcs-images/tangentarctangentcircle.png "圆两条线之间的切线弧线")

添加到轮廓线的曲线不触及中指定的点任一`ArcTo`方法。 它包含一条直线从当前点为第一个切点和一段弧线，在第二个切点处结尾：

![](arcs-images/tangentarchighlight.png "之间的两行的突出显示切线图弧线")

下面是最终直线和添加到轮廓线的弧线：

![](arcs-images/tangentarc.png "之间的两行的突出显示切线图弧线")

可以从第二个切点继续具有轮廓。

**正切弧线**页面允许您尝试使用切线弧。这是派生自的多个页面的第一个[ `InteractivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/InteractivePage.cs)，后者定义了一些方便`SKPaint`对象，并执行`TouchPoint`处理：

```csharp
public class InteractivePage : ContentPage
{
    protected SKCanvasView baseCanvasView;
    protected TouchPoint[] touchPoints;

    protected SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3
    };

    protected SKPaint redStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 15
    };

    protected SKPaint dottedStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    };

    protected void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = baseCanvasView.CanvasSize.Width / (float)baseCanvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            baseCanvasView.InvalidateSurface();
        }
    }
}
```

`TangentArcPage` 类派生自 `InteractivePage`。 中的构造函数[ **TangentArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs)文件都负责实例化和初始化`touchPoints`数组，并且设置`baseCanvasView`(在`InteractivePage`) 到`SKCanvasView`中实例化对象[ **TangentArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml)文件：

```csharp
public partial class TangentArcPage : InteractivePage
{
    public TangentArcPage()
    {
        touchPoints = new TouchPoint[3];

        for (int i = 0; i < 3; i++)
        {
            TouchPoint touchPoint = new TouchPoint
            {
                Center = new SKPoint(i == 0 ? 100 : 500,
                                     i != 2 ? 100 : 500)
            };
            touchPoints[i] = touchPoint;
        }

        InitializeComponent();

        baseCanvasView = canvasView;
        radiusSlider.Value = 100;
    }

    void sliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

`PaintSurface`处理程序使用`ArcTo`方法，以便绘制弧基于触摸点和`Slider`，但也通过算法计算的圆圈，表示的角度依赖于使用：

```csharp
public partial class TangentArcPage : InteractivePage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw the two lines that meet at an angle
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.LineTo(touchPoints[1].Center);
            path.LineTo(touchPoints[2].Center);
            canvas.DrawPath(path, dottedStrokePaint);
        }

        // Draw the circle that the arc wraps around
        float radius = (float)radiusSlider.Value;

        SKPoint v1 = Normalize(touchPoints[0].Center - touchPoints[1].Center);
        SKPoint v2 = Normalize(touchPoints[2].Center - touchPoints[1].Center);

        double dotProduct = v1.X * v2.X + v1.Y * v2.Y;
        double angleBetween = Math.Acos(dotProduct);
        float hypotenuse = radius / (float)Math.Sin(angleBetween / 2);
        SKPoint vMid = Normalize(new SKPoint((v1.X + v2.X) / 2, (v1.Y + v2.Y) / 2));
        SKPoint center = new SKPoint(touchPoints[1].Center.X + vMid.X * hypotenuse,
                                     touchPoints[1].Center.Y + vMid.Y * hypotenuse);

        canvas.DrawCircle(center.X, center.Y, radius, this.strokePaint);

        // Draw the tangent arc
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.ArcTo(touchPoints[1].Center, touchPoints[2].Center, radius);
            canvas.DrawPath(path, redStrokePaint);
        }

        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
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
}
```

下面是**正切弧线**页在所有三个平台上运行：

[![](arcs-images/tangentarc-small.png "正切值弧线页面三重屏幕截图")](arcs-images/tangentarc-large.png#lightbox "三倍的正切值弧线页面屏幕截图")

在 Windows 移动设备上，三个点是几乎共线，和弧是非常小。

切线弧线非常适合于创建圆的角，如圆角矩形。 因为`SKPath`已包含`AddRoundedRect`方法，**舍入 Heptagon**页演示如何使用`ArcTo`用于舍入七侧多边形的边角。 （代码通用化任何正则多边形中）。

`PaintSurface`处理程序[ `RoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs)类包含一个`for`循环来计算 heptagon 和第二个来计算从这些七个边指的七个顶点的坐标顶点。 然后，使用这些指构造路径：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float cornerRadius = 100;
    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPoint[] vertices = new SKPoint[numVertices];
    SKPoint[] midPoints = new SKPoint[numVertices];

    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    // Coordinates of the midpoints of the sides connecting the vertices
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        int prevVertex = (vertex + numVertices - 1) % numVertices;
        midPoints[vertex] = new SKPoint((vertices[prevVertex].X + vertices[vertex].X) / 2,
                                        (vertices[prevVertex].Y + vertices[vertex].Y) / 2);
    }

    // Create the path
    using (SKPath path = new SKPath())
    {
        // Begin at the first midpoint
        path.MoveTo(midPoints[0]);

        for (int vertex = 0; vertex < numVertices; vertex++)
        {
            SKPoint nextMidPoint = midPoints[(vertex + 1) % numVertices];

            // Draws a line from the current point, and then the arc
            path.ArcTo(vertices[vertex], nextMidPoint, cornerRadius);

            // Connect the arc with the next midpoint
            path.LineTo(nextMidPoint);
        }
        path.Close();

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);
        }
    }
}

```

此处是三个平台上运行的程序：

[![](arcs-images/roundedheptagon-small.png "三重的舍入 Heptagon 页面屏幕截图")](arcs-images/roundedheptagon-large.png#lightbox "三倍的舍入 Heptagon 页面屏幕截图")

## <a name="the-elliptical-arc"></a>椭圆弧

椭圆弧添加到路径中通过调用[ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKPoint/System.Single/SkiaSharp.SKPathArcSize/SkiaSharp.SKPathDirection/SkiaSharp.SKPoint/)方法具有两个`SKPoint`参数，或[ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/System.Single/System.Single/System.Single/SkiaSharp.SKPathArcSize/SkiaSharp.SKPathDirection/System.Single/System.Single/)具有单独的 X 和 Y 坐标的重载：

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

椭圆弧是与一致[椭圆弧](http://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands)包含在可缩放向量图形 (SVG) 和通用 Windows 平台[ `ArcSegment` ](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/)类。

这些`ArcTo`方法两个点，即具有轮廓的当前点之间绘制一段弧线，和的最后一个参数`ArcTo`方法 (`xy`参数或单独`x`和`y`参数):

![](arcs-images/ellipticalarcpoints.png "定义椭圆弧的两个点")

第一个点参数`ArcTo`方法 (`r`，或`rx`和`ry`) 根本不是点，但改为指定的椭圆; 的水平和垂直半径

![](arcs-images/ellipticalarcellipse.png "定义椭圆弧椭圆")

`xAxisRotate`参数是沿顺时针方向旋转的度数此椭圆数：

![](arcs-images/ellipticalarctiltedellipse.png "定义椭圆弧倾斜的椭圆")

如果此倾斜的椭圆然后位于以便与其接触的两个点，由两个不同弧连接点：

![](arcs-images/ellipticalarcellipse1.png "第一条椭圆弧组")

这些两个弧可以区分两种方式： 顶弧大于底部圆弧，并且如弧的绘制从左到右，顶部绘制弧将沿顺时针方向时按逆时针方向绘制底部弧。

还有可能以适应另一种方法的两个点之间的椭圆：

![](arcs-images/ellipticalarcellipse2.png "第二条椭圆弧的组")

现在有了为较小的弧沿顺时针方向，绘制的顶部和底部绘制为更大的弧逆时针旋转。

因此可以由一段弧线的总时间为四种方式倾斜的椭圆定义连接这两个点：

![](arcs-images/ellipticalarccolors.png "所有四个条椭圆弧")

通过的四个的组合来区分这些四个弧[ `SKPathArcSize` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathArcSize/)和[ `SKPathDirection` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathDirection/)枚举的类型参数`ArcTo`方法：

- 红色： SKPathArcSize.Large 和 SKPathDirection.Clockwise
- 绿色： SKPathArcSize.Small 和 SKPathDirection.Clockwise
- 蓝色： SKPathArcSize.Small 和 SKPathDirection.CounterClockwise
- 洋红色： SKPathArcSize.Large 和 SKPathDirection.CounterClockwise

如果倾斜的椭圆不是足以容纳两个点之间的然后它会统一缩放直到足够大。 只有两个唯一弧在这种情况下连接两个点。 可以与区分这些`SKPathDirection`参数。

尽管此方法定义一段弧线，听起来复杂上第一次遇到，它是唯一的方法，允许定义一段弧线，与旋转椭圆，且这经常是最简单的方法，当你需要与轮廓线的其他部分集成弧时。

**椭圆弧**页面允许您以交互方式设置的两个点的大小和椭圆旋转角度。 `EllipticalArcPage`类派生自`InteractivePage`，和`PaintSurface`中的处理程序[ **EllipticalArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs)代码隐藏文件绘制四个弧：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        int colorIndex = 0;
        SKPoint ellipseSize = new SKPoint((float)xRadiusSlider.Value,
                                          (float)yRadiusSlider.Value);
        float rotation = (float)rotationSlider.Value;

        foreach (SKPathArcSize arcSize in Enum.GetValues(typeof(SKPathArcSize)))
            foreach (SKPathDirection direction in Enum.GetValues(typeof(SKPathDirection)))
            {
                path.MoveTo(touchPoints[0].Center);
                path.ArcTo(ellipseSize, rotation,
                           arcSize, direction,
                           touchPoints[1].Center);

                strokePaint.Color = colors[colorIndex++];
                canvas.DrawPath(path, strokePaint);
                path.Reset();
            }
    }

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}

```

此处三个平台上运行它：

[![](arcs-images/ellipticalarc-small.png "三重的椭圆弧页面屏幕截图")](arcs-images/ellipticalarc-large.png#lightbox "椭圆弧页面的三个屏幕截图")

**弧线无穷大**页使用椭圆弧绘制无穷登录。 无穷大登录开始算起具有 100 个单位的隔开 100 个单位的半径的两个圆：

![](arcs-images/infinitycircles.png "两个圆")

跨相互的两行是到两个圆的正切值：

![](arcs-images/infinitycircleslines.png "带有切线的两个圆")

无穷大登录是这些圆形和两个行的部分的组合。 若要使用椭圆弧绘制无穷登录，必须确定其中的两个行是于圆形的正切值的坐标。

构造中的一个圆圈的右矩形：

![](arcs-images/infinitytriangle.png "两个圆圈，切线和嵌入的圆圈")

圆的半径是 100 个单位，还是三角形的斜边 150 个单位，因此 α 的角度的反正弦值 （反正弦值） 为 100 划分 150，通过或 41.8 度。 另一方的三角形长度为 150 时间的余弦值 41.8 度或 112，还可以通过毕达哥拉定理来计算。

然后可以使用此信息计算正切点的坐标：

x = 112·cos(41.8) = 83

y = 112·sin(41.8) = 75

四个切点是所有所需用圆圈半径 100 个点 （0，0） 上绘制居中无穷大登录：

![](arcs-images/infinitycoordinates.png "两个圆圈，切线和坐标")

`PaintSurface`中的处理程序[ `ArcInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs)类定位无穷大登录以便 （0，0） 点是否位于页的中心和缩放的屏幕大小的路径：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.LineTo(83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.CounterClockwise, 83, -75);
        path.LineTo(-83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.Clockwise, -83, -75);
        path.Close();

        // Use path.TightBounds for coordinates without control points
        SKRect pathBounds = path.Bounds;

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / pathBounds.Width,
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

该代码使用`Bounds`属性`SKPath`来确定的无穷大正弦值扩展到画布的大小的尺寸：

[![](arcs-images/arcinfinity-small.png "三重的弧线无穷大页面屏幕截图")](arcs-images/arcinfinity-large.png#lightbox "弧线无穷大页面的三个屏幕截图")

则结果看起来有点小，这意味着`Bounds`属性`SKPath`大小超过路径的报告。

在内部，Skia 总数接近于弧使用多个二次贝塞尔曲线。 这些曲线 （如上所示将在下一节中） 包含控制点，它们控制如何绘制曲线，但不是呈现曲线的一部分。 `Bounds`属性包括这些控点。

若要获取更紧密地适应页面，请使用`TightBounds`属性，用于排除的控制点。 下面是在横向模式中运行并使用该程序`TightBounds`属性来获取路径边界：

[![](arcs-images/arcinfinitytightbounds-small.png "三重的紧密边界弧线无穷大页的屏幕截图")](arcs-images/arcinfinitytightbounds-large.png#lightbox "三重的紧密边界弧线无穷大页的屏幕截图")

虽然数学上平滑弧和直线之间的连接，但从弧线到直线的变化可能看起来有点突然。 更好的无穷大登录呈现在下一页中。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
