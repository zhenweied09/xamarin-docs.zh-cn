---
title: "多点触控手指跟踪"
description: "本主题演示如何从多个指跟踪触控事件"
ms.topic: article
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 53e972211ce506b6bf32ee4785c853982528d92e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="multi-touch-finger-tracking"></a>多点触控手指跟踪

_本主题演示如何从多个指跟踪触控事件_

有多点触控应用程序需要跟踪各个指随着它们同时在屏幕上的时间。 一个典型的应用程序是一个 finger-paint 程序。 您希望用户能够绘制与为单指，而且还可以同时使用多个指绘制。 如你的程序处理多个触控事件，它需要区分哪些事件对应于每个手指。 Android 提供的 ID 代码用于此目的，但获取和处理该代码可能会稍有很棘手。

对于与特定手指关联的所有事件，ID 代码保持不变。 手指首先触摸屏幕，并从屏幕抬起手指后将变为无效时，分配 ID 代码。
这些 ID 代码是通常很小的整数，并且 Android 将它们重新用于更高版本的触控事件。

几乎始终跟踪各个指程序维护触摸跟踪的字典。 字典的键是标识特定的手指 ID 代码。 字典值取决于应用程序。 在[FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)程序中，每个手指描边 （从与您联系，释放） 是与包含呈现与该手指绘制的线条所需的所有信息的对象相关联。 程序定义一个较小`FingerPaintPolyline`类实现此目的：

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new Path();
    }

    public Color Color { set; get; }

    public float StrokeWidth { set; get; }

    public Path Path { private set; get; }
}
```

每个折线具有一种颜色，描边宽度和 Android 图形[ `Path` ](https://developer.xamarin.com/api/type/Android.Graphics.Path/)对象累积并呈现的行的多个点，为要绘制的方式。

中包含的下面显示的代码的其余部分`View`派生名为`FingerPaintCanvasView`。 类维护类型的对象的字典`FingerPaintPolyline`它们主动正在由一个或多个指绘制时：

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

此字典允许的视图来快速获取`FingerPaintPolyline`与特定手指关联信息。

`FingerPaintCanvasView`类还维护`List`折线的已完成的对象：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

在此对象`List`已绘制了它们的顺序相同。

`FingerPaintCanvasView` 重写由定义的两个方法`View`: [ `OnDraw` ](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/p/Android.Graphics.Canvas/)和[ `OnTouchEvent` ](https://developer.xamarin.com/api/member/Android.Views.View.OnTouchEvent/p/Android.Views.MotionEvent/)。
在其`OnDraw`重写时，视图绘制折线的已完成，然后绘制折线的正在进行中。

重写`OnTouchEvent`方法首先获取`pointerIndex`值从`ActionIndex`属性。 这`ActionIndex`值区分多个指，但它不是一致跨多个事件。 为此，你使用`pointerIndex`获取指针`id`值从`GetPointerId`方法。 此 ID*是*跨多个事件一致性：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        // ...
    }

    // Invalidate to update the view
    Invalidate();

    // Request continued touch input
    return true;
}
```

请注意，重写使用`ActionMasked`中的属性`switch`语句而不是`Action`属性。 原因如下：

当您处理的多点触控`Action`属性具有的值`MotionEventsAction.Down`的第一个手指触摸屏幕上，然后值`Pointer2Down`和`Pointer3Down`如第二个和第三个指还触摸屏幕。 第四个和第五个手指进行联系，`Action`属性具有甚至不对应于的成员的数值`MotionEventsAction`枚举 ！ 你需要检查在要解释它们的含义的值的位标志的值。

同样，因为指与屏幕上，使联系人`Action`属性具有值的`Pointer2Up`和`Pointer3Up`为第二个和第三个手指和`Up`的第一个手指。

`ActionMasked`属性采用到数量较少因为它具有用于与结合使用的值数`ActionIndex`属性来区分多个手指。 当手指触摸屏幕时，该属性可以仅能等于`MotionEventActions.Down`的第一个手指和`PointerDown`为后续指。 因为指使屏幕，`ActionMasked`具有值的`Pointer1Up`为后续指和`Up`的第一个手指。

使用时`ActionMasked`、`ActionIndex`到触摸和离开屏幕中的，但你通常不需要使用除该值中的其他方法的自变量作为后续指区分开来`MotionEvent`对象。 有关多点触控，其中一个的最重要的这些方法是`GetPointerId`上面代码中调用。 方法返回一个值可用于字典的键将指到特定事件相关联。

`OnTouchEvent`中重写[FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)程序进程`MotionEventActions.Down`和`PointerDown`相同通过创建新的事件`FingerPaintPolyline`对象并将其添加到字典：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:

            // Create a Polyline, set the initial point, and store it
            FingerPaintPolyline polyline = new FingerPaintPolyline
            {
                Color = StrokeColor,
                StrokeWidth = StrokeWidth
            };

            polyline.Path.MoveTo(args.GetX(pointerIndex),
                                 args.GetY(pointerIndex));

            inProgressPolylines.Add(id, polyline);
            break;
        // ...
    }
    // ...        
}
```

请注意，`pointerIndex`还可用于获取视图中上方的手指的位置。 与关联的所有触摸信息`pointerIndex`值。 `id`唯一标识跨多个消息，指将的用于创建字典条目。

同样，`OnTouchEvent`还重写句柄`MotionEventActions.Up`和`Pointer1Up`相同通过转移到已完成的折线`completedPolylines`集合以便它们可以在绘制`OnDraw`重写。 代码还会删除`id`从字典的条目：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Up:
        case MotionEventActions.Pointer1Up:

            inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                args.GetY(pointerIndex));

            // Transfer the in-progress polyline to a completed polyline
            completedPolylines.Add(inProgressPolylines[id]);
            inProgressPolylines.Remove(id);
            break;

        case MotionEventActions.Cancel:
            inProgressPolylines.Remove(id);
            break;
    }
    // ...        
}
```

现在为棘手。

向下和之间的事件，通常有许多`MotionEventActions.Move`事件。 这些对单个调用中捆绑`OnTouchEvent`，并且必须从以不同方式处理它们`Down`和`Up`事件。 `pointerIndex`从前面获取的值`ActionIndex`必须忽略属性。 相反，该方法必须获取多个`pointerIndex`对值进行循环介于 0 和`PointerCount`属性，然后获取`id`其中每个`pointerIndex`值：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Move:

            // Multiple Move events are bundled, so handle them differently
            for (pointerIndex = 0; pointerIndex < args.PointerCount; pointerIndex++)
            {
                id = args.GetPointerId(pointerIndex);

                inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                    args.GetY(pointerIndex));
            }
            break;
        // ...
    }
    // ...        
}
```

处理此类允许[FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)程序来跟踪各个指，并在屏幕上绘制结果：

[![FingerPaint 示例中的示例屏幕快照](touch-tracking-images/image01.png)](touch-tracking-images/image01.png)

现在，你已了解如何在屏幕上跟踪各个指和区分它们。


## <a name="related-links"></a>相关链接

- [等效的 Xamarin iOS 指南](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
