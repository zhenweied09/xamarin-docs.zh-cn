---
title: 简介 watchOS 3
description: 本文介绍的所有新增和更改 Api 和 watchOS 3 中可用的功能的 Xamarin 开发人员。
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/07/2017
ms.openlocfilehash: 506e3795538ceffc77301a608c504fc6ec2045a7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-watchos-3"></a>简介 watchOS 3

_本文介绍的所有新增和更改 Api 和 watchOS 3 中可用的功能的 Xamarin 开发人员。_

本文档将涉及以下主题：

- [什么是新建 watchOS 3](#Whats-New-in-watchOS-3)
    - [Apple 支付增强功能](#Apple-Pay-Enhancements)Apple Watch 上增加的应用内付款的支持。
    - [后台任务](#Background-Tasks)为应用提供能够更新其信息在后台，以便随时可将当用户需要它。
    - [复杂性增强功能](#Complications-Enhancements)已为 watchOS 3 的应用提供新功能进行了。
    - [新可用框架](#Newly-Available-Frameworks)已公开的 watchOS 应用。
    - [主动建议](#Proactive-Suggestions)允许应用主动向用户显示信息。
    * 多个[安全和隐私增强功能](#Security-and-Privacy-Enhancements)watchOS 3 对进行了。
    - [快照和停靠](#Snapshots-and-Dock)用户提供快速访问的应用程序 watchOS 应用。
    - [用户通知](#User-Notifications)向用户提供本地和远程通知。
    * 多个[监视连接 Framework 增强功能](#Watch-Connectivity-Framework-Enhancements)已在 watchOS 3 中进行。
    * 多个[WatchKit Framework 增强功能](#WatchKit-Framework-Enhancements)已在 watchOS 3 中进行。
    - [测验应用增强功能](#Workout-App-Enhancements)锻炼提供新功能相关的 Apple Watch 应用。
- [其他 Framework 更改](#Additional-Framework-Changes)进行了整个 watchOS 3。
- [弃用的 Api](#Deprecated-APIs) watchOS 3 中。

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>什么是新建 watchOS 3

Apple 具有 watchOS 3 以及对现有功能，包括许多增强功能中添加几个新的 Api 和服务：

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Apple 支付增强功能

在 watchOS 3，PassKit framework 已扩展为允许为安全的应用内付款 （的物理货物和服务） 为在 Apple Watch 上运行的应用程序的支持。

使用新[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)和[PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)类为响应其中用户可以授权付款请求的接口和呈现。

若要了解详细信息，请参阅我们[Apple 支付增强功能](~/ios/watchos/platform/apple-pay.md)指南。

<a name="Background-Tasks" />

## <a name="background-tasks"></a>后台任务

watchOS 3 引入了多个应用程序可以使用更新后打开它，用户需要其信息确保它具有内容的后台任务。

以下新的后台任务有：

- **后台应用程序刷新**- [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask)任务允许应用程序以更新其状态在后台。 通常，这将包括另一个任务，例如从 internet 使用下载的新内容[NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)。
- **后台快照刷新**- [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)任务允许应用程序以更新其内容和 UI 之前系统采用将用于填充停靠的快照。
- **后台监视连接**- [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask)从配对 iPhone 接收背景数据时，应用程序启动的任务。
- **后台 URL 会话**- [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask)后台传输要求获得授权或完成 （成功或错误） 时，为应用程序启动的任务。

若要了解详细信息，请参阅我们[后台任务](~/ios/watchos/platform/background-tasks.md)指南。

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>复杂性增强功能

复杂性是提供一目了然的有用信息的小型可视元素。 具体取决于选择手表表盘，用户已与一个或多个复杂性手表表盘的自定义功能。

watchOS 3 为应用提供了创建一个或多个监视应用程序的复杂性，以便用户可以从手表表盘访问其信息在直观的能力。

此外，复杂性提供以下好处：

- 用户可以快速启动应用，通过直接从手表表盘复杂性上点击。
- 有关监视表面原因保留一个应用程序的复杂性系统以保持它尝试启动在后台，应用程序的已准备启动状态应用程序将其保存在内存和提供它更长时间来更新。
- 复杂性被保证每日至少 50 推送更新。
- 当应用程序包括复杂性时，它将显示在 Apple Watch 表盘库中 (请参阅 Apple 的[添加到库的复杂性](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery)有关详细信息的文档)。

在 watchOS 3，ClockKit framework 现在包括适用于特大型复杂性的几个新模板如[CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext)和[CLKComplicationTemplateExtraLargeRingImage](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). 此外，若要创建可本地化的文本，可使用的新方法[CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider)类。

若要了解详细信息，请参阅我们[watchOS 3 的快速交互技术](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>新的可用框架

watchOS 3 包括多个现有的 Apple 框架，例如对以前不可用：

- **SceneKit** -使用 SceneKit 包括三维模型到包括的大多数照明，明暗度，动画，物理学像在其他平台上可用的功能和粒子系统的监视应用程序的 UI。 不支持三维空间音频、 自定义配置文件或 OpenGL 的着色器、 核心映像筛选器和基于以物理方式的材料。
- **SpriteKit** -使用 SpriteKit 来呈现和动画中包括的大多数功能可在操作、 物理、 照明和粒子系统之类的其他平台上的应用程序监视应用程序的 UI 层进行动画处理。 不支持三维空间音频、 视频播放和核心映像筛选器。
- **AVFoundation** -若要将管理和播放音频。
- **CloudKit** -若要监视应用程序和 iCloud 容器之间移动数据。
- **核心音频**-若要管理数据类型用于表示音频流、 复杂缓冲区和时间值。
- **GameKit** -若要创建社交游戏。

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>主动建议

watchOS 3 允许应用主动向内的用户显示信息给定上下文。 为了支持此功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity)现在包括`MapItem`使应用可以通过其他应用提供位置信息以供将来使用的属性。

若要了解详细信息，请参阅我们[简介主动建议](~/ios/watchos/platform/proactive-suggestions.md)指南。

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>安全和隐私增强功能

Apple 已对安全和隐私有助于开发人员提高其应用程序的安全性，并确保最终用户的隐私的 watchOS 3 中进行几项增强功能。

因此，watchOS 3 （或更高版本） 上运行的应用必须以静态方式声明其想要通过输入中一个或多个隐私特定键访问特定功能或用户信息其`Info.plist`向为什么应用程序希望获得访问权限的用户说明的文件。

由于 watchOS 3 与 iOS 10 共享这些更改，请参阅我们的 iOS 10[安全和隐私增强功能](~/ios/app-fundamentals/security-privacy.md)指南以获取详细信息。

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>快照和停靠

在 watchOS 3，Apple 添加了的停靠用户可以固定其最喜欢的应用并快速访问它们。 当用户按端按钮下 Apple Watch 上时，将显示固定的应用快照的库。 左或向右以查找所需的应用，然后点击应用程序以启动该快照将替换为正在运行的应用的接口，用户可以往下轻扫。

系统定期将快照应用程序的 UI，并使用这些快照来填充文档。watchOS 使应用程序可以拍摄此快照之前更新其内容和 UI。

有关详细信息，请参阅我们[后台任务](~/ios/watchos/platform/background-tasks.md)指南和 Apple 的[WKSnapshotRefreshBackgroundTask 引用](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)。

<a name="User-Notifications" />

## <a name="user-notifications"></a>用户通知

用户通知框架 watchOS 3 中引入支持本地和远程通知到 Apple Watch 的传送。 使用此框架来计划基于特定条件如时间天或位置的通知以及接收和处理通知。

若要了解详细信息，请参阅我们[watchOS 3 的快速交互技术](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>观看连接 Framework 增强功能

新`HasContentPending`属性[WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession)类表示会话已收到在需要处理后台的数据。 与`RemainingComplicationUserInfoTransfers`属性返回时间，剩余的 iOS 应用可以更新其 watchOS 复杂性。

若要了解详细信息，请参阅我们[后台任务](~/ios/watchos/platform/background-tasks.md)指南。

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>WatchKit Framework 增强功能

watchOS 3 包括到 WatchKit 框架包括以下几个增强功能：

- 应用程序可以获取的数字王冠状态使用新[WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer)类和用户旋转王冠使用时接收更新[WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate)类。
- [WKExtension](https://developer.apple.com/reference/watchkit/wkextension)类现在包括`ApplicationState`方法和[WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate)应用程序可用于跟踪应用程序的运行时状态的常量。 `WKExtension` 此外提供了可用于计划后台任务的两个新方法。
- [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)现在包括新`ApplicationWillEnterForeground`，`ApplicationDidEnterBackground`和`HandleBackgroundTasks`监视应用程序的状态的更改和处理后台任务更新的方法。
- 一个新[WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer)添加类以提供笔势识别应用于监视应用程序的以下类型： [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer)， [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer)， [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer)和[WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer)。
- 新[WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera)类提供一个接口，对于任何 HomeKit 连接 IP 相机。
- 新[WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie)类允许应用程序以显示电影"海报"将在用户点击它时被替换为正在运行的影片。
- 新[WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton)类允许应用将启动时分流的付款请求其用户界面中存在一个 Apple Pay 按钮。
- 新[WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene)类提供用于在 Apple Watch 上显示 SceneKit 场景的接口。
- 新[WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene)类提供用于在 Apple Watch 上显示 SpriteKit 场景的接口。

若要了解详细信息，请参阅我们[watchOS 3 的快速交互技术](~/ios/watchos/platform/quick-interaction-techniques.md)指南。

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>测验应用增强功能

新与 watchOS 3，测验相关应用程序能够在 Apple Watch 上在后台运行。 若要启用此功能 （和获取对 HealthKit 数据访问），应用程序必须包括`WKBackgroundModes`中的键`Info.plist`文件中的使用值`workout-processing`。

此外，开发人员现在可以启动配对的 iPhone 上的 iOS 应用程序版本中的 watchOS 锻炼应用程序的能力。

若要了解详细信息，请参阅我们[锻炼应用增强功能](~/ios/watchos/platform/workout-apps.md)指南。

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>附加的框架的更改

除了的主要 framework 更改和上面列出的添加件，Apple 已作出 watchOS 3 中的许多其他的次要 framework 更改。

若要了解详细信息，请参阅我们[其他 Framework 更改](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md)指南。

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>弃用的 API

WatchOS 3 中已弃用以下 Api:

- `UILocalNotification` UIKit 类已弃用，因此应替换的用户通知框架。

请参阅 Apple 的[watchOS 2.2 的 watchOS 3.0 API 差异](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html)的弃用功能和更改的文档的完整列表。


## <a name="related-links"></a>相关链接

- [watchOS 示例](https://developer.xamarin.com/samples/watchos/all/)
- [什么是 watchOS 3 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
