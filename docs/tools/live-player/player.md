---
title: Xamarin 实时播放器应用程序
description: 本文档介绍了 Xamarin 实时播放器应用程序，可用于预览代码更改实时设备。 它讨论了安装程序、 示例、 日志、 管理设备的设置。
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: topgenorth
ms.author: toopge
ms.date: 05/14/2017
ms.openlocfilehash: 88f7f62650484007c221aa7baaa684f872e0a8e9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794145"
---
# <a name="xamarin-live-player-app"></a>Xamarin 实时播放器应用程序

![预览功能](~/media/shared/preview.png)

一旦你的手机上安装应用程序之后，请按照[安装说明进行操作](~/tools/live-player/install.md)连接到你的计算机。 尝试之一[示例应用](~/tools/live-player/samples.md)使其正常工作。

在启动时，实时的 Xamarin Player 应用类似如下所示 (iOS 和 Android 上分别):

![实时播放器 iOS 应用屏幕快照](player-images/app-iphone-sml.png) ![实时播放器 Android 应用屏幕快照](player-images/app-android-sml.png)

当你按**到 Visual Studio 对**，使用相机来扫描在你的计算机上显示的条形码：

![IOS 条形码扫描程序的屏幕截图](player-images/scan-iphone-sml.png) ![Android 条形码扫描程序的屏幕截图](player-images/scan-android-sml.png)

如果成功连接，则应立即运行代码在设备上几乎 (如[计算器示例](https://developer.xamarin.com/samples/mobile/LivePlayer/BasicCalculator)):

![在设备上运行的示例计算器应用程序](player-images/basic-calculator-iphone-sml.png)

## <a name="options"></a>选项

按信息按钮 **(i)** 底部的应用程序以显示**选项**菜单：

[![选项菜单的屏幕截图](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>日志

查看日志来诊断问题。

### <a name="settings"></a>设置

- 切换显示的编译和运行时错误。
- 版本信息。
- 发送反馈。

[![设置的屏幕截图](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>管理设备

若要首次连接设备，请按照中的说明[要求和安装程序](~/tools/live-player/install.md)。 你可以对多个设备 （如 iOS 和 Android），并通过 IDE 管理它们。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中，选择**工具 > 实时的 Xamarin Player > 管理的设备...**

![管理设备窗口](player-images/manage-tools-menu-vs.png)

此窗口允许你执行以下操作：

- 对通过扫描代码的新设备
- 或者通过键入其屏幕上显示的代码进行配对的设备
- 从列表中删除现有的设备

从设备列表，你也可以访问此窗口。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在适用于 Mac 的 Visual Studio，选择**工具 > (实时的 Xamarin Player) 管理的设备...**

![管理设备窗口](player-images/manage-tools-menu.png)

此窗口允许你执行以下操作：

- 对通过扫描代码的新设备
- 或者通过键入其屏幕上显示的代码进行配对的设备
- 从列表中删除现有的设备

![管理设备窗口](player-images/manage.png)

从设备列表，你也可以访问此窗口：

![从设备列表中选择 Xamarin 实时播放机设备](player-images/manage-device-menu.png)

-----

如果遇到任何问题，请参阅[限制和故障排除](~/tools/live-player/troubleshooting.md)。

## <a name="related-links"></a>相关链接

- [限制](~/tools/live-player/limitations.md)
- [疑难解答](~/tools/live-player/troubleshooting.md)
- [Xamarin 实时播放器示例](samples.md)
