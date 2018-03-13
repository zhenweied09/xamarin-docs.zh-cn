---
title: "注册应用程序在后台运行"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 5fcb41f4f60adc8ca5be761c2b9a7449387a89d0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="registering-applications-to-run-in-the-background"></a>注册应用程序在后台运行

注册为后台权限适用于某些应用程序，但如果应用程序不断地调用时，若要执行重要的、 长时间运行任务，例如获取有关如何通过 GPS 用户，会发生什么情况的各个任务？ 而是应将这些应用程序注册为已知背景需要应用程序。

注册应用程序向发出信号，iOS 应用程序应授予在后台执行任务所需的特殊权限。

## <a name="application-registration-categories"></a>应用程序注册类别

已注册的应用可以分为几个类别：

-  **音频**-音乐播放器和其他应用程序使用的音频内容注册继续播放音频甚至应用时不再在前台。 如果此类别中的应用程序尝试执行任何操作而非播放音频或在后台下载，iOS 将终止它。
-  **VoIP** -语音通过，Internet 协议 (VoIP) 应用程序获取的相同的特权授予到音频的应用程序以保留处理在后台的音频。 它们还可以根据需要响应电源它们，以使其连接处于活动状态的 VoIP 服务。
-  **外部附件和蓝牙**-保留给需要与蓝牙设备和其他外部硬件附件通信的应用程序，在这些类别注册允许应用程序以保持连接到的硬件。
-  **Newsstand** -Newsstand 应用程序可以继续同步在后台的内容。
-  **位置**-应用程序使用的 GPS 或网络位置数据可以发送和接收位置更新在后台。
-  **提取 (iOS 7 +)** -为后台提取特权可以按固定的间隔，向用户显示与已更新的内容，并且在返回到应用程序时检查新内容的提供程序注册的应用程序。
-  **远程通知 (iOS 7 +)** -应用程序可以注册以接收通知，从提供程序，并使用通知以开始更新之前在用户打开应用程序。 通知可以采用以下形式的推送通知，或选择以无提示方式唤醒应用程序。


应用程序可以通过设置注册**需要后台模式**中应用程序的属性*Info.plist*。 应用程序可以在多个类别，因为它需要中注册：

 [![](registering-applications-to-run-in-background-images/bgmodes.png "设置后台模式")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

注册用于后台位置更新的应用程序的分步指南，请参阅[后台位置演练](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md)。

## <a name="application-does-not-run-in-background-property"></a>应用程序并非运行在后台属性

另一个属性，可以在设置*Info.plist*是*应用程序并非运行在后台*，或`UIApplicationExitsOnSuspend`属性：

 [![](registering-applications-to-run-in-background-images/plist.png "禁用后台运行")](registering-applications-to-run-in-background-images/plist.png#lightbox)

这样将后台应用程序刷新设置设置为 off 在 iOS 7 + 中，但只能在开发人员端中，更改完全相同的效果，并可用 iOS 4 及更高版本。 应用程序将在输入后台后立即挂起，并且将不能执行任何处理。

如果你的应用程序未设计为处理后台处理，因为它有助于避免意外的行为，请使用此属性。

## <a name="background-fetch-and-remote-notifications"></a>背景提取和远程通知

背景提取和远程通知是 iOS 7 中引入的特殊注册类别。 这些类别允许应用程序从提供程序，接收新内容，然后在后台更新。 下一节介绍提取和更详细地远程通知，并还引入了位置感知如意味着更新应用程序在后台对 iOS 6。
