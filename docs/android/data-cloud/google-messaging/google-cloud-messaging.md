---
title: Google Cloud Messaging
description: Google Cloud Messaging (GCM) 是一种服务，它方便了移动应用程序和服务器应用程序之间的消息传递。 本文概述 GCM 的工作原理，并介绍如何配置 Google 服务，使应用程序可以使用 GCM。
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: 29cccf414759a79a8ba74dfc35b7ba9f6a1cc5d6
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31044738"
---
# <a name="google-cloud-messaging"></a>Google Cloud Messaging

_Google Cloud Messaging (GCM) 是一种服务，它方便了移动应用程序和服务器应用程序之间的消息传递。本文概述 GCM 的工作原理，并介绍如何配置 Google 服务，使应用程序可以使用 GCM。_

[![Google Cloud Messaging 徽标](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

本主题提供的 Google Cloud Messaging 将您的应用程序和应用程序服务器，之间的消息的路由的高级概述和它为获取凭据，以便应用程序可以使用 GCM 服务提供的分步过程。

> [!NOTE]
> GCM 已被取代[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM)。
> GCM 服务器和客户端 Api[已弃用](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html)不再将尽快 2019 年 4 月 11，提供。

## <a name="overview"></a>概述

Google Cloud Messaging (GCM) 是处理发送、 路由和服务器应用程序和移动客户端应用程序之间的消息队列服务。 A*客户端应用程序*是一个在设备运行的 GCM 启用应用。 *应用程序服务器*（由你或你的公司） 是客户端应用程序与进行通信通过 GCM GCM 启用服务器：

[![GCM 驻留在客户端应用和应用程序服务器之间](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

使用 GCM，应用程序服务器可以将消息发送到单个设备、 一组设备或多个主题订阅的设备。 客户端应用程序可以使用 GCM 订阅下游消息从应用程序服务器 （例如，若要接收远程通知）。 此外，GCM 它可以让客户端应用以将上游消息发送回应用程序服务器。

有关对 GCM 实施应用程序服务器的信息，请参阅[有关 GCM 连接服务器](https://developers.google.com/cloud-messaging/server)。



## <a name="google-cloud-messaging-in-action"></a>Google Cloud Messaging 中操作

当下游消息从应用程序服务器发送到客户端应用程序时，应用程序服务器将发送到消息*GCM 连接服务器*; GCM 连接服务器，反过来，将消息转发给正在运行客户端应用程序的设备。 可以通过 HTTP 发送消息或[XMPP](https://developers.google.com/cloud-messaging/ccs) （可扩展消息传送和存在协议）。 因为客户端应用程序不始终连接或运行的 GCM 连接服务器将排和存储消息，将它们发送到客户端应用程序，因为它们重新连接并变得可用。 同样，GCM 排入队列将消息从客户端应用上游到应用程序服务器，如果将应用程序服务器不可用。

GCM 使用下面的凭据来标识应用程序服务器和客户端应用程序，并使用这些凭据授权通过 GCM 消息事务：

-   **API 密钥** &ndash; *API 密钥*访问你应用程序服务器的 Google 服务; 示例：GCM 使用此密钥来验证你的应用程序服务器。
    你可以使用 GCM 服务之前，你必须首先获取 API 密钥从[Google 开发人员控制台](https://console.developers.google.com/)通过创建*项目*。 API 密钥应保持安全的;有关保护你的 API 密钥的详细信息，请参阅[用于安全地使用 API 密钥的最佳做法](https://support.google.com/cloud/answer/6310037?hl=en)。

-   **发件人 ID** &ndash; *发件人 ID*授权向客户端应用程序的应用程序服务器&ndash;它是标识允许将消息发送到客户端应用程序的应用程序服务器的唯一编号。
    发件人 ID 也是你的项目编号;当你注册你的项目时，可以从 Google 开发人员控制台获得的发件人 ID。

-   **注册令牌** &ndash; *注册令牌*是客户端应用程序的给定设备上的 GCM 标识。 在运行时生成的注册令牌&ndash;时它第一次注册到 GCM 的设备上运行时，你的应用程序收到注册令牌。 注册令牌授权客户端应用程序 （在该特定的设备上运行） 的实例从 GCM 中接收消息。

-   **应用程序 ID** &ndash;客户端应用程序 （独立于任何给定设备） 注册从 GCM 中接收消息的标识。 在 Android 上，应用程序 ID 是记录中的包名称**AndroidManifest.xml**，如`com.xamarin.gcmexample`。

[设置向上 Google Cloud Messaging](#settingup) （在本指南后面介绍） 提供有关创建项目并生成这些凭据的详细的说明。

以下各节说明当客户端应用程序与应用程序服务器通过 GCM 通信时，如何使用这些凭据。



### <a name="registration-with-gcm"></a>注册到 GCM

消息传递进行之前，首先必须向 GCM 注册设备上安装客户端应用程序。 客户端应用程序必须完成注册步骤下图中所示：

[![应用程序注册步骤](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1.  客户端应用程序将联系 GCM 获取注册令牌，将发件人 ID 传递到 GCM。

2.  GCM 返回到客户端应用程序注册令牌。

3.  客户端应用程序将转发到的应用服务器的注册令牌。

应用程序服务器缓存用来与客户端应用程序的后续通信的注册令牌。 （可选） 应用程序服务器可以将确认发送回客户端应用程序，以指示已收到注册令牌。 此握手发生后，客户端应用程序可以接收来自消息 （或将消息发送到） 应用程序服务器。

当客户端应用程序不再想要从应用程序服务器接收消息时，它可以发送到要删除的注册令牌的应用程序服务器的请求。 如果客户端应用程序在接收主题消息 （本文后面所述），它可以从主题取消订阅。
如果从设备上卸载客户端应用程序，GCM 检测到此和自动通知要删除的注册令牌的应用程序服务器。

Google[注册客户端应用程序](https://developers.google.com/cloud-messaging/registration)说明注册过程中更多详细信息; 它还说明了注销和取消订阅，并在卸载客户端应用程序时介绍注销的过程。



### <a name="downstream-messaging"></a>下游消息传送

当应用程序服务器将下游消息发送到客户端应用程序时，它遵循下图所示的步骤：

[![下游消息存储和转发的关系图](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1.  应用程序服务器将消息发送到 GCM。

2.  如果客户端设备不可用，GCM 服务器会将消息存储在队列的更高版本的传输。

3.  当客户端设备可用时，GCM 会将消息发送到该设备上的客户端应用。

4.  客户端应用程序从 GCM 中接收消息，并相应地对其进行处理。 例如，如果消息是一个远程的通知，则将它显示给用户。

在此消息传递方案中 （其中应用程序服务器将发送一条消息到单个客户端应用程序），消息可以是长度最大为 4 kB。

有关详细信息 （包括代码示例） 接收在 Android 上的下游 GCM 消息，请参阅[远程通知](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md)。


#### <a name="topic-messaging"></a>主题消息传送

*主题消息*是一种下游消息传送应用程序服务器到订阅的主题 （如天气预报） 的多个客户端应用程序设备发送一条消息的位置。 主题消息可以是最大为 2 KB 的长度，并且消息传递主题支持最多 10 亿订阅每个应用程序。 如果 GCM 仅正用于消息传送的主题，客户端应用程序不需要将注册令牌发送到应用程序服务器。 Google[实现主题的消息传送](https://developers.google.com/cloud-messaging/topic-messaging)说明如何将消息从应用程序服务器发送到某个特定主题订阅的多个设备。



#### <a name="group-messaging"></a>组消息传递

*组消息传递*是一种下游消息传送应用程序服务器到属于的组 （例如，属于单个用户的设备组） 的多个客户端应用程序设备发送一条消息的位置。 组消息可以最大为 2 KB 对于 iOS 设备，长度和高达 4 KB 的适用于 Android 设备的长度。 组被限制为最多 20 个成员。 Google[设备组消息传递](https://developers.google.com/cloud-messaging/notifications)说明如何应用程序服务器可以将单个消息发送到属于某个组的设备上运行的多个客户端应用程序实例。


### <a name="upstream-messaging"></a>上游消息传送

如果客户端应用程序连接到支持的服务器[XMPP](https://developers.google.com/cloud-messaging/ccs)，它可以将消息发送回应用程序服务器，如下面的关系图中所示：

[![上游消息传送的关系图](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1.  客户端应用程序将消息发送到 GCM XMPP 连接服务器。

2.  如果应用程序服务器断开连接时，在 GCM 服务器会将消息存储在队列的更高版本转发。

3.  当应用程序服务器重新连接时，GCM 消息转发给应用程序服务器。

4.  应用程序服务器分析消息来验证身份的客户端应用程序，然后它将"ack"发送到 GCM 以确认消息回执。

5.  应用程序服务器处理的消息。

Google[上游消息](https://developers.google.com/cloud-messaging/ccs#upstream)说明了如何构建 JSON 编码的消息并将其发送给运行 Google 的基于符合 XMPP 的云连接服务器的应用程序服务器。


<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>Google Cloud Messaging 设置

在你的应用程序中使用 GCM 服务之前，您首先必须获取用于访问 Google 的 GCM 服务器凭据。 下列各节描述完成此过程所需的步骤：



### <a name="enable-google-services-for-your-app"></a>启用 Google 服务为你的应用

1.  登录到[Google 开发人员控制台](https://developers.google.com/mobile/add?platform=android)替换为你的 Google 帐户 （即，你 gmail 的地址），并创建一个新项目。 如果你有现有项目中，选择你想要成为 GCM 启用的项目。 在以下示例中，新的项目称为**XamarinGCM**创建：

    [![创建 XamarinGCM 项目](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2.  接下来，输入你的应用程序的包名称 (在此示例中，包名称是**com.xamarin.gcmexample**)，然后单击**继续访问选择和配置服务**:

    [![输入的包名称](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    请注意，此包名称也是你的应用程序的应用程序 ID。

3.  **选择和配置服务**部分列出了你可以将其添加到你的应用程序的 Google 服务。 单击**云消息传送**:

    [![选择云消息传送](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4.  接下来，单击**启用 GOOGLE CLOUD MESSAGING**:

    [![启用 Google Cloud Messaging](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5.  A**服务器 API 密钥**和**发件人 ID**会为你的应用程序的生成。 记录这些值，并且单击**关闭**:

    [![服务器 API 密钥和发件人 ID 显示](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    保护 API 密钥&ndash;它不是以公共方式使用。 API 密钥已泄漏，如果未经授权的服务器无法将消息发布到客户端应用程序。
    [用于安全地使用 API 密钥的最佳做法](https://support.google.com/cloud/answer/6310037?hl=en)提供有用的指导原则，用于保护你的 API 密钥。



### <a name="view-your-project-settings"></a>查看你的项目设置

你可以在任何时候查看你的项目设置，通过登录到[Google Cloud Console](https://console.cloud.google.com/)并选择你的项目。 例如，你可以查看**发件人 ID**通过在页面顶部的下拉菜单中选择你的项目 (在此示例中，项目称为**XamarinGCM**)。 发件人 ID 是此屏幕截图中所示的项目编号 (此处的发件人 ID 是**9349932736**):

[![查看发件人 ID](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

若要查看**API 密钥**，单击**API Manager** ，然后单击**凭据**:

[![查看 API 密钥](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)



## <a name="for-further-reading"></a>其他阅读材料

-   Google[注册客户端应用程序](https://developers.google.com/cloud-messaging/registration)描述客户端注册过程中的详细信息，并提供有关配置自动重试和保持注册状态同步信息。

-   [RFC 6120](https://tools.ietf.org/html/rfc6120)和[RFC 6121](https://tools.ietf.org/html/rfc6121)说明和定义的可扩展消息传送和在线协议 (XMPP)。



## <a name="summary"></a>总结

本文章提供 Google Cloud Messaging (GCM) 的概述。 它介绍了用于标识和授权应用程序服务器和客户端应用程序之间的消息传送的各种凭据。 它说明最常见的消息传递方案，并且它详细向 GCM 使用 GCM 服务注册你的应用的步骤。


## <a name="related-links"></a>相关链接

- [云消息传送](https://developers.google.com/cloud-messaging/)
