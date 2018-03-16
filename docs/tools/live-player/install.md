---
title: "Xamarin 实时播放器安装程序"
description: "编辑和实时 iOS 或 Android 设备上测试应用"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/22/2017
ms.openlocfilehash: ddc16dc1faaf623098aad5bca340c15f943223ba
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="xamarin-live-player-setup"></a>Xamarin 实时播放器安装程序

Xamarin 实时播放机使您对您的应用程序进行实时的编辑并使这些更改反映出来实时在设备上。 在实时的 Xamarin Player 应用 – 无需设置的仿真程序或使用电缆将部署中运行你的代码 ！ 本文介绍如何设置实时的 Xamarin Player。

![预览功能](~/media/shared/preview.png)

## <a name="1-get-the-app"></a>1.获取应用

### <a name="xamarin-live-player-for-android"></a>适用于 Android 的 Xamarin 实时播放器
实时的 Xamarin Player 用于 Android 从 Google Play:

[ ![在 Google Play 上提供](install-images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)

适用于 Android 的设备，而无需 Google Play Xamarin 实时播放机可通过[HockeyApp](https://aka.ms/xlp-hockeyapp)分发。 此外，早期预览版中生成为可以通过启用直接从 Google Play 安装 Android[打开 beta 程序](https://play.google.com/apps/testing/com.xamarin.live)

### <a name="xamarin-live-player-for-ios"></a>Xamarin iOS 的实时播放器
我们鼓励用户加入[实时的 Xamarin Player 应用_iOS 预览_](https://aka.ms/liveplayeralpha)以享受到通过 TestFlight 的最新改进的快速访问。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="2-get-visual-studio-2017-preview-on-windows-or-for-mactabsvsmac"></a>2.在 Windows 上获取 Visual Studio 自 2017 年 1 Preview (或[适用于 Mac](?tabs=vsmac))

实时的 Xamarin Player 需要：

- Visual Studio 2017 [15.4](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/#visualstudio2017)或更高版本。
- Visual Studio 计算机和相同的 WiFi 网络上的设备

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3.第一次使用实时的 Xamarin Player

1. 打开**Visual Studio 2017**。
2. 转到**工具 > 选项...**和选择**Xamarin > 其他**选项卡。
3. 刻度**启用实时的 Xamarin Player**:

  ![启用 Xamarin 实时播放器中复选框选项窗口](install-images/vs2017-options.png)

2. 创建或打开一个 Xamarin 项目 (或[示例](~/tools/live-player/samples.md))。
3. 选择**实时播放器**设备列表中：

  ![设备列表包括实时的 Xamarin Player 选项](install-images/devices-empty-windows.png)

  * 如果你已具有配对的设备，它将可作为一个选项。
  * 否则系统将提示你配对的设备在需要时。
4. 按**运行**按钮或选择以下任一选项从**运行**或右键单击菜单：

  - **启动但不调试**– 你可以编辑应用程序，并在设备发生的更改，请参阅 （如进行更改并保存该文件，应用程序重新启动）。
  - **启动调试**– 你可以设置断点并检查变量，但无法编辑代码。

  或者，选择**工具 > 实时的 Xamarin Player > 实时运行的当前视图**，可以在其中编辑应用程序，并在设备发生的更改，请参阅。 当前视图显示 （而不是应用程序的主屏幕）。

5. 如果设备已成对的并且在设备上运行的实时的 Xamarin Player 应用，代码将立即执行 ！

  如果没有设备配对，QR 代码将显示的说明来对设备：

  ![对设备窗口](install-images/manage-empty-windows.png)

  如果设备不能用于配对，则会出现错误。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="2-get-visual-studio-for-mac-or-for-windowstabsvswin"></a>2.获取适用于 Mac 的 Visual Studio (或[适用于 Windows](?tabs=vswin))

实时的 Xamarin Player 需要：

- OS X 10.11，macOS 10.12，或更高版本
- Visual Studio for Mac
- Mac 和相同的 WiFi 网络上的设备

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3.第一次使用 Xamarin 实时播放机

1. 打开**Visual Studio for Mac**。
2. 转到**Visual Studio > 首选项...**和选择**项目 > Xamarin 实时播放器 （预览版）**选项卡。
3. 刻度**启用实时的 Xamarin Player**:

  [![启用 Xamarin 实时播放器中复选框选项窗口](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

2. 创建或打开一个 Xamarin 项目 (或[示例](~/tools/live-player/samples.md))。
3. 选择**实时播放器**设备列表中。

  ![设备列表包括实时的 Xamarin Player 选项](install-images/devices.png)

  * 如果你已具有配对的设备，它将可作为一个选项。
  * 否则系统将提示你配对的设备在需要时。
  * 选择**Xamarin 实时播放器设备...**来管理你想要与实时的 Xamarin Player 一起使用的设备。

4. 按**运行**按钮或选择以下任一选项从**运行**或右键单击菜单：

  ![运行菜单选项](install-images/run-menu.png)

  - **启动但不调试**– 你可以编辑应用程序，并在设备发生的更改，请参阅 （如进行更改并保存该文件，应用程序重新启动）。
  - **启动调试**– 你可以设置断点并检查变量，但无法编辑代码。
  - **实时运行当前视图**– 你可以编辑应用程序，并在设备发生的更改，请参阅。 当前视图显示 （而不是应用程序的主屏幕）。

5. 如果设备已成对的并且在设备上运行的实时的 Xamarin Player 应用，代码将立即执行 ！

  如果设备没有配对，QR 代码将显示包含有关如何对设备的说明：

  ![对设备窗口](install-images/manage-empty.png)

  如果设备不能用于配对，将出现错误：

  ![无法连接到设备错误消息](install-images/error-cannot-connect.png)


-----

如果遇到任何问题，请参阅[限制和故障排除](~/tools/live-player/troubleshooting.md)。


## <a name="related-links"></a>相关链接

- [限制](~/tools/live-player/limitations.md)
- [疑难解答](~/tools/live-player/troubleshooting.md)
- [Xamarin 实时播放器示例](~/tools/livehttps://developer.xamarin.com/samples.md)
