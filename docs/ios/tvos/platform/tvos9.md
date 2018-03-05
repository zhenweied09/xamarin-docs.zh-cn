---
title: "TvOS 9 简介"
description: "本文介绍的所有新增和更改 Api 和 tvOS 9 中可用的功能为 Xamarin.tvOS 开发人员。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: c1fafe85fb7f5c30dca0dff6fe3dff5172e0b9aa
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-tvos-9"></a>TvOS 9 简介

_本文介绍的所有新增和更改 Api 和 tvOS 9 中可用的功能为 Xamarin.tvOS 开发人员。_


Apple 已发布 Apple TV 硬件采用经过重新设计，触摸启用远程，运行新的 （基于 iOS 9） 的 tvOS 操作系统的第四个生成。

第一次，tvOS 打开给开发人员，使您可以创建丰富、 引人入胜应用和释放它们通过 Apple 电视内置应用商店中此过程类似于编写和释放使用 iTunes 应用程序的 iOS 应用的体验的 Apple TV 平台存储区。

如果你熟悉 Xamarin.iOS 开发，你应查找转换到 tvOS 相当简单。 Api 和功能的大部分都是相同，但是，许多常见的 Api （如 WebKit) 不可用。 此外，使用与 Siri 远程会带来在触摸屏基于 iOS 设备中不存在一些设计难题。

本指南将为 Xamarin.tvOS 开发人员提供的所有新增和更改 Api 和 tvOS 9 中可用的功能的介绍。 有关 tvOS 的详细信息，请参阅 Apple 的[开发新的 Apple TV 的](https://developer.apple.com/tvos/)文档。

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>支持和不支持的功能

tvOS 应用在 Apple TV 上运行具有以下支持的功能和特性：

 - 应用组
 - 后台模式
 - 数据保护
 - Game Center
 - 游戏控制器
 - iCloud
 - 应用内购买
 - Keychain 共享

不支持的以下特性和功能：

 - Apple Pay
 - 应用沙盒
 - 关联的域
 - HealthKit
 - HomeKit
 - 应用间音频
 - 映射
 - 个人 VPN
 - 推送通知
 - Wallet
 - 无线附件配置

请参阅我们[支持程序集](~/ios/tvos/internals/assemblies.md)和[支持框架](~/ios/tvos/internals/frameworks.md)文档以了解更多信息。

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Apple TV 硬件

新的 Apple TV 具有以下硬件规范：

 - 64 位 A8 处理器
 - 32 GB 或 64 GB 的存储
 - 2 GB 的 RAM
 - 10/100 mbps 以太网
 - WiFi 802.11a/b/g/n/ac
 - 分辨率为 1080p 且
 - HDMI
 - USB C 端口 （针对开发人员和仅限诊断使用）
 - 新 Siri 远程或 Apple 电视远程 （基于区域）

### <a name="siri-remote"></a>Siri 远程

根据区域，提供 Apple 电视远程将出现在任一一个配置： Siri 远程或 Apple 电视远程。

在以下的国家/地区使用 Siri 远程是当前可用：

 - 澳大利亚
 - 加拿大
 - 法国
 - 德国
 - 日本
 - 西班牙
 - 英国
 - 美国

所有其他国家/地区将会收到 Apple 电视远程，Siri 按钮替换为与用于搜索的文本输入的默认搜索屏幕将显示一个搜索按钮：

[ ![](tvos9-images/remote02.png "Siri 远程")](tvos9-images/remote02.png)

有关详细信息，请参阅我们[Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文档。

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Apple 电视设置

就像针对 iOS 进行开发，新 tvOS 将需要的开发和分发根据团队成员和签名标识已建立了的与 Apple 的正确的预配配置文件。

正确设置，还必须访问 tvOS 功能，如 iCloud KVS 或 CloudKit 数据存储区。 请参阅我们[资源和数据存储](~/ios/tvos/app-fundamentals/resources-data-storage.md)有关支持 iCloud Xamarin.tvOS 应用中的信息。

创建和安装相同的方式使用 Xamarin.iOS 应用程序预配配置文件。 在这种情况下，请参阅我们的 iOS[设备资源调配](~/ios/get-started/installation/device-provisioning/index.md)更多详细信息的文档。

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Apple 电视应用

新 Apple TV 硬件和 tvOS 9 支持两种类型的应用： 传统和客户端-服务器应用程序。

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>传统的应用

传统应用从 Apple 电视应用商店购买和直接在设备上安装。 这些应用可以是游戏、 实用程序或作为 Xamarin.iOS 应用程序中使用相同的框架和技术开发的媒体应用程序。

Apple TV 应用具有最大大小为 200 MB，并可以下载使用按需资源的内容额外的 2 GB。 请参阅我们[资源和数据存储](~/ios/tvos/app-fundamentals/resources-data-storage.md)有关详细信息。

请参阅我们[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)来熟悉的工具和开发使用 Xamarin.tvOS tvOS 应用所需的概念。

<a name="Summary" />

### <a name="client-server-apps"></a>客户端-服务器应用程序

除了传统的已安装应用程序，Apple TV 可以轻松创建流式处理应用程序使用 web 技术 （HTTPS、 XML 和 JavaScript） 的基于 web 的客户端-服务器媒体。 你将设计用户界面使用 Apple 的 TVML 标记语言和 JavaScript 用于定义使用 TVMLKit 的应用程序的行为。

有关详细信息，请参阅 Apple 的[Apple 电视标记语言参考](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064)， [TVJS Framework 参考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076)， [TVMLKit Framework 参考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429)，[有关HTTP 实时流式处理](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978)和[HLS Apple TV 的创作规范](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf)文档。

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>用户界面问题

与 iOS 或 OS X，不同 Apple TV 没有触摸屏或允许用户直接选择并应用程序或其内容与之交互的鼠标。 改为这些用户在新的 Siri 远程数据库或蓝牙游戏控制器导航应用程序的用户界面。 有关详细信息，请参阅我们[Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文档。

此外，整体用户体验是明显不同于 iOS 或 Mac 应用程序往往是单个用户体验。 Apple 的电视节目，用户体验往往能够更社交本质，其中的多个用户可能坐在新床与单个应用程序和相互交互。 若要设计成功的 Apple TV 应用体验，（新的应用程序或将移植现有），这些更改必须考虑在内。 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>处理焦点和视差图像

如上面所述，那么不将与它的接口直接作为 ios 其中他们点击设备的屏幕上的映像但间接从在同一房间使用 Siri 远程交互 Xamarin.tvOS 应用程序的用户。 若要显示和处理这种用户交互，Apple TV，请使用基于焦点模式。 

焦点发生变动，作为使用细微动画和 （如对映像视差影响） 的效果清楚地将标识当前具有焦点的用户界面项。

如果用户在 Siri 远程数据库上进行速度慢、 循环笔势，已设定焦点的项将 sway 实时响应此移动。 Sway 发生时，照明的光泽适用于其映像进行显示表现的图面。 给定的处于非活动状态之后, 的任何扩展的焦点内容变暗和已设定焦点的项将变得甚至更大。

有关详细信息，请参阅我们[使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md)和[处理图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)文档。

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>主屏幕

Apple 电视主页屏幕显示所有应用程序安装并提供用于访问用户首选项的方法：

[ ![](tvos9-images/home01.png "主屏幕")](tvos9-images/home01.png)

用户导航上使用 Siri 远程使用焦点以选择应用程序，然后启动它使用触摸手势的应用程序图标的网格。 应用程序图标是你的第一个概率潜在用户留下很好的印象，应传达一眼的应用程序的用途。

每个应用必须提供一个较小和其应用程序图标的大型版本。 安装应用时，将在 Apple 电视主页屏幕上使用的小图标。 应用商店使用的大型的版本。 大的应用程序图标应模拟的小图标版本的外观和感觉。

有关详细信息，请参阅我们[处理图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)文档。

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>顶层架子

如果用户具有 Apple 电视主页屏幕上的顶部行上放置 Xamarin.tvOS 应用程序，将显示较大的顶部架图像时由用户选择您的应用程序。 此映像应突出显示你的应用程序的功能，或者提供其内容的直接链接。

[ ![](tvos9-images/topshelf01.png "顶层架子")](tvos9-images/topshelf01.png)

也可以作为单个静态提供顶部架映像`.png`或`.lsr`文件，也可以动态创建在运行时作为可获得焦点的项的单个行。

而不是显示静态的顶部架图像，它可以包含动态行或可获得焦点的项或一组动态的滚动横幅。 这两个这些动态样式，可以突出显示你的应用或跳转到其最常用的功能通过提供的内容。

有关详细信息，请参阅我们[处理图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)文档和 Apple 的[TVServices Framework 参考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412)有关将顶部架扩展添加到你的应用程序提供的详细信息动态顶部架内容。






## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [构建应用程序对 tvOS 使用 Xamarin （视频）](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
