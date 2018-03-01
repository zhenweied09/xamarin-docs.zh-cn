---
title: "不推荐使用的通知技术"
ms.topic: article
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/07/2016
ms.openlocfilehash: be005f62c914fe5f59696c5ef9b608a73a45d269
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="deprecated-notification-technologies"></a>不推荐使用的通知技术

本部分说明如何实现本地和 Xamarin.iOS 中的推送通知。 它将解释 iOS 通知的各种 UI 元素，并讨论 API 的涉及创建和显示一条通知。

> [!IMPORTANT]
> **注意：**此部分中的信息与 iOS 9 和之前，它仍处于此处以支持较旧的 iOS 版本。 对于 iOS 10 及更高版本，请参阅[用户通知 Framework 指南](~/ios/platform/user-notifications/index.md)支持的 iOS 设备上的本地和远程通知。




## <a name="sections"></a>部分

<a name="Local Notifications In iOS" />

##  <a name="local-notifications-in-ioslocal-notifications-in-iosmd"></a>[在 iOS 中的本地通知](local-notifications-in-ios.md)

本部分将讨论如何在 Xamarin.iOS 中实现本地通知。 它将解释 iOS 通知的各种 UI 元素，并讨论 API 的涉及创建和显示一条通知。

<a name="Local Notifications Walkthrough" />

##  <a name="walkthrough---using-local-notifications-in-xamarinioslocal-notifications-in-ios-walkthroughmd"></a>[演练-在 Xamarin.iOS 中使用本地通知](local-notifications-in-ios-walkthrough.md)

在本部分中我们将演练如何在 Xamarin.iOS 应用程序中使用本地通知。 它将演示创建和发布将弹出警报时接收的应用程序的通知的基础知识。

<a name="Remote Notifications In iOS" />

##  <a name="remote-notifications-in-iosremote-notifications-in-iosmd"></a>[在 iOS 中的远程通知](remote-notifications-in-ios.md)

本部分将介绍在 iOS 中的推送通知。 它引入了 Apple 推送通知网关服务 (APNS) 和它到 iOS 应用程序的发布通知中所扮演的角色。 它将说明如何创建启用推送通知和讨论所需的安全证书。 最后本部分将讨论某些应用程序服务器必须执行能够跟踪客户端移动设备的管理任务。

## <a name="related-links"></a>相关链接

- [通知 （示例）](https://developer.xamarin.com/samples/monotouch/Notifications/)
