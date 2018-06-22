---
title: CPU 体系结构
description: Xamarin.Android 支持多个 CPU 体系结构，包括 32 位和 64 位设备。 此文章介绍了如何应用定位到一个或多个 Android 支持 CPU 体系结构。
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: abfe22683de024f056d7798dc3ac2de13ebd953e
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2018
ms.locfileid: "34311684"
---
# <a name="cpu-architectures"></a>CPU 体系结构

_Xamarin.Android 支持多个 CPU 体系结构，包括 32 位和 64 位设备。此文章介绍了如何应用定位到一个或多个 Android 支持 CPU 体系结构。_

## <a name="cpu-architectures-overview"></a>CPU 体系结构概述

当你准备你的应用程序版本时，你必须指定哪些平台 CPU 体系结构你应用的支持。 单个 APK 可包含计算机代码，以支持多个不同的体系结构。 与关联的特定于体系结构的代码的每个集合*应用程序二进制接口*(ABI)。 每个 ABI 定义如何此机代码都应在运行时与 Android 进行交互。
有关此工作原理的详细信息，请参阅[多核设备&amp;Xamarin.Android](~/android/deploy-test/multicore-devices.md)。


## <a name="how-to-specify-supported-architectures"></a>如何指定支持的体系结构

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

通常情况下，你明确选择一种体系结构 （或体系结构） 在你的应用程序配置为**版本**。 在你的应用程序配置为**调试**、**使用共享运行时**和**使用快速部署**启用的选项，从而禁用显式体系结构选择。

在 Visual Studio 中，右键单击你的项目下**解决方案资源管理器**和选择**属性**。 下**Android 选项**页上，确保**打包属性**部分，并验证**使用共享运行时**处于禁用状态 （如果关闭此可以为显式选择以支持哪些 ABIs）。 单击**高级**按钮并在**支持的体系结构**，检查你想要支持的体系结构：

[![选择 armeabi 和 armeabi v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

通常情况下，你明确选择一种体系结构 （或体系结构） 在你的应用程序配置为**版本**。 在你的应用程序配置为**调试**、**使用共享单声道运行时**和**快速程序集部署**启用的选项，这阻止显式体系结构所选内容。

在适用于 Mac 的 Visual Studio，找到你的项目中**解决方案**填充，单击你的项目旁边的齿轮图标并选择**选项**。 在**项目选项**对话框中，单击**Android 生成**。 单击**常规**选项卡上，并验证**使用共享单声道运行时**处于禁用状态 （如果关闭此允许你显式选择哪些 ABIs 以支持）。 单击**高级**选项卡并在**支持 ABIs**，检查你想要支持的体系结构 ABIs:

[![选择 armeabi 和 armeabi v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


Xamarin.Android 支持以下体系结构：

-   **armeabi** &ndash;至少支持 ARMv5TE 指令集的基于 ARM 的 Cpu。 请注意，`armeabi`不是线程安全的不应在多 CPU 设备上使用。

-   **armeabi v7a** &ndash;具有硬件浮点运算和多个 CPU (SMP) 设备的基于 ARM 的 Cpu。 请注意，`armeabi-v7a`机器代码将不会在 ARMv5 设备上运行。

-   **arm64 v8a** &ndash;基于在 64 位 ARMv8 体系结构上的 Cpu。

-   **x86** &ndash;支持 x86 （或 ia-32） 的 Cpu 指令集。 此指令集相当于的 Pentium Pro，包括 MMX、 SSE、 SSE2 和 SSE3 说明。

-   **x86_64**支持 64 位 x86 的 Cpu (也称为*x64*和*AMD64*) 指令集。

Xamarin.Android 默认为`armeabi-v7a`为**版本**生成。 此设置可以提供性能明显优于`armeabi`。 如果你正面向 64 位的 ARM 平台 （如 Nexus 9 中)，选择`arm64-v8a`。 如果你将您的应用程序部署到 x86 设备中，选择`x86`。 如果面向 x86 设备使用 64 位 CPU 体系结构，选择`x86_64`。

## <a name="targeting-multiple-platforms"></a>面向多个平台

若要针对多个 CPU 体系结构，你可以选择多个 ABI （对应的代价是更大 APK 文件大小）。 你可以使用**生成一个包 (.apk) 每个所选 ABI**选项 (中所述[设置包属性](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) 以创建每个单独 APK 支持的体系结构。

无需选择**arm64 v8a**或**x86_64**面向 64 位设备; 64 位支持不需要在 64 位硬件上运行你的应用程序。 例如，64 位 ARM 设备 (如[Nexus 9](http://www.google.com/nexus/9/)) 可以运行的应用配置为`armeabi-v7a`。 启用 64 位支持的主要优点是使你的应用以处理更多内存。

> [!NOTE]
> 64 位运行时支持目前试验性功能。 请记住，64 位运行时是*不*64 位设备上运行你的应用程序所必需。 

## <a name="additional-information"></a>其他信息

在某些情况下，你可能需要创建每个体系结构单独 APK (可减小你 APK，或者是因为你的应用程序已共享特定于特定的 CPU 体系结构的库)。
有关这种方法的详细信息，请参阅[生成 ABI 特定 APKs](~/android/deploy-test/building-apps/abi-specific-apks.md)。
