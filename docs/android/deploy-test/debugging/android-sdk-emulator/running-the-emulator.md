---
title: 运行 Android SDK 仿真器
description: 如何使用 Android SDK 仿真器调试应用
ms.prod: xamarin
ms.assetid: AEA165A4-D81A-411B-91DF-2DED2EED27B5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 630520f88dd23d3860b5f42fbb9bc4eb35ca2c4b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="running-the-android-sdk-emulator"></a>运行 Android SDK 仿真器

本指南介绍如何在 Android SDK 仿真器中启动虚拟设备以调试和测试应用。

## <a name="using-a-pre-configured-virtual-device"></a>使用预配置的虚拟设备

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio 包含预配置虚拟设备，可以在“设备”下拉菜单中查看。 例如，在下面的 Visual Studio 2017 屏幕截图中，多个预配置虚拟设备都可用：

-   **VisualStudio\_android-23\_arm\_phone**

-   **VisualStudio\_android-23\_arm\_tablet**

-   **VisualStudio\_android-23\_x86\_phone** 

-   **VisualStudio\_android-23\_x86\_tablet** 

[![虚拟设备](running-the-emulator-images/win/01-virtual-devices-sml.png)](running-the-emulator-images/win/01-virtual-devices.png#lightbox)

通常都会选择使用“VisualStudio\_android-23\_x86\_phone”虚拟设备来测试和调试手机应用。 如果这些预配置虚拟设备中有一个能够满足需求（即与应用的目标 API 级别一致），请跳转到[启动仿真器](#launching)，开始在仿真器中运行应用。 （如果尚不熟悉 Android API 级别，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。）

如果 Xamarin.Android 项目使用的目标框架级别与可用虚拟设备都不兼容，不可用的虚拟设备会在下拉菜单中的“不支持的设备”下列出。 例如，以下项目的目标框架设为“Android 7.1 Nougat (API 25)”，这与默认提供的“Android 6.0”虚拟设备不兼容：

[![不兼容的虚拟设备](running-the-emulator-images/win/02-incompatible-level-sml.png)](running-the-emulator-images/win/02-incompatible-level.png#lightbox)

可以单击“更改最低 Android 目标”，更改项目的最低 Android 版本，使其与可用虚拟设备的 API 级别一致。 也可以使用“Android 仿真器管理器”，新建支持目标 API 级别的虚拟设备，如后面的[配置虚拟设备](#virtualdevice)中所述。 必须先安装新 API 级别对应的系统映像（下一部分将介绍具体操作方法），然后才能为虚拟设备配置此 API 级别。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Visual Studio for Mac 包含预配置虚拟设备，可在“设备”下拉菜单中查看这些设备。 例如，在下面的屏幕截图中，两个预配置虚拟设备都可用：

-   Android\_Accelerated\_x86

-   Android\_ARMv7a

[![虚拟设备](running-the-emulator-images/mac/01-virtual-devices-sml.png)](running-the-emulator-images/mac/01-virtual-devices.png#lightbox)

通常会选择使用“Android\_Accelerated\_x86”虚拟设备来测试和调试手机应用。 如果此预配置虚拟设备满足需求（即与应用的目标 API 级别一致），请跳转到[启动仿真器](#launching)，开始在仿真器中运行应用。 （如果尚不熟悉 Android API 级别，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。）

-----

## <a name="creating-custom-virtual-devices"></a>创建自定义虚拟设备

若要创建自定义虚拟设备，必须使用 Xamarin Android 设备管理器或使用作为 Android SDK 一部分的旧版 Google 仿真器管理器。 有关创建和自定义虚拟设备的详细信息，请参阅 [Xamarin Android 设备管理器](~/android/get-started/installation/android-emulator/xamarin-device-manager.md)。
如果想使用旧版 Google 仿真器管理器，请参阅 [Google 仿真器管理器](~/android/get-started/installation/android-emulator/google-emulator-manager.md)。

请注意，如果要针对 Android 8.0 Oreo 进行开发，则必须使用 Xamarin Android 设备管理器。

<a name="launching" />

## <a name="launching-the-emulator"></a>启动仿真器

在 IDE 的顶部附近，有一个下拉列表菜单，可用于选择“调试”或“发布”模式。 选择“调试”可将调试器附加到仿真器内运行的应用程序进程。 

在“设备”下拉菜单中选择虚拟设备后，选择“调试”或“发布”模式，再单击“播放”按钮，以运行应用：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![“调试”和“发布”模式以及“播放”按钮](running-the-emulator-images/win/17-debug-release-sml.png)](running-the-emulator-images/win/17-debug-release.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![“调试”和“发布”模式以及“播放”按钮](running-the-emulator-images/mac/16-debug-release-sml.png)](running-the-emulator-images/mac/16-debug-release.png#lightbox)

-----

在 Android 仿真器启动后，Xamarin.Android 会将应用部署到仿真器。 仿真器会使用配置的虚拟设备映像运行此应用。 下面的示例屏幕截图展示了 Android SDK 仿真器（仿真器正在运行名为“MyApp”的空白应用）：

![运行空白应用的仿真器](running-the-emulator-images/emulator-running.png)

可以一直运行仿真器；无需关闭仿真器并在每次运行应用时重启。 当 Xamarin.Android 应用首次在仿真器中运行时，将会先安装面向目标 API 级别的 Xamarin.Android 共享运行时，再安装应用。 运行时安装过程可能需要一段时间，请耐心等待。 仅当首次向仿真器部署 Xamarin.Android 应用时，才会安装运行时 &ndash; 后续部署速度更快，因为仅将应用复制到仿真器。

若要深入了解如何使用 Android SDK 仿真器，请参阅以下 Android 开发者主题：

-   [屏幕导航](https://developer.android.com/studio/run/emulator.html#navigate)

-   [在仿真器中执行基本任务](https://developer.android.com/studio/run/emulator.html#tasks)

-   [使用扩展控件、设置和帮助](https://developer.android.com/studio/run/emulator.html#extended)

