---
redirect_url: /xamarin/tools/live-player/
title: XAML 实时预览
description: 本文档讨论了如何使用 Xamarin Live Player 实时预览 XAML 页面、 对 XAML，进行更改并查看立即出现在设备上的更改。
ms.prod: xamarin
ms.assetid: 86E9A179-21F8-4F3A-A9CE-36F0FC5DB4A8
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: 1602c98eceaff607c79400a37c4ace60d5bf8807
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110591"
---
# <a name="xaml-live-previewing"></a>XAML 实时预览

Xamarin Live Player 的优势之一是能够实时预览 XAML 页面，对 Visual Studio 中的代码进行更改并查看立即显示在设备上的更改。 可在 Android 设备上或在模拟器或仿真器上实时预览。 本指南演示如何使用实时预览功能来查看单个 XAML 屏幕。

## <a name="requirements"></a>要求

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 运行 Windows 7 或更高版本的计算机。
2. Visual Studio 2017 版本 15.4 或更高版本与**使用.NET 的移动开发**安装工作负载。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. Mac OS X 10.11，macOS 10.12，或更高版本。
2. Visual Studio for Mac 7.2 或更高版本。 我们建议使用最新版本。

-----

<a name="deploydevice" />

## <a name="deploying-to-device"></a>部署到设备

在 Android 设备上使用 Xamarin Live Player 之前，你将需要下载 Xamarin Live Player 应用和配对到 Visual Studio 中所述[安装](~/tools/live-player/install.md)指南。 您已成功配对后您的设备与 Visual Studio，你可以开始实时预览 XAML 页面。 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 打开要在 Visual Studio 2017 编辑器中的实时预览 XAML 页：

    ![](live-view-images/vs-image1.png)

2. 将设备配置设置为**调试**并从列表中选择的 Live Player 设备：

    ![](live-view-images/vs-image2.png)

3. 若要运行此 XAML 页，为你的设备上的实时视图，请选择**工具 > Xamarin Live Player > 实时运行当前视图**从菜单栏：

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 打开要为 Mac 编辑器在 Visual Studio 中的实时预览 XAML 页：

    ![](live-view-images/image1.png)

2. 将设备配置设置为**调试**并从列表中选择的 Live Player 设备：

    ![](live-view-images/image2.png)

3. 若要运行此 XAML 页，为你的设备上的实时视图，请选择**运行 > 实时运行当前视图**从菜单栏：

    ![](live-view-images/image3.png)

-----

## <a name="deploying-to-android-emulator"></a>部署到 Android 仿真器

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 打开要在 Visual Studio 2017 编辑器中的实时预览 XAML 页：

    ![](live-view-images/vs-image1.png)

2. 将设备配置设置为**调试**适用于 Android 和选择列表中的 Live Player 设备：

    ![](live-view-images/vs-image4.png)

3. 若要为 Android 仿真程序上的实时视图中运行此 XAML 页，选择**工具 > Xamarin Live Player > 实时运行当前视图**从菜单栏：

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 打开要为 Mac 编辑器在 Visual Studio 中的实时预览 XAML 页：

    ![](live-view-images/image7.png)

2. 将设备配置设置为**调试**适用于 Android 和选择列表中的 Live Player 设备：

    ![](live-view-images/image6.png)

3. 若要运行此 XAML 页，为你的设备上的实时视图，选择运行 > 实时运行当前视图从菜单栏：

    ![](live-view-images/image3.png)

-----

## <a name="related-links"></a>相关链接

- [Xamarin Live Player 概述](https://xamarin.com/live)
- [博客文章](https://blog.xamarin.com/live-player/)
- [Xamarin Live Player 示例](~/tools/live-player/samples.md)
