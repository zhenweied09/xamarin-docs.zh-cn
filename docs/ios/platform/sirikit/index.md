---
title: SiriKit
description: "这篇文章演示如何在 Xamarin.iOS 应用程序中使用 SiriKit 提供的 iOS 设备上使用 Siri 向用户访问服务。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 9d7773be1244b0ba4e1a57c8a1efbddf02396138
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="sirikit"></a>SiriKit

_这篇文章演示如何在 Xamarin.iOS 应用程序中使用 SiriKit 提供的 iOS 设备上使用 Siri 向用户访问服务。_

新 SiriKit 到 iOS 10，允许 iOS 应用程序提供使用应用程序扩展和新的 iOS 设备上使用 Siri 和映射的应用程序的用户可以访问的服务**意向**和**意向 UI**框架。

Siri 配合的概念**域**，组知道相关任务的操作。 应用程序必须具有与 Siri 每个交互必须属于其已知的服务域之一，如下所示：

- 音频或视频的调用。
- 预订持续一段时间。
- 管理锻炼。
- 消息传递。
- 搜索照片。
- 发送或接收付款。

当用户进行的请求 Siri 涉及应用扩展的服务之一时，SiriKit 发送扩展**意向**描述以及任何支持的数据的用户的请求的对象。 然后，应用扩展生成相应**响应**对象给定**意向**，详细介绍如何扩展可以处理该请求。

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)

本文介绍如何将需要在 Xamarin.iOS 应用程序中使用 SiriKit 的关键概念。 它涵盖新意向和意向 UI 扩展点和如何使用应用程序和用户词汇以打开应用程序使用 Siri。

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[实现 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

本文介绍如何在 Xamarin.iOS 应用程序中实现 SiriKit 支持所需的步骤。 开发人员应尝试将 SiriKit 支持作为介绍概念将成功实现所需的密钥添加到一个应用程序之前先阅读上面了解 SiriKit 概念指南。





## <a name="related-links"></a>相关链接

- [ElizaChat 示例](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [SiriKit 编程指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [意向 Framework 参考](https://developer.apple.com/reference/intents)
- [意向 UI Framework 参考](https://developer.apple.com/reference/intentsui)
