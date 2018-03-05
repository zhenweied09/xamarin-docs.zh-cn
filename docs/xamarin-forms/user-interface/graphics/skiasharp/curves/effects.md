---
title: "路径效果"
description: "发现允许路径用于进行描边，并填充的不同路径效果"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 07/29/2017
ms.openlocfilehash: 4344559c85ad637ad198b3afc28b17e694a14f82
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="path-effects"></a>路径效果

_发现允许路径用于进行描边，并填充的不同路径效果_

A*路径效果*是的一个实例[ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/)使用八个静态之一创建的类`Create`方法。 `SKPathEffect`然后将对象设置为[ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/)属性`SKPaint`对象各种有趣效果，例如，大于描画一条线较小的复制路径：

![](effects-images/patheffectsample.png "链接链示例")

路径效果使您能够：

- 绘制一条线点和短划线
- 描边，则任何已填充的路径行
- 用阴影行填充区域
- 平铺路径用填充区域
- 使具有陡舍入的角
- 添加随机"抖动"到直线和曲线

此外，你可以组合两个或多个路径效果。

这篇文章还演示如何使用`GetFillPath`方法`SKPaint`将一条路径转换为另一条路径，通过应用属性`SKPaint`，包括`StrokeWidth`和`PathEffect`。 这会导致一些有趣的技术，例如获取另一个路径概括的路径。 `GetFillPath` 也会与路径效果有所帮助。

## <a name="dots-and-dashes"></a>点和短划线

使用[ `PathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/)文章中所述方法[**三点和短划线**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)。 该方法的第一个参数是数组，其中包含两个或多个值，长度的短划线和间隙短划线的长度之间交替为偶数：

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

这些值是*不*相对于描边宽度。 例如，如果描边宽度为 10，并且你希望行组成正方形短划线和正方形间隙，设置`intervals`数组 {10，10}。 `phase`参数用于指示在短划线图案中的行开始的位置。 在此示例中，如果你想要利用正方形 gap 开始的行，设置`phase`到 10。

在结束短划线受`StrokeCap`属性`SKPaint`。 对于宽描边宽度，它是很常见，若要将此属性设置为`SKStrokeCap.Round`要舍入的端的短划线。 在此情况下中的值`intervals`数组执行*不*包括舍入，导致的额外长度，这意味着一个循环的点，需要指定的宽度为零。 有关的描边宽度为 10，若要创建带有圆点和相同直径的点之间间隙的行，使用`intervals`的数组 {0，20}。

**进行动画处理以点分隔格式的文本**页是类似于**概述文本**文所述的页[**集成文本和图形**](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)中它将显示通过设置概述文本字符`Style`属性`SKPaint`对象传递给`SKPaintStyle.Stroke`。 此外，**进行动画处理以点分隔格式的文本**使用`SKPathEffect.CreateDash`以便这大纲线的外观和程序还进行动画处理`phase`参数`SKPathEffect.CreateDash`方法来执行似乎旅行放置在文本两侧的点字符。 下面是在横向模式中的页：

[![](effects-images/animateddottedtext-small.png "三重的动画处理以点分隔格式的文本页的屏幕截图")](effects-images/animateddottedtext-large.png "进行动画处理以点分隔格式的文本页的三个屏幕截图")

[ `AnimatedDottedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs)类开始通过定义某些常量，并且还重写`OnAppearing`和`OnDisappearing`用于动画的方法：

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    const string text = "DOTTED";
    const float strokeWidth = 10;
    static readonly float[] dashArray = { 0, 2 * strokeWidth };

    SKCanvasView canvasView;
    bool pageIsActive;

    public AnimatedDottedTextPage()
    {
        Title = "Animated Dotted Text";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;

        Device.StartTimer(TimeSpan.FromSeconds(1f / 60), () =>
        {
            canvasView.InvalidateSurface();
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

`PaintSurface`处理程序首先创建`SKPaint`对象以显示文本。 `TextSize`根据屏幕的宽度调整属性：

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create an SKPaint object to display the text
        using (SKPaint textPaint = new SKPaint
            {
                Style = SKPaintStyle.Stroke,
                StrokeWidth = strokeWidth,
                StrokeCap = SKStrokeCap.Round,
                Color = SKColors.Blue,
            })
        {
            // Adjust TextSize property so text is 95% of screen width
            float textWidth = textPaint.MeasureText(text);
            textPaint.TextSize *= 0.95f * info.Width / textWidth;

            // Find the text bounds
            SKRect textBounds;
            textPaint.MeasureText(text, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Animate the phase; t is 0 to 1 every second
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 1 / 1);
            float phase = -t * 2 * strokeWidth;

            // Create dotted line effect based on dash array and phase
            using (SKPathEffect dashEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                // Set it to the paint object
                textPaint.PathEffect = dashEffect;

                // And draw the text
                canvas.DrawText(text, xText, yText, textPaint);
            }
        }
    }
}
```

该方法末尾`SKPathEffect.CreateDash`使用调用方法`dashArray`，定义为字段，并经过动画处理`phase`值。 `SKPathEffect`实例设置为`PathEffect`属性`SKPaint`对象以显示文本。

或者，您可以设置`SKPathEffect`对象传递给`SKPaint`之前测量文本和居中的页上的对象。 在这种情况下，但是，动画的点和短划线的呈现文本的大小会导致某些变体，并且文本倾向于有点振动。 （试试看 ！）

你还会发现作为周围的文本字符的动画的点圆圈，有每个闭合曲线中的某一点点似乎弹入和移出存在。 这是定义字符大纲的路径开始和结束的地方。 如果路径长度不是短划线图案 （在这种情况下 20 像素为单位） 的长度的整数倍仅属于该模式适合在路径末尾。

可以调整以适应的路径长度在短划线图案的长度，但需要确定的路径，是一种技术的长度在以后的文章中介绍。

**点 / 短划线-变体**程序进行本身在短划线图案动画处理，以便短划线似乎划分为多点，将结合到窗体短划线，试：

[![](effects-images/dotdashmorph-small.png "三重的点 Dash 变体页的屏幕截图")](effects-images/dotdashmorph-large.png "点 Dash 变体页面的三个屏幕截图")

[ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs)类重写`OnAppearing`和`OnDisappearing`方法就像上一个程序，但该类定义`SKPaint`作为字段的对象：

```csharp
public class DotDashMorphPage : ContentPage
{
    const float strokeWidth = 30;
    static readonly float[] dashArray = new float[4];

    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint ellipsePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = strokeWidth,
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create elliptical path
        using (SKPath ellipsePath = new SKPath())
        {
            ellipsePath.AddOval(new SKRect(50, 50, info.Width - 50, info.Height - 50));

            // Create animated path effect
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 3 / 3);
            float phase = 0;

            if (t < 0.25f)  // 1, 0, 1, 2 --> 0, 2, 0, 2
            {
                float tsub = 4 * t;
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2 * tsub;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2;
            }
            else if (t < 0.5f)  // 0, 2, 0, 2 --> 1, 2, 1, 0
            {
                float tsub = 4 * (t - 0.25f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2 * (1 - tsub);
                phase = strokeWidth * tsub;
            }
            else if (t < 0.75f) // 1, 2, 1, 0 --> 0, 2, 0, 2
            {
                float tsub = 4 * (t - 0.5f);
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2 * tsub;
                phase = strokeWidth * (1 - tsub);
            }
            else               // 0, 2, 0, 2 --> 1, 0, 1, 2
            {
                float tsub = 4 * (t - 0.75f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2 * (1 - tsub);
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2;
            }

            using (SKPathEffect pathEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                ellipsePaint.PathEffect = pathEffect;
                canvas.DrawPath(ellipsePath, ellipsePaint);
            }
        }
    }
}
```

`PaintSurface`处理程序创建的大小的页上，基于椭圆路径并执行长的设置的代码部分`dashArray`和`phase`变量。 为动画变量`t`的范围为 0 到 1，`if`块分解，时间为四个季度和每个这些季度中`tsub`还范围从 0 到 1。 在非常结束时，程序创建`SKPathEffect`并将它设置为`SKPaint`对象以进行绘制。

## <a name="from-path-to-path"></a>从到路径的路径

[ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/)方法`SKPaint`将转换为另一个基于中设置的一个路径`SKPaint`对象。 若要查看此工作原理，替换`canvas.DrawPath`调用在上一个程序替换为以下代码中：

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

在此新代码中，`GetFillPath`调用将`ellipsePath`（这是仅一个椭圆） 到`newPath`，然后显示与`newPaint`。 `newPaint`对象创建使用所有默认属性设置只不过`Style`属性设置基于在布尔值返回值从`GetFillPath`。

视觉对象是相同的颜色，在中设置除外`ellipsePaint`但不是`newPaint`。 而不是简单中定义的椭圆`ellipsePath`，`newPath`包含大量路径分布定义的一系列点和短划线。 这是应用的各种属性的结果`ellipsePaint`- `StrokeWidth`， `StrokeCap`，和`PathEffect`-到`ellipsePath`并在中放置产生的路径`newPath`。 `GetFillPath`方法返回一个布尔值，该值指示是否目标路径为要填充; 在此示例中，返回值是`true`用于填充的路径。

请尝试更改`Style`中设置`newPaint`到`SKPaintStyle.Stroke`，你将看到一个像素宽度行所述的各个路径分布。

## <a name="stroking-with-a-path"></a>使用路径描边

[ `SKPathEffect.Create1DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create1DPath/p/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPath1DPathEffectStyle/)方法是从概念上讲类似于`SKPathEffect.CreateDash`只不过指定路径，而不是短划线和间隙的模式。 此路径被复制到描边行或曲线多次。

语法为：

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

> [!IMPORTANT]
> 注意： 没有的重载`Create1DPath`定义的类型的枚举参数`SkPath1DPathEffect`小写字母 'k'。 此名称为错误，并因此枚举和方法已弃用，但不推荐使用的方法将成为你的代码，一部分非常方便，很难准确查看不正确。

通常，你将传递给的路径`Create1DPath`将小并围绕 （0，0） 的点。 `advance`参数指示的路径中心的距离在行上复制路径。 此外，你通常会将此参数设置为路径的大致宽度。 `phase`与它相同此处的角色中执行的自变量起到`CreateDash`方法。

[ `SKPath1DPathEffectStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath1DPathEffectStyle/)有三个成员：

- [`Translate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Translate/)
- [`Rotate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Rotate/)
- [`Morph`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Morph/)

`Translate`成员会导致要保留在相同的方向中复制沿行或曲线的路径。 有关`Rotate`，根据为曲线的切线旋转路径。 路径包含其正常方向为水平线条。 `Morph` 类似于`Rotate`只不过路径本身也弯曲以匹配正在描边的行的曲率。

**1 D 路径效果**页演示这三个选项。 [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml)文件定义包含对应于枚举的三个成员的三个项选取器：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.OneDimensionalPathEffectPage"
             Title="1D Path Effect">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="effectStylePicker"
                Title="Effect Style"
                Grid.Row="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Translate</x:String>
                <x:String>Rotate</x:String>
                <x:String>Morph</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1" />
    </Grid>
</ContentPage>
```

[ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs)代码隐藏文件定义三个`SKPathEffect`作为字段的对象。 这些创建使用`SKPathEffect.Create1DPath`与`SKPath`对象创建使用`SKPath.ParseSvgPathData`。 第一种是一个简单的框，第二个是一个菱形形状上，第三个是一个矩形。 这些凭据用于进行演示三个影响样式：

```csharp
public partial class OneDimensionalPathEffectPage : ContentPage
{
    SKPathEffect translatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 -10 L 10 -10, 10 10, -10 10 Z"),
                                  24, 0, SKPath1DPathEffectStyle.Translate);

    SKPathEffect rotatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 0 L 0 -10, 10 0, 0 10 Z"),
                                  20, 0, SKPath1DPathEffectStyle.Rotate);

    SKPathEffect morphPathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -25 -10 L 25 -10, 25 10, -25 10 Z"),
                                  55, 0, SKPath1DPathEffectStyle.Morph);

    SKPaint pathPaint = new SKPaint
    {
        Color = SKColors.Blue
    };

    public OneDimensionalPathEffectPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            path.MoveTo(new SKPoint(0, 0));
            path.CubicTo(new SKPoint(2 * info.Width, info.Height),
                         new SKPoint(-info.Width, info.Height),
                         new SKPoint(info.Width, 0));

            switch (effectStylePicker.Items[effectStylePicker.SelectedIndex])
            {
                case "Translate":
                    pathPaint.PathEffect = translatePathEffect;
                    break;

                case "Rotate":
                    pathPaint.PathEffect = rotatePathEffect;
                    break;

                case "Morph":
                    pathPaint.PathEffect = morphPathEffect;
                    break;
            }

            canvas.DrawPath(path, pathPaint);
        }
    }
}
```

`PaintSurface`处理程序创建循环本身，并访问选取器，以便确定哪些的贝塞尔曲线`PathEffect`应该用于绘制它。 三个选项- `Translate`， `Rotate`，和`Morph`-从左到右显示：

[![](effects-images/1dpatheffect-small.png "三重的一维路径效果页的屏幕截图")](effects-images/1dpatheffect-large.png "1 D 路径效果页面的三个屏幕截图")

中指定的路径`SKPathEffect.Create1DPath`始终填充方法。 中指定的路径`DrawPath`方法始终进行描边，如果`SKPaint`对象都有其`PathEffect`属性设置为 1 D 路径效果。 请注意，`pathPaint`对象未包含任何`Style`设置，默认值通常`Fill`，但无论描边的路径。

在中使用的框`Translate`示例为方形，20 像素和`advance`参数设置为 24。 这种差异会导致间隙时行大致是水平还是垂直，但对应的框稍有重叠线路何时对角线，因为框中的对角线为 28.3 像素的框。 

中的菱形形状`Rotate`示例也是 20 像素宽。 `advance` ，以便该点继续触摸，因为随行的曲率一起旋转菱形设置为 20。

中的矩形形状`Morph`示例是 50 像素宽与`advance`55 设置以使它们弯曲的情况下绕贝塞尔曲线的矩形之间应有一小间隔。

如果`advance`参数小于后的路径的大小，然后复制的路径可以重叠。 这可能导致一些有趣的效果。 **链接链**页显示重叠圆圈似乎类似于在不同形状的 catenary 挂起一个链接的链的一系列：

[![](effects-images/linkedchain-small.png "三重的链接链页面屏幕截图")](effects-images/linkedchain-large.png "链接链页面的三个屏幕截图")

查找非常接近，你将看到这些并不实际圆圈。 链中的每个链接是两个弧，调整大小并放置因此它们看起来与相邻的链接进行连接。

链或统一的权重分发电缆挂起 catenary 形式。 倒 catenary 的形式建立体系结构受益于平均分布的压力从体系结构的权重。 Catenary 具有看似简单的数学说明：

y = a · cosh(x / a)

*Cosh*是双曲余弦值函数。 有关*x*等于 0， *cosh*为零和*y*等于。 这是 catenary 的中心。 如*余弦*函数， *cosh*被认为是*甚至*，这意味着， *cosh(–x)*等于*cosh(x)*，并提高正整数或负自变量的值增加。 这些值描述窗体 catenary 两侧的曲线。

查找适当的值的以适应电话的页的尺寸为 catenary 不是直接计算。 如果*w*和*h*是的宽度和高度的矩形的最佳值满足以下公式：

cosh(w / 2 / a) = 1 + h / a

中的以下方法[ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs)类通过两个表达式左侧和右侧的等号作为引用包含该相等`left`和`right`。 值较小，`left`大于`right`; 对于较大值的，`left`是小于`right`。 `while`循环收缩中上的最佳值:

```csharp
float FindOptimumA(float width, float height)
{
    Func<float, float> left = (float a) => (float)Math.Cosh(width / 2 / a);
    Func<float, float> right = (float a) => 1 + height / a;

    float gtA = 1;         // starting value for left > right
    float ltA = 10000;     // starting value for left < right

    while (Math.Abs(gtA - ltA) > 0.1f)
    {
        float avgA = (gtA + ltA) / 2;

        if (left(avgA) < right(avgA))
        {
            ltA = avgA;
        }
        else
        {
            gtA = avgA;
        }
    }

    return (gtA + ltA) / 2;
}
```

`SKPath`对象类的构造函数，并且所产生的中创建链接`SKPathEffect`然后将对象设置为`PathEffect`属性`SKPaint`存储为字段的对象：

```csharp
public class LinkedChainPage : ContentPage
{
    const float linkRadius = 30;
    const float linkThickness = 5;

    Func<float, float, float> catenary = (float a, float x) => (float)(a * Math.Cosh(x / a));

    SKPaint linksPaint = new SKPaint
    {
        Color = SKColors.Silver
    };

    public LinkedChainPage()
    {
        Title = "Linked Chain";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the individual links
        SKRect outer = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
        SKRect inner = outer;
        inner.Inflate(-linkThickness, -linkThickness);

        using (SKPath linkPath = new SKPath())
        {
            linkPath.AddArc(outer, 55, 160);
            linkPath.ArcTo(inner, 215, -160, false);
            linkPath.Close();

            linkPath.AddArc(outer, 235, 160);
            linkPath.ArcTo(inner, 395, -160, false);
            linkPath.Close();

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(linkPath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);
        }
    }
    ...
}
```

主要工作`PaintSurface`处理程序是创建 catenary 本身的路径。 确定最佳之后并将其在存储`optA`变量，它还需要计算从窗口顶部的偏移量。 然后，它可以累积的集合`SKPoint`catenary，值成一个路径，打开，并绘制使用以前创建的路径`SKPaint`对象：

```csharp
public class LinkedChainPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Width and height of catenary
        int width = info.Width;
        float height = info.Height - linkRadius;

        // Find the optimum 'a' for this width and height
        float optA = FindOptimumA(width, height);

        // Calculate the vertical offset for that value of 'a'
        float yOffset = catenary(optA, -width / 2);

        // Create a path for the catenary
        SKPoint[] points = new SKPoint[width];

        for (int x = 0; x < width; x++)
        {
            points[x] = new SKPoint(x, yOffset - catenary(optA, x - width / 2));
        }

        using (SKPath path = new SKPath())
        {
            path.AddPoly(points, false);

            // And render that path with the linksPaint object
            canvas.DrawPath(path, linksPaint);
        }
    }
    ...
}
```

此程序定义中使用的路径`Create1DPath`具有其 （0，0） 的中心点。 这看起来合理因为 （0，0） 与线条或它所装饰的曲线对齐路径的点。 但是，你可以使用非居中 （0，0） 的一些特殊效果点。

**传送带**页创建类似于使用曲线的顶部和底部这大小调整窗口的尺寸为椭圆形传送带的路径。 该路径进行描边通过简单`SKPaint`对象 20 像素宽和彩色的灰色，然后能与另一个试描边`SKPaint`对象`SKPathEffect`对象，用于引用类似于很少的存储桶的路径：

[![](effects-images/conveyorbelt-small.png "三重的传送带页面屏幕截图")](effects-images/conveyorbelt-large.png "传送带页面的三个屏幕截图")

（0，0） 的存储桶路径一点句柄时，因此，在`phase`自变量进行动画处理，存储桶似乎围绕传送带，可能是 scooping 向上底部水位和转储出来的顶部。

[ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs)类实现通过重写的动画`OnAppearing`和`OnDisappearing`方法。 在页面的构造函数中定义的存储桶的路径：

```csharp
public class ConveyorBeltPage : ContentPage
{
    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint conveyerPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 20,
        Color = SKColors.DarkGray
    };

    SKPath bucketPath = new SKPath();

    SKPaint bucketsPaint = new SKPaint
    {
        Color = SKColors.BurlyWood,
    };

    public ConveyorBeltPage()
    {
        Title = "Conveyor Belt";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the bucket starting with the handle
        bucketPath.AddRect(new SKRect(-5, -3, 25, 3));

        // Sides
        bucketPath.AddRoundedRect(new SKRect(25, -19, 27, 18), 10, 10, 
                                  SKPathDirection.CounterClockwise);
        bucketPath.AddRoundedRect(new SKRect(63, -19, 65, 18), 10, 10, 
                                  SKPathDirection.CounterClockwise);

        // Five slats
        for (int i = 0; i < 5; i++)
        {
            bucketPath.MoveTo(25, -19 + 8 * i);
            bucketPath.LineTo(25, -13 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small, 
                             SKPathDirection.CounterClockwise, 65, -13 + 8 * i);
            bucketPath.LineTo(65, -19 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small, 
                             SKPathDirection.Clockwise, 25, -19 + 8 * i);
            bucketPath.Close();
        }

        // Arc to suggest the hidden side
        bucketPath.MoveTo(25, -17);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small, 
                         SKPathDirection.Clockwise, 65, -17);
        bucketPath.LineTo(65, -19);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small, 
                         SKPathDirection.CounterClockwise, 25, -19);
        bucketPath.Close();

        // Make it a little bigger and correct the orientation
        bucketPath.Transform(SKMatrix.MakeScale(-2, 2));
        bucketPath.Transform(SKMatrix.MakeRotationDegrees(90));
    }
    ...
```

存储桶创建代码后，进行稍有更大的存储桶侧向打开两个转换完成。 应用这些转换时比调整前面的代码中的所有坐标。 

`PaintSurface`处理程序开始通过定义本身的传送带的路径。 这是只需一对行和分号绘制有 20 个像素宽暗灰色线的圆圈对：

```csharp
public class ConveyorBeltPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float width = info.Width / 3;
        float verticalMargin = width / 2 + 150;

        using (SKPath conveyerPath = new SKPath())
        {
            // Straight verticals capped by semicircles on top and bottom
            conveyerPath.MoveTo(width, verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large, 
                               SKPathDirection.Clockwise, 2 * width, verticalMargin);
            conveyerPath.LineTo(2 * width, info.Height - verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large, 
                               SKPathDirection.Clockwise, width, info.Height - verticalMargin);
            conveyerPath.Close();

            // Draw the conveyor belt itself
            canvas.DrawPath(conveyerPath, conveyerPaint);

            // Calculate spacing based on length of conveyer path
            float length = 2 * (info.Height - 2 * verticalMargin) +
                           2 * ((float)Math.PI * width / 2);

            // Value will be somewhere around 200
            float spacing = length / (float)Math.Round(length / 200);

            // Now animate the phase; t is 0 to 1 every 2 seconds
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 2 / 2);
            float phase = -t * spacing;

            // Create the buckets PathEffect
            using (SKPathEffect bucketsPathEffect = 
                        SKPathEffect.Create1DPath(bucketPath, spacing, phase, 
                                                  SKPath1DPathEffectStyle.Rotate))
            {
                // Set it to the Paint object and draw the path again
                bucketsPaint.PathEffect = bucketsPathEffect;
                canvas.DrawPath(conveyerPath, bucketsPaint);
            }
        }
    }
}
```

在横向模式下不工作绘制传送带的逻辑。

存储桶应该大约为 200 像素相隔上传送带用空白分隔。 但是，传送带可能不是为 200 像素长，这意味着作为倍数`phase`参数`SKPathEffect.Create1DPath`是进行动画处理，存储桶会弹出流入和流出是否存在。 

因此，程序将首先计算名为的值`length`即传送带的长度。 由于传送带由直线和用圆圈组成，这是一种简单运算。 接下来，通过存储桶数除以`length`通过 200。 此值将舍入为最接近的整数，而数是然后划分成`length`。 结果是不可或缺的存储桶数量间距。 `phase`参数是只需的一小部分。

## <a name="from-path-to-path-again"></a>从路径再次路径

在底部`DrawSurface`中的处理程序**传送带**，注释掉`canvas.DrawPath`调用并将其替换为以下代码：

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

前面的示例与`GetFillPath`，你将看到的结果都是相同的颜色除外。 执行后`GetFillPath`、`newPath`对象包含的存储桶路径的多个副本，每个将放置在相同的地点，动画的优势，它们在调用时。

## <a name="hatching-an-area"></a>阴影区域

[ `SKPathEffect.Create2DLines` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DLine/p/System.Single/SkiaSharp.SKMatrix/)方法填充一块区域与平行直线，通常称为*剖面线*。 该方法具有以下语法：

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

`width`参数指定阴影行的描边宽度。 `matrix`参数是缩放和可选旋转的组合。 缩放因子指示像素，递增该 Skia 用于空间的阴影行。 行之间的分离是减的缩放因子`width`自变量。 缩放因子是否小于或等于`width`值之间的阴影行中，将没有空间和区域将显示要填充。 指定水平和垂直缩放的相同值。 

默认情况下，阴影行是水平。 如果`matrix`参数包含旋转、 阴影行是否沿顺时针方向旋转。

**阴影填充**页演示此路径效果。 [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs)类作为字段定义三个路径效果，它们是水平阴影线宽度为 3 个像素的缩放系数表示第一个间距相隔 6 个像素。 因此，行之间的分离是 3 个像素。 第二个路径效果是宽度为 6 个像素的垂直阴影行间距 24 像素相隔 （因此分离是 18 个像素），以及第三个是否是的对角线阴影行 12 像素宽间距 36 像素分离。 

```csharp
public class HatchFillPage : ContentPage
{
    SKPaint fillPaint = new SKPaint();

    SKPathEffect horzLinesPath = SKPathEffect.Create2DLine(3, SKMatrix.MakeScale(6, 6));

    SKPathEffect vertLinesPath = SKPathEffect.Create2DLine(6, 
        Multiply(SKMatrix.MakeRotationDegrees(90), SKMatrix.MakeScale(24, 24)));

    SKPathEffect diagLinesPath = SKPathEffect.Create2DLine(12, 
        Multiply(SKMatrix.MakeScale(36, 36), SKMatrix.MakeRotationDegrees(45)));

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target;
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

请注意矩阵`Multiply`方法。 水平和垂直比例因子是相同的因为在其中缩放和旋转矩阵相乘顺序并不重要。

`PaintSurface`处理程序使用三个不同的颜色与结合使用这些三个路径效果`fillPaint`以填充大小调整，以使其适应页面一个圆角的矩形。 `Style`属性设置的`fillPaint`将被忽略; 当`SKPaint`对象包括从创建一个路径效果`SKPathEffect.Create2DLine`，而不考虑填充的区域：

```csharp
public class HatchFillPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath roundRectPath = new SKPath())
        {
            // Create a path 
            roundRectPath.AddRoundedRect(
                new SKRect(50, 50, info.Width - 50, info.Height - 50), 100, 100);

            // Horizontal hatch marks
            fillPaint.PathEffect = horzLinesPath;
            fillPaint.Color = SKColors.Red;
            canvas.DrawPath(roundRectPath, fillPaint); 

            // Vertical hatch marks
            fillPaint.PathEffect = vertLinesPath;
            fillPaint.Color = SKColors.Blue;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Diagonal hatch marks -- use clipping
            fillPaint.PathEffect = diagLinesPath;
            fillPaint.Color = SKColors.Green;

            canvas.Save();
            canvas.ClipPath(roundRectPath);
            canvas.DrawRect(new SKRect(0, 0, info.Width, info.Height), fillPaint);
            canvas.Restore();

            // Outline the path
            canvas.DrawPath(roundRectPath, strokePaint);
        }
    }
    ...
}
```

如果你仔细查看结果，你将看到红色和蓝色阴影行不局限精确于圆角矩形。 （这是显然是基础的 Skia 代码的特征。）如果这是让人不满意，对角线阴影线以绿色显示另一种方法： 圆角的矩形用作剪切路径，并且在整个页面上绘制阴影行。

`PaintSurface`处理程序结束调用来只需描边，则圆角的矩形，以便你可以查看具有红色和蓝色阴影行差异：

[![](effects-images/hatchfill-small.png "三重的阴影填充页的屏幕截图")](effects-images/hatchfill-large.png "三倍的阴影填充页的屏幕截图")

Android 屏幕不真正的外观： 屏幕快照的缩放已导致的红色的细线和精简的空间将整合到看似更广泛红线和更多空间。

## <a name="filling-with-a-path"></a>具有路径的填充

[ `SKPathEffect.Create2DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DPath/p/SkiaSharp.SKMatrix/SkiaSharp.SKPath/)允许你以填充区域的水平或垂直地复制路径实际上平铺区域：

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

`SKMatrix`比例因子指示复制路径的水平和垂直间距。 但你无法旋转使用此路径`matrix`自变量; 如果你想转动的路径旋转路径本身使用`Transform`方法由定义`SKPath`。 

复制的路径通常与屏幕，而不是所填充的区域的左侧和顶部边缘对齐。 可以通过提供介于 0 和要指定从左和最高四条边的水平和垂直偏移量的缩放因素之间的转换因素来重写此行为。

**路径磁贴填充**页演示此路径效果。 使用用于平铺区域的路径定义中的一个字段为[ `PathFileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs)类。 水平和垂直坐标范围从 –40 到 40，这意味着此路径为正方形的 80 像素： 

```csharp
public class PathTileFillPage : ContentPage
{
    SKPath tilePath = SKPath.ParseSvgPathData(
        "M -20 -20 L 2 -20, 2 -40, 18 -40, 18 -20, 40 -20, " + 
        "40 -12, 20 -12, 20 12, 40 12, 40 40, 22 40, 22 20, " + 
        "-2 20, -2 40, -20 40, -20 8, -40 8, -40 -8, -20 -8 Z");
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Red;

            using (SKPathEffect pathEffect =
                   SKPathEffect.Create2DPath(SKMatrix.MakeScale(64, 64), tilePath))
            {
                paint.PathEffect = pathEffect;

                canvas.DrawRoundRect(
                    new SKRect(50, 50, info.Width - 50, info.Height - 50), 
                    100, 100, paint);
            }
        }
    }
}
```

在`PaintSurface`处理程序，`SKPathEffect.Create2DPath`调用将水平和垂直间距设置为 64 导致 80 像素正方形磁贴重叠。 幸运的是，路径将类似于拼图，接合很好地与相邻的磁贴：

[![](effects-images/pathtilefill-small.png "三重的路径磁贴填充页的屏幕截图")](effects-images/pathtilefill-large.png "三重的路径磁贴填充页的屏幕截图")

从原始的屏幕截图缩放导致某些失真，特别是在 Android 屏幕上。

请注意，这些磁贴将始终显示整个而且从不会被截断。 除了在 Windows 10 移动版屏幕上，它不是甚至明显所填充的区域是一个圆角的矩形。 如果要进行截断操作的特定区域这些磁贴，使用剪切路径。

请尝试使用设置`Style`属性`SKPaint`对象传递给`Stroke`，你会看到所述，而不是填充单个的磁贴。

## <a name="rounding-sharp-corners"></a>舍入尖锐的角

**舍入 Heptagon**中提供程序[**绘制一段弧线，三个方法**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)文章用于切线弧线曲线七侧图的点。 **另一个舍入 Heptagon**页显示使用从创建一个路径效果的更简单的方法[ `SKPathEffect.CreateCorner` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCorner/p/System.Single/)方法：

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

尽管为单个自变量，但`radius`必须将其设置为半的所需的圆角半径。 （这是基础的 Skia 代码的特征。）

下面是`PaintSurface`中的处理程序[ `AnotherRoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs)类：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);
    SKPoint[] vertices = new SKPoint[numVertices];
    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    float cornerRadius = 100;

    // Create the path
    using (SKPath path = new SKPath())
    {
        path.AddPoly(vertices, true);

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            // Set argument to half the desired corner radius!
            paint.PathEffect = SKPathEffect.CreateCorner(cornerRadius / 2);

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);

            // Uncomment DrawCircle call to verify corner radius
            float offset = cornerRadius / (float)Math.Sin(Math.PI * (numVertices - 2) / numVertices / 2);
            paint.Color = SKColors.Green;
            // canvas.DrawCircle(vertices[0].X, vertices[0].Y + offset, cornerRadius, paint);
        }
    }
}
```

您可以使用这种效果描边或基于填充`Style`属性`SKPaint`对象。 下面是在所有三个平台上：

[![](effects-images/anotherroundedheptagon-small.png "三重的另一个舍入 Heptagon 页面屏幕截图")](effects-images/anotherroundedheptagon-large.png "三倍的另一个舍入 Heptagon 页面屏幕截图")

你将看到此舍入的 heptagon 等同于早期版本的程序。 如果你需要更可信的圆角半径真正 100 而中指定 50`SKPathEffect.CreateCorner`调用，则可以取消注释最后一条语句中的程序和，请参阅 100 radius 圆角叠加。

## <a name="random-jitter"></a>随机抖动

有时计算机图形完美直线不是了您的需求，并且需要少量的随机性。 在这种情况下，你将需要重[ `SKPathEffect.CreateDiscrete` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDiscrete/p/System.Single/System.Single/System.UInt32/)方法：

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

你可以为描边或填充使用此路径效果。 行分为连接段-由指定的近似长度`segLength`-和扩展在不同的方向。 指定的范围从原始行的偏差`deviation`。 

最后一个参数是用于生成使用效果的伪随机序列的种子。 抖动效果看起来稍有不同的不同的种子。 自变量具有默认值为零，这意味着只要运行程序，效果都相同。 如果你想不同抖动，每当重新绘制屏幕，你可以设置种子为`Millisecond`属性`DataTime.Now`值 （例如）。


**抖动试验**页面允许您的描边矩形中的不同值进行试验：

[![](effects-images/jitterexperiment-small.png "仅使用三重抖动试验页面的屏幕截图")](effects-images/jitterexperiment-large.png "Triple screenshot of the JitterExperiment page")

Straightfoward 程序。 [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml)文件实例化两个`Slider`元素和`SKCanvasView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.JitterExperimentPage"
             Title="Jitter Experiment">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Minimum" Value="0" />
                    <Setter Property="Maximum" Value="100" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="segLengthSlider"
                Grid.Row="0"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference segLengthSlider},
                              Path=Value,
                              StringFormat='Segment Length = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="deviationSlider"
                Grid.Row="2"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference deviationSlider},
                              Path=Value,
                              StringFormat='Deviation = {0:F0}'}"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

`PaintSurface`中的处理程序[ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs)代码隐藏文件称为每当`Slider`值更改。 它调用`SKPathEffect.CreateDiscrete`使用两个`Slider`值，并使用该值来绘制矩形：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float segLength = (float)segLengthSlider.Value;
    float deviation = (float)deviationSlider.Value;

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke; 
        paint.StrokeWidth = 5;
        paint.Color = SKColors.Blue;

        using (SKPathEffect pathEffect = SKPathEffect.CreateDiscrete(segLength, deviation))
        {
            paint.PathEffect = pathEffect;

            SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

您可以使用此效果用于填充，在这种情况下填充区域的轮廓的受到这些随机方差。 **抖动文本**页演示了如何使用此路径效果显示文本。 中的代码的大多数`PaintSurface`处理程序[ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs)类专用于大小调整和居中文本：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "FUZZY";

    using (SKPaint textPaint = new SKPaint())
    {
        textPaint.Color = SKColors.Purple;
        textPaint.PathEffect = SKPathEffect.CreateDiscrete(3f, 10f);

        // Adjust TextSize property so text is 95% of screen width
        float textWidth = textPaint.MeasureText(text);
        textPaint.TextSize *= 0.95f * info.Width / textWidth;

        // Find the text bounds
        SKRect textBounds;
        textPaint.MeasureText(text, ref textBounds);

        // Calculate offsets to center the text on the screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

此处它在横向模式中运行所有三个平台上：

[![](effects-images/jittertext-small.png "仅使用三重抖动文本页面的屏幕截图")](effects-images/jittertext-large.png "Triple screenshot of the JitterText page")

## <a name="path-outlining"></a>路径大纲显示

你已经了解的两个小示例[ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/)方法`SKPaint`，其中也存在在[重载](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/):

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale)
```

仅前两个参数是必需的。 方法访问引用的路径`src`自变量，修改基于中的笔划属性对路径数据`SKPaint`对象 (包括`PathEffect`属性)，然后将写入到结果`dst`路径。 `resScale`参数允许创建一个较小的目标路径的精度减少与`cullRect`自变量可以消除外部矩形轮廓。

此方法的一个基本的使用不涉及路径效果。 如果`SKPaint`对象都有其`Style`属性设置为`SKPaintStyle.Stroke`，且不会*不*具有其`PathEffect`设置，则`GetFillPath`创建表示一个路径*大纲*的源路径就像它已描边之后由绘制属性。

例如，如果`src`路径是一个简单的圆的 radius 500，和`SKPaint`对象指定的描边宽度为 100，则`dst`路径成为两个同心圆，一个半径为 450，另一个半径为 550。 此方法叫做`GetFillPath`因为填充这`dst`路径是相同描边`src`路径。 但你可以绘制`dst`路径，若要查看路径轮廓。

**到大纲点击路径**演示这一操作。 `SKCanvasView`和`TapGestureRecognizer`中实例化[ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml)文件。 [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs)代码隐藏文件定义三个`SKPaint`作为两个用于与描边的字段的对象进行描边宽度为 100 和 20 和第三方作为填充：

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    bool outlineThePath = false;

    SKPaint redThickStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 100
    };

    SKPaint redThinStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 20
    };

    SKPaint blueFill = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    public TapToOutlineThePathPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewTapped(object sender, EventArgs args)
    {
        outlineThePath ^= true;
        (sender as SKCanvasView).InvalidateSurface();
    }
    ...
}
```

如果屏幕未已被点击，`PaintSurface`处理程序使用`blueFill`和`redThickStroke`绘制对象来呈现循环路径：

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circlePath = new SKPath())
        {
            circlePath.AddCircle(info.Width / 2, info.Height / 2,
                                 Math.Min(info.Width / 2, info.Height / 2) - 
                                 redThickStroke.StrokeWidth);

            if (!outlineThePath)
            {
                canvas.DrawPath(circlePath, blueFill);
                canvas.DrawPath(circlePath, redThickStroke);
            }
            else
            {
                using (SKPath outlinePath = new SKPath())
                {
                    redThickStroke.GetFillPath(circlePath, outlinePath);

                    canvas.DrawPath(outlinePath, blueFill);
                    canvas.DrawPath(outlinePath, redThinStroke);
                }
            }
        }
    }
}
```

在圆是填充和描边一样：

[![](effects-images/taptooutlinethepathnormal-small.png "正常的点击到大纲 Path 页面的三个屏幕截图")](effects-images/taptooutlinethepathnormal-large.png "正常的点击到大纲 Path 页面的三个屏幕截图")

当你点击屏幕，`outlineThePath`设置为`true`，和`PaintSurface`处理程序创建新`SKPath`对象并将其用作对的调用中的目标路径`GetFillPath`上`redThickStroke`绘制对象。 该目标路径然后填充并用描边`redThinStroke`，这会导致在下面的示例：

[![](effects-images/taptooutlinethepathoutlined-small.png "轮廓包围的点击到大纲 Path 页面的三个屏幕截图")](effects-images/taptooutlinethepathoutlined-large.png "轮廓包围的点击到大纲 Path 页面的三个屏幕截图")

两个的红色圆圈清楚地指示原始循环路径，已转换为两个循环分布。

此方法可能非常有用开发路径用于`SKPathEffect.Create1DPath`方法。 复制路径时，始终将填充这些方法中指定的路径。 如果你不想要填充的整条路径，你必须仔细定义轮廓。

例如，在**链接链**示例，具有四个弧，其中每个对基于两个 radii 概述要填充的路径的区域的一系列定义链接。 可以替换中的代码`LinkedChainPage`类来执行此操作略有不同。

首先，你将需要重新定义`linkRadius`常量：

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

`linkPath`现在基于该单个 radius，与所需的仅使用两个弧开始角度和扫描角度：

```csharp
using (SKPath linkPath = new SKPath())
{
    SKRect rect = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
    linkPath.AddArc(rect, 55, 160);
    linkPath.AddArc(rect, 235, 160);

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = linkThickness;

        using (SKPath outlinePath = new SKPath())
        {
            strokePaint.GetFillPath(linkPath, outlinePath);

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(outlinePath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);

        }

    }
}
```

`outlinePath`对象然后是的轮廓的收件人`linkPath`它时进行中指定的属性与描边`strokePaint`。 

使用此方法的另一个示例为下一步中使用的路径为即将`SKPathEffect.Create2DPath`方法。 

## <a name="combining-path-effects"></a>路径作用结合起来

两种最终静态创建方法`SKPathEffect`是[ `SKPathEffect.CreateSum` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateSum/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/)和[ `SKPathEffect.CreateCompose` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCompose/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

这两种方法组合两个路径效果创建复合路径效果。 `CreateSum`方法创建一个路径效果类似于分别应用的两个路径效果时`CreateCompose`应用一个路径效果 ( `inner`)，然后将应用`outer`于。

你已经了解如何`GetFillPath`方法`SKPaint`可以将一条路径转换为另一个路径基于`SKPaint`属性 (包括`PathEffect`) 使其不应成为*太*神秘如何`SKPaint`对象可以对该操作执行两次的中指定的两个路径效果`CreateSum`或`CreateCompose`方法。

一个明显使用`CreateSum`是定义`SKPaint`: 路径使用一条路径起作用，填充和描边与另一个路径效果的路径的对象。 此进行了演示**帧中的 Cats**示例，后者显示具有扇形边缘 cats 在范围内的数组：

[![](effects-images/catsinframe-small.png "三重的 Cats 中帧页面屏幕截图")](effects-images/catsinframe-large.png "Cats 中帧页面的三个屏幕截图")

[ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs)类开始通过定义多个字段。 你可能会识别的第一个字段从[ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs)类[ **SVG 路径数据**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)文章。 第二个路径基于行和弧线对于帧扇贝模式：

```csharp
public class CatsInFramePage : ContentPage
{
    // From PathDataCatPage.cs
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint catStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5
    };

    SKPath scallopPath = 
        SKPath.ParseSvgPathData("M 0 0 L 50 0 A 60 60 0 0 1 -50 0 Z");

    SKPaint framePaint = new SKPaint
    {
        Color = SKColors.Black
    };
    ...
}
```

`catPath`中可能使用`SKPathEffect.Create2DPath`方法如果`SKPaint`对象`Style`属性设置为`Stroke`。 但是，如果`catPath`使用直接在此程序中，然后 cat 整个头将进行填写，并须线甚至不可见。 （试试看 ！）需要获取该路径的大纲，并使用在该大纲`SKPathEffect.Create2DPath`方法。

构造函数执行此作业。 它会先应用到两个转换`catPath`移动 （0，0） 到中心点，然后向下缩放的大小。 `GetFillPath` 获取的中的分布的所有大纲`outlinedCatPath`，并且在使用该对象`SKPathEffect.Create2DPath`调用。 的缩放因素`SKMatrix`值是略大于水平和垂直大小的 cat 提供磁贴，虽然转换因素之间的小缓冲区派生某种程度上基于以便完整 cat 中是可见的框架窗口左上角：

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    public CatsInFramePage()
    {
        Title = "Cats in Frame";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Move (0, 0) point to center of cat path
        catPath.Transform(SKMatrix.MakeTranslation(-240, -175));

        // Now catPath is 400 by 250
        // Scale it down to 160 by 100
        catPath.Transform(SKMatrix.MakeScale(0.40f, 0.40f));

        // Get the outlines of the contours of the cat path
        SKPath outlinedCatPath = new SKPath();
        catStroke.GetFillPath(catPath, outlinedCatPath);

        // Create a 2D path effect from those outlines
        SKPathEffect fillEffect = SKPathEffect.Create2DPath(
            new SKMatrix { ScaleX = 170, ScaleY = 110,
                           TransX = 75, TransY = 80,
                           Persp2 = 1 },
            outlinedCatPath);

        // Create a 1D path effect from the scallop path
        SKPathEffect strokeEffect = 
            SKPathEffect.Create1DPath(scallopPath, 75, 0, SKPath1DPathEffectStyle.Rotate);

        // Set the sum the effects to frame paint
        framePaint.PathEffect = SKPathEffect.CreateSum(fillEffect, strokeEffect);
    }
    ...
}
```

然后调用构造函数`SKPathEffect.Create1DPath`扇形的帧。 请注意，路径的宽度为 100 个像素，但高级，以便在框架四周重叠的复制的路径，则为 75 像素。 构造函数调用的最后一条语句`SKPathEffect.CreateSum`组合两个路径效果并将结果设置为`SKPaint`对象。

此工作允许`PaintSurface`处理程序是非常简单。 它只需定义一个矩形，并使其使用`framePaint`:

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect rect = new SKRect(50, 50, info.Width - 50, info.Height - 50);
        canvas.ClipRect(rect);
        canvas.DrawRect(rect, framePaint);
    }
}
```

路径效果背后的算法始终导致描边或填充使用若要显示，这可能导致一些视觉对象显示矩形之外的整个路径。 `ClipRect`之前调用`DrawRect`调用允许为显著清洗磁带的视觉对象。 （试试会不经剪辑 ！）

它是常见的是使用`SKPathEffect.CreateCompose`将某些抖动添加到另一个路径效果。 你当然可以试验自己，但下面是一个稍有不同的示例：

**阴影的虚线**替换阴影行，虚线表示填充椭圆。 中的工作的大多数[ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs)执行类中的字段定义的权限。 这些字段定义短划线效果和阴影效果。 它们定义为`static`因为然后中引用`SKPathEffect.CreateCompose`调用`SKPaint`定义：

```csharp
public class DashedHatchLinesPage : ContentPage
{
    static SKPathEffect dashEffect = 
        SKPathEffect.CreateDash(new float[] { 30, 30 }, 0);

    static SKPathEffect hatchEffect = SKPathEffect.Create2DLine(20,
        Multiply(SKMatrix.MakeScale(60, 60), 
                 SKMatrix.MakeRotationDegrees(45)));

    SKPaint paint = new SKPaint()
    {
        PathEffect = SKPathEffect.CreateCompose(dashEffect, hatchEffect),
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target;
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

`PaintSurface`处理程序需要包含仅标准开销加上一次调用`DrawOval`:

```csharp
public class DashedHatchLinesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawOval(info.Width / 2, info.Height / 2, 
                        0.45f * info.Width, 0.45f * info.Height, 
                        paint);
    }
    ...
}
```

一旦已发现的情况下，阴影行不是精确地限制为的区域中，内部，并且在此示例中，它们始终开始，以替换为整个短划线左：

[![](effects-images/dashedhatchlines-small.png "三重的阴影虚线页面屏幕截图")](effects-images/dashedhatchlines-large.png "阴影虚线页面的三个屏幕截图")

现在，你已了解路径影响到有点奇怪的组合，频率范围介于简单点和短划线，使用你 imagination 并查看你可以创建。



## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
