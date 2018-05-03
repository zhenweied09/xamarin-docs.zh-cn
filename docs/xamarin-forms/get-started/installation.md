---
title: Xamarin.Forms 要求
description: Xamarin.Forms 平台和开发系统要求。
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/19/2018
ms.openlocfilehash: d2125c1ddaa3edc3e2ee76d8e03e384efdca42c6
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2018
---
# <a name="xamarinforms-requirements"></a>Xamarin.Forms 要求

_Xamarin.Forms 平台和开发系统要求。_

请参阅[安装](~/cross-platform/get-started/installation/index.md)一文中跨平台应用的安装和设置概述。

## <a name="target-platforms"></a>目标平台

可对以下操作系统编写 Xamarin.Forms 应用程序：

-  iOS 8 或更高版本
-  Android 4.0.3 (API 15) 或更高版本（[详细信息](#android)）
-  Windows 10 通用 Windows 平台（[详细信息](#windows10)）
-  Windows 8.1 / Windows Phone 8.1 WinRT（[详细信息](#windows)）
-  *Windows Phone 8 Silverlight（已弃用）*

假定开发人员熟悉[可移植类库](~/cross-platform/app-fundamentals/pcl.md)和[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)。

<a name="android" />

### <a name="android"></a>Android

应安装最新的 Android SDK 工具和 Android API 平台。 可使用 [Android SDK 管理器](~/android/get-started/installation/android-sdk.md)更新到最新版本。

此外，Android 项目的目标/编译版本必须设置为“使用最新安装的平台”。 但是，最低版本可设置为 API 15，因此可继续支持使用 Android 4.0.3（以及更高版本）的设备。 在“项目选项”中可设置这些值：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

“项目选项”>“应用程序”>“应用程序属性”

![](installation-images/options-android-vs-sml.png "Visual Studio 中的 Android 生成选项")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

“生成”>“常规”

![](installation-images/options-general-sml.png "“生成”>“常规”")

“生成”>“Android 应用程序”

![](installation-images/options-android-sml.png "“生成”>“Android 应用程序”")

-----


<a name="windows10" />

### <a name="universal-windows-platform"></a>通用 Windows 平台

在 macOS 上创建解决方案时不会添加 Windows 10 UWP 项目。 有关如何向现有解决方案添加这些项目的说明，请参阅[添加通用 Windows 平台 (UWP) 应用](~/xamarin-forms/platform/windows/installation/universal.md)。


<a name="windows" />

### <a name="windows-81--windows-phone-81-winrt"></a>Windows 8.1 / Windows Phone 8.1 WinRT

在 macOS 上创建解决方案时不会添加 Windows 8.1 / Windows Phone 8.1 WinRT 项目。 有关如何向现有解决方案添加这些项目的说明，请参阅[添加 Windows Phone 应用](~/xamarin-forms/platform/windows/installation/phone.md)和[添加 Windows 应用](~/xamarin-forms/platform/windows/installation/tablet.md)。


## <a name="development-system-requirements"></a>开发系统要求

可在 macOS 和 Windows 上开发 Xamarin.Forms 应用。 但是，生成 Windows 版本的应用需要使用 Windows 和 Visual Studio。

## <a name="mac-system-requirements"></a>Mac 系统要求

可使用 Visual Studio for Mac 在 OS X El Capitan (10.11) 或更高版本上开发 Xamarin.Forms 应用。 若要开发 iOS 应用，建议至少安装 iOS 10 SDK 和 Xcode 8。

> [!NOTE]
>  不能在 macOS 上开发 Windows 应用。

## <a name="windows-system-requirements"></a>Windows 系统要求

任何支持 Xamarin 开发的 Windows 安装上都可生成适用于 iOS 和 Android 的 Xamarin.Forms 应用。 这要求 Visual Studio 2017 或更高版本在 Windows 7 或更高版本上运行。 iOS 开发需要使用联网的 Mac。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

为 UWP 开发 Xamarin.Forms 应用需要：

* Windows 10（建议使用 Fall Creators Update）

* Visual Studio 2017

* [Windows 10 SDK](https://dev.windows.com/downloads/windows-10-sdk)

UWP 项目包含在 Visual Studio 2015 和 Visual Studio 2017 中所创建的 Xamarin.Forms 解决方案中。
还可以在现有的 Xamarin.Forms 解决方案中[添加通用 Windows 平台 (UWP) 应用](~/xamarin-forms/platform/windows/installation/universal.md)。

