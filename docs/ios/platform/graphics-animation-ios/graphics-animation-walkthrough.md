---
title: "演练-使用 CoreGraphics 和 CoreAnimation"
description: "本文逐步演示如何创建使用核心图形和核心动画的应用程序。 它显示在响应用户触摸屏幕上绘制的方式，以及如何进行动画处理要沿路径移动的图像。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: c62601ff446c114e97e9d4c2ded3727d08220095
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="drawing-and-animating-along-a-path"></a>绘制和沿路径进行动画处理

在本演练中我们将绘制在响应中使用核心图形触摸屏输入的路径。 然后，我们将添加`CALayer`包含我们将沿着路径对进行动画处理的映像。

以下屏幕截图显示完成的应用程序：

![](graphics-animation-walkthrough-images/00-final-app.png "已完成的应用程序")

我们开始下载之前*GraphicsDemo*附带本指南的示例。 可以下载[此处](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)和位于**GraphicsWalkthrough**目录启动名为的项目**GraphicsDemo_starter**通过双击它，并打开`DemoView`类。

## <a name="drawing-a-path"></a>绘制路径


1. 在`DemoView`添加`CGPath`变量到类和实例构造函数中。 也声明两个`CGPoint`变量，`initialPoint`和`latestPoint`，我们将使用来捕获从中我们构造路径的触摸点：
    
    ```csharp
    public class DemoView : UIView
    {
        CGPath path;
        CGPoint initialPoint;
        CGPoint latestPoint;
    
        public DemoView ()
        {
            BackgroundColor = UIColor.White;
    
            path = new CGPath ();
        }
    }
    ```

2. 添加以下 using 指令：

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. 接下来，重写`TouchesBegan`和`TouchesMoved,`并添加以下实现分别捕获初始触摸点和每个后续触摸点：

    ```csharp
    public override void TouchesBegan (NSSet touches, UIEvent evt){
    
        base.TouchesBegan (touches, evt);
    
        UITouch touch = touches.AnyObject as UITouch;
        
        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }
    
    public override void TouchesMoved (NSSet touches, UIEvent evt){
    
        base.TouchesMoved (touches, evt);
    
        UITouch touch = touches.AnyObject as UITouch;
        
        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }
    ```

    `SetNeedsDisplay` 将收尾工作将按顺序为每次调用`Draw`在下次运行的循环通过上调用。

4. 我们将添加到的路径中的行`Draw`方法并使用红色的虚线线绘制所使用。 [实现`Draw`](~/ios/platform/graphics-animation-ios/core-graphics.md)与下面显示的代码：

    ```csharp
    public override void Draw (CGRect rect){
    
        base.Draw (rect);
    
        if (!initialPoint.IsEmpty) {
    
            //get graphics context
            using(CGContext g = UIGraphics.GetCurrentContext ()){
                    
                //set up drawing attributes
                g.SetLineWidth (2);
                UIColor.Red.SetStroke ();
    
                //add lines to the touch points
                if (path.IsEmpty) {
                    path.AddLines (new CGPoint[]{initialPoint, latestPoint});
                } else {
                    path.AddLineToPoint (latestPoint);
                }
            
                //use a dashed line
                g.SetLineDash (0, new nfloat[] { 5, 2 * (nfloat)Math.PI });
                                
                //add geometry to graphics context and draw it
                g.AddPath (path);       
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
    ```

如果我们运行应用程序现在，我们可以触摸要绘制的屏幕上，如下面的屏幕截图中所示：

![](graphics-animation-walkthrough-images/01-path.png "在屏幕上绘制")

## <a name="animating-along-a-path"></a>沿着路径对进行动画处理

现在，我们已实现的代码，以允许用户绘制的路径，让我们添加代码以进行动画处理的层沿绘制的路径。

1. 首先，添加[ `CALayer` ](~/ios/platform/graphics-animation-ios/core-animation.md)到类变量的构造函数中创建它：

    ```csharp
    public class DemoView : UIView
        {
            …
    
            CALayer layer;
    
            public DemoView (){
                …
    
                //create layer
                layer = new CALayer ();
                layer.Bounds = new CGRect (0, 0, 50, 50);
                layer.Position = new CGPoint (50, 50);
                layer.Contents = UIImage.FromFile ("monkey.png").CGImage;
                layer.ContentsGravity = CALayer.GravityResizeAspect;
                layer.BorderWidth = 1.5f;
                layer.CornerRadius = 5;
                layer.BorderColor = UIColor.Blue.CGColor;
                layer.BackgroundColor = UIColor.Purple.CGColor;
            }
    ```

2. 接下来，我们将为视图的图层的子层添加层，当用户将提升其手指从屏幕。 然后，我们将创建使用的路径，一个关键帧动画进行动画处理的层`Position`。

    若要完成此我们需要重写`TouchesEnded`并添加以下代码：

    ```csharp
    public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            base.TouchesEnded (touches, evt);

            //add layer with image and animate along path

            if (layer.SuperLayer == null)
                Layer.AddSublayer (layer);

            // create a keyframe animation for the position using the path
            layer.Position = latestPoint;
            CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
            animPosition.Path = path;
            animPosition.Duration = 3;
            layer.AddAnimation (animPosition, "position");
        }
    ```

3. 现在，并绘制后，请运行应用程序，与映像层添加和旅行沿绘制的路径：

![](graphics-animation-walkthrough-images/00-final-app.png "与图像层添加和旅行沿绘制路径")

## <a name="summary"></a>摘要

在本文中，我们会单步遍历的示例，绑定在一起的图形和动画的概念。 首先，我们介绍了如何使用核心图形绘制的路径`UIView`以响应用户触摸屏输入。 然后，我们介绍了如何使用核心动画来使沿该路径移动图像。


## <a name="related-links"></a>相关链接

- [核心动画](~/ios/platform/graphics-animation-ios/core-animation.md)
- [核心图形](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [核心动画配方](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
