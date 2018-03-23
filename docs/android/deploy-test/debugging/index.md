---
title: 调试
description: 如何测试和调试 Xamarin.Android 应用
ms.topic: article
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/16/2018
ms.openlocfilehash: 61d7bb1b84b225146c5ebc67e9dc89b2b17a8c21
ms.sourcegitcommit: cc38757f56aab53bce200e40f873eb8d0e5393c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2018
---
# <a name="debugging"></a>调试

## <a name="debugging-overview"></a>调试概述

开发 Android 应用程序需要在物理硬件上或者使用仿真器或模拟器运行应用程序。 使用硬件是最好的方法，但并不总是最实用的方法。 在许多情况下，使用如下所述的仿真器之一模拟/仿真 Android 硬件，这可简化操作且更具成本效益。


### <a name="android-sdk-emulatorandroiddeploy-testdebuggingandroid-sdk-emulatorindexmd"></a>[Android SDK 仿真器](~/android/deploy-test/debugging/android-sdk-emulator/index.md)

这些文章介绍如何使用随 Android SDK 提供的默认仿真器。 此仿真器适用于 Visual Studio for Windows 和 Visual Studio for Mac。

### <a name="visual-studio-android-emulatorandroiddeploy-testdebuggingvisual-studio-android-emulatormd"></a>[Visual Studio Android Emulator](~/android/deploy-test/debugging/visual-studio-android-emulator.md)

本文介绍如何使用 Visual Studio 2015 中内置的 Android 仿真器来调试和测试 Xamarin.Android 应用。 如果使用 Visual Studio 2015 并且不需要自定义设备配置文件，则此仿真器是个不错的选择。

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[在设备上进行调试](~/android/deploy-test/debugging/debug-on-device.md)

本文介绍如何配置物理 Android 设备，以便可以直接从Visual Studio 或 Visual Studio for Mac 将 Xamarin.Android 应用程序部署到此设备。

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)

开发人员用于调试应用程序的一个非常常见的技巧是使用 `Console.WriteLine`。 但是，在移动平台（如 Android）上没有控制台。 Android 设备会提供日志，你在编写应用时可能需要利用它。 这有时称为 **logcat**，原因在于为检索它而输入的命令。 本文介绍如何使用 **logcat**。

> [!WARNING]
> 请注意，**Xamarin Android Player** 已弃用。 有关详细信息，请参阅[此博客文章中的公告](https://blog.xamarin.com/live-from-dotnetconf-cycle-7-xamarin-studio-6-and-more/)。
