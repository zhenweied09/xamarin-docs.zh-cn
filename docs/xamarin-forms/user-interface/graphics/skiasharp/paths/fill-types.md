---
title: 路径的填充类型
description: 这篇文章检查不同的效果可能具有 SkiaSharp 路径的填充类型，并演示这一替换示例代码。
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: d54ebd157fcc76b0fcc15bf89c72edbcd88b42f2
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243702"
---
# <a name="the-path-fill-types"></a>路径的填充类型

_发现不同的效果，可以使用 SkiaSharp 路径填充类型_

路径中的两个轮廓可以重叠，从而构成单个轮廓线的行可能发生重叠。 任何封闭的区域可能进行填写，但你可能不想以填充所有封闭的区域。 以下是一个示例：

![](fill-types-images/filltypeexample.png "五个指向星部分 filles")

必须对此的少量控制。 填充算法受[ `SKFillType` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.FillType/)属性`SKPath`，设置为属于[ `SKPathFillType` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathFillType/)枚举：

- [`Winding`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.Winding/)默认值
- [`EvenOdd`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.EvenOdd/)
- [`InverseWinding`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.InverseWinding/)
- [`InverseEvenOdd`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.InverseEvenOdd/)

绕和奇偶算法确定任何封闭的区域是填充还是不根据从该区域绘制向无穷远的假设线条填充。 该行与相交组成的路径的一个或多个边界行。 绕模式中，如果边界绘制的直线在一个方向出绘制在另一个方向，则区域的行数的数量不填充。 否则填充区域。 如果边界行数为奇数，则奇偶算法填充一块区域。

与许多例程路径，绕算法通常填充路径的所有封闭的的区域。 奇偶算法通常产生更有趣的结果。

典型的示例中所示是五个星， **Five-Pointed 星型**页。 [FivePointedStarPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FivePointedStarPage.xaml)文件实例化两个`Picker`视图以选中路径填充类型和是否描边或填充路径和 / 或，以及以什么顺序：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.FivePointedStarPage"
             Title="Five-Pointed Star">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="fillTypePicker"
                Title="Path Fill Type"
                Grid.Row="0"
                Grid.Column="0"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Winding</x:String>
                <x:String>EvenOdd</x:String>
                <x:String>InverseWinding</x:String>
                <x:String>InverseEvenOdd</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="drawingModePicker"
                Title="Drawing Mode"
                Grid.Row="0"
                Grid.Column="1"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Fill only</x:String>
                <x:String>Stroke only</x:String>
                <x:String>Stroke then Fill</x:String>
                <x:String>Fill then Stroke</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

代码隐藏文件使用这两个`Picker`值绘制五个星：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPath path = new SKPath
    {
        FillType = (SKPathFillType)Enum.Parse(typeof(SKPathFillType),
                        fillTypePicker.Items[fillTypePicker.SelectedIndex])
    };
    path.MoveTo(info.Width / 2, info.Height / 2 - radius);

    for (int i = 1; i < 5; i++)
    {
        // angle from vertical
        double angle = i * 4 * Math.PI / 5;
        path.LineTo(center + new SKPoint(radius * (float)Math.Sin(angle),
                                        -radius * (float)Math.Cos(angle)));
    }
    path.Close();

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 50,
        StrokeJoin = SKStrokeJoin.Round
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    switch (drawingModePicker.SelectedIndex)
    {
        case 0:
            canvas.DrawPath(path, fillPaint);
            break;

        case 1:
            canvas.DrawPath(path, strokePaint);
            break;

        case 2:
            canvas.DrawPath(path, strokePaint);
            canvas.DrawPath(path, fillPaint);
            break;

        case 3:
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, strokePaint);
            break;
    }
}
```

通常情况下，路径的填充类型应影响仅填充和不描边，但两个`Inverse`模式影响填充和描边。 对填充，两个`Inverse`类型填充区域 oppositely 以便填充星形以外的区域。 用于描边，这两个`Inverse`类型颜色描边以外的所有内容。 使用这些反填充类型会产生一些奇数副作用，如 iOS 屏幕截图所示：

[![](fill-types-images/fivepointedstar-small.png "三重的 Five-Pointed 星型页面屏幕截图")](fill-types-images/fivepointedstar-large.png#lightbox "Five-Pointed 星型页面的三个屏幕截图")

Android 和 UWP 屏幕截图显示典型的奇偶和绕效果，但的笔画和填充的顺序也会影响结果。

绕算法是依赖于绘制线条的方向。 通常当你要创建一个路径，你可以控制该方向作为指定从一个点绘制连线，到另一个。 但是，`SKPath`类还定义等方法`AddRect`和`AddCircle`绘制整个轮廓。 若要控制如何绘制这些对象的方法包括类型的参数[ `SKPathDirection` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathDirection/)，它具有两个成员：

- [`Clockwise`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathDirection.Clockwise/)
- [`CounterClockwise`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathDirection.CounterClockwise/)

中的方法`SKPath`包括`SKPathDirection`参数为其提供的默认值为`Clockwise`。

**重叠圆圈**页创建具有四个重叠圆圈，奇偶路径填充类型的路径：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(info.Width, info.Height) / 4;

    SKPath path = new SKPath
    {
        FillType = SKPathFillType.EvenOdd
    };

    path.AddCircle(center.X - radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X - radius / 2, center.Y + radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y + radius / 2, radius);

    SKPaint paint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    canvas.DrawPath(path, paint);

    paint.Style = SKPaintStyle.Stroke;
    paint.StrokeWidth = 10;
    paint.Color = SKColors.Magenta;

    canvas.DrawPath(path, paint);
}
```

它是代码的使用最少创建一个有趣图像：

[![](fill-types-images/overlappingcircles-small.png "三重的重叠圆圈页面屏幕截图")](fill-types-images/overlappingcircles-large.png#lightbox "重叠圆圈页面的三个屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
