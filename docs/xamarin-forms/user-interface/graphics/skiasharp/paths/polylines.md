---
title: 折线和参数的等式
description: 本文章介绍如何使用 SkiaSharp 呈现任何行到可以定义为上面带有参数的公式，并演示这一替换示例代码。
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9539a21b7dbc91da63795639610886233ed705be
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245304"
---
# <a name="polylines-and-parametric-equations"></a>折线和参数的等式

_使用 SkiaSharp 来呈现任何行，可以定义为上面带有参数的公式_

在本指南稍后部分，你将看到的各种方法，`SKPath`定义来呈现某些类型的曲线。 但是，它，有时需要绘制曲线不直接支持的一种`SKPath`。 在这种情况下，你可以使用折线 （连接的直线的集合） 绘制数学上可以定义任何曲线。 如果你进行行足够小，并且大量足够结果将如下所示曲线。 此螺旋线达到实际 3,600 较短的行：

![](polylines-images/spiralexample.png "螺旋线")

通常最好定义方面的参数的公式对一条曲线。 这些是公式的 X 和 Y 坐标，依赖于第三个变量，有时称为`t`时间。 例如，以下参数的公式为定义包含一个半径为 1 （0，0） 的点处居中圆圈，圆圈*t*从 0 到 1:

 x = cos(2πt) y = sin(2πt)

 如果你想 radius 大于 1，则你只需将的正弦值和余弦值乘以该 radius 并如果你需要将 center 移动到另一个位置添加这些值：

 x = xCenter + radius·cos(2πt) y = yCenter + radius·sin(2πt)

有关与水平和垂直轴并行的椭圆，涉及两个 radii:

x = xCenter + xRadius·cos(2πt) y = yCenter + yRadius·sin(2πt)

然后可以将等效的 SkiaSharp 代码放在循环，用于计算的各种点并将添加到路径中。 下面的 SkiaSharp 代码创建`SKPath`填充显示图面的椭圆的对象。 循环直接循环访问 360 度。 中心是一半的宽度和高度显示图面，和两个 radii 也是如此：

```csharp
SKPath path = new SKPath();

for (float angle = 0; angle < 360; angle += 1)
{
    double radians = Math.PI * angle / 180;
    float x = info.Width / 2 + (info.Width / 2) * (float)Math.Cos(radians);
    float y = info.Height / 2 + (info.Height / 2) * (float)Math.Sin(radians);

    if (angle == 0)
    {
        path.MoveTo(x, y);
    }
    else
    {
        path.LineTo(x, y);
    }
}
path.Close();
```

这会导致 360 较短的行定义的椭圆。 当呈现时，它将显示平滑。

当然，你不必创建使用折线，因为的椭圆`SKPath`包括`AddOval`会为您的方法。 但你可能想要绘制不由提供的视觉对象`SKPath`。

**Archimedean 螺旋**页具有代码，它类似于椭圆代码，但有重要区别。 它循环圆 360 度 10 次，持续调整 radius:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(center.X, center.Y);

    using (SKPath path = new SKPath())
    {
        for (float angle = 0; angle < 3600; angle += 1)
        {
            float scaledRadius = radius * angle / 3600;
            double radians = Math.PI * angle / 180;
            float x = center.X + scaledRadius * (float)Math.Cos(radians);
            float y = center.Y + scaledRadius * (float)Math.Sin(radians);
            SKPoint point = new SKPoint(x, y);

            if (angle == 0)
            {
                path.MoveTo(point);
            }
            else
            {
                path.LineTo(point);
            }
        }

        SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 5
        };

        canvas.DrawPath(path, paint);
    }
}
```

结果也称为*算术螺旋*因为每个循环之间的偏移量是常量：

[![](polylines-images/archimedeanspiral-small.png "三重的 Archimedean 螺旋页面屏幕截图")](polylines-images/archimedeanspiral-large.png#lightbox "Archimedean 螺旋页面的三个屏幕截图")

请注意，`SKPath`中创建`using`块。 这`SKPath`占用的内存量大于`SKPath`意味着的上一个程序中的对象`using`块是更合适，若要释放任何非托管的资源。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
