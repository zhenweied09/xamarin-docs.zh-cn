---
title: 调用从效果的事件
description: 一种效果可以定义和调用的事件，信号基础本机视图中的更改。 这篇文章演示如何实现低级别的多点触控手指跟踪，以及如何生成指示触摸活动的事件。
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: eb4ed3df4ea1f9e6aacf1c875eab17908d73cb7c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="invoking-events-from-effects"></a>调用从效果的事件

_一种效果可以定义和调用的事件，信号基础本机视图中的更改。这篇文章演示如何实现低级别的多点触控手指跟踪，以及如何生成指示触摸活动的事件。_

本文中所述的效果提供低级别的触控事件对访问。 这些低级别的事件不是可通过现有`GestureRecognizer`类，但它们会对某些类型的应用程序至关重要。 例如，finger-paint 跟踪各个指随着在屏幕上的应用程序需要。 音乐键盘需要检测到分流并释放各个密钥，以及从一个项与另一种滑音 gliding 手指。

一种效果非常适合于多点触控手指跟踪，因为它可以附加到 Xamarin.Forms 中的任何元素。

## <a name="platform-touch-events"></a>平台触控事件

IOS、 Android 和通用 Windows 平台都包括一个低级别 API，用于应用程序能够检测触摸活动。 所有区分之间的三种基本类型的这些平台触控事件：

- *按下*，当手指触摸屏幕
- *移动*，当手指触摸屏幕移动
- *发布*，当上方的手指从屏幕释放

在多点触控环境中，多个指可以在同一时间触摸屏幕。 各种平台包括应用程序可用于区分多个指标识 (ID) 数。

在 iOS 中，`UIView`类定义三个可重写方法， `TouchesBegan`， `TouchesMoved`，和`TouchesEnded`与这三个基本事件相对应。 文章[多点触控手指跟踪](~/ios/app-fundamentals/touch/touch-tracking.md)介绍如何使用这些方法。 但是，iOS 程序不需要重写派生自的类`UIView`才能使用这些方法。 IOS`UIGestureRecognizer`还定义了这些相同三个方法，并且你可以将派生自的类的实例附加`UIGestureRecognizer`到任何`UIView`对象。

在 Android 中，`View`类定义名为的可重写方法`OnTouchEvent`来处理所有触摸活动。 触摸活动的类型定义的枚举成员`Down`， `PointerDown`， `Move`， `Up`，和`PointerUp`文章中所述[多点触控手指跟踪](~/android/app-fundamentals/touch/touch-tracking.md)。 Android`View`还定义了名为事件`Touch`允许事件处理程序附加到任何`View`对象。

在通用 Windows 平台 (UWP)，`UIElement`类定义在名为的事件`PointerPressed`， `PointerMoved`，和`PointerReleased`。 这些文章中描述了[MSDN 上的处理指针输入文章](/windows/uwp/input-and-devices/handle-pointer-input/)和 API 文档[ `UIElement` ](/uwp/api/windows.ui.xaml.uielement/)类。

`Pointer`在通用 Windows 平台的 API 用于统一鼠标、 触控和笔输入。 为此，`PointerMoved`在鼠标移过元素中，即使未按下鼠标按钮时调用事件。 `PointerRoutedEventArgs`附带这些事件的对象有一个名为`Pointer`，有一个名为`IsInContact`指示是否按鼠标左键或手指是与屏幕联系。

此外，UWP 定义两个名为的更多事件`PointerEntered`和`PointerExited`。 这些警报表示当鼠标或手指将移动从一个元素到另一个。 例如，考虑两个相邻元素名为 A 和 b。这两个元素已安装指针事件的处理程序。 当为指按下上 A，`PointerPressed`事件调用。 当上方的手指移动时，一个时，将调用`PointerMoved`事件。 如果上方的手指移从 A 到 B，将调用一个`PointerExited`事件和 B 调用`PointerEntered`事件。 如果然后发布上方的手指，B 时，将调用`PointerReleased`事件。

IOS 和 Android 平台是不同于 UWP： 首先获取对的调用的视图`TouchesBegan`或`OnTouchEvent`时的手指触摸视图将继续获取所有活动即便如果上方的手指移到不同视图。 如果应用程序会捕获指针，UWP 可以具有同样行为： 在`PointerEntered`事件处理程序中，元素调用`CapturePointer`，然后从该手指中获取所有触摸活动。

UWP 方法经证明是对于某些类型的应用程序，例如，音乐键盘非常有用。 每个密钥可以处理该注册表项的触摸事件和检测何时从一个项，为指具有滑动到另一个使用`PointerEntered`和`PointerExited`事件。

为此，本文中所述的触摸跟踪效果实现 UWP 方法。

## <a name="the-touch-tracking-effect-api"></a>触摸跟踪效果 API

[ **Touch 跟踪效果演示**](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)示例包含的类 （和枚举） 实现低级别的触摸跟踪。 这些类型属于命名空间`TouchTracking`并且单词的开头`Touch`。 **TouchTrackingEffectDemos**可移植类库项目包括`TouchActionType`触控事件的类型的枚举：

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

所有平台还都包含指示已取消触摸事件的事件。

`TouchEffect`在 PCL 中的类派生自`RoutingEffect`并定义名为事件`TouchAction`和一个名为方法`OnTouchAction`，它调用`TouchAction`事件：

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

另请注意`Capture`属性。 若要捕获触控事件，应用程序必须设置此属性为`true`之前`Pressed`事件。 否则，触控事件的行为类似于通用 Windows 平台中。

`TouchActionEventArgs`在 PCL 中的类包含附带的每个事件的所有信息：

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

应用程序可以使用`Id`跟踪各个指的属性。 请注意`IsInContact`属性。 此属性始终是`true`为`Pressed`事件和`false`为`Released`事件。 它也是始终`true`为`Moved`iOS 和 Android 上的事件。 `IsInContact`属性可能`false`为`Moved`当在桌面上运行程序并没有一个按钮的情况下移动鼠标指针在通用 Windows 平台上的事件按下。

你可以使用`TouchEffect`自己的应用程序，通过在解决方案的 PCL 项目中，包括该文件并添加到实例中的类`Effects`任何 Xamarin.Forms 元素的集合。 附加到处理程序`TouchAction`获取触控事件的事件。

若要使用`TouchEffect`在自己的应用程序，还需要包括在平台实现**TouchTrackingEffectDemos**解决方案。

## <a name="the-touch-tracking-effect-implementations"></a>触摸跟踪效果实现

IOS、 Android 和 UWP 的实现`TouchEffect`与的最简单的实现 (UWP) 开头和结尾与 iOS 实现，因为它是比其他更结构上复杂如下所述。

### <a name="the-uwp-implementation"></a>UWP 实现

UWP 实现`TouchEffect`是最简单。 像往常一样，类派生自`PlatformEffect`并包括两个程序集特性：

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

        // Get access to the TouchEffect class in the PCL
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

`OnPointerPressed`处理程序通过调用调用效果事件`onTouchAction`字段`CommonHandler`方法：

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

`OnPointerPressed` 此外将检查的值`Capture`中的 PCL 和调用中的影响类属性`CapturePointer`若是`true`。

 其他 UWP 事件处理程序是甚至更简单：

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

Android 和 iOS 实现不一定更复杂，因为它们必须实现`Exited`和`Entered`当手指从在元素移到另一个事件。 这两个实现同样的构成。

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

从字典中移除项`OnDetached`。 每个实例`TouchEffect`与效果附加到的特定视图相关联。 静态字典允许任何`TouchEffect`实例用于循环访问所有其他视图和及其相应`TouchEffect`实例。 这是必需的以便将事件从一个视图转移到另一个。

Android 将分配一个 ID 代码来向下触控事件允许应用程序来跟踪各个指。 `idToEffectDictionary`将与此 ID 代码相关联`TouchEffect`实例。 项添加到此字典时`Touch`为手指按调用处理程序：

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

            capture = pclTouchEffect.Capture;
            break;

```

从移除的项`idToEffectDictionary`上方的手指从屏幕的发布时。 `FireEvent`方法只是积累调用所需的所有信息`OnTouchAction`方法：

```csharp
void FireEvent(TouchEffect touchEffect, int id, TouchActionType actionType, Point pointerLocation, bool isInContact)
{
    // Get the method to call for firing events
    Action<Element, TouchActionEventArgs> onTouchAction = touchEffect.pclTouchEffect.OnTouchAction;

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

两种不同方式处理所有其他触摸类型： 如果`Capture`属性是`true`，触摸事件是相当简单的平移`TouchEffect`信息。 它变得更为复杂时`Capture`是`false`因为触控事件可能需要移动到另一个视图中。 这是责任`CheckForBoundaryHop`方法，在移动事件过程中调用该方法。 此方法使用这两个静态字典。 它通过枚举`viewDictionary`以确定当前接触上方的手指，并使用的视图`idToEffectDictionary`存储当前`TouchEffect`实例 （并因此，当前视图） 与某一特定 ID 相关联：

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

如果已在更改`idToEffectionDictionary`，该方法可能调用`FireEvent`为`Exited`和`Entered`到两个视图之间的传输。 但是，上方的手指可能已被移至占用不含一个附加的视图区域`TouchEffect`，或从该区域写入具有附加的效果的视图。

请注意`try`和`catch`阻止访问视图时。 在导航到，然后向后定位到主页页面的页面中`OnDetached`不调用方法和项保留在`viewDictionary`但 Android 将认为它们释放。

### <a name="the-ios-implementation"></a>IOS 实现

IOS 实现是类似于只不过的 Android 实现 iOS`TouchEffect`类必须实例化的派生`UIGestureRecognizer`。 这是名为的 iOS 项目中的类`TouchRecognizer`。 此类维护存储的两个静态字典`TouchRecognizer`实例：

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

大部分此结构`TouchRecognizer`类是类似于 Android`TouchEffect`类。

## <a name="putting-the-touch-effect-to-work"></a>将 Touch 效果放到工作

[ **TouchTrackingEffectDemos** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)程序包含 5 个测试常见任务的触摸跟踪效果的页。

**字数拖动**页面允许您添加`BoxView`元素与`AbsoluteLayout`然后将它们拖动鼠标屏幕。 [XAML 文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml)实例化两个`Button`视图添加`BoxView`元素与`AbsoluteLayout`和清除`AbsoluteLayout`。

中的方法[代码隐藏文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml.cs)，它将添加一个新`BoxView`到`AbsoluteLayout`还将添加`TouchEffect`对象传递给`BoxView`并将事件处理程序附加到效果：

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

`TouchAction`事件处理程序会为所有处理所有触控事件`BoxView`元素，但它需要一些谨慎： 它不能在单个上允许双指`BoxView`因为程序仅实现拖动，并将两根手指会相互影响。 鉴于此页定义当前跟踪每个手指嵌入的类：

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

`dragDictionary`包含条目适用于每个`BoxView`当前拖动。

`Pressed`触摸操作将项添加到此字典与`Released`操作将其删除。 `Pressed`逻辑必须检查是否有已项对该字典中`BoxView`。 如果是这样，`BoxView`为所拖动，新的事件是第二个手指上的相同`BoxView`。 有关`Moved`和`Released`操作，事件处理程序必须检查是否字典有一个条目，该`BoxView`且触摸`Id`属性拖动到`BoxView`是否匹配中作为字典项：

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

`Pressed`逻辑集`Capture`属性`TouchEffect`对象传递给`true`。 将该手指的所有后续事件传送到相同的事件处理程序效果。

`Moved`逻辑移动`BoxView`通过更改`LayoutBounds`附加属性。 `Location`属性的事件自变量始终是相对于`BoxView`正在拖动如果`BoxView`所拖动以恒定速度`Location`的连续事件属性将大约相同。 例如，如果为指按下`BoxView`在其中心`Pressed`操作存储区`PressPoint`属性 （50，50），也仍然是相同的后续事件。 如果`BoxView`以恒定速率，后续沿对角线拖`Location`期间属性`Moved`操作可能的值 （55，55），在这种情况下`Moved`逻辑将 5 添加到的水平和垂直位置`BoxView`. 这样会将移动`BoxView`，以便其中心再次是直接在上方的手指。

你可以移动多个`BoxView`元素同时使用不同的手指。

[![](touch-tracking-images/boxviewdragging-small.png "三重的字数拖动页的屏幕截图")](touch-tracking-images/boxviewdragging-large.png#lightbox "三倍的字数拖动页的屏幕截图")

### <a name="subclassing-the-view"></a>子类化视图

通常情况下，很容易 Xamarin.Forms 元素来处理其自己的触摸事件。 **可拖动字数拖动**页面的功能相同**字数拖动**页上，但用户拖动的实例的元素[ `DraggableBoxView` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/DraggableBoxView.cs)派生自的类`BoxView`:

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

构造函数创建并将附加`TouchEffect`，并将设置`Capture`时首次实例化该对象的属性。 没有字典是必需的因为类本身将存储`isBeingDragged`， `pressPoint`，和`touchId`与每个手指关联的值。 `Moved`处理更改`TranslationX`和`TranslationY`属性，以便将工作逻辑即使的父`DraggableBoxView`不`AbsoluteLayout`。

### <a name="integrating-with-skiasharp"></a>将与 SkiaSharp 集成

下面的两个演示需要图形，并且为此目的使用 SkiaSharp。 你可能想要了解有关[Xamarin.Forms 中使用 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)你先研究这些示例之前。 （"SkiaSharp 绘制基础知识"和"SkiaSharp 行和路径"） 的前两个文章涵盖此处需要的所有内容。

**椭圆绘制**页面允许您通过轻扫手指在屏幕上的绘制椭圆形。 根据移动手指的方式，您可以从左上到右下角，或从任何其他角到相反角绘制椭圆。 绘制椭圆是随机的颜色和不透明度。

[![](touch-tracking-images/ellipsedrawing-small.png "三重的椭圆绘图页的屏幕截图")](touch-tracking-images/ellipsedrawing-large.png#lightbox "三重的椭圆绘图页的屏幕截图")

如果你然后触摸省略号之一，可以将其拖动到另一个位置。 这要求称为"命中测试，"这就需要搜索的特定点的图形对象的方法。 SkiaSharp 省略号不可 Xamarin.Forms 元素，因此它们无法执行其自己`TouchEffect`处理。 `TouchEffect`必须应用于整个`SKCanvasView`对象。

[EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml)文件实例化`SKCanvasView`中单个单元格`Grid`。 `TouchEffect`对象附加到的`Grid`:

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

在 Android 和通用 Windows 平台上，`TouchEffect`可以直接向附加`SKCanvasView`，但不起作用的 iOS 上。 请注意，`Capture`属性设置为`true`。

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

`StartPoint`和`EndPoint`时该程序正在处理触摸屏输入; 使用属性`Rectangle`属性用于绘制椭圆。 `LastFingerLocation`属性派上用场时所拖动椭圆，和`IsInEllipse`方法辅助命中测试。 该方法返回`true`如果该点在内部椭圆。

[代码隐藏文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml.cs)维护三个集合：

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

`draggingFigure`字典包含一部分`completedFigures`集合。 SkiaSharp`PaintSurface`事件处理程序只需呈现在这些对象`completedFigures`和`inProgressFigures`集合：

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

最棘手的触摸处理部分是`Pressed`处理。 这就是执行命中测试，但如果代码检测到用户的手指下的椭圆，如果当前未正在由另一个手指拖动可以仅将拖动该椭圆。 如果没有用户的手指下没有椭圆，该代码将开始绘制新椭圆的过程：

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

其他 SkiaSharp 示例是**手指绘制**页。 可以从两个选择描边颜色和描边宽度`Picker`视图，然后绘制与一个或多个指：

[![](touch-tracking-images/fingerpaint-small.png "三重的手指绘制页的屏幕截图")](touch-tracking-images/fingerpaint-large.png#lightbox "手指绘制页面的三个屏幕截图")

此示例还需要一个单独的类来表示在屏幕上绘制每个行：

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

`SKPath`对象用于呈现每个行。 [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/FingerPaintPage.xaml.cs)文件维护这些对象，一个用于当前绘制这些折线，另一个用于折线的已完成的两个集合：

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

`Pressed`处理过程将创建一个新`FingerPaintPolyline`，调用`MoveTo`上要存储的初始点的路径对象，并将添加到该对象`inProgressPolylines`字典。 `Moved`处理调用`LineTo`上的新的手指位置的路径对象和`Released`处理传输已完成从折线`inProgressPolylines`到`completedPolylines`。 同样，绘制代码实际 SkiaSharp 是相对简单的：

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

### <a name="tracking-view-to-view-touch"></a>跟踪视图以查看触摸

设置所有前面的示例`Capture`属性`TouchEffect`到`true`，当`TouchEffect`已创建，或者当`Pressed`发生事件。 这可确保同一元素收到与首次按下视图手指关联的所有事件。 最终的示例执行*不*设置`Capture`到`true`。 这会导致不同的行为，在与屏幕联系手指从一个元素移到另。 元素上方的手指从移动接收的事件`Type`属性设置为`TouchActionType.Exited`和第二个元素接收的事件`Type`设置`TouchActionType.Entered`。

触摸处理此类型是对音乐键盘非常有用。 需要能够检测到它按下时，但还时手指从一个项滑到另一个项。

**无提示键盘**页定义小[ `WhiteKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/WhiteKey.cs)和[ `BlackKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BlackKey.cs)派生自的类[ `Key` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/Key.cs)，它派生自`BoxView`。

`Key`类是可供在实际音乐程序中使用。 它定义公共属性名为`IsPressed`和`KeyNumber`，这是想要设置到由 MIDI 标准建立的键代码。 `Key`类还定义了名为事件`StatusChanged`，后者时调用`IsPressed`属性更改。

多个指可以在每个键。 为此，`Key`类维护`List`当前点击该注册表项的所有指的触摸屏输入 ID 号：

```csharp
List<long> ids = new List<long>();
```

`TouchAction`事件处理程序添加到一个 ID`ids`两个列表`Pressed`事件类型和`Entered`类型，但仅当`IsInContact`属性是`true`为`Entered`事件。 从删除该标识`List`为`Released`或`Exited`事件：

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

`AddToList`和`RemoveFromList`方法都检查如果`List`间空且非空，已更改时，如果是这样，将调用和`StatusChanged`事件。

各种`WhiteKey`和`BlackKey`元素排列在页中的[XAML 文件](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/SilentKeyboardPage.xaml)，它看起来最佳电话保存在横向模式中时：

[![](touch-tracking-images/silentkeyboard-small.png "三重的无提示键盘页面屏幕截图")](touch-tracking-images/silentkeyboard-large.png#lightbox "三倍的无提示键盘页面屏幕截图")

如果你扫描手指跨密钥中，你将看到的颜色的颜色的细微更改将触控事件传输从一个项到另一个。

## <a name="summary"></a>总结

本文演示了如何调用事件起作用，以及如何编写和使用实现低级别的多点触控处理效果。


## <a name="related-links"></a>相关链接

- [在 iOS 中的多点触控手指跟踪](~/ios/app-fundamentals/touch/touch-tracking.md)
- [在 Android 中跟踪的多点触控手指](~/android/app-fundamentals/touch/touch-tracking.md)
- [触摸跟踪效果 （示例）](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)
