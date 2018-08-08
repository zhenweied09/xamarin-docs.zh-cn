---
title: 线和笔划大写字母
description: 此文介绍了如何使用 SkiaSharp 绘制在 Xamarin.Forms 应用程序中具有不同笔画顶端行，此示例代码进行了演示。
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 000bf24c1b06baab892f0b165c8b9eeebebce49d
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615257"
---
# <a name="lines-and-stroke-caps"></a>线和笔划大写字母

_了解如何使用 SkiaSharp 绘制具有不同笔画顶端行_

SkiaSharp，呈现单行是非常不同于呈现一系列相互连接的直线。 甚至当绘制单个线条，但是，通常是要为行指定一个特定的笔划宽度和越宽的行，更重要的将变为调用的代码行末尾的外观*笔划 cap*:

![](lines-images/strokecapsexample.png "三个笔画 caps 选项")

用于绘制单个线条`SKCanvas`定义一种简单[ `DrawLine` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawLine/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/)方法的参数指示的起始和结束的包含的行坐标`SKPaint`对象：

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

默认情况下`StrokeWidth`属性的新实例化`SKPaint`对象是 0，它具有为粗细中呈现的一个像素行 1 的值相同的效果。 这会显示非常细小手机，等的高分辨率设备上可能需要设置`StrokeWidth`到更大的值。 但后，开始画可调整大小的粗细的线，将引发另一个问题： 应开始和结束这些粗线条呈现方式？

调用的开始和结束的行的外观*线帽*，或者在 Skia，*笔划 cap*。 在此上下文中的"cap"一词是指一种类型的 hat&mdash;位于行尾的内容。 您设置[ `StrokeCap` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeCap/)的属性`SKPaint`对象的以下成员之一[ `SKStrokeCap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeCap/)枚举：

- [`Butt`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Butt/) （默认值）
- [`Square`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)

这些最好说明与示例程序。 主页上的第二部分[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程序开始页面标题为**笔划大写字母**基于[ `StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs)类。 此页定义`PaintSurface`循环访问的三个成员的事件处理程序`SKStrokeCap`显示这两个枚举成员的名称和绘制一条直线使用该笔划 cap 枚举：

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

每个成员的`SKStrokeCap`枚举，该处理程序绘制笔画粗细设置为 50 像素和笔划粗细为 2 个像素定位在顶部上的另一条线路有两个线条。 此第二行用于说明的几何开始和结束的独立于线条粗细和笔划上限的行：

[![](lines-images/strokecaps-small.png "三重的笔划大写字母页屏幕截图")](lines-images/strokecaps-large.png#lightbox "带来三倍的笔划大写字母页屏幕截图")

正如您所看到的`Square`和`Round`笔划大写字母有效地通过一半笔划宽度在行开头和结尾处再次扩展行的长度。 需要确定呈现的图形对象的维度时，此扩展变得重要。

`SKCanvas`类还包含用于绘制多个行是有点奇怪的另一种方法：

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

`points`参数是一个数组`SKPoint`值和`mode`属于[ `SKPointMode` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPointMode/)枚举，它具有三个成员：

- [`Points`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Points/) 若要呈现的各个点
- [`Lines`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Lines/) 若要连接的点的每个对
- [`Polygon`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Polygon/) 若要连接所有连续点

**多行**页演示此方法。 [ `MultipleLinesPage` XAML 文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml)实例化两个`Picker`视图，可选择的成员`SKPointMode`枚举的成员和`SKStrokeCap`枚举：

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

`SelectedIndexChanged`两个处理程序`Picker`视图只是使`SKCanvasView`对象：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

此处理程序需要检查是否存在`SKCanvasView`对象是第一个事件处理程序时，调用`SelectedIndex`的属性`Picker`在 XAML 文件中，设置为 0 和之前将发生这种情况`SKCanvasView`已实例化。

`PaintSurface`处理程序访问用于获取两个选定的项目从一个泛型方法`Picker`视图并将它们转换为枚举值：

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

屏幕截图显示了各种`Picker`对三个平台进行的选择：

[![](lines-images/multiplelines-small.png "三重的多个行页的屏幕截图")](lines-images/multiplelines-large.png#lightbox "的多个行页的三个屏幕截图")

在左侧显示了 iPhone 如何`SKPointMode.Points`枚举成员会导致`DrawPoints`要呈现的每个中点`SKPoint`线帽是否为一个方框数组`Butt`或`Square`。 线帽是否呈现圆圈`Round`。

当你改为使用`SKPointMode.Lines`，在中心，在 Android 屏幕上所示`DrawPoints`方法的每个对之间绘制一条线`SKPoint`值，在这种情况下使用指定的线帽， `Round`。

UWP 的屏幕截图显示的结果`SKPointMode.Polygon`值。 在数组中，在连续点之间绘制一条线，但如果您非常仔细查看，您将看到这些行未连接。 每个这些单独的行开始和结束的指定的线帽。 如果您选择`Round`顶端行可能看起来连接，但它们实际上未连接。

行是否已连接或未连接是使用的图形路径的一个重要方面。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
