---
title: "Android 仿真器硬件加速"
description: "如何准备计算机以获得 Android SDK 仿真器的最佳性能"
ms.topic: article
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 12/22/2017
ms.openlocfilehash: 7560900ace62a737ac765bcfe93f759f8985aca2
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="android-emulator-hardware-acceleration"></a>Android 仿真器硬件加速

因为如果不进行硬件加速，Android SDK 仿真器的运行速度会过于缓慢，所以推荐使用 Intel 的 HAXM（硬件加速执行管理器）大幅提升 Android SDK 仿真器的性能。


## <a name="haxm-overview"></a>HAXM 概述

HAXM 是硬件协助虚拟化引擎（虚拟机监控程序），使用 Intel 虚拟化技术 (VT) 在主机上加快执行 Android 应用仿真。 通过与 Intel 提供的 Android x86 仿真器映像以及官方 Android SDK 管理器配合使用，HAXM 可以在已启用 VT 的系统上加快执行 Android 仿真。 如果开发计算机的 Intel CPU 具有 VT 功能，可以使用 HAXM 大幅加快 Android SDK 仿真器的运行速度（如果不确定 CPU 是否支持 VT，请参阅[确定处理器是否支持 Intel 虚拟化技术](https://www.intel.com/content/www/us/en/support/processors/000005486.html)）。

Android SDK 仿真器会自动使用可用的 HAXM。 选择基于 x86 的虚拟设备（如[配置和使用](~/android/deploy-test/debugging/android-sdk-emulator/index.md)中所述）后，该虚拟设备将使用 HAXM 来执行硬件加速。 首次使用 Android SDK 仿真器前，最好先验证 HAXM 是否已安装，且能否用于 Android SDK 仿真器。

> [!NOTE]
> 无法在虚拟机上运行 HAXM。


## <a name="verifying-haxm-installation"></a>验证 HAXM 安装

可以在仿真器启动时查看“正在启动 Android 仿真器”窗口，从而确定 HAXM 是否可用。 若要启动 Android SDK 仿真器，请执行以下操作：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 依次单击“工具”>“Android”>“Android 仿真器管理器”，启动 Android 仿真器管理器：

    [![“Android 仿真器管理器”菜单项位置](hardware-acceleration-images/win/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/win/01-avd-manager-menu-item.png#lightbox)

2. 如果看到类似下面所示的“性能警告”对话框，则表示计算机上尚未安装或未正确配置 HAXM：

    ![提示 HAXM 未准备就绪的“性能警告”对话框](hardware-acceleration-images/win/11-perf-warn.png)

   如果出现了这样的“性能警告”对话框，请参阅[性能警告](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn)，确定原因并解决基本问题。

3. 选择 x86 映像（例如，“VisualStudio\_android-23\_x86\_phone”），再依次单击“开始”和“启动”：

    ![使用默认的虚拟设备映像启动 Android SDK 仿真器](hardware-acceleration-images/win/02-start-default-avd.png)

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

    [使用默认的虚拟设备映像启动 Android SDK 仿真器![](hardware-acceleration-images/mac/02-start-default-avd-sml.png)](hardware-acceleration-images/mac/02-start-default-avd.png#lightbox)

3. 在仿真器启动时，观察“正在启动 Android 仿真器”对话框窗口。 如果已安装 HAXM，则会看到内容为“HAX 正在运行，且仿真器在快速虚拟模式下运行”的消息，如下面的屏幕截图所示：

    ![HAXM 在“正在启动 Android 仿真器”对话框中显示为可用](hardware-acceleration-images/mac/03-haxm-detected.png)

   如果计算机上未安装 HAXM（例如，如果看到诸如“请确保 Intel HAXM 已正确安装且可用”之类的错误消息），请按照下一部分中的步骤操作，从而安装 HAXM。


-----

<a name="install-haxm" />

## <a name="installing-haxm"></a>安装 HAXM

如果仿真器未启动，可能需要手动安装 HAXM。 可以从 [Intel 硬件加速执行管理器](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager)网页下载适用于 Windows 和 macOS 的 HAXM 安装包。 若要手动下载并安装 HAXM，请按照下列步骤操作：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 从 Intel 网站下载适用于 Windows 的最新 [HAXM 虚拟化引擎](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/)安装程序。 直接从 Intel 网站下载 HAXM 安装程序的优点是可以确保使用最新版本。

   或者，可使用 SDK 管理器下载 HAXM 安装程序（在 SDK 管理器中依次单击“工具”>“附加程序”>“Intel x86 仿真器加速器(HAXM 安装程序)”）。 Android SDK 通常会将 HAXM 安装程序下载到以下位置：

   C:\\Program Files (x86)\\Android\\android-sdk\\extras\\intel\\Hardware\_Accelerated\_Execution\_Manager

   请注意，SDK 管理器不会安装 HAXM，它只是将 HAXM 安装程序下载到上述位置；仍需要手动启动安装程序。

2. 运行 intelhaxm android.exe，启动 HAXM 安装程序。 接受安装程序对话框中的默认值：

   ![“Intel 硬件加速执行管理器安装程序”窗口](hardware-acceleration-images/win/05-haxm-installer.png)

如果看到以下错误对话框（“此计算机不支持 Intel 虚拟化技术(VT-x)，或正在被 Hyper-V 独占使用”），必须先禁用 Hyper-V，然后才能安装 HAXM：

![由于出现 Hyper-V 冲突，无法安装 HAXM](hardware-acceleration-images/win/06-cant-install-haxm.png)

下一部分将介绍如何禁用 Hyper-V。

<a name="disable-hyperv" />

## <a name="disabling-hyper-v"></a>禁用 Hyper-V

如果使用已启用 Hyper-V 的 Windows，必须首先将它禁用并重启计算机才能安装和使用 HAXM。 可以按照下列步骤操作，在“控制面板”中禁用 Hyper-V：

1. 在 Windows 搜索框中输入“程序”，再单击“程序和功能”搜索结果。

2. 在“控制面板”的“程序和功能”对话框中，单击“打开或关闭 Windows 功能”：

    ![打开或关闭 Windows 功能](hardware-acceleration-images/win/07-turn-windows-features.png)

3. 取消选中“Hyper-V”，再重启计算机：

    ![在“Windows 功能”对话框中禁用 Hyper-V](hardware-acceleration-images/win/08-uncheck-hyper-v.png)

也可以使用下列 Powershell cmdlet 禁用 Hyper-V：

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

Intel HAXM 和 Microsoft Hyper-V 不能同时处于活动状态。 遗憾的是，目前尚没有办法在不重启计算机的情况下在 Hyper-V 和 HAXM 之间切换。 若要使用 [Visual Studio Android 仿真器](~/android/deploy-test/debugging/visual-studio-android-emulator.md)（依赖 Hyper-V），如果不重启，将无法使用 Android SDK 仿真器。 同时使用 Hyper-V 和 HAXM 的一种方法是创建多重引导安装程序，如[不创建虚拟机监控程序启动项](https://blogs.msdn.microsoft.com/virtual_pc_guy/2008/04/14/creating-a-no-hypervisor-boot-entry/)中所述。

在某些情况下，如果启用了 Device Guard 和 Credential Guard，按照前述步骤操作将无法成功禁用 Hyper-V。 如果无法禁用 Hyper-V（或似乎已禁用，但仍无法安装 HAXM），请按照下一部分中的步骤操作，禁用 Device Guard 和 Credential Guard。

<a name="disable-devguard" />

## <a name="disabling-device-guard"></a>禁用 Device Guard

Device Guard 和 Credential Guard 可阻止在 Windows 计算机上禁用 Hyper-V。 对于由负责组织配置和控制的域加入计算机而言，这通常都是一个需要解决的问题。
在 Windows 10 上，请按照下列步骤操作，检查 Device Guard 是否在运行：

1. 在“Windows Search”中，键入“系统信息”，启动“系统信息”应用。

2. 在“系统摘要”中，检查是否有“基于 Device Guard 虚拟化的安全性”；若有，检查是否处于“正在运行”状态：

   [![Device Guard 存在且正在运行](hardware-acceleration-images/win/09-device-guard-sml.png)](hardware-acceleration-images/win/09-device-guard.png#lightbox)

如果已启用 Device Guard，请按照下列步骤操作，禁用 Device Guard：

1. 确保已按照上一部分所述禁用“Hyper-V”（在“打开或关闭 Windows 功能”下）。

2. 在 Windows 搜索框中，键入“gpedit”，再选择“编辑组策略”搜索结果。 这会启动“本地组策略编辑器”。

3. 在“本地组策略编辑器”中，依次转到“计算机配置”>“管理模板”>“系统”>“Device Guard”：

   [![“本地组策略编辑器”中的“Device Guard”](hardware-acceleration-images/win/10-group-policy-editor-sml.png)](hardware-acceleration-images/win/10-group-policy-editor.png#lightbox)

4. 将“打开基于虚拟化的安全性”更改为“已禁用”（如上所示），再退出“本地组策略编辑器”。

5. 在 Windows 搜索框中，键入“cmd”。 右键单击搜索结果中的“命令提示符”，再选择“以管理员身份运行”。

6. 将以下命令复制并粘贴到命令提示符窗口（如果正在使用驱动器 Z:，请改为选择未使用的驱动器号）：

        mountvol Z: /s
        copy %WINDIR%\System32\SecConfig.efi Z:\EFI\Microsoft\Boot\SecConfig.efi /Y
        bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
        bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=Z:
        mountvol Z: /d

7. 重新启动计算机。 在启动屏幕上，应该会看到以下提示：

   **是否要禁用 Credential Guard?**

   按下指示的键，以根据提示禁用 Credential Guard。

8. 重启计算机后，再次检查，以确保 Hyper-V 已禁用（如前述步骤所述）。

如果 Hyper-V 仍未禁用，域加入计算机的策略可能会阻止禁用 Device Guard 或 Credential Guard。 在这种情况下，可以向域管理员申请豁免，以便能够选择禁用 Credential Guard。 此外，还可以使用未加入域的计算机来使用 HAXM。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 从 Intel 网站下载适用于 macOS 的最新 [HAXM 虚拟化引擎](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/)安装程序。

2. 运行 HAXM 安装程序。 接受安装程序对话框中的默认值：

   [![“Intel 硬件加速执行管理器安装程序”窗口](hardware-acceleration-images/mac/05-haxm-installer-sml.png)](hardware-acceleration-images/win/05-haxm-installer.png#lightbox)

-----
