---
title: 系统要求
description: 本文档列出了在 Mac 和 Windows 计算机上使用 Xamarin 生成应用的系统要求。 它还链接到安装说明。
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
author: conceptdev
ms.author: crdun
ms.date: 07/24/2018
ms.openlocfilehash: 422eb24b86ba14ff4e5362db8aeec5775fab5833
ms.sourcegitcommit: aa16f267c59725cc88bd84b049544ecfbec297ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2018
ms.locfileid: "43263510"
---
# <a name="system-requirements"></a>系统要求

Xamarin 产品依赖于 Apple 和 Google 中的平台 SDK 才能面向 iOS 或 Android，因此系统要求应与这些平台的要求匹配。 此页列出了 Xamarin 平台的系统兼容性以及建议的开发环境和 SDK 版本。

有关获取软件和所需 SDK 的详细信息，请参阅[安装说明](#installation-instructions)。

## <a name="development-environments"></a>开发环境

此表显示了可使用不同开发工具和操作系统组合生成的平台：

[!include[](~/cross-platform/includes/development-environment.md)]

> [!NOTE]
> 若要在 Windows 计算机上针对 iOS 进行开发，必须有[可通过网络访问的 Mac 计算机](~/ios/get-started/installation/windows/connecting-to-mac/index.md)，以供远程编译和调试。 这同样适用于 Visual Studio 在 Mac 计算机上的 Windows VM 内运行的情况。

## <a name="macos-requirements"></a>macOS 要求

使用 Mac 计算机进行 Xamarin 开发需要以下软件/SDK 版本。 检查操作系统版本，并按照 [Xamarin 安装程序](#installation-instructions)相关说明操作。

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> 可通过 [developer.apple.com](https://developer.apple.com/xcode/download/) 或 Mac App Store 安装（和更新）Xcode。

### <a name="testing--debugging-on-macos"></a>macOS 上的测试和调试

- 可通过 USB 将 Xamarin 移动应用程序部署到物理设备以进行测试和调试（Apple Watch 应用首先部署到配对的 iPhone）。
- 可直接在开发计算机上测试 Xamarin.Mac 应用。

[!include[](~/cross-platform/includes/macos-testing.md)]

> [!WARNING]
> 即将发布的 Xamarin.Mac 4.8 版本仅支持 macOS 10.9 或更高版本。
> 早期版本的 Xamarin.Mac 支持 macOS 10.7 或更高版本，但这些较旧的 macOS 版本缺少足够的 TLS 基础结构，无法支持 TLS 1.2。 若要面向 macOS 10.7 或 macOS 10.8，请使用 Xamarin.Mac 4.6 或更早版本。

## <a name="windows-requirements"></a>Windows 要求

使用 Windows 计算机进行 Xamarin 开发需要以下软件/SDK 版本。
检查操作系统版本（请确保使用的不是 Express 版本的 Visual Studio - 如果使用的是该版本，则请考虑更新至 Community 版本）。
Visual Studio 2017 安装程序支持自动安装 Xamarin（使用 .NET 的移动开发）。

[!include[](~/cross-platform/includes/windows-requirements.md)]

> [!NOTE]
> - 适用于 Visual Studio 的 Xamarin 支持各种 Visual Studio 2017 版本（Community、Professional 和 Enterprise）。
> - 必须在 Windows 10 上安装 Visual Studio 2017，才能为通用 Windows 平台 (UWP) 开发 Xamarin.Forms 应用。

### <a name="testing--debugging-on-windows"></a>Windows 上的测试和调试

可通过 USB 或无线将 Xamarin 移动应用程序部署到物理设备以进行测试和调试（iOS 设备必须连接到 Mac 计算机，而不是运行 Visual Studio 的计算机）。

[!include[](~/cross-platform/includes/windows-testing.md)]

## <a name="installation-instructions"></a>安装说明

可从 [xamarin.com/download](http://xamarin.com/download) 下载适用于 macOS 的最新版 Xamarin。 对于 Windows，请按照 [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 安装说明进行安装。

[当前发布页](http://developer.xamarin.com/releases/current/)上提供了当前产品版本的完整列表。 此页还概述了 beta 和 alpha 通道的个人产品版本（和发行说明的链接）。

有关每个平台的特定[安装](~/cross-platform/get-started/installation/index.md)说明，请访问：

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

还提供有关 [Xamarin.Forms 要求和支持的平台](~/xamarin-forms/get-started/installation.md)的其他信息。

## <a name="related-links"></a>相关链接

- [下载 Xamarin](https://visualstudio.microsoft.com/xamarin/)
- [当前版本](https://developer.xamarin.com/releases/current/)
