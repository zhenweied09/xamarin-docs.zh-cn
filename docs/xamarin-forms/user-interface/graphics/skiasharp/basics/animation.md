---
title: 基本动画
description: 发现如何进行动画处理 SkiaSharp 图形
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 7435807e77a9a79d7fc3821675c1d959a16caa8f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="basic-animation"></a>基本动画

_发现如何进行动画处理 SkiaSharp 图形_

您可以动态 SkiaSharp Xamarin.Forms 中的图形显示，从而`PaintSurface`非常频繁地调用方法每次绘制图形略有不同。 此处是一个动画更高版本中从中心看似展开的同心圆与本文所示：

![](animation-images/animationexample.png "从中心看似展开的几个同心圆")

**Pulsating 椭圆**页面[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程序动画处理的椭圆的两个轴，以便它似乎 pulsating，并甚至可以控制此 pulsation 率：


[ **PulsatingEllipsePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml)文件实例化 Xamarin.Forms`Slider`和`Label`以显示将滑块的当前值。 这是一种常见的方法来集成`SKCanvasView`与其他 Xamarin.Forms 视图：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.PulsatingEllipsePage"
             Title="Pulsating Ellipse">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Slider x:Name="slider"
                Grid.Row="0"
                Maximum="10"
                Minimum="0.1"
                Value="5"
                Margin="20, 0" />

        <Label Grid.Row="1"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Cycle time = {0:F1} seconds'}"
               HorizontalTextAlignment="Center" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

代码隐藏文件实例化`Stopwatch`对象作为高精度时钟。 `OnAppearing`重写集`pageIsActive`字段`true`并调用一个名为方法`AnimationLoop`。 `OnDisappearing`替代设置`pageIsActive`字段`false`:

```csharp
Stopwatch stopwatch = new Stopwatch();
bool pageIsActive;
float scale;            // ranges from 0 to 1 to 0

public PulsatingEllipsePage()
{
    InitializeComponent();
}

protected override void OnAppearing()
{
    base.OnAppearing();
    pageIsActive = true;
    AnimationLoop();
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    pageIsActive = false;
}
```

`AnimationLoop`方法启动`Stopwatch`，然后时循环`pageIsActive`是`true`。 这是实质上是"无限循环"页处于活动状态，但它不会导致程序挂起，因为循环到结束通过调用`Task.Delay`与`await`运算符，这使程序函数的其他部分。 自变量`Task.Delay`后，即可在 1/30 秒后完成。 这定义动画的帧速率。

```csharp
async Task AnimationLoop()
{
    stopwatch.Start();

    while (pageIsActive)
    {
        double cycleTime = slider.Value;
        double t = stopwatch.Elapsed.TotalSeconds % cycleTime / cycleTime;
        scale = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;
        canvasView.InvalidateSurface();
        await Task.Delay(TimeSpan.FromSeconds(1.0 / 30));
    }

    stopwatch.Stop();
}

```

`while`循环开始通过获取周期时间与`Slider`。 这是以秒为单位，例如，5 时间。 第二个语句计算值为`t`为*时间*。 有关`cycleTime`为 5，`t`增加从 0 到 1 每隔 5 秒。 自变量`Math.Sin`函数中的第二个语句范围从 0 到 2π 每隔 5 秒。 `Math.Sin`函数将返回值从 0 到 1 后为 0，然后执行到&ndash;1 和 0 每隔 5 秒，但其速度更慢更改值时附近 1 或为-1 的值。 值 1 添加的值始终为正数，从而然后它除以 2，因此值范围为 ½ 到 ½ 为介于 0 到 ½，但速度较慢的值时大约 1 和 0 到 1。 这存储在`scale`字段中，与`SKCanvasView`失效。

`PaintSurface`方法使用此`scale`要计算的椭圆的两个轴值：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float maxRadius = 0.75f * Math.Min(info.Width, info.Height) / 2;
    float minRadius = 0.25f * maxRadius;

    float xRadius = minRadius * scale + maxRadius * (1 - scale);
    float yRadius = maxRadius * scale + minRadius * (1 - scale);

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.Color = SKColors.Blue;
        paint.StrokeWidth = 50;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);

        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.SkyBlue;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
    }
}
```

该方法计算基于的显示区域中，大小的最大半径和最小半径基于最大的半径。 `scale`值进行动画处理介于 0 和 1 之间，并返回到 0，因此该方法使用该值来计算`xRadius`和`yRadius`的范围之间`minRadius`和`maxRadius`。 这些值用于绘制和填充椭圆：

[![](animation-images/pulsatingellipse-small.png "三重的脉动椭圆页面屏幕截图")](animation-images/pulsatingellipse-large.png#lightbox "脉动椭圆页面的三个屏幕截图")

请注意，`SKPaint`中创建对象`using`块。 如许多 SkiaSharp 类`SKPaint`派生自`SKObject`，它派生自`SKNativeObject`，该类实现[ `IDisposable` ](https://developer.xamarin.com/api/type/System.IDisposable/)接口。 `SKPaint` 重写`Dispose`方法来释放非托管的资源。

 将`SKPaint`中`using`块可确保`Dispose`用于释放这些非托管的资源的块结束时调用。 发生这种情况是否仍要使用的内存时`SKPaint`释放对象.NET 垃圾回收器，但是在动画代码，最好是将某种程度上主动地以更旧方法释放内存。

 在此特定情况更好的解决方案是创建两个`SKPaint`对象一次并保存它们，以作为字段。

这就是**展开圆圈**动画未。 [ `ExpandingCirclesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs)类开始通过定义多个字段，包括`SKPaint`对象：

```csharp
public class ExpandingCirclesPage : ContentPage
{
    const double cycleTime = 1000;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float t;
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke
    };

    public ExpandingCirclesPage()
    {
        Title = "Expanding Circles";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ...
}
```

此程序使用另一种动画基于 Xamarin.Forms `Device.StartTimer`。 `t`字段动画处理的从 0 到 1 每个`cycleTime`（毫秒):

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            t = (float)(stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime);
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

`PaintSurface`处理程序，绘制具有动画半径的 5 个同心圆。 如果`baseRadius`变量的计算方式为 100，然后作为`t`从 0 到 1，五个圆圈增加了从 0 到 100、 100 到 200、 200 到 300、 300 到 400 和 400 到 500 radii 动态显示。 对于大多数圆圈`strokeWidth`是 50 但第一个圆`strokeWidth`动画处理从 0 到 50。 对于大多数圆圈，颜色为蓝色，但为最后一个圆圈，颜色进行动画处理从蓝色透明：

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
        float baseRadius = Math.Min(info.Width, info.Height) / 12;

        for (int circle = 0; circle < 5; circle++)
        {
            float radius = baseRadius * (circle + t);

            paint.StrokeWidth = baseRadius / 2 * (circle == 0 ? t : 1);
            paint.Color = new SKColor(0, 0, 255,
                (byte)(255 * (circle == 4 ? (1 - t) : 1)));

            canvas.DrawCircle(center.X, center.Y, radius, paint);
        }
    }
}
```

结果是图像看起来相同在`t`等于 0 时`t`等于 1，且圆圈似乎继续永久展开：

[![](animation-images/expandingcircles-small.png "展开圆圈页面的三个屏幕截图")](animation-images/expandingcircles-large.png#lightbox "展开圆圈页面的三个屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
