---
title: TextKit
description: "使用文本工具包 API 提供功能强大的文本中 Xamarin.iOS 的布局和呈现功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: d3a370c3a8d930c817d38422b249b496532fa33f
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="text-kit"></a>文本工具包

文本工具包是一种新的 API，提供功能强大的文本布局和呈现功能。 它构建在较低级别核心文本框架之上，但可以更轻松地使用比核心文本。

要使文本工具包的功能可用于标准控件，多个 iOS 文本控件已被重新实现，若要使用文本工具包，其中包括：

-  UITextView
-  UITextField
-  UILabel


## <a name="architecture"></a>体系结构

文本工具包提供的布局和显示，包括以下类分离开来的文本存储分层体系结构：

-  `NSTextContainer` – 提供的坐标系统和用于对布局文本的几何图形。
-  `NSLayoutManager` – 布局到标志符号旋转文本的文本表示。 
-  `NSTextStorage` – 包含文本数据，以及处理批处理文本属性更新。 任何批处理更新在送达更改，例如重新计算布局，并且重新绘制文本的实际处理的布局管理器。


这三个类适用于在呈现文本的视图。 处理视图，如内置文本`UITextView`， `UITextField`，和`UILabel`已有这些设置，但你可以创建并将它们应用于任何`UIView`以及实例。

下图阐释了这种体系结构：

 ![](textkit-images/textkitarch.png "下图说明了文本工具包体系结构")

## <a name="text-storage-and-attributes"></a>文本存储和属性

`NSTextStorage`类包含由视图显示的文本。 它还通信对 text-如对字符的更改或其属性-显示的布局管理器的任何更改。 `NSTextStorage` 继承自`MSMutableAttributed`字符串，允许文本属性之间的批次中指定的更改`BeginEditing`和`EndEditing`调用。

例如，下面的代码段指定变为前台和背景颜色，分别，而且有针对性的特定范围：

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

后`EndEditing`是调用，所做的更改发送到布局管理器，后者反过来执行任何必要的布局和视图中显示的文本的呈现计算。

## <a name="layout-with-exclusion-path"></a>排除路径的布局

文本工具包还支持布局，并允许为复杂的方案，如多列文本和指定的路径流动文本调用*排除路径*。 排除路径会应用到修改文本布局，从而导致文本围绕指定的路径排列的几何图形的文本容器中。

添加排除路径需要设置`ExclusionPaths`布局管理器上的属性。 设置此属性将导致布局管理器使无效文本布局并流周围排除路径的文本。

### <a name="exclusion-based-on-a-cgpath"></a>基于 CGPath 排除

请考虑以下`UITextView`子类实现：

```csharp
public class ExclusionPathView : UITextView
{
    CGPath exclusionPath;
    CGPoint initialPoint;
    CGPoint latestPoint;
    UIBezierPath bezierPath;

    public ExclusionPathView (string text)
    {
        Text = text;
        ContentInset = new UIEdgeInsets (20, 0, 0, 0);
        BackgroundColor = UIColor.White;
        exclusionPath = new CGPath ();
        bezierPath = UIBezierPath.Create ();

        LayoutManager.AllowsNonContiguousLayout = false;
    }

    public override void TouchesBegan (NSSet touches, UIEvent evt)
    {
        base.TouchesBegan (touches, evt);

        var touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt)
    {
        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }

    public override void TouchesEnded (NSSet touches, UIEvent evt)
    {
        base.TouchesEnded (touches, evt);

        bezierPath.CGPath = exclusionPath;
        TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
    }

    public override void Draw (CGRect rect)
    {
        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            using (var g = UIGraphics.GetCurrentContext ()) {

                g.SetLineWidth (4);
                UIColor.Blue.SetStroke ();

                if (exclusionPath.IsEmpty) {
                    exclusionPath.AddLines (new CGPoint[] { initialPoint, latestPoint });
                } else {
                    exclusionPath.AddLineToPoint (latestPoint);
                }

                g.AddPath (exclusionPath);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
}
```

此代码添加在文本视图使用核心图形上绘制的支持。 由于`UITextView`类现在构建要用于其文本呈现和布局的文本工具包，它可以使用的文本工具包，如设置排除路径的所有功能。

> [!IMPORTANT]
> 此示例子类`UITextView`添加触摸绘图支持。 子类化`UITextView`无需获取文本工具包的功能。



用户绘制上绘制的文本视图后`CGPath`应用于`UIBezierPath`通过设置实例`UIBezierPath.CGPath`属性：

```csharp
bezierPath.CGPath = exclusionPath;
```

更新以下代码行使更新在路径两边的文本布局：

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

下面的屏幕截图演示了如何更改文本布局周围绘制的路径流动：

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")--> 
![](textkit-images/exclusionpath2.png "此屏幕截图演示了如何更改文本布局周围绘制的路径流动")

请注意，布局管理器的`AllowsNonContiguousLayout`属性设置为 false，在这种情况下。 这将导致重新计算所有用例的文本更改其中的布局。 将此值设置为 true 获益性能通过避免全布局刷新，尤其是对于大型文档。 但是，将设置`AllowsNonContiguousLayout`到 true 会阻止排除路径更新布局在某些情况下-例如，如果文本输入在运行时，没有尾随的回车符返回之前正在设置的路径。


## <a name="related-links"></a>相关链接

- [IOS 7 （示例） 的简介](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 用户界面概述](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [后台处理](~/ios/app-fundamentals/backgrounding/index.md)
