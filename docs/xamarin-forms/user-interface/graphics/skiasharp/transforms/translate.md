---
title: "转换变换"
description: "了解如何使用转换变换移动 SkiaSharp 图形"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: cac2479af2778af6043a85583f9d7b518748d7da
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="the-translate-transform"></a>转换变换

_了解如何使用转换变换移动 SkiaSharp 图形_

最简单的 SkiaSharp 中的转换类型是*转换*或*转换*转换。 此转换会转而在水平和垂直方向的图形对象。 在某种意义上，转换是最不必要的转换，因为你通常可以通过以下方式实现相同效果只需更改你绘制函数中使用的坐标。 当呈现路径，但是，所有坐标都封装在路径中，因此很容易得多，在应用转换变换要位移的整条路径。

转换是也适用于动画，以及为简单文本效果：

![](translate-images/translateexample.png "雕刻，并使用转换浮雕的文本阴影")

[ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/System.Single/System.Single/)中的方法`SKCanvas`具有导致随后绘制的图形对象在水平和垂直要移动的两个参数：

```csharp
public void Translate (Single dx, Single dy)
```

这些自变量可以是负数。 第二个[ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/SkiaSharp.SKPoint/)方法组合在一次的这两种转换值`SKPoint`值：

```csharp
public void Translate (SKPoint point)
```

**累积转换**页[ **SkiaSharpForms** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)示例程序演示了多个调用的`Translate`方法可以累计。 [ `AccumulatedTranslate` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs)类显示的相同的矩形的 20 个版本，每个偏移量从以前的矩形正好这样它们对角线将延长。 下面是`PaintSurface`事件处理程序：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Color = SKColors.Black;
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = 3;

        int rectangleCount = 20;
        SKRect rect = new SKRect(0, 0, 250, 250);
        float xTranslate = (info.Width - rect.Width) / (rectangleCount - 1);
        float yTranslate = (info.Height - rect.Height) / (rectangleCount - 1);

        for (int i = 0; i < rectangleCount; i++)
        {
            canvas.DrawRect(rect, strokePaint);
            canvas.Translate(xTranslate, yTranslate);
        }
    }
}
```

连续矩形渗透沿页：

[![](translate-images/accumulatedtranslate-small.png "三重的累积转换页的屏幕截图")](translate-images/accumulatedtranslate-large.png#lightbox "三重的累积转换页的屏幕截图")

如果累积的转换因素是`dx`和`dy`，并且在绘制函数中指定的点是 (`x`， `y`)，然后在点呈现图形对象 (`x'`， `y'`)，其中：

x = x + dx

y = y + dy

这些被称为*转换公式*的转换。 默认值`dx`和`dy`新`SKCanvas`均为 0。

通常用于转换变换阴影效果和类似技术，作为**翻译文本效果**页说明。 以下是相关的部分`PaintSurface`中的处理程序[ `TranslateTextEffectsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs)类：

```csharp
float textSize = 150;

using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = textSize;
    textPaint.FakeBoldText = true;

    float x = 10;
    float y = textSize;

    // Shadow
    canvas.Translate(10, 10);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("SHADOW", x, y, textPaint);
    canvas.Translate(-10, -10);
    textPaint.Color = SKColors.Pink;
    canvas.DrawText("SHADOW", x, y, textPaint);

    y += 2 * textSize;

    // Engrave
    canvas.Translate(-5, -5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("ENGRAVE", x, y, textPaint);
    canvas.ResetMatrix();
    textPaint.Color = SKColors.White;
    canvas.DrawText("ENGRAVE", x, y, textPaint);

    y += 2 * textSize;

    // Emboss
    canvas.Save();
    canvas.Translate(5, 5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("EMBOSS", x, y, textPaint);
    canvas.Restore();
    textPaint.Color = SKColors.White;
    canvas.DrawText("EMBOSS", x, y, textPaint);
}
```

中的三个示例中，每个`Translate`显示要偏离通过给定的位置的文本将为调用`x`和`y`变量。 然后在另一种颜色，而不转换影响再次显示文本：

[![](translate-images/translatetexteffects-small.png "三重的翻译文本效果页面屏幕截图")](translate-images/translatetexteffects-large.png#lightbox "翻译文本效果页面的三个屏幕截图")

每三个示例显示了不同的绝对值`Translate`调用：

第一个示例只需调用`Translate`再次，但具有负值。 因为`Translate`调用可以累计，这一序列的调用只需将总转换还原为默认值为零。

第二个示例调用[ `ResetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ResetMatrix()/)。 这将导致要返回到其默认状态的所有转换。

第三个示例将保存的状态的`SKCanvas`对象通过调用[ `Save` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Save()/) ，然后还原通过调用状态[ `Restore` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Restore/)。 这是用途最广泛的方法来操作有关的绘制操作一系列的转换。 这些`Save`和`Restore`调用例如的堆栈的函数： 你可以调用`Save`多个时间，，然后调用`Restore`在反向序列，以返回到之前的状态。 `Save`方法返回一个整数，并可以将传递到该整数[ `RestoreToCount` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RestoreToCount/)有效地调用`Restore`多次。 [ `SaveCount` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.SaveCount/)属性返回当前保存在堆栈上的状态数。

但是，你无需担心从一次调用的执行的转换`PaintSurface`到下一个处理程序。 每次新调用`PaintSurface`提供新`SKCanvas`与默认转换的对象。

另一个常见用途`Translate`转换是呈现的视觉对象已最初创建使用十分方便的绘图区域的坐标。 例如，你可能想要使用的中心点 （0，0） 在指定坐标是模拟时钟。 你可以然后使用转换来显示所需的位置。 此进行了演示 [**Hendecagram 数组**] 页。 [ `HendecagramArrayPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/HendecagramPage.cs)类首先创建`SKPath`11 星的对象。 `HendecagramPath`对象定义为公共、 静态的且只读的因此，它可以从其他演示程序访问。 在静态构造函数中创建它：

```csharp
public class HendecagramArrayPage : ContentPage
{
    ...
    public static readonly SKPath HendecagramPath;

    static HendecagramArrayPage()
    {
        // Create 11-pointed star
        HendecagramPath = new SKPath();
        for (int i = 0; i < 11; i++)
        {
            double angle = 5 * i * 2 * Math.PI / 11;
            SKPoint pt = new SKPoint(100 * (float)Math.Sin(angle),
                                    -100 * (float)Math.Cos(angle));
            if (i == 0)
            {
                HendecagramPath.MoveTo(pt);
            }
            else
            {
                HendecagramPath.LineTo(pt);
            }
        }
        HendecagramPath.Close();
    }
}
```

如果在星型的中心点 （0，0），在星型的所有点都是圆周围的该点。 每个点都加 5/11ths 为 360 度的角度的正弦值和余弦值的组合。 (也是可以通过增加 2 的角度创建 11 星/11，3/11ths 或 4/11 的圆。)该圆的半径被设置为 100。

中心如果而无需任何转换呈现，则此路径，将放置在左上角处`SKCanvas`，并且仅有四分之一的它将可见。 `PaintSurface`处理程序`HendecagramPage`改用`Translate`以磁贴在画布上添加多个副本在星型，每个随机变色：

```csharp
public class HendecagramArrayPage : ContentPage
{
    Random random = new Random();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            for (int x = 100; x < info.Width + 100; x += 200)
                for (int y = 100; y < info.Height + 100; y += 200)
                {
                    // Set random color
                    byte[] bytes = new byte[3];
                    random.NextBytes(bytes);
                    paint.Color = new SKColor(bytes[0], bytes[1], bytes[2]);

                    // Display the hendecagram
                    canvas.Save();
                    canvas.Translate(x, y);
                    canvas.DrawPath(HendecagramPath, paint);
                    canvas.Restore();
                }
        }
    }
}

```

下面是结果：

[![](translate-images/hendecagramarray-small.png "三重的 Hendecagram 数组页面屏幕截图")](translate-images/hendecagramarray-large.png#lightbox "Hendecagram 数组页面的三个屏幕截图")

动画通常涉及到转换。 **Hendecagram 动画**页移动 11 星的圆圈，圆圈中。 [ `HendecagramAnimationPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs)类开头某些字段并且重写的`OnAppearing`和`OnDisappearing`启动和停止 Xamarin.Forms 计时器方法：

```csharp
public class HendecagramAnimationPage : ContentPage
{
    const double cycleTime = 5000;      // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float angle;

    public HendecagramAnimationPage()
    {
        Title = "Hedecagram Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            angle = (float)(360 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

`angle`字段动画处理的从 0 到 360 度每隔 5 秒。 `PaintSurface`处理程序使用`angle`两种方式的属性： 指定在颜色色调的`SKColor.FromHsl`方法，和的自变量为`Math.Sin`和`Math.Cos`方法来控制在星型的位置：

```csharp
public class HendecagramAnimationPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.Translate(info.Width / 2, info.Height / 2);
        float radius = (float)Math.Min(info.Width, info.Height) / 2 - 100;

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColor.FromHsl(angle, 100, 50);

            float x = radius * (float)Math.Sin(Math.PI * angle / 180);
            float y = -radius * (float)Math.Cos(Math.PI * angle / 180);
            canvas.Translate(x, y);
            canvas.DrawPath(HendecagramPage.HendecagramPath, paint);
        }
    }
}
```

`PaintSurface`处理程序调用`Translate`方法两次，首先要转换为画布的中心，然后转换为圆的中心周长 （0，0）。 圆的半径设置可以同时仍保护范围内的页上的星形尽可能大：

[![](translate-images/hendecagramanimation-small.png "三重的 Hendecagram 动画页面屏幕截图")](translate-images/hendecagramanimation-large.png#lightbox "Hendecagram 动画页面的三个屏幕截图")

请注意在星型维护相同的方向，如围绕页的中心。 它不能将根本旋转。 这是旋转变换的作业。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
