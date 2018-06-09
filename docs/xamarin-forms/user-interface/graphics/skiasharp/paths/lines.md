---
title: 行和描边大写字母
description: 本文章介绍如何使用 SkiaSharp Xamarin.Forms 应用程序中绘制具有不同的描边顶端的行，并演示这一替换示例代码。
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: f63d14ae0dff5f94580c86d1c5bfdfc66bbee7f9
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244089"
---
# <a name="lines-and-stroke-caps"></a>行和描边大写字母

_了解如何使用 SkiaSharp 绘制具有不同的描边顶端的线条_

SkiaSharp 中, 呈现单个行是非常不同的呈现一系列相互连接的直线。 即使时绘制单个线条，但是，它通常是要提供一个特定的描边宽度和的范围越广的行的行所需，更重要的将成为调用的行的末尾的外观*描边端点*:

![](lines-images/strokecapsexample.png "三个笔画 caps 选项")

用于绘制单个线条`SKCanvas`定义一种简单[ `DrawLine` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawLine/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/)方法其自变量指定开始日期和结束的行坐标`SKPaint`对象：

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

默认情况下，`StrokeWidth`属性的新实例化`SKPaint`对象是 0，它具有相同的效果的值为 1 中呈现在粗细的线一个像素。 它显示非常细小手机，等的高分辨率设备上的因此你可能需要设置`StrokeWidth`到更大的值。 但一旦开始绘制相当多的粗细的线，引发另一个问题： 应启动并且这些密集行尾的呈现方式？

调用的开始和结束的行的外观*线帽*或在 Skia，*描边端点*。 在此上下文中的"cap"一词是指一种 hat&mdash;位于行末尾的内容。 你设置[ `StrokeCap` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeCap/)属性`SKPaint`对象的以下成员之一[ `SKStrokeCap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeCap/)枚举：

- [`Butt`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Butt/) （默认值）
- [`Square`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)

这些最佳所示的示例程序。 主页上的第二部分[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程序开始用页标题为**笔划端头**基于[ `StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs)类。 此页定义`PaintSurface`循环访问的三个成员的事件处理程序`SKStrokeCap`枚举，显示的枚举成员的名称和绘制一条直线使用该描边端点：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Center
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width / 2;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = textPaint.FontSpacing;

    foreach (SKStrokeCap strokeCap in Enum.GetValues(typeof(SKStrokeCap)))
    {
        // Display text
        canvas.DrawText(strokeCap.ToString(), xText, y, textPaint);
        y += textPaint.FontSpacing;

        // Display thick line
        thickLinePaint.StrokeCap = strokeCap;
        canvas.DrawLine(xLine1, y, xLine2, y, thickLinePaint);

        // Display thin line
        canvas.DrawLine(xLine1, y, xLine2, y, thinLinePaint);
        y += 2 * textPaint.FontSpacing;
    }
}
```

每个成员`SKStrokeCap`枚举，该处理程序绘制两个行，另一个使用笔画粗细设置为 50 像素和另一个行位于顶部，使用 2 个像素笔画粗细。 此第二行被用于阐述的几何开始和结束的独立于直线粗细和描边端点的行：

[![](lines-images/strokecaps-small.png "三重的笔划端头页的屏幕截图")](lines-images/strokecaps-large.png#lightbox "笔划端头页面的三个屏幕截图")

如你所见，`Square`和`Round`笔划端头有效地扩展半个描边宽度在行的开头和结尾的行的长度。 需要以确定呈现的图形对象的维度时，此扩展变得非常重要。

`SKCanvas`类还包括用于绘制多个线条是某种程度上特殊的另一种方法：

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

`points`参数是数组的`SKPoint`值和`mode`为属于[ `SKPointMode` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPointMode/)枚举，有三个成员：

- [`Points`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Points/) 呈现单独的点
- [`Lines`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Lines/) 若要连接每个对的点
- [`Polygon`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Polygon/) 连接所有连续点

**多行**页演示此方法。 [ `MultipleLinesPage` XAML 文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml)实例化两个`Picker`视图允许你选择的成员`SKPointMode`枚举和的成员`SKStrokeCap`枚举：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.MultipleLinesPage"
             Title="Multiple Lines">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="pointModePicker"
                Title="Point Mode"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Points</x:String>
                <x:String>Lines</x:String>
                <x:String>Polygon</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Butt</x:String>
                <x:String>Round</x:String>
                <x:String>Square</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

`SelectedIndexChanged`处理程序同时`Picker`视图只需失效`SKCanvasView`对象：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

此处理程序需要检查是否存在`SKCanvasView`对象因为事件处理程序是第一个时，调用`SelectedIndex`属性`Picker`在 XAML 文件中，设置为 0 和之前将发生这种情况`SKCanvasView`已实例化。

`PaintSurface`处理程序访问用于获取从两个选择的项目的泛型方法`Picker`视图并将它们转换为枚举值：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create an array of points scattered through the page
    SKPoint[] points = new SKPoint[10];

    for (int i = 0; i < 2; i++)
    {
        float x = (0.1f + 0.8f * i) * info.Width;

        for (int j = 0; j < 5; j++)
        {
            float y = (0.1f + 0.2f * j) * info.Height;
            points[2 * j + i] = new SKPoint(x, y);
        }
    }

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.DarkOrchid,
        StrokeWidth = 50,
        StrokeCap = GetPickerItem<SKStrokeCap>(strokeCapPicker)
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = GetPickerItem<SKPointMode>(pointModePicker);
    canvas.DrawPoints(pointMode, points, paint);
}

T GetPickerItem<T>(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return default(T);
    }
    return (T)Enum.Parse(typeof(T), picker.Items[picker.SelectedIndex]);
}
```

屏幕快照显示了各种`Picker`三个平台上的选择：

[![](lines-images/multiplelines-small.png "三重的多个行页的屏幕截图")](lines-images/multiplelines-large.png#lightbox "三倍的多个行页的屏幕截图")

在左侧显示 iPhone 如何`SKPointMode.Points`枚举成员会导致`DrawPoints`呈现的每个中点`SKPoint`线帽是否为方块数组`Butt`或`Square`。 线帽是否呈现圆圈`Round`。

当你改为使用`SKPointMode.Lines`，如在中心中，Android 屏幕上显示`DrawPoints`方法的每个对之间绘制一条`SKPoint`值，在这种情况下使用指定的线帽， `Round`。

UWP 屏幕快照显示的结果`SKPointMode.Polygon`值。 在数组中，在连续点之间绘制一条线，但如果您非常仔细查看，你将看到未连接这些行。 每个这些单独的行开始和结束的指定的线帽。 如果你选择`Round`上限，行可能看起来连接，但它们实际上未连接。

行是否连接或未连接是使用图形路径的一个重要方面。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
