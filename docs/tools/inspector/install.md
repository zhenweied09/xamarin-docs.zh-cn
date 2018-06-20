---
title: 检查器安装和要求
description: 本文档介绍如何安装 Xamarin 检查器，并讨论受支持的操作系统、 Ide，以及应用程序平台。
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: topgenorth
ms.author: toopge
ms.date: 06/19/2018
ms.openlocfilehash: f7c5217a9c2d3881ca29094c3186e448975db6a3
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268964"
---
# <a name="inspector-installation-and-requirements"></a>检查器安装和要求

## <a name="download-and-installation"></a>下载和安装

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. 下载并安装[Visual Studio Enterprise](https://www.visualstudio.com/vs/)和选择**使用.NET 进行移动开发**工作负荷。
1. [登录](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio)若要启用你的企业订阅。
1. [检查](~/tools/inspector/inspect.md)你自己的应用 ！

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. 下载并安装[Visual Studio for Mac](https://www.visualstudio.com/vs/mac/)。
1. [登录](https://docs.microsoft.com/visualstudio/mac/activation)若要启用你的企业订阅。
1. [检查](~/tools/inspector/inspect.md)你自己的应用 ！

-----

## <a name="requirements"></a>要求

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 或更高版本
- **Windows** -Windows 7 或更高版本 (通过 Internet Explorer 11 或更高版本和.NET 4.6.1 或更高版本)

### <a name="supported-ides"></a>支持的 Ide

- Visual Studio for Mac
- 使用 visual Studio 2017**使用.NET 进行移动开发**工作负荷

实时应用程序检查是适用于企业的客户。

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>支持的应用程序平台

|应用程序平台|IDE 支持|说明|
|--- |--- |--- |
|Mac|仅支持 Visual Studio for Mac|
|iOS|在 Visual Studio 2017 和 Visual Studio 中支持的 Mac| |
|Android|在 Visual Studio 2017 和 Visual Studio 中支持的 Mac|必须为目标 Android > = 4.0.3，替换**fastdev**启用。<br />必须使用 Google、 Visual Studio 或 Xamarin Android 仿真程序。 Android 7 仿真程序可能不允许在此时间的检查。|
|WPF|仅支持 Visual Studio 2017|

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

Visual Studio

- **帮助 > 有关 Visual Studio > 复制信息**
- 让我们知道你的操作系统版本和您运行的 32 位或 64 位 Windows。

### <a name="log-files"></a>日志文件

始终附加 IDE 和检查器客户端日志文件。

检查器客户端

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4.x 还提供了从主菜单中查找程序 (macOS) 或资源管理器 (Windows) 直接选择日志文件的能力：

- **帮助 > 显示日志文件**

Visual Studio For Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Visual Studio 的内容**输出**窗格中，也可能提供有用的信息。

### <a name="project-settings"></a>项目设置

如果可以将附加 **.csproj**对于想要检查的项目，它将非常有用。 这是比询问有关单个设置。

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
