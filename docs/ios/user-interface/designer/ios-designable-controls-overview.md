---
title: "IOS 的 Xamarin 设计器中的自定义控件"
description: "Xamarin 设计器中为 iOS 支持呈现自定义控件在你的项目中创建或从外部源，如 Xamarin 组件应用商店中引用。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 83ec11ab6a17717dd9556122745afc8d87959186
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>IOS 的 Xamarin 设计器中的自定义控件

_Xamarin 设计器中为 iOS 支持呈现自定义控件在你的项目中创建或从外部源，如 Xamarin 组件应用商店中引用。_

Xamarin 设计器中为 iOS 是可视化应用程序的用户界面的强大工具，并提供所见即所得编辑大多数 iOS 视图和视图控制器的支持。 你的应用程序也可能包含扩展的内置于 iOS 的自定义控件。 如果这些自定义控件编写与记住的一些准则，它们可以还呈现 ios 设计器中，提供甚至更丰富的编辑体验。 本文档将介绍这些准则。

## <a name="requirements"></a>惠?

将设计图面上呈现的控件，可满足以下要求：

1.  它是直接或间接的子类[UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)或[UIViewController](https://developer.xamarin.com/api/type/UIKit.UIView/Controller)。 其他[NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/)子类将显示为设计图面上的图标。
2.  它具有[RegisterAttribute](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/)将其公开到目标 c。
3.  它具有[必需的 IntPtr 构造函数](~/ios/internals/api-design/index.md)。
4.  它可以实现[IComponent](https://developer.xamarin.com/api/type/System.ComponentModel.IComponent/)接口或具有[DesignTimeVisibleAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.DesignTimeVisibleAttribute/)设置为 True。

在代码中定义的控件满足上述要求会在设计器中显示其包含的项目编译为模拟器时。 默认情况下，所有自定义控件将显示在**自定义组件**部分**工具箱**。 但是， [CategoryAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.CategoryAttribute/)可以应用到自定义控件的类，以指定的其他部分。

设计器不支持加载第三方 OBJECTIVE-C 的库。

## <a name="custom-properties"></a>自定义属性

声明由自定义控件的属性将显示在属性面板中，如果满足以下条件：

1.  该属性具有公共 getter 和 setter。
1.  该属性具有[ExportAttribute](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/)以及[BrowsableAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.BrowsableAttribute/)设置为 True。
1.  该属性类型是数值类型、 枚举类型、 string、 bool 和[SizeF](https://developer.xamarin.com/api/type/System.Drawing.SizeF/)， [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/)，或[UIImage](https://developer.xamarin.com/api/type/UIKit.UIImage/)。 可能在将来扩展支持类型的列表。


此外可以用修饰属性[DisplayNameAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.DisplayNameAttribute/)若要指定为其属性面板中显示的标签。

## <a name="initialization"></a>初始化

有关`UIViewController`子类，应使用[ViewDidLoad](https://developer.xamarin.com/api/member/UIKit.UIViewController.ViewDidLoad/)取决于你在设计器中创建的视图的代码的方法。

有关`UIView`和其他`NSObject`子类， [AwakeFromNib](https://developer.xamarin.com/api/member/Foundation.NSObject.AwakeFromNib/)方法是从布局文件加载后执行自定义控件的初始化的建议的位置。 这是因为当运行时控件的构造函数，但会将它们设置之前，不会设置在属性面板中设置任何自定义属性`AwakeFromNib`调用：


```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        // Initialize the view here.
    }
}
```

如果该控件还旨在代码中直接创建，你可能想要创建具有常见初始化，类似下面的代码的方法：

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
    }
}
```

## <a name="property-initialization-and-awakefromnib"></a>属性初始化和 AwakeFromNib

有关何时以及如何初始化中并不会覆盖已在 iOS 设计器内设置的值的自定义组件可设计属性应格外小心。 作为示例，请执行下面的代码：

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {
    
    [Export ("Counter"), Browsable (true)]
    public int Counter {get; set;}

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
        Counter = 0;
    }
}
```

`CustomView`组件公开`Counter`可以由内部 iOS 设计器开发人员设置的属性。 但是，无论在设计器的值设置哪些值`Counter`属性将始终为零 (0)。 原因如下：

-  实例`CustomControl`放大从情节提要文件。
-  设置在 iOS 设计器中修改任何属性 (如设置的值`Counter`到两 （2)，例如)。
-  `AwakeFromNib`执行方法和调用组件的`Initialize`方法。
-  内部`Initialize`的值`Counter`属性将重置为零 (0)。


若要解决上述情况，或者初始化`Counter`属性在其他位置 （例如，在组件的构造函数） 或不会覆盖`AwakeFromNib`方法并且调用`Initialize`如果该组件不需要任何进一步初始化外部内容当前正在处理由其构造函数。

## <a name="design-mode"></a>设计模式

设计图面上的自定义控件必须遵循一些限制：

-  应用程序捆绑资源不可用在设计模式下。 映像是通过在加载时可用[UIImage 方法](https://developer.xamarin.com/api/type/UIKit.UIImage/%2fM)。
-  不应在设计模式下执行异步操作，例如 web 请求。 设计图面不支持动画或控件的 UI 的任何其他异步更新。


自定义控件可以实现[IComponent](https://developer.xamarin.com/api/type/System.ComponentModel.IComponent/)并用[以下](https://developer.xamarin.com/api/property/System.ComponentModel.ISite.DesignMode/)属性检查是否为设计图面上。 在此示例中，该标签将在设计图面和"运行时"显示"设计模式"，在运行时：

```csharp
[Register ("DesignerAwareLabel")]
public class DesignerAwareLabel : UILabel, IComponent {

    #region IComponent implementation

    public ISite Site { get; set; }
    public event EventHandler Disposed;

    #endregion

    public DesignerAwareLabel (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        if (Site != null &amp;&amp; Site.DesignMode)
            Text = "Design Mode";
        else
            Text = "Runtime";
    }
}
```

应始终检查`Site`属性`null`然后再尝试访问它的任何成员。 如果`Site`是`null`，则可以安全地假设控件未在设计器中运行。
在设计模式下，`Site`将控件的构造函数已运行之后，并在设置`AwakeFromNib`调用。

## <a name="debugging"></a>调试

将在工具箱中显示满足上述要求的控件，并将其图面上呈现。
如果不呈现控件，请检查该控件或其依赖项之一中的 bug。

设计图面通常可以捕获同时继续呈现其他控件由单独的控件引发的异常。 发生故障的控件将被替换的红色占位符，你可以通过单击感叹号图标查看异常跟踪：

 ![](ios-designable-controls-overview-images/exception-box.png "将发生故障的控件为红色占位符和异常详细信息")

如果调试符号是可用于该控件，跟踪必须文件名和行号。 双击某一行中的堆栈跟踪将跳转到在源代码行。

如果设计器不能隔离故障的控制，一条警告消息将显示在设计图面顶部：

 ![](ios-designable-controls-overview-images/info-bar.png "在设计图面顶部一条警告消息")

当发生故障的控件是修复或删除从设计图面时，将恢复完整呈现。

## <a name="summary"></a>摘要

这篇文章引入的创建和 iOS 设计器中的自定义控件的应用程序。 首先，它描述为设计图面上呈现和公开这样的属性面板中的自定义属性，控件必须满足的要求。 它然后看背后的初始化的控件并将以下属性的代码。 最后，它描述会发生什么情况时引发的异常以及如何解决此问题。