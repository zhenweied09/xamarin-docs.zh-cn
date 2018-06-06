---
title: 演练： 在 Xamarin.iOS 中使用触摸
description: 本文档介绍如何处理触摸讨论示例触摸交互、 手势识别器和自定义笔势识别器的 Xamarin.iOS 应用程序中。
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: fff49599d3843bb09d407316d6964ca54b6a1004
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784785"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>演练： 在 Xamarin.iOS 中使用触摸

本演练演示如何编写对不同类型的触控事件做出响应代码。 每个示例包含在单独的屏幕：

- [Touch 示例](#Touch_Samples)– 如何对触控事件做出响应。
- [笔势识别器示例](#Gesture_Recognizer_Samples)– 如何使用内置笔势识别器。
- [自定义笔势识别器示例](#Custom_Gesture_Recognizer)– 如何构建自定义笔势识别器。

每个部分包含说明从头开始编写代码。
[启动示例代码](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)已包含完整的情节提要和菜单屏幕：

 [![](ios-touch-walkthrough-images/image3.png "此示例包含菜单屏幕")](ios-touch-walkthrough-images/image3.png#lightbox)

按照下面将代码添加到情节提要，并了解有关触控事件可用在 iOS 中的不同类型的说明。 或者，打开[已完成的示例](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)若要查看使用的所有内容。

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>Touch 示例

在此示例中，我们将演示一些触摸 Api。 请按照下列步骤以添加需实现触控事件的代码：


1. 打开项目**Touch_Start**。 运行该项目的第一个以确保一切都好了和触摸**Touch 示例**按钮。 （尽管没有任何按钮将起作用），你应看到类似于以下屏幕：
    
    [![](ios-touch-walkthrough-images/image4.png "使用非工作按钮运行示例应用程序")](ios-touch-walkthrough-images/image4.png#lightbox)


1. 编辑文件**TouchViewController.cs**并将以下两个实例变量添加到类`TouchViewController`:

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```


1. 实现`TouchesBegan`方法，如下面的代码中所示：

    ```csharp 
    public override void TouchesBegan(NSSet touches, UIEvent evt)
    {
        base.TouchesBegan(touches, evt);
    
        // If Multitouch is enabled, report the number of fingers down
        TouchStatus.Text = string.Format ("Number of fingers {0}", touches.Count);
    
        // Get the current touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            // Check to see if any of the images have been touched
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Fist image touched
                TouchImage.Image = UIImage.FromBundle("TouchMe_Touched.png");
                TouchStatus.Text = "Touches Began";
            } else if (touch.TapCount == 2 && DoubleTouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Second image double-tapped, toggle bitmap
                if (imageHighlighted)
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
                    TouchStatus.Text = "Double-Tapped Off";
                }
                else
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
                    TouchStatus.Text = "Double-Tapped On";
                }
                imageHighlighted = !imageHighlighted;
            } else if (DragImage.Frame.Contains(touch.LocationInView(View)))
            {
                // Third image touched, prepare to drag
                touchStartedInside = true;
            }
        }
    }
    ```
    
    此方法的工作原理是检查`UITouch`对象，以及如果它存在执行基于触摸发生一些操作：

    * _内部 TouchImage_ – 显示文本`Touches Began`标签和更改映像中。
    * _内部 DoubleTouchImage_ – 更改显示如果笔势双点击的图像。
    * _内部 DragImage_ – 设置一个标志，指示已开始触摸屏输入。 该方法`TouchesMoved`将使用此标志来确定如果`DragImage`应移动屏幕，正如我们应看到在下一步。

    上面的代码仅处理各个收尾工作，则仍没有行为如果用户在屏幕上移动其手指。 若要对移动做出响应，实现`TouchesMoved`中下面的代码所示：

    ```csharp 
    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchStatus.Text = "Touches Moved";
            }

            //==== IMAGE DRAG
            // check to see if the touch started in the drag me image
            if (touchStartedInside)
            {
                // move the shape
                float offsetX = touch.PreviousLocationInView(View).X - touch.LocationInView(View).X;
                float offsetY = touch.PreviousLocationInView(View).Y - touch.LocationInView(View).Y;
                DragImage.Frame = new RectangleF(new PointF(DragImage.Frame.X - offsetX, DragImage.Frame.Y - offsetY), DragImage.Frame.Size);
            }
        }
    }
    ```

    此方法获取`UITouch`对象，然后检查以查看何处出现触摸屏输入。 如果触摸发生在`TouchImage`，然后收尾工作移动显示在屏幕的文本。 

    如果`touchStartedInside`为 true，则我们知道用户对拥有其手指`DragImage`和来回移动。 代码将移动`DragImage`在用户移动其手指在屏幕。

1. 我们需要处理的情况，用户将提升到屏幕外，他或她手指或 iOS 取消触摸事件时。 为此，我们将实现`TouchesEnded`和`TouchesCancelled`如下所示：

    ```csharp
    public override void TouchesCancelled(NSSet touches, UIEvent evt)
    {
        base.TouchesCancelled(touches, evt);
    
        // reset our tracking flags
        touchStartedInside = false;
        TouchImage.Image = UIImage.FromBundle("TouchMe.png");
        TouchStatus.Text = "";
    }
    
    public override void TouchesEnded(NSSet touches, UIEvent evt)
    {
        base.TouchesEnded(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchImage.Image = UIImage.FromBundle("TouchMe.png");
                TouchStatus.Text = "Touches Ended";
            }
        }
        // reset our tracking flags
        touchStartedInside = false;
    }
    ```
    
    这两种方法将重置`touchStartedInside`标志设为 false。 `TouchesEnded` 此外将显示`TouchesEnded`在屏幕上。

1. 此时结束 Touch 示例屏幕。 请注意如何屏幕更改当你进行交互时与每个映像，如下面的屏幕截图中所示：
        
    [![](ios-touch-walkthrough-images/image4.png "起始的应用程序屏幕")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "屏幕后用户拖动按钮")](ios-touch-walkthrough-images/image5.png#lightbox)
 

<a name="Gesture_Recognizer_Samples" />

##  <a name="gesture-recognizer-samples"></a>笔势识别器示例

[上一节](#Touch_Samples)演示了如何通过使用触控事件拖动围绕屏幕对象。
本部分中我们将消除触控事件，并演示如何使用以下笔势识别器：

-  `UIPanGestureRecognizer`拖动围绕屏幕图像。
-  `UITapGestureRecognizer`响应在屏幕上的双精度分流。

如果你运行[启动示例代码](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)，然后单击**笔势识别器示例**按钮，你应看到以下屏幕：

 [![](ios-touch-walkthrough-images/image6.png "单击手势识别器示例按钮会显示此屏幕")](ios-touch-walkthrough-images/image6.png#lightbox)

请按照下列步骤以实现笔势识别器操作：


1. 编辑文件**GestureViewController.cs**并添加以下实例变量：

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    我们需要此实例变量来跟踪的映像之前的位置。
平移笔势识别器将使用`originalImageFrame`值来计算在屏幕上重绘图像所需的偏移量。

1. 将以下方法添加到控制器：

    ```csharp
    private void WireUpDragGestureRecognizer()
    {
        // Create a new tap gesture
        UIPanGestureRecognizer gesture = new UIPanGestureRecognizer();
    
        // Wire up the event handler (have to use a selector)
        gesture.AddTarget(() => HandleDrag(gesture));  // to be defined
    
        // Add the gesture recognizer to the view
        DragImage.AddGestureRecognizer(gesture);
    }
    ```

    此代码实例化`UIPanGestureRecognizer`实例，并将其添加到视图。
请注意我们将目标分配给方法的形式在笔势`HandleDrag`– 提供此方法是在下一步。

1. 若要实现 HandleDrag，请到控制器中添加以下代码：

    ```csharp
    private void HandleDrag(UIPanGestureRecognizer recognizer)
    {
        // If it's just began, cache the location of the image
        if (recognizer.State == UIGestureRecognizerState.Began)
        {
            originalImageFrame = DragImage.Frame;
        }
    
        // Move the image if the gesture is valid
        if (recognizer.State != (UIGestureRecognizerState.Cancelled | UIGestureRecognizerState.Failed
            | UIGestureRecognizerState.Possible))
        {
            // Move the image by adding the offset to the object's frame
            PointF offset = recognizer.TranslationInView(DragImage);
            RectangleF newFrame = originalImageFrame;
            newFrame.Offset(offset.X, offset.Y);
            DragImage.Frame = newFrame;
        }
    }
    ```

    上面的代码将先检查笔势识别器的状态，然后移动在屏幕的映像。 使用此代码中的位置，控制器现在可以支持拖动在屏幕的一个映像。


1. 添加`UITapGestureRecognizer`那会将更改 DoubleTouchImage 中正在显示的图像。 添加以下方法`GestureViewController`控制器：

    ```csharp
    private void WireUpTapGestureRecognizer()
    {
        // Create a new tap gesture
        UITapGestureRecognizer tapGesture = null;
    
        // Report touch
        Action action = () => {
            TouchStatus.Text = string.Format("Image touched at: {0}",tapGesture.LocationOfTouch(0, DoubleTouchImage));
    
            // Toggle the image
            if (imageHighlighted)
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
            }
            else
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
            }
            imageHighlighted = !imageHighlighted;
        };
    
        tapGesture = new UITapGestureRecognizer(action);
    
        // Configure it
        tapGesture.NumberOfTapsRequired = 2;
    
        // Add the gesture recognizer to the view
        DoubleTouchImage.AddGestureRecognizer(tapGesture);
    }
    ```

    此代码是非常相似的代码`UIPanGestureRecognizer`但而不是我们将使用的目标使用委托`Action`。 

1. 我们需要做最后一件事情是修改`ViewDidLoad`，以便它调用我们刚添加的方法。 更改 ViewDidLoad，以使它类似于下面的代码：

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        Title = "Gesture Recognizers";
    
        // Save initial state
        originalImageFrame = DragImage.Frame;
    
        WireUpTapGestureRecognizer();
        WireUpDragGestureRecognizer();
    }
    ```

    我们初始化的值也请注意`originalImageFrame`。


1. 运行该应用程序，并使用两个图像进行交互。
下面的屏幕截图是这些交互的一个示例：
    
    [![](ios-touch-walkthrough-images/image7.png "此屏幕快照显示拖交互")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>自定义笔势识别器

在本部分中我们将前面几节来生成自定义笔势识别器中应用的概念。 自定义笔势识别器将子类`UIGestureRecognizer`，并将在用户绘制"V"时识别在屏幕上，然后切换位图。 下面的屏幕截图演示了此屏幕：

 [![](ios-touch-walkthrough-images/image8.png "用户在屏幕上绘制 V 时，将识别应用程序")](ios-touch-walkthrough-images/image8.png#lightbox)

按照这些步骤创建自定义笔势识别器：


1. 将新类添加到名为的项目`CheckmarkGestureRecognizer`，并使它看上去类似下列代码：

    ```csharp
    using System;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace Touch
    {
        public class CheckmarkGestureRecognizer : UIGestureRecognizer
        {
            #region Private Variables
            private CGPoint midpoint = CGPoint.Empty;
            private bool strokeUp = false;
            #endregion
    
            #region Override Methods
            /// <summary>
            ///   Called when the touches end or the recognizer state fails
            /// </summary>
            public override void Reset()
            {
                base.Reset();
    
                strokeUp = false;
                midpoint = CGPoint.Empty;
            }
    
            /// <summary>
            ///   Is called when the fingers touch the screen.
            /// </summary>
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                // we want one and only one finger
                if (touches.Count != 1)
                {
                    base.State = UIGestureRecognizerState.Failed;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the touches are cancelled due to a phone call, etc.
            /// </summary>
            public override void TouchesCancelled(NSSet touches, UIEvent evt)
            {
                base.TouchesCancelled(touches, evt);
                // we fail the recognizer so that there isn't unexpected behavior
                // if the application comes back into view
                base.State = UIGestureRecognizerState.Failed;
            }
    
            /// <summary>
            ///   Called when the fingers lift off the screen
            /// </summary>
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                //
                if (base.State == UIGestureRecognizerState.Possible && strokeUp)
                {
                    base.State = UIGestureRecognizerState.Recognized;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the fingers move
            /// </summary>
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                // if we haven't already failed
                if (base.State != UIGestureRecognizerState.Failed)
                {
                    // get the current and previous touch point
                    CGPoint newPoint = (touches.AnyObject as UITouch).LocationInView(View);
                    CGPoint previousPoint = (touches.AnyObject as UITouch).PreviousLocationInView(View);
    
                    // if we're not already on the upstroke
                    if (!strokeUp)
                    {
                        // if we're moving down, just continue to set the midpoint at
                        // whatever point we're at. when we start to stroke up, it'll stick
                        // as the last point before we upticked
                        if (newPoint.X >= previousPoint.X && newPoint.Y >= previousPoint.Y)
                        {
                            midpoint = newPoint;
                        }
                        // if we're stroking up (moving right x and up y [y axis is flipped])
                        else if (newPoint.X >= previousPoint.X && newPoint.Y <= previousPoint.Y)
                        {
                            strokeUp = true;
                        }
                        // otherwise, we fail the recognizer
                        else
                        {
                            base.State = UIGestureRecognizerState.Failed;
                        }
                    }
                }
    
                Console.WriteLine(base.State.ToString());
            }
            #endregion
        }
    }
    ```

    重置方法调用时`State`属性更改为`Recognized`或`Ended`。 这是要重置在自定义笔势识别器中设置任何内部状态的时间。
现在类可以重新开始在用户交互应用程序后下, 一次，并随时可以重新尝试识别手势。



1. 既然我们已经定义自定义笔势识别器 (`CheckmarkGestureRecognizer`) 编辑**CustomGestureViewController.cs**文件并添加以下两个实例变量：

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. 若要实例化和配置我们笔势识别器，将以下方法添加到控制器：

    ```csharp
    private void WireUpCheckmarkGestureRecognizer()
    {
        // Create the recognizer
        checkmarkGesture = new CheckmarkGestureRecognizer();
    
        // Wire up the event handler
        checkmarkGesture.AddTarget(() => {
            if (checkmarkGesture.State == (UIGestureRecognizerState.Recognized | UIGestureRecognizerState.Ended))
            {
                if (isChecked)
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Unchecked.png");
                }
                else
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Checked.png");
                }
                isChecked = !isChecked;
            }
        });
    
        // Add the gesture recognizer to the view
        View.AddGestureRecognizer(checkmarkGesture);
    }
    ```

1. 编辑`ViewDidLoad`，以便它调用`WireUpCheckmarkGestureRecognizer`，下面的代码段中所示：

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. 运行该应用程序，并尝试在屏幕上绘制"V"。 你应看到图像显示更改，如以下屏幕截图中所示：
    
    [![](ios-touch-walkthrough-images/image9.png "检查按钮")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "取消选中的按钮")](ios-touch-walkthrough-images/image10.png#lightbox)



上述三个部分表现出不同的方式来响应 touch 在 iOS 中的事件： 使用触控事件，内置笔势识别器，或使用自定义笔势识别器。



## <a name="related-links"></a>相关链接

- [iOS Touch 启动 （示例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS 最终 Touch （示例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
