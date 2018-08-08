---
title: 点和短划线在 SkiaSharp
description: 本文介绍如何掌握 SkiaSharp，在绘制点线和虚线线条的复杂性以及此示例代码进行了演示。
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 7c336e6b5224f61ff84eb39652788b23f52b806e
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615413"
---
# <a name="dots-and-dashes-in-skiasharp"></a>点和短划线在 SkiaSharp

_掌握在 SkiaSharp 绘制点线和虚线线条的复杂性_

SkiaSharp 可绘制线条的都是不可靠的而是点和短划线组成：

![](dots-images/dottedlinesample.png "点线")

为此可以使用*路径效果*，即实例[ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/)设置为的类[ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/)属性`SKPaint`。 可以创建路径效果 （或合并路径效果），它使用静态`Create`定义的方法`SKPathEffect`。

若要绘制虚线，请使用[ `SKPathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/)静态方法。 有两个参数： 第一次，这是一个数组`float`来指示点和短划线的长度，以及它们之间的空格的长度的值。 此数组必须具有偶数数目的元素，并且应至少两个元素。 （可以有零个元素数组中的，但该结果一条实线。）如果有两个元素，第一个圆点或短划线的长度，第二个间隙的长度在下一步的句点或破折号之前。 如果有多个两个元素，则它们是按以下顺序： 短划线的长度、 间隙长度、 短划线的长度、 间隙长度等。

通常情况下，您将想要的短划线和间隙长度笔划宽度的倍数。 如果笔划宽度为 10 个像素，例如，然后数组 {10，10} 将绘制点线的点和间隙的笔画粗细的长度相同。

但是，`StrokeCap`设置的`SKPaint`对象也会影响这些点和短划线。 稍后将看到，此数组的元素具有影响。

以点分隔和上演示了虚线**点和短划线**页。 [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml)文件实例化两个`Picker`查看时，一个用于允许您选择笔画线帽和第二个选择的短划线数组：

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

中的前三个项`dashArrayPicker`假定笔划宽度为 10 像素。 {10，10} 的点线，数组是 {30，10} 是虚线，和 {10、 10、 30、 10} 是针对点-划线行。 （其他三个将讨论很快。）

[ `DotsAndDashesPage`代码隐藏文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs)包含`PaintSurface`事件处理程序和几个帮助器例程用于访问`Picker`视图：

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

在下面的屏幕截图上最左侧的 iOS 屏幕显示点线：

[![](dots-images/dotsanddashes-small.png "三个点和短划线页屏幕截图")](dots-images/dotsanddashes-large.png#lightbox "三个点和短划线页屏幕截图")

但是，还应该 Android 屏幕显示点线使用数组 {10，10} 改为直线是实线。 这是怎么回事？ 问题是 Android 屏幕还具有笔划上限设置为`Square`。 这将扩展一半笔划宽度，从而导致填满间隙的所有短的划线。

若要获取解决此问题时使用的笔画上限`Square`或`Round`，必须减小的短划线长度是以数组的笔划长度 （有时会导致短划线的长度为 0），并提高笔划长度的间隙长度。 这是如何的最后三个短划线中的数组`Picker`计算在 XAML 文件中：

- {10，10} 将成为 {0，20} 的点线
- {30，10} 将成为 {20、 20} 的虚线
- {10、 10、 30、 10} 成为点线和虚线行 {0、 20、 20、 20}

点线和虚线绘制笔画的行，UWP 屏幕所示的上限`Round`。 `Round`描边端点通常粗线条中提供的点和短划线最佳外观。

到目前为止，将没有提到这一点做的第二个参数的`SKPathEffect.CreateDash`方法。 此参数名为`phase`，它是指行开头的点划线模式内的偏移量。 例如，如果 dash 数组为 {10，10} 和`phase`是 10，则在行开头间隔而不是一个圆点。

一个有趣的应用程序`phase`参数是动画。 **经过动画处理的临界点**页是类似于**Archimedean 螺旋**页上，不同之处在于[ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/AnimatedSpiralPage.cs)该类进行动画处理`phase`参数。 该页还演示了动画的另一种方法。 前面的示例[ `PulsatingEllipsePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml.cs)使用`Task.Delay`方法，以控制动画。 此示例改为使用 Xamarin.Forms`Device.Timer`方法：


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

当然，您必须以实际运行该程序，查看动画：

[![](dots-images/animatedspiral-small.png "经过动画处理的临界点页的三个屏幕截图")](dots-images/animatedspiral-large.png#lightbox "带来三倍的经过动画处理的临界点页屏幕截图")

现在已了解如何绘制的线条以及如何定义使用参数化的等式的曲线。 若要在以后发布的部分将介绍各种类型的曲线的`SKPath`支持。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
