---
title: Android SDK 仿真器故障排除
description: 如何识别和解决 Android SDK 仿真器问题。
ms.prod: xamarin
ms.assetid: 4B05C3C5-E1F6-47A9-B098-C31E630194F6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: b3e55e02d27307dbcef8b6a62b2da368cd0201f3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="android-sdk-emulator-troubleshooting"></a>Android SDK 仿真器故障排除

本文介绍 Android SDK 仿真器（及其解决方案）相关的常见警告消息和问题。
 
<a name="perfwarn" />

## <a name="performance-warnings"></a>性能警告

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

从 Visual Studio 2017 版本 15.4 开始，首次将应用部署到 Android SDK 仿真器时，可能会看到性能警告对话框。 下面介绍了这些警告对话框。

### <a name="computer-does-not-contain-an-intel-procesor"></a>计算机不具有 Intel 处理器

![计算机不具有 Intel 处理器](troubleshooting-images/01-no-intel-processor.png)

如果出现此对话框，则表示计算机没有加速 Android SDK 仿真器所必需的 Intel 处理器。 如果计算机没有 Intel 处理器，建议使用 Android 物理设备进行开发。

### <a name="hyper-v-is-installed-or-active"></a>Hyper V 已安装或处于活动状态

![Hyper V 已安装或处于活动状态](troubleshooting-images/02-hyper-v-active.png)

如果出现此对话框，则表示 Hyper-V 已安装或处于活动状态，必须将其禁用。 [禁用 Hyper-V](~/android/get-started/installation/android-emulator/hardware-acceleration.md#disable-hyperv) 介绍如何解决此问题。 

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

从 Visual Studio for Mac 版本 7.2（内部版本 559）开始，首次将应用部署到 Android SDK 仿真器时，可能会看到性能警告对话框。 下面介绍了这些警告对话框。

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

通过对计算机进行配置更改或安装其他软件，可解决许多常见的 Android SDK 仿真器问题。 以下各部分介绍这些问题并提供解决方案。


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

如果 Android SDK 仿真器无法正常启动，这通常是由 HAXM 问题所致。 导致 HAXM 问题出现的原因通常包括：与其他虚拟化技术冲突、设置不正确或 HAXM 驱动器不是最新版本。

<a name="virt-conflicts" />

#### <a name="haxm-virtualization-conflicts"></a>HAXM 虚拟化冲突

HAXM 可能与其他使用虚拟化的技术（如 Hyper-V、Windows Device Guard 和某防病毒软件）冲突：

- **Hyper-V** &ndash; 如果使用的 Windows 已启用 Hyper-V，请按照[禁用 Hyper-V](~/android/get-started/installation/android-emulator/hardware-acceleration.md#disable-hyperv) 中介绍的步骤操作。

- Device Guard &ndash; Device Guard 和 Credential Guard 可阻止在 Windows 计算机上禁用 Hyper-V。 若要禁用 Device Guard 和 Credential Guard，请参阅[禁用 Device Guard](~/android/get-started/installation/android-emulator/hardware-acceleration.md#disable-devguard)。

- **防病毒软件** &ndash; 如果运行的防病毒软件（如 Avast）使用硬件协助虚拟化，请禁用或卸载此软件，再重启并重试运行 Android SDK 仿真器。


#### <a name="incorrect-bios-settings"></a>BIOS 设置不正确

若要在 Windows PC 上使用 HAXM，只有在 BIOS 中启用虚拟化技术 (Intel VT-x)，HAXM 才能正常运行。 如果已禁用 VT-x ，则会尝试启用 Android SDK 仿真器时看到如下错误消息：

**此计算机符合 HAXM 要求，但未启用 Intel 虚拟化技术 (VT-x)。**

若要更正此错误，请将计算机引导到 BIOS，同时启用 VT-x 和 SLAT（第二级地址转换），再重启计算机，以返回到 Windows。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

如果 Android SDK 仿真器无法正常启动，这通常是由 HAXM 问题所致。 导致 HAXM 问题出现的原因通常包括：与其他虚拟化技术冲突、设置不正确或 HAXM 驱动器不是最新版本。 按照[安装 HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) 中介绍的步骤，尝试重新安装 HAXM 驱动程序。

-----
