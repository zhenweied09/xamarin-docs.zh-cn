---
title: 触控事件和在 Xamarin.iOS 的手势
description: 本文档介绍如何使用触控事件、 多点触控、 手势、 多个手势和 Xamarin.iOS 应用程序中的自定义特定动作。
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 34073474ef3ef74f2fddbf487b3377224dc1aa3e
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784584"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>触控事件和在 Xamarin.iOS 的手势

它很重要，若要了解触控事件并在 iOS 应用程序，按 Api，因为它们是与设备的所有物理交互的中心。 所有触摸交互都涉及`UITouch`对象。 在本文中，我们将了解如何使用`UITouch`类，并支持触摸其 Api。 更高版本，我们将扩展在我们的知识，若要了解如何支持手势。

## <a name="enabling-touch"></a>启用触摸

中的控件`UIKit`– 这些子类化从 UIControl – 因此依赖于它们具有手势 UIKit 中内置的用户交互并因此不需要启用 Touch。 已启用。

但是，许多的视图中的`UIKit`没有触摸默认情况下启用。 有两种方法，以便在控件上的触摸屏输入。 第一种方法是检查 iOS 设计器中，属性板中的启用用户交互复选框下面的屏幕截图中所示：

 [![](touch-in-ios-images/image1.png "检查属性填充的 ios 设计器中的启用用户交互复选框")](touch-in-ios-images/image1.png#lightbox)

我们还可以使用一个控制器设置`UserInteractionEnabled`属性设置为 true; 如果`UIView`类。 如果在代码中创建 UI，这是必需的。

以下代码行是一个示例：

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>触摸事件

有一些用户触摸屏幕、 移动其手指，或删除其手指时发生的三个阶段的触摸屏输入。 这些方法定义中`UIResponder`，即 UIView 的基类。 iOS 将替代关联的方法上`UIView`和`UIViewController`来处理触摸：

-  `TouchesBegan` -这称为时首次访问屏幕。
-  `TouchesMoved` – 用户所在的位置的触摸更改滑动其手指在屏幕时，将调用此。
-  `TouchesEnded` 或`TouchesCancelled`–`TouchesEnded`用户的指提起关闭屏幕后调用。  `TouchesCancelled` 获取调用 iOS 取消触摸 – 例如，如果，如果用户幻灯片他或她离开一个用于取消按下的某个按钮的手指。


通过 UIViews，检查触摸事件是否为视图对象的边界内的堆栈的向下触控事件旅行以递归方式。 此情况通常称作_命中测试_。 它们将首先调用上的最顶层`UIView`或`UIViewController`然后将调用上`UIView`和`UIViewControllers`下面这些视图层次结构中。

A`UITouch`将用户触摸屏幕每次创建对象。 `UITouch`对象包括有关触摸，如触摸发生时，它的发生位置如果触摸轻扫等的数据。触控事件获取传递收尾工作属性 –`NSSet`包含一个或多个收尾工作。 我们可以使用此属性获取指触摸屏输入，并确定应用程序的响应。

重写其中一个触控事件的类应首先调用基实现，然后获取`UITouch`与事件关联的对象。 若要获取对第一个触摸的引用，请调用`AnyObject`属性并将其转换为`UITouch`如示下面的示例：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        //code here to handle touch
    }
}
```

iOS 自动识别连续的快速涉及在屏幕上，并且将收集这些日志所有为在一次一个点击`UITouch`对象。 这使得检查双击简单地检查`TapCount`属性，如下面的代码中所示：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        if (touch.TapCount == 2)
        {
            // do something with the double touch.
        }
    }
}
```

## <a name="multi-touch"></a>多点触控

默认情况下，在控件上不启用多点触控。 在 iOS 设计器中，可以启用多点触控，如下面的屏幕截图所示：

 [![](touch-in-ios-images/image2.png "在 iOS 设计器中启用多点触控")](touch-in-ios-images/image2.png#lightbox)

还有可能要以编程方式设置设置多点触控`MultipleTouchEnabled`属性，如以下代码行中所示：

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

若要确定多少指接触屏幕，请使用`Count`属性`UITouch`属性：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>确定触摸位置

该方法`UITouch.LocationInView`返回包含在给定的视图触摸的坐标的 CGPoint 对象。 此外，我们可以测试以查看该位置是否在控件内通过调用方法`Frame.Contains`。 下面的代码段显示了此示例：

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

现在，我们已在 iOS 中的触控事件的了解，让我们了解笔势识别器。

## <a name="gesture-recognizers"></a>笔势识别器

笔势识别器可以极大地简化，并减少应用程序中支持触摸的编程工作量。 iOS 笔势识别器聚合到单个触摸事件触控事件的一系列。

Xamarin.iOS 提供类`UIGestureRecognizer`作为以下内置笔势识别器的基类：

-  *UITapGestureRecognizer* – 这是用于一个或多个分流。
-  *UIPinchGestureRecognizer* – Pinching 和相隔指进行传播。
-  *UIPanGestureRecognizer* – 平移或拖动。
-  *UISwipeGestureRecognizer* – 轻扫以任意方向。
-  *UIRotationGestureRecognizer* – 轮换顺时针旋转或逆时针旋转动态的两个手指。
-  *UILongPressGestureRecognizer* – 按下并按住、 有时称为 long 类型的值按或 long 类型的值并单击。


使用手势识别器的基本模式是，如下所示：

1.  **实例化笔势识别器**– 首先，实例化`UIGestureRecognizer`子类。 实例化的对象将关联的视图和时，将垃圾收集视图释放。 不需要创建类级别变量作为此视图。
1.  **配置任何笔势设置**– 下一步是配置笔势识别器。 Xamarin 的文档，请查阅上`UIGestureRecognizer`可以用来控制的行为的属性的列表及其子类别`UIGestureRecognizer`实例。
1.  **配置目标**– 由于其 Objective C 有继承，Xamarin.iOS 不会引发事件时笔势识别器匹配的笔势。  `UIGestureRecognizer` 有一个方法 – `AddTarget` –，可以接受匿名委托或替换为代码 OBJECTIVE-C 的选择器笔势识别器使匹配项时要执行。
1.  **启用笔势识别器**– 只需与使用触摸事件手势时，将仅识别启用触摸交互一样。
1.  **将笔势识别器添加到视图**– 最后一步是将笔势添加到视图中，通过调用`View.AddGestureRecognizer`，并将其传递笔势识别器对象。

请参阅[笔势识别器示例](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples)有关如何在代码中实现它们的详细信息。

当调用该笔势的目标时，它将传递对发生的笔势的引用。 这允许笔势目标以获取有关发生该笔势的信息。 可用于的信息的程度取决于所使用的笔势识别器的类型。 Xamarin 的文档了解有关可用的数据为每个的信息，请参阅`UIGestureRecognizer`子类。

请务必记住，一旦笔势识别器添加到视图后，视图 （和其下的任何视图） 将不会收到任何触控事件。 若要允许同时使用笔势，触控事件`CancelsTouchesInView`属性必须设置为 false，，如下面的代码所示：

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

每个`UIGestureRecognizer`提供有关状态的笔势识别器的重要信息的状态属性。 每次更改此属性的值，iOS 将调用并向其提供更新的订阅方法。 如果自定义笔势识别器永远不会更新状态属性，订阅服务器将永远不会调用，使笔势识别器无用。

手势可以归纳如下两种类型之一：

1.  *离散*– 它们识别这些手势唯一激发第一个时间。
1.  *连续*– 这些手势继续激发，只要它们识别。


笔势识别器存在问题的以下状态之一：

-  *可能*– 这是所有的笔势识别器的初始状态。 这是默认值的 State 属性。
-  *Began* – 当首次识别连续笔势时，其状态设置为 Began。 这样，订阅区分笔势识别启动时与更改时。
-  *已更改*– 连续笔势已经开始，但尚未完成后，状态将设置为已更改每次触摸屏输入移动或更改，只要它仍位于笔势的预期参数。
-  *取消*– 将设置此状态，如果在识别器从 Began 转到已更改，而不再与此类的方式更改收尾工作然后容纳笔势的模式。
-  *识别*– 将设置状态，当笔势识别器收尾工作的一组相匹配，并将通知订阅服务器上是否完成了笔势。
-  *结束*– 这是识别状态的别名。
-  *失败*– 如果笔势识别器不再匹配正在侦听的状态将更改为失败收尾工作。


Xamarin.iOS 表示这些值`UIGestureRecognizerState`枚举。

## <a name="working-with-multiple-gestures"></a>使用多个手势

默认情况下，iOS 不允许同时运行的默认手势。 相反，每个笔势识别器将不确定的顺序接收触控事件。 下面的代码段演示如何使同时运行笔势识别器：

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

还有可能要禁用在 iOS 中的笔势。 有两个委托属性可允许笔势识别器检查应用程序和当前触控事件，，使得确定如何以及如果笔势中应识别的状态。 两个事件是：

1.  *ShouldReceiveTouch* – 笔势识别器传递了触摸事件，并提供了一个机会检查收尾工作，并确定哪些收尾工作将由笔势识别器之前，正确，会调用此委托。
1.  *ShouldBegin* – 这称为时识别器尝试将从可能的状态更改为某个其他状态。 返回 false，则将强制笔势识别器更改为失败的状态。


你可以重写这些方法通过强类型化`UIGestureRecognizerDelegate`、 弱委托或通过事件处理程序语法，如下面的代码段所示的绑定：

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

最后，它是可能排队等候笔势识别器，以便它仅将会成功如果另一个笔势识别器失败。 例如，双击笔势识别器失败时，仅应成功单个的点击手势识别器。 下面的代码段提供了此示例：

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>创建自定义笔势

虽然 iOS 提供一些默认笔势识别器，但它可能需要在某些情况下创建自定义笔势识别器。 创建自定义笔势识别器包括以下步骤：

1.  子类`UIGestureRecognizer`。
1.  重写相应触摸事件方法。
1.  通过基类的 State 属性识别状态向上冒泡。


将在中介绍的这一个实际示例[在 iOS 中使用 Touch](ios-touch-walkthrough.md)演练。
