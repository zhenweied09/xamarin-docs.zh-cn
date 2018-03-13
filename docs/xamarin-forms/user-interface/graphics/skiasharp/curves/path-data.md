---
title: "SVG 路径数据"
description: "定义使用的可缩放的向量图形格式的文本字符串的路径"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: charlespetzold
ms.author: chape
ms.date: 05/24/2017
ms.openlocfilehash: cc21f789d96f9375655766916f80c6f3f524c29d
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="svg-path-data"></a>SVG 路径数据

_定义使用的可缩放的向量图形格式的文本字符串的路径_

`SKPath`类支持可缩放向量图形 (SVG) 规范建立格式的文本字符串中的整个路径对象的定义。 你将看到在本文后面的整个路径，例如这个文本字符串中的表示方式：

![](path-data-images/pathdatasample.png "定义与 SVG 路径数据示例路径")

SVG 是编程语言的网页的基于 XML 的图形。 SVG 必须允许在标记，而不是一系列函数调用中定义的路径，因为标准 SVG 包括的文本字符串形式指定一个整个图形路径非常简洁的方式。

在 SkiaSharp，此格式被称为"SVG 路径的数据。" 格式还支持在基于 Windows XAML 的编程环境中，包括 Windows Presentation Foundation 和通用 Windows 平台上，名为[路径标记语法](https://msdn.microsoft.com/library/ms752293%28v=vs.110%29.aspx)或[移动绘制命令语法和](/windows/uwp/xaml-platform/move-draw-commands-syntax/)。 它还可以充当矢量图形图像，尤其是在基于文本的文件，如 XML 交换格式。

SkiaSharp 定义单词的两个方法`SvgPathData`在其名称中：

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

静态[ `ParseSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ParseSvgPathData/p/System.String/)方法将字符串转换为`SKPath`对象，而[ `ToSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ToSvgPathData()/)将转换`SKPath`为 string 的对象。

下面是一个五-星以点为中心 （0，0） 一个半径为 100 SVG 字符串：

```csharp
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

字母均为生成的命令`SKPath`对象。 `M` 指示`MoveTo`调用，`L`是`LineTo`，和`Z`是`Close`关闭轮廓。 每个编号对提供的点的 X 和 Y 坐标。 请注意，`L`命令后跟用逗号分隔的多个点。 在一系列坐标和点、 逗号和空格将被视为相同。 某些程序员倾向将逗号之间的 X 和 Y 坐标，而不是各点之间但逗号或空格只需避免多义性。 这是完全合法的：

```csharp
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

在正式记录的 SVG 路径数据语法[SVG 规范的部分 8.3](http://www.w3.org/TR/SVG11/paths.html#PathData)。 下面是摘要：

## <a name="moveto"></a>**MoveTo**

```csharp
M x y
```

这可以通过设置当前位置开始路径中的一个新轮廓。 路径数据应始终以开头`M`命令。

## <a name="lineto"></a>**LineTo**

```csharp
L x y ...
```

此命令向该路径中添加一条直线 （或行），并将新的当前位置设置为的最后一行的末尾。 你可以按照`L`命令具有多个对*x*和*y*坐标。

## <a name="horizontal-lineto"></a>**水平线条**

```csharp
H x ...
```

此命令向该路径中添加一条水平线，并将新的当前位置设置为行的末尾。 你可以按照`H`与多个命令*x*坐标，但它不太大意义。

## <a name="vertical-line"></a>**垂直行**

```csharp
V y ...
```

此命令将垂直的行添加到路径，并将新的当前位置设置为行的末尾。

## <a name="close"></a>**关闭**

```csharp
Z
```

`C`命令关闭轮廓通过从当前位置到轮廓线的开头添加一条直线。

## <a name="arcto"></a>**ArcTo**

命令将一条椭圆弧添加到具有轮廓到目前为止是整个 SVG 路径数据规范中的最复杂命令。 它是在该数字可表示坐标值以外的唯一命令：

```csharp
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

*Rx*和*ry*参数是椭圆的水平和垂直半径。 *旋转角度*是以度为单位顺时针旋转。

设置*大型弧线标志*为 1。 对于大型弧线或小弧线的 0。

设置*扫描标志*为 1。 对于沿顺时针方向和为 0 以逆时针旋转。

到点绘制弧 (*x*， *y*)，它将变为新的当前位置。

## <a name="cubicto"></a>**CubicTo**

```csharp
C x1 y1 x2 y2 x3 y3 ...
```

此命令将从当前位置添加三次方贝塞尔曲线 (*x3*， *y3*)，它将变为新的当前位置。 点 (*x1*， *y1*) 和 (*x2*， *y2*) 的控制点。

可以通过单个指定多个贝塞尔曲线`C`命令。 点的数目必须是 3 的倍数。

此外还有一个"平滑"的贝塞尔曲线命令：

```csharp
S x2 y2 x3 y3 ...
```

此命令应遵循常规的贝塞尔命令 （尽管不是严格要求的）。 平滑贝塞尔命令计算的第一个控制点，以便围绕其相互点以前的贝塞尔曲线的第二个控制点的反射。 这些三个点因此共线，也是平滑的两条贝塞尔曲线之间的连接。

## <a name="quadto"></a>**QuadTo**

```csharp
Q x1 y1 x2 y2 ...
```

为二次贝塞尔曲线点数目必须是 2 的倍数。 控点是 (*x1*， *y1*) 的终结点 （和新的当前位置） 和 (*x2*， *y2*)

此外，还有平滑二次曲线命令：

```csharp
T x2 y2 ...
```

根据前一二次条曲线的控制点计算的控制点。

所有这些命令也会出现在"相对"版本中，坐标点相对于当前的位置的位置。 这些相对命令小写字母开头，例如`c`而非`C`三次方贝塞尔命令的相对版本。

这是 SVG 路径数据定义的范围。 没有任何工具用于重复组的命令或执行计算的任何类型。 命令`ConicTo`或其他类型的圆弧规范不可用。

静态[ `SKPath.ParseSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ParseSvgPathData/p/System.String/)方法需要一个有效的 SVG 命令字符串。 如果检测到任何语法错误，该方法返回`null`。 这是唯一的错误指示。

[ `ToSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ToSvgPathData()/)方法很适用获取 SVG 路径数据从现有`SKPath`对象将传输到另一个程序，或以存储中基于文本的文件格式，如 XML。 (`ToSvgPathData`这篇文章中的代码示例不演示方法。)执行*不*预期`ToSvgPathData`返回完全对应的方法调用，创建路径的字符串。 具体而言，你将发现弧将转换为多个`QuadTo`命令，并且这是其返回的路径数据中的显示方式`ToSvgPathData`。

**路径数据 Hello**页上 spells 出单词"HELLO"使用 SVG 路径数据。 同时`SKPath`和`SKPaint`对象定义为中的字段[ `PathDataHelloPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs)类：

```csharp
public class PathDataHelloPage : ContentPage
{
    SKPath helloPath = SKPath.ParseSvgPathData(
        "M 0 0 L 0 100 M 0 50 L 50 50 M 50 0 L 50 100" +                // H
        "M 125 0 C 60 -10, 60 60, 125 50, 60 40, 60 110, 125 100" +     // E
        "M 150 0 L 150 100, 200 100" +                                  // L
        "M 225 0 L 225 100, 275 100" +                                  // L
        "M 300 50 A 25 50 0 1 0 300 49.9 Z");                           // O

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ...
}
```

定义文本字符串的路径开始在点 （0，0） 的左上角。 每个字母都是宽的 50 个单位和 100 个单位高，并且字母分隔通过另一个的 25 个单元，这意味着整个路径是 350 个单位宽。

'Hello"'H' 组成三个单行分布，而 E 是两个连接的三次方贝塞尔曲线。 请注意，`C`命令后面跟有六个点和两个控制点具有 – 10 和 110，将其置于其他字母的 Y 坐标的范围的 Y 坐标。 L 是两个连接的直线，而 O 是呈现所使用的椭圆`A`命令。

请注意，`M`开始最后一个轮廓线的命令将位置设置为点 （350，50），这是左侧的垂直中心侧的 O。 后面的第一个数字所述`A`命令时，该椭圆具有 25 的水平半径和 50 的垂直半径。 终结点将由中的数字的最后一个对`A`命令，它表示点 （300，49.9）。 这是有意只需稍有不同的起始点。 如果终结点设置为等于起始点，将不呈现圆弧。 若要绘制整个椭圆，你必须设置终结点关闭 （但不是等于） 的起始点，或你必须使用两个或多`A`命令时，每个完整的椭圆的一部分。

你可能想要将以下语句添加到页面的构造函数，然后设置断点，以检查生成的字符串：

```csharp
string str = helloPath.ToSvgPathData();
```

你将发现弧线已替换的一长串`Q`弧线使用二次贝塞尔曲线的段落近似的命令。

`PaintSurface`处理程序获取紧密边界的路径，这不包括的控点的 E 和 O 曲线。 三种转换移动到的点 （0，0） 的路径的中心、 缩放的画布 （但还考虑描边宽度），大小的路径，然后将该路径的中心移动到画布的中心：

```csharp
public class PathDataHelloPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect bounds;
        helloPath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(info.Width / (bounds.Width + paint.StrokeWidth),
                     info.Height / (bounds.Height + paint.StrokeWidth));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(helloPath, paint);
    }
}
```

路径填充画布，看起来更合理在横向模式中查看时：

[![](path-data-images/pathdatahello-small.png "路径数据 Hello 页面的三个屏幕截图")](path-data-images/pathdatahello-large.png#lightbox "路径数据 Hello 页面的三个屏幕截图")

**路径数据 Cat**页是类似。 路径和绘制对象定义为字段在[ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs)类：

```csharp
public class PathDataCatPage : ContentPage
{
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

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 5
    };
    ...
}
```

Cat 头是一个圆形，并使用两个呈现此处`A`命令，其中每个绘制一个半圆。 同时`A`命令头定义为 100 的水平和垂直半径。 第一个圆弧开始 （240，100），并在结束 （240，300），它将变为结束回位于第二个弧线的起始点 （240，100）。

使用两个还呈现了两个眼睛`A`命令，并与 cat 的 head，第二个`A`命令结尾与第一个开始相同的点`A`命令。 但是，这些对`A`命令不会定义一个椭圆。 与每个弧的为 40 单位和 radius 还有 40 单位，这意味着这些弧不完整 semicircles。

`PaintSurface`处理程序与上面的示例中，执行类似的转换，但设置单个`Scale`要保持纵横比，并提供很少边距，以便 cat 的须线不触摸屏幕两侧因素：

```csharp
public class PathDataCatPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        SKRect bounds;
        catPath.GetBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(0.9f * Math.Min(info.Width / bounds.Width,
                                     info.Height / bounds.Height));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(catPath, paint);
    }
}
```

下面是在所有三个平台上运行的程序：

[![](path-data-images/pathdatacat-small.png "三重的路径数据 Cat 页面屏幕截图")](path-data-images/pathdatacat-large.png#lightbox "路径数据 Cat 页面的三个屏幕截图")

通常，当`SKPath`对象定义为字段，必须在构造函数或另一种方法定义的路径的轮廓。 当使用 SVG 路径数据，但是，你已了解可以完全在字段定义指定的路径。

早期版本**繁琐模拟时钟**示例[**旋转转换**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)文章显示为简单的线条的时钟指针。 **非常模拟时钟**下面的程序会将替换与这些行`SKPath`对象定义中的字段为[ `PrettyAnalogClockPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs)类和`SKPaint`对象：

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    // Clock hands pointing straight up
    SKPath hourHandPath = SKPath.ParseSvgPathData(
        "M 0 -60 C   0 -30 20 -30  5 -20 L  5   0" +
                "C   5 7.5 -5 7.5 -5   0 L -5 -20" +
                "C -20 -30  0 -30  0 -60 Z");

    SKPath minuteHandPath = SKPath.ParseSvgPathData(
        "M 0 -80 C   0 -75  0 -70  2.5 -60 L  2.5   0" +
                "C   2.5 5 -2.5 5 -2.5   0 L -2.5 -60" +
                "C 0 -70  0 -75  0 -80 Z");

    SKPath secondHandPath = SKPath.ParseSvgPathData(
        "M 0 10 L 0 -80");

    // SKPaint objects
    SKPaint handStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2,
        StrokeCap = SKStrokeCap.Round
    };

    SKPaint handFillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Gray
    };
    ...
}
```

小时和分钟手现在括了区域，因此，若要使这些之手彼此不同，它们将绘制了黑色边框和灰色填充使用`handStrokePaint`和`handFillPaint`对象。

在早期版本**繁琐模拟时钟**示例的一小圆圈标记小时和分钟绘制在循环中。 在此**非常模拟时钟**示例，使用完全不同的方法： 小时和分钟标记是绘制的虚线`minuteMarkPaint`和`hourMarkPaint`对象：

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    SKPaint minuteMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 3 * 3.14159f }, 0)
    };

    SKPaint hourMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 6,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 15 * 3.14159f }, 0)
    };
    ...
}
```

[**三点和短划线**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)指南讨论如何使用`SKPathEffect.CreateDash`方法来创建为虚线。 第一个参数是`float`通常有两个元素的数组： 第一个元素是短划线，长度和第二个元素是短划线之间的差距。 当`StrokeCap`属性设置为`SKStrokeCap.Round`，然后的舍入后的端的短划线有效地延长 dash 长度短划线两端的描边宽度。 因此，将第一个数组元素设置为 0 创建点线。

这些点之间的距离受第二个数组元素。 如很快就会看到，这些两个`SKPaint`对象用于绘制一个半径为 90 个单位的圆圈。 因此，此圆的周长是 180π，这意味着在 60 分钟标记必须显示每个 3 π 单位，这是中的第二个值`float`数组中`minuteMarkPaint`。 12 个小时标记必须显示每个 15π 单位，即在第二个值`float`数组。

`PrettyAnalogClockPage`类设置一个计时器，以使其无效面每隔 16 毫秒，和`PaintSurface`该速率调用处理程序。 早期定义`SKPath`和`SKPaint`对象允许的非常简洁绘制代码：

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Transform for 100-radius circle in center
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / 200, info.Height / 200));

        // Draw circles for hour and minute marks
        SKRect rect = new SKRect(-90, -90, 90, 90);
        canvas.DrawOval(rect, minuteMarkPaint);
        canvas.DrawOval(rect, hourMarkPaint);

        // Get time
        DateTime dateTime = DateTime.Now;

        // Draw hour hand
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawPath(hourHandPath, handStrokePaint);
        canvas.DrawPath(hourHandPath, handFillPaint);
        canvas.Restore();

        // Draw minute hand
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawPath(minuteHandPath, handStrokePaint);
        canvas.DrawPath(minuteHandPath, handFillPaint);
        canvas.Restore();

        // Draw second hand
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        canvas.Save();
        canvas.RotateDegrees(6 * (dateTime.Second + (float)t));
        canvas.DrawPath(secondHandPath, handStrokePaint);
        canvas.Restore();
    }
}
```

特殊的内容是使用进行第二个指针，但是。 因为在更新时钟每隔 16 毫秒，`Millisecond`属性`DateTime`值可能被用于第二个手动动画扫描，而不是一个在离散跳转中移动从第二个为第二个。 但此代码不允许是平滑的移动。 相反，它使用 Xamarin.Forms [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/)和[ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/)动画的缓动函数的一种不同的移动。 这些缓动函数会导致第二个指针在的 jerkier 的方式 （&） #x 2014; 中移动只能回滚一个小之前它将移动，并且然后略有这遗憾的过度解决其目标，效果无法重现这些静态的屏幕截图中：

[![](path-data-images/prettyanalogclock-small.png "三重的非常模拟时钟页面屏幕截图")](path-data-images/prettyanalogclock-large.png#lightbox "非常模拟时钟页面的三个屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
