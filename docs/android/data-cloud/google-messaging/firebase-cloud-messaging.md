---
title: "Firebase 云消息传送"
description: "Firebase 云消息传送 (FCM) 是一种服务，它方便了移动应用程序和服务器应用程序之间的消息传递。 本文提供 FCM 的工作原理的概述和它还说明了如何配置 Google 服务，以便应用程序可以使用 FCM。"
ms.topic: article
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/29/2017
ms.openlocfilehash: 9f084899f44e0104d0aa2d4b3c0509812bd3fdd2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="firebase-cloud-messaging"></a>Firebase 云消息传送

_Firebase 云消息传送 (FCM) 是一种服务，它方便了移动应用程序和服务器应用程序之间的消息传递。本文提供 FCM 的工作原理的概述和它还说明了如何配置 Google 服务，以便应用程序可以使用 FCM。_

[![Firebase Cloud Messaging 英雄映像](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png)

本主题提供的 Firebase Cloud Messaging 将 Xamarin.Android 应用程序和应用程序服务器，之间的消息的路由的高级概述和它为获取凭据，以便应用程序可以使用 FCM 服务提供的分步过程。


<a name="overview" />

## <a name="overview"></a>概述

Firebase 云消息传送 (FCM) 是一个跨平台服务，处理发送、 路由和服务器应用程序和移动客户端应用程序之间的消息的队列。 FCM 是后继版本到 Google Cloud Messaging (GCM)，并基于 Google Play 服务。

下图中所示，FCM 充当邮件发件人和客户端之间的媒介。 A*客户端应用程序*设备上运行一个 FCM 启用应用程序。 *应用程序服务器*（由你或你的公司） 是客户端应用程序与进行通信通过 FCM FCM 启用服务器。 与 GCM，不同 FCM 使你可将消息发送到客户端应用程序直接通过 Firebase 控制台通知 GUI:

[![FCM 位于客户端应用程序和应用程序服务器之间](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png)

使用 FCM，应用程序服务器可以发送消息到单个设备、 一组设备，或多个主题订阅的设备。 客户端应用程序可以使用 FCM 订阅下游消息从应用程序服务器 （例如，若要接收远程通知）。 有关不同类型的 Firebase 消息的详细信息，请参阅[关于 FCM 消息](https://firebase.google.com/docs/cloud-messaging/concept-options)。


<a name="inaction" />

## <a name="firebase-cloud-messaging-in-action"></a>Firebase 云中操作的消息传送

当从应用程序服务器的下游消息发送到客户端应用程序时，应用程序服务器将发送到消息*FCM 连接服务器*Google; 提供 FCM 连接服务器，反过来，将消息转发给正在运行的设备客户端应用程序。 可以通过 HTTP 发送消息或[XMPP](https://developers.google.com/cloud-messaging/ccs) （可扩展消息传送和存在协议）。 因为客户端应用程序不始终连接或运行的 FCM 连接服务器将排和存储消息，将它们发送到客户端应用程序，因为它们重新连接并变得可用。 同样，FCM，则会排入队列上游消息从客户端应用到的应用服务器应用程序服务器不可用。 有关详细信息 FCM 连接服务器，请参阅[有关 Firebase 云消息传送服务器](https://firebase.google.com/docs/cloud-messaging/server)。

FCM 使用下面的凭据来标识应用程序服务器和客户端应用程序，并使用这些凭据授权通过 FCM 消息事务：

-   **发件人 ID** &ndash; *发件人 ID*是唯一的数字值时创建 Firebase 项目分配。 发件人 ID 用于标识可以将消息发送到客户端应用程序的每个应用程序服务器。 发件人 ID 也是你的项目编号;当你注册你的项目时，可以从 Firebase 控制台获得的发件人 ID。 发件人 ID 的一个示例是`496915549731`。

-   **API 密钥** &ndash; *API 密钥*Firebase 服务; 示例： 使应用程序服务器访问FCM 使用此密钥进行身份验证应用程序服务器。 此凭据也称为*服务器密钥*或*Web API 密钥*。 API 密钥的一个示例是`AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`。

-   **应用程序 ID** &ndash;客户端应用程序 （独立于任何给定设备） 注册从 FCM 接收消息的标识。 应用程序 ID 的一个示例是`1:415712510732:android:0e1eb7a661af2460`。

-   **注册令牌** &ndash; *注册令牌*(也称为*实例 ID*) 是客户端应用程序的给定设备上的 FCM 标识。 在运行时生成的注册令牌&ndash;您的应用程序收到的注册令牌，首先向注册时 FCM 在设备上运行时。 注册令牌授权客户端应用程序 （在该特定的设备上运行） 的实例从 FCM 接收消息。
    注册令牌的一个示例是`fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS`（很长字符串）。

[设置向上 Firebase Cloud Messaging](#setup_fcm) （在本指南后面介绍） 提供有关创建项目并生成这些凭据的详细的说明。 在创建新项目时在[Firebase 控制台](https://console.firebase.google.com/)，凭据文件调用**google services.json**创建&ndash;中所述，将此文件添加到你的Xamarin.Android项目[远程通知 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

以下各节说明当客户端应用程序与通过 FCM 的应用程序服务器通信时，如何使用这些凭据。


<a name="registration" />

### <a name="registration-with-fcm"></a>向 FCM 注册

消息传递进行之前，首先必须向 FCM 注册客户端应用程序。 客户端应用程序必须完成注册步骤下图中所示：

[![应用程序注册步骤关系图](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png)

1.  客户端应用程序将联系 FCM 以获取传递给 FCM 的发件人 ID、 API 密钥和应用程序 ID 的注册令牌。

2.  FCM 返回到客户端应用程序注册令牌。

3.  客户端应用程序 （可选） 将转发到的应用服务器的注册令牌。

应用程序服务器缓存用来与客户端应用程序的后续通信的注册令牌。 应用程序服务器可以将确认发送回客户端应用程序，以指示已收到注册令牌。 此握手发生后，客户端应用程序可以接收来自消息 （或将消息发送到） 应用程序服务器。 如果旧的令牌已透露，客户端应用程序可能会收到新的注册令牌 (请参阅[远程通知 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)有关应用如何接收注册令牌更新的示例)。

当客户端应用程序不再想要从应用程序服务器接收消息时，它可以发送到要删除的注册令牌的应用程序服务器的请求。 如果从设备上卸载客户端应用程序，FCM 检测到此和自动通知要删除的注册令牌的应用程序服务器。


<a name="downstream" />

### <a name="downstream-messaging"></a>下游消息传送

下图阐释了如何 Firebase Cloud Messaging 存储和转发下游邮件：

[![FCM 用于存储和转发下游消息传送](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png)

当应用程序服务器将下游消息发送到客户端应用程序时，它作为枚举上图中使用以下步骤：

1.  应用程序服务器将消息发送到 FCM。

2.  如果客户端设备不可用，FCM 服务器会将消息存储在队列的更高版本的传输。 消息保留在 FCM 存储最多 4 周 (有关详细信息，请参阅[设置一条消息的使用期限](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl))。

3.  当客户端设备可用时，FCM 将转发到该设备上的客户端应用的消息。

4.  客户端应用程序从 FCM 接收消息、 处理它，并为用户显示。 例如，如果消息是一个远程的通知，则将它显示给用户的通知区域中。

在此消息传递方案中 （其中应用程序服务器将发送一条消息到单个客户端应用程序），消息可以是长度最大为 4 kB。

有关接收在 Android 上的下游 FCM 消息的详细信息，请参阅[远程通知 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。


<a name="topic" />

### <a name="topic-messaging"></a>主题消息传送

*主题消息*使应用程序服务器向已选择加入某个特定主题的多个设备发送一条消息。 你还可以撰写和发送 Firebase 控制台通知 GUI 中主题的消息。 FCM 处理的路由和的主题消息传送到订阅的客户端。 此功能可以用于天气警报、 股票行情等头条新闻的消息。

[![主题的消息传递图](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png)

（在客户端应用程序获取注册令牌，如前面所述） 后，将主题消息传送中使用下列步骤：

1.  客户端应用程序到主题订阅，订阅将消息发送到 FCM。

2.  应用程序服务器分发将主题消息发送到 FCM。

3.  FCM 将主题消息转发给该主题已订阅的客户端。

有关 Firebase 主题消息传送的详细信息，请参阅 Google[主题的消息传送在 Android 上](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging)。


<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>设置 Firebase 云消息传送

你可以将 FCM 服务使用你的应用程序之前，你必须创建新的项目 （或导入一个现有项目） 通过[Firebase 控制台](https://console.firebase.google.com/)。 使用以下步骤创建用于你的应用程序的 Firebase Cloud Messaging 项目：

1.  登录到[Firebase 控制台](https://console.firebase.google.com/)使用 Google 帐户 （即，你 Gmail 的地址） 和单击**创建新项目**:

    [![创建新项目按钮](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png)

    如果你的一个现有项目，请单击**导入 Google 项目**。

2.  在**创建项目**对话框中，输入你的项目的名称，然后单击**创建项目**。 在以下示例中，新的项目称为**XamarinFCM**创建：

    [![创建项目对话框](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png)

3.  在 Firebase 控制台**概述**，单击**向 Android 应用程序添加 Firebase**:

    [![向 Android 应用添加 Firebase](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png)

4.  在下一个屏幕中，输入你的应用程序的程序包名称。 在此示例中，包名称是**com.xamarin.fcmexample**。 此值必须匹配 Android 应用程序的包的名称。 也可以输入中的应用程序昵称**应用昵称**字段：

    [![输入应用昵称 FCM 示例](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png)

5.  如果你的应用程序使用动态链接，邀请或 Google 身份验证，你还必须输入你调试签名证书。 有关查找签名证书的详细信息，请参阅[查找密钥库的 MD5 或 SHA1 签名](~/android/deploy-test/signing/keystore-signature.md)。
    在此示例中，为空的签名证书。

6.  单击**添加应用**:

    [![单击添加应用按钮](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png)

    为该应用自动生成服务器 API 密钥和客户端 ID。 此信息打包在**google services.json**单击时自动下载的文件**添加应用程序**。
    请务必将此文件保存在安全位置。

有关如何添加的详细示例**google services.json**到应用程序项目以接收 FCM 在 Android 上的推送通知消息，请参阅[远程通知 FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。


<a name="furtherreading" />

## <a name="for-further-reading"></a>其他阅读材料

-   Google [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/)提供 Firebase Cloud Messaging 的关键功能的概述，它的工作原理，以及安装说明进行操作的说明。

-   Google[生成应用程序服务器发送请求](https://firebase.google.com/docs/cloud-messaging/send-message)说明如何将与你的应用程序服务器的消息发送。

-   [RFC 6120](https://tools.ietf.org/html/rfc6120)和[RFC 6121](https://tools.ietf.org/html/rfc6121)说明和定义的可扩展消息传送和在线协议 (XMPP)。


<a name="summary" />

## <a name="summary"></a>摘要

本文章提供概述 Firebase 云消息传送 (FCM)。 它介绍了用于标识和授权应用程序服务器和客户端应用程序之间的消息传送的各种凭据。 它说明注册和下游的消息传递方案，并且它的详细使用 FCM 使用 FCM 服务注册你的应用的步骤。


## <a name="related-links"></a>相关链接

- [Firebase 云消息传送](https://firebase.google.com/docs/cloud-messaging/)
