---
title: 在 Xamarin.iOS 中创建用户界面对象
description: 本文档概述了如何在 Xamarin.iOS 中创建用户界面。 它讨论 iOS 设计器、 Xcode 接口生成器、 C# 和情节提要。
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: c688dcdf7498b0a2860d1878d893beae4f5cf8fc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790147"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>在 Xamarin.iOS 中创建用户界面对象

Apple 组相关功能集成到等同于 Xamarin.iOS 命名空间的"框架"的部分。 `UIKit` 是包含适用于 iOS 的所有用户界面控件的命名空间。

每当你的代码需要引用用户界面控件，如标签或按钮，请记住包括以下 using 语句：

```csharp
using UIKit;
```

这一章中所述的所有控件都均在 UIKit 命名空间，并且每个用户控件类名具有`UI`前缀。

你可以编辑 UI 控件和在以下三种方式中的布局：

-  **[Xamarin iOS 设计器](~/ios/user-interface/designer/index.md)** – 使用 Xamarin 的内置布局设计器设计屏幕。 双击情节提要或 XIB 文件以使用内置设计器进行编辑。
-  **Xcode 接口生成器**– 将控件拖到屏幕的布局与接口生成器。 通过右键单击中的文件在 Xcode 中打开情节提要或 XIB 文件**解决方案 Pad**并选择**打开 > Xcode 接口生成器**。
-  **使用 C#** – 控件可以此外以编程方式使用代码构造的并且添加到视图层次结构。

可以通过右键单击 iOS 项目，然后选择添加新的情节提要和 XIB 文件**添加 > 新文件...**.

无论你使用哪种方法控制属性和事件仍然使用 C# 应用程序逻辑中操作。

## <a name="using-xamarin-ios-designer"></a>使用 Xamarin iOS 设计器

若要开始在 iOS 设计器中创建你的用户界面，双击情节提要文件。 可以将控件拖动到设计图面从**工具箱**如下所示：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

 [![](creating-ui-objects-images/image2b.png "工具箱填充")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 [![](creating-ui-objects-images/image2b-vs.png "工具箱 Pad-Visual Stuio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

当在设计图面上选择了一个控件**属性填充**将显示该控件的属性。 **小组件 > 标识 > 名称**字段，在下面的屏幕截图中填充，并用作*Outlet*名称。 这是如何引用 C# 中的控件：

 [![](creating-ui-objects-images/image3b.png "属性小组件填充")](creating-ui-objects-images/image3b.png#lightbox)

有关更深入了解使用 iOS 设计器，请参阅[iOS 设计器简介](~/ios/user-interface/designer/introduction.md)指南。

## <a name="using-xcode-interface-builder"></a>使用 Xcode 接口生成器

如果你熟悉使用接口生成器，请参阅 Apple 的[接口生成器](https://developer.apple.com/xcode/interface-builder/)文档。

若要在 Xcode 中打开情节提要，右键单击访问情节提要文件的上下文菜单并选择使用打开**Xcode 接口生成器**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

 [![](creating-ui-objects-images/imagexcode.png "情节提要上下文菜单-Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](creating-ui-objects-images/imagexcode-vs.png "情节提要上下文菜单-Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

控件可以拖动到设计图面从上**对象库**下面所示：

 [![](creating-ui-objects-images/image5a.png "Xcode 对象库")](creating-ui-objects-images/image5a.png#lightbox)

当您设计你的 UI 使用接口生成器必须创建**Outlet**为你想要在 C# 中引用的每个控件。 这可通过打开**助手编辑器中**使用中心**编辑器**Xcode 工具栏按钮的按钮：

 [![](creating-ui-objects-images/image6a.png "助手编辑器按钮")](creating-ui-objects-images/image6a.png#lightbox)

单击用户界面对象;然后**控件拖**到.h 文件。 到 * * 控件拖 * *，按住 control 密钥，然后单击并按住通过用户界面对象，您正在为创建 outlet （或操作）。 保留按住 Control 密钥，拖动到标头文件时。 完成下面拖动`@interface`定义。 下面的屏幕截图中所示，标题插入电源插座或 Outlet 集合应显示一条蓝线。

当你释放单击系统将提示你提供 Outlet，将用于在代码中创建可以引用一个 C# 属性的名称：

 [![](creating-ui-objects-images/image8a.png "创建 outlet")](creating-ui-objects-images/image8a.png#lightbox)

有关如何 Xcode 的接口生成器与集成 Visual Studio for Mac 的详细信息，请参阅[Xib 代码生成](~/ios/internals/xib-code-generation.md#generated)文档。

##  <a name="using-c"></a>使用 C#

如果你决定以编程方式创建使用 C# （在视图或视图控制器，例如） 的用户界面对象，请按照下列步骤：

-  声明的用户界面对象的类级字段。 创建该控件本身在中一次，`ViewDidLoad`例如。 然后可以在整个生命周期方法中的视图控制器 （如引用的对象
`ViewWillAppear`）格式模式中出现的位置生成。
-  创建`CGRect`，它定义控件 （在屏幕上，以及其宽度和高度其 X 和 Y 坐标表示） 的帧。 你将需要确保你具有`using CoreGraphics`此指令。
-  调用构造函数以创建并分配控件。
-  设置任何属性或事件处理程序。
-  调用`Add()`控件添加到视图层次结构。

下面是创建一个简单示例`UILabel`视图控制器中使用 C#:

```csharp
UILabel label1;
public override void ViewDidLoad () {
    base.ViewDidLoad ();
    var frame = new CGRect(10, 10, 300, 30);
    label1 = new UILabel(frame);
    label1.Text = "New Label";
    View.Add (label1);
}
```

<a name="partial_classes" />

## <a name="using-c-and-storyboards"></a>使用 C# 和情节提要

查看控制器添加到设计图面时，将在项目中创建两个相应的 C# 文件。 在此示例中，`ControlsViewController.cs`和`ControlsViewController.designer.cs`自动创建：

 [![](creating-ui-objects-images/image9b.png "ViewController 分部类")](creating-ui-objects-images/image9b.png#lightbox)

`MainViewController.cs`文件用于在*代码*。 这就是`View`生命周期方法，如`ViewDidLoad`和`ViewWillAppear`实现和你可在其中添加你自己的属性、 字段和方法。

`ControlsViewController.designer.cs`生成的代码包含一个分部类。 当名称对该设计的控件为 Mac 上，在 Visual Studio 中呈现，或创建 Xcode、 对应的属性或分部方法中的电源插座或操作的时被添加到设计器 (designer.cs) 文件中。 下面的代码演示的两个按钮和文本视图中，为生成的代码示例，其中的一个按钮还具有`TouchUpInside`事件。

分部类中，这些元素启用你的代码以引用的控件并响应设计图面声明的操作：

```csharp
[Register ("ControlsViewController")]
    partial class ControlsViewController
    {
        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button1 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button2 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UITextField textfield1 { get; set; }

        [Action ("button2_TouchUpInside:")]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        partial void button2_TouchUpInside (UIButton sender);

        void ReleaseDesignerOutlets ()
        {
            if (Button1 != null) {
                Button1.Dispose ();
                Button1 = null;
            }
            if (Button2 != null) {
                Button2.Dispose ();
                Button2 = null;
            }
            if (textfield1 != null) {
                textfield1.Dispose ();
                textfield1 = null;
            }
        }
    }
}
```

`designer.cs`不应手动编辑文件 – IDE (Visual Studio for Mac 或 Visual Studio) 负责，使其保持同步情节提要。

用户界面对象添加中时以编程方式添加`View`或`ViewController`实例化和你自己，管理的对象引用，因此没有设计器的文件是必需的。



## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
