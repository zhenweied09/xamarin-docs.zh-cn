---
title: 安装和在 Xamarin 中使用 watchOS
description: 本文档介绍如何安装和 Xamarin 中使用 watchOS。 还讨论了安装，watchOS 项目结构，如何使用 iOS 设计器、 Xcode 集成，并提供了故障排除提示。
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/05/2017
ms.openlocfilehash: ea0c7b6a68077cde83fa211e4e6f3432b3e39d5c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791233"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>安装和在 Xamarin 中使用 watchOS

watchOS 4 需要 macOS Sierra (10.12) 以 Xcode 9。

watchOS 1 最初需要 Xcode 7 的 OS X Yosemite (10.10)。

> [!WARNING]
> [后 2018 年 4 月 1，将不接受 watchOS 1 更新](https://developer.apple.com/news/?id=11162017a)。 将来的更新必须使用 watchOS 2 SDK 或更高版本;生成与 watchOS 建议 4 SDK。

## <a name="project-structure"></a>项目结构

Watch 应用由三个项目组成：

- **Xamarin.iOS iPhone 应用程序项目**-这是正常的 iPhone 项目，它可以是任何 Xamarin.iOS 模板。 将此主项目内捆绑监视应用程序和其扩展名。

- **监视扩展项目**-它包含的代码 （例如控制器类） 个 Watch 应用。

- **监视应用程序项目**-这包含 Watch 应用与 UI 的所有资源的用户界面情节提要文件。

[监视工具包目录示例](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)解决方案在 Xamarin.Studio 中如下所示：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](installation-images/catalog-solution.png "Visual Studio 中的解决方案")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](installation-images/catalog-solution-vs.png "Visual Studio 中的解决方案")

-----

下载并运行[WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)示例吧。
可以上找到的示例屏幕[控件](~/ios/watchos/user-interface/index.md)页。


## <a name="creating-a-new-project"></a>创建新项目

无法创建新"监视解决方案"，而是将监视应用程序添加到现有的 iOS 应用程序。 按照这些步骤创建 watch 应用：

1. 如果你没有现有项目，首先选择**文件 > 新解决方案**和创建的 iOS 应用 (例如，**单视图应用**):

    [![](installation-images/cycle8-2-sml.png "选择文件 > 新解决方案和创建的 iOS 应用")](installation-images/cycle8-2.png#lightbox)

2. 后创建的 iOS 应用 （或你打算使用你现有的 iOS 应用程序），右击该解决方案并选择**添加 > 添加新项目...**.在**新项目**窗口中选择**watchOS > 应用程序 > WatchKit 应用**:

    [![](installation-images/cycle8-6-sml.png "选择 watchOS > 应用程序 > WatchKit 应用")](installation-images/cycle8-6.png#lightbox)

3. 下一个屏幕可让你选择的 iOS 应用程序项目应包括 watch 应用：

    [![](installation-images/cycle8-7-sml.png "选择的 iOS 应用程序项目应包括 watch 应用")](installation-images/cycle8-7.png#lightbox)

4. 最后，选择要保存项目的位置 （和 （可选） 启用源代码管理）：

    [![](installation-images/cycle8-8-sml.png "选择要保存项目的位置")](installation-images/cycle8-8.png#lightbox)

5. 适用于 Mac 的 visual Studio 会自动配置[项目引用和**Info.plist**设置](~/ios/watchos/get-started/project-references.md)为你。

## <a name="creating-the-watch-user-interface"></a>创建监视用户界面

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>使用 Xamarin iOS 设计器

监视应用程序的双击**Interface.storyboard**编辑使用 iOS 设计器。 你可以将接口控制器和 UI 控件拖到从情节提要**工具箱**并将其配置使用**属性**填充：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](installation-images/iosdesigner-sml.png "情节提要设计器中")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](installation-images/iosdesigner-sml-vs.png "情节提要设计器中")](installation-images/iosdesigner-vs.png#lightbox)

-----

你应授予每个新的接口控制器**类**选择它，然后输入中的名称**属性**pad （自动，这将创建所需的 C# 代码隐藏文件）：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](installation-images/iosdesigner-classname.png "为每个新的接口控制器提供一个类")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](installation-images/iosdesigner-classname-vs.png "为每个新的接口控制器提供一个类")

-----

创建由 segue **Ctrl + 拖动**从到另一个接口控制器上的按钮、 表或接口的控制器。


### <a name="using-xcode-on-the-mac"></a>在 Mac 上使用 Xcode

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

你可以继续使用 Xcode 生成通过右键单击 Interface.storyboard 文件并选择你的用户界面**打开 > Xcode 接口生成器**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio 用户还可以使用 Xcode 若要通过切换要直接使用 Mac 生成主机生成自己的用户界面。
你的解决方案在 Visual Studio 中打开 for Mac 然后右键单击 Interface.storyboard 文件并选择**打开 > Xcode 接口生成器**:

-----

![](installation-images/openwith-xcode.png "在 Xcode 接口生成器中打开 Interface.storyboard")

如果使用 Xcode，然后应遵循与正常的监视应用的相同步骤[iOS 应用情节提要](~/ios/user-interface/storyboards/index.md)(如创建 outlet 和操作**Ctrl + 拖动**到 **.h**标头文件)。

在您保存情节提要在它会自动将添加的 Xcode 接口生成器中的 outlet 和你创建的操作为 C# **。 designer.cs**监视扩展项目中的文件。


### <a name="adding-additional-screens-in-xcode"></a>在 Xcode 中添加其他屏幕

当将 （超出中包含的内容模板默认情况下） 的其他屏幕添加到情节提要使用 Xcode 接口生成器**必须手动将 C# 代码文件添加**针对每个新的接口控制器。

请参阅[高级说明如何将新接口控制器添加到情节提要](~/ios/watchos/troubleshooting.md#add)。

*Xamarin iOS 设计器自动执行此操作，没有手动步骤是必需的。*


## <a name="building"></a>生成

如其他 iOS 项目生成包含 watch 应用的项目。 生成过程会导致包含监视扩展 (.appex)，后者又包含代码的监视应用程序 (.app) iPhone 应用程序 (.app)。


## <a name="launching"></a>启动

你可以启动监视应用程序在模拟器中使用 Visual Studio for Mac 或 Visual Studio （开始 Mac 生成主机上）。

有两种模式启动 WatchKit 应用：

 - 正常的应用程序模式 （默认值）、 和
- [通知](~/ios/watchos/platform/notifications.md)（要求采用 JSON 格式的测试通知负载）。

### <a name="xcode-8-support"></a>Xcode 8 支持

安装 Xcode 8 （或更高版本） 后，Apple Watch 模拟器是分开 iOS 模拟器 (与不同[Xcode 6](#xcode6)，其中它们显示为*外部显示器*)。
如果你选择监视应用程序项目，并且将其作为启动项目，将显示模拟器列表*iOS 模拟器*可供选择 （如下所示）。

[![](installation-images/xs-xcode8-watchos3-sml.png "选择模拟器类型")](installation-images/xs-xcode8-watchos3.png#lightbox)

当开始调试，*两个*模拟器应启动的 iOS 模拟器*和*Apple Watch 模拟器。 使用**命令 + Shift + H**导航到手表菜单和时钟表盘; 并使用**硬件**菜单设置**强制 Touch 压力**。 使用数字王冠模仿 trackpad 或鼠标上滚动。

#### <a name="troubleshooting"></a>疑难解答

以下的错误将出现在**应用程序输出**如果你尝试启动与不具有配对的监视模拟器：

```csharp
error MT0000: Unexpected error - Please file a bug report at http://bugzilla.xamarin.com
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

请参阅[Apple 的论坛](https://forums.developer.apple.com/thread/7783)有关配置模拟器中，如果默认值不起作用的说明。


<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 和 watchOS 1

必须进行*监视扩展项目***启动项目**之前运行或调试应用程序。 你不能"启动"监视应用程序本身，并且如果你选择的 iOS 应用然后它将开始照常在 iOS 模拟器中。

默认在正常启动 watch 应用**应用**模式 （不快速或通知模式） 从适用于 Mac 的 Visual Studio**运行**或**调试**命令。

使用 Xcode 6 时 iPhone 5、 iPhone 5 条、 iPhone 6 和 iPhone 6 Plus 可以激活或者的外部显示**Apple Watch-38 mm**或**Apple Watch-42 mm**监视应用程序将在其中显示。

**注意：** 请记住，监视屏幕不自动显示在 iOS 模拟器中使用 Xcode 6 时。
使用**硬件 > 外部显示器**菜单可显示监视屏幕。

<a name="custommodes" />

## <a name="launching-notification-mode"></a>启动通知模式

请参阅[通知页](~/ios/watchos/platform/notifications.md)信息如何处理代码中的通知。


适用于 Mac 的 visual Studio 可以将监视应用程序开头通知_启动模式_通知：



右键单击监视应用程序项目并选择**运行与 > 自定义配置...**:


[![](installation-images/runwith-customparams-sml.png "运行自定义配置")](installation-images/runwith-customparams.png#lightbox)


这将打开**自定义参数**窗口可以在其中选择**通知**（和提供 JSON 负载），然后按**运行**在模拟器中启动监视应用程序：


[![](installation-images/runwith-execargs-sml.png "设置通知和负载")](installation-images/runwith-execargs.png#lightbox)



## <a name="debugging"></a>调试

在适用于 Mac 的 Visual Studio 和 Visual Studio 支持调试。
请记得在通知模式下调试时提供通知 JSON 文件。 此屏幕截图显示调试断点被命中在监视应用程序中：

![](installation-images/debug-sml.png "此屏幕快照显示调试断点被命中中监视应用程序")

启动说明后你最终将得到运行的应用程序监视**iOS 模拟器 （监视）**。
你可以选择为通知模式**调试 > 打开系统日志**(**CMD + /**) 并使用`Console.WriteLine`在代码中。

### <a name="debugging-lifecycle-event-handlers"></a>调试生命周期事件处理程序

<!--
To test the functionality in your  and 
    methods, use the **Hardware > Lock** command in the iOS Simulator.
    Locking will trigger the `DidDeactivate` method and the watch simulator
    will indicate that it has been locked. Swipe the iOS Simulator to unlock,
    which triggers the `WillActivate` method of the watch app.
-->

WatchOS 模板文件 (如`InterfaceController`， `ExtensionDelegate`， `NotificationController`，和`ComplicationController`) 附带已经实现其所需的生命周期方法。 添加`Console.WriteLine`调用和读取**应用程序输出**以更好地了解事件的生命周期。



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [第一个 Watch 应用视频](http://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 的 WatchKit 提示](https://developer.apple.com/watchkit/tips/)
