---
title: watchOS 平台功能
description: 此文档链接到各种指南描述 watchOS 平台功能，如 Apple Pay、 通知、 复杂性、 主动建议、 锻炼应用和的详细信息。
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 16d10dd69223f404aac7c933302992a1544461e9
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066609"
---
# <a name="watchos-platform-features"></a>watchOS 平台功能

此文档链接到各种指南描述 watchOS 平台功能，如 Apple Pay、 通知、 复杂性、 主动建议、 锻炼应用和的详细信息。

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[watchOS 4 简介](introduction-to-watchos4.md)

本文档提供功能在 watchOS 4 添加并更新的高级的概述。

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[watchOS 3 简介](introduction-to-watchos3/index.md)

本指南介绍了 watchOS 3 中的新的和更新 Api。

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Apple Pay 增强功能](~/ios/watchos/platform/apple-pay.md)

在 watchOS 3，PassKit framework 已扩展为允许为安全的应用内付款 （的物理货物和服务） 为在 Apple Watch 上运行的应用程序的支持。

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[后台任务](~/ios/watchos/platform/background-tasks.md)

watchOS 3 引入了多个应用程序可以使用更新后打开它，用户需要其信息确保它具有内容的后台任务。

## <a name="notificationsnotificationsmd"></a>[通知](notifications.md)

了解如何提供自定义通知您监视的应用程序中的处理。

## <a name="complicationscomplicationsmd"></a>[复杂情况](complications.md)

添加复杂性支持在手表表盘上显示的最新数据。

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[主动建议](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 允许应用主动向内的用户显示信息给定上下文。 为了支持此功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity)现在包括`MapItem`使应用可以通过其他应用提供位置信息以供将来使用的属性。

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[快速交互技术](~/ios/watchos/platform/quick-interaction-techniques.md)

提供快速的用户交互至关重要创建引人注目的 Apple Watch 应用和复杂性。 新 watchOS 3，到 Apple 添加了对笔势识别器访问的数字王冠和新的用户通知和导航技术支持。 此操作，请添加支持 SceneKit 和 SpriteKit，以及允许开发人员可以轻松创建丰富，方便查看是快速、 响应快的接口。

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[测验应用增强功能](~/ios/watchos/platform/workout-apps.md)

新与 watchOS 3，测验相关应用程序能够在 Apple Watch 上在后台运行。 若要启用此功能 （和获取对 HealthKit 数据访问），应用程序必须包括`WKBackgroundModes`中的键`Info.plist`文件中的使用值`workout-processing`。
