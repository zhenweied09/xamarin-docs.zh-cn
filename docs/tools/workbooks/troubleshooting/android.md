---
title: "在 Android 上的故障排除 Xamarin 工作簿"
ms.topic: article
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: eb188abb3e757f6f66af7758ced311ae1236d3ce
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>在 Android 上的故障排除 Xamarin 工作簿

## <a name="emulator-support"></a>仿真程序支持

若要运行的 Android 工作簿的 Android 仿真程序，必须可供使用。 不支持物理 Android 设备。

如果你的计算机支持它，我们建议以及 HAXM Google 的仿真程序。
如果你必须有你的系统上启用 HYPER-V，转而是使用 Visual Studio Android 仿真程序。

你必须具有的仿真程序运行 Android 5.0 或更高版本。 不支持 ARM 仿真程序。 使用`x86`或`x86_64`仅限设备。

请阅读[我们的文档上设置 Android 仿真程序][ android-emu]如果你不熟悉此过程。

**注意：** 1.1 和更早版本的工作簿将重试 （和失败 ！） 到它们是否可用，使用 ARM 仿真程序。 若要解决此问题，启动 x86 仿真程序之前打开或创建一个 Android 工作簿所选。 工作簿将始终希望连接到正在运行的仿真程序中，只要它可兼容。

## <a name="workbooks-wont-load"></a>不会加载工作簿

### <a name="workbook-window-spins-forever-never-loads-windows"></a>工作簿窗口永久，旋转永远不会加载 (Windows)

首先，请查看仿真程序通过在仿真程序的 web 浏览器中测试的任何网站具有完全工作网络访问权限。

### <a name="visual-studio-android-emulator-cannot-connect-to-internet"></a>Visual Studio Android 仿真程序无法连接到 internet

如果仿真程序不具有网络访问权限，你可能需要按照以下步骤来修复你的 HYPER-V 网络交换机。 如果你经常 Wi-fi 网络之间进行切换你可能需要定期重复此操作：

0. **请确保任何关键的网络操作都已完成，因为这可能会暂时断开与 internet 连接 Windows。**
1. 关闭模拟器。
2. 打开 `Hyper-V Manager`。
3. 下`Actions`，打开`Virtual Switch Manager...`。
4. 删除所有虚拟交换机。
5. 单击 `OK`。
6. 启动 VS Android 仿真程序。 你可能将提示重新创建虚拟网络交换机。
7. 测试 VS Android 仿真程序的浏览器可以访问 internet。

[android-emu]: https://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debug-on-emulator/


## <a name="related-links"></a>相关链接

- [报告 Bug](~/tools/workbooks/install.md#reporting-bugs)
