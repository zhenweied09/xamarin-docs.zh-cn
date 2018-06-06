---
title: 在 Xamarin.iOS 中跟踪的多点触控手指
description: 本文档介绍如何跟踪各个指在 Xamarin.iOS 应用程序中的多点触控笔势。 它是围绕 finger-painting 应用示例。
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 85dbd3c158408026f4ecef5fb2b01c265747140e
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784398"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>在 Xamarin.iOS 中跟踪的多点触控手指

_本文档演示如何从多个指跟踪触控事件_

有多点触控应用程序需要跟踪各个指随着它们同时在屏幕上的时间。 一个典型的应用程序是一个 finger-paint 程序。 您希望用户能够绘制与为单指，而且还可以同时使用多个指绘制。 如你的程序处理多个触控事件，它需要区分这些指。

当第一次，手指触摸屏幕时，iOS 将创建[ `UITouch` ](https://developer.xamarin.com/api/type/UIKit.UITouch/)该手指的对象。 上方的手指在屏幕上移动，然后将上移开从屏幕中，在哪个点释放此对象是此对象保持不变。 若要跟踪的指，程序应该避免存储这`UITouch`直接对象。 相反，它可以使用[ `Handle` ](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/)类型的属性`IntPtr`来唯一地标识这些`UITouch`对象。

几乎始终跟踪各个指程序维护触摸跟踪的字典。 对于程序 iOS 字典的键是`Handle`值，该值标识特定的手指。 字典值取决于应用程序。 在[FingerPaint](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)程序中，每个手指描边 （从与您联系，释放） 是与包含呈现与该手指绘制的线条所需的所有信息的对象相关联。 程序定义一个较小`FingerPaintPolyline`类实现此目的：

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new CGPath();
    }

    public CGColor Color { set; get; }

    public float StrokeWidth { set; get; }

    public CGPath Path { private set; get; }
}
```

每个折线具有一种颜色、 描边宽度和 iOS 图形[ `CGPath` ](https://developer.xamarin.com/api/type/CoreGraphics.CGPath/)对象累积并呈现的行的多个点，为要绘制的方式。


中包含的下面显示的代码的所有 rest`UIView`派生名为`FingerPaintCanvasView`。 类维护类型的对象的字典`FingerPaintPolyline`它们主动正在由一个或多个指绘制时：

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

此字典允许的视图来快速获取`FingerPaintPolyline`信息与每个手指关联基于`Handle`属性`UITouch`对象。

`FingerPaintCanvasView`类还维护`List`折线的已完成的对象：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

在此对象`List`已绘制了它们的顺序相同。

`FingerPaintCanvasView` 重写由定义的五个方法`View`:

- [`TouchesBegan`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesBegan/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesMoved`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesMoved/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesEnded`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesEnded/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesCancelled`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesCancelled/p/Foundation.NSSet/UIKit.UIEvent/)
- [`Draw`](https://developer.xamarin.com/api/member/UIKit.UIView.Draw/p/CoreGraphics.CGRect/)

各种`Touches`替代积累构成折线的分数。

[`Draw`] 替代绘制折线的已完成，然后折线的正在进行：

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (CGContext context = UIGraphics.GetCurrentContext())
    {
        // Stroke settings
        context.SetLineCap(CGLineCap.Round);
        context.SetLineJoin(CGLineJoin.Round);

        // Draw the completed polylines
        foreach (FingerPaintPolyline polyline in completedPolylines)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }

        // Draw the in-progress polylines
        foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

每个`Touches`替代可能报告的多个指，由一个或多个操作`UITouch`对象存储在`touches`方法自变量。 `TouchesBegan`替代循环访问这些对象。 每个`UITouch`对象，该方法创建并初始化一个新`FingerPaintPolyline`对象，包括存储从获取上方的手指的初始位置`LocationInView`方法。 这`FingerPaintPolyline`对象添加到`InProgressPolylines`字典使用`Handle`属性`UITouch`作为字典键的对象：

```csharp
public override void TouchesBegan(NSSet touches, UIEvent evt)
{
    base.TouchesBegan(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Create a FingerPaintPolyline, set the initial point, and store it
        FingerPaintPolyline polyline = new FingerPaintPolyline
        {
            Color = StrokeColor,
            StrokeWidth = StrokeWidth,
        };

        polyline.Path.MoveToPoint(touch.LocationInView(this));
        inProgressPolylines.Add(touch.Handle, polyline);
    }
    SetNeedsDisplay();
}
```

该方法最后将通过调用`SetNeedsDisplay`生成调用`Draw`重写和更新屏幕。

随着手指在屏幕上，`View`获取多个调用其`TouchesMoved`重写。 此替代同样循环访问`UITouch`对象存储在`touches`自变量并将上方的手指当前位置添加到图形路径：

```csharp
public override void TouchesMoved(NSSet touches, UIEvent evt)
{
    base.TouchesMoved(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Add point to path
        inProgressPolylines[touch.Handle].Path.AddLineToPoint(touch.LocationInView(this));
    }
    SetNeedsDisplay();
}
```

`touches`集合包含仅`UITouch`对象自上次调用移动了指`TouchesBegan`或`TouchesMoved`。 如果您曾需要`UITouch`对象对应于*所有*当前与屏幕联系指，该信息是可通过`AllTouches`属性`UIEvent`方法自变量。

`TouchesEnded`替代具有两个作业。 它必须将最后一个点添加到的图形路径和传输`FingerPaintPolyline`对象`inProgressPolylines`字典`completedPolylines`列表：

```csharp
public override void TouchesEnded(NSSet touches, UIEvent evt)
{
    base.TouchesEnded(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Get polyline from dictionary and remove it from dictionary
        FingerPaintPolyline polyline = inProgressPolylines[touch.Handle];
        inProgressPolylines.Remove(touch.Handle);

        // Add final point to path and save with completed polylines
        polyline.Path.AddLineToPoint(touch.LocationInView(this));
        completedPolylines.Add(polyline);
    }
    SetNeedsDisplay();
}
```

`TouchesCancelled`替代由只需放弃`FingerPaintPolyline`字典中的对象：

```csharp
public override void TouchesCancelled(NSSet touches, UIEvent evt)
{
    base.TouchesCancelled(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        inProgressPolylines.Remove(touch.Handle);
    }
    SetNeedsDisplay();
}
```

此处理一起，允许[FingerPaint](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)程序来跟踪各个指，并在屏幕上绘制结果：

[![](touch-tracking-images/image01.png "跟踪单个指并在屏幕上绘制结果")](touch-tracking-images/image01.png#lightbox)

现在，你已了解如何在屏幕上跟踪各个指和区分它们。



## <a name="related-links"></a>相关链接

- [等效的 Xamarin Android 指南](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint （示例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
