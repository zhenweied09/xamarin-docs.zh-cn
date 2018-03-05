---
title: "Windows 安装"
description: "本指南介绍了在 Windows 上安装 Xamarin.Android for Visual Studio 的步骤，并介绍了如何配置 Xamarin.Android 来生成你的第一个 Xamarin.Android 应用程序。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2BE4D5AD-D468-B177-8F96-837D084E7DE1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 10cfb790fca30b67faaa8f7720a358ec34c25663
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="windows-installation"></a>Windows 安装

_本指南介绍了在 Windows 上安装 Xamarin.Android for Visual Studio 的步骤，并介绍了如何配置 Xamarin.Android 来生成你的第一个 Xamarin.Android 应用程序。_

<a name="overview" />

## <a name="overview"></a>概述

现在，所有版本的 Visual Studio 中都免费附带 Xamarin，并且不需要单独的许可证，可使用 Visual Studio 安装程序下载和安装 Xamarin.Android 工具。
（不再需要早期版本的 Xamarin.Android 所需的手动安装和许可步骤。）本指南将介绍以下内容：

-   如何为 Java 开发工具包、Android SDK 和 Android NDK 配置自定义位置。

-   如何启动 Android SDK 管理器，下载并安装其他 Android SDK 组件。

-   如何准备 Android 设备或仿真器进行调试和测试。

-   如何创建第一个 Xamarin.Android 应用项目。

本指南结束时，你需要将一个有效的 Xamarin.Android 安装集成到 Visual Studio 中，并且准备好开始生成你们的第一个 Xamarin.Android 应用程序。

## <a name="installation"></a>安装

有关安装与 Windows 上的 Visual Studio 配合使用的 Xamarin 的详细信息，请参阅 [Windows 安装](~/cross-platform/get-started/installation/windows.md)指南。

<a name="configuration" />

## <a name="configuration"></a>配置

Xamarin.Android 使用 Java 开发工具包 (JDK) 和 Android SDK 生成应用。 在安装过程中，Visual Studio 安装程序会将这些工具放置在其默认位置，并使用适当的路径配置来配置开发环境。 可单击“工具”>“选项”>“Xamarin”>“Android 设置”查看和更改这些位置：

![Xamarin Android 设置对话框的屏幕截图](windows-images/07-settings.png)

对于大多数用户，默认位置会起作用，无需进行进一步更改。 但是，你可能希望将 Visual Studio 配置为这些工具的自定义位置（例如，如果你已在其他位置安装了 Java JDK、Android SDK 或 NDK）。 单击要更改的路径旁边的“更改”，然后导航到新位置。

Xamarin.Android 使用[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)，这是在为 API 级别 24 或更高级别进行开发时所必需的（JDK 8 还支持低于 24 的 API 级别）。 如果专门为 API 级别 23 或更低级别进行开发，可以继续使用 [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)。

> [!IMPORTANT]
> **注意：**Xamarin.Android 不支持 JDK 9。

<a name="sdk_manager" />

### <a name="android-sdk-manager"></a>Android SDK 管理器

Android 使用多个 Android API 级别设置来确定应用在各种版本的 Android 中的兼容性（有关 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)）。
根据要面向的 Android API 级别，可能需要下载和安装其他 Android SDK 组件。 此外，可能需要安装 Android SDK 中提供的可选工具和仿真器映像。 为此，请使用 Android SDK 管理器。 可单击“工具”>“Android”>“Android SDK 管理器”，启动“Android SDK管理器”：

[![如何启动 Android SDK 管理器](windows-images/08-sdk-manager-sml.png)](windows-images/08-sdk-manager.png)

默认情况下，Visual Studio 会安装 Google Android SDK 管理器：

![Google Android SDK 管理器的屏幕截图示例](windows-images/09-google-sdk-manager.png)

可使用 Google Android SDK 管理器安装最高版本为 25.2.3 的 Android SDK 工具包。 但是，如果需要使用更高版本的 Android SDK 工具包，则必须安装适用于 Visual Studio 的 Xamarin Android SDK 管理器插件（可从 Visual Studio Marketplace 获取）。 这是必需的，因为 Google 的独立 SDK 管理器已在 Android SDK 工具包 25.2.3 版本中弃用。 

有关使用 Xamarin Android SDK 管理器的详细信息，请参阅 [Android SDK 安装](~/android/get-started/installation/android-sdk.md)。

<a name="emulator" />

### <a name="android-emulator"></a>Android 仿真器

如果没有用于测试的 Android 物理设备，可使用 Android 仿真器来测试应用。 有关 Google Android 仿真器的详细信息，请参阅 [Android SDK 仿真器](~/android/deploy-test/debugging/android-sdk-emulator/index.md)。

Google Android 仿真器使用的是 Intel HAXM（硬件加速执行管理器），其可能与其他仿真器使用的虚拟化技术相冲突。 有 3 种主要的虚拟化技术：

-   Hyper-V（适用于 Android 的 Visual Studio 仿真器和 Windows Phone 仿真器使用） 

-   VirtualBox（Genymotion 使用）

-   Intel HAXM（Google Android SDK 仿真器使用） 

因为开发计算机的 CPU 一次仅支持一种虚拟化技术，因此最好只在开发计算机上采用一种技术。

<a name="device" />

### <a name="android-device"></a>Android 设备

如果有用于测试的 Android 物理设备，现在可设置设备用于开发。 通过查看[设置设备进行开发](~/android/get-started/installation/set-up-device-for-development.md)配置 Android 设备进行开发，然后将其连接到计算机以运行和调试 Xamarin.Android 应用程序。


<a name="create_app" />

## <a name="create-an-application"></a>创建应用程序

安装 Xamarin.Android 后，可启动 Visual Studio 创建一个新项目。 单击“文件”>“新建”>“项目”，开始创建应用：

![如何创建新项目](windows-images/10-new-project.png)

在“新建项目”对话框中的“模板”下，选择“Android”，然后单击右窗格中的“空白应用(Android)”。 输入应用名称（在下面的屏幕截图中，应用称为 MyApp），然后单击“确定”：

[![新建项目对话框的屏幕截图，创建一个空白 Android 应用](windows-images/11-first-app-sml.png)](windows-images/11-first-app.png)

就这么简单！ 现在即可使用 Xamarin.Android 创建 Android 应用程序！


<a name="summary" />

## <a name="summary"></a>摘要

本文介绍了如何在 Windows 上设置和安装 Xamarin.Android 平台、如何（可选）使用自定义 Java JDK 和 Android SDK 安装位置配置 Visual Studio、如何启动 SDK Manager 安装其他 Android SDK 组件、如何设置 Android 设备或仿真器，以及如何开始构建你的第一个应用程序。

下一步是查看[了解 Android](~/android/get-started/hello-android/index.md) 教程，了解如何创建可用的 Xamarin.Android 应用。


## <a name="related-links"></a>相关链接

- [下载 Visual Studio](https://www.visualstudio.com/vs/)
- [安装 Visual Studio Tools for Xamarin](~/cross-platform/get-started/installation/windows.md)
- [系统要求](~/cross-platform/get-started/requirements.md)
- [Android SDK 安装](~/android/get-started/installation/android-sdk.md)
- [Android SDK 仿真器](~/android/get-started/installation/android-emulator/index.md)
- [设置设备进行开发](~/android/get-started/installation/set-up-device-for-development.md)
