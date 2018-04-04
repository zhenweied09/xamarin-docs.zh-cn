---
redirect_url: /xamarin/tools/live-player/
title: XAML 实时预览
description: 在 iOS 或 Android 设备上的实时中测试应用程序代码更改
ms.prod: xamarin
ms.assetid: 86E9A179-21F8-4F3A-A9CE-36F0FC5DB4A8
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 12/21/2017
ms.openlocfilehash: 721266e1ddfe927b33529a9f4d0eb55a008dd4e8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xaml-live-previewing"></a>XAML 实时预览

Xamarin 实时播放器的优势之一是能够实时预览 XAML 页面，对在 Visual Studio 中，代码进行更改并查看立即显示在你的设备上的更改。 在 iOS 或 Android 设备上或在模拟器或仿真程序上，可以进行实时预览。 本指南演示如何使用实时预览功能，若要查看单个 XAML 屏幕。

## <a name="requirements"></a>要求

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 运行 Windows 7 或更高版本的计算机。
2. Visual Studio 2017 15.4 或使用更高版本**使用.NET 进行移动开发**安装的工作负荷。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 具有 OS X 10.11，macOS 10.12，或更高版本的 Mac。
2. Mac 7.2 或更高版本的 visual Studio。 我们建议的最新版本。

-----



<a name="deploydevice" />

## <a name="deploying-to-device"></a>将部署到设备

你可以使用 Xamarin 实时播放器 iOS 或 Android 设备之前，你将需要下载实时的 Xamarin Player 应用和中所述到 Visual Studio 配对[安装](~/tools/live-player/install.md)指南。 你已成功完成配对后您的设备与 Visual Studio，你可以开始实时预览 XAML 页面。 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 打开要在 Visual Studio 2017 编辑器中的实时预览的 XAML 页：

    ![](live-view-images/vs-image1.png)

2. 将设备配置设置为**调试 | iPhone**适用于 iOS 或**调试**Android，然后选择列表的实时播放器设备：

    ![](live-view-images/vs-image2.png)

3. 若要运行此 XAML 页用作你的设备上的实时视图，选择**工具 > 实时的 Xamarin Player > 实时运行的当前视图**从菜单栏：

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 打开要在 Visual Studio 中的实时预览为 Mac 编辑器的 XAML 页：

    ![](live-view-images/image1.png)

2. 将设备配置设置为**调试 | iPhone**适用于 iOS 或**调试**Android，然后选择列表的实时播放器设备：

    ![](live-view-images/image2.png)

3. 若要运行此 XAML 页用作你的设备上的实时视图，选择**运行 > 实时运行的当前视图**从菜单栏：

    ![](live-view-images/image3.png)

-----








## <a name="deploying-to-android-emulator"></a>将部署到 Android 仿真程序

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 打开要在 Visual Studio 2017 编辑器中的实时预览的 XAML 页：

    ![](live-view-images/vs-image1.png)

2. 将设备配置设置为**调试**适用于 Android 和选择列表的实时播放器设备：

    ![](live-view-images/vs-image4.png)

3. 若要运行此 XAML 页作为 Android 仿真程序上的实时视图，选择**工具 > 实时的 Xamarin Player > 实时运行的当前视图**从菜单栏：

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 打开要在 Visual Studio 中的实时预览为 Mac 编辑器的 XAML 页：

    ![](live-view-images/image7.png)

2. 将设备配置设置为**调试**适用于 Android 和选择列表的实时播放器设备：

    ![](live-view-images/image6.png)

3. 若要运行此 XAML 页用作你的设备上的实时视图，选择运行 > 实时运行当前视图从菜单栏：

    ![](live-view-images/image3.png)

-----





## <a name="deploying-to-ios-simulator"></a>部署到 iOS 模拟器

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

目前尚不支持使用实时 XAML 预览在 Windows 上进行远程处理的 iOS 模拟器上的方法。 应改为[部署到设备](#deploydevice)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 打开要在 Visual Studio 中的实时预览为 Mac 编辑器的 XAML 页：

    ![](live-view-images/image1.png)

2. 将设备配置设置为**调试 | iPhoneSimulator**适用于 iOS 和 iOS 模拟器从列表中选择：

    ![](live-view-images/image2.png)

3. 选择**运行 > 实时运行的当前视图**从菜单栏中启动模拟器并显示 XAML 页：

    ![](live-view-images/image4.png)

4. 一旦模拟器已启动，可以开始编辑 XAML，并查看显示实时预览：

    ![](live-view-images/image5.png)  

-----








## <a name="related-links"></a>相关链接

- [Xamarin 实时播放机概述](https://xamarin.com/live)
- [博客文章](https://blog.xamarin.com/live-player/)
- [Xamarin 实时播放器示例](~/tools/livehttps://developer.xamarin.com/samples.md)
