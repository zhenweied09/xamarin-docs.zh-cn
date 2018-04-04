---
title: 进行远程处理的 iOS 模拟器 （用于 Windows)
description: 完全在 Windows 上的 Visual Studio 的测试和调试 iOS 应用
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 04/07/2017
ms.openlocfilehash: bcf0aa2b1677af0b980c6ca48bb29c1cad32e52d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="remoted-ios-simulator-for-windows"></a>进行远程处理的 iOS 模拟器 （用于 Windows)

_完全在 Windows 上的 Visual Studio 的测试和调试 iOS 应用_

[![](ios-simulator-images/hero-sml.png "iOS 模拟器在 Windows 上运行")](ios-simulator-images/hero.png#lightbox)

## <a name="download-and-install"></a>下载并安装

下载[installer](https://dl.xamarin.com/xamarin-simulator/Xamarin.Simulator.Installer.msi)并在你的 Windows 计算机上安装。 应已安装适用于 Xamarin 的 visual Studio 工具。

> [!NOTE]
> 在 Visual Studio 上使用远程 iOS 模拟器需要安装 Xamarin 与联网的 Mac。

## <a name="getting-started"></a>入门

若要使用远程 iOS 模拟器：

1. 请确保 Visual Studio 已连接到你的 Mac 上，在开始远程 iOS 模拟器之前至少一次。
2. 确保 iOS 或 tvOS 应用**启动项目**并开始调试。

你可以禁用从远程 iOS 模拟器**工具 > 选项 > Xamarin > iOS 设置**通过取消选中框**到 Windows 的远程模拟器**此处所示：

[![](ios-simulator-images/options-sml.png "复选框可使用模拟器")](ios-simulator-images/options.png#lightbox)

IOS 模拟器随后将打开连接的 Mac 计算机上。 选中此选项以启用远程 iOS 模拟器。

## <a name="features"></a>功能

远程 iOS 模拟器为你提供了一种方法进行测试和调试完全从 Visual Studio 在 Windows 模拟器上的 iOS 应用。

### <a name="simulator-window"></a>模拟器窗口

窗口工具栏包含很多的按钮与模拟器进行交互：

- **主**– 模拟在设备上的主页按钮。
- **锁定**– 锁定的模拟器 （你可以往下轻扫以解锁）。
- **屏幕快照**– 将模拟器的屏幕快照保存到磁盘。
- [**设置**](#settings) – 配置的键盘和位置。
- 其他[**选项**](#options) – 各种模拟器选项可用如旋转、 动摇，或调用在模拟器中的其他状态。 当模糊显示一些选项时，可以从显示在工具栏中，或通过在窗口中右键单击省略号图标访问它们。

    [![](ios-simulator-images/maps-app-sml.png "iOS 模拟器映射示例")](ios-simulator-images/maps-app.png#lightbox)


### <a name="settings"></a>设置

"齿轮"图标打开**设置**窗口：

[![](ios-simulator-images/settings-sml.png "iOS 模拟器设置")](ios-simulator-images/settings.png#lightbox)

这允许您启用硬件键盘在模拟器中，并选择哪些位置报告给设备 （包括静态位置或其他移动位置选项）。



### <a name="other-options"></a>其他选项

右键单击在模拟器窗口以查看在模拟器中，例如旋转触发动摇手势，并重新启动模拟器中可用的所有选项的任意位置：

[![](ios-simulator-images/more-sml.png "iOS 模拟器其他设置")](ios-simulator-images/more.png#lightbox)

### <a name="touchscreen-support"></a>触摸屏支持

大多数现代的 Windows 计算机具有触摸屏，远程 iOS 模拟器，可以在 touch 模拟器窗口，以在 iOS 应用程序中测试用户交互。

这包括挤压、 轻扫，和多个手指触摸手势-以前无法仅可轻松地测试在物理设备的操作。

Windows 中的触笔支持还会转换为 Apple 铅笔模拟器上的输入。

<!--
<a name="knownissues" />

# Known Issues

 - Apple Watch devices may show in the Visual Studio device list, but are not yet supported.
 - Launching in **Release** mode may also start Apple’s simulator on the networked Mac.
 - Closing the remote iOS Simulator on Windows will not immediately stop debugging in Visual Studio. Stop debugging manually from the menu or the red button.
 - Opening too many different simulators simultaneously will produce unexpected results.
 - Exception of type `Foundation.NSErrorException` may be thrown while launching Simulators. Workaround is to kill csproxy (server process) on the Mac host and re-deploy to the simulator.
 - Performance may be slower when using Xcode 8
-->
