---
title: 将与 Xamarin.Forms 集成
description: 本文章介绍如何创建响应触摸屏的 SkiaSharp 图形和 Xamarin.Forms 元素，并演示这一替换示例代码。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: 9233850686fa6b20f858bf3358fc46393685297f
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243500"
---
# <a name="integrating-with-xamarinforms"></a>将与 Xamarin.Forms 集成

_创建响应的触摸和 Xamarin.Forms 元素的 SkiaSharp 图形_

SkiaSharp 图形可以多种方式 Xamarin.Forms 的其余部分集成。 你可以合并 SkiaSharp 画布和在同一页上，Xamarin.Forms 元素和位于 SkiaSharp 画布顶端的甚至位置 Xamarin.Forms 元素：

![](integration-images/integrationexample.png "选择具有滑块的颜色")

创建交互式 SkiaSharp 图形 Xamarin.Forms 中的另一种方法是通过触摸屏输入。
中的第二页[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程序有权**点击切换填充**。 一个简单的圆形两种方法，它可绘制&mdash;填充而无需具有填充&mdash;通过点击切换。 [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs)类演示如何改变 SkiaSharp 图形，以响应用户输入。

有关此页上，`SKCanvasView`中实例化类[TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml)文件，还将设置 Xamarin.Forms [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)视图：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.TapToggleFillPage"
             Title="Tap Toggle Fill">

    <skia:SKCanvasView PaintSurface="OnCanvasViewPaintSurface">
        <skia:SKCanvasView.GestureRecognizers>
            <TapGestureRecognizer Tapped="OnCanvasViewTapped" />
        </skia:SKCanvasView.GestureRecognizers>
    </skia:SKCanvasView>
</ContentPage>
```

请注意`skia`XML 命名空间声明。

`Tapped`处理程序`TapGestureRecognizer`对象只是切换的值的布尔型字段以及调用[ `InvalidateSurface` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface()/)方法`SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

调用`InvalidateSurface`有效地生成调用`PaintSurface`处理程序，使用`showFill`字段以填充或不能填充圆：

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
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 50
    };
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);

    if (showFill)
    {
        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.Blue;
        canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    }
}
```

`StrokeWidth`属性已设置为 50 强调差异。 你还可以通过首先绘制内部，，然后概要中查看整个线条宽度。 默认情况下，图形图表绘制更高版本中`PaintSurface`事件处理程序会掩盖那些绘制前面的处理程序。

**颜色浏览**页演示如何，你还可以在与其他 Xamarin.Forms 元素，集成 SkiaSharp 图形，并且还演示两种替代方法，用于定义在 SkiaSharp 中的颜色之间的差异。 静态[ `SKColor.FromHsl` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsl/p/System.Single/System.Single/System.Single/System.Byte/)方法创建`SKColor`值基于色调饱和度亮度模型：

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

静态[ `SKColor.FromHsv` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsv/p/System.Single/System.Single/System.Single/System.Byte/)方法创建`SKColor`值基于类似的色调饱和度值模型：

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

在这两种情况下，`h`自变量的范围为 0 到 360。 `s`， `l`，和`v`自变量的范围从 0 到 100。 `a` （字母或不透明度） 自变量的范围为 0 到 255。

[ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml)文件创建两个`SKCanvasView`中的对象`StackLayout`与并行`Slider`和`Label`允许用户选择 HSL 的视图和HSV 颜色值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Basics.ColorExplorePage"
             Title="Color Explore">
    <StackLayout>
        <!-- Hue slider -->
        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference hueSlider},
                              Path=Value,
                              StringFormat='Hue = {0:F0}'}" />

        <!-- Saturation slider -->
        <Slider x:Name="saturationSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference saturationSlider},
                              Path=Value,
                              StringFormat='Saturation = {0:F0}'}" />

        <!-- Lightness slider -->
        <Slider x:Name="lightnessSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference lightnessSlider},
                              Path=Value,
                              StringFormat='Lightness = {0:F0}'}" />

        <!-- HSL canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hslCanvasView"
                               PaintSurface="OnHslCanvasViewPaintSurface" />

            <Label x:Name="hslLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>

        <!-- Value slider -->
        <Slider x:Name="valueSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference valueSlider},
                              Path=Value,
                              StringFormat='Value = {0:F0}'}" />

        <!-- HSV canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hsvCanvasView"
                               PaintSurface="OnHsvCanvasViewPaintSurface" />

            <Label x:Name="hsvLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>
    </StackLayout>
</ContentPage>
```

这两个`SKCanvasView`元素位于单个单元格`Grid`与`Label`上显示的结果的 RGB 颜色值的顶部放置。

[ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs)代码隐藏文件是相对简单。 共享`ValueChanged`这三个处理程序`Slider`元素只需失效同时`SKCanvasView`元素。 `PaintSurface`处理程序用颜色由清除画布`Slider`元素，并且还设置`Label`坐之上`SKCanvasView`元素：

```csharp
public partial class ColorExplorePage : ContentPage
{
    public ColorExplorePage()
    {
        InitializeComponent();

        hueSlider.Value = 0;
        saturationSlider.Value = 100;
        lightnessSlider.Value = 50;
        valueSlider.Value = 100;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        hslCanvasView.InvalidateSurface();
        hsvCanvasView.InvalidateSurface();
    }

    void OnHslCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsl((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)lightnessSlider.Value);
        args.Surface.Canvas.Clear(color);

        hslLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }

    void OnHsvCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsv((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)valueSlider.Value);
        args.Surface.Canvas.Clear(color);

        hsvLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }
}
```

在 HSL 和 HSV 颜色模型中，色调值范围从 0 到 360，并指示基准颜色色调的。 这些是传统的彩虹颜色： 红色、 橙色、 黄色、 绿色、 蓝色、 indigo、 紫色和红色圆圈，圆圈中的返回。

在 HSL 模型中，亮度的 0 值始终为黑色，和一个 100 的值始终为白色。 时饱和度值为 0，0 和 100 之间的亮度值将是灰色阴影。 增加饱和度将添加更多颜色。 （它们都是与等于 255，另一个等于 0，且第三个范围从 0 到 255 的一个组件的 RGB 值） 的纯颜色饱和度为 100 且亮度为 50 时发生。

在 HSV 模型中，纯颜色导致饱和度和值均为 100 时。 值为 0，而不考虑任何其他设置时的颜色为黑色。 饱和度是 0 和值范围为 0 到 100 时，会出现灰色的色调。

但感受两个模型的最佳方法是尝试使用它们自己：

[![](integration-images/colorexplore-large.png "三重的颜色浏览页面的屏幕截图")](integration-images/colorexplore-small.png#lightbox "三重的颜色浏览页面的屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
