---
title: 缩放变换
description: 发现 SkiaSharp 缩放变换的缩放到各种大小的对象
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: b89e7472a332ed5cd518a26bc54af59f12b19c2a
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="the-scale-transform"></a>缩放变换

_发现 SkiaSharp 缩放变换的缩放到各种大小的对象_

如你所见中[转换转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)文章，转换变换可以图形对象从一个位置移动到另一个。 与此相反，缩放变换的图形对象的大小更改：

![](scale-images/scaleexample.png "显示为按比例大小高 word")

缩放变换通常还会导致将随着它们所做的更大的图形坐标。

前面你已了解描述的翻译因素的影响的两个转换公式`dx`和`dy`:

x = x + dx

y = y + dy

缩放的因素`sx`和`sy`是乘法而不是累加性：

x = sx ·x

y = sy ·y

Translate 因素的默认值为 0;缩放比例的默认值为 1。

`SKCanvas`类定义了四个`Scale`方法。 第一个[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/)方法对于时所需的相同水平和垂直缩放的情况下考虑因素是：

```csharp
public void Scale (Single s)
```

这称为*各向同性*缩放&mdash;缩放，它相同在两个方向。 各向同性缩放保留对象的长宽比。

第二个[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/)方法允许你指定的水平和垂直缩放的不同值：

```csharp
public void Scale (Single sx, Single sy)
```

这会导致*各向异性*缩放。
第三个[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/SkiaSharp.SKPoint/)方法组合在一次的两个缩放因素`SKPoint`值：

```csharp
public void Scale (SKPoint size)
```

第四个`Scale`将很快所述方法。

**基本缩放**页演示`Scale`方法。 [ **BasicScalePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) XAML 文件包含两个`Slider`元素，可以选择介于 0 和 10 之间的水平和垂直比例因子。 [ **BasicScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs)代码隐藏文件中使用这些值来调用`Scale`显示圆角的矩形用虚线描边和调整大小以适应在左上方的一些文本之前画布角：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] {  7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        canvas.Scale((float)xScaleSlider.Value,
                     (float)yScaleSlider.Value);

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);

        float margin = 10;
        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

你可能想知道： 缩放因素如何影响从返回的值`MeasureText`方法`SKPaint`？ 问题的回答是： 根本不容易。 `Scale` 是一种方法`SKCanvas`。 它不会影响你使用需要做什么`SKPaint`对象，直到你使用该对象来呈现在画布上的内容。

如你所见，所有内容后绘制`Scale`按比例调用增加：

[![](scale-images/basicscale-small.png "三重的基本缩放页的屏幕截图")](scale-images/basicscale-large.png#lightbox "三重的基本缩放页的屏幕截图")

文本，虚线，在这一行中，角和之间的画布的左侧，从上边缘和圆角的矩形的 10 个像素边距舍入短划线的长度的宽度都受到相同的比例因子。

> [!IMPORTANT]
> 通用 Windows 平台无法正确呈现 anisotropicly 缩放后的文本。

各向异性缩放原因变得不同的行的描边宽度与对齐水平和垂直轴。 （这也是表露此页上的第一个图像。）如果不希望的描边宽度的缩放因素会影响，将其设置为 0，它将始终为一个像素宽而不考虑`Scale`设置。

缩放是相对于画布的左上角。 这可能是完全所需的但它可能不是。 假设你想要在画布上放置文本和矩形其他位置，并且你想要缩放相对于其中心。 在这种情况下，你可以使用的第四个版本[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/System.Single/System.Single/)方法，其中包括两个附加参数来指定的缩放中心：

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

`px`和`py`参数定义一个点，它有时称为*缩放 center*但在 SkiaSharp 文档称为*透视点*。 这是不受缩放画布相对于窗口左上角的点。 相对于该中心会进行所有缩放。

[**居中缩放**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs)页显示此工作原理。 `PaintSurface`处理程序是类似于**基本缩放**程序只不过`margin`计算值以使文本水平居中，这意味着，程序最适合在纵向模式下：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);
        float margin = (info.Width - textBounds.Width) / 2;

        float sx = (float)xScaleSlider.Value;
        float sy = (float)yScaleSlider.Value;
        float px = margin + textBounds.Width / 2;
        float py = margin + textBounds.Height / 2;

        canvas.Scale(sx, sy, px, py);

        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

定位圆角矩形的左上角`margin`画布的左侧的像素为单位和`margin`像素。 最后两个自变量`Scale`方法设置为这些值加上的宽度和高度的文本，也是圆角矩形的宽度和高度。 这意味着所有缩放是相对于该矩形的中心：

[![](scale-images/centeredscale-small.png "三重的居中比例页的屏幕截图")](scale-images/centeredscale-large.png#lightbox "三重的居中比例页的屏幕截图")

`Slider`在此程序中的元素具有一系列&ndash;10 到 10。 如你所见，负值垂直扩展 （如在中心屏幕在 Android 上） 将导致对象翻转围绕通过缩放中心的水平轴。 负值水平伸缩 （例如，在 Windows 屏幕右侧） 导致对象翻转围绕通过缩放中心的垂直轴。

此第四个版本的`Scale`方法是实际的快捷方式。 你可能想要了解通过将其原理`Scale`中此代码替换为以下方法：

```csharp
canvas.Translate(-px, -py);
```

这些是透视点坐标的减号。

现在再次运行程序。 你将看到，以便在画布的左上角的中心是移动矩形和文本。 您几乎可以看到它。 滑块，因为现在不在所有缩放程序当然不工作。

现在，将添加基本`Scale`（不带缩放中心中） 调用*之前*，`Translate`调用：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

如果你熟悉此练习中其他图形编程系统，您可能会认为这是错误，但它不是。 Skia 连续转换调用稍有以不同方式处理从您可能已熟悉。

使用连续`Scale`和`Translate`调用，圆角矩形的中心仍然在左上角中，但是可以现在缩放该相对于窗口左上角的画布上，这也是圆角矩形的中心。

现在，在此之前`Scale`调用添加另一个`Translate`居中的值与调用：

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

这将移动缩放后的结果返回到原始位置。 这些三个调用是到等效的：

```csharp
canvas.Scale(sx, sy, px, py);
```

因此就总转换公式而加剧各个转换：

 x = sx ·(x – px) + px

 y = sy ·(y – py) + py

请记住，默认值的`sx`和`sy`均为 1。 很容易确信这些公式不转换透视点 （上一年度中的 px）。 它仍保留在相对于画布的相同位置。

在合并`Translate`和`Scale`调用的顺序非常重要。 如果`Translate`之后`Scale`，按比例因子进行有效地缩放转换因素。 如果`Translate`之前`Scale`，不缩放转换因素。 此过程变成了某种程度上更清晰 (尽管较为复杂的数学) 时引入的转换矩阵的主题。

`SKPath`类定义的只读[ `Bounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.Bounds/)返回的属性`SKRect`在路径中定义的坐标的范围。 例如，当`Bounds`从之前创建的 hendecagram 路径获取属性`Left`和`Top`矩形属性是大约-100、`Right`和`Bottom`属性大约 100，与`Width`和`Height`属性是大约 200。 （大部分的实际值都是较少因为用一个半径为 100 圆圈定义星形的点，但仅顶部点不与水平或垂直轴平行。）

此信息的可用性意味着它应当可以派生缩放和平移因素适用于缩放画布的大小的路径。 [**各向异性缩放**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs)页演示这一操作与 11 星。 *各向异性*缩放意味着，它是不相等沿水平方向和垂直方向，这意味着在星型将不会保留其原始的纵横比。 下面是相关的代码中`PaintSurface`处理程序：

```csharp
SKPath path = HendecagramPage.HendecagramPath;
SKRect pathBounds = path.Bounds;

using (SKPaint fillPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Pink
})
using (SKPaint strokePaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 3,
    StrokeJoin = SKStrokeJoin.Round
})
{
    canvas.Scale(info.Width / pathBounds.Width,
                 info.Height / pathBounds.Height);
    canvas.Translate(-pathBounds.Left, -pathBounds.Top);

    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

`pathBounds`获取此代码的顶部附近和随后用于更高版本的宽度和高度在画布矩形`Scale`调用。 通过呈现时，单独的调用将缩放路径的坐标`DrawPath`调用但星形将居中在画布的右上角。 它需要向下和向左移动。 这是个作业的`Translate`调用。 这两种属性`pathBounds`是大约-100，因此转换因素是约 100。 因为`Translate`后，将调用`Scale`调用，以便它们将在星型的中心移动到画布的中心，这些值有效地缩放比例因子，通过：

[![](scale-images/anisotropicscaling-small.png "三重的各向异性缩放页的屏幕截图")](scale-images/anisotropicscaling-large.png#lightbox "各向异性缩放页的三个屏幕截图")

你可以考虑的另一种方法`Scale`和`Translate`调用是在反向序列中确定的效果：`Translate`调用将转移路径，使之成为完全可见但面向画布的左上角。 `Scale`方法然后使该星级相对于窗口左上角更大。

实际上，它显示在星型是略大于画布。 问题在于描边宽度。 `Bounds`属性`SKPath`指示坐标的尺寸编码在路径中，并且程序使用来扩展它。 当特定描边宽度呈现路径时，呈现的路径大于画布。

若要修复此问题，你需要的补偿。 在此程序中的一个简单的方法是将添加以下语句右侧之前`Scale`调用：

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

这会增加`pathBounds`的所有四个边 1.5 单元的矩形。 仅当舍入描边联接时，这是一个合理的解决方案。 斜接联接可能较长且难以进行计算。

你还可以通过以下方式使用了类似的方法以文本作为**各向异性文本**页说明。 以下是相关的部分`PaintSurface`处理程序[ `AnisotropicTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs)类：

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 0.1f,
    StrokeJoin = SKStrokeJoin.Round
})
{
    SKRect textBounds = new SKRect();
    textPaint.MeasureText("HELLO", ref textBounds);

    // Inflate bounds by the stroke width
    textBounds.Inflate(textPaint.StrokeWidth / 2,
                       textPaint.StrokeWidth / 2);

    canvas.Scale(info.Width / textBounds.Width,
                 info.Height / textBounds.Height);
    canvas.Translate(-textBounds.Left, -textBounds.Top);

    canvas.DrawText("HELLO", 0, 0, textPaint);
}
```

它是类似的逻辑，且文本将扩展到基于文本的边界矩形从返回的页大小`MeasureText`（这是略大于实际文本）：

[![](scale-images/anisotropictext-small.png "三重各向异性测试页的屏幕截图")](scale-images/anisotropictext-large.png#lightbox "各向异性测试页的三个屏幕快照")

如果你需要保留纵横比的图形对象，你将需要使用各向同性缩放。 **各向同性缩放**页演示这一为 11 星。 从概念上讲，在使用各向同性缩放比例页的中心显示图形对象的步骤如下：

- 将转换到的左上角的图形对象的中心。
- 缩放基于除以图形对象维度的水平和垂直页尺寸的最小值的对象。
- 将转换到页的中心的扩展对象的中心。

[ `IsotropicScalingPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs)显示星形前会按相反的顺序执行以下步骤：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPath path = HendecagramArrayPage.HendecagramPath;
    SKRect pathBounds = path.Bounds;

    using (SKPaint fillPaint = new SKPaint())
    {
        fillPaint.Style = SKPaintStyle.Fill;

        float scale = Math.Min(info.Width / pathBounds.Width,
                               info.Height / pathBounds.Height);

        for (int i = 0; i <= 10; i++)
        {
            fillPaint.Color = new SKColor((byte)(255 * (10 - i) / 10),
                                          0,
                                          (byte)(255 * i / 10));
            canvas.Save();
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(scale);
            canvas.Translate(-pathBounds.MidX, -pathBounds.MidY);
            canvas.DrawPath(path, fillPaint);
            canvas.Restore();

            scale *= 0.9f;
        }
    }
}
```

代码十次还将显示在星型，10%和渐进式将颜色更改从红色为蓝色，减少的缩放每次挑选：

[![](scale-images/isotropicscaling-small.png "三重的各向同性缩放页的屏幕截图")](scale-images/isotropicscaling-large.png#lightbox "三倍的各向同性缩放页的屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
