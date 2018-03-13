---
title: "实现视频播放器"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0CE9BEE7-4F81-4A00-B9B3-5E2535CD3050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: a889be5ee31f667117d2c36859e667980f0e6610
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="implementing-a-video-player"></a>实现视频播放器

因此，有时需要播放在 Xamarin.Forms 应用程序中的视频文件。 此系列文章讨论如何编写适用于 iOS、 Android 和一个名为的 Xamarin.Forms 类通用 Windows 平台 (UWP) 的自定义呈现器`VideoPlayer`。

在[ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)取样，请实现并支持所有文件`VideoPlayer`在文件夹中名为`FormsVideoLibrary`使用命名空间的标识`FormsVideoLibrary`或命名空间开始`FormsVideoLibrary`。 此组织命名应进行轻松地将视频播放器文件复制到 Xamarin.Forms 解决方案。

`VideoPlayer` 可以播放从三种类型的源的视频文件：

- 使用 URL 在 Internet
- 在平台应用程序中嵌入的资源
- 设备的视频库

视频播放器需要*传输控件*、 它们用于播放和暂停视频中，按钮和定位栏，显示了通过视频的进度，并允许用户快速跳到不同的位置。 `VideoPlayer` 可以使用传输控件和定位栏提供的平台 （如下所示），也可以提供自定义传输控件和定位栏。 此处是在 iOS、 Android 和通用 Windows 平台下运行的程序：

[![播放 Web 视频](web-videos-images/playwebvideo-small.png "播放 Web 视频")](web-videos-images/playwebvideo-large.png#lightbox "播放 Web 视频")

当然，你可以打开手机上，以进行较大的视图。

更复杂的视频播放器将具有一些其他功能，如卷控制、 电话呼叫时，通过中断视频的机制和一种在播放期间保持屏幕活动。

以下一系列文章渐进式演示如何生成的平台呈现器和支持类：

## <a name="creating-the-platform-video-playersplayer-creationmd"></a>[创建平台视频播放器](player-creation.md)

每个平台需要`VideoPlayerRenderer`类创建和维护包含平台支持的视频播放器控件。 这篇文章演示呈现器的结构类，以及如何创建播放器。

## <a name="playing-a-web-videoweb-videosmd"></a>[播放 Web 视频](web-videos.md)

可能的视频的视频播放器最常见来源是 Internet。 本文介绍如何引用和用作视频播放器源 Web 视频。

## <a name="binding-video-sources-to-the-playersource-bindingsmd"></a>[绑定到播放器的视频源](source-bindings.md)

本文章将使用`ListView`来呈现视频播放的集合。 一个程序显示的代码隐藏文件如何可以设置视频源的视频播放器，但第二个程序演示如何使用数据绑定之间`ListView`和视频播放器。

## <a name="loading-application-resource-videosloading-resourcesmd"></a>[加载应用程序资源视频](loading-resources.md)

在平台项目中，可以作为资源嵌入视频。 这篇文章演示如何存储这些资源和更高版本将它们加载到程序中要播放的视频播放器。

## <a name="accessing-the-devices-video-libraryaccessing-librarymd"></a>[访问设备的视频库](accessing-library.md)

使用设备的照相机创建视频时，视频文件存储在设备的映像库中。 这篇文章演示如何访问设备的图像选取器，以便选择的视频，并随后使用视频播放器播放。

## <a name="custom-video-transport-controlscustom-transportmd"></a>[自定义视频传输控件](custom-transport.md)

尽管每个平台上的视频播放器提供自己的按钮的窗体中的传输控制**播放**和**暂停**，可以禁止显示这些按钮，还可以提供你自己。 本文介绍如何。

## <a name="custom-video-positioningcustom-positioningmd"></a>[自定义视频定位](custom-positioning.md)

每个平台视频播放器都有一个位置栏，显示视频的进度，并允许你以跳到特定位置的向前或向后。 本文演示如何可以将该位置栏替换为自定义控件。





## <a name="related-links"></a>相关链接

- [视频播放器演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
