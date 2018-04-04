---
title: 手指绘制
description: 使用您的手指在画布上绘制。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: charlespetzold
ms.author: chape
ms.date: 04/05/2017
ms.openlocfilehash: 9f7e3f64122f3b95291973d032aaf507dfbc8fab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="finger-painting"></a>手指绘制

_使用您的手指在画布上绘制。_

`SKPath`可以不断更新和显示对象。 此功能允许要用于交互式绘图，如在 finger-painting 程序中的路径。

![](finger-paint-images/fingerpaintsample.png "中手指绘制一个过程")

Xamarin.Forms 中的触摸支持不允许在屏幕上，跟踪各个指，以便在已开发 Xamarin.Forms 触摸跟踪效果以提供其他触摸支持。 文章中介绍了这种效果[**将事件调用从效果**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)。 示例程序[**触摸跟踪效果演示**](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/)将包括使用 SkiaSharp，包括 finger-painting 程序的两个页。

[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)解决方案包括此触摸跟踪事件。 可移植类库项目包括`TouchEffect`类，`TouchActionType`枚举，`TouchActionEventHandler`委托，委托和`TouchActionEventArgs`类。 每个平台项目包括`TouchEffect`该平台类; iOS 项目还包含`TouchRecognizer`类。

**手指绘制**页面**SkiaSharpFormsDemos**是手指绘制的简化的实现。 不允许选择颜色或描边宽度，则具有清除画布上，没有办法，而且当然你无法保存你的图片。

[ **FingerPaintPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml)文件将`SKCanvasView`中单个单元格`Grid`并将其附加`TouchEffect`与`Grid`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Paths.FingerPaintPage"
             Title="Finger Paint">

    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

附加`TouchEffect`直接`SKCanvasView`工作不在所有平台。

[ **FingerPaintPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml.cs)代码隐藏文件定义用于存储的两个集合`SKPath`对象，以及`SKPaint`呈现这些路径的对象：

```csharp
public partial class FingerPaintPage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };

    public FingerPaintPage()
    {
        InitializeComponent();
    }
    ...
}
```

顾名思义，`inProgressPaths`字典存储由一个或多个指当前正在绘制的路径。 字典的键是附带触控事件的 touch ID。 `completedPaths`字段是已完成时手指从屏幕绘制提升的路径的路径的集合。

`TouchAction`处理程序管理这些两个集合。 时的手指首先触摸屏幕上，新`SKPath`添加到`inProgressPaths`。 当该手指移动时，要将其他点添加到路径中。 当发布上方的手指后时，将路径传输到`completedPaths`集合。 可以同时绘制多个手指。 指向某个路径或集合，每次更改后`SKCanvasView`失效：

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                           (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }
}
```

伴随的触摸跟踪事件的点是 Xamarin.Forms 坐标;这些必须转换为 SkiaSharp 坐标，是像素。 目的就在于`ConvertToPixel`方法。

`PaintSurface`处理程序然后只需呈现这两个集合的路径。 正在进行的路径的下方显示的更早版本的完整的路径：

```csharp
public partial class FingerPaintPage : ContentPage
{
    ,,,
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (SKPath path in completedPaths)
        {
            canvas.DrawPath(path, paint);
        }

        foreach (SKPath path in inProgressPaths.Values)
        {
            canvas.DrawPath(path, paint);
        }
    }
    ...
}
```

你 talent 只受限于手指作品：

[![](finger-paint-images/fingerpaint-small.png "三重的手指绘制页的屏幕截图")](finger-paint-images/fingerpaint-large.png#lightbox "手指绘制页面的三个屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [触摸跟踪效果演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/)
- [调用从效果的事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
