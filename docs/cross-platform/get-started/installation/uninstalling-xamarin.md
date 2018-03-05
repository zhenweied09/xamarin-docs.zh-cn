---
title: "卸载 Xamarin"
description: "从计算机上卸载 Xamarin 产品"
ms.topic: article
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/08/2017
ms.openlocfilehash: d2db4af7dd13611075bc2b100470b5fb3ba83118
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="uninstalling-xamarin"></a>卸载 Xamarin

> [!IMPORTANT]
> 本文介绍如何从 Mac 或 Windows 计算机卸载 Xamarin Studio 或 Xamarin 的其他产品。 若要了解如何卸载 Visual Studio for Mac，请参阅 docs.microsoft.com 上的[卸载](https://docs.microsoft.com/visualstudio/mac/uninstall)指南

# <a name="overview"></a>概述

Visual Studio 中支持许多能实现跨平台应用程序开发的 Xamarin 产品，包括 Xamarin Studio 等独立应用，以及 Xamarin 支持等其他应用的扩展。

本指南介绍如何在 macOS 上删除 Xamarin 功能，或如何在 Windows 上从 Visual Studio 中删除 Xamarin 功能：

1.  [卸载 Xamarin Studio](#uninstallxamarinstudio)
  1.  [卸载 Mono](#uninstallmono)
  1.  [卸载 Xamarin.Android](#uninstallandroid)
  1.  [卸载 Xamarin.iOS](#uninstallios)
  1.  [卸载 Xamarin.Mac](#uninstallmac)
  2.  [卸载检查器和工作簿](#uninstallworkbooks)
1.  [从 Windows 卸载 Xamarin](#uninstallwindows)
  1.  [从 Visual Studio 2015 及更早版本中卸载 Xamarin](#uninstallvs2015)
  1.  [从 Visual Studio 2017 中卸载 Xamarin](#uninstallvs2017)
1.  [卸载 Visual Studio for Mac](#uninstallvsmac)

如果需要使用通用安装程序重新安装 Xamarin，始终建议先重启计算机。

# <a name="uninstalling-xamarin-on-mac"></a>卸载 Mac 上的 Xamarin

本指南介绍了单独卸载每个产品的知识，可导航到相关部分查看。 遵循本指南中的全部指导，可卸载整个 Xamarin 工具集。

有关[卸载脚本](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh)的使用帮助，请跳转到本指南底部的[使用卸载脚本](#uninstallscript)。

<a name="uninstallxamarinstudio" />

## <a name="uninstall-xamarin-studio"></a>卸载 Xamarin Studio

从 Mac 中卸载 Xamarin Studio 的第一步是在 **/Applications** 目录中找到 **Xamarin Studio.app**，并将其拖动到**回收站**。 或者，单击右键，然后选择“移到回收站”，如下所示：

 [ ![](uninstalling-xamarin-images/image1.png "或者，单击右键并选择“移到回收站”，如下所示")](uninstalling-xamarin-images/image1.png)

删除此应用程序包会删除 Xamarin Studio，但与 Xamarin 相关的其他文件仍在文件系统上。

若要删除 Xamarin Studio 的所有痕迹，应在终端运行以下命令：

```bash
sudo rm -rf "/Applications/Xamarin Studio.app"
rm -rf ~/Library/Caches/XamarinStudio-*
rm -rf ~/Library/Preferences/XamarinStudio-*
rm -rf ~/Library/Logs/XamarinStudio-*
rm -rf ~/Library/XamarinStudio-*
```

<a name="uninstallmono" />

## <a name="uninstall-mono-sdk-mdk"></a>卸载 Mono SDK (MDK)

Mono 是 Microsoft .NET Framework 的开放源代码实现，可供所有 Xamarin 产品（Xamarin.iOS、Xamarin.Android 和 Xamarin.Mac）使用以在 C# 中开发这些平台。

> [!IMPORTANT]
> 注意：Xamarin 外的其他应用程序也使用 Mono，例如 Unity。 卸载 Mono 前，请确保 Mono 上没有其他依赖项。

若要从计算机删除 Mono Framework，请在终端运行以下命令：

```bash
sudo rm -rf /Library/Frameworks/Mono.framework
sudo pkgutil --forget com.xamarin.mono-MDK.pkg
```

<a name="uninstallandroid" />

## <a name="uninstall-xamarinandroid"></a>卸载 Xamarin.Android

安装和使用 Xamarin.Android 需要许多必备项，例如 Android SDK 和 Java SDK。 有关这些必备组件的详细信息，请参阅[手动安装](https://docs.microsoft.com/visualstudio/mac/installation/)指南。

使用以下命令删除 Xamarin.Android：

```bash
sudo rm -rf /Developer/MonoDroid
rm -rf ~/Library/MonoAndroid
sudo pkgutil --forget com.xamarin.android.pkg
sudo rm -rf /Library/Frameworks/Xamarin.Android.framework
```

### <a name="uninstall-android-sdk-and-java-sdk"></a>卸载 Android SDK 和 Java SDK

开发 Android 应用程序需要 Android SDK。 若要完全删除 Android SDK 的所有部分，请在 **~/Library/Developer/Xamarin/** 中找到相关文件，并将其移到**回收站**，如下所示：

 [ ![](uninstalling-xamarin-images/image2.png "若要完全删除 Android SDK 的所有部分，请找到相关文件，并将其移到“回收站”，如下所示")](uninstalling-xamarin-images/image2.png)

不必卸载 Java SDK (JDK)，因为它已预先打包为 Mac OS X 一部分。

<a name="uninstallios" />

## <a name="uninstall-xamarinios"></a>卸载 Xamarin.iOS

Xamarin.iOS 允许结合使用 C# 或 F# 和 Mac 上的 Xamarin Studio 开发 iOS 应用程序。
Xamarin 生成主机也是随早期版本的 Xamarin.iOS 自动安装的，以便在 Visual Studio 中进行 iOS 开发。 若要从计算机中卸载这二者，请遵循以下步骤：

在终端使用以下命令从文件系统删除所有 Xamarin.iOS 文件：

```bash
rm -rf ~/Library/MonoTouch
sudo rm -rf /Library/Frameworks/Xamarin.iOS.framework
sudo rm -rf /Developer/MonoTouch
sudo pkgutil --forget com.xamarin.monotouch.pkg
sudo pkgutil --forget com.xamarin.xamarin-ios-build-host.pkg
```

### <a name="uninstall-the-mac-build-host"></a>卸载 Mac 生成主机

注意：如已更新为 Xamarin 4，则应已删除此项；否则，请在终端运行以下命令以删除生成主机应用程序：

```bash
sudo rm -rf "/Applications/Xamarin.iOS Build Host.app"
```

生成主机进程或 `launchd` 作业可能仍在特定端口上运行或侦听。
通过在终端运行 `launchctl list | grep com.xamarin.mtvs.buildserver` 可检查其状态。

```bash
sudo launchctl unload /Library/LaunchAgents/com.xamarin.mtvs.buildserver.plist
sudo rm -f /Library/LaunchAgents/com.xamarin.mtvs.buildserver.plist
```

<a name="uninstallmac" />

## <a name="uninstall-xamarinmac"></a>卸载 Xamarin.Mac

成功卸载 Xamarin Studio 后，可使用以下 2 个分别用于从 Mac 分别根除产品和许可证的命令从计算机删除 Xamarin.Mac：

```bash
sudo rm -rf /Library/Frameworks/Xamarin.Mac.framework
rm -rf ~/Library/Xamarin.Mac
```

<a name="uninstallworkbooks" />

## <a name="uninstall-workbooks-and-inspector"></a>卸载工作簿和检查器

以下 Bash 命令删除 Xamarin Inspector 和工作簿 1.2.2 版及更高版本：

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

对于较早版本，请参阅[工作簿](~/tools/workbooks/install.md#uninstall-macos)卸载指南。

## <a name="uninstall-the-xamarin-installer"></a>卸载 Xamarin 安装程序

使用以下命令删除 Xamarin 通用安装程序的所有痕迹：

```bash
rm -rf ~/Library/Caches/XamarinInstaller/
rm -rf ~/Library/Logs/XamarinInstaller/
rm -rf ~/Library/Preferences/Xamarin/
```

<a name="uninstallscript" />

## <a name="using-the-uninstall-script"></a>使用卸载脚本

[卸载脚本](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh)可从计算机中删除 Xamarin。 使用卸载脚本：

1.  下载卸载脚本，并记下其下载位置。 默认情况下，下载位置为 **/Downloads** 目录。
1.  打开“终端”，并将工作目录更改为下载脚本的位置：

        $ cd /location/of/file

1. 使脚本可执行，并通过 **sudo** 运行它：

        $ chmod +x ./xamarin_uninstall.sh
        $ sudo ./xamarin_uninstall.sh

1. 最后，删除卸载脚本。

此时，Xamarin 应该已从计算机上被卸载了。

<a name="uninstallwindows" />

# <a name="uninstalling-xamarin-on-windows"></a>卸载 Windows 上的 Xamarin

<a name="uninstallvs2015" />

## <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 及更早版本

通过“控制面板”，可从 Windows 计算机卸载 Xamarin。 导航到“程序和功能” ，或“程序”>“卸载程序”，如下所示：

 [ ![](uninstalling-xamarin-images/image3.png "导航到“程序和功能”或“程序”  卸载程序，如下所示")](uninstalling-xamarin-images/image3.png) [ ![](uninstalling-xamarin-images/image4.png "Navigate to Programs and Features or Programs  Uninstall a Program as illustrated here")](uninstalling-xamarin-images/image4.png)

若要卸载 Xamarin Studio，请在程序列表中找到 **Xamarin Studio 5.x.x**，然后单击“卸载”按钮。 若要删除 Visual Studio 的 Xamarin 扩展和 SDK，请在程序列表中找到 **Xamarin**，然后单击“卸载”。 如以下屏幕截图所示：

 [ ![](uninstalling-xamarin-images/image4a.png "如屏幕截图所示")](uninstalling-xamarin-images/image4a.png)

完全卸载所有 Xamarin 组件也可删除这些程序：

-  Android SDK


  [ ![](uninstalling-xamarin-images/image5.png "完全卸载所有 Xamarin 组件也可删除这些程序")](uninstalling-xamarin-images/image5.png)
-  GTK#


  [ ![](uninstalling-xamarin-images/image6.png "完全卸载所有 Xamarin 组件也可删除这些程序")](uninstalling-xamarin-images/image6.png)
-  Xamarin 通用安装程序


 [ ![](uninstalling-xamarin-images/image7.png "完全卸载所有 Xamarin 组件也可删除这些程序")](uninstalling-xamarin-images/image7.png)
-  Java SDK（删除时请谨慎操作，因为其上可能有其他依赖项）


 [ ![](uninstalling-xamarin-images/image8.png "删除 Java SDK 时请谨慎操作，因为其上可能有其他依赖项")](uninstalling-xamarin-images/image8.png)

若要完全卸载 Visual Studio，请按照 [Microsoft 的说明](https://msdn.microsoft.com/library/mt720585.aspx)进行操作。


<a name="uninstallvs2017" />

# <a name="visual-studio-2017"></a>Visual Studio 2017

可使用安装程序应用从 Visual Studio 2017 中卸载 Xamarin：

1. 使用“开始”菜单打开“Visual Studio 安装程序”。

  [ ![](uninstalling-xamarin-images/vs2017-01-sml.png "启动 Visual Studio 安装程序")](uninstalling-xamarin-images/vs2017-01.png)

1. 针对想要更改的实例按“修改”按钮。

  [ ![](uninstalling-xamarin-images/vs2017-02-sml.png "按“修改”按钮")](uninstalling-xamarin-images/vs2017-02.png)

1. 在“工作负载”选项卡中，取消选择“使用 .NET 的移动开发”选项（在“移动与游戏”部分中）。

  [ ![](uninstalling-xamarin-images/vs2017-03-sml.png "取消选中“移动开发”工作负载")](uninstalling-xamarin-images/vs2017-03.png)

1. 在窗口的右下角中，单击“修改”按钮。
1. 安装程序将删除已取消选择的组件（必须要在 Visual Studio 2017 关闭之后，安装程序才可进行修改）。

  [ ![](uninstalling-xamarin-images/vs2017-04-sml.png "按“修改”按钮")](uninstalling-xamarin-images/vs2017-04.png)

通过在步骤 3 中切换到“各个组件”选项卡并取消选中特定的组件，可卸载单个 Xamarin 组件（如 Profiler 或 Workbooks）：

[ ![](uninstalling-xamarin-images/vs2017-components-sml.png "卸载单个组件")](uninstalling-xamarin-images/vs2017-components.png)

若要完全卸载 Visual Studio 2017，请从“启动”按钮旁具有三条横线的菜单中选择“卸载”。

[ ![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "完全卸载 Visual Studio")](uninstalling-xamarin-images/vs2017-uninstall.png)

> [!IMPORTANT]
> 警告：如果并行 (SxS) 安装了两个（或多个）Visual Studio 的实例（如发行版和预览版），则卸载一个实例可能删除另一个 Visual Studio 实例中的一些 Xamarin 功能，包括：
>
> - Xamarin Profiler
> - Xamarin Workbooks/Inspector
> - Xamarin 远程 iOS 模拟器
> - Apple Bonjour SDK
>
> 在某些情况下，卸载其中一个 SxS 实例可能导致这些功能将被错误地删除。 在卸载了 SxS 实例后，这可能会降低针对系统上剩余的 Visual Studio 实例的 Xamarin 平台性能。
>
>通过在 Visual Studio 安装程序中运行“修复”选项（即将重新安装缺少的组件），可解决此问题。

<a name="uninstallvsmac" />

# <a name="uninstalling-visual-studio-for-mac"></a>卸载 Visual Studio for Mac

若要卸载 Visual Studio for Mac，但要继续使用 Xamarin Studio，请在 **/Applications** 目录中找到 **Visual Studio.app**，并将其拖到回收站。 或者，单击右键，然后选择“移到回收站”，如下所示：

 [ ![](uninstalling-xamarin-images/image9.png "右键单击 Visual Studio 图标，然后选择“移到回收站”")](uninstalling-xamarin-images/image9.png)

若要从计算机完全卸载 Xamarin，首先请删除 Visual Studio for Mac，然后按照[卸载 Xamarin Studio](#uninstallxamarinstudio) 部分列出的步骤执行操作。

# <a name="summary"></a>摘要

本文介绍了如何通过使用终端命令从 Mac 完全卸载 Xamarin，以及如何通过“程序和功能”选项（适用于 Visual Studio 2015 及更早版本）和使用 **Visual Studio 安装程序**（适用于 Visual Studio 2017）从 Windows 计算机卸载 Xamarin。


## <a name="related-links"></a>相关链接

- [卸载脚本（示例）](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh)
