---
title: 路径填充类型
description: 这篇文章检查不同的效果可能使用 SkiaSharp 路径填充类型，并演示此示例代码。
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 17043054c920a69570f38b227d05980494e29139
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615465"
---
# <a name="the-path-fill-types"></a>路径填充类型

_发现不同的效果可能使用 SkiaSharp 路径填充类型_

在路径中的两个分布图可以重叠，并构成了单个轮廓的行可以重叠。 可能可以填充任何封闭的区域，但可能不想要填充所有封闭的区域。 以下是一个示例：

![](fill-types-images/filltypeexample.png "五个指向部分星级 filles")

你可以这样的一些控制。 填充算法受[ `SKFillType` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.FillType/)的属性`SKPath`，它设置为的成员[ `SKPathFillType` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathFillType/)枚举：

- [`Winding`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.Winding/)默认值
- [`EvenOdd`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.EvenOdd/)
- [`InverseWinding`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.InverseWinding/)
- [`InverseEvenOdd`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.InverseEvenOdd/)

缠绕和奇偶算法确定任何封闭的区域是填充还是不根据从该区域绘制为无穷大的假设直线填充。 该行超过构成路径的一个或多个边界行。 环绕模式下，如果不是在一个方向取得均衡的另一个方向，然后在区域中绘制的行数绘制边界行数填充。 否则填充区域。 如果边界行数为奇数，则奇偶算法填充一块区域。

很多例行路径环绕的算法通常填充路径的所有封闭的区域。 奇偶算法通常会产生更有趣的结果。

典型的示例是 5 星，如中所示**Five-Pointed 星型**页。 [FivePointedStarPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FivePointedStarPage.xaml)文件实例化两个`Picker`视图以选中路径填充类型和路径是描边还是填充和 / 或所采用顺序：

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

代码隐藏文件使用这两个`Picker`值绘制一个五个星：

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

通常情况下，路径填充类型应影响仅填充并不是笔画，但两个`Inverse`模式影响的填充和笔画。 对填充的两个`Inverse`类型填充区域 oppositely，以便在星型之外的区域填充。 用于描边，这两个`Inverse`类型颜色笔画以外的所有内容。 使用这些反向填充类型会产生一些奇怪的效果，如 iOS 屏幕截图中所示：

[![](fill-types-images/fivepointedstar-small.png "三重 Five-Pointed 星型页屏幕截图")](fill-types-images/fivepointedstar-large.png#lightbox "Five-Pointed 星型页面的三个屏幕截图")

Android 和 UWP 的屏幕截图显示了典型的奇偶而曲折效果，但笔划和填充的顺序也会影响结果。

缠绕算法是依赖于绘制行的方向。 通常当你要创建一个路径，您可以控制该方向按你指定的行来自一个点到另一个。 但是，`SKPath`类还定义了方法，如`AddRect`和`AddCircle`绘制整个分布图。 若要控制如何绘制这些对象，方法包括类型的参数[ `SKPathDirection` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathDirection/)，其中包含两个成员：

- [`Clockwise`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathDirection.Clockwise/)
- [`CounterClockwise`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathDirection.CounterClockwise/)

中的方法`SKPath`，包括`SKPathDirection`参数为其指定默认值为`Clockwise`。

**重叠圆圈**页与四个重叠圆圈奇偶路径填充类型，创建一个路径：

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

[![](fill-types-images/overlappingcircles-small.png "三个重叠圆圈页屏幕截图")](fill-types-images/overlappingcircles-large.png#lightbox "三个重叠圆圈页屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
