---
title: Android 仿真器硬件加速
description: 如何准备计算机以获得 Google Android Emulator 的最佳性能
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/10/2018
ms.openlocfilehash: 2f0bb6f1371b9ce1b925b876851d58f3c4d01419
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2018
---
# <a name="android-emulator-hardware-acceleration"></a>Android 仿真器硬件加速

若没有硬件加速，Google Android Emulator 会特别慢。 可以显著提高 Google Android Emulator 的性能，方法是使用面向 x86 硬件的特殊仿真器硬件映像以及以下两项虚拟化技术之一：

1. **Microsoft Hyper-V 和虚拟机监控程序平台** &ndash; Hyper-V 是可用于 Windows 10 的虚拟化组件，支持在物理主机上运行虚拟化的计算机系统。 建议对加速的 Google Android Emulator 映像使用此虚拟化技术。 若要了解有关 Hyper-V 的详细信息，请咨询 [Windows 10 上的 Hyper-V 指南](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/)。
2. **Intel 硬件加速执行管理器 (HAXM)** &ndash; 这是用于运行 Intel CPU 的计算机的虚拟化引擎。 建议无法使用 Hyper-V 的开发人员使用此虚拟化引擎。

如[配置和使用](~/android/deploy-test/debugging/android-sdk-emulator/index.md)中所述，Android SDK 管理器将自动使用硬件加速，当它可用时，将专门为基于 x86 的虚拟设备运行仿真器映像。

## <a name="hyper-v-overview"></a>Hyper-V 概述

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](~/media/shared/preview.png)

> [!NOTE]
> Hyper-V 支持当前处于预览状态。

强烈建议使用 Windows 10（2018 年 4 月更新）的开发人员使用 Microsoft Hyper-V。 Visual Studio Tools for Xamarin 便于开发人员在无法使用 Android 设备的情况下测试和调试 Xamarin.Android 应用程序。

若要开始使用 Hyper-V 和 Google Android Emulator，请执行以下操作：

1. **更新至 Windows 10 2018 年 4 月更新（内部版本 1803）**&ndash; 若要确认正在运行的 Windows 版本，请在 Cortana 搜索栏中单击，然后键入“关于”。 在搜索结果中选择“关于你的电脑”。 在“关于”对话框中向下滚动，直到“Windows 规范”部分。 版本应至少为 1803：

    [![Windows 规范](hardware-acceleration-images/win/12-about-windows.w10-sml.png)](hardware-acceleration-images/win/12-about-windows.w10.png#lightbox)

2. **启用 Hyper-V 和 Windows 虚拟机监控程序平台** &ndash; 在 Cortana 搜索栏中，键入“打开或关闭 Windows 功能”。
   在“Windows 功能”对话框中向下滚动，确保启用了“Windows 虚拟机监控程序平台”。

    [![启用了 Hyper-V 和 Windows 虚拟机监控程序平台](hardware-acceleration-images/win/13-windows-features.w10-sml.png)](hardware-acceleration-images/win/13-windows-features.w10.png#lightbox)

    启用 Hyper-V 和 Windows 虚拟机监控程序平台后可能需要重启 Windows。

3. **安装 [Visual Studio 15.8 预览版 1 或更高版本](https://www.visualstudio.com/vs/preview/)** &ndash; 此 Visual Studio 版本通过 Hyper-V 支持提供用于开始使用 Google Android Emulator 的 IDE 支持。

4. **安装 Google Android Emulator 包 27.2.7 或更高版本** &ndash; 若要安装此包，请在 Visual Studio 中导航到“工具”>“Android”>“Android SDK 管理器”。 选择“工具”选项卡，确保 Android Emulator 组件的版本至少为 27.2.7。

    [![“Android SDK 和工具”对话框](hardware-acceleration-images/win/14-sdk-manager.w158-sml.png)](hardware-acceleration-images/win/14-sdk-manager.w158.png#lightbox)

5. 如果 Android Emulator 的版本低于 27.3.1，请应用“已知问题”（下一节）中介绍的其他变通方法。


### <a name="known-issues"></a>已知问题

-   如果仿真器的版本介于 27.2.7 和 27.3.1 之间，使用 Hyper-V 时需要使用以下变通方法：
    1.  在 C:\\Users\\username\\.android 文件夹中，创建名为“advancedFeatures.ini”的文件（如果不存在）。
    2.  将以下行添加到“advancedFeatures.ini”：
        ```
        WindowsHypervisorPlatform = on
        ```

-   使用某些 Intel 和基于 AMD 的处理器时，性能可能会降低。

-   部署时，Android 应用程序可能花费异常长的时间才能加载完成。

-   MMIO 访问错误可能会间歇性地阻止启动 Android Emulator。 重启仿真器应能够解决此问题。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Hyper-V 支持要求使用 Windows 10。 请参阅 [Hyper-V 要求](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v#check-requirements)获取详细信息。

-----

## <a name="haxm-overview"></a>HAXM 概述

HAXM 是硬件协助虚拟化引擎（虚拟机监控程序），使用 Intel 虚拟化技术 (VT) 在主机上加快执行 Android 应用仿真。 通过与 Intel 提供的 Android x86 仿真器映像以及官方 Android SDK 管理器配合使用，HAXM 可以在已启用 VT 的系统上加快执行 Android 仿真。 

如果开发计算机的 Intel CPU 具有 VT 功能，可以使用 HAXM 大幅加快 Google Android Emulator 的运行速度（如果不确定 CPU 是否支持 VT，请参阅[确定处理器是否支持 Intel 虚拟化技术](https://www.intel.com/content/www/us/en/support/processors/000005486.html)）。

> [!NOTE]
> 不可在另一 VM（例如由 VirtualBox、VMWare 或 Docker 托管的 VM）内运行经过 VM 加速的仿真器。 必须[直接在系统硬件上](https://developer.android.com/studio/run/emulator-acceleration.html#extensions)运行 Google Android 仿真器。

首次使用 Google Android Emulator 前，最好先验证 HAXM 是否已安装并能用于 Google Android Emulator。

### <a name="verifying-haxm-installation"></a>验证 HAXM 安装

可以在仿真器启动时查看“正在启动 Android 仿真器”窗口，从而确定 HAXM 是否可用。 若要启动 Google Android Emulator，请执行以下操作：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 依次单击“工具”>“Android”>“Android 仿真器管理器”，启动 Android 仿真器管理器：

    [![“Android 仿真器管理器”菜单项位置](hardware-acceleration-images/win/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/win/01-avd-manager-menu-item.png#lightbox)

2. 如果看到类似下面所示的“性能警告”对话框，则表示计算机上尚未安装或未正确配置 HAXM：

    ![提示 HAXM 未准备就绪的“性能警告”对话框](hardware-acceleration-images/win/11-perf-warn.png)

   如果出现了这样的“性能警告”对话框，请参阅[性能警告](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn)，确定原因并解决基本问题。

3. 选择 x86 映像（例如，“VisualStudio\_android-23\_x86\_phone”），再依次单击“开始”和“启动”：

    ![使用默认的虚拟设备映像启动 Google Android Emulator](hardware-acceleration-images/win/02-start-default-avd.png)

4. 在仿真器启动时，观察“正在启动 Android 仿真器”对话框窗口。 如果已安装 HAXM，则会看到内容为“HAX 正在运行，且仿真器在快速虚拟模式下运行”的消息，如下面的屏幕截图所示：

    ![HAXM 在“正在启动 Android 仿真器”对话框中显示为可用](hardware-acceleration-images/win/03-haxm-detected.png)

   如果未看到此消息，可能表明未安装 HAXM。 例如，下面的屏幕截图展示了在未安装 HAXM 时看到的消息：

    ![此计算机上未安装 HAXM](hardware-acceleration-images/win/04-haxm-error.png)

   如果计算机上未安装 HAXM，请按照下一部分中的步骤操作，从而安装 HAXM。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 依次单击“工具”>“Google 仿真器管理器”，启动 Android 仿真器管理器：

    [![“Android 仿真器管理器”菜单项位置](hardware-acceleration-images/mac/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/mac/01-avd-manager-menu-item.png#lightbox)

2. 如果看到类似下面所示的“性能警告”对话框，则表示计算机上尚未安装或未正确配置 HAXM：

    ![提示 HAXM 未准备就绪的“性能警告”对话框](hardware-acceleration-images/mac/04-avd-warning.png)

   如果出现了这样的“性能警告”对话框，请参阅[性能警告](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn)，确定原因并解决基本问题。

3. 选择 x86 映像（例如，Android\_Accelerated\_x86），单击“开始”，然后单击“启动”：

    [![使用默认的虚拟设备映像启动 Google Android Emulator](hardware-acceleration-images/mac/02-start-default-avd-sml.png)](hardware-acceleration-images/mac/02-start-default-avd.png#lightbox)

3. 在仿真器启动时，观察“正在启动 Android 仿真器”对话框窗口。 如果已安装 HAXM，则会看到内容为“HAX 正在运行，且仿真器在快速虚拟模式下运行”的消息，如下面的屏幕截图所示：

    ![HAXM 在“正在启动 Android 仿真器”对话框中显示为可用](hardware-acceleration-images/mac/03-haxm-detected.png)

   如果计算机上未安装 HAXM（例如，如果看到诸如“请确保 Intel HAXM 已正确安装且可用”之类的错误消息），请按照下一部分中的步骤操作，从而安装 HAXM。


-----

<a name="install-haxm" />

### <a name="installing-haxm"></a>安装 HAXM

如果仿真器未启动，可能需要手动安装 HAXM。 可以从 [Intel 硬件加速执行管理器](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager)网页下载适用于 Windows 和 macOS 的 HAXM 安装包。 若要手动下载并安装 HAXM，请按照下列步骤操作：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 从 Intel 网站下载适用于 Windows 的最新 [HAXM 虚拟化引擎](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/)安装程序。 直接从 Intel 网站下载 HAXM 安装程序的优点是可以确保使用最新版本。

   或者，可使用 SDK 管理器下载 HAXM 安装程序（在 SDK 管理器中依次单击“工具”>“附加程序”>“Intel x86 仿真器加速器(HAXM 安装程序)”）。 Android SDK 通常会将 HAXM 安装程序下载到以下位置：

   C:\\Program Files (x86)\\Android\\android-sdk\\extras\\intel\\Hardware\_Accelerated\_Execution\_Manager

   请注意，SDK 管理器不会安装 HAXM，它只是将 HAXM 安装程序下载到上述位置；仍需要手动启动安装程序。

2. 运行 intelhaxm android.exe，启动 HAXM 安装程序。 接受安装程序对话框中的默认值：

   ![“Intel 硬件加速执行管理器安装程序”窗口](hardware-acceleration-images/win/05-haxm-installer.png)

## <a name="hardware-acceleration-and-amd-cpus"></a>硬件加速和 AMD CPU

由于 Google Android 仿真器目前[仅在 Linux 上](https://developer.android.com/studio/run/emulator-acceleration.html#dependencies)支持 AMD 硬件加速，因此硬件加速不可用于运行 Windows 的基于 AMD 的计算机。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 从 Intel 网站下载适用于 macOS 的最新 [HAXM 虚拟化引擎](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/)安装程序。

2. 运行 HAXM 安装程序。 接受安装程序对话框中的默认值：

   [![“Intel 硬件加速执行管理器安装程序”窗口](hardware-acceleration-images/mac/05-haxm-installer-sml.png)](hardware-acceleration-images/win/05-haxm-installer.png#lightbox)

-----


## <a name="related-links"></a>相关链接

* [在 Android Emulator 上运行应用](https://developer.android.com/studio/run/emulator)
