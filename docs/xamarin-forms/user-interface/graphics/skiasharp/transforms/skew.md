---
title: 时间差转换
description: 请参阅如何倾斜转换可以在 SkiaSharp 创建倾斜图形对象
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: charlespetzold
ms.author: chape
ms.date: 03/20/2017
ms.openlocfilehash: c8913dcb5dbe9664f1186b1acf46f09cb8da74ed
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2018
---
# <a name="the-skew-transform"></a>时间差转换

_请参阅如何倾斜转换可以在 SkiaSharp 创建倾斜图形对象_

在 SkiaSharp，时间差转换倾斜图形对象，如本图像中卷影：

![](skew-images/skewexample.png "下面举例说明倾斜从倾斜卷影文本程序")

偏差将矩形转换为 parallelograms，但偏斜的椭圆仍是一个椭圆。

尽管 Xamarin.Forms 定义平移、 缩放和旋转的属性，但是具有没有对应的属性 Xamarin.Forms 中的偏差。

[ `Skew` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Skew/p/System.Single/System.Single/)方法`SKCanvas`接受扭曲的偏差水平和垂直两个自变量：

```csharp
public void Skew (Single xSkew, Single ySkew)
```

第二个[ `Skew` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Skew/p/SkiaSharp.SKPoint/)方法组合在一次这些自变量`SKPoint`值：

```csharp
public void Skew (SKPoint skew)
```

但是，不太可能，你将使用这些两种方法之一中隔离。

**倾斜试验**页允许您对偏差进行试验的数值 – 10 到 10 之间。 文本字符串在左上角的页上，定位与从两个中获取的偏差值`Slider`元素。 下面是`PaintSurface`中的处理程序[ `SkewExperimentPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs)类：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        canvas.Skew((float)xSkewSlider.Value, (float)ySkewSlider.Value);
        canvas.DrawText(text, 0, -textBounds.Top, textPaint);
    }
}
```

值`xSkew`参数位移适合正值的文本或离开负值的底部。 值`ySkew`向下移动文本的右边，对于正值或负值注册：

[![](skew-images/skewexperiment-small.png "三重的倾斜试验页面屏幕截图")](skew-images/skewexperiment-large.png#lightbox "倾斜试验页面的三个屏幕截图")

如果`xSkew`长度的负值`ySkew`，结果是旋转，但还缩放某种程度上，UWP 显示所示。

转换公式如下所示：

x = x + xSkew ·y

y = ySkew ·x + y

例如，对于一个正`xSkew`值，已转换`x'`值随着`y`会增加。 这是什么因素会导致倾斜。

如果三角形 200 像素宽和等于 100 个像素高位于其左上角的点 （0，0） 位于和呈现使用`xSkew`值为 1.5，以下的平行四边形结果：

![](skew-images/skeweffect.png "一个矩形上的倾斜转换效果")

下边缘的坐标具有`y`值为 100，因此很向右移动 150 像素。

为非零值的`xSkew`或`ySkew`、 只点 （0，0） 保持不变。 该点可被视为倾斜的中心。 如果你需要的倾斜为其他内容的中心 （这通常是这种情况），则没有`Skew`提供，它的方法。 你将需要显式合并`Translate`使用调用`Skew`调用。 到中心再在倾斜`px`和`py`，进行以下调用：

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

复合转换公式是：

x = x + xSkew ·(y – py)

y = ySkew ·(x – px) + y

如果`ySkew`为零，且仅在指定的一个非零值`xSkew`，然后`px`不使用值。 值是不相关的并同样对于`ySkew`和`py`。

你可能会觉得更舒服指定倾斜角度的倾斜，如在此关系图的角度 α 作为：

![](skew-images/skewangleeffect.png "上一个带有倾斜角度的矩形的倾斜转换效果指示")

为 100 像素垂直 150 像素 shift 的比率为在此示例中该角度的正切值 56.3 度。

XAML 文件**倾斜角度试验**页是类似于**倾斜角度**只不过页上`Slider`元素介于 – 90 到 90 度。 [ `SkewAngleExperiment` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs)代码隐藏文件中心页上的文本，并使用`Translate`设置页的中心倾斜的中心。 Short`SkewDegrees`底部的代码的方法将转换角度，以扭曲值：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;

        canvas.Translate(xCenter, yCenter);
        SkewDegrees(canvas, xSkewSlider.Value, ySkewSlider.Value);
        canvas.Translate(-xCenter, -yCenter);
        canvas.DrawText(text, xText, yText, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

随着角度接近正整数或负 90 度，正切值接近无穷大，但最多大约 80 度左右的角度可用：

[![](skew-images/skewangleexperiment-small.png "三重的倾斜角度试验页面屏幕截图")](skew-images/skewangleexperiment-large.png#lightbox "三倍的倾斜角度试验页面屏幕截图")

小的负水平扭曲可作为模拟斜体或倾斜文本**倾斜文本**页说明。 [ `ObliqueTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs)类演示如何执行：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Maroon,
        TextAlign = SKTextAlign.Center,
        TextSize = info.Width / 8       // empirically determined
    })
    {
        canvas.Translate(info.Width / 2, info.Height / 2);
        SkewDegrees(canvas, -20, 0);
        canvas.DrawText(Title, 0, 0, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

`TextAlign`属性`SKPaint`设置为`Center`。 而无需任何转换，`DrawText`调用使用的坐标 （0，0） 将在左上角位置与基线的水平居中的文本。 `SkewDegrees`偏差文本水平相对于基线的 20 度。 `Translate`调用将水平居中的文本的基线移到画布的中心：

[![](skew-images/obliquetext-small.png "三重的倾斜文本页的屏幕截图")](skew-images/obliquetext-large.png#lightbox "三重的倾斜文本页的屏幕截图")

**倾斜卷影文本**页演示如何使用 45 度倾斜和垂直缩放的组合，可使倾斜离开文本的文本阴影。 以下是相关的部分`PaintSurface`处理程序：

```csharp
using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = info.Width / 6;   // empirically determined

    // Common to shadow and text
    string text = "Shadow";
    float xText = 20;
    float yText = info.Height / 2;

    // Shadow
    textPaint.Color = SKColors.LightGray;
    canvas.Save();
    canvas.Translate(xText, yText);
    canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
    canvas.Scale(1, 3);
    canvas.Translate(-xText, -yText);
    canvas.DrawText(text, xText, yText, textPaint);
    canvas.Restore();

    // Text
    textPaint.Color = SKColors.Blue;
    canvas.DrawText(text, xText, yText, textPaint);
}
```

阴影，则显示第一个，然后选择文本：

[![](skew-images/skewshadowtext1-small.png "三重的倾斜卷影文本页的屏幕截图")](skew-images/skewshadowtext1-large.png#lightbox "三重的倾斜卷影文本页的屏幕截图")

垂直坐标传递给`DrawText`方法指示的位置相对于基线的文本。 这就是用于的倾斜中心相同的垂直坐标。 如果文本字符串包含下行字母，则此方法将无法工作。 例如，替代"古怪"此处"阴影"以及该单词的结果：

[![](skew-images/skewshadowtext2-small.png "三重的下行字母与替换单词倾斜卷影文本页的屏幕截图")](skew-images/skewshadowtext2-large.png#lightbox "三重的下行字母与替换单词倾斜卷影文本页的屏幕截图")

卷影和文本仍在基线中，对齐，但效果只是看起来有错误。 若要修复此错误，你需要获取文本边界：

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

`Translate`调用需要进行调整的下行字母的高度：

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

现在阴影扩展这些下行字母的底部：

[![](skew-images/skewshadowtext3-small.png "三重的下行字母的调整倾斜卷影文本页的屏幕截图")](skew-images/skewshadowtext3-large.png#lightbox "三重的下行字母的调整倾斜卷影文本页的屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
