---
title: '安装和设置磨损 OS onXamarin.Android '
description: 本文将指导完成的安装步骤和准备你的计算机和 Android 磨损开发的设备所需的配置详细信息。 这篇文章结束时，你将拥有一个有效的 Xamarin.Android 磨损安装集成到 Visual Studio 用于 Mac 和/或 Microsoft Visual Studio 中，而你将准备就绪，若要开始生成首个 Xamarin.Android 磨损应用程序。
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: 14162663c518fdd1324f2b0340592fbae491d112
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436500"
---
# <a name="setup-and-installation"></a>设置和安装

_本文将指导完成的安装步骤和准备你的计算机和 Android 磨损开发的设备所需的配置详细信息。这篇文章结束时，你将拥有一个有效的 Xamarin.Android 磨损安装集成到 Visual Studio 用于 Mac 和/或 Microsoft Visual Studio 中，而你将准备就绪，若要开始生成首个 Xamarin.Android 磨损应用程序。_

## <a name="requirements"></a>要求

创建基于 Xamarin Android 磨损应用需要以下：

-   **Visual Studio 或 Visual Studio for Mac** &ndash;你如果你正在使用 Visual Studio 中，Visual Studio 2015 Professional 或更高版本。

-   **Xamarin.Android** &ndash; Xamarin.Android 4.17 或更高版本必须安装并配置了 Visual Studio 或 Visual Studio for mac。

-   **Android SDK** -Android SDK 5.0.1 (API 21) 或更高版本必须安装通过 Android SDK 管理器。

-   **Java 开发人员工具包** &ndash; Xamarin Android 开发需要[JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)如果你是开发的 API 级别 24 或更高版本 （JDK 1.8 还支持 API 级别早于 24）。

你可以继续使用[JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)如果你是专门为 API 级别 23 开发或更早版本。

> [!IMPORTANT]
> Xamarin.Android 不支持 JDK 9。

## <a name="installation"></a>安装

安装 Xamarin.Android 后，执行以下步骤，以便你准备好生成并测试 Android 磨损应用： 

1.  安装所需的 Android SDK 和工具。
2.  配置测试设备。
3.  创建第一个 Android 磨损应用。

以下各节将介绍这些步骤。


### <a name="install-android-sdk-and-tools"></a>安装 Android SDK 和工具 

启动**Android SDK 管理器**: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![如何启动 Visual Studio 中的 Android SDK 管理器](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![如何为 Mac 启动 Visual Studio 中的 Android SDK 管理器](installation-images/xs/sdk-menu.png)

-----


确保你具有以下 Android SDK 和安装工具：

* Android SDK 工具 v 24.0.0 或更高版本，并
* Android 4.4W (API20)，或
* Android 5.0.1 (API21) 或更高版本。

如果没有最新的 SDK 和安装的工具，下载所需的 SDK 工具*和*API bits (可能需要进行滚动找到它们有一位&ndash;API 选择如下所示): 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![示例 SDK 管理器屏幕截图启用 Android 5.0.1 的组件](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![示例 SDK 管理器屏幕快照： 启用 Android 4.4 和 5.0.1 组件](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>配置

你可以使用之前测试你的应用程序，你必须配置 Android 磨损在仿真器或真实 Android 磨损设备。 


### <a name="android-wear-emulator"></a>Android 磨损仿真程序

你可以使用 Android 磨损仿真程序之前，必须配置 Android 磨损 Android 虚拟设备 (AVD) 使用**Google 仿真程序管理器**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![如何启动从 Visual Studio Android 模拟器管理器](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![如何启动 Mac 从 Visual Studio Android 模拟器管理器](installation-images/xs/emulator-menu.png)

-----

有关设置 Android 磨损仿真程序的详细信息，请参阅[在模拟器上调试 Android 磨损](~/android/wear/deploy-test/debug-on-emulator.md)。


### <a name="android-wear-device"></a>Android 磨损设备

如果你没有如 Android 磨损 Smartwatch Android 磨损设备，你可以调试而不是使用仿真程序此设备上的应用。 有关使用磨损设备开发的信息，请参阅[磨损设备上调试](~/android/wear/deploy-test/debug-on-device.md)。


## <a name="create-your-first-android-wear-app"></a>创建第一个 Android 磨损应用

请按照[Hello，带](~/android/wear/get-started/hello-wear.md)生成第一个监视应用程序的说明。


## <a name="packaging-your-app"></a>打包应用

Android 磨损应用程序始终随助理 Android 手机版应用一起分发。 

当你将 Android 磨损应用程序添加为对主 Android 应用程序的引用它被自动假定为 Android 磨损的项目，并将为你生成所有必需的 XML 和元数据。 此外，它将验证包和版本号匹配以便你可以轻松地将你的应用发运到 Google Play。 

若要了解有关打包磨损应用的详细信息，请参阅[使用打包](~/android/wear/deploy-test/packaging.md)。


## <a name="related-links"></a>相关链接

- [SkeletonWear （示例）](https://developer.xamarin.com/samples/SkeletonWear/)
