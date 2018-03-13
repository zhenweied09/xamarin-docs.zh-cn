---
title: "核心动画"
description: "本文将讨论的核心动画框架中，显示它如何使高性能、 流体动画中 UIKit，以及如何才能将其直接较低级别动画控件。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: f0cb4e00abffead854c2590bde6df45c200ff0bb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="core-animation"></a>核心动画

_本文将讨论的核心动画框架中，显示它如何使高性能、 流体动画中 UIKit，以及如何才能将其直接较低级别动画控件。_

iOS 包括[*核心动画*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html)以动画支持你的应用程序中的视图。
所有 iOS 如滚动的表和轻扫不同的视图之间中的超平滑动画执行以及做的原因是它们内部依赖于核心动画。

核心动画和核心图形框架可协同工作来创建美观，动画二维图形。 中的事实核心动画可以甚至转换二维图形 3D 空间中的创建了令人惊叹、 电影的体验。 但是，若要创建 true 3D 图形，你需要使用 OpenGL ES 如或游戏从而对 API，如 MonoGame，尽管三维功能超出了本文的范围。

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>核心动画

iOS 使用核心动画框架来创建视图之间转换，滑动菜单和滚动效果仅举几例等的动画效果。 有两种方法要使用动画：

- [通过 UIKit](#Using_UIKit_Animation)，其中包括基于视图的动画，以及动画的控制器之间的转换。
- [通过核心动画](#Using_Core_Animation)，哪些层直接，允许进行精细的控制。

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>使用 UIKit 动画

UIKit 提供了一些功能，方便地将动画添加到应用程序。 尽管它在内部使用核心动画，但它避开它以便仅使用视图和控制器。

本部分讨论 UIKit 动画功能包括：

-  控制器之间的转换
-  视图之间的转换
-  视图属性动画


### <a name="view-controller-transitions"></a>视图控制器转换

 `UIViewController` 通过查看控制器之间转换提供内置支持`PresentViewController`方法。 使用时`PresentViewController`，转换到第二个控制器可以根据需要进行动画处理。

例如，考虑两个控制器，其中接触中第一个控制器按钮调用的应用程序`PresentViewController`以显示第二个控制器。 若要控制哪些过渡动画用于显示第二个控制器，只需设置其[ `ModalTransitionStyle` ](https://developer.xamarin.com/api/type/UIKit.UIModalTransitionStyle/)属性如下所示：

```csharp
SecondViewController vc2 = new SecondViewController {
    ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

在这种情况下`PartialCurl`使用动画，而多个其他可用，包括：

-  `CoverVertical` – 滑块向上从屏幕的底部
-  `CrossDissolve` – 旧视图淡出和淡出新视图
-  `FlipHorizontal` -从右到左 a 水平翻转。 开除上转换翻转从左到右。


若要进行动画处理转换，将传递`true`作为第二个参数`PresentViewController`:

```csharp
PresentViewController (vc2, true, null);
```

下面的屏幕截图显示转换的外观`PartialCurl`用例：

 ![](core-animation-images/06-view-transitions.png "此屏幕快照显示 PartialCurl 转换")

### <a name="view-transitions"></a>视图转换

除了控制器之间的转换，UIKit 还支持要交换的另一个视图的视图之间进行动画处理的转换。

例如，假设你必须具有的控制器`UIImageView`、 在图像上的点击应该会显示第二个`UIImageView`。 要进行动画处理图像转换到第二个图像视图的视图的超级视图非常简单，只调用`UIView.Transition`，将其传递`toView`和`fromView`如下所示：

```csharp
UIView.Transition (
    fromView: view1,
    toView: view2,
    duration: 2,
    options: UIViewAnimationOptions.TransitionFlipFromTop |
        UIViewAnimationOptions.CurveEaseInOut,
    completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` 此外采用`duration`参数控制多长时间运行动画，以及[ `options` ](https://developer.xamarin.com/api/type/UIKit.UIViewAnimationOptions/)指定等动画的缓动函数使用与。 此外，你可以指定将在动画完成时调用完成处理程序。

下面显示的屏幕截图间图像的动画的转换视图时`TransitionFlipFromTop`使用：

 ![](core-animation-images/07-animated-transition.png "此屏幕快照显示间图像视图使用 TransitionFlipFromTop 时的动画的转换")

### <a name="view-property-animations"></a>视图属性动画

UIKit 支持上对各种属性进行动画处理`UIView`类免费，其中包括：

-  Frame
-  边界
-  居中
-  Alpha
-  Transform
-  颜色


通过指定中的属性更改隐式发生了这些动画`NSAction`委托传递给静态`UIView.Animate`方法。 例如，下面的代码进行动画处理的中心点`UIImageView`:

```csharp
pt = imgView.Center;

UIView.Animate (
    duration: 2, 
    delay: 0, 
    options: UIViewAnimationOptions.CurveEaseInOut | 
        UIViewAnimationOptions.Autoreverse,
    animation: () => {
        imgView.Center = new CGPoint (View.Bounds.GetMaxX () 
            - imgView.Frame.Width / 2, pt.Y);},
    completion: () => {
        imgView.Center = pt; }
);
```

这会导致映像进行动画处理来回沿顶部显示的屏幕，如下所示：

 ![](core-animation-images/08-animate-center.png "映像进行动画处理来回屏幕顶部作为输出")

与`Transition`方法，`Animate`允许的持续时间，若要设置，以及的缓动函数。 此示例还使用`UIViewAnimationOptions.Autoreverse`选项，它使动画回初始一个动画起始值。 但是，此代码还设置`Center`回其在完成处理程序中的初始价值。 虽然动画随着时间的推移插值属性值，属性的实际模型值始终是已设置的最终值。 在此示例中，值为超级视图的右边的点。 如果没有设置`Center`为初始的点，这是由于完成动画`Autoreverse`设置，该映像将管理单元返回的右侧动画完成后，如下所示：

 ![](core-animation-images/09-animation-complete.png "如果未设置到初始点的中心，图像将管理单元返回的右侧在动画完成后")

## <a name="using-core-animation"></a>使用核心动画

 `UIView` 动画允许大量的功能，并应尽可能使用由于实现的易用性。 如前所述，UIView 动画将使用核心动画框架。 但是，不能与完成的一些事项`UIView`动画，如对不能进行动画处理的视图的其他属性进行动画处理或沿非线性路径插值。 在这种情况下，需要更好的控制，可以直接以及使用核心动画。

### <a name="layers"></a>层

在使用核心动画时，动画发生情况通过*层*，类型`CALayer`。 在于存在非常层层次结构，如查看层次结构一样，层是从概念上讲类似于视图。 实际上，层回视图，与视图添加对用户交互的支持。 你可以访问通过视图的任何视图层`Layer`属性。 事实上，在使用的上下文`Draw`方法`UIView`实际创建从层。 在内部，备份的层`UIView`已设置为视图本身，这是什么调用其委托`Draw`。 因此，当绘制到`UIView`，你实际上绘制到它的层。

层动画可能会隐式或显式。 隐式动画是声明性。 你只需声明层属性应更改和动画完全可以正常运行。 另一方面，显式动画会创建通过添加到层中的动画类。 显式动画允许控制如何创建动画的添加。 下列各节深入探讨更深入的隐式和显式动画。

### <a name="implicit-animations"></a>隐式动画

层的属性进行动画处理的一种方法是通过隐式动画。 `UIView` 动画创建隐式的动画。 但是，您可以创建直接针对层以及隐式动画。

例如，下面的代码设置层的`Contents`从映像，设置边框宽度和颜色，并添加作为视图的图层的一个子图层的层：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    layer = new CALayer ();
    layer.Bounds = new CGRect (0, 0, 50, 50);
    layer.Position = new CGPoint (50, 50);
    layer.Contents = UIImage.FromFile ("monkey2.png").CGImage;
    layer.ContentsGravity = CALayer.GravityResize;
    layer.BorderWidth = 1.5f;
    layer.BorderColor = UIColor.Green.CGColor;

    View.Layer.AddSublayer (layer);
}
```

若要添加为层隐式动画，只需将包装中的属性更改`CATransaction`。 这允许对不会如视图动画中可进行动画处理的属性进行动画处理`BorderWidth`和`BorderColor`如下所示：

```csharp
public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    CATransaction.Begin ();
    CATransaction.AnimationDuration = 10;
    layer.Position = new CGPoint (50, 400);
    layer.BorderWidth = 5.0f;
    layer.BorderColor = UIColor.Red.CGColor;
    CATransaction.Commit ();
}
```

此代码还进行动画处理的层`Position`，即从左上方的 superlayer 的坐标度量的层的定位点的位置。 一个层的定位点是在该层的坐标系统内的规范化的点。

下图显示的位置和定位点：

 ![](core-animation-images/10-postion-anchorpt.png "此图显示的位置和定位点")

当运行示例时， `Position`，`BorderWidth`和`BorderColor`进行动画处理，如以下屏幕截图中所示：

 ![](core-animation-images/11-implicit-animation.png "当运行示例时，位置、 BorderWidth 和边框颜色进行动画处理所示")

### <a name="explicit-animations"></a>显式动画

除了隐式动画核心动画包括继承的类`CAAnimation`，使您能够封装然后显式添加到层中的动画。 这些行为允许精细的控制，如修改动画的起始值、 分组动画以及指定为允许非线性路径的关键帧的动画。

下面的代码演示显式动画使用的示例`CAKeyframeAnimation`层更早版本 （在隐式动画部分） 所示：

```csharp
public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);
    
    // get the initial value to start the animation from
    CGPoint fromPt = layer.Position;
    
    /* set the position to coincide with the final animation value
    to prevent it from snapping back to the starting position
    after the animation completes*/
    layer.Position = new CGPoint (200, 300);
    
    // create a path for the animation to follow
    CGPath path = new CGPath ();
    path.AddLines (new CGPoint[] { fromPt, new CGPoint (50, 300), new CGPoint (200, 50), new CGPoint (200, 300) });
    
    // create a keyframe animation for the position using the path
    CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
    animPosition.Path = path;
    animPosition.Duration = 2;
    
    // add the animation to the layer.
    /* the "position" key is used to overwrite the implicit animation created
    when the layer positino is set above*/
    layer.AddAnimation (animPosition, "position");
}
```

此代码将更改`Position`层，通过创建然后用于定义关键帧动画的路径。 请注意，在该层的`Position`设置为的最终值`Position`从动画。 如果没有此，层将突然返回到其`Position`动画之前因为动画仅更改的演示文稿值而不是实际模型值。 通过将模型值设置为的最终值，从动画，层保持不变末尾的动画。

以下屏幕截图显示包含通过指定路径的映像动画处理的图层：

 ![](core-animation-images/12-explicit-animation.png "此屏幕快照显示包含通过指定路径的映像动画处理的图层")
 
## <a name="summary"></a>摘要

在本文中我们讨论过的动画功能，通过提供*核心动画*框架。 我们检查核心动画，显示它为 UIKit 中的动画提供支持，又如何可以直接为较低级别动画控件使用它。

## <a name="related-links"></a>相关链接

- [核心动画示例](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [核心图形](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [图形和动画演练](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [核心动画](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
