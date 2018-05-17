---
title: 适用于 Windows 的远程 iOS 模拟器
description: 进行远程处理的 iOS 模拟器为 Windows 可测试你的应用在 iOS 模拟器中 Windows 与 Visual Studio 2017 一起显示。
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: topgenorth
ms.author: toopge
ms.date: 05/11/2018
ms.openlocfilehash: b07cc24e63f4aa3ce4451e3bdb5819f1df1058c6
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="remoted-ios-simulator-for-windows"></a>适用于 Windows 的远程 iOS 模拟器

进行远程处理的 iOS 模拟器为 Windows 可测试你的应用在 iOS 模拟器中 Windows 与 Visual Studio 2017 一起显示。

[![](ios-simulator-images/hero-sml.png "iOS 模拟器在 Windows 上运行")](ios-simulator-images/hero.png#lightbox)

## <a name="getting-started"></a>入门

适用于 Windows 的远程连接的 iOS 模拟器在 Visual Studio 2017 Xamarin 的一部分自动安装。 若要使用它，请按照下列步骤：

1. [到 Mac 生成主机配对 Visual 2017](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 在 Visual Studio 2017，开始调试 iOS 或 tvOS 项目。 适用于 Windows 的远程连接的 iOS 模拟器将显示您的 Windows 计算机上。

## <a name="simulator-window"></a>模拟器窗口

模拟器窗口顶部的工具栏包含很多的有用的按钮：

- **主**– 模拟 iOS 设备上的主页按钮
- **锁定**– 锁定模拟器 （轻扫以解锁）
- **屏幕快照**– 将保存模拟器的屏幕快照
- [**设置**](#settings) – 显示键盘、 位置和其他设置
- [**其他选项**](#other-options) – 引出如旋转和动摇手势的各种模拟器选项

    [![](ios-simulator-images/maps-app-sml.png "iOS 模拟器映射示例")](ios-simulator-images/maps-app.png#lightbox)

## <a name="settings"></a>设置

单击工具栏的齿轮图标将打开**设置**窗口：

[![](ios-simulator-images/settings-sml.png "iOS 模拟器设置")](ios-simulator-images/settings.png#lightbox)

这些设置，可以启用硬件键盘上，选择设备应位置 （静态和移动位置同时支持） 的报表，启用 Touch ID 和重置的内容和设置模拟器。

## <a name="other-options"></a>其他选项

工具栏的省略号按钮将显示其他选项，例如旋转、 动摇手势和重新启动。 可以通过在模拟器的窗口中的任意位置右键单击以列表形式查看这些相同的选项：

[![](ios-simulator-images/more-sml.png "iOS 模拟器其他设置")](ios-simulator-images/more.png#lightbox)

## <a name="touchscreen-support"></a>触摸屏支持

大多数现代的 Windows 计算机具有触摸屏。 由于远程连接的 iOS 模拟器为 Windows 支持触摸交互，你可以测试你的应用使用相同捏合、 轻扫和你使用物理 iOS 设备使用的多手指触摸手势。

同样，进行远程处理的 iOS 模拟器为 Windows 视为 Apple 铅笔输入 Windows 触笔输入。

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>禁用远程连接的 iOS 模拟器 for Windows

若要禁用远程连接的 iOS 模拟器为 Windows，导航到**工具 > 选项 > Xamarin > iOS 设置**并取消选中**到 Windows 的远程模拟器**。

[![](ios-simulator-images/options-sml.png "复选框可使用模拟器")](ios-simulator-images/options.png#lightbox)

使用此选项时禁用，调试将打开连接 Mac 上的 iOS 模拟器构建的主机。
