---
title: Xamarin.iOS for Visual Studio 简介
description: 本文介绍如何使用 Visual Studio 生成并测试 Xamarin iOS 应用程序。 它将介绍如何使用 Visual Studio 新建 iOS 项目、生成 iOS 应用程序，然后使用通过网络连接到主机 Apple 编译器和模拟器的 Mac 以及 Xamarin 生成工具链进行编译、测试和调试。
ms.prod: xamarin
ms.assetid: bf3c779f-959f-428d-babb-428f363f7e4e
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a8264d3ebd5f294b1b77fbbafd660825d5ce5180
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-xamarinios-for-visual-studio"></a>Xamarin.iOS for Visual Studio 简介

本文介绍如何使用 Visual Studio 生成并测试 Xamarin iOS 应用程序。其中将说明如何使用 Visual Studio 新建 iOS 项目、生成 iOS 应用程序，然后使用通过网络连接到主机 Apple 编译器和模拟器的 Mac 以及 Xamarin 生成工具链进行编译、测试和调试。

借助 Xamarin for Windows，iOS 应用程序可在 Visual Studio 中进行编写和测试，并且由联网的 Mac 提供生成和部署服务。

本文介绍在每台计算机上安装和配置 Xamarin.iOS 工具以使用 Visual Studio 生成 iOS 应用程序的步骤。

在 Visual Studio 内部开发 iOS 具有以下优点：

-  为 iOS、Android 和 Windows 应用程序创建跨平台解决方案。
-  对所有跨平台项目（包括 iOS 源代码）使用常用 Visual Studio 工具（如 Resharper 和 Team Foundation Server）。
-  使用熟悉的 IDE，同时利用所有 Apple API 的 Xamarin.iOS 绑定。


<a name="Requirements_and_Installation" />

## <a name="requirements-and-installation"></a>要求和安装

在 Visual Studio 中开发 iOS 时必须遵守一些要求。 正如概述中简要提到，编译 IPA 文件需要 Mac，且应用程序不能部署到没有 Apple 证书和代码签名工具的设备上。

可从众多配置选项中选择最适合开发需求的选项。 以下列出了这些选项：

-  使用 Mac 作为主要的开发计算机，并运行安装了 Visual Studio 的 Windows 虚拟机。 建议使用 VM 软件，如 [Parallels](http://www.parallels.com/products/desktop/) 或 [VMWare](http://www.vmware.com/products/fusion/)。
-  将 Mac 当做生成主机使用。 在这种情况下，只需连接到与 Windows 计算机所处的同一网络上，该计算机已安装[必备](~/cross-platform/get-started/installation/windows.md#installation)工具。


对于每种情况，都应遵循以下步骤：

- [在 Mac 主机上安装 Xamarin.iOS 工具](https://docs.microsoft.com/visualstudio/mac/installation)
- [配置 Mac](~/ios/get-started/installation/windows/index.md#configuring)
- [在 Windows 上安装 Xamarin 工具](~/cross-platform/get-started/installation/windows.md)

若要在 Visual Studio 中使用 Xamarin 进行开发，必须至少使用 Visual Studio 2015 Professional 或更高版本。 使用 Visual Studio 的 Express 版本时，Xamarin 将**不起作用**，因为这些版本不支持外接程序。

## <a name="connecting-to-the-mac"></a>连接到 Mac

可通过 Visual Studio 工具栏上的图标连接 Mac 生成主机（前提是已打开 iOS 应用程序）：

[![](introduction-to-xamarin-ios-for-visual-studio-images/xma1a.png "“连接到 Mac”图标")](introduction-to-xamarin-ios-for-visual-studio-images/xma1a.png#lightbox)

也可通过在 Visual Studio 中浏览到“工具”>“选项”并选择“Xamarin”>“iOS 设置”：

 [![](introduction-to-xamarin-ios-for-visual-studio-images/xma-ios-options.png "iOS 选项")](introduction-to-xamarin-ios-for-visual-studio-images/xma-ios-options.png#lightbox)

可通过单击“查找 Xamarin Mac 代理”按钮更改 Mac 生成主机。 以下屏幕显示更新 Mac 生成主机：

  [![](introduction-to-xamarin-ios-for-visual-studio-images/xma-dialog.png "“Xamarin Mac Agent”对话框")](introduction-to-xamarin-ios-for-visual-studio-images/xma-dialog.png#lightbox)


## <a name="visual-studio-toolbar-overview"></a>Visual Studio 工具栏概述

Xamarin iOS for Visual Studio 将项添加到标准工具栏和新的 iOS 工具栏。
下面介绍了这些工具栏的功能。



### <a name="standard-toolbar"></a>标准工具栏

与 Xamarin iOS 开发相关的控件用红线圈出：

 [![](introduction-to-xamarin-ios-for-visual-studio-images/03.png "与 Xamarin iOS 开发相关的控件已用红线圈出")](introduction-to-xamarin-ios-for-visual-studio-images/03.png#lightbox "The controls relevant to Xamarin iOS development are circled in red")

-  **开始** - 开始在选定平台上调试或运行应用。 Mac 必须已连接（可查看 iOS 工具栏中的状态指示器）。
-  **解决方案配置** - 允许选择要使用的配置（例如，调试和发布）。
-  **解决方案平台** - 可以选择“iPhone”或“iPhoneSimulator”进行部署。


### <a name="ios-toolbar"></a>iOS 工具栏

iOS 工具栏在所有版本的 Visual Studio 中都类似。 外观如下所示：

[![](introduction-to-xamarin-ios-for-visual-studio-images/iostoolbar.png "iOS 工具栏")](introduction-to-xamarin-ios-for-visual-studio-images/iostoolbar.png#lightbox)

下面解释每个项：

-  **Mac 代理/连接管理器** - 显示 Xamarin Mac 代理对话框。 正在连接时，此图标显示为橙色；已连接时，显示为绿色。
-  **显示 iOS 模拟器** - 在 Mac 上将 iOS 模拟器窗口放在最前面。
-  **显示生成服务器上的 IPA 文件** - 打开 Mac 上的 Finder，找到应用程序的 IPA 输出文件位置。



## <a name="ios-output-options"></a>iOS 输出选项


### <a name="output-window"></a>输出窗口

“输出”窗格具有多个选项，可以查看这些选项，发现有关生成、部署、连接消息和错误。

以下屏幕截图显示可用的输出窗口，这些窗口可能因项目类型而有所不同：

[![](introduction-to-xamarin-ios-for-visual-studio-images/output-sml.png "可用输出窗口")](introduction-to-xamarin-ios-for-visual-studio-images/output-large.png#lightbox)

- **Xamarin** - 此项包含仅与 Xamarin 相关的信息，如与 Mac 的连接情况和激活状态。

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output3-sml.png "仅与 Xamarin 相关的信息，如与 Mac 的连接情况和激活状态")](introduction-to-xamarin-ios-for-visual-studio-images/output3-large.png#lightbox)

- **Xamarin 诊断** - 此窗格显示 Xamarin 项目的更多详细信息，如与 Android 的交互。

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output4-sml.png "有关 Xamarin 项目的详细信息")](introduction-to-xamarin-ios-for-visual-studio-images/output3-large.png#lightbox)

仍可以在“输出”视图中选择其他默认 Visual Studio 输出窗格（如“调试”和“生成”），以便调试输出和 MSBuild 输出：

-  **调试**

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output2-sml.png "调试输出")](introduction-to-xamarin-ios-for-visual-studio-images/output2-large.png#lightbox)

- **生成**与**生成顺序**

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output1-sml.png "MSBuild 输出")](introduction-to-xamarin-ios-for-visual-studio-images/output1-large.png#lightbox)


## <a name="ios-project-properties"></a>iOS 项目属性

可通过右键单击项目名称并选择上下文菜单中的“属性”找到 Visual Studio 的项目属性。 这样就可以配置 iOS 应用程序了，如以下屏幕截图所示：


 ![](introduction-to-xamarin-ios-for-visual-studio-images/iosproperties.png "配置 iOS 应用程序")

-  iOS 应用程序包签名 - 连接 Mac 以填充代码签名标识和预配配置文件：


 ![](introduction-to-xamarin-ios-for-visual-studio-images/bundlesigning.png "填充代码签名标识和预配配置文件")

-  iOS IPA 选项 - IPA 文件将保存到 Mac 文件系统中：


 ![](introduction-to-xamarin-ios-for-visual-studio-images/ipaoptions.png "iOS IPA 选项")

-  iOS 运行选项 - 配置其他参数：

 ![](introduction-to-xamarin-ios-for-visual-studio-images/iosrunoptions.png "iOS 运行选项")



## <a name="creating-a-new-project-for-ios-applications"></a>为 iOS 应用程序创建新的项目

在 Visual Studio 中创建新的 iOS 项目同创建其他项目类型一样。 选择“文件”>“新建项目”会打开如下所示的对话框，描述创建新的 iOS 项目的一些可用模板：


![](introduction-to-xamarin-ios-for-visual-studio-images/newproject.png "新建项目")

可以在 Visual Studio 中使用 iOS 设计器编辑情节提要和 .xib 文件。 若要创建情节提要，请选择某个情节提要模板。 这会在“解决方案资源管理器”中生成 **Main.storyboard** 文件，如以下屏幕截图所示：

![](introduction-to-xamarin-ios-for-visual-studio-images/solution-explorer-new.png "解决方案资源管理器中的 Main.storyboard 文件")

若要开始创建或编辑情节提要，请双击 `Main.storyboard` 将其在 iOS 设计器中打开：

![](introduction-to-xamarin-ios-for-visual-studio-images/iosdesigner.png "iOS 设计器中的 Main.storyboard")

若要向视图添加对象，请使用“工具箱”窗格，将项拖放到设计图面上。 如果尚未添加工具箱，可通过选择“视图”>“工具箱”添加。 可使用“属性”窗格修改对象属性、调整布局，并创建事件，如下所示：

![](introduction-to-xamarin-ios-for-visual-studio-images/properties.png "“属性”窗格")

 有关使用 iOS 设计器的详细信息，请参阅[设计器](~/ios/user-interface/designer/index.md)指南。


## <a name="running--debugging-ios-applications"></a>运行和调试 iOS 应用程序

### <a name="device-logging"></a>设备日志记录

在 Visual Studio 2015 及更高版本中，统一了 Android 和 iOS 日志面板

Visual Studio 新的“设备日志”工具窗口允许显示 Android 和 iOS 设备的日志。 执行以下任一命令均可显示此窗口：

- “视图”>“其他窗口”>“设备日志”
- “工具”>“iOS”>“设备日志”
- “iOS 工具栏”>“设备日志”

在工具窗口显示后，用户便可以从“设备”下拉列表中选择物理设备。 选定设备后，日志将自动添加到表中。 在设备之间切换可停止和启动设备日志记录。

为了在组合框中显示设备，必须加载 iOS 项目。 此外对于 iOS，Visual Studio 还必须[连接到 Mac 服务器](~/ios/get-started/installation/windows/connecting-to-mac/index.md)以发现连接到 Mac 的 iOS 设备。

此工具窗口包括：一个日志条目表、一个用于选择设备的下拉列表、一种清除日志条目的方法、一个搜索框，以及播放/停止/暂停按钮。


### <a name="set-debugging-stops"></a>设置调试停止

可以在应用程序中的任意位置设置断点，将信号发送到调试器以暂停程序执行。 若要在 Visual Studio 中设置断点，请在想要中断的代码行号旁边，单击编辑器的空白区：

![](introduction-to-xamarin-ios-for-visual-studio-images/image18.png "设置调试点")

开始调试，并使用模拟器或设备将应用程序导航到断点。 命中断点时，将突出显示此行并启用 Visual Studio 的正常调试行为：你可以进入、结束或跳出代码，检查局部变量，或使用即时窗口。

此屏幕截图显示使用 OS X 上的 Parallels 在 Visual Studio 旁运行的 iOS 模拟器：


![](introduction-to-xamarin-ios-for-visual-studio-images/image19.png "此屏幕截图显示使用 OS X 上的 Parallels 在 Visual Studio 旁运行的 iOS 模拟器")

### <a name="examine-local-variables"></a>检查局部变量

![](introduction-to-xamarin-ios-for-visual-studio-images/image20.png "通过调试检查局部变量")

## <a name="summary"></a>总结

本文介绍如何使用 Xamarin.iOS for Visual Studio。 列出了可用于从 Visual Studio 中创建、生成和测试 iOS 应用的各种功能，并介绍了如何生成和调试简单的 iOS 应用程序。

## <a name="related-links"></a>相关链接

- [Xamarin.iOS 安装](~/ios/get-started/installation/windows/index.md)
- [设备预配](~/ios/get-started/installation/device-provisioning/index.md)
- [在代码中创建 iOS UI](~/ios/app-fundamentals/ios-code-only.md)
- [使用 XMA 将 Mac 连接到 Visual Studio 环境（视频）](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
