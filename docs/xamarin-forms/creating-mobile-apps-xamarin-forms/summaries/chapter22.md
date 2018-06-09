---
title: 章 22 的摘要。 动画
description: 使用 Xamarin.Forms 创建移动应用： 章 22 的摘要。 动画
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: b25fed9a86b82e56cb3b2bf5e3276c8ff63f4e35
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241964"
---
# <a name="summary-of-chapter-22-animation"></a>章 22 的摘要。 动画

你已了解，你可以创建动画使用 Xamarin.Forms 计时器或`Task.Delay`，但通常可以更轻松，使用提供的 Xamarin.Forms 动画功能。 三个类实现这些动画：

- [`ViewExtensions`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)高级方法
- [`Animation`](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)但更难更通用
- [`AnimationExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)最多用途、 最低级别的方法

通常情况下，由可绑定属性的属性为目标动画。 这不是要求，但它们是动态响应更改的唯一属性。

没有 XAML 界面用于这些动画，但您可以将动画集成到 XAML 使用中讨论的技术[**章 23。触发器和行为**](chapter23.md)。

## <a name="exploring-basic-animations"></a>浏览基本动画

基本动画函数是在中找到的扩展方法[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)类。 这些方法应用到派生自的任何对象`VisualElement`。 最简单的动画目标属性中所述的转换[ `Chapter 21. Transforms` ](chapter21.md)。

[ **AnimationTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout)演示如何`Clicked`事件处理程序`Button`可以调用[ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)扩展方法，以启动圆圈，圆圈中的按钮。

`RotateTo`方法更改`Rotation`属性`Button`从 0 到 360 （默认情况下） 的一个季度第二个过程。 如果`Button`点击再次，但是，它不执行任何操作因为`Rotation`属性已 360 度。

### <a name="setting-the-animation-duration"></a>设置动画持续时间

第二个参数`RotateTo`持续时间以毫秒为单位。 如果设置为较大的值，点击`Button`动画播放期间启动当前角度新动画开始。

### <a name="relative-animations"></a>相对动画

`RelRotateTo`方法通过将指定的值添加到现有值执行相对旋转。 此方法允许`Button`以多个时间和每个分流时间增加`Rotation`360 度的属性。

### <a name="awaiting-animations"></a>等待动画

中的所有动画方法`ViewExtensions`返回`Task<bool>`对象。 这意味着你可以定义一系列的使用顺序动画`ContinueWith`或`await`。 `bool`完成的返回值是`false`如果动画完成而不会中断或`true`如果取消了[ `CancelAnimation` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/)方法，这将取消所有动画由启动中的其他方法`ViewExtensions`，设置在同一元素上。

### <a name="composite-animations"></a>复合动画

可以混合等待和非等待创建复合动画的动画。 这些是中的动画`ViewExtensions`面向`TranslatonX`， `TranslationY`，和`Scale`转换属性：

- [`TranslateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)
- [`ScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.ScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)
- [`RelScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)

请注意，`TranslateTo`有可能会同时影响`TranslationX`和`TranslationY`属性。

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAll 和 Task.WhenAny

还有可能管理使用的同步动画[ `Task.WhenAll` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.WhenAll%7BTResult%7D/p/System.Collections.Generic.IEnumerable%7BSystem.Threading.Tasks.Task%7BTResult%7D%7D/)，这表示当所有结束多个任务，和[ `Task.WhenAny` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.WhenAny%7BTResult%7D/p/System.Collections.Generic.IEnumerable%7BSystem.Threading.Tasks.Task%7BTResult%7D%7D/)，这表示当多个第一个任务已结束。

### <a name="rotation-and-anchors"></a>旋转和定位点

在调用时`ScaleTo`， `RelScaleTo`， `RotateTo`，和`RelRotateTo`方法，你可以设置[ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)和[ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)属性，以指示缩放和旋转中心。

[ **CircleButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton)演示这种方法通过旋转`Button`围绕页的中心。

### <a name="easing-functions"></a>缓动函数

通常动画是线性从起始值到结束值。 缓动函数可能会导致动画来加快或减慢移动其过程。 动画方法的最后一个可选参数属于类型[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)，定义类型的 11 静态只读字段的类`Easing`:

- [`Linear`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/)默认值
- [`SinIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinIn/)[ `SinOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinOut/)，和 [`SinInOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinInOut/)
- [`CubicIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicIn/)[ `CubicOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicOut/)，和 [`CubicInOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicInOut/)
- [`BounceIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceIn/) 和 [`BounceOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceOut/)
- [`SpringIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) 和 [`SpringOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/)

`In`后缀指示的效果是动画，开头`Out`在结束时，意味着和`InOut`意味着，它是开头和末尾动画。

[ **BounceButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton)示例演示如何使用的缓动函数。

### <a name="your-own-easing-functions"></a>您自己的缓动函数

你还可以定义你自己的缓动函数通过传递[ `Func<double, double>` ](https://developer.xamarin.com/api/type/System.Func%3CT,TResult%3E/)到[`Easing`构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Easing.Easing/p/System.Func%7BSystem.Double,System.Double%7D/)。 `Easing` 此外定义了隐式转换`Func<double, double>`到`Easing`。 缓动函数的自变量始终是 0 到 1 范围内随着动画进行线性从开头到末尾。 该函数*通常*0 到 1 范围内返回一个值，但可以是简要负数或大于 1 (这与情况相同`SpringIn`和`SpringOut`函数) 或可能中断规则，如果你知道你要执行的操作。

[ **UneasyScale** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale)示例演示如何使用自定义缓动函数和[ **CustomCubicEase** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase)演示另一个。

[ **SwingButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton)示例还演示自定义缓动函数，以及更改一种技术`AnchorX`和`AnchorY`的旋转动画序列内的属性。

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库具有[ `JiggleButton` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs)类，该类使用自定义缓动函数来单击时其 jiggle 按钮。 [ **JiggleButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo)示例演示它。

### <a name="entrance-animations"></a>进入动画

当第一次显示一个页面，一种常用类型的动画时发生。 可以启动此类动画[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/)页的重写。 对于这些动画，其最大努力设置的方式页 XAML 显示*后*动画，然后初始化并进行动画处理代码中的布局。

[ **FadingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance)示例使用[ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)扩展方法淡入页的内容。

[ **SlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance)示例使用[ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)扩展方法，以从边滑入页的内容。

[ **SwingingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance)示例使用[ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)要进行动画处理的扩展方法`RotationY`属性。 A [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法也是可用。

### <a name="forever-animations"></a>不限次数动画

在其他极端情况下，"forever"动画之前运行程序将终止。 这些通常用于演示目的。

[ **FadingTextAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation)示例使用[ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)动画淡入淡出两段文本。

[**PalindromeAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation)显示 palindrome，然后按顺序将单个字母旋转 180 度，因此要对其所有颠倒。 然后整个字符串被翻转 180 度读取与原始字符串相同。

[ **CopterAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation)示例旋转简单`BoxView`直升机时旋转围绕屏幕的中心。

[**RotatingSpokes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes)需要考虑的是`BoxView`分支围绕该屏幕的中心服务器，然后旋转每个辐射本身来创建有趣的模式：

[![轮换分支服务器的三个屏幕截图](images/ch22fg21-small.png "轮换分支服务器")](images/ch22fg21-large.png#lightbox "轮换分支服务器")

但是，逐渐提高`Rotation`元素的属性可能无法工作长远来看，作为[ **RotationBreakdown** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown)示例演示如何。

[ **SpinningImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage)示例使用[ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)， [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)，和[ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)以使其看起来就像在三维空间中根本旋转位图。

### <a name="animating-the-bounds-property"></a>边界属性进行动画处理

中的唯一扩展方法`ViewExtensions`尚未演示是[ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/)，这有效地动画应用只读[ `Bounds` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Bounds/)属性通过调用[`Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/)方法。 此方法通常称为`Layout`衍生产品，如将中所述[**章 26。CustomLayouts**](chapter26.md)。

`LayoutTo`方法应被限制为特殊目的。 [ **BouncingBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox)程序使用它来压缩和展开`BoxView`如它关闭纸张的面退回。

[ **XamagonXuzzle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)示例使用`LayoutTo`移动中的磁贴的经典实现显示经过编码的映像，而不是带编号的磁贴的 15-16 拼图：

[![Xamarin Xuzzle 的三个屏幕截图](images/ch22fg26-small.png "Xuzzle 拼图游戏")](images/ch22fg26-large.png#lightbox "Xuzzle 拼图游戏")

### <a name="your-own-awaitable-animations"></a>等待动画

[ **TryAwaitableAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation)示例创建等待动画。 可以返回的重要类`Task`从方法和信号动画完成的对象是[ `TaskCompletionSource` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.TaskCompletionSource%3CTResult%3E/)。

## <a name="deeper-into-animations"></a>深入到动画

Xamarin.Forms 动画系统可以有点令人困惑。 除了`Easing`类，动画系统中有`ViewExtensions`， `Animation`，和`AnimationExtension`classses。

### <a name="viewextensions-class"></a>ViewExtensions 类

你已经了解[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)。 它定义了返回的九个方法`Task<bool>`和[ `CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/)。 七个九个方法目标转换属性。 另外两个是[ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)，均面向[ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/)属性，和[ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/)，哪些调用[ `Layout`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/)方法。

### <a name="animation-class"></a>动画类

[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)类具有[构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Animation.Animation/p/System.Action%7BSystem.Double%7D/System.Double/System.Double/Xamarin.Forms.Easing/System.Action/)具有五个参数来定义回调和完成的方法，以及参数的动画。

可以使用添加子动画[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/)， [ `Insert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Insert/p/System.Double/System.Double/Xamarin.Forms.Animation/)， [ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/Xamarin.Forms.Animation/System.Double/System.Double/)，和重载的[ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/System.Action%7BSystem.Double%7D/System.Double/System.Double/Xamarin.Forms.Easing/System.Double/System.Double/).

动画对象然后入门调用[ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BSystem.Double,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/)方法。

### <a name="animationextensions-class"></a>AnimationExtensions 类

[ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)类包含主要扩展方法。 有多个版本的`Animate`方法和泛型[ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate%7BT%7D/p/Xamarin.Forms.IAnimatable/System.String/System.Func%7BSystem.Double,T%7D/System.Action%7BT%7D/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BT,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/)方法是各种不同场合，它实际上是需要唯一动画函数。

## <a name="working-with-the-animation-class"></a>使用动画类

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)示例演示如何[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)类几个不同的动画。

### <a name="child-animations"></a>子动画

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)示例还演示的子动画，使您能够使用的 （非常类似） [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/)和[ `Insert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Insert/p/System.Double/System.Double/Xamarin.Forms.Animation/)方法。

### <a name="beyond-the-high-level-animation-methods"></a>超出高级动画方法

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)示例还演示如何执行超出所针对的属性之一的动画`ViewExtensions`方法。 在一个示例中，一系列句点变长;另举一例，`BackgroundColor`属性进行动画处理。

### <a name="more-of-your-own-awaitable-methods"></a>多个自己等待方法

[ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法`ViewExtensions`不适用于[ `Easing.SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/)函数。 它会停止时的缓动输出获取高于 1。

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库包含[ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)类，该类具有[ `TranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12)和[ `TranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49)不具有此问题的扩展方法，以及[ `CancelTranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44)和[ `CancelTranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71)取消那些方法动画。

[ **SpringSlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance)演示`TranslateXTo`方法。

`MoreExtensions`类还包含[ `TranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76)将合并 X 和 Y 转换的扩展方法和一个[ `CancelTranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113)方法。

### <a name="implementing-a-bezier-animation"></a>实现贝塞尔动画

还有可能开发动画移动的元素的贝塞尔样条路径。 [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库包含[ `BezierSpline` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs)封装的贝塞尔样条的结构和[ `BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs)控件方向的枚举。

[ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)类包含[ `BezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118)扩展方法和一个[ `CancelBezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161)方法。

[ **BezierLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop)示例演示如何对进行动画处理沿 Beizer 路径元素。

## <a name="working-with-animationextensions"></a>使用 AnimationExtensions

一种类型的标准集合中缺少是动画的颜色动画。 问题是没有正确的方式，要在其间两个`Color`值。 可以插入单个的 RGB 值，但只需为有效插值 HSL 值。

为此， [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库包含两个`Color`动画方法： [ `RgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)和[ `HslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188)。 (此外还有两个取消方法： [ `CancelRgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183)和[ `CancelHslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206))。

这两种方法使使用[ `ColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211)，这将通过调用广泛泛型执行动画[ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate%7BT%7D/p/Xamarin.Forms.IAnimatable/System.String/System.Func%7BSystem.Double,T%7D/System.Action%7BT%7D/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BT,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/)中的方法[ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)。

[ **ColorAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations)示例演示如何使用这些两种类型的颜色动画。

## <a name="structuring-your-animations"></a>构建动画

有时它可为 express 在 XAML 中的动画并将其与 MVVM 结合使用。 这一点在下一章， [**章 23。触发器和行为**](chapter23.md)。



## <a name="related-links"></a>相关链接

- [章 22 全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [章 22 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [动画](~/xamarin-forms/user-interface/animation/index.md)
