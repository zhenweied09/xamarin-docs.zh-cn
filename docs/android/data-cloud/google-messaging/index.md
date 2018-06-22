---
title: Google 消息传送
description: 本部分包含介绍如何实现使用 Google 的消息传递服务的 Xamarin.Android 应用程序的指南。
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: cf1eaec3dfee7c3457a4614147c9b5564843b2a7
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31044660"
---
# <a name="google-messaging"></a>Google 消息传送

_本部分包含介绍如何实现使用 Google 的消息传递服务的 Xamarin.Android 应用程序的指南。_

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Firebase 云消息传送](firebase-cloud-messaging.md)

Firebase 云消息传送 (FCM) 是一种服务，它方便了移动应用程序和服务器应用程序之间的消息传递。 FCM 是到 Google Cloud Messaging 的 Google 的后继版本。 本文提供 FCM 的工作原理的概述和它为获取凭据，以便应用程序可以使用 FCM 服务提供的分步过程。

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[远程通知 Firebase 云消息传送](remote-notifications-with-fcm.md)

本演练提供在 Xamarin.Android 应用程序中如何使用 Firebase Cloud Messaging 实现远程通知 （也称为推送通知） 的分步说明。 它演示如何实现所需的通信使用 Firebase 云消息传送 (FCM)，提供如何配置用于访问 FCM，Android 清单的示例，演示如何使用 Firebase 下游消息传递的各种类控制台。

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Google 云消息传送](google-cloud-messaging.md)

本部分提供的 Google Cloud Messaging (GCM) 将你的应用程序和应用程序服务器，之间的消息的路由的高级概述，并获取凭据，以便应用程序可以使用 GCM 服务提供的分步过程。 （请注意 FCM 被取代 GCM。）

> [!NOTE]
> GCM 已被取代[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM)。
> GCM 服务器和客户端 Api[已弃用](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html)不再将尽快 2019 年 4 月 11，提供。

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[使用 Google Cloud Messaging 的远程通知](remote-notifications-with-gcm.md)

本部分提供了使用 Google Cloud Messaging 如何在 Xamarin.Android 中实现远程通知的分步说明。
它还说明了必须利用 Android 应用程序中启用 Google Cloud Messaging 的各种组件。


