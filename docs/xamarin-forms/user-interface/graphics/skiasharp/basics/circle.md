---
title: SkiaSharp 绘制一个简单的圆圈
description: 本文说明 SkiaSharp 绘制，包括画布和画图，在 Xamarin.Forms 应用程序的基础知识，并演示此示例代码。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: e06a1310fad01da11c8d8b115df504cc19426344
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615218"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>SkiaSharp 绘制一个简单的圆圈

_了解 SkiaSharp 绘制，包括画布和画图的基础知识_

本文介绍在 Xamarin.Forms 中使用 SkiaSharp，包括创建绘制图形的概念`SKCanvasView`对象以承载图形，处理`PaintSurface`事件，以及如何使用`SKPaint`要指定颜色和其他图形对象特性。

[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程序包含本系列的 SkiaSharp 文章的所有示例代码。 第一页是已获授权**简单的圆圈**，并调用 page 类[ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs)。 此代码演示如何在一个半径为 100 像素的页的中心绘制一个圆。 圆的边框为红色，并内部的圆圈将显示为蓝色。

![](circle-images/circleexample.png "以红色标出将蓝色圆形")

[ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) Page 类派生自`ContentPage`和包含两个`using`SkiaSharp 命名空间指令：

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

以下类的构造函数创建[ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/)对象，将附加的处理程序[ `PaintSurface` ](https://developer.xamarin.com/api/event/SkiaSharp.Views.Forms.SKCanvasView.PaintSurface/)事件，并设置`SKCanvasView`对象作为页面的内容：

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvasView`占用页面的整个内容区域。 或者，你可以组合`SKCanvasView`与其他 Xamarin.Forms`View`派生类，你将看到在其他示例。

`PaintSurface`事件处理程序完成所有绘制的。 此方法通常称为多次运行程序时，这样它应保持的所有信息重新创建所需的图形显示：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

[ `SKPaintSurfaceEventArgs` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs/)伴随该事件的对象具有两个属性：

- [`Info`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info/) 类型 [`SKImageInfo`](https://developer.xamarin.com/api/type/SkiaSharp.SKImageInfo/)
- [`Surface`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface/) 类型 [`SKSurface`](https://developer.xamarin.com/api/type/SkiaSharp.SKSurface/)

`SKImageInfo`结构包含信息的绘图图面，最重要的是，它是宽度和高度 （像素）。 `SKSurface`对象表示的绘图图面本身。 在此程序中的绘图图面是视频显示，但在其他程序`SKSurface`对象还可以表示使用 SkiaSharp 上绘制的位图。

最重要的属性`SKSurface`是[ `Canvas` ](https://developer.xamarin.com/api/property/SkiaSharp.SKSurface.Canvas/)类型的[ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/)。 此类是绘图上下文用于执行实际绘制的图形。 `SKCanvas`对象将封装图形状态，其中包括图形转换和剪辑。

下面是典型的开始部分`PaintSurface`事件处理程序：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();
    ...
}

```

[ `Clear` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Clear()/)方法清除在画布上添加透明的颜色。 重载允许您指定背景色的画布。

此处的目标是绘制用蓝色填充的红色圆圈。 由于这一特定图形图像包含两个不同的颜色，因此需要在两个步骤中完成的作业。 第一步是绘制圆的轮廓。 若要指定颜色和其他特征的行，创建并初始化[ `SKPaint` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaint/)对象：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 25
    };
    ...
}
```

[ `Style` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Style/)属性指示你想要的*笔划*行 （在本例中的圆大纲） 而非*填充*内部。 三个成员[ `SKPaintStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaintStyle/)枚举如下所示：

- [`Fill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Fill/)
- [`Stroke`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Stroke/)
- [`StrokeAndFill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.StrokeAndFill/)

默认值为 `Fill`。 第三个选项用于绘制线条笔画和内部填充颜色相同。

设置[ `Color` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Color/)属性的值类型[ `SKColor` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColor/)。 一种方法来获取`SKColor`值是通过将转换 Xamarin.Forms`Color`值设置为`SKColor`值使用扩展方法[ `ToSKColor` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.Extensions.ToSKColor/p/Xamarin.Forms.Color/)。 [ `Extensions` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.Extensions/)类中`SkiaSharp.Views.Forms`命名空间包含的其他方法的 Xamarin.Forms 值和 SkiaSharp 值之间进行转换。

[ `StrokeWidth` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeWidth/)属性指示的行宽度。 此处它被设置为 25 像素。

使用该`SKPaint`对象来绘制圆：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

相对于显示器表面的左上角坐标来指定坐标。 X 坐标向右增加和 Y 坐标增加停止运行。 在讨论有关图形中，通常的数学表示法 （x，y） 用于表示一个点。 点 （0，0） 是显示器表面的左上角，通常称为*原点*。

前两个参数的`DrawCircle`表示圆的中心的 X 和 Y 坐标。 这些分配一半宽度和高度显示图面，将显示图面中心圆的中心。 第三个参数指定圆的半径，并最后一个参数是`SKPaint`对象。

若要填充的圆形内部，可以更改的两个属性`SKPaint`对象，并调用`DrawCircle`试。 此代码还显示了替代方法以获取`SKColor`之一的许多字段的值[ `SKColors` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColors/)结构：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```
这一次，`DrawCircle`调用填充使用的新属性的圆圈`SKPaint`对象。

下面是在 iOS、 Android 和通用 Windows 平台上运行的程序：

[![](circle-images/simplecircle-small.png "三个简单的圆圈页屏幕截图")](circle-images/simplecircle-large.png#lightbox "三个简单的圆圈页屏幕截图")

时自行运行该程序，可以打开手机或模拟器侧向若要查看如何重绘图形。 每次需要重绘图形`PaintSurface`再次调用事件处理程序。

`SKPaint`对象是略高于图形绘制属性的集合。 这些对象是非常轻量。 可以重用`SKPaint`对象，如此程序执行，也可以创建多个`SKPaint`对象的绘制属性的各种组合。 可以创建并初始化这些对象之外`PaintSurface`事件处理程序，并且您可以将其保存为字段页类中。

尽管圆的轮廓的宽度指定为 25 像素&mdash;或四分之一圆的半径&mdash;似乎更精细，并且没有充分的理由： 在行宽度的一半的蓝色圆圈将被遮盖。 参数`DrawCircle`方法定义一个圆形的抽象几何坐标。 蓝色的内部大小调整为该维度与最接近像素，但 25 个像素宽大纲跨越几何圆圈&mdash;一半内部和外部的下半部分上。

中的下一个示例[与 Xamarin.Forms 集成](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)文章演示是以可视方式。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
