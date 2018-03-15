---
title: "Android 仿真器设置"
description: "本部分介绍如何准备好 Android SDK 仿真器以测试应用。 其中介绍了如何加速仿真器以获得最佳性能，并且演示了如何使用仿真器管理器创建和自定义虚拟设备。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/25/2018
ms.openlocfilehash: 55f5cf22718713fdcf11c49e0993f47c2f5a6f1d
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="android-emulator-setup"></a>Android 仿真器设置

_本部分介绍如何准备好 Android SDK 仿真器以测试应用。其中介绍了如何加速仿真器以获得最佳性能，并且演示了如何使用仿真器管理器创建和自定义虚拟设备。_


## <a name="overview"></a>概述

可使用各种配置运行 Google Android SDK 仿真器来模拟不同的设备。 其中的每个配置都创建为虚拟设备。 本指南介绍如何加速 Android 仿真器以获得更佳的性能，以及使用 Xamarin Android 仿真器管理器或旧版 Google 仿真器管理器以创建虚拟设备。


> [!NOTE]
> 自 Android SDK 工具版本 26.0.1 和更高版本开始，Google 已删除了对现有 AVD/SDK 管理器的支持，以支持其新的 CLI（命令行接口）工具。 由于此弃用更改，因此现在将 Xamarin SDK/设备管理器用于 Android 工具 26.0.1 和更高版本，而不使用 Google SDK/仿真器管理器。 （有关 Xamarin SDK 管理器的详细信息，请参阅 [Android SDK 安装](~/android/get-started/installation/android-sdk.md)）。


## <a name="sections"></a>部分

### <a name="hardware-accelerationandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[硬件加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

如何准备计算机以获得 Android SDK 仿真器的最佳性能。 由于在没有硬件加速的情况下 Android SDK 仿真器的运行可能会极度缓慢，因此，建议在使用 Android SDK 仿真器之前在开发计算机上启用硬件加速。

### <a name="xamarin-android-device-managerandroidget-startedinstallationandroid-emulatorxamarin-device-managermd"></a>[Xamarin Android 设备管理器](~/android/get-started/installation/android-emulator/xamarin-device-manager.md)

如何使用 Xamarin Android 设备管理器创建和自定义 Android SDK 仿真器虚拟设备。 **Xamarin Android 设备管理器**（当前提供预览版）用于取代旧版 Google 仿真器管理器。 如果你面向的是 Android Oreo 8.0 或更高版本，则必须使用 Xamarin Android 设备管理器，而不是 Google 仿真器管理器。

### <a name="google-emulator-managerandroidget-startedinstallationandroid-emulatorgoogle-emulator-managermd"></a>[Google 仿真器管理器](~/android/get-started/installation/android-emulator/google-emulator-manager.md)

如何使用旧版 Google 仿真器管理器创建和自定义 Android SDK 仿真器虚拟设备。 可以通过保留 Android SDK Tools 版本 25.2.5 或更低版本以继续运行具有原始 Google 仿真器管理器的 Google Android 仿真器。

配置 Android SDK 仿真器后，有关如何启动仿真器以及如何使用它测试并调试应用的信息，请参阅 [Android SDK Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md)。
