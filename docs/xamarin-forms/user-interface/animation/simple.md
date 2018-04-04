---
title: 简单的动画
description: ViewExtensions 类提供可用于构造简单动画的扩展方法。 本文演示如何创建和取消使用 ViewExtensions 类的动画。
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2017
ms.openlocfilehash: 0d2cc30f9bc1ae5602394b8ca2d8e75517a01b54
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="simple-animations"></a>简单的动画

_ViewExtensions 类提供可用于构造简单动画的扩展方法。本文演示如何创建和取消使用 ViewExtensions 类的动画。_


[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)类提供了以下用于创建简单的动画的扩展方法：

- [`TranslateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) 进行动画处理[ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)和[ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)属性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`ScaleTo`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) 进行动画处理[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)属性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`RelScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) 适用的动画的增量增加或减少到[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)属性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`RotateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) 进行动画处理[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)属性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`RelRotateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) 适用的动画的增量增加或减少到[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)属性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`RotateXTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) 进行动画处理[ `RotationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/)属性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`RotateYTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) 进行动画处理[ `RotationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/)属性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`FadeTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) 进行动画处理[ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/)属性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。

默认情况下，每个动画将采用 250 毫秒。 创建动画，但是，可以指定每个动画的持续时间。

[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)类还进行了[ `CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/)可用于取消任何动画的方法。

> [!NOTE]
> [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)类提供[ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/)扩展方法。 但是，此方法旨在供布局进行动画处理包含大小和位置更改的布局状态之间转换。 因此，它应仅由[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)子类。

中的动画扩展方法[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)类是所有异步、 返回`Task<bool>`对象。 返回值是`false`动画完成后，如果和`true`如果动画已取消。 因此，动画方法通常都应与使用`await`运算符，这样就可以轻松地确定当动画已完成。 此外，它随后将成为可以通过来创建顺序动画执行前一种方法完成后的后续动画方法。 有关详细信息，请参阅[复合动画](#compound)。

如果要求让动画完成在后台，则`await`运算符，则可以省略。 在此方案中，动画扩展方法将快速启动动画，与在后台中发生的动画后返回。 此操作时可以执行利用创建复合动画。 有关详细信息，请参阅[复合动画](#composite)。

有关详细信息`await`运算符，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

## <a name="single-animations"></a>单个动画

在每个扩展方法[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)实现渐进式更改属性从一个值为其他值的时间一段时间内的单个动画操作。 本部分介绍了每个动画操作。

### <a name="rotation"></a>旋转

下面的代码示例演示如何使用[ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法要进行动画处理[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)属性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

此代码进行动画处理[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)通过旋转达 360 度超过 2 秒 （2000年毫秒） 的实例。 [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法获取当前[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)属性值的动画，开始，然后从该值旋转到其第一个自变量 (360)。 动画，则一旦完成，该映像[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)属性重置为 0。 这样可确保`Rotation`属性不会保留在 360 后动画到结束，可能会妨碍其他旋转。

以下屏幕截图显示了每个平台上的进行中的旋转：

![](simple-images/rotateto.png "旋转动画")

### <a name="relative-rotation"></a>相对旋转

下面的代码示例演示如何使用[ `RelRotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法以增量方式增加或减少[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)属性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RelRotateTo (360, 2000);
```

此代码进行动画处理[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)通过旋转从它的起始位置的 360 度超过 2 秒 （2000年毫秒） 的实例。 [ `RelRotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法获取当前[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)属性值的动画，开始，然后从该值旋转到值再加上其第一个参数 (360)。 这可确保每个动画将始终为从起始位置的 360 度旋转。 因此，如果动画已正在进行时调用新动画，则它将开始从当前位置，并可能最终会在不是增量为 360 度的位置。

以下屏幕截图显示了每个平台上的进行中的相对旋转：

![](simple-images/relrotateto.png "相对旋转动画")

### <a name="scaling"></a>缩放

下面的代码示例演示如何使用[ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)方法要进行动画处理[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)属性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.ScaleTo (2, 2000);
```

此代码进行动画处理[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)通过向上扩展到两次其大小超过 2 秒 （2000年毫秒） 的实例。 [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)方法获取当前[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)开头的动画，然后可从该值扩展到其第一个参数 (2) 的属性值 （默认值为 1）。 此操作将扩展到其大小的两倍的图像的大小。

以下屏幕快照显示在每个平台上进行缩放：

![](simple-images/scaleto.png "缩放动画")

### <a name="relative-scaling"></a>相对缩放

下面的代码示例演示如何使用[ `RelScaleTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法要进行动画处理[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)属性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RelScaleTo (2, 2000);
```

此代码进行动画处理[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)通过向上扩展到两次其大小超过 2 秒 （2000年毫秒） 的实例。 [ `RelScaleTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法获取当前[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)开头的动画，然后可从该值扩展到值再加上其第一个参数 (2) 的属性值。 这可确保每个动画将始终为一种缩放比例 2 从起始位置。

### <a name="scaling-and-rotation-with-anchors"></a>缩放和旋转使用锚

[ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)和[ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)属性设置的缩放或旋转中心[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)和[ `Scale`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)属性。 因此，其值也会影响[ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)和[ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)方法。

给定[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)已被放在布局的中心，下面的代码示例演示了如何通过设置轮换围绕布局的中心映像其[ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)属性：

```csharp
image.AnchorY = (Math.Min (absoluteLayout.Width, absoluteLayout.Height) / 2) / image.Height;
await image.RotateTo (360, 2000);
```

要旋转[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)布局，围绕中心实例[ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)和[ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)属性必须设置为值相对于宽度和高度`Image`。 在此示例中，中心`Image`定义要在布局中，中心，因此默认值`AnchorX`值为 0.5 不需要更改。 但是，`AnchorY`属性重定义为从顶部的值`Image`到布局的中心点。 这样可确保`Image`使布局的中心点围绕 360 度的完整旋转，如以下屏幕截图中所示：

![](simple-images/rotate-anchors.png "使用锚旋转动画")

### <a name="translation"></a>转换

下面的代码示例演示如何使用[ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法要进行动画处理[ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)和[ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) 属性[`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

此代码进行动画处理[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)通过将其转换水平和垂直超过 1 秒 （1000年毫秒） 的实例。 [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)左侧，图像 100 像素和等于 100 个像素向上，同时将转换方法。 这是因为第一个和第二个参数均为这两个负数。 提供正数会将转换到右，运行时间和停机的映像。

以下屏幕截图显示每个平台上的进行中的转换：

![](simple-images/translateto.png "转换动画")

> [!NOTE]
> 如果元素是最初关闭屏幕布局，则在转换到屏幕上，在转换后的元素的输入的布局保持状态关闭屏幕并且用户不能与它交互。 因此，建议应在到达其最终位置，布局视图，然后任何所需执行的转换。

### <a name="fading"></a>淡入淡出

下面的代码示例演示如何使用[ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法要进行动画处理[ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/)属性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

此代码进行动画处理[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)通过淡入淡出它在超过 4 秒 （4000 毫秒为单位） 的实例。 [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法获取当前[ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/)开头的动画，然后淡化中的从该值于其第一个参数 (1) 的属性值。

以下屏幕截图显示了每个平台上的进行中的淡入淡出：

![](simple-images/fadeto.png "淡入淡出动画")

<a name="compound" />

## <a name="compound-animations"></a>复合动画

复合动画是顺序组合的动画，并且可以用来创建`await`运算符，如下面的代码示例中所示：

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

在此示例中， [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)转换超过 6 秒 （6000 毫秒为单位）。 转换`Image`使用五个动画`await`，该值指示每个动画按顺序执行的运算符。 因此，后续动画方法来执行前一种方法完成后。

<a name="composite" />

## <a name="composite-animations"></a>复合动画

复合动画，则的动画组合两个或多个动画同时运行。 可以通过混合等待和非等待动画创建复合动画，如下面的代码示例中所示：

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

在此示例中， [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)已缩放和同时旋转超过 4 秒 （4000 毫秒为单位）。 缩放`Image`使用两个在旋转在同一时间发生的顺序动画。 [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法能够执行且不`await`运算符并立即返回，第一个[ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)然后开始的动画。 `await`在第一个运算符`ScaleTo`方法调用延迟第二个`ScaleTo`直到第一个方法调用`ScaleTo`方法调用已完成。 此时`RotateTo`动画，则一半方式完成和`Image`将旋转 180 度。 在最终的 2 秒 （2000年毫秒），第二个`ScaleTo`动画和`RotateTo`同时完成动画。

### <a name="running-multiple-asynchronous-methods-concurrently"></a>同时运行多个异步方法

`static` `Task.WhenAny`和`Task.WhenAll`方法可用于进行同时运行多个异步方法，因此可以用于创建复合动画。 这两种方法返回`Task`对象，并接受方法的集合，每个返回`Task`对象。 `Task.WhenAny`方法完成其集合中的任何方法完成执行，如下面的代码示例中所示：

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

在此示例中，`Task.WhenAny`方法调用包含两个任务。 第一个任务将图像旋转超过 4 秒 （4000 毫秒为单位），并且第二个任务可以扩展映像超过 2 秒 （2000年毫秒为单位）。 第二个任务完成时`Task.WhenAny`方法调用完成。 但是，即使[ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法仍在运行，第二个[ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)方法可以开始。

`Task.WhenAll`方法完成其集合中的所有方法都完成后，如下面的代码示例中所示：

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

在此示例中，`Task.WhenAll`方法调用包含三个任务，其中每个执行超过 10 分钟。 每个`Task`使不同数量的 360 度旋转 – 307 轮替[ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)，251 轮替[ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)，和 199 循环[ `RotateYTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/). 这些值是质数，因此确保旋转的未同步，并因此不会导致重复模式。

以下屏幕截图显示每个平台上的进行中的多个旋转：

![](simple-images/multiple-rotations.png "复合动画")

## <a name="canceling-animations"></a>取消动画

应用程序可以取消通过调用的一个或多个动画`static` [ `ViewExtensions.CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/)方法，如下面的代码示例中所示：

```csharp
ViewExtensions.CancelAnimations (image);
```

此操作将立即取消所有当前运行的动画[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)实例。

## <a name="summary"></a>总结

本文说明了创建和取消使用动画[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)类。 此类提供扩展方法，可用于构造旋转、 缩放、 平移和淡入淡出的简单动画[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)实例。


## <a name="related-links"></a>相关链接

- [异步支持概述](~/cross-platform/platform/async.md)
- [基本动画 （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/basic/)
- [ViewExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)
