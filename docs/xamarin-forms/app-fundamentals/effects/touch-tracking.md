---
title: 调用效果中的事件
description: 一种效果可以定义和调用的事件，信号基础本机视图中的更改。 本文介绍如何实现低级别多点触控手指跟踪，以及如何生成信号触摸活动的事件。
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 0a5e2c1a7a7807da91fd98e617467ea251a25bc0
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527399"
---
# <a name="invoking-events-from-effects"></a>调用效果中的事件

_一种效果可以定义和调用的事件，信号基础本机视图中的更改。本文介绍如何实现低级别多点触控手指跟踪，以及如何生成信号触摸活动的事件。_

本文中所述的效果提供低级的触摸事件的访问权限。 这些低级别的事件不是可通过现有`GestureRecognizer`类，但它们是对某些类型的应用程序至关重要。 例如，finger-paint 跟踪各手指，因为它们在屏幕上移动的应用程序需要。 音乐键盘需要检测分流点，并释放上各个项，以及从一个密钥为另一种滑奏 gliding 手指。

效果非常适用于多点触控手指跟踪，因为可以将它附加到 Xamarin.Forms 的任何元素。

## <a name="platform-touch-events"></a>平台触摸事件

IOS、 Android 和通用 Windows 平台都包括一个低级别 API，允许应用程序以检测触摸活动。 所有的三种基本类型之间区分这些平台触摸事件：

- *按下*，当有手指触摸屏幕
- *移动*、 当移动手指触摸屏幕
- *发布*，当手指松开的屏幕

在多点触控环境中，多个手指可同时触摸屏幕。 各种平台包括应用程序可用于区分多个手指标识 (ID) 号。

在 iOS 中，`UIView`类定义了三种可重写方法`TouchesBegan`， `TouchesMoved`，和`TouchesEnded`与这三个基本事件相对应。 文章[多点触控手指跟踪](~/ios/app-fundamentals/touch/touch-tracking.md)介绍如何使用这些方法。 但是，iOS 程序不需要重写派生类`UIView`使用这些方法。 IOS`UIGestureRecognizer`还定义了这些相同三种方法，并且您可以将派生的类的实例附加`UIGestureRecognizer`任何`UIView`对象。

在 Android 中，`View`类定义可重写方法名为`OnTouchEvent`来处理所有触摸活动。 触摸活动的类型定义的枚举成员`Down`， `PointerDown`， `Move`， `Up`，并`PointerUp`文章中所述[多点触控手指跟踪](~/android/app-fundamentals/touch/touch-tracking.md)。 Android`View`还定义了名为的事件`Touch`允许事件处理程序附加到任何`View`对象。

通用 Windows 平台 (UWP) 中，`UIElement`类定义名为事件`PointerPressed`， `PointerMoved`，和`PointerReleased`。 这些文章中所述[MSDN 上的处理指针输入文章](/windows/uwp/input-and-devices/handle-pointer-input/)和 API 文档[ `UIElement` ](/uwp/api/windows.ui.xaml.uielement/)类。

`Pointer`中通用 Windows 平台的 API 旨在统一鼠标、 触控和笔输入。 为此，`PointerMoved`在即使在未按下鼠标按钮时，鼠标移过元素时调用事件。 `PointerRoutedEventArgs`通常会显示这些事件的对象有一个名为`Pointer`具有一个名为属性`IsInContact`指示是否按下鼠标或手指的屏幕。

此外，UWP 定义两个名为的更多事件`PointerEntered`和`PointerExited`。 这些错误表示当鼠标或手指移动从一个元素到另一个。 例如，考虑两个相邻元素名为 A 和 b。两个元素都已安装指针事件处理的程序。 当手指按下 A 上,`PointerPressed`调用事件。 当手指移动时，一个调用`PointerMoved`事件。 如果手指移动从 A 到 B，A 调用`PointerExited`事件和 B 调用`PointerEntered`事件。 如果手指然后释放，B 调用`PointerReleased`事件。

IOS 和 Android 平台是不同于 UWP： 首先获取对的调用的视图`TouchesBegan`或`OnTouchEvent`时上方的手指触摸视图将继续获取所有触摸活动，即使手指移动到不同的视图。 如果应用程序捕获该指针的 UWP 可以同样行为： 在`PointerEntered`事件处理程序中，元素调用`CapturePointer`，然后获取从该手指触摸屏输入的所有活动。

UWP 方法证明是非常有用，对于某些类型的应用程序，例如，音乐键盘。 每个密钥可以处理该注册表项的触摸事件，当有手指已从一个项滑到另一个使用时进行检测`PointerEntered`和`PointerExited`事件。

出于此原因，本文中所述的触控跟踪效果实现 UWP 方法。

## <a name="the-touch-tracking-effect-api"></a>触控跟踪影响 API

[**接触跟踪效果演示**](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)示例包含的类 （和枚举） 实现低级别触控跟踪。 这些类型属于命名空间`TouchTracking`和单词开头`Touch`。 **TouchTrackingEffectDemos** .NET Standard 库项目包括`TouchActionType`触控事件的类型的枚举：

```csharp
public enum TouchActionType
{
    Entered,
    Pressed,
    Moved,
    Released,
    Exited,
    Cancelled
}
```

所有平台还都包括一个事件，指出已取消触摸事件。

`TouchEffect` .NET 标准库中的类派生自`RoutingEffect`，并定义名为的事件`TouchAction`和一个名为方法`OnTouchAction`，它调用`TouchAction`事件：

```csharp
public class TouchEffect : RoutingEffect
{
    public event TouchActionEventHandler TouchAction;

    public TouchEffect() : base("XamarinDocs.TouchEffect")
    {
    }

    public bool Capture { set; get; }

    public void OnTouchAction(Element element, TouchActionEventArgs args)
    {
        TouchAction?.Invoke(element, args);
    }
}
```

另请注意`Capture`属性。 若要捕获触摸事件，应用程序必须设置此属性为`true`早于`Pressed`事件。 否则，触控事件的行为类似于通用 Windows 平台中。

`TouchActionEventArgs` .NET Standard 库中的类包含附带的每个事件的所有信息：

```csharp
public class TouchActionEventArgs : EventArgs
{
    public TouchActionEventArgs(long id, TouchActionType type, Point location, bool isInContact)
    {
        Id = id;
        Type = type;
        Location = location;
        IsInContact = isInContact;
    }

    public long Id { private set; get; }

    public TouchActionType Type { private set; get; }

    public Point Location { private set; get; }

    public bool IsInContact { private set; get; }
}
```

应用程序可以使用`Id`跟踪各手指的属性。 请注意`IsInContact`属性。 此属性始终为`true`有关`Pressed`事件并`false`为`Released`事件。 它也是始终`true`为`Moved`iOS 和 Android 上的事件。 `IsInContact`属性可能会`false`为`Moved`事件时在桌面上运行该程序并没有一个按钮的情况下移动鼠标指针在通用 Windows 平台上按下。

可以使用`TouchEffect`自己的应用程序通过在.NET Standard 库项目中的解决方案，包括该文件并添加到实例中的类`Effects`Xamarin.Forms 的任何元素的集合。 附加到处理程序`TouchAction`获取触控事件的事件。

若要使用`TouchEffect`在自己的应用程序，您将需要包含在平台的实现**TouchTrackingEffectDemos**解决方案。

## <a name="the-touch-tracking-effect-implementations"></a>触控跟踪效果实现

IOS、 Android 和 UWP 的实现`TouchEffect`开头的最简单的实现 (UWP) 和结尾的 iOS 实现，因为它是比其他更多结构复杂如下所述。

### <a name="the-uwp-implementation"></a>UWP 实现

UWP 实现`TouchEffect`最简单的方法。 像往常一样，类派生自`PlatformEffect`并包含两个程序集特性：

```csharp
[assembly: ResolutionGroupName("XamarinDocs")]
[assembly: ExportEffect(typeof(TouchTracking.UWP.TouchEffect), "TouchEffect")]

namespace TouchTracking.UWP
{
    public class TouchEffect : PlatformEffect
    {
        ...
    }
}
```

`OnAttached`重写将某些信息保存为字段和附加到所有指针事件的处理程序：

```csharp
public class TouchEffect : PlatformEffect
{
    FrameworkElement frameworkElement;
    TouchTracking.TouchEffect effect;
    Action<Element, TouchActionEventArgs> onTouchAction;

    protected override void OnAttached()
    {
        // Get the Windows FrameworkElement corresponding to the Element that the effect is attached to
        frameworkElement = Control == null ? Container : Control;

        // Get access to the TouchEffect class in the .NET Standard library
        effect = (TouchTracking.TouchEffect)Element.Effects.
                    FirstOrDefault(e => e is TouchTracking.TouchEffect);

        if (effect != null && frameworkElement != null)
        {
            // Save the method to call on touch events
            onTouchAction = effect.OnTouchAction;

            // Set event handlers on FrameworkElement
            frameworkElement.PointerEntered += OnPointerEntered;
            frameworkElement.PointerPressed += OnPointerPressed;
            frameworkElement.PointerMoved += OnPointerMoved;
            frameworkElement.PointerReleased += OnPointerReleased;
            frameworkElement.PointerExited += OnPointerExited;
            frameworkElement.PointerCanceled += OnPointerCancelled;
        }
    }
    ...
}    
```

`OnPointerPressed`处理程序的调用来调用影响事件`onTouchAction`字段中`CommonHandler`方法：

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerPressed(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Pressed, args);

        // Check setting of Capture property
        if (effect.Capture)
        {
            (sender as FrameworkElement).CapturePointer(args.Pointer);
        }
    }
    ...
    void CommonHandler(object sender, TouchActionType touchActionType, PointerRoutedEventArgs args)
    {
        PointerPoint pointerPoint = args.GetCurrentPoint(sender as UIElement);
        Windows.Foundation.Point windowsPoint = pointerPoint.Position;  

        onTouchAction(Element, new TouchActionEventArgs(args.Pointer.PointerId,
                                                        touchActionType,
                                                        new Point(windowsPoint.X, windowsPoint.Y),
                                                        args.Pointer.IsInContact));
    }
}
```

`OnPointerPressed` 此外会检查的值`Capture`属性的效果类中的.NET Standard 库和调用`CapturePointer`若是`true`。

 其他 UWP 事件处理程序是更简单：

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerEntered(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Entered, args);
    }
    ...
}
```

### <a name="the-android-implementation"></a>Android 实现

Android 和 iOS 实现是一定更复杂，因为它们必须实现`Exited`和`Entered`事件时手指将从一个元素移到另一个。 这两个实现结构类似。

Android`TouchEffect`类安装的处理程序`Touch`事件：

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

类还定义了两个静态字典：

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    static Dictionary<Android.Views.View, TouchEffect> viewDictionary =
        new Dictionary<Android.Views.View, TouchEffect>();

    static Dictionary<int, TouchEffect> idToEffectDictionary =
        new Dictionary<int, TouchEffect>();
    ...
```

`viewDictionary`获取每个时间的新条目`OnAttached`调用重写：

```csharp
viewDictionary.Add(view, this);
```

从字典中删除该条目`OnDetached`。 每个实例`TouchEffect`效果附加到的特定视图相关联。 静态字典允许任何`TouchEffect`实例以枚举所有其他视图和其对应`TouchEffect`实例。 这是将事件从一个视图传输到另一个允许所必需的。

Android 将分配一个 ID 代码触摸事件，允许应用程序可以跟踪各手指。 `idToEffectDictionary`将使用此 ID 代码相关联`TouchEffect`实例。 项添加到此字典时`Touch`的手指按调用处理程序：

```csharp
void OnTouch(object sender, Android.Views.View.TouchEventArgs args)
{
    ...
    switch (args.Event.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:
            FireEvent(this, id, TouchActionType.Pressed, screenPointerCoords, true);

            idToEffectDictionary.Add(id, this);

            capture = libTouchEffect.Capture;
            break;

```

该项已从`idToEffectDictionary`手指从屏幕的发布时。 `FireEvent`方法只是积累调用所需的所有信息`OnTouchAction`方法：

```csharp
void FireEvent(TouchEffect touchEffect, int id, TouchActionType actionType, Point pointerLocation, bool isInContact)
{
    // Get the method to call for firing events
    Action<Element, TouchActionEventArgs> onTouchAction = touchEffect.libTouchEffect.OnTouchAction;

    // Get the location of the pointer within the view
    touchEffect.view.GetLocationOnScreen(twoIntArray);
    double x = pointerLocation.X - twoIntArray[0];
    double y = pointerLocation.Y - twoIntArray[1];
    Point point = new Point(fromPixels(x), fromPixels(y));

    // Call the method
    onTouchAction(touchEffect.formsElement,
        new TouchActionEventArgs(id, actionType, point, isInContact));
}
```

在两种不同方式处理所有其他触摸屏输入类型： 如果`Capture`属性是`true`，触控事件是相当简单转换为`TouchEffect`信息。 它变得更加复杂时`Capture`是`false`因为可能需要从一个视图移到另一个触摸事件。 这负责`CheckForBoundaryHop`move 事件期间调用的方法。 此方法利用了这两个静态字典。 它通过枚举`viewDictionary`来确定当前触摸手指，和它使用的视图`idToEffectDictionary`用于存储当前`TouchEffect`实例 （并因此，当前视图） 具有特定 ID 相关联：

```csharp
void CheckForBoundaryHop(int id, Point pointerLocation)
{
    TouchEffect touchEffectHit = null;

    foreach (Android.Views.View view in viewDictionary.Keys)
    {
        // Get the view rectangle
        try
        {
            view.GetLocationOnScreen(twoIntArray);
        }
        catch // System.ObjectDisposedException: Cannot access a disposed object.
        {
            continue;
        }
        Rectangle viewRect = new Rectangle(twoIntArray[0], twoIntArray[1], view.Width, view.Height);

        if (viewRect.Contains(pointerLocation))
        {
            touchEffectHit = viewDictionary[view];
        }
    }

    if (touchEffectHit != idToEffectDictionary[id])
    {
        if (idToEffectDictionary[id] != null)
        {
            FireEvent(idToEffectDictionary[id], id, TouchActionType.Exited, pointerLocation, true);
        }
        if (touchEffectHit != null)
        {
            FireEvent(touchEffectHit, id, TouchActionType.Entered, pointerLocation, true);
        }
        idToEffectDictionary[id] = touchEffectHit;
    }
}
```

如果已更改`idToEffectDictionary`，该方法可能会调用`FireEvent`有关`Exited`和`Entered`到传输到另一个视图中。 但是，手指可能已被移至占用而无需附加视图区域`TouchEffect`，或从该区域具有附加的效果的视图。

请注意`try`和`catch`访问视图时阻止。 在页中，导航到，然后导航回主页上，`OnDetached`不会调用方法和项将保留在`viewDictionary`但 Android 将认为它们释放。

### <a name="the-ios-implementation"></a>IOS 实现

IOS 实现是类似于 Android 的实现不同之处在于 iOS`TouchEffect`类必须实例化的派生`UIGestureRecognizer`。 这是一个类中名为的 iOS 项目`TouchRecognizer`。 此类维护存储的两个静态字典`TouchRecognizer`实例：

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

此结构的大部分`TouchRecognizer`类是类似于 Android`TouchEffect`类。

## <a name="putting-the-touch-effect-to-work"></a>将触摸效果放到工作

[ **TouchTrackingEffectDemos** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)程序包含五个测试的常见任务的触控跟踪效果的页。

**字数拖动**页面允许您添加`BoxView`元素到`AbsoluteLayout`然后将它们拖在屏幕上。 [XAML 文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml)实例化两个`Button`添加的视图`BoxView`元素`AbsoluteLayout`清除`AbsoluteLayout`。

中的方法[代码隐藏文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml.cs)，添加一个新`BoxView`到`AbsoluteLayout`还添加了`TouchEffect`对象传递给`BoxView`并将事件处理程序附加到效果：

```csharp
void AddBoxViewToLayout()
{
    BoxView boxView = new BoxView
    {
        WidthRequest = 100,
        HeightRequest = 100,
        Color = new Color(random.NextDouble(),
                          random.NextDouble(),
                          random.NextDouble())
    };

    TouchEffect touchEffect = new TouchEffect();
    touchEffect.TouchAction += OnTouchEffectAction;
    boxView.Effects.Add(touchEffect);
    absoluteLayout.Children.Add(boxView);
}
```

`TouchAction`事件处理程序处理所有触控事件的所有`BoxView`元素，但它需要一些多加小心： 它不能在单个上允许两根手指`BoxView`因为该程序仅实现拖动，和两个手指的会相互干扰。 出于此原因，页上定义的当前跟踪每个手指嵌入的类：

```csharp
class DragInfo
{
    public DragInfo(long id, Point pressPoint)
    {
        Id = id;
        PressPoint = pressPoint;
    }

    public long Id { private set; get; }

    public Point PressPoint { private set; get; }
}

Dictionary<BoxView, DragInfo> dragDictionary = new Dictionary<BoxView, DragInfo>();
```

`dragDictionary`包含的一项每个`BoxView`当前正被拖动。

`Pressed`触摸操作将项添加到此字典和`Released`操作将其删除。 `Pressed`逻辑必须检查是否已经存在某个项的字典中`BoxView`。 如果是这样，`BoxView`为正被拖动，新的事件是第二个手指上的相同`BoxView`。 有关`Moved`和`Released`操作，事件处理程序必须检查是否字典为此，有一个条目`BoxView`和的触摸屏输入`Id`属性拖动`BoxView`字典条目中的相匹配：

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    BoxView boxView = sender as BoxView;

    switch (args.Type)
    {
        case TouchActionType.Pressed:
            // Don't allow a second touch on an already touched BoxView
            if (!dragDictionary.ContainsKey(boxView))
            {
                dragDictionary.Add(boxView, new DragInfo(args.Id, args.Location));

                // Set Capture property to true
                TouchEffect touchEffect = (TouchEffect)boxView.Effects.FirstOrDefault(e => e is TouchEffect);
                touchEffect.Capture = true;
            }
            break;

        case TouchActionType.Moved:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                Rectangle rect = AbsoluteLayout.GetLayoutBounds(boxView);
                Point initialLocation = dragDictionary[boxView].PressPoint;
                rect.X += args.Location.X - initialLocation.X;
                rect.Y += args.Location.Y - initialLocation.Y;
                AbsoluteLayout.SetLayoutBounds(boxView, rect);
            }
            break;

        case TouchActionType.Released:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                dragDictionary.Remove(boxView);
            }
            break;
    }
}
```

`Pressed`逻辑集`Capture`的属性`TouchEffect`对象传递给`true`。 这根手指的所有后续事件传送到同一个事件处理程序效果。

`Moved`逻辑移动`BoxView`通过更改`LayoutBounds`附加属性。 `Location`属性的事件参数始终是相对于`BoxView`正在拖动，并且如果`BoxView`正被拖动的固定速度`Location`连续的事件的属性将大约相同。 例如，如果按下一个手指`BoxView`中的中心，而`Pressed`操作存储区`PressPoint`属性 （50，50），也仍然相同的后续事件。 如果`BoxView`沿对角线方向拖动以恒定速率，后续`Location`属性期间`Moved`操作可能的值 （55、 55），在这种情况下`Moved`逻辑的水平和垂直位置加上5`BoxView`. 这样可将移动`BoxView`，以便其中心再次是直接在手指下。

可以移动多个`BoxView`元素同时使用不同的手指。

[![](touch-tracking-images/boxviewdragging-small.png "三重的字数拖动页面的屏幕截图")](touch-tracking-images/boxviewdragging-large.png#lightbox "字数拖动页面的三个屏幕截图")

### <a name="subclassing-the-view"></a>子类化视图

通常情况下，更加容易 Xamarin.Forms 元素来处理其自身的触摸事件。 **可拖动字数拖动**页面的功能与相同**字数拖动**页上，但用户拖动的实例的元素[ `DraggableBoxView` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/DraggableBoxView.cs)从派生类`BoxView`:

```csharp
class DraggableBoxView : BoxView
{
    bool isBeingDragged;
    long touchId;
    Point pressPoint;

    public DraggableBoxView()
    {
        TouchEffect touchEffect = new TouchEffect
        {
            Capture = true
        };
        touchEffect.TouchAction += OnTouchEffectAction;
        Effects.Add(touchEffect);
    }

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!isBeingDragged)
                {
                    isBeingDragged = true;
                    touchId = args.Id;
                    pressPoint = args.Location;
                }
                break;

            case TouchActionType.Moved:
                if (isBeingDragged && touchId == args.Id)
                {
                    TranslationX += args.Location.X - pressPoint.X;
                    TranslationY += args.Location.Y - pressPoint.Y;
                }
                break;

            case TouchActionType.Released:
                if (isBeingDragged && touchId == args.Id)
                {
                    isBeingDragged = false;
                }
                break;
        }
    }
}
```

构造函数创建并附加`TouchEffect`，并设置`Capture`时首次实例化该对象的属性。 没有字典是必需的因为该类本身存储`isBeingDragged`， `pressPoint`，和`touchId`与每个手指关联的值。 `Moved`处理更改`TranslationX`并`TranslationY`属性，以便处理逻辑将即使的父`DraggableBoxView`不是`AbsoluteLayout`。

### <a name="integrating-with-skiasharp"></a>将与 SkiaSharp 集成

接下来两个演示需要图形，并且使用 SkiaSharp 实现此目的。 你可能想要了解有关[Xamarin.Forms 中使用 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)研究这些示例之前。 前两篇文章 （"SkiaSharp 绘制基础知识"和"SkiaSharp 线和路径"） 涵盖所有需要此处。

**椭圆绘图**页面允许您通过轻扫手指在屏幕上的绘制一个椭圆。 具体取决于如何移动手指，您可以从左上到右下角或任何其他的对角角绘制椭圆。 使用随机颜色和不透明度是绘制椭圆。

[![](touch-tracking-images/ellipsedrawing-small.png "三个椭圆绘图页屏幕截图")](touch-tracking-images/ellipsedrawing-large.png#lightbox "的椭圆绘制页的三个屏幕截图")

如果您然后触摸省略号之一，可以将其拖到另一个位置。 这要求称为"命中测试，"这涉及到搜索的特定点处的图形对象的技术。 SkiaSharp 省略号不是 Xamarin.Forms 元素，因此它们不能执行其自己`TouchEffect`处理。 `TouchEffect`必须应用于整个`SKCanvasView`对象。

[EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml)文件实例化`SKCanvasView`在单个单元格`Grid`。 `TouchEffect`对象附加到的`Grid`:

```xaml
<Grid x:Name="canvasViewGrid"
        Grid.Row="1"
        BackgroundColor="White">

    <skia:SKCanvasView x:Name="canvasView"
                        PaintSurface="OnCanvasViewPaintSurface" />
    <Grid.Effects>
        <tt:TouchEffect Capture="True"
                        TouchAction="OnTouchEffectAction" />
    </Grid.Effects>
</Grid>
```

在 Android 和通用 Windows 平台，`TouchEffect`可以直接向附加`SKCanvasView`，但不起作用的 iOS 上。 请注意，`Capture`属性设置为`true`。

由类型的对象表示 SkiaSharp 呈现每个椭圆`EllipseDrawingFigure`:

```csharp
class EllipseDrawingFigure
{
    SKPoint pt1, pt2;

    public EllipseDrawingFigure()
    {
    }

    public SKColor Color { set; get; }

    public SKPoint StartPoint
    {
        set
        {
            pt1 = value;
            MakeRectangle();
        }
    }

    public SKPoint EndPoint
    {
        set
        {
            pt2 = value;
            MakeRectangle();
        }
    }

    void MakeRectangle()
    {
        Rectangle = new SKRect(pt1.X, pt1.Y, pt2.X, pt2.Y).Standardized;
    }

    public SKRect Rectangle { set; get; }

    // For dragging operations
    public Point LastFingerLocation { set; get; }

    // For the dragging hit-test
    public bool IsInEllipse(SKPoint pt)
    {
        SKRect rect = Rectangle;

        return (Math.Pow(pt.X - rect.MidX, 2) / Math.Pow(rect.Width / 2, 2) +
                Math.Pow(pt.Y - rect.MidY, 2) / Math.Pow(rect.Height / 2, 2)) < 1;
    }
}
```

`StartPoint`并`EndPoint`程序处理触摸输入; 时所使用属性`Rectangle`属性用于绘制椭圆。 `LastFingerLocation`属性时所拖动的椭圆，发挥作用，`IsInEllipse`方法中的命中测试的辅助工具。 该方法将返回`true`如果点在 ellipse 内部的。

[代码隐藏文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml.cs)维护三个集合：

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

`draggingFigure`字典包含的子集`completedFigures`集合。 SkiaSharp`PaintSurface`事件处理程序只需将呈现在这些对象`completedFigures`和`inProgressFigures`集合：

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Fill
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (EllipseDrawingFigure figure in completedFigures)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
    foreach (EllipseDrawingFigure figure in inProgressFigures.Values)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
}
```

触摸处理的最棘手部分是`Pressed`处理。 这就是执行命中测试，但如果代码检测到用户的手指下一个椭圆，如果当前未被另一个手指拖动可仅被拖动该椭圆形。 如果用户的手指下没有椭圆，代码开始绘制新椭圆的过程：

```csharp
case TouchActionType.Pressed:
    bool isDragOperation = false;

    // Loop through the completed figures
    foreach (EllipseDrawingFigure fig in completedFigures.Reverse<EllipseDrawingFigure>())
    {
        // Check if the finger is touching one of the ellipses
        if (fig.IsInEllipse(ConvertToPixel(args.Location)))
        {
            // Tentatively assume this is a dragging operation
            isDragOperation = true;

            // Loop through all the figures currently being dragged
            foreach (EllipseDrawingFigure draggedFigure in draggingFigures.Values)
            {
                // If there's a match, we'll need to dig deeper
                if (fig == draggedFigure)
                {
                    isDragOperation = false;
                    break;
                }
            }

            if (isDragOperation)
            {
                fig.LastFingerLocation = args.Location;
                draggingFigures.Add(args.Id, fig);
                break;
            }
        }
    }

    if (isDragOperation)
    {
        // Move the dragged ellipse to the end of completedFigures so it's drawn on top
        EllipseDrawingFigure fig = draggingFigures[args.Id];
        completedFigures.Remove(fig);
        completedFigures.Add(fig);
    }
    else // start making a new ellipse
    {
        // Random bytes for random color
        byte[] buffer = new byte[4];
        random.NextBytes(buffer);

        EllipseDrawingFigure figure = new EllipseDrawingFigure
        {
            Color = new SKColor(buffer[0], buffer[1], buffer[2], buffer[3]),
            StartPoint = ConvertToPixel(args.Location),
            EndPoint = ConvertToPixel(args.Location)
        };
        inProgressFigures.Add(args.Id, figure);
    }
    canvasView.InvalidateSurface();
    break;
```

其他 SkiaSharp 示例是**手指绘制**页。 可以从两个选择笔画颜色和笔划宽度`Picker`视图，然后使用一个或多个手指绘制：

[![](touch-tracking-images/fingerpaint-small.png "手指绘制页的三个屏幕截图")](touch-tracking-images/fingerpaint-large.png#lightbox "的手指绘制页的三个屏幕截图")

此示例还需要一个单独的类表示在屏幕上绘制每个行：

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new SKPath();
    }

    public SKPath Path { set; get; }

    public Color StrokeColor { set; get; }

    public float StrokeWidth { set; get; }
}
```

`SKPath`对象用于呈现每个行。 [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/FingerPaintPage.xaml.cs)文件维护这些对象，一个用于当前所绘制这些折线，另一个用于已完成的折线的两个集合：

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

`Pressed`处理过程将创建一个新`FingerPaintPolyline`，调用`MoveTo`上要存储的初始点的路径对象，并添加到该对象`inProgressPolylines`字典。 `Moved`处理调用`LineTo`上的新的指位置的路径对象和`Released`处理传输已完成从 polyline`inProgressPolylines`到`completedPolylines`。 再次重申，实际 SkiaSharp 绘制代码是相对简单：

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    StrokeCap = SKStrokeCap.Round,
    StrokeJoin = SKStrokeJoin.Round
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (FingerPaintPolyline polyline in completedPolylines)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }

    foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }
}
```

### <a name="tracking-view-to-view-touch"></a>跟踪视图来查看触摸

设置所有前面的示例`Capture`的属性`TouchEffect`到`true`，时`TouchEffect`已创建，或者当`Pressed`事件发生。 这可确保同一元素接收首次按下该视图的手指与关联的所有事件。 最后一个示例的用途*不*设置`Capture`到`true`。 当屏幕的手指移动从一个元素到另一个时，这会导致不同的行为。 从移动手指的元素接收的事件`Type`属性设置为`TouchActionType.Exited`和第二个元素接收的事件`Type`设置为`TouchActionType.Entered`。

此类型的触摸处理是非常有用的音乐键盘。 需要能够检测到它按下时，而且还时手指从一个项滑到另一个项。

**无提示键盘**页上定义小[ `WhiteKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/WhiteKey.cs)并[ `BlackKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BlackKey.cs)派生的类[ `Key` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/Key.cs)，它派生`BoxView`。

`Key`类已准备好实际音乐程序中使用。 它定义了名为的公共属性`IsPressed`和`KeyNumber`，用于将设置为建立由 MIDI 标准的键代码。 `Key`类还定义了名为的事件`StatusChanged`，这当调用`IsPressed`属性更改。

每个密钥允许使用多根手指。 出于此原因，`Key`类维护`List`的当前接触该注册表项的所有根手指的触控 ID 号：

```csharp
List<long> ids = new List<long>();
```

`TouchAction`事件处理程序添加到一个 ID`ids`两个列表`Pressed`事件类型和一个`Entered`类型，但仅当`IsInContact`属性是`true`为`Entered`事件。 ID 已从`List`有关`Released`或`Exited`事件：

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    switch (args.Type)
    {
      case TouchActionType.Pressed:
          AddToList(args.Id);
          break;

        case TouchActionType.Entered:
            if (args.IsInContact)
            {
                AddToList(args.Id);
            }
            break;

        case TouchActionType.Moved:
            break;

        case TouchActionType.Released:
        case TouchActionType.Exited:
            RemoveFromList(args.Id);
            break;
    }
}

```

`AddToList`并`RemoveFromList`这两种方法检查是否`List`empty 和非空之间发生了更改，如果是这样，调用`StatusChanged`事件。

各种`WhiteKey`并`BlackKey`中的页的排列元素[XAML 文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/SilentKeyboardPage.xaml)，当手机保持在横向模式下时，该查找最佳：

[![](touch-tracking-images/silentkeyboard-small.png "无提示键盘页的三个屏幕截图")](touch-tracking-images/silentkeyboard-large.png#lightbox "带来三倍的无提示键盘页屏幕截图")

如果在扫描您的手指，您将看到颜色的细微更改从一个密钥传输触控事件，到另一个。

## <a name="summary"></a>总结

本文演示了如何调用事件起作用，以及如何编写和使用实现低级别多点触控处理的效果。


## <a name="related-links"></a>相关链接

- [在 iOS 中的多点触控手指跟踪](~/ios/app-fundamentals/touch/touch-tracking.md)
- [多点触控手指在 Android 中跟踪](~/android/app-fundamentals/touch/touch-tracking.md)
- [触控跟踪效果 （示例）](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)
