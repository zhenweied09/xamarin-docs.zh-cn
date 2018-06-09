---
title: 点和 SkiaSharp 中的短划线
description: 本文探讨如何主 SkiaSharp 中的绘图线和虚线的复杂性，并演示这一替换示例代码。
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 5571f2d1824cef72e192a19d15f9af03276f7523
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243868"
---
# <a name="dots-and-dashes-in-skiasharp"></a>点和 SkiaSharp 中的短划线

_主 SkiaSharp 中的绘图线和虚线的复杂性_

SkiaSharp 可绘制线条，它们不稳定，但改为将由点和短划线组成：

![](dots-images/dottedlinesample.png "虚线")

实现此操作*路径效果*，即实例[ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/)设置为的类[ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/)属性`SKPaint`。 你可用于创建路径效果 （或合并路径效应），它使用静态`Create`由定义方法`SKPathEffect`。

若要绘制虚线，你可以使用[ `SKPathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/)静态方法。 有两个参数： 首先，这是一个数组`float`值，用于指示点和短划线的长度和它们之间的空格的长度。 此数组必须具有偶数数目的元素，并且应该有至少两个元素。 （可以有零个元素数组中的但在一条实线而产生。）如果有两个元素，第一个是句点或短划线，长度，第二个是在下一个句点或短划线之前的差异的长度。 如果有超过两个元素，则它们将按以下顺序： 短划线-长度、 间隙长度、 短划线长度、 间隙长度等。

通常情况下，你将想要的短划线和间隙长度描边宽度的倍数。 如果描边宽度为 10 像素，例如，然后数组 {10，10} 将绘制点线点和间隙的与笔画粗细的长度相同。

但是，`StrokeCap`设置`SKPaint`对象也会影响这些点和短划线。 正如您很快将看到，，在此数组的元素上产生影响。

以点分隔格式和虚线演示上**三点和短划线**页。 [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml)文件实例化两个`Picker`查看时，一个用于允许您选择描边 cap 和第二个选择的短划线数组：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.DotsAndDashesPage"
             Title="Dots and Dashes">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="0"
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

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>10, 10</x:String>
                <x:String>30, 10</x:String>
                <x:String>10, 10, 30, 10</x:String>
                <x:String>0, 20</x:String>
                <x:String>20, 20</x:String>
                <x:String>0, 20, 20, 20</x:String>
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

中的前三个项`dashArrayPicker`假定描边宽度为 10 个像素。 {10，10} 数组为虚线，{30，10} 为虚线，和 {10、 10、 30，10} 是针对点划线行。 （其他三个将讨论很快。）

[ `DotsAndDashesPage`代码隐藏文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs)包含`PaintSurface`事件处理程序和几个用于访问的帮助器例程`Picker`视图：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = (SKStrokeCap)Enum.Parse(typeof(SKStrokeCap),
                        strokeCapPicker.Items[strokeCapPicker.SelectedIndex]),

        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint);
}

T GetPickerItem<T>(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return default(T);
    }

    return (T)Enum.Parse(typeof(T), picker.Items[picker.SelectedIndex]);
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string[] strs = picker.Items[picker.SelectedIndex].Split(new char[] { ' ', ',' },
                                                             StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

在下面的屏幕截图上最左侧, 的 iOS 屏幕显示点线：

[![](dots-images/dotsanddashes-small.png "三重的点和短划线页的屏幕截图")](dots-images/dotsanddashes-large.png#lightbox "三重的点和短划线页的屏幕截图")

但是，还应该 Android 屏幕显示使用数组 {10，10} 虚线，但改为直线是实线。 这是怎么回事？ 问题是 Android 屏幕还具有描边上限设置为`Square`。 这将扩展半个描边宽度，从而导致它们填补间隙的所有短划线。

若要使用的描边上限时，获取解决此问题`Square`或`Round`，必须降低 （有时会导致短划线长度为 0），描边长度数组中的短划线长度和增加描边长度的间隔长度。 这是如何的最后三个短划线-中的数组`Picker`计算在 XAML 文件中：

- {10，10} 将成为 {0，20} 的虚线
- {30，10} 将成为 {20、 20} 的虚线
- {10、 10、 30、 10} 变为虚线与虚线行 {0、 20、 20、 20}

UWP 屏幕显示以点分隔格式，虚线笔画的行的制定一个上限`Round`。 `Round`描边端点通常为赋予点和短划线的最佳外观密集的行中。

到目前为止的第二个参数的做出没有提及`SKPathEffect.CreateDash`方法。 此参数的名称为`phase`，它是指在该点划线模式中的行的开头的偏移量。 例如，如果 dash 数组为 {10，10} 和`phase`为 10，然后在行开头间隔而不是一个点。

一个有趣的应用程序`phase`参数处于一种动画效果。 **进行动画处理螺旋**页是类似于**Archimedean 螺旋**页上，只不过[ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/AnimatedSpiralPage.cs)类进行动画处理`phase`参数。 该页还演示动画另一种方法。 前面的示例[ `PulsatingEllipsePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml.cs)使用`Task.Delay`方法可控制动画。 此示例改为使用 Xamarin.Forms`Device.Timer`方法：


```csharp
const double cycleTime = 250;       // in milliseconds

SKCanvasView canvasView;
Stopwatch stopwatch = new Stopwatch();
bool pageIsActive;
float dashPhase;

public AnimatedSpiralPage()
{
    Title = "Animated Spiral";

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
        dashPhase = (float)(10 * t);
        canvasView.InvalidateSurface();

        if (!pageIsActive)
        {
            stopwatch.Stop();
        }

        return pageIsActive;
    });
}
```

当然，你将需要实际运行此程序以查看动画：

[![](dots-images/animatedspiral-small.png "进行动画处理螺旋页面的三个屏幕截图")](dots-images/animatedspiral-large.png#lightbox "进行动画处理螺旋页面的三个屏幕截图")

现在，你已了解如何绘制线条以及如何定义使用参数化方程的曲线。 若要更高版本发布部分将介绍各种类型的曲线，`SKPath`支持。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
