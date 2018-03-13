---
title: "视图控制器转换"
ms.topic: article
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 88849d3007c007a5ac8820ca84083aa01459c4ce
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="view-controller-transitions"></a>视图控制器转换

UIKit 添加自定义提供视图控制器时发生的动画的转换的支持。 此支持仅包含与内置控制器，以及从直接继承任何自定义控制器`UIViewController`。 此外，`UICollectionViewController`利用控制器转换自定义项，利用集合视图布局中的动画的转换。

## <a name="custom-transitions"></a>自定义转换

在 iOS 7 中的视图控制器间的动画的转换是完全可自定义。 `UIViewController` 现在包括`TransitioningDelegate`在转换发生时提供到系统的自定义作为动画制作者类的属性。

若要使用自定义转换与`PresentViewController`:

1.  设置`ModalPresentationStyle`到`UIModalPresentationStyle.Custom`呈现在控制器上。
2.  实现`UIViewControllerTransitioningDelegate`创建作为动画制作者类，即实例`UIViewControllerAnimatedTransitioning`。
3.  设置`TransitioningDelegate`指向的实例的属性`UIViewControllerTransitioningDelegate`，还在控制器上显示。
4.  提供视图控制器。


例如，下面的代码提供的类型的视图控制器`ControllerTwo`-`UIViewController`子类：

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

运行应用并点击按钮会导致第二个控制器视图中进行动画处理的底部，默认动画，如下所示：

 ![](transitions-images/no-custom-transition.png "运行应用并点击按钮使第二个的控制器视图，在动画起始底部的默认动画")

但是，将设置`ModalPresentationStyle`和`TransitioningDelegate`转换自定义动画将导致：

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo () {
        ModalPresentationStyle = UIModalPresentationStyle.Custom;
        };

    transitioningDelegate = new TransitioningDelegate ();
    controllerTwo.TransitioningDelegate = transitioningDelegate;

    this.PresentViewController (controllerTwo, true, null);
};
```

`TransitioningDelegate`负责创建的实例`UIViewControllerAnimatedTransitioning`子类-调用`CustomAnimator`在下面的示例：

```csharp
public class TransitioningDelegate : UIViewControllerTransitioningDelegate
{
    CustomTransitionAnimator animator;

    public override IUIViewControllerAnimatedTransitioning PresentingController (UIViewController presented, UIViewController presenting, UIViewController source)
    {
        animator = new CustomTransitionAnimator ();
        return animator;
    }
}
```

系统在转换发生时创建的实例`IUIViewControllerContextTransitioning`，其传递到生成器的方法。 `IUIViewControllerContextTransitioning` 包含`ContainerView`动画出现的位置，以及视图控制器启动转换和转换到的视图控制器。

`UIViewControllerAnimatedTransitioning`类处理实际的动画。 必须实现两种方法：

1.  `TransitionDuration` – 以秒为单位返回动画持续时间。
1.  `AnimateTransition` – 执行实际的动画。


例如，下面的类实现`UIViewControllerAnimatedTransitioning`要进行动画处理的控制器视图的框架：

```csharp
public class CustomTransitionAnimator : UIViewControllerAnimatedTransitioning
{
    public CustomTransitionAnimator ()
    {
    }

    public override double TransitionDuration (IUIViewControllerContextTransitioning transitionContext)
    {
        return 1.0;
    }

    public override void AnimateTransition (IUIViewControllerContextTransitioning transitionContext)
        {
            var inView = transitionContext.ContainerView;
            var toVC = transitionContext.GetViewControllerForKey (UITransitionContext.ToViewControllerKey);
            var toView = toVC.View;

            inView.AddSubview (toView);

            var frame = toView.Frame;
            toView.Frame = CGRect.Empty;

            UIView.Animate (TransitionDuration (transitionContext), () => {
                toView.Frame = new CGRect (20, 20, frame.Width - 40, frame.Height - 40);
            }, () => {
                transitionContext.CompleteTransition (true);
            });
        }
}
```

现在，当点击按钮，动画中实现`UIViewControllerAnimatedTransitioning`使用类：

 ![](transitions-images/custom-transition.png "下面举例说明有效运行的缩放")

## <a name="collection-view-transitions"></a>集合视图转换

集合视图具有用于创建动画的过渡的内置支持：

-  **导航控制器**-进行动画处理两个之间的转换`UICollectionViewController`实例可以根据需要自动处理时`UINavigationController`对其进行管理。
-  **转换布局**– 一个新`UICollectionViewTransitionLayout`类允许交互式布局之间转换。


### <a name="navigation-controller-transitions"></a>导航控制器转换

内的导航控制器中，使用时`UICollectionViewController`包括对控制器之间的动画转换的支持。 此支持是内置的并需要仅几个简单步骤来实现：

1.  设置`UseLayoutToLayoutNavigationTransitions`到`false`上`UICollectionViewController`。
1.  将的一个实例添加`UICollectionViewController`导航控制器堆栈的根。
1.  创建第二个`UICollectionViewController`并设置其`UseLayoutToLayoutNavigtionTransitions`属性`true`。
1.  推送第二个`UICollectionViewController`到导航控制器的堆栈。


下面的代码添加`UICollectionViewController`子类名为`ImagesCollectionViewController`导航控制器的堆栈的根与`UseLayoutToLayoutNavigationTransitions`属性设置为`false`:

```csharp
UIWindow window;
ImagesCollectionViewController viewController;
UICollectionViewFlowLayout layout;
UINavigationController navController;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    window = new UIWindow (UIScreen.MainScreen.Bounds);

    // create and initialize a UICollectionViewFlowLayout
    layout = new UICollectionViewFlowLayout (){
        SectionInset = new UIEdgeInsets (10,5,10,5),
        MinimumInteritemSpacing = 5,
        MinimumLineSpacing = 5,
        ItemSize = new CGSize (100, 100)
    };

    viewController = new ImagesCollectionViewController (layout) {
            UseLayoutToLayoutNavigationTransitions = false;
        }

    navController = new UINavigationController (viewController);

    window.RootViewController = navController;
    window.MakeKeyAndVisible ();
    
    return true;
}
```

如果选择项，第二个实例`ImagesController`创建，这次使用一个不同的布局的类。 此控制器`UseLayoutToLayoutNavigtionTransitions`设置为`true`，如下所示：

```csharp
CircleLayout circleLayout;
ImagesCollectionViewController controller2;

...

public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // UseLayoutToLayoutNavigationTransitions when item is selected
        circleLayout = new CircleLayout (Monkeys.Instance.Count){
                ItemSize = new CGSize (100, 100)
            };
            
    controller2 = new ImagesCollectionViewController (circleLayout) {
        UseLayoutToLayoutNavigationTransitions = true;
        }

    NavigationController.PushViewController (controller2, true);
}
```

`UseLayoutToLayoutNavigationTransitions`属性必须在将控制器添加到导航堆栈之前设置。 此属性设置后，正常水平的滑动转换替换为两个控制器中，在布局之间的动画转换如下所示：

![](transitions-images/nav2.png "动画的两个控制器在布局之间转换")

### <a name="transition-layout"></a>转换布局

导航控制器中的布局转换支持，除了新的布局，调用`UICollectionViewTransitionLayout`现已可用。 此布局类允许在交互式控制在布局转换过程中，通过允许`TransitionProgress`若要从代码中设置。 `UICollectionViewTransitionLayout` 从不同的和不能替代对-`SetCollectionViewLayout`从 iOS 6 导致动画的布局转换发生的方法。 该方法不未提供内置支持，用于控制动画转换的进度。

 `UICollectionViewTransitionLayout` 例如，允许笔势识别器要进行配置以控制在响应用户交互，通过管理原始布局，以及转换为预期的布局中的布局间的转换。

实现内使用手势识别器交互式转换的步骤`UICollectionViewTransitionLayout`如下所示：

1.  创建笔势识别器。
1.  调用`StartInteractiveTransition`方法`UICollectionView`，将其传递目标布局和完成处理程序。
1.  设置`TransitionProgress`属性`UICollectionViewTransitionLayout`从返回的实例`StartInteractiveTransition`方法。
1.  使无效布局。
1.  调用`FinishInteractiveTransition`方法`UICollectionView`若要完成转换或`CancelInteractiveTransition`方法取消它。  `FinishInteractiveTransition` 使动画完成转换到目标布局中，而`CancelInteractiveTransition`导致动画返回到原始的布局。
1.  处理完成处理程序中的转换完成`StartInteractiveTransition`方法。
1.  将笔势识别器添加到集合视图。


下面的代码实现捏合手势识别器中的交互式布局转换：

```csharp
imagesController = new ImagesCollectionViewController (flowLayout);

nfloat sf = 0.4f;
UICollectionViewTransitionLayout trLayout = null;
UICollectionViewLayout nextLayout;

pinch = new UIPinchGestureRecognizer (g => {

    var progress = Math.Abs(1.0f -  g.Scale)/sf;

    if(trLayout == null){
        if(imagesController.CollectionView.CollectionViewLayout is CircleLayout)
            nextLayout = flowLayout;
        else
            nextLayout = circleLayout;

        trLayout = imagesController.CollectionView.StartInteractiveTransition (nextLayout, (completed, finished) => {   
            Console.WriteLine ("transition completed");
            trLayout = null;
        });
    }

    trLayout.TransitionProgress = (nfloat)progress;

    imagesController.CollectionView.CollectionViewLayout.InvalidateLayout ();

    if(g.State == UIGestureRecognizerState.Ended){
        if (trLayout.TransitionProgress > 0.5f)
            imagesController.CollectionView.FinishInteractiveTransition ();
        else
            imagesController.CollectionView.CancelInteractiveTransition ();
    }

});

imagesController.CollectionView.AddGestureRecognizer (pinch);

```

用户 pinches 集合视图中，如`TransitionProgress`相对于刻度捏合设置。 在此实现中，如果用户结束捏合之前的转换属 50%完成，则取消转换。 否则，完成转换。




## <a name="related-links"></a>相关链接

- [IOS 7 （示例） 的简介](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 用户界面概述](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [后台处理](~/ios/app-fundamentals/backgrounding/index.md)
