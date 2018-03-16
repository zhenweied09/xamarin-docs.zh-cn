---
title: "检查器安装和要求"
description: "如何下载、 安装和使用 Xamarin 检查器。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/29/2017
ms.openlocfilehash: d93481a865be1ee9272c7ce7969bdb6ebd6b5536
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="inspector-installation-and-requirements"></a>检查器安装和要求

## <a name="download-and-installation"></a>下载和安装


# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. 下载并安装[Xamarin 工作簿和检查器的 Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi)。
2. [检查你自己的应用 ！](~/tools/inspector/inspect.md)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. 下载并安装[Xamarin 工作簿 （&) 适用于 Mac 的检查器](https://dl.xamarin.com/interactive/XamarinInteractive.pkg)。
2. [检查你自己的应用 ！](~/tools/inspector/inspect.md)

-----

## <a name="requirements"></a>惠?

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 或更高版本
- **Windows** -Windows 7 或更高版本 (通过 Internet Explorer 11 或更高版本和.NET 4.6.1 或更高版本)

### <a name="supported-ides"></a>支持的 Ide

- Xamarin Studio 6.2 或更高版本
- 为 Mac Preview 4 或更高版本的 visual Studio
- 使用 Xamarin 的 visual Studio 2015 4.3.x 或更高版本
- 与 Xamarin 工作负荷的 visual Studio 2017

实时应用程序检查是适用于企业的客户。

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>支持的应用程序平台

|应用程序平台|IDE 支持|说明|
|--- |--- |--- |
|Mac （统一）|仅支持在 Mac 上|
|iOS （统一）|支持 XS 和 Visual Studio 中|检查在 Windows 中的 iOS 应用需要检查器还在 Mac 生成主机上安装相同的版本。|
|Android|支持 XS 和 Visual Studio 中|必须为目标 Android > = 4.0.3，替换**fastdev**启用。<br />必须使用 Google、 Visual Studio 或 Xamarin Android 仿真程序。 Android 7 仿真程序可能不允许在此时间的检查。|
|WPF|仅支持在 Windows 上的 Visual Studio 中|


<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>报告 Bug

应直接通过 Visual Studio 报告的 bug:

- **帮助 > 发送反馈 > 报告问题**

请包括所有的以下信息：

### <a name="platform-version-information"></a>平台版本信息

此信息是至关重要的。

Visual Studio For Mac

- **Visual Studio > 有关 Visual Studio > 显示详细信息 > 复制信息**
- 将粘贴到 bug 报表

Xamarin Studio

- **Xamarin Studio > 有关 Xamarin Studio > 显示详细信息 > 复制信息**
- 将粘贴到 bug 报表

Visual Studio

- **帮助 > 有关 Visual Studio > 复制信息**
- 让我们知道你的操作系统版本和您运行的 32 位或 64 位 Windows。

### <a name="log-files"></a>日志文件

始终附加 IDE 和检查器客户端日志文件。

检查器客户端

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4.x 还提供了从主菜单中查找程序 (macOS) 或资源管理器 (Windows) 直接选择日志文件的能力：

- **帮助 > 显示日志文件**

Visual Studio For Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Xamarin Studio

- `~/Library/Logs/XamarinStudio-6.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Visual Studio 的内容**输出**窗格中，也可能提供有用的信息。

### <a name="project-settings"></a>项目设置

如果可以将附加**.csproj**对于想要检查的项目，它将非常有用。 这是比询问有关单个设置。

此外请确认你在调试配置中。

### <a name="selected-devices"></a>所选的设备

为 Android 和 iOS，这至关重要，我们知道当你想要检查进行调试何种设备。 我们需要知道：

- IDE 中所示的设备的名称
- 你的设备的操作系统版本
- Android： 验证是否正在使用 x86 仿真程序
- Android： 哪些仿真器平台是否使用？ Google 仿真程序？ Visual Studio Android 仿真程序？ Xamarin Android 播放器？
- 不正确调试应用显示，在设备中正常工作？
- 设备是否具有网络连接 （通过 web 浏览器的检查）？

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>卸载

### <a name="windows"></a>Windows

具体取决于你如何获取工作簿和检查器，你可能需要执行两个卸载过程。 请检查这两种若要完全卸载软件。

#### <a name="visual-studio-installer"></a>Visual Studio 安装程序

如果你有 Visual Studio 2017，打开**Visual Studio Installer**，查看**各个组件**为**Xamarin 工作簿**。 如果选中，取消选中它，然后单击"修改"卸载。

#### <a name="system-uninstall"></a>系统卸载

如果您自己安装的工作簿和检查器使用下载的安装程序，它将需要通过卸载**应用和功能**Windows 10 上或通过系统设置页**添加/删除程序**较旧版本的 Windows 上的控制面板中。

> **启动 > 设置 > 系统 > 应用程序和功能**

![](install-images/windows-remove.png "Xamarin 工作簿和应用程序和功能中列出的检查器")

**你仍应遵循为 Visual Studio 安装程序，以确保工作簿的过程 （&) 检查器不会不获取重新安装你不知情的情况下。**

### <a name="macos"></a>macOS

从开始[1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/)，Xamarin 工作簿和检查器可以通过进行卸载从终端运行：

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

文件和目录，它将删除并在继续前要求确认，则将详细介绍卸载程序。

传递`-help`参数`uninstall`用于更高级方案的脚本。

对于旧版本，需手动删除以下各项：

1. 在 `"/Applications/Xamarin Workbooks.app"` 删除 Workbooks 应用
2. 在 `"Applications/Xamarin Inspector.app"` 删除 Inspector 应用
2. 删除加载项：`"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` 和 `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. 在 `/Library/Frameworks/Xamarin.Interactive.framework` 和 `/Library/Frameworks/Xamarin.Inspector.framework` 删除 Inspector 和支持文件

