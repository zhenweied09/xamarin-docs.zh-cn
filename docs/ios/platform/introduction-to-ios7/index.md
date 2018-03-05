---
title: "IOS 7 简介"
description: "本文介绍如何在 iOS 7，包括视图控制器转换，UIView 动画，UIKit Dynamics 和文本工具包的增强功能中引入的主要新 Api。 它还介绍了一些更改其用户界面和新的增强多任务功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a7bebc2b73ecb564028a92340c726bd5c1f1c54b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-ios-7"></a>IOS 7 简介

_本文介绍如何在 iOS 7，包括视图控制器转换，UIView 动画，UIKit Dynamics 和文本工具包的增强功能中引入的主要新 Api。它还介绍了一些更改其用户界面和新的增强多任务功能。_

iOS 7 是一个重大更新到 iOS。 它引入了将焦点置于内容，而不是应用程序的镶边全新的用户界面设计。 可视更改，以及 iOS 7 将添加大量新的 Api 来创建更丰富的交互和体验。 此文档调查新技术引入 iOS 7，并充当起点进行进一步的研究。

## <a name="uiview-animation-enhancements"></a>UIView 动画增强功能

iOS 7 增强 UIKit，允许应用程序执行以前需要执行直接到核心动画框架的删除操作中的动画支持。 例如， `UIView` spring 动画，以及关键帧动画，现在可以执行先前`CAKeyframeAnimation`应用于`CALayer`。

### <a name="spring-animations"></a>弹簧动画

 `UIView` 现在支持使用 spring 效果进行动画处理属性更改。 若要添加该列，调用`AnimateNotify`或`AnimateNotifyAsync`方法，传入值 spring 阻止比率和初始 spring 速度，如下所述：

-  `springWithDampingRatio` – 介于 0 和 1，其中振荡会对较小的值增加。
-  `initialSpringVelocity` – 每秒的总动画距离的百分比初始 spring 速度。


下面的代码产生 spring 效果图像视图的中心更改时：

```csharp
void AnimateWithSpring ()
{ 
    float springDampingRatio = 0.25f;
    float initialSpringVelocity = 1.0f;
    
    UIView.AnimateNotify (3.0, 0.0, springDampingRatio, initialSpringVelocity, 0, () => {
    
        imageView.Center = new CGPoint (imageView.Center.X, 400);   
            
    }, null);
}
```

此 spring 效果可使图像视图能够看上去像反弹在完成到新的中心位置，其动画，如下所示：

 ![](images/spring-animation.png "此 spring 效果可使图像视图能够看上去像反弹在完成到新的中心位置其动画")

### <a name="keyframe-animations"></a>关键帧动画

`UIView`类现在包括`AnimateWithKeyframes`方法用于创建上的关键帧动画`UIView`。 此方法类似于其他是`UIView`动画方法，除了其他`NSAction`传递作为参数，以包括关键帧。 在`NSAction`，通过调用添加的关键帧`UIView.AddKeyframeWithRelativeStartTime`。

例如，下面的代码段将创建一个关键帧的动画动画并旋转视图以及视图的中心：

```csharp
void AnimateViewWithKeyframes ()
{
    var initialTransform = imageView.Transform;
    var initialCeneter = imageView.Center;

    // can now use keyframes directly on UIView without needing to drop directly into Core Animation

    UIView.AnimateKeyframes (2.0, 0, UIViewKeyframeAnimationOptions.Autoreverse, () => {
        UIView.AddKeyframeWithRelativeStartTime (0.0, 0.5, () => { 
            imageView.Center = new CGPoint (200, 200);
        });

        UIView.AddKeyframeWithRelativeStartTime (0.5, 0.5, () => { 
            imageView.Transform = CGAffineTransform.MakeRotation ((float)Math.PI / 2);
        });
    }, (finished) => {
        imageView.Center = initialCeneter;
        imageView.Transform = initialTransform;

        AnimateWithSpring ();
    });
}
```

前两个参数`AddKeyframeWithRelativeStartTime`方法指定的开始时间和持续时间的关键帧，分别为总体动画长度的百分比。 在下一步的第二个旋转 90 度跟导致图像视图进行动画处理到其新的中心通过第一秒中，上面的示例。 由于动画指定`UIViewKeyframeAnimationOptions.Autoreverse`作为一个选项，这两个关键帧动画按相反的顺序以及。 最后，最终值设置为完成处理程序中的初始状态。

下面的屏幕截图阐释了组合的动画关键帧通过：

 ![](images/keyframes.png "此屏幕截图演示组合通过关键帧动画")

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics 是一组新的 UIKit 允许应用程序创建基于物理动画的交互的 Api。 UIKit Dynamics 封装一个二维物理引擎来做到这一点。

API 是声明性本质。 声明的物理交互通过创建对象的调用的行为方式*行为*-快速物理概念，例如重力、 冲突、 springs，等等。然后将 behavior(s) 附加到另一个对象，调用*动态生成器*，封装视图。 动态生成器采用关注的声明的物理将行为应用于*动态项*-项实现`IUIDynamicItem`，如`UIView`。

有几个不同的基元行为可用来触发复杂的交互，包括：

-  `UIAttachmentBehavior` – 附加两个动态的项，以便它们一起，移动或将动态项附加到一个连接点。
-  `UICollisionBehavior` – 允许动态要参与冲突的项。
-  `UIDynamicItemBehavior` – 指定一组常规的属性将应用于动态项，如弹性、 密度和摩擦。
-  `UIGravityBehavior` -适用于动态项，从而导致项来加快引力方向重力。
-  `UIPushBehavior` – 适用于动态项 force。
-  `UISnapBehavior` – 允许 spring 效果与将其置于管理单元动态项。


尽管有许多基元，向使用 UIKit Dynamics 视图添加基于物理的交互的一般过程在跨行为是一致的：

1.  创建动态生成器。
1.  创建 behavior(s)。
1.  将行为添加到动态生成器。


### <a name="dynamics-example"></a>Dynamics 示例

让我们看一下的示例，将添加重力以及到一个冲突边界`UIView`。

#### <a name="uigravitybehavior"></a>UIGravityBehavior

向映像视图添加重力遵循上述 3 个步骤。

我们将在工作`ViewDidLoad`对于此示例的方法。 首先，添加`UIImageView`实例，如下所示：

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

这将创建屏幕的顶部边缘居中的图像视图。 若要使用重力使映像"回退"，创建的实例`UIDynamicAnimator`:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

`UIDynamicAnimator`采用的引用的实例`UIView`或`UICollectionViewLayout`，其中包含将每个附加 behavior(s) 进行动画处理的项。

接下来，创建`UIGravityBehavior`实例。 你可以将传递实现的一个或多个对象`IUIDynamicItem`、 like `UIView`:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

行为传递的数组`IUIDynamicItem`，在这种情况下，其中包含单个`UIImageView`我们进行动画处理的实例。

最后，将行为添加到动态生成器：

```csharp
dynAnimator.AddBehavior (gravity);
```

这会导致与重力，作为如下所示的向下进行动画处理的映像：

![](images/gravity2.png "起始映像位置") 
![](images/gravity3.png "结束的图像位置")

由于没有任何约束屏幕的边界，图像视图只需将关闭底部。 若要约束的视图，以便与屏幕边缘冲突的映像，我们可以添加`UICollisionBehavior`。 在下一部分中，我们将介绍这。

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

我们将首先通过创建`UICollisionBehavior`并将其添加到动态生成器，就像我们所做一样`UIGravityBehavior`。

修改代码以包括`UICollisionBehavior`:

```csharp
using (image = UIImage.FromFile ("monkeys.jpg")) {

    imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
        Image =  image
    };

    View.AddSubview (imageView);

    // 1. create the dynamic animator
    dynAnimator = new UIDynamicAnimator (this.View);

    // 2. create behavior(s)
    var gravity = new UIGravityBehavior (imageView);
    var collision = new UICollisionBehavior (imageView) {
        TranslatesReferenceBoundsIntoBoundary = true
    };

    // 3. add behaviors(s) to the dynamic animator
    dynAnimator.AddBehaviors (gravity, collision);
}
```

`UICollisionBehavior`具有称为属性`TranslatesReferenceBoundsIntoBoundry`。 此选项设置为`true`导致引用视图的边界用作冲突边界。

现在，当映像进行动画处理向下与重力，它退回略有关闭屏幕的底部才选定存在其他部分。

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

我们可以进一步控制其他行为的行为的下降的图像视图。 例如，我们无法添加`UIDynamicItemBehavior`以提高灵活性，导致图像视图时它与屏幕的底部冲突反弹的详细信息。

添加`UIDynamicItemBehavior`遵循相同的步骤与其他行为。 首先创建行为：

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

然后，将行为添加到动态生成器：

 `dynAnimator.AddBehavior (dynBehavior);`

与就地此行为，图像视图退回的详细信息时它与边界冲突。

## <a name="general-user-interface-changes"></a>常规用户界面更改

除了新的 UIKit Api 如 UIKit Dynamics、 控制器转换以及增强的 UIView 动画上面所述，iOS 7 引入了各种到 UI，可视更改和相关的 API 更改各种视图和控件。 有关详细信息请参阅[iOS 7 用户界面概述](~/ios/platform/introduction-to-ios7/ios7-ui.md)。

## <a name="text-kit"></a>文本工具包

文本工具包是一种新的 API，提供功能强大的文本布局和呈现功能。 它构建在较低级别核心文本框架之上，但可以更轻松地使用比核心文本。

有关详细信息，请参阅我们[TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>多任务

iOS 7 更改何时以及如何执行后台工作。 完成任务在 iOS 7 中的不再使应用程序保持处于唤醒状态时在后台，正在运行的任务和应用程序都被唤醒，以非连续的方式处理的背景。 iOS 7 还用在后台的新内容更新应用程序添加三个新的 Api:

-  背景提取 – 允许应用程序以更新在固定时间间隔后台的内容。
-  远程通知-允许应用程序以接收推送通知时，更新内容。 通知可以是无提示或可以在锁定屏幕上显示横幅。
-  后台传输服务 – 允许上载和下载的数据，例如大型文件，而无需按固定的时间限制。


有关新的多任务功能的详细信息，请参阅 Xamarin iOS 部分[Backgrounding 指南](~/ios/app-fundamentals/backgrounding/index.md)。

## <a name="summary"></a>摘要

本文介绍几个主要新添加的内容到 iOS。 首先，它演示如何将自定义转换添加到视图控制器。 然后，它演示如何使用在集合视图中，同时从内的导航控制器，以及以交互方式集合视图之间的转换。 接下来，它引入了对 UIView 动画，显示应用程序如何使用 UIKit 的以前需要执行针对核心动画直接编程的操作所做的几项增强功能。 最后，新 UIKit Dynamics API，这将向 UIKit 提供物理引擎，是与文本工具包框架中当前可用的多格式文本支持一起引入的。

## <a name="related-links"></a>相关链接

- [IOS 7 （示例） 的简介](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 用户界面概述](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [后台处理](~/ios/app-fundamentals/backgrounding/index.md)
