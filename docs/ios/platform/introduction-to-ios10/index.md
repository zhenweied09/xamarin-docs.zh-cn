---
title: IOS 10 简介
description: 本文介绍的所有新增和更改 Api 和 iOS 10 中可用的功能为 Xamarin.iOS 开发人员。
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: c3bee0f15016394005a67e98cd8435e6d63b3ac6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-ios-10"></a>IOS 10 简介

_本文介绍的所有新增和更改 Api 和 iOS 10 中可用的功能为 Xamarin.iOS 开发人员。_

## <a name="introducing-ios-10"></a>引入 iOS 10

使用新的 iOS 10 SDK，Apple 具有包含新 Api 和服务，使开发人员创建的应用程序和功能的新类别。 IOS 应用程序现在可以扩展消息、 Siri、 Phone 和映射应用到以前不可用的最终用户提供丰富、 吸引人的功能。

有关 iOS 10 的详细信息，请参阅 Apple 的[iOS + 应用](https://developer.apple.com/ios/)文档。

## <a name="whats-new-in-ios-10"></a>什么是新建 iOS 10

Apple 已在 iOS 10 以及对现有功能，包括许多增强功能中添加几个新的 Api 和服务：


## <a name="adapting-to-the-true-tone-display"></a>调整到 True 音显示

Apple 的 True 音显示技术使用 iOS 设备中的环境光线传感器来动态调整颜色和显示效果以与当前的照明条件匹配的强度。 iOS 10 提供新[UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31)可以添加到应用程序的密钥`Info.plist`文件，并控制 True 音应用标准颜色变化的方式。 

以下值有：

- `UIWhitePointAdaptivityStyleStandard` **默认**-使用标准的空白点 adaptivity。
- `UIWhitePointAdaptivityStyleReading` -用于为中心读取的应用。
- `UIWhitePointAdaptivityStyleGame` -用于游戏中心应用。
- `UIWhitePointAdaptivityStyleVideo` -用于视频已设定焦点的应用。
- `UIWhitePointAdaptivityStylePhoto` -用于摄影已设定焦点应用色彩保真度所在环境的空白点调整比更重要。

<a name="app-extensions" />

## <a name="app-extensions"></a>应用程序扩展

Apple 提供的 iOS 10 中存在多个新的应用程序扩展点：

- 调用目录
- 意向和意向 UI
- 消息
- 通知内容
- Notification Services
- 不干胶标签包

此外，第三方键盘应用扩展具有以下增强功能：

- 新`DocumentInputMode`属性`UITextDocumentProxy`类可以确定输入文档的语言，并允许键盘扩展与该语言一致。
- 新`HandleInputModeList`方法允许显示点击的全球键的响应中系统的键盘选取器菜单的键盘扩展。

有关详细信息，请参阅我们[扩展简介](~/ios/platform/extensions.md)，[消息应用集成](~/ios/platform/message-app-integration/index.md)，[简介主动建议](~/ios/platform/search/proactive-suggestions.md)， [简介 SiriKit](~/ios/platform/sirikit/index.md)，[简介用户通知](~/ios/platform/user-notifications/index.md)和 Apple 的[应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

## <a name="app-search-enhancements"></a>应用程序搜索增强功能

在 iOS 10 中的核心 Spotlight 如提供对应用程序搜索的多项增强功能：

- **Crowdsourced 深层链接受欢迎程度 （与差异隐私）** -提供一种方法来升级在搜索结果中的深层链接应用内容。
- **应用内搜索**-使用新`CSSearchQuery`类以提供应用内 Spotlight 搜索功能类似于邮件、 消息和注释应用的工作原理。
- **搜索延续**-允许用户在 Spotlight 或 Safari，启动搜索，然后打开应用程序，并继续了搜索。
- **可视化效果的验证结果**-Apple 的[应用搜索 API 验证工具](https://search.developer.apple.com/appsearch-validation-tool)时 preforming 测试现在显示可视表示形式网站的标记和深层链接。
- **消息应用映像共享**-允许为共享 （通过一个消息应用程序扩展） 的消息才会显示在 Spotlight 搜索提供的常用应用内映像。

若要了解详细信息，请参阅我们[应用搜索增强功能](~/ios/platform/search/app-search-enhancements.md)指南。

## <a name="apple-pay-enhancements"></a>Apple 支付增强功能

Apple 具有对 Apple Pay 进行多项增强功能，允许用户从网站和通过与 Siri 和映射进行交互的安全付款的 iOS 10。

使用 iOS 10，采用了 iOS 和 watchOS 以支持动态付款网络和一个新的沙盒测试环境已添加了几个新的 Api。

此外，PassKit framework 已扩展为支持 Apple Pay 之外`UIKit`和允许卡颁发者提供在其应用程序中的从其卡。

若要了解详细信息，请参阅我们[Apple 支付增强功能](~/ios/platform/apple-pay.md)指南。

## <a name="alternate-app-icons"></a>备用的应用程序图标

Apple iOS 10.3，允许管理图标将其应用到添加了几项增强功能：

 - `ApplicationIconBadgeNumber` -获取或设置应用程序图标的徽章 Springboard 中。
 - `SupportsAlternateIcons` -如果`true`应用程序具有一组备用的图标。
 - `AlternateIconName` -返回当前选定的备用图标的名称或`null`如果使用的主图标。
 - `SetAlternameIconName` -使用此方法以切换到给定的备用图标的应用程序的图标。

若要了解详细信息，请参阅我们[备用的应用程序图标](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)指南。


## <a name="introduction-to-callkit"></a>CallKit 简介

IOS 10 中的新 CallKit API 提供一种 VOIP 应用将与 iPhone UI 集成和提供熟悉的界面，并向最终用户体验的方法。 对于此 API，用户可以查看和与 VOIP 呼叫从 iOS 设备锁定屏幕进行交互和管理使用电话应用的联系人**收藏夹**和**最近**视图。

此外，CallKit API 提供的功能，以创建应用程序扩展，可将电话号码相关联的名称 (调用方 ID) 也可指示应为数字时，系统将阻止 （调用阻塞）。

若要了解详细信息，请参阅我们[简介 Callkit](~/ios/platform/callkit.md)指南。

## <a name="message-app-integration"></a>消息应用程序集成

iOS 10 中与集成的 Xamarin.iOS 解决方案允许消息应用程序扩展包含**消息**应用并显示向用户的新功能。 扩展可以发送文本、 标签、 媒体文件和交互式消息。 两种类型的消息应用扩展有：

- **不干胶标签包**-包含用户可以向消息中添加的不干胶标签的集合。 不干胶标签包可以创建无需编写任何代码。
- **iMessage 应用**-可以显示自定义用户界面中选择不干胶标签、 输入文本，包括 （带可选类型转换） 的媒体文件和创建、 编辑和发送交互消息的消息应用。

若要了解详细信息，请参阅我们[消息应用集成](~/ios/platform/message-app-integration/index.md)指南。

## <a name="news-publisher-enhancements"></a>新闻发布服务器增强功能

与 iOS 10，Apple 将允许从主要杂志和博客和独立要注册的发布者和产品的新组织的任何人，并将内容传送到 Apple 新闻应用程序。 若要了解详细信息，请参阅 Apple 的[新闻资源](https://newsresources.apple.com/)文档。

## <a name="providing-haptic-feedback"></a>提供 Haptic 反馈

在 iPhone 7 和 iPhone 7 加、 Apple 具有包含提供其他方式来以物理方式面向用户的新 haptics 响应。 使用新的边用反馈选项以便获取用户的注意并强调其操作。

几个内置的 UI 元素已提供 haptic 反馈如选取器、 交换机和滑块。 iOS 10 现在添加了以编程方式触发 haptics 使用的具体子类的功能`UIFeedbackGenerator`类。

若要了解详细信息，请参阅我们[提供 Haptic 反馈](~/ios/user-interface/ios-ui/haptic-feedback.md)指南。

## <a name="proactive-suggestions"></a>主动建议

iOS 10 通过允许系统以主动有用的信息自动向用户显示在适当的时间显示推动用户参与策略应用到的新方式。 为 iOS 9 提供的功能添加到 Spotlight、 Handoff 和 Siri 建议使用的 iOS 10 应用可以公开可以从以下位置中的系统通过向用户显示的功能的应用的深层搜索：

- 应用程序切换器
- 锁定屏幕
- CarPlay
- 映射
- Siri 交互
- QuickType 建议 

应用程序公开此功能到使用技术的集合，如系统[NSUserActivity](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/)，web 标记中，核心 Spotlight、 MapKit、 Media Player 和 UIKit。

若要了解详细信息，请参阅我们[简介主动建议](~/ios/platform/search/proactive-suggestions.md)指南。

## <a name="request-app-review"></a>查看请求应用程序

新到 iOS 10.3`RequestReview()`方法允许 iOS 应用程序要求用户进行评估或对其进行评审。 而有意义的用户体验中的任意位置，可以调用此方法，评审过程是控制并且由应用商店策略处理。 因此，此方法可能或可能不会显示警报，应永远不会调用以响应用户操作，如点击按钮。

若要了解详细信息，请参阅我们[请求应用审查](~/ios/platform/request-app-review.md)指南。

## <a name="security-and-privacy-enhancements"></a>安全和隐私增强功能

Apple 已对安全和隐私有助于开发人员提高其应用程序的安全性，并确保最终用户的隐私的 iOS 10 中的几项增强功能。

因此，在 iOS 10 （或更高版本） 上运行的应用静态必须声明其想要通过输入中一个或多个隐私特定键访问特定功能或用户信息其`Info.plist`向为什么应用程序希望获得访问权限的用户说明的文件。

若要了解详细信息，请参阅我们[安全和隐私增强功能](~/ios/app-fundamentals/security-privacy.md)指南。

## <a name="sirikit"></a>SiriKit

新 SiriKit 到 iOS 10，允许 Xamarin.iOS 应用程序提供的 iOS 设备上使用 Siri 向用户访问服务。 此功能提供一个或多个应用程序扩展中使用新**意向**和**意向 UI**框架。

SiriKit 支持以下服务域：

- 音频或视频的调用。
- 预订持续一段时间。
- 管理锻炼。
- 消息传递。
- 搜索照片。
- 发送或接收付款。

当用户进行的请求 Siri 涉及应用扩展的服务之一时，SiriKit 发送扩展**意向**描述以及任何支持的数据的用户的请求的对象。 然后，应用扩展生成相应**响应**对象给定**意向**，详细介绍如何扩展可以处理该请求。

应用扩展时使用 Siri 通常处理所有用户交互，可使用**意向 UI** framework 提供丰富、 自定义用户界面采用应用程序的品牌和其他信息。

若要了解详细信息，请参阅我们[简介 SiriKit](~/ios/platform/sirikit/index.md)指南。

## <a name="speech-recognition"></a>语音识别

iOS 10 包括一个新的语音 API，用于应用程序支持连续语音识别和语音 （从实时或录制音频流），理赔将转换为文本。

因为语音识别需要的传输和 Apple 的服务器，应用程序上的数据的临时存储_必须_请求用户的权限以通过包括执行识别`NSSpeechRecognitionUsageDescription`中的键其`Info.plist`文件和调用`SFSpeechRecognizer.RequestAutorization`方法。

若要了解详细信息，请参阅我们[简介语音识别](~/ios/platform/speech.md)指南。

## <a name="user-notifications"></a>用户通知

新 iOS 10，框架允许在传递和本地和远程通知的处理其用户通知。 使用此框架，应用程序或应用扩展可以计划传递本地通知通过指定一组条件，例如位置或当天的时间。

此外，应用程序或扩展可以接收 （和可能修改） 本地和远程通知在传递到用户的 iOS 设备。

新的用户通知 UI 框架允许在应用程序或应用扩展，它们会呈现给用户时，自定义的本地和远程通知的外观。

若要了解详细信息，请参阅我们[用户通知 Framework](~/ios/platform/user-notifications/index.md)指南。

## <a name="video-subscriber-account"></a>视频的订阅服务器帐户

新对于 iOS 10，该视频订阅服务器帐户框架允许在应用该支持身份验证的流式处理或视频点播向其电缆或附属电视提供程序为最终用户使用单一登录体验进行身份验证。

## <a name="wide-color"></a>广泛的颜色

iOS 10 扩展对扩展范围像素格式和整个系统包括框架，例如核心图形、 Core 映像、 金属和 AVFoundation 宽色域颜色空间的支持。 通过提供在整个图形堆栈整个此行为可以进一步简化对具有广泛的颜色显示设备的支持。

此外， [UIKit](https://developer.xamarin.com/api/namespace/UIKit/)已修改的新工作扩展**sRGB**色彩空间，从而更便于混合中而不会显著的性能降低的广泛的颜色色域的颜色。

在处理宽颜色时，Apple 将提供下列最佳方案：

- [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/)现在使用 sRGB 颜色空间，将不再 clamp 值`0.0`到`1.0`范围。 如果应用程序依赖于以前的夹具行为，它将需要为 iOS 10 进行修改。
- 当执行自定义绘图环境将进行配置 sRGB 颜色空间`UIView`在 iPad Pro 上绘制。
- 如果该应用程序执行的自定义呈现`UIImages`，使用新[UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer)类来指定的扩展范围或标准的格式的用法。
- 当使用低级别的 API，如核心图形或裸机提供图像处理，开发人员应使用支持 16 位浮点值的扩展的范围颜色空间和像素格式。 在必要时，开发人员将不得不手动 clamp 颜色组件值。
- 核心图形、 Core 映像和金属性能着色器都提供两个颜色空间之间进行转换的新方法。

若要了解详细信息，请参阅我们[简介广泛的颜色](~/ios/platform/wide-color.md)指南。

## <a name="widget-enhancements"></a>小组件增强功能

Apple 引入了对小组件系统以确保小组件显示 10 锁定屏幕新的 iOS 存在任何背景上很好多项增强。 [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect)属性已弃用并已使用新取代[WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect)或[WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)属性。 此外，小组件现在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)属性，允许开发人员描述了多少内容是否可用并允许用户可以展开和折叠内容。

若要了解详细信息，请参阅我们[搜索和主页屏幕小组件增强功能](~/ios/platform/search/widgets.md)指南。

## <a name="additional-framework-changes"></a>附加的框架的更改

除了的主要 framework 更改和上面列出的添加件，Apple 已作出 iOS 10 中的许多其他的次要 framework 更改。

若要了解详细信息，请参阅我们[其他 Framework 更改](~/ios/platform/introduction-to-ios10/additional-framework-changes.md)指南。

## <a name="deprecated-apis"></a>弃用的 API

IOS 10 中已弃用以下 Api:

- `CKDiscoverAllContactsOperation`， `CKDiscoveredUserInfo`，`CKDiscoverUserInfosOperation`和`CKFetchRecordChangesOperation`类中已弃用 CloudKit 适用于 iOS 10。 使用[CKDiscoverAllUserIdentitiesOperation](https://developer.xamarin.com/api/type/CloudKit.CKDiscoverUserIdentitiesOperation/)， [CKUserIdentity](https://developer.xamarin.com/api/type/CloudKit.CKUserIdentity/)和[CKFetchRecordZoneChangesOperation](https://developer.xamarin.com/api/type/CloudKit.CKFetchRecordZoneChangesOperation/)类 （它们支持记录共享） 相反。
- 多个[CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) Api （例如基于区域和基于查询的订阅） 已弃用。 使用[CKRecordZoneSubscription](https://developer.xamarin.com/api/type/CloudKit.CKRecordZoneSubscription/)和[CKQuerySubscription](https://developer.xamarin.com/api/type/CloudKit.CKQuerySubscription/) Api 相反。
- [NSPersistentStoreCoordnator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/)已弃用与无处不在内容相关的符号。
- `ADBannerView``ADInterstitialAd`和相关中的符号[UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/)类已弃用。
- [SKUniform](https://developer.apple.com/reference/spritekit/skuniform)符号与浮点值已弃用。
- `UILocalNotification`， `UIMutableUserNotificationAction`， `UIMutableUserNotificationCategory`， `UIUserNotificationAction`，`UIUserNotificationCategory`和`UIUserNotificationSettings`UIKit 类已弃用。 使用[用户通知](#User-Notifications)framework 相反。
- `HandleActionForLocalNotification`， `HandleActionForRemoteNotification`，`DidReceiveLocalNotification`和`DidReceiveRemoteNotification`WatchKit 方法已弃用。 使用`HandleActionForNotification`和`DidReceiveNotification`方法相反。
- `DidReceiveLocalNotification`和`DidReceiveRemoteNotification`方法[WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate)已弃用。 创建的实例[UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) ，实现适当的方法，并将其分配给`Delegate`属性[UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter)对象。
- **游戏中心应用**已弃用并从 iOS 中删除。 如果应用使用 GameKit，它_必须_提供其自己的界面，以显示 GameKit 功能，例如排名，等等。

请参阅 Apple 的[iOS 9.3 的 iOS 10.0 API 差异](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html)弃用功能的文档的完整列表。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [什么是 iOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
