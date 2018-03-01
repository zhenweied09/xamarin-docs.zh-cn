---
title: "Hello 监视"
description: "开始使用 Xamarin 和 watchOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/14/2016
ms.openlocfilehash: 88f9a86173756738d44f099b13177489226fa0e1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="hello-watch"></a>Hello 监视

创建一个解决方案中的步骤后[设置和安装](~/ios/watchos/get-started/installation.md)，你将具有 3 个项目：

- IOS 父应用程序，用于安装程序或其他设备上的管理任务。 （与其他类型的 iOS 扩展，这通常称为"容器"应用程序。）与监视应用程序，它将是一种用户可以开始运行不含监视应用**曾经**运行父应用程序;
- 监视扩展，它包含监视应用程序; 的程序代码和
- 监视应用程序，它保存在手表呈现的情节提要和图像资源。

检查你[引用正确无误](~/ios/watchos/get-started/project-references.md)： 父应用具有为扩展，引用和扩展具有到 Watch 应用的引用。

确认你的捆绑标识符遵循\*.watchkitextension \*.watchkitapp 约定并扩展的 Info.plist 文件，将该属性具有**WKApp 捆绑 ID**值设置为的捆绑标识符监视应用程序。

你应该能够运行你监视的应用现在，但在监视应用程序的情节提要文件为空，因为你将无法告知。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](hello-watch-images/projectstructure.png "解决方案资源管理器")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-watch-images/vs-projectstructure.png "解决方案资源管理器")

-----

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
双击 Interface.storyboard 监视应用程序，以启动 Xamarin iOS 设计器中 (如果要在 Mac 上还可以右键单击和**打开 > Xcode 接口生成器**)


1.  确保**工具箱**和**属性**填充是可见的
1.  单击以选择接口控制器中，
1.  设置的标识符和标题接口控制器到**interfaceController**和**Hi 监视**，
1.  验证**类**设置为**InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "将标识符和接口控制器标题设置为 interfaceController 和 Hi 监视")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要使用 Xamarin iOS Visual Studio 中的设计器编辑操作所需的监视应用程序中 Interface.storyboard 上，双击：

1.  打开属性窗格中;
1.  更改到类**InterfaceController**;
1.  单击接口控制器中;和
1.  设置的标识符和标题接口控制器到**interfaceController**和**Hi 监视**。

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "将标识符和接口控制器标题设置为 interfaceController 和 Hi 监视")

-----


创建你的 UI:

1. 从**工具箱**板中，
1. 拖放式**按钮**和**标签**到场景，和
1. 设置文本和控件所示的属性：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](hello-watch-images/draganddrop.png "设置文本和控件所示的属性")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-watch-images/vs-draganddrop.png "设置文本和控件所示的属性")

-----

1. 设置**名称**中**属性**为每个控件的填充。 在此示例中我们使用了`myButton`和`myLabel`。

1. 选择在情节提要按钮，然后转到**属性**填充的**事件**列表，然后

1. 创建一个新**操作**通过键入`OnButtonPress`按**Enter**。
  操作将显示在列表中，并且在 C# 中将自动创建分部方法。

![](hello-watch-images/buttonaction.png "OnButtonPress 操作添加到按钮")

保存情节提要之后, **InterfaceController.designer.cs**获取更新控件的名称和操作... 如果已更新后，您可以打开此文件，你可以看到如何`RegisterAttribute`对应于控制器和 UI 控件如何对应于 C# 实例变量标记为`OutletAttribute`和如何将操作映射到分部方法标记有`ActionAttribute`:

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac from the outlets and
// actions declared in your storyboard file.
// Manual changes to this file will not be maintained.
//
[Register ("InterfaceController")]
partial class InterfaceController
{
    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceButton myButton { get; set; }

    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceLabel myLabel { get; set; }

    [Action ("OnButtonPress:")]
    [GeneratedCode ("iOS Designer", "1.0")]
    partial void OnButtonPress (WatchKit.WKInterfaceButton sender);

    void ReleaseDesignerOutlets ()
    {
        if (myButton != null) {
            myButton.Dispose ();
            myButton = null;
        }
        if (myLabel != null) {
            myLabel.Dispose ();
            myLabel = null;
        }
    }
}
```

现在打开**InterfaceController.cs** (*不*InterfaceController.designer.cs) 并添加以下代码：

```csharp
int clickCount = 0;

partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}

```

此代码应相当透明： 实例变量`clickCount`是每次函数时递增`OnButtonPress`调用。 文本`myLabel`更改以反映此计数;`myLabel`，当然，是在 XCode 中创建的 Outlet 之一的名称。 `partial`函数是与你指定的操作名称关联的函数的实现。

如果它尚未启动项目，

1. 右键单击你的监视扩展项目并选择**设为启动项目**，

1. 将部署目标设置为 （如 iPhone 6 iOS 8.2) 的监视工具包兼容模拟器映像

1. 按**调试**按钮以触发生成和模拟器启动。

    [ ![](hello-watch-images/readytodebug-sml.png "Visual Studio 界面元素")](hello-watch-images/readytodebug.png)

当模拟器启动时，按按钮以递增标签。
祝贺你，您就可以得到 Watch 应用 ！

![](hello-watch-images/running.png "在模拟器中运行的应用程序")


## <a name="related-links"></a>相关链接

- [入门 （示例）](https://developer.xamarin.com/samples/monotouch/WatchKit/GettingStarted/)
- [设置和安装](~/ios/watchos/get-started/installation.md)
- [第一个 Watch 应用视频](http://blog.xamarin.com/your-first-watch-kit-app/)
