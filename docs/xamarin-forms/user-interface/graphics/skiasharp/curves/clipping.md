---
title: 使用路径和区域的剪辑
description: 对特定区域，并创建区域使用剪辑图形路径
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: charlespetzold
ms.author: chape
ms.date: 06/16/2017
ms.openlocfilehash: 051ceec148a569d00048a661e6ba8dc3ce96fc81
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="clipping-with-paths-and-regions"></a>使用路径和区域的剪辑

_对特定区域，并创建区域使用剪辑图形路径_

另外，有时需要限制对特定区域的图形呈现。 这称为*剪辑*。 对于某些特殊效果，例如通过锁眼看到 monkey 此映像，可以使用剪辑：

![](clipping-images/clippingsample.png "通过锁眼 monkey")

*剪辑区域*是在其中呈现图形屏幕的区域。 不呈现的剪辑区域之外显示的任何内容。 通常定义的剪辑区域[ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/)对象但你可以或者定义剪辑区域使用[ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/)对象。 这两种类型的对象首先似乎相关，因为你可以从路径中创建一个区域。 但是，无法创建区域上的路径和它们仍然是非常不同内部： 路径由组成的直线和曲线，一系列，而一个区域定义的一系列水平扫描线。

上面的图像由**通过锁眼 Monkey**页。 [ `MonkeyThroughKeyholePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs)类定义使用 SVG 数据的路径，并使用此构造函数从程序资源加载位图：

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    SKBitmap bitmap;
    SKPath keyholePath = SKPath.ParseSvgPathData(
        "M 300 130 L 250 350 L 450 350 L 400 130 A 70 70 0 1 0 300 130 Z");

    public MonkeyThroughKeyholePage()
    {
        Title = "Monkey through Keyhole";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }
    }
    ...
}
```

尽管`keyholePath`对象描述的锁眼轮廓，坐标是完全任意的并且反映什么时方便路径数据已制定出。 为此，`PaintSurface`处理程序获取的此路径和调用边界`Translate`和`Scale`来将路径移到屏幕的中心并使它几乎高度都是屏幕：


```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transform to center and enlarge clip path to window height
        SKRect bounds;
        keyholePath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.98f * info.Height / bounds.Height);
        canvas.Translate(-bounds.MidX, -bounds.MidY);

        // Set the clip path
        canvas.ClipPath(keyholePath);

        // Reset transforms
        canvas.ResetMatrix();

        // Display monkey to fill height of window but maintain aspect ratio
        canvas.DrawBitmap(bitmap,
            new SKRect((info.Width - info.Height) / 2, 0,
                       (info.Width + info.Height) / 2, info.Height));
    }
}
```

但不是呈现路径。 相反，在转换中，以下路径用于设置此语句的剪辑区域：

```csharp
canvas.ClipPath(keyholePath);
```

`PaintSurface`处理程序会通过调用转换将重置`ResetMatrix`和绘制位图，以将扩展到屏幕的最大高度。 此代码假定将位图是方形，这是此特定的位图。 仅在由剪切路径定义的区域中呈现位图：

[![](clipping-images/monkeythroughkeyhole-small.png "通过锁眼页 Monkey 的三个屏幕截图")](clipping-images/monkeythroughkeyhole-large.png#lightbox "通过锁眼页 Monkey 的三个屏幕截图")

剪切路径是进行转换生效时`ClipPath`方法被调用，并且不到转换生效时图形 （如位图） 将显示一个对象。 剪切路径是使用保存的画布状态的一部分`Save`方法和还原与`Restore`方法。

## <a name="combining-clipping-paths"></a>组合剪切路径

严格地说，剪辑区域未"设置"`ClipPath`方法。 相反，它结合了现有的剪切路径开始为屏幕大小等于一个矩形。 你可以获取的剪辑区域使用的矩形边界[ `ClipBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipBounds/)属性或[ `ClipDeviceBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipDeviceBounds/)属性。 `ClipBounds`属性返回`SKRect`值，该值反映任何转换，可能会生效。 `ClipDeviceBounds`属性返回`RectI`值。 这是一个具有整数维度的矩形，描述实际像素维度中的剪辑区域。

任何调用到`ClipPath`减少通过组合具有新区域的剪辑区域的剪辑区域。 完整语法[ `ClipPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipPath/p/SkiaSharp.SKPath/SkiaSharp.SKClipOperation/System.Boolean/)方法是：

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

此外，还有[ `ClipRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRect/p/SkiaSharp.SKRect/SkiaSharp.SKClipOperation/System.Boolean/)结合矩形的剪辑区域的方法：

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

默认情况下，生成的剪辑区域是现有的剪辑区域的交集和`SKPath`或`SKRect`中指定`ClipPath`或`ClipRect`方法。 此进行了演示**四个圆圈相交剪辑**页。 `PaintSurface`中的处理程序[ `FourCircleInteresectClipPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs)类重复使用相同`SKPath`对象以创建四个重叠圆圈，其中每个减少了通过连续调用的剪辑区域`ClipPath`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float size = Math.Min(info.Width, info.Height);
    float radius = 0.4f * size;
    float offset = size / 2 - radius;

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    using (SKPath path = new SKPath())
    {
        path.AddCircle(-offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(-offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColors.Blue;
            canvas.DrawPaint(paint);
        }
    }
}
```

剩下的是这些四个圆圈的交集：

[![](clipping-images//fourcircleintersectclip-small.png "三重的四个圆相交剪辑页的屏幕截图")](clipping-images/fourcircleintersectclip-large.png#lightbox "三倍的四个圆相交剪辑页面屏幕截图")

[ `SKClipOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKClipOperation/)枚举具有只有两个成员：

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Difference/) 从现有的剪辑区域中删除指定的路径或矩形

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Intersect/) 与指定的路径或使用现有的剪辑区域的矩形

如果替换包含四个`SKClipOperation.Intersect`中的自变量`FourCircleIntersectClipPage`类，该类具有`SKClipOperation.Difference`，你将看到以下：

[![](clipping-images//fourcircledifferenceclip-small.png "三重的差值运算的四个圆相交剪辑页的屏幕截图")](clipping-images/fourcircledifferenceclip-large.png#lightbox "三重的差值运算的四个圆相交剪辑页的屏幕截图")

已从剪辑区域删除四个重叠圆圈。

**剪辑操作**页演示使用刚刚圆圈一对这两个操作之间的差异。 在左侧的第一个圆圈将添加到与默认剪辑操作的剪辑区域`Intersect`，而第二个圆右侧将添加到剪辑区域与剪辑操作由文本标签指示：

[![](clipping-images//clipoperations-small.png "三重的剪辑操作页面的屏幕截图")](clipping-images/clipoperations-large.png#lightbox "三倍的剪辑操作页面的屏幕截图")

[ `ClipOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs)类定义了两个`SKPaint`对象，以作为字段，，然后将屏幕分成两个矩形区域。 这些区域是电话是否处于纵向或横向朝模式而异。 `DisplayClipOp`类然后显示的文本和调用`ClipPath`的两个圆路径来演示每个剪辑操作：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;

    foreach (SKClipOperation clipOp in Enum.GetValues(typeof(SKClipOperation)))
    {
        // Portrait mode
        if (info.Height > info.Width)
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width, y + info.Height / 2), clipOp);
            y += info.Height / 2;
        }
        // Landscape mode
        else
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width / 2, y + info.Height), clipOp);
            x += info.Width / 2;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKClipOperation clipOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(clipOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    canvas.Save();

    using (SKPath path1 = new SKPath())
    {
        path1.AddCircle(xCenter - radius / 2, yCenter, radius);
        canvas.ClipPath(path1);

        using (SKPath path2 = new SKPath())
        {
            path2.AddCircle(xCenter + radius / 2, yCenter, radius);
            canvas.ClipPath(path2, clipOp);

            canvas.DrawPaint(fillPaint);
        }
    }

    canvas.Restore();
}
```

调用`DrawPaint`通常会导致要使用的填充整个画布`SKPaint`对象，但在此情况下，该方法仅绘制剪辑区域内。

## <a name="exploring-regions"></a>浏览区域

如果您已了解的 API 文档`SKCanvas`，你可能已经注意到的重载`ClipPath`和`ClipRect`而是类似于上面所述的方法的方法具有一个名为参数[ `SKRegionOperation`](https://developer.xamarin.com/api/type/SkiaSharp.SKRegionOperation/)而非`SKClipOperation`。 `SKRegionOperation` 具有六个成员，提供某种程度上更灵活地合并到窗体剪辑区域的路径：

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Difference/)

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Intersect/)

- [`Union`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Union/)

- [`XOR`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.XOR/)

- [`ReverseDifference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.ReverseDifference/)

- [`Replace`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Replace/)

但是的重载`ClipPath`和`ClipRect`与`SKRegionOperation`参数已过时，且无法使用它们。

你仍然可以使用`SKRegionOperation`枚举，但它需要定义方面的剪辑区域[ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/)对象。

一个新创建`SKRegion`对象描述的空白区域。 在对象上的第一次调用通常是[ `SetRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetRect/p/SkiaSharp.SKRectI/)以便区域描述的矩形区域。 参数`SetRect`是`SKRectI`值&mdash;的矩形值与整数属性。 然后，你可以调用[ `SetPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetPath/p/SkiaSharp.SKPath/SkiaSharp.SKRegion/)与`SKPath`对象。 这将创建的路径，内部相同但剪辑到的初始的矩形区域的区域。

`SKRegionOperation`枚举仅派上用场时调用的一个[ `Op` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.Op/p/SkiaSharp.SKRegion/SkiaSharp.SKRegionOperation/)方法重载，例如这个：

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

正在进行的区域`Op`调用上的结合了多个作为基于参数指定的区域`SKRegionOperation`成员。 当您最后获取区域适合剪辑时，你可以设置为画布使用的剪辑区域[ `ClipRegion` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRegion/p/SkiaSharp.SKRegion/SkiaSharp.SKClipOperation/)方法`SKCanvas`:

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

下面的屏幕截图显示六个区域操作所基于的剪辑区域。 左的 circle 是区域的`Op`调用方法和右圆圈是传递到的区域`Op`方法：

[![](clipping-images//regionoperations-small.png "三重的区域操作页面的屏幕截图")](clipping-images/regionoperations-large.png#lightbox "三倍的区域操作页面的屏幕截图")

这些所有可能的匹配项的组合这些两个圆？ 考虑为它们的本身中所示的三个组件组合生成的图像`Difference`， `Intersect`，和`ReverseDifference`操作。 第三个次幂，两个或八个，组合的总数目。 缺少两个是原始的区域 (这将导致从不调用`Op`根本) 和完全空白区域。

将更难以用于剪辑因为你需要首先从该路径中，创建一个路径，和区域中，然后合并多个区域的区域。 整体结构**区域操作**页是非常类似于**剪辑操作**但[ `RegionOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs)类将屏幕分为六个区域和显示要用于此作业的区域所需的额外工作：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;
    float width = info.Height > info.Width ? info.Width / 2 : info.Width / 3;
    float height = info.Height > info.Width ? info.Height / 3 : info.Height / 2;

    foreach (SKRegionOperation regionOp in Enum.GetValues(typeof(SKRegionOperation)))
    {
        DisplayClipOp(canvas, new SKRect(x, y, x + width, y + height), regionOp);

        if ((x += width) >= info.Width)
        {
            x = 0;
            y += height;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKRegionOperation regionOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(regionOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    SKRectI recti = new SKRectI((int)rect.Left, (int)rect.Top,
                                (int)rect.Right, (int)rect.Bottom);

    using (SKRegion wholeRectRegion = new SKRegion())
    {
        wholeRectRegion.SetRect(recti);

        using (SKRegion region1 = new SKRegion(wholeRectRegion))
        using (SKRegion region2 = new SKRegion(wholeRectRegion))
        {
            using (SKPath path1 = new SKPath())
            {
                path1.AddCircle(xCenter - radius / 2, yCenter, radius);
                region1.SetPath(path1);
            }

            using (SKPath path2 = new SKPath())
            {
                path2.AddCircle(xCenter + radius / 2, yCenter, radius);
                region2.SetPath(path2);
            }

            region1.Op(region2, regionOp);

            canvas.Save();
            canvas.ClipRegion(region1);
            canvas.DrawPaint(fillPaint);
            canvas.Restore();
        }
    }
}
```

下面是很大区别`ClipPath`方法和`ClipRegion`方法：

> [!IMPORTANT]
> 与不同`ClipPath`方法，`ClipRegion`方法不受转换。

若要了解这种差异的基本原理，最好先了解哪些区域是。 如果你已考虑如何剪辑操作或区域操作可能会实现内部，它可能看起来非常复杂。 正在合并多个可能非常复杂的路径，并产生的路径的轮廓可能是算法麻烦。

但如果每个路径都会减少到水平扫描行，如老式孤立管电视机一系列显著简化此作业。 每个扫描行是只带有一个起点和终点的水平线。 例如，有一个半径为 10 的圆圈可以分解为 20 水平扫描行，其中每个开始的圆的左侧部分、 结尾的右侧部分。 组合两个圆与任何区域操作成为非常简单，因为它是只需检查每个对的相应扫描行的开始和结束坐标。

这是什么区域是： 定义一个区域的一系列水平扫描线。

但是，当一个区域都会减少到扫描一系列行，这些扫描线基于特定像素维度。 严格地说，区域不是矢量图形对象。 它是更接近于比到的路径的压缩单色位图显示时的性质。 因此，区域不能缩放或旋转而不会丢失保真度，并因此它们不转换用于剪辑区域时。

但是，可以将转换应用到用于绘制的区域。 **区域绘制**程序生动演示区域的内部性质。 [ `RegionPaintPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs)类创建`SKRegion`对象基于`SKPath`的 10 单元 radius 圆。 转换然后展开该圆圈，以适应页面：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int radius = 10;

    // Create circular path
    using (SKPath circlePath = new SKPath())
    {
        circlePath.AddCircle(0, 0, radius);

        // Create circular region
        using (SKRegion circleRegion = new SKRegion())
        {
            circleRegion.SetRect(new SKRectI(-radius, -radius, radius, radius));
            circleRegion.SetPath(circlePath);

            // Set transform to move it to center and scale up
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(Math.Min(info.Width / 2, info.Height / 2) / radius);

            // Fill region
            using (SKPaint fillPaint = new SKPaint())
            {
                fillPaint.Style = SKPaintStyle.Fill;
                fillPaint.Color = SKColors.Orange;

                canvas.DrawRegion(circleRegion, fillPaint);
            }

            // Stroke path for comparison
            using (SKPaint strokePaint = new SKPaint())
            {
                strokePaint.Style = SKPaintStyle.Stroke;
                strokePaint.Color = SKColors.Blue;
                strokePaint.StrokeWidth = 0.1f;

                canvas.DrawPath(circlePath, strokePaint);
            }
        }
    }
}
```

`DrawRegion`调用填充橙色，区域时`DrawPath`调用中比较的蓝色描边的原始路径：

[![](clipping-images//regionpaint-small.png "三重的区域绘制页的屏幕截图")](clipping-images/regionpaint-large.png#lightbox "区域绘制页面的三个屏幕截图")

区域是离散的坐标的一系列操作。

如果你不需要使用转换的剪辑区域与，可用于区域剪辑，作为**四个-叶与苜蓿**页说明。 [ `FourLeafCloverPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs)类构造从四个循环区域的复合区域、 为剪辑区域中，设置该复合区域，然后绘制一系列 360 直线从页的中心发出的：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float xCenter = info.Width / 2;
    float yCenter = info.Height / 2;
    float radius = 0.24f * Math.Min(info.Width, info.Height);

    using (SKRegion wholeScreenRegion = new SKRegion())
    {
        wholeScreenRegion.SetRect(new SKRectI(0, 0, info.Width, info.Height));

        using (SKRegion leftRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion rightRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion topRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion bottomRegion = new SKRegion(wholeScreenRegion))
        {
            using (SKPath circlePath = new SKPath())
            {
                // Make basic circle path
                circlePath.AddCircle(xCenter, yCenter, radius);

                // Left leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, 0));
                leftRegion.SetPath(circlePath);

                // Right leaf
                circlePath.Transform(SKMatrix.MakeTranslation(2 * radius, 0));
                rightRegion.SetPath(circlePath);

                // Make union of right with left
                leftRegion.Op(rightRegion, SKRegionOperation.Union);

                // Top leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, -radius));
                topRegion.SetPath(circlePath);

                // Combine with bottom leaf
                circlePath.Transform(SKMatrix.MakeTranslation(0, 2 * radius));
                bottomRegion.SetPath(circlePath);

                // Make union of top with bottom
                bottomRegion.Op(topRegion, SKRegionOperation.Union);

                // Exclusive-OR left and right with top and bottom
                leftRegion.Op(bottomRegion, SKRegionOperation.XOR);

                // Set that as clip region
                canvas.ClipRegion(leftRegion);

                // Set transform for drawing lines from center
                canvas.Translate(xCenter, yCenter);

                // Draw 360 lines
                for (double angle = 0; angle < 360; angle++)
                {
                    float x = 2 * radius * (float)Math.Cos(Math.PI * angle / 180);
                    float y = 2 * radius * (float)Math.Sin(Math.PI * angle / 180);

                    using (SKPaint strokePaint = new SKPaint())
                    {
                        strokePaint.Color = SKColors.Green;
                        strokePaint.StrokeWidth = 2;

                        canvas.DrawLine(0, 0, x, y, strokePaint);
                    }
                }
            }
        }
    }
}
```

它实际上看上去不像四个-叶与苜蓿，但它是不可能很难会不经剪辑呈现的图像：

[![](clipping-images//fourleafclover-small.png "三重的四个-叶与苜蓿页面屏幕截图")](clipping-images/fourleafclover-large.png#lightbox "三倍的四个-叶与苜蓿页面屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
