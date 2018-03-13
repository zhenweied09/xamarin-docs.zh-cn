---
title: "在 Windows 上安装 Xamarin.iOS"
description: "本文章介绍如何安装 Xamarin.iOS for Visual Studio。 其中包含 Visual Studio 适用的 Xamarin 扩展的安装过程，并探讨了如何连接到 Mac 上安装的 Apple SDK。"
ms.topic: article
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/29/2017
ms.openlocfilehash: cfbe2df23317ee3ad11c9970ab892ddcc251b9d6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="installing-xamarinios-on-windows"></a>在 Windows 上安装 Xamarin.iOS

_本文介绍如何安装 Xamarin.iOS for Visual Studio。其中包含 Visual Studio 适用的 Xamarin 扩展的安装过程，并探讨了如何连接到 Mac 上安装的 Apple SDK。_

## <a name="overview"></a>概述

借助 Xamarin.iOS for Visual Studio，iOS 应用程序可在 Windows 计算机上进行编写和测试，同时允许一台 Mac 联网提供生成和部署服务。

通过在 Visual Studio 内部开发 iOS，可实现以下功能：

- 为 iOS、Android 和 Windows 应用程序创建跨平台解决方案。
- 对 iOS 源代码等所有跨平台项目使用 Visual Studio 工具（如 Resharper 和 Team Foundation Server）。
- 使用熟悉的 IDE，同时利用所有 Apple API 的 Xamarin.iOS 绑定

如果 Visual Studio 在 Mac 的 Windows 虚拟机内部运行（利用 Parallels 或 VMWare）或者其位于在 Mac 等效网络上课件的单独计算机上，则 Xamarin.iOS for Visual Studio 支持配置。 无论哪种配置最适合你，Visual Studio 都会使用 SSH 迅速安全地连接到 Mac。

本文介绍了如何在 Mac 和 Windows 计算机上安装和配置 Xamarin.iOS 工具，并介绍了如何连接到 Mac 主机，以便开发人员可使用 Visual Studio 构建、调试和部署 Xamarin.iOS 应用程序。

下图简要概述了 Xamarin.iOS 开发工作流：

[![Xamarin.iOS 开发工作流](images/xma2.png)](images/xma2.png#lightbox)

> [!IMPORTANT]
>  实际上，为了生成项目，Visual Studio 会启动单独的 MSBuild 进程。 此过程将创建与 Mac 的新连接，这表示当 Visual Studio 生成时，实际上将存在两个从 Windows 到 Mac 的 SSH 连接。 通过[命令行](~/ios/get-started/installation/windows/connecting-to-mac/index.md)生成仅会创建一个 MSBuild 进程。 为使该图简洁，仅用一个箭头表示所有连接。

## <a name="requirements"></a>惠?

Xamarin.iOS for Visual Studio 可实现强大功能：让开发人员能在 Windows 计算机上使用 Visual Studio IDE 创建、生成和调试 iOS 应用程序。 它不能单独实现此功能 - 必须使用 Apple 编译器创建 iOS 应用程序，且必须通过 Apple 证书和代码签名工具进行部署。 这意味着，Xamarin.iOS for Visual Studio 安装需要连接到一台联网的 Mac OS X 计算机才能执行这些任务。 配置完成后，Xamarin 的工具将使过程尽可能流畅。


<a name="system-requirements"/>

### <a name="system-requirements"></a>系统要求

系统要求包括：

### <a name="windows"></a>Windows

1. Windows 7 或更高版本。

2. Visual Studio 2015 Professional 或更高版本

    a. 若拥有企业版许可证，则需安装 Visual Studio Enterprise。

3. Xamarin for Visual Studio。

由于缺少插件支持，Xamarin 工具不能与 Visual Studio Express 版配合使用。 Visual Studio 社区支持 Xamarin。

### <a name="mac"></a>Mac

1. 运行 macOS Sierra (10.12) 或更高版本的 Mac（但建议使用最新稳定版）。

2. Xamarin iOS SDK。 这将在下载 Visual Studio for Mac 时安装

3. Apple 的 Xcode IDE 和 iOS SDK（建议使用 Mac App Store 中的最新稳定版）。

**Windows 计算机必须能够通过网络连接到 Mac。**

### <a name="apple-developer-account"></a>Apple 开发人员帐户

若要将应用程序部署到设备或提交到 App Store，必须具有 Apple 开发人员帐户。 必须先在联网的 Mac 上创建和安装相关的开发人员证书和配置文件，然后 Xamarin.iOS for Visual Studio 才能正常工作。 若要了解获取开发证书和预配设备的步骤，请参阅[设备预配](~/ios/get-started/installation/device-provisioning/index.md)文章。

## <a name="features"></a>功能 

通过 Xamarin.iOS for Visual Studio，可在 Windows 中创建、编辑、生成和部署 Xamarin.iOS 项目。 包括以下功能：

- 新建 iOS 项目。

- 编辑 iOS 项目和跨平台解决方案（包括 Xamarin.Android 和 UWP 项目）。

- 编译 iOS 项目和跨平台解决方案（包括 Xamarin.Android 和 UWP 项目）。

- 利用 iOS 设计器进行情节提要和 .xib 支持。

- 部署和调试 iOS 应用程序，其中应用本身在模拟器中运行或在连接到 Mac 的设备上运行。

- Windows 上的 iOS 模拟器 - 若要详细了解如何在 Windows 上使用 iOS 模拟器，请参阅[这篇指南](~/tools/ios-simulator.md)。

<a name="configuring" />

## <a name="configuring-your-mac"></a>配置 Mac

<a name="installation"/>

### <a name="installation"></a>安装

若要在 Mac 主机上安装 Xamarin.iOS 工具，必须[安装 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation)。

安装软件后，请按照后续各部分中的步骤在 macOS 上配置 Xamarin.iOS，以实现其与 Xamarin for Visual Studio 的连接。

> [!IMPORTANT]
>  Windows 计算机必须使用与其连接的 Mac 相同的 Xamarin.iOS 版本。 为了确保满足此要求，必须符合以下条件：
>
> - **Visual Studio 2015 及更低版本**：确保位于与 Visual Studio for Mac 相同的[更新通道](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/)上。
>
> - **Visual Studio 2017 发布版**：确保位于 Visual Studio for Mac 的稳定版通道上。
>
> - **Visual Studio 2017 预览版**：确保位于 Visual Studio for Mac 的 Alpha 通道上。

<a name="configuration" />


### <a name="configuration"></a>配置

若要使 Visual Studio 适用的 Xamarin 扩展与 Mac 之间能够通信，需要在 Mac 上允许“远程登录”。 请按照以下步骤完成此设置：

1. 打开 Spotlight (**Cmd-Space**) 并搜索“远程登录”，然后选择“共享”结果。 系统将在“共享”面板打开“系统首选项”。

![用于远程登录的 Spotlight 搜索](images/spotlight.png)

2. 在左侧的“服务”列表中标记“远程登录”选项，允许 Xamarin for Visual Studio 连接到 Mac。

![选中“服务”列表中的“远程登录”选项](images/sharing.png)

3. 确保“远程登录”设为允许访问“所有用户”，或者 Mac 用户名或组包含在右侧的允许用户列表中。

如果 Mac 位于相同网络上，则 Visual Studio 现也可发现 Mac。

> [!NOTE]
> 如果 macOS 防火墙默认设置为阻止已签名的应用程序，则可能需要允许 `mono-sgen` 接收传入的连接。 若出现此情况，系统将显示警报对话框提示用户。

<a name="developersetup"/>

### <a name="ios-developer-setup"></a>iOS 开发人员安装程序

对于 iOS 开发，务必使用相关的签名标识配置 Mac 计算机。 由此，用户可正确签名应用，以便通过 App Store 或 Ad Hoc 进行分发。 参照下面的链接，了解设置 Mac 以利用 Xamarin 实现 iOS 开发的相关说明：

- [设备预配](~/ios/get-started/installation/device-provisioning/index.md?ide=vs)

Mac 配置后，即可设置 Windows 计算机。

<a name="windowsinstallation"/>

## <a name="windows-installation"></a>Windows 安装

Xamarin 可安装为 Visual Studio 2017 或 2015 的一部分。 若要安装 Visual Studio tools for Xamarin，请参阅 [Windows 安装](~/cross-platform/get-started/installation/windows.md)指南。

## <a name="installation-complete"></a>安装完成

安装过程完成后，仍需再执行几个步骤才可完全恢复正常：

- [将 Visual Studio 连接到 Mac](#connectingtomac) - Visual Studio 必须先连接到 Mac 生成主机，然后才能生成 Xamarin.iOS 项目。
- [配置 Visual Studio 工具栏](#toolbar) - 这样一来，可以在 Visual Studio 中轻松访问 Xamarin.iOS 功能。

<a name="connectingtomac" /> 

### <a name="connecting-to-the-mac"></a>连接到 Mac

Xamarin.iOS for Visual Studio 到 Mac 生成主机的连接是通过计算机间的 SSH 连接实现的。 有关连接的详细信息，请参阅[连接到 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) 指南。

若要连接 Mac，请按以下步骤操作：

- 浏览到“工具”>“选项”，然后在“Xamarin”下选择“iOS 设置”：

  [![“iOS 设置”屏幕](images/image2.png)](images/image2.png#lightbox)

- 如果 Mac 已正确[配置](#configuration)为允许“远程登录”，则应能够在列表中选择你的 Mac：

  [![“远程主机”对话框](images/xma3.png)](images/xma3.png#lightbox)

- 将提示输入 Mac 主机的管理凭据：

  [![登录对话框](images/xma4.png)](images/xma4.png#lightbox)

- 连接后，计算机名称旁会显示“连接成功”图标：

  [![“远程主机”对话框在计算机名称旁边显示“连接成功”图标](images/image6.png)](images/image6.png#lightbox)

每次启动 Visual Studio 都需重新连接。

<a name="toolbar" />

## <a name="visual-studio-toolbar-configuration"></a>Visual Studio 工具栏配置

打开 iOS 项目后，iOS 工具栏默认可见且无需配置。

如果未显示 iOS 工具栏，则可以使用下面的步骤。

若要配置工具栏，请先依次打开“视图”>“工具栏”菜单，再确保已选中“iOS”项。 选择此屏幕截图中所示的菜单项 - 应勾选该项以指示工具栏可见：

[![依次选择“工具栏”>“iOS”](images/image31.png)](images/image31.png#lightbox)

### <a name="visual-studio-2015"></a>Visual Studio 2015

在早于 Visual Studio 2017 的版本中，可能需要将“解决方案平台”按钮添加到标准工具栏中。 这样，用户即可在调试时选择 iOS 设备或 iOS 模拟器。 请按照以下说明进行设置

单击“标准”菜单栏右侧的菜单按钮：

- 选择“添加或删除按钮”
- 选择“解决方案平台”

[![选择“解决方案平台”](images/image35.png)](images/image35.png#lightbox)

“标准”和“iOS”工具栏现应如以下屏幕截图所示：

[![“标准”和“iOS”工具栏现在应如此屏幕截图所示](images/image36.png)](images/image36.png#lightbox)

工具栏配置完成后，即可开始使用 Xamarin iOS for Visual Studio。

## <a name="summary"></a>摘要

本文提供了安装、配置和使用 Xamarin iOS for Visual Studio 的分步指南。

介绍了如何在 Windows 和 Mac OS X 上安装和配置系统必备工具。


## <a name="related-links"></a>相关链接

- [安装](~/cross-platform/get-started/installation/windows.md)
- [设备预配](~/ios/get-started/installation/device-provisioning/index.md)
- [Xamarin.iOS for Visual Studio 简介](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)
- [使用 XMA 将 Mac 连接到 Visual Studio 环境（视频）](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
