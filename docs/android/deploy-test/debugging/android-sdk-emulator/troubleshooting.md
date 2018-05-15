---
title: Google Android Emulator 故障排除
description: 如何识别和解决 Google Android Emulator 问题
ms.prod: xamarin
ms.assetid: 4B05C3C5-E1F6-47A9-B098-C31E630194F6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/04/2018
ms.openlocfilehash: 001fc21a519a251715d24b43acfdd4251b5fbc91
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="google-android-emulator-troubleshooting"></a>Google Android Emulator 故障排除

本文介绍 Google Android Emulator（及其解决方案）相关的常见警告消息和问题。
 
<a name="perfwarn" />

## <a name="performance-warnings"></a>性能警告

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

从 Visual Studio 2017 版本 15.4 开始，首次将应用部署到 Android SDK 仿真器时，可能会看到性能警告对话框。 下面介绍了这些警告对话框。

### <a name="computer-does-not-contain-an-intel-procesor"></a>计算机不具有 Intel 处理器

![计算机不具有 Intel 处理器](troubleshooting-images/01-no-intel-processor.png)

如果出现此对话框，则表示计算机没有加速 Android SDK 仿真器所必需的 Intel 处理器。 如果计算机没有 Intel 处理器，建议使用 Android 物理设备进行开发。

### <a name="hyper-v-is-installed-or-active"></a>Hyper V 已安装或处于活动状态

![Hyper V 已安装或处于活动状态](troubleshooting-images/02-hyper-v-active.png)

如果出现此对话框，则表示 Hyper-V 已安装或处于活动状态，必须将其禁用。 [禁用 Hyper-V](#disable-hyperv) 介绍如何解决此问题。

### <a name="haxm-is-not-installed"></a>未安装 HAXM

![未安装 HAXM](troubleshooting-images/03-haxm-not-installed.png)

此对话框表示计算机具有 Intel 处理器且已禁用 Hyper-V，但未安装 HAXM。
[安装 HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) 介绍如何安装 HAXM。

### <a name="haxm-process-not-running"></a>HAXM 进程未运行

![HAXM 进程未运行](troubleshooting-images/04-haxm-process-not-running.png)

如果计算机具有 Intel 处理器、已禁用 Hyper-V 并且安装了 Intel HAXM，但 HAXM 进程未运行，则会出现此对话框。 若要解决此问题，请打开命令提示符窗口，然后输入以下命令：

```cmd
sc query intelhaxm
```

如果 HAXM 进程正在运行，应看到类似于下面的输出：

```cmd
SERVICE_NAME: intelhaxm
    TYPE               : 1  KERNEL_DRIVER
    STATE              : 4  RUNNING
                            (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
    WIN32_EXIT_CODE    : 0  (0x0)
    SERVICE_EXIT_CODE  : 0  (0x0)
    CHECKPOINT         : 0x0
    WAIT_HINT          : 0x0
```


如果“状态”未设置为“正在运行”，请参阅[如何使用 Intel 硬件加速执行管理器](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator)，解决该问题。


### <a name="other-failures"></a>其他故障

![其他故障](troubleshooting-images/05-other-failure.png)

如果计算机具有 Intel 处理器、已禁用 Hyper-V 、安装了 Intel HAXM 且 HAXM 进程正在运行，但仿真器因某些未知原因无法启动，则会出现此对话框。
若要帮助解决此错误，请参阅[如何使用 Intel 硬件加速执行管理器](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator)。

### <a name="disabling-performance-warnings"></a>禁用性能警告

如果不希望看到性能警告，可将其禁用。 在 Visual Studio 中，依次单击“工具”>“选项”>“Xamarin”>“Android 设置”然后禁用“如果不支持 AVD 加速(HAXM)，则发出警告”选项：

[![禁用 AVD 加速警告](troubleshooting-images/win/06-disable-perf-warnings-sml.png)](troubleshooting-images/win/06-disable-perf-warnings.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

从 Visual Studio for Mac 版本 7.2（内部版本 559）开始，首次将应用部署到 Google Android Emulator 时，可能会看到性能警告对话框。 下面介绍了这些警告对话框。

### <a name="haxm-is-not-installed"></a>未安装 HAXM

![未安装 HAXM](troubleshooting-images/03-haxm-not-installed.png)

此对话框指示未安装 HAXM。
[安装 HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) 介绍如何安装 HAXM。

### <a name="haxm-process-not-running"></a>HAXM 进程未运行

![HAXM 进程未运行](troubleshooting-images/04-haxm-process-not-running.png)

如果 HAXM 进程未运行，则会显示此对话框。 有关帮助解决此问题的详细信息，请参阅[如何使用 Intel 硬件加速执行管理器](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator)，解决该问题。

### <a name="other-failures"></a>其他故障

![其他故障](troubleshooting-images/05-other-failure.png)

如果仿真器因某种未知原因无法启动，则会显示此对话框。 若要帮助解决此错误，请参阅[如何使用 Intel 硬件加速执行管理器](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator)，解决该问题。

-----


## <a name="solutions-to-common-problems"></a>常见问题解决方案

通过对计算机进行配置更改或安装其他软件，可解决许多常见的 Google Android Emulator 问题。 以下各部分介绍这些问题并提供解决方案。


### <a name="deployment-issues"></a>部署问题

如果看到有关无法在仿真器上安装 APK 或无法运行 Android Debug Bridge (adb) 的错误消息，请验证 Android SDK 能否连接仿真器。 为此，请安装下列步骤操作：

1. 通过“Android 虚拟设备(AVD)管理器”启动仿真器（选择虚拟设备并单击“启动”）。

2. 打开命令提示符，转到 adb 的安装文件夹。 例如，在 Windows 上，位置可能是 C:\\Program Files (x86)\\Android\\android-sdk\\platform-tools\\adb.exe。

3. 键入以下命令：

   ```shell
   adb devices
   ```

4. 如果可以通过 Android SDK 访问仿真器，那么仿真器应该就显示在附加设备列表中。 例如:

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. 如果仿真器不在此列表中，请启动“Android SDK 管理器”，应用所有更新，再尝试重启仿真器。



### <a name="haxm-issues"></a>HAXM 问题

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

如果 Google Android Emulator 无法正常启动，这通常是由 HAXM 问题所致。 导致 HAXM 问题出现的原因通常包括：与其他虚拟化技术冲突、设置不正确或 HAXM 驱动器不是最新版本。

<a name="virt-conflicts" />

#### <a name="haxm-virtualization-conflicts"></a>HAXM 虚拟化冲突

HAXM 可能与其他使用虚拟化的技术（如 Hyper-V、Windows Device Guard 和某防病毒软件）冲突：

- **Hyper-V** &ndash; 如果使用的 Windows 已启用 Hyper-V，请按照[禁用 Hyper-V](#disable-hyperv) 中介绍的步骤操作。

- Device Guard &ndash; Device Guard 和 Credential Guard 可阻止在 Windows 计算机上禁用 Hyper-V。 若要禁用 Device Guard 和 Credential Guard，请参阅[禁用 Device Guard](#disable-devguard)。

- **防病毒软件** &ndash; 如果运行的防病毒软件（如 Avast）使用硬件协助虚拟化，请禁用或卸载此软件，再重启并重试运行 Android SDK 仿真器。


#### <a name="incorrect-bios-settings"></a>BIOS 设置不正确

若要在 Windows PC 上使用 HAXM，只有在 BIOS 中启用虚拟化技术 (Intel VT-x)，HAXM 才能正常运行。 如果已禁用 VT-x，则在尝试启用 Google Android Emulator 时会看到如下错误消息：

**此计算机符合 HAXM 要求，但未启用 Intel 虚拟化技术 (VT-x)。**

若要更正此错误，请将计算机引导到 BIOS，同时启用 VT-x 和 SLAT（第二级地址转换），再重启计算机，以返回到 Windows。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

如果 Google Android Emulator 无法正常启动，这通常是由 HAXM 问题所致。 导致 HAXM 问题出现的原因通常包括：与其他虚拟化技术冲突、设置不正确或 HAXM 驱动器不是最新版本。 按照[安装 HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) 中介绍的步骤，尝试重新安装 HAXM 驱动程序。

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

<a name="disable-hyperv" />

### <a name="disabling-hyper-v"></a>禁用 Hyper-V

如果使用已启用 Hyper-V 的 Windows，必须首先将它禁用并重启计算机才能安装和使用 HAXM。 可以按照下列步骤操作，在“控制面板”中禁用 Hyper-V：

1. 在 Windows 搜索框中输入“程序”，再单击“程序和功能”搜索结果。

2. 在“控制面板”的“程序和功能”对话框中，单击“打开或关闭 Windows 功能”：

    ![打开或关闭 Windows 功能](troubleshooting-images/win/07-turn-windows-features.png)

3. 取消选中“Hyper-V”，再重启计算机：

    ![在“Windows 功能”对话框中禁用 Hyper-V](troubleshooting-images/win/08-uncheck-hyper-v.png)

也可以使用下列 Powershell cmdlet 禁用 Hyper-V：

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

Intel HAXM 和 Microsoft Hyper-V 不能同时处于活动状态。 遗憾的是，目前尚没有办法在不重启计算机的情况下在 Hyper-V 和 HAXM 之间切换。 若要使用 [Visual Studio Android 仿真器](~/android/deploy-test/debugging/visual-studio-android-emulator.md)（依赖 Hyper-V），如果不重启，将无法使用 Android SDK 仿真器。 同时使用 Hyper-V 和 HAXM 的一种方法是创建多重引导安装程序，如[不创建虚拟机监控程序启动项](https://blogs.msdn.microsoft.com/virtual_pc_guy/2008/04/14/creating-a-no-hypervisor-boot-entry/)中所述。

在某些情况下，如果启用了 Device Guard 和 Credential Guard，按照前述步骤操作将无法成功禁用 Hyper-V。 如果无法禁用 Hyper-V（或似乎已禁用，但仍无法安装 HAXM），请按照下一部分中的步骤操作，禁用 Device Guard 和 Credential Guard。

<a name="disable-devguard" />

#### <a name="disabling-device-guard"></a>禁用 Device Guard

Device Guard 和 Credential Guard 可阻止在 Windows 计算机上禁用 Hyper-V。 对于由负责组织配置和控制的域加入计算机而言，这通常都是一个需要解决的问题。
在 Windows 10 上，请按照下列步骤操作，检查 Device Guard 是否在运行：

1. 在“Windows Search”中，键入“系统信息”，启动“系统信息”应用。

2. 在“系统摘要”中，检查是否有“基于 Device Guard 虚拟化的安全性”；若有，检查是否处于“正在运行”状态：

   [![Device Guard 存在且正在运行](troubleshooting-images/win/09-device-guard-sml.png)](troubleshooting-images/win/09-device-guard.png#lightbox)

如果已启用 Device Guard，请按照下列步骤操作，禁用 Device Guard：

1. 确保已按照上一部分所述禁用“Hyper-V”（在“打开或关闭 Windows 功能”下）。

2. 在 Windows 搜索框中，键入“gpedit”，再选择“编辑组策略”搜索结果。 这会启动“本地组策略编辑器”。

3. 在“本地组策略编辑器”中，依次转到“计算机配置”>“管理模板”>“系统”>“Device Guard”：

   [![“本地组策略编辑器”中的“Device Guard”](troubleshooting-images/win/10-group-policy-editor-sml.png)](troubleshooting-images/win/10-group-policy-editor.png#lightbox)

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


# <a name="visual-studiotabvsmac"></a>[Visual Studio](#tab/vsmac)

Hyper-V 不可用于 OS X 或 macOS。

-----

