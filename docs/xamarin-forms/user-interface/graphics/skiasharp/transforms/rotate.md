---
title: 旋转变换
description: 浏览效果和动画，可以使用 SkiaSharp 旋转变换
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: e3e9c42fafc9a3d139a90bda6289260c5f3a667b
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="the-rotate-transform"></a>旋转变换

_浏览效果和动画，可以使用 SkiaSharp 旋转变换_

使用旋转变换，SkiaSharp 图形对象可以与水平和垂直轴中断免费的对齐方式的约束：

![](rotate-images/rotateexample.png "围绕中心旋转的文本")

修改绕 SkiaSharp 同时支持的点 （0，0） 的图形对象[ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/)方法和一个[ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/)方法：

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

360 度圆等同于 2π 弧度表示，因此很容易的两个单位之间进行转换。 使用者为准非常方便。 在静态中的所有三角函数[ `Math` ](https://developer.xamarin.com/api/type/System.Math/)类使用的弧度为单位的单位。

提高角度顺时针旋转。 （尽管笛卡尔坐标系统上的旋转逆时针旋转按照约定，但顺时针旋转是一致的向下增加持续的 Y 坐标。）负角度和角度晚于允许 360 度。

旋转转换公式非常复杂，比翻译和小数位数。 为 α 的角度，是转换公式：

x' = x•cos(α) – y•sin(α)   

y` = x•sin(α) + y•cos(α)

**基本旋转**页演示`RotateDegrees`方法。 [ `BasicRotate.xaml.cs` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs)文件显示在页面上居中其基线一些文本，并将其基于旋转`Slider`介于-360 到 360 范围。 以下是相关的部分`PaintSurface`处理程序：

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

因为旋转围绕此程序中设置的大多数角度的画布的左上角的文本旋转不在屏幕中：

[![](rotate-images/basicrotate-small.png "三重的基本旋转页的屏幕截图")](rotate-images/basicrotate-large.png#lightbox "三重的基本旋转页的屏幕截图")

非常频繁，你将需要旋转内容以使用这些版本的指定的透视点中心[ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/System.Single/System.Single/)和[ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/System.Single/System.Single/)方法：

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

**居中旋转**页则类似**基本旋转**只不过的扩展的版本`RotateDegrees`用于用于放置的文本的同一点设置旋转中心的：

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

现在在围绕用于决定文本，这是文本的基线的水平居中的位置的点旋转文本：

[![](rotate-images/centeredrotate-small.png "三重的居中旋转页面的屏幕截图")](rotate-images/centeredrotate-large.png#lightbox "三倍的居中旋转页面的屏幕截图")

与一样的居中新版`Scale`方法中，为中心的版本`RotateDegrees`调用是一种快捷方式：

```csharp
RotateDegrees (degrees, px, py);
```

此表达式等效于以下表达式：

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

你将发现，你有时可以组合`Translate`使用调用`Rotate`调用。 例如，以下是`RotateDegrees`和`DrawText`调用**居中旋转**页上;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

`RotateDegrees`调用相当于两个`Translate`调用和非居中`RotateDegrees`:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

`DrawText`调用来显示文本的特定位置相当于`Translate`跟该位置调用`DrawText`在点 （0，0）：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

两个连续`Translate`调用取消相互出：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

从概念上讲，两个转换的应用顺序相反其在代码中的显示方式。 `DrawText`调用画布的左上角显示的文本。 `RotateDegrees`调用旋转该文本相对于窗口左上角。 则`Translate`调用文本画布的中心移动。

通常，有几种方法组合旋转和平移。 **旋转文本**页创建以下显示：

[![](rotate-images/rotatedtext-small.png "三重的旋转文本页的屏幕截图")](rotate-images/rotatedtext-large.png#lightbox "三倍的旋转文本页的屏幕截图")

下面是`PaintSurface`处理程序[ `RotatedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs)类：

```csharp
static readonly string text = "    ROTATE";
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 72
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float yText = yCenter - textBounds.Height / 2 - textBounds.Top;

        for (int degrees = 0; degrees < 360; degrees += 30)
        {
            canvas.Save();
            canvas.RotateDegrees(degrees, xCenter, yCenter);
            canvas.DrawText(text, xCenter, yText, textPaint);
            canvas.Restore();
        }
    }
}

```

`xCenter`和`yCenter`值表明画布的中心。 `yText`值是稍有偏离。 这表示放置的文本，以使它真正垂直居中的页上所需的 Y 坐标。 `for`循环然后设置在画布的中心上居中的旋转。 在 30 度的增量旋转。 使用绘制文本`yText`值。 单词之前的空白的数量"旋转"中`text`值已根据经验确定使似乎 dodecagon 这些 12 文本的字符串之间的连接。

一种方法来简化此代码是每次循环之后增加 30 度的旋转角度`DrawText`调用。 这消除了对调用的需求`Save`和`Restore`。 请注意，`degrees`主体中不再使用变量`for`块：

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

还有可能要使用的简单形式`RotateDegrees`的开端通过调用循环`Translate`要画布的中心移动所有内容：

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

已修改`yText`计算不再包含`yCenter`。 现在`DrawText`调用中心垂直画布顶部的文本。

由于它们出现在代码中不要从概念上讲应用转换，它通常是可能开始时更具有全局性转换，跟多个本地转换。 这通常是组合旋转和平移的最简单方法。

例如，假设你想要围绕其类似于其轴上轮换世界各地的中心绘制旋转的图形对象。 但你还想要围绕类似于旋转绕太阳世界各地屏幕的中心旋转此对象。

可以通过在画布的左上角中定位对象，然后使用动画围绕该角旋转它来执行此操作。 接下来，将转换水平如绕 radius 的对象。 现在，应用第二个的动画的旋转，沿原点还。 这使得围绕角对象。 现在，将转换到画布的中心。

下面是`PaintSurface`处理程序，它包含这些转换调用按相反的顺序：

```csharp
float revolveDegrees, rotateDegrees;
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Red
    })
    {
        // Translate to center of canvas
        canvas.Translate(info.Width / 2, info.Height / 2);

        // Rotate around center of canvas
        canvas.RotateDegrees(revolveDegrees);

        // Translate horizontally
        float radius = Math.Min(info.Width, info.Height) / 3;
        canvas.Translate(radius, 0);

        // Rotate around center of object
        canvas.RotateDegrees(rotateDegrees);

        // Draw a square
        canvas.DrawRect(new SKRect(-50, -50, 50, 50), fillPaint);
    }
}
```

`revolveDegrees`和`rotateDegrees`字段进行动画处理。 此程序使用不同的动画技术基于 Xamarin.Forms`Animation`类。 (此类描述在[的章 22*具有 Xamarin.Forms 创建移动应用*](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf))`OnAppearing`重写创建两个`Animation`对象与回调方法，然后调用`Commit`对其动画持续时间：

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    new Animation((value) => revolveDegrees = 360 * (float)value).
        Commit(this, "revolveAnimation", length: 10000, repeat: () => true);

    new Animation((value) =>
    {
        rotateDegrees = 360 * (float)value;
        canvasView.InvalidateSurface();
    }).Commit(this, "rotateAnimation", length: 1000, repeat: () => true);
}
```

第一个`Animation`对象进行动画处理`revolveDegrees`从 0 到 360 度时间超过 10 秒。 第二个进行动画处理`rotateDegrees`从 0 到 360 度每 1 第二个以及使图面无效生成另一个调用`PaintSurface`处理程序。 `OnDisappearing`替代取消这些两个动画：

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

**繁琐模拟时钟**（因此称为因为更具吸引力的模拟时钟将在更高版本的文章中所述） 的程序使用旋转，来绘制时钟分钟和 1 小时标记和旋转手中。 程序绘制使用圆点 （0，0） 一个半径为 100 中心基于任意坐标系统的时钟。 它使用转换和缩放来展开和中心页上的该圆圈。

`Translate`和`Scale`调用执行全局应用时钟，因此这种调用以下的初始化的第一`SKPaint`对象：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    using (SKPaint fillPaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.Color = SKColors.Black;
        strokePaint.StrokeCap = SKStrokeCap.Round;

        fillPaint.Style = SKPaintStyle.Fill;
        fillPaint.Color = SKColors.Gray;

        // Transform for 100-radius circle centered at origin
        canvas.Translate(info.Width / 2f, info.Height / 2f);
        canvas.Scale(Math.Min(info.Width / 200f, info.Height / 200f));
        ...
    }
}

```csharp
There are 60 marks of two different sizes that must be drawn in a circle around the clock. The `DrawCircle` call draws that circle at the point (0, –90), which relative to the center of the clock corresponds to 12:00. The `RotateDegrees` call increments the rotation angle by 6 degrees after every tick mark. The `angle` variable is used solely to determine if a large circle or a small circle is drawn:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        // Hour and minute marks
        for (int angle = 0; angle < 360; angle += 6)
        {
            canvas.DrawCircle(0, -90, angle % 30 == 0 ? 4 : 2, fillPaint);
            canvas.RotateDegrees(6);
        }
    ...
    }
}
```

最后，`PaintSurface`处理程序获取当前时间，并针对小时、 分钟和第二个之手计算旋转度。 以便的旋转角度是相对的将在 12:00 位置绘制每个手动：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        DateTime dateTime = DateTime.Now;

        // Hour hand
        strokePaint.StrokeWidth = 20;
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawLine(0, 0, 0, -50, strokePaint);
        canvas.Restore();

        // Minute hand
        strokePaint.StrokeWidth = 10;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawLine(0, 0, 0, -70, strokePaint);
        canvas.Restore();

        // Second hand
        strokePaint.StrokeWidth = 2;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Second);
        canvas.DrawLine(0, 10, 0, -80, strokePaint);
        canvas.Restore();
    }
}
```

而是粗糙手虽然，时钟是当然功能：

[![](rotate-images/uglyanalogclock-small.png "仅使用三重繁琐模拟时钟文本页面的屏幕截图")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
