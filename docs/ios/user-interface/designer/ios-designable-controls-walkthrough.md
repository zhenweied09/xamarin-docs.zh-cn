---
title: 演练-与 Xamarin 设计器使用 iOS 自定义控件
description: 本文提供了演示如何创建自定义控件和将其 Xamarin 设计器中用于 iOS 的分步演练。 它演示如何使控件在设计器的工具箱中可用，因此它可以拖动/放置到一个视图。 此外，它演示如何实现控件，因此它正确呈现在设计时和运行时，以及如何创建可以在设计时设置的属性。
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 4a7fb6cba82b52f2a3506df7a36b4813a88ff583
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---using-custom-controls-with-the-xamarin-designer-for-ios"></a>演练-与 Xamarin 设计器使用 iOS 自定义控件

_本文提供了演示如何创建自定义控件和将其 Xamarin 设计器中用于 iOS 的分步演练。它演示如何使控件在设计器的工具箱中可用，因此它可以拖动/放置到一个视图。此外，它演示如何实现控件，因此它正确呈现在设计时和运行时，以及如何创建可以在设计时设置的属性。_

## <a name="requirements"></a>要求

Xamarin 设计器中为 iOS 是针对 Mac 和 Visual Studio 2015 和 2017 Windows 上的 Visual Studio 中提供。

本指南假定你熟悉中介绍的内容[入门指导](~/ios/get-started/index.md)。

## <a name="walkthrough"></a>演练

> [!IMPORTANT]
> 在 Xamarin.Studio 5.5 中启动，在其中创建自定义控件的方法是到早期版本略有不同。 若要创建自定义控件，或者`IComponent`界面是必需 （使用相关的实现方法中） 或的类可以是使用批注`[DesignTimeVisible(true)]`。 在下面的演练示例正在使用后一种方法。


1. 创建新的解决方案从**iOS > 应用程序 > 单视图应用程序 > C#**模板，将其命名为`ScratchTicket`，并继续完成新项目向导：

    [![](ios-designable-controls-walkthrough-images/01new.png "创建新的解决方案")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. 创建名为的新的空类文件`ScratchTicketView`:

    [![](ios-designable-controls-walkthrough-images/02new.png "创建一个新的 ScratchTicketView 类")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. 添加以下代码`ScratchTicketView`类：

    ```csharp
    using System;
    using System.ComponentModel;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace ScratchTicket
    {
        [Register("ScratchTicketView"), DesignTimeVisible(true)]
        public class ScratchTicketView : UIView
        {
            CGPath path;
            CGPoint initialPoint;
            CGPoint latestPoint;
            bool startNewPath = false;
            UIImage image;
    
            [Export("Image"), Browsable(true)]
            public UIImage Image
            {
                get { return image; }
                set
                {
                    image = value;
                    SetNeedsDisplay();
                }
            }
    
            public ScratchTicketView(IntPtr p)
                : base(p)
            {
                Initialize();
            }
    
            public ScratchTicketView()
            {
                Initialize();
            }
    
            void Initialize()
            {
                initialPoint = CGPoint.Empty;
                latestPoint = CGPoint.Empty;
                BackgroundColor = UIColor.Clear;
                Opaque = false;
                path = new CGPath();
                SetNeedsDisplay();
            }
    
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                var touch = touches.AnyObject as UITouch;
    
                if (touch != null)
                {
                    initialPoint = touch.LocationInView(this);
                }
            }
    
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                var touch = touches.AnyObject as UITouch;
    
                if (touch != null)
                {
                    latestPoint = touch.LocationInView(this);
                    SetNeedsDisplay();
                }
            }
    
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                startNewPath = true;
            }
    
            public override void Draw(CGRect rect)
            {
                base.Draw(rect);
    
                using (var g = UIGraphics.GetCurrentContext())
                {
                    if (image != null)
                        g.SetFillColor((UIColor.FromPatternImage(image).CGColor));
                    else
                        g.SetFillColor(UIColor.LightGray.CGColor);
                    g.FillRect(rect);
    
                    if (!initialPoint.IsEmpty)
                    {
                        g.SetLineWidth(20);
                        g.SetBlendMode(CGBlendMode.Clear);
                        UIColor.Clear.SetColor();
    
                        if (path.IsEmpty || startNewPath)
                        {
                            path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                            startNewPath = false;
                        }
                        else
                        {
                            path.AddLineToPoint(latestPoint);
                        }
    
                        g.SetLineCap(CGLineCap.Round);
                        g.AddPath(path);        
                        g.DrawPath(CGPathDrawingMode.Stroke);
                    }
                }
            }
        }
    }
    ```


1. 添加`FillTexture.png`，`FillTexture2.png`和`Monkey.png`文件 (可用[从 GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) 到**资源**文件夹。
    
1. 双击`Main.storyboard`文件以在设计器中打开它：

    [![](ios-designable-controls-walkthrough-images/03new.png "IOS 设计器")](ios-designable-controls-walkthrough-images/03new.png#lightbox)


1. 拖放**图像视图**从**工具箱**到情节提要中的视图。

    [![](ios-designable-controls-walkthrough-images/04new.png "图像视图添加到布局")](ios-designable-controls-walkthrough-images/04new.png#lightbox)


1. 选择**图像视图**并更改其**映像**属性`Monkey.png`。

    [![] (ios-可设计的控件的演练-映像/05new.png Monkey.png 到"设置图像视图图像属性)](ios-designable-controls-walkthrough-images/05new.png#lightbox)

    
1. 随着我们使用大小类我们将需要限制此图像视图。 单击此图像两次以将其放置约束模式。 让我们通过单击 center 固定句柄到中心约束它，并将其对齐同时垂直和水平方向：

    [![](ios-designable-controls-walkthrough-images/06new.png "使映像内居中")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. 若要将限制的高度和宽度，单击大小固定句柄 （骨骼调整句柄），并分别选择宽度和高度：

    [![](ios-designable-controls-walkthrough-images/07new.png "将约束添加")](ios-designable-controls-walkthrough-images/07new.png#lightbox)


1. 更新基于通过单击工具栏中的更新按钮的约束的帧：

    [![](ios-designable-controls-walkthrough-images/08new.png "约束工具栏")](ios-designable-controls-walkthrough-images/08new.png#lightbox)


1. 接下来，生成项目，以便**暂存票证视图**将出现在**自定义组件**工具箱中：

    [![](ios-designable-controls-walkthrough-images/09new.png "自定义组件工具箱")](ios-designable-controls-walkthrough-images/09new.png#lightbox)


1. 拖放式**暂存票证视图**以使其 monkey 图像上方显示。 调整拖动手柄，让暂存票证视图涵盖 monkey 完全，如下所示：

    [![](ios-designable-controls-walkthrough-images/10new.png "暂存票证视图，通过该图像视图")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. 通过绘制边框选择这两个视图来限制到图像视图的暂存票证视图。 选择相应选项以对其进行约束到宽度、 高度、 中心和中间和更新帧基于约束，如下所示：

    [![](ios-designable-controls-walkthrough-images/11new.png "居中和添加约束")](ios-designable-controls-walkthrough-images/11new.png#lightbox)


1. 运行应用程序和"暂存关闭"以显示 monkey 的映像。

    [![](ios-designable-controls-walkthrough-images/10-app.png "运行示例应用程序")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>添加设计时属性

设计器还包括自定义控件的属性类型数值、 枚举、 字符串、 bool、 CGSize、 UIColor 和 UIImage 的设计时支持。 为了演示，让我们将属性添加到`ScratchTicketView`设置为"简单关闭。"的映像

以下代码添加到`ScratchTicketView`属性的类：

```csharp
[Export("Image"), Browsable(true)]
public UIImage Image 
{
    get { return image; }
    set { 
            image = value;
            SetNeedsDisplay ();
        }
}
```

我们可能还想要添加到的 null 检查`Draw`方法，如下所示：

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (var g = UIGraphics.GetCurrentContext())
    {
        if (image != null)
            g.SetFillColor ((UIColor.FromPatternImage (image).CGColor));
        else
            g.SetFillColor (UIColor.LightGray.CGColor);
            
        g.FillRect(rect);

        if (!initialPoint.IsEmpty)
        {
             g.SetLineWidth(20);
             g.SetBlendMode(CGBlendMode.Clear);
             UIColor.Clear.SetColor();

             if (path.IsEmpty || startNewPath)
             {
                 path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                 startNewPath = false;
             }
             else
             {
                 path.AddLineToPoint(latestPoint);
             }

             g.SetLineCap(CGLineCap.Round);
             g.AddPath(path);       
             g.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

包括`ExportAttribute`和`BrowsableAttribute`参数设置为`true`显示在设计器的属性会导致**属性**面板。 将属性更改为另一个映像包含项目后，如`FillTexture2.png`，导致控件也随之更新在设计时，如下所示：

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "编辑设计时属性")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>总结

在本文中我们演练如何创建一个自定义控件，以及使用 iOS 设计器的 iOS 应用程序中使用它。 我们已了解如何创建和生成的控件，以使其可用的应用程序在设计器的**工具箱**。 此外，我们介绍了如何实现控件，以便它能在设计时和运行时，正确呈现，以及如何公开设计器中的自定义控件属性。



## <a name="related-links"></a>相关链接

- [ScratchTicket （示例）](https://developer.xamarin.com/samples/monotouch/ScratchTicket/)
- [所需的映像 （示例）](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
