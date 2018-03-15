---
title: "工作簿安装和要求"
description: "如何下载、 安装和使用 Xamarin 的工作簿。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: abd6ac7d4bce5a180db087488b32c878ed0a416b
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2018
---
# <a name="workbooks-installation-and-requirements"></a>工作簿安装和要求

<a name="install" />

## <a name="download-and-install"></a>下载并安装

# <a name="windowstabvswin"></a>[Windows](#tab/vswin)

1. 检查[要求](#requirements)下面。
2. 下载并安装[Windows 的 Xamarin 工作簿](https://dl.xamarin.com/interactive/XamarinInteractive.msi)。
3. 启动[体验了一番](~/tools/workbooks/workbook.md)使用工作簿或试用[示例](https://developer.xamarin.com/workbooks)

# <a name="macostabvsmac"></a>[macOS](#tab/vsmac)

1. 检查[要求](#Requirements)下面。
2. 下载并安装[适用于 Mac 的 Xamarin 工作簿](https://dl.xamarin.com/interactive/XamarinInteractive.pkg)。
3. 启动[体验了一番](~/tools/workbooks/workbook.md)使用工作簿或试用[示例](https://developer.xamarin.com/workbooks)

-----

## <a name="requirements"></a>惠?

#### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 或更高版本
- **Windows** -Windows 7 或更高版本 (通过 Internet Explorer 11 或更高版本和.NET 4.6.1 或更高版本)

#### <a name="supported-app-platforms"></a>支持的应用程序平台

|应用程序平台|支持 32 位操作系统|说明|
|--- |--- |--- |
|Mac （统一）|仅支持在 Mac 上|
|iOS （统一）|在 Mac 和 Windows 上受支持|必须 mac 上安装 Xamarin.iOS 11.0 和 Xcode 9.0 或更高版本 在 Windows 上运行 iOS 工作簿需要运行的更高的版本，所有的 Mac 生成主机和[进行远程处理的 iOS 模拟器](~/tools/ios-simulator.md)在 Windows 上安装。|
|Android|在 Mac 和 Windows 上受支持|必须将 Google、 Visual Studio 或 Xamarin Android 仿真程序中，使用虚拟设备 > = 5.0|
|WPF|在 Windows 上才支持|
|控制台 (.NET Framework)|在 Mac 和 Windows 上受支持|
|控制台 （.NET 核心）|在 Mac 和 Windows 上受支持|


## <a name="reporting-bugs"></a>报告 Bug

请[GitHub 上报告问题][bugs]，并包括所有的以下信息：

### <a name="log-files"></a>日志文件

始终附加工作簿客户端日志文件：

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4.x 还提供了从主菜单中查找程序 (macOS) 或资源管理器 (Windows) 直接选择日志文件的能力：

- **帮助 > 显示日志文件**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>为工作簿 1.3 及更早版本的日志路径：

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>平台版本信息

它是很有帮助了解有关你的操作系统的详细信息，并且安装 Xamarin 产品。

从工作簿中的主菜单：

* **帮助 > 复制版本信息**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>为工作簿 1.3 及更早版本的说明：

Visual Studio For Mac

- **Visual Studio > 有关 Visual Studio > 显示详细信息 > 复制信息**
- 将粘贴到 bug 报表

Visual Studio

- **帮助 > 有关 Visual Studio > 复制信息**
- 让我们知道你的操作系统版本和您运行的 32 位或 64 位 Windows。

### <a name="samples"></a>示例

如果可以将附加或链接到**.workbooks**文件时遇到问题，可能帮助更快地解决 bug。

### <a name="devices"></a>设备

如果你有连接 iOS 或 Android 工作簿时遇到问题，并已签入[我们故障排除的页面](~/tools/workbooks/troubleshooting/index.md)，我们将需要知道：

- 你尝试连接到的设备的名称
- 你的设备的操作系统版本
- Android： 验证是否正在使用 x86 仿真程序
- Android： 哪些仿真器平台是否使用？ Google 仿真程序？
  Visual Studio Android 仿真程序？ Xamarin Android 播放器？
- 在 Windows 上的 iOS: Xamarin 远程 ios 模拟器版本是否已安装 (检查**添加/删除程序**中**控制面板**)？
- 在 Windows 上的 iOS： 请为 Mac 生成主机还提供一种平台版本信息
- 设备是否具有网络连接 （通过 web 浏览器的检查）？

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>卸载

### <a name="windows"></a>Windows

具体取决于你如何获取工作簿和检查器，你可能需要执行两个卸载过程。 请检查这两种若要完全卸载软件。

#### <a name="visual-studio-installer"></a>Visual Studio 安装程序

如果你有 Visual Studio 2017，打开**Visual Studio Installer**，查看**各个组件**为**Xamarin 工作簿**。 如果选中，取消选中它，然后单击**修改**卸载。

#### <a name="system-uninstall"></a>系统卸载

如果您自己安装的工作簿和检查器使用下载的安装程序，它将需要通过卸载**应用和功能**Windows 10 上或通过系统设置页**添加/删除程序**较旧版本的 Windows 上的控制面板中。

> **启动 > 设置 > 系统 > 应用程序和功能**

![](install-images/windows-remove.png "Xamarin 工作簿和检查器中列出&quot;应用&amp;功能&quot;")

**你仍应遵循为 Visual Studio 安装程序，以确保工作簿的过程 （&) 检查器不会不获取重新安装你不知情的情况下。**

<a name="uninstall-macos" />

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

## <a name="downgrading"></a>降级

捆绑标识符**应用程序/Xamarin Workbooks.app**从更改`com.xamarin.Inspector`到`com.xamarin.Workbooks`1.4 的发行版本以方便将来拆分 Xamarin 工作簿和检查器安装程序中。

较旧的安装程序中的 bug，由于不可能降级 1.4 或较新版本使用 1.3.2 或较旧的安装程序。

若要从 1.4 或 1.3.2 到更高版本或更低版本降级：

1. [手动卸载工作簿 （&） 检查器](#macOS)
2. 运行 1.3.2 或更低版本`.pkg`安装程序