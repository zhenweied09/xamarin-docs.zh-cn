---
title: 绘制一个简单圆圈
description: 了解 SkiaSharp 绘图，包括画布和绘制的基础知识
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 402470c3a27ba4327afa6e77336d60748abad436
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="drawing-a-simple-circle"></a>绘制一个简单圆圈

_了解 SkiaSharp 绘图，包括画布和绘制的基础知识_

本文介绍在使用 SkiaSharp，包括创建 Xamarin.Forms 中绘制图形的概念`SKCanvasView`对象以承载图形，处理`PaintSurface`事件，以及如何使用`SKPaint`对象来指定颜色和其他绘制属性。

[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程序包含所有这一系列的 SkiaSharp 文章的示例代码。 第一页有权**简单圆圈**并调用页类[ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs)。 此代码演示如何在一个半径为 100 个像素页的中心绘制一个圆。 轮廓的圆为红色，，圆的内部是蓝色的。

![](circle-images/circleexample.png "用红色蓝色圆圈")

[ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs)页类派生自`ContentPage`和包含两个`using`SkiaSharp 命名空间的指令：

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

类的以下构造函数创建[ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/)对象，将附加的处理程序[ `PaintSurface` ](https://developer.xamarin.com/api/event/SkiaSharp.Views.Forms.SKCanvasView.PaintSurface/)事件，并设置`SKCanvasView`对象作为页面的内容：

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvasView`占用的页的整个内容区域。 或者，你可以组合`SKCanvasView`使用其他 Xamarin.Forms`View`衍生产品，当你将看到在其他示例。

`PaintSurface`事件处理程序是你执行所有绘图的都操作。 此方法通常称为多次运行程序时，因此它应维护的所有信息重新创建所需图形显示：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

[ `SKPaintSurfaceEventArgs` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs/)附带事件的对象具有两个属性：

- [`Info`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info/) 类型 [`SKImageInfo`](https://developer.xamarin.com/api/type/SkiaSharp.SKImageInfo/)
- [`Surface`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface/) 类型 [`SKSurface`](https://developer.xamarin.com/api/type/SkiaSharp.SKSurface/)

`SKImageInfo`结构包含绘图图面，有关的信息最重要的是，它是宽度和高度 （像素）。 `SKSurface`对象表示的绘图图面的本身。 在此程序的绘图图面的是视频显示，但在其他程序`SKSurface`对象还可以表示使用 SkiaSharp 上绘制的位图。

最重要属性`SKSurface`是[ `Canvas` ](https://developer.xamarin.com/api/property/SkiaSharp.SKSurface.Canvas/)类型的[ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/)。 此类是一种图形绘制使用执行实际的绘图的上下文。 `SKCanvas`对象所封装的图形状态，包括图形转换和剪辑。

以下是典型启动`PaintSurface`事件处理程序：

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

[ `Clear` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Clear()/)方法清除在画布上添加透明颜色。 重载允许您指定为 canvas 背景色。

这里的目标是绘制用蓝色填充一个红圈。 由于此特定的图形图像包含两个不同的颜色，作业将需要在两个步骤中，进行。 第一步是绘制圆的轮廓。 若要指定的颜色和行的其他特征，你可以创建和初始化[ `SKPaint` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaint/)对象：

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

[ `Style` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Style/)属性指示要对*描边*（在本例中的圆大纲） 行而不是*填充*内部。 三个成员[ `SKPaintStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaintStyle/)枚举如下所示：

- [`Fill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Fill/)
- [`Stroke`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Stroke/)
- [`StrokeAndFill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.StrokeAndFill/)

默认值为 `Fill`。 第三个选项用于绘制线条笔画和使用相同的颜色填充内部。

设置[ `Color` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Color/)属性类型的值[ `SKColor` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColor/)。 一种方法来获取`SKColor`值是通过将转换 Xamarin.Forms`Color`值赋给`SKColor`值使用扩展方法[ `ToSKColor` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.Extensions.ToSKColor/p/Xamarin.Forms.Color/)。 [ `Extensions` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.Extensions/)类`SkiaSharp.Views.Forms`命名空间包括 Xamarin.Forms 值与 SkiaSharp 值之间进行转换的其他方法。

[ `StrokeWidth` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeWidth/)属性指示线条的粗度。 此处它被设置为 25 像素。

使用该`SKPaint`若要绘制圆形的对象：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

坐标相对于显示图面左上角指定。 X 协调右侧的增加和 Y 坐标增加停止运行。 在讨论有关图形中，通常的数学表示法 （x，y） 用于表示点。 显示面左上角和情况通常称作点 （0，0）*原点*。

前两个自变量`DrawCircle`指示圆的中心的 X 和 Y 坐标。 这些分配一半的宽度和高度显示面以显示面的中心置于的圆的中心。 第三个参数指定圆的半径，最后一个自变量为`SKPaint`对象。

若要填充的圆形的内部，可以更改两个属性`SKPaint`对象并调用`DrawCircle`试。 此代码还演示一种替代方式来获取`SKColor`之一的许多字段的值[ `SKColors` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColors/)结构：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```
此时，`DrawCircle`调用填充使用的新属性的圆圈`SKPaint`对象。

此处是在 iOS、 Android 和通用 Windows 平台上运行的程序：

[![](circle-images/simplecircle-small.png "三重的简单圆圈页面屏幕截图")](circle-images/simplecircle-large.png#lightbox "三倍的简单圆圈页面屏幕截图")

在自行运行程序，你可以启用电话或模拟器，若要查看如何重绘图形。 图形需要重绘，每次`PaintSurface`再次调用事件处理程序。

`SKPaint`对象是略高于图形绘制属性的集合。 这些对象是非常轻量。 你可以重复使用`SKPaint`对象，如此程序执行，或者可以创建多个`SKPaint`绘制属性的各种组合的对象。 你可以创建和初始化这些对象之外`PaintSurface`事件处理程序，也可以将其保存为字段页类中。

尽管圆的轮廓的宽度指定为 25 像素&mdash;或四分之一的圆的半径&mdash;它看起来越窄，并且没有充分的理由： 的行的半角是否被遮盖用蓝色圆圈。 自变量`DrawCircle`方法定义一个圆形的抽象几何坐标。 蓝色内部大小调整为该维度与最近的像素，但 25 个像素宽大纲跨越几何圆圈&mdash;一半内部和外部的下半部分上。

中的下一步示例[集成与 xamarin.forms 结合](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)文章演示这一操作以可视方式。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
