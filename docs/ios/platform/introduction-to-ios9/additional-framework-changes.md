---
title: "其他 iOS 9 框架更改"
description: "这篇文章介绍了其他的次要更改或增强现有框架 ios 9 功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 82c6c2451deafb8a4314254a8138804d927c9bbf
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="additional-ios-9-frameworks-changes"></a>其他 iOS 9 框架更改

_这篇文章介绍了其他的次要更改或增强现有框架 ios 9 功能。_

[ ![](additional-framework-changes-images/ios9-sml.png "iOS 9 Logo")](additional-framework-changes-images/ios9.png)

除了对 iOS 的主要更改，Apple 已在 iOS 9 中进行修改和对多个现有框架的改进。

## <a name="av-foundation-framework-additions"></a>AV Foundation Framework 添加件

在 AV Foundation framework 中， [AVSpeechSynthesisVoice](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechSynthesisVoice/)类现在允许你通过以指定一种声音除了语言标识符。

例如，以下代码将获取所有可用的声音的列表：

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

然后可以通过设置其作为使用其中一个从列表中的语音`Voice`实例的属性[AVSpeachUtterance](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechUtterance/)类。

[AVQueuePlayer](https://developer.xamarin.com/api/type/AVFoundation.AVQueuePlayer/)类现在支持 internet 流式处理和基于文件的媒体的混合队列中。 以前的版本无法只队列媒体上的相同的类型。

有关详细信息，请参阅 Apple 的[AVSpeechSynthesisVoice 引用](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice)。

## <a name="avkit-framework-additions"></a>AVKit Framework 添加件

若要使用新的图片中图片 (PIP) 功能，AVKit framework 包括新`AVPictureInPictureController`和[AVPlayerViewController](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/)类：

- **AVPictureInPictureController** -此类允许 iOS 9 应用以响应用户启动的 iPad 上的浮动的可调整大小 PIP 窗口中的视频播放。
- **AVPlayerViewController** -管理`AVPlayer`控制器来显示在 iPad 上的 PIP 窗口浮动的可调整大小的视频。

有关详细信息，请参阅我们[适用于 iPad 的多任务](~/ios/platform/introduction-to-ios9/index.md#multitasking)文档和 Apple 的[AVPictureInPictureController 引用](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController)和[AVPlayerViewController 引用](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController).

## <a name="introducing-cloudkit-web-services"></a>引入 CloudKit Web 服务

该访问 iCloud，CloudKit framework 简化了开发应用程序。 这包括应用程序数据和资产权限，以及能够安全地存储应用程序信息的检索。 此工具包提供用户的匿名层通过允许访问其 iCloud Id 与应用程序而不用共享个人信息。

新_CloudKit Web 服务_framework 提供了一个 JavaScript 库 (CloudKit JS)，可以纳入你的网站以提供对相同 CloudKit 基于数据和作为你的 Xamarin.iOS 应用程序内容的访问。

> [!IMPORTANT]
> **注意：**可以访问、 显示或更新使用 CloudKit JS CloudKit 数据库中的内容之前，你必须具有以前定义该数据库的架构。




有关详细信息，请参阅以下文档：

- [简介 CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) -我们介绍了如何在 Xamarin.iOS 应用程序中使用 CloudKit。
- [CloudKit 快速启动](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)-CloudKit 的 Apple 的简介。
- [CloudKit JS 引用](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359)-Apple 的 CloudKit JS 文档。
- [CloudKit Web 服务引用](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240)-Apple 的引用，描述 CloudKit 的 HTTP 接口。
- [CloudKit 目录： An introduction to CloudKit （Cocoa 和 JavaScript） 简介](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599)-使用 CloudKit 和 CloudKit JS Apple 的示例应用程序。

## <a name="foundation-framework-additions"></a>Foundation Framework 添加件

Apple iOS 9 中包含对 Foundation framework 的以下更改：

### <a name="changes-to-nsbundle"></a>对 NSBundle 更改

已对以下更改[NSBundle](https://developer.xamarin.com/api/type/Foundation.NSBundle/) ios 9 的类：

* `GetPreservationPriorityForTag (NSString tag)` -获取具有给定标记的资源的当前保留优先级。 有效值为范围内`0.0`到`1.0`，将首先清除优先级最低的资源。
* `SetPreservationPriorityForTag (double priority, NSSet tags)` -设置具有给定标记的资源的当前保留优先级。 有效值为范围内`0.0`到`1.0`，将首先清除优先级最低的资源。

有关详细信息，请参阅 Apple 的[NSBundle 引用](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle)。

### <a name="changes-to-nsprocessinfo"></a>对 NSProcessInfo 更改

在 iOS 设备上运行每个进程都有一个，_进程信息代理_(PIA)。 使用[NSProcessInfo](https://developer.xamarin.com/api/type/Foundation.NSProcessInfo/)类以提供有关当前的 PIA 和控制电源和散热管理给定进程的信息。

例如，若要控制自动终止进程可以使用下面的代码：

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

有关详细信息，请参阅 Apple 的[NSProcessInfo 引用](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo)。

### <a name="reacting-to-low-power-mode"></a>对作出反应到低能耗模式

使用`LowPowerModeEnabled`属性[NSProcessInfo](https://developer.xamarin.com/api/type/Foundation.NSProcessInfo/)类以确定是否已在应用程序运行在 iOS 设备上启用低功耗模式。 例如:

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>HealthKit 框架将更改

Apple 包括以下更改到[HealthKit](https://developer.xamarin.com/api/namespace/HealthKit/) iOS 9 中的框架：

- 支持大容量删除和删除跟踪 HealthKit 数据库中的条目。 请参阅 Apple 的[HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject)， [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery)和[HKHealthStore 类引用](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708)有关详细信息。
- 已添加到新的跟踪类别和特征`HKQuantityTypeIdentifier`类 (如`UVExposure`) 并对其`HKCategoryTypeIdentifier`类 (如`OvulationTestResult`)。 请参阅 Apple 的[HealthKit 常量引用](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710)有关详细信息。

请参阅我们[简介 HealthKit](~/ios/platform/healthkit.md) HealthKit Xamarin.iOS 中使用的详细信息的文档。

## <a name="local-authentication-framework-changes"></a>本地身份验证框架更改

Apple 包括以下更改到[本地身份验证](https://developer.xamarin.com/api/namespace/LocalAuthentication/)iOS 9 中的框架：

- 使用`EvaluateAccessControl`和`EvaluatePolicy`方法[LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/)类，现在，你可以重复使用 Touch ID 的匹配上一个成功解锁尝试。
- 能够获取当前已注册指的列表。
- 用于跟踪为指添加或删除从身份验证时支持。
- 能够使用_身份验证上下文_Keychain 调用以及对评估密钥链访问控制的支持在列表中。
- 取消用户提示消息代码中的功能。

请参阅我们[简介 Touch ID](~/ios/platform/touchid.md)文档使用在 Xamarin.iOS Touch ID 的详细信息。

### <a name="lacontext-changes"></a>LAContext 更改

已对以下更改[LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) ios 9 的类：

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -返回的最大可重复使用 touch ID 身份验证的时间量。
- **EvaluatedPolicyDomainState** -获取或设置评估策略的状态。
- **MaxBiometryFailures** -已在 iOS 9 中已弃用。
- **TouchIdAuthenticationAllowableReuseDuration**获取或设置可重复使用 touch ID 身份验证的时间量。
- **EvaluateAccessControl** -以异步方式计算结果的身份验证策略。
- **使无效**-失效给定的 touch ID 身份验证。
- **IsCredentialSet** -返回`true`如果当前设置凭据。
- **SetCredentialType**设置给定的凭据类型。

请参阅 Apple 的[LAContext 引用](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:)有关详细信息。

## <a name="mapkit-framework-changes"></a>MapKit 框架将更改

Apple 包括以下更改到[MapKit](https://developer.xamarin.com/api/namespace/MapKit/) iOS 9 中的框架：

- MapKit 现在提供了支持用于直接在传输过程的说明启动映射应用程序和查询传输到达 （η） 使用的估计时间[MKLaunchOptions](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/)和[MKDirections](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/)类。
- 返回 MapKit 的搜索结果和[CLGeocoder](https://developer.xamarin.com/api/type/CoreLocation.CLGeocoder/)类还可以提供结果的时区。
- 你现在可以完全自定义 iOS 应用程序使用提供的地图批注`DetailCalloutAccessoryView`属性[MKAnnotationView](https://developer.xamarin.com/api/type/MapKit.MKAnnotationView/)类。

请参阅我们[iOS 地图](~/ios/user-interface/controls/ios-maps/index.md)和[演练-浏览批注和覆盖层中 MapKit](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md)使用地图和 Xamarin.iOS 和 Apple中的批注的详细信息的文档[CLGeocoder 引用](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder)有关详细信息。

## <a name="passkit-framework-additions"></a>PassKit Framework 添加件

Apple 包括以下更改到[PassKit](https://developer.xamarin.com/api/namespace/PassKit/) iOS 9 中的框架：

- Apple Pay 现在支持存储借方和以及发现卡的信用卡。 请参阅**付款网络**Apple 的部分[PKPaymentRequest 类引用](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832)有关详细信息。
- 从直接在 Xamarin.iOS 应用程序，你现在可以添加付款网络和卡颁发者到 Apple Pay。 请参阅 Apple 的[PKAddPaymentPassViewController 类引用](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116)有关详细信息。

请参阅我们[简介 PassKit](~/ios/platform/passkit.md) PassKit Xamarin.iOS 中使用的详细信息的文档。

## <a name="safari-services-framework-additions"></a>Safari 服务框架添加件

Apple 包括以下更改到[Safari 服务](https://developer.xamarin.com/api/namespace/SafariServices/)iOS 9 中的框架：

- 你现在可以使用新[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)类来显示一个 Xamarin.iOS 应用程序中的 web 内容。 它提供的功能与 Safari 应用中共享网站数据和 cookie，并包括几个 Safari 的功能 （如读取器和自动填充）。 [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)功能**完成**在完成查看 web 内容时将返回到你的应用程序的用户的按钮。

因为[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)类定制用于显示 web 内容的单个页面，您应该考虑使用它来替换任何[WKWebKit](https://developer.xamarin.com/api/type/WebKit.WKWebView/)或[UIWebView](https://developer.xamarin.com/api/type/UIKit.UIWebView/)你现有的 Xamarin.iOS 应用程序内的控件。

### <a name="displaying-a-website"></a>显示网站

下面的代码演示了调用[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)从内另一个视图控制器：

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>UIKit 框架将更改

Apple 包含很多增强功能的一些元素[UIKit](https://developer.xamarin.com/api/namespace/UIKit/) ios 9 的框架。 下列各节将详细介绍这些更改。

### <a name="3d-touch-events"></a>3D Touch 事件

新 iOS 9 iPhone 6s 和 iPhone 6s Plus，3D Touch 压力敏感手势添加到你的 iOS 应用。 因此，如果在 iOS 9 （或更高版本） 上运行你的应用和 iOS 设备是否可以支持 3D Touch，压力中的更改将导致`TouchesMoved`引发事件。 

由于此更改的行为，而您 iOS 应用程序应准备为`TouchesMoved`更频繁调用的事件即使 X / Y 坐标，未更改。 

有关详细信息，请参阅我们[简介 3D Touch](~/ios/platform/3d-touch.md)指南。

### <a name="document-open-in-place-functionality"></a>文档打开的就地功能

通过使用`FinishedLaunching (application, launchOptions)`或`WillFinishLaunching (Application, launchOptions)`方法[UIApplicationDelegate](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/)类，现在可以打开文档，并对其进行修改，在 （而不是副本上工作） 的位置。

为了支持新打开的就地功能，添加`LSSupportsOpeningDocumentsInPlace`到 Xamarin.iOS 应用程序的密钥**Info.plist**文件中使用的值`YES`。

请参阅 Apple 的[UIApplicationDelegate 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate)有关详细信息。

### <a name="enhanced-touch-events"></a>增强的触控事件

Apple 提供了几项增强功能 Touch 事件在 iOS 9 中。 这些包括能够使用 Touch 预测并有权访问显示刷新之间的中间收尾工作。

请参阅 Apple 的[适用于 iOS 的事件处理指南](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html)有关详细信息。

### <a name="fetching-tailored-content"></a>提取定制的内容

新`NSDataAsset`类允许 Xamarin.iOS 应用程序以提取定制的内存和图形功能的 iOS 设备上当前运行的内容。

### <a name="new-layout-anchors"></a>新的布局锚

新`NSLayoutAnchor`和`NSLayoutDimension`布局定位点类可与新的定位点属性的[UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)类 (如`LeadingAnchor`和`WidthAnchor`) 以在 iOS 9 中轻松布局。

请参阅我们[简介统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)文档使用自动布局和大小类中的 Xamarin.iOS 应用程序和 Apple 的详细信息[NSLayoutAnchor 引用](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor)， [NSLayoutDimension 引用](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension)和[UIView 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView)有关详细信息。

### <a name="new-readable-content-margins"></a>新的可读内容边距

新`UILayoutGuide`类可以用于提供可读内容的边距，并定义在视图内的内容的绘图区域。 请参阅 Apple 的[UILayoutGuide 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide)有关详细信息。

### <a name="text-input-in-notifications-modifications"></a>在通知修改的文本输入

[UIUserNotificationAction](https://developer.xamarin.com/api/type/UIKit.UIUserNotificationAction/)类有一个新的`Behavior`可以用于支持从通知文本输入的属性。

### <a name="uiapplicationdelegate-changes"></a>UIApplicationDelegate 更改

虽然不正式否决 Apple，它们建议到的所有调用都替换为`FinishedLaunching (UIApplication application)`方法[UIApplicationDelegate](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/)类具有`FinishedLaunching (UIApplication application, NSDictionary launchOptions)`或`WillFinishLaunching (UIApplication application, NSDictionary launchOptions)`方法。

请参阅 Apple 的[UIApplicationDelegate 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate)有关详细信息。

### <a name="uikit-dynamics-changes"></a>UIKit Dynamics 更改

Apple iOS 9 中包含对 UIKit Dynamics 的以下更改：

- Dynamics 现在为非矩形冲突边界提供支持。
- 将新的、 可自定义`UIFieldBehavior`类用于支持不同的字段类型。
- 已添加到其他的附件类型`UIAttachmentBehavior`类。

请参阅 Apple 的[UIAttachment 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior)有关详细信息。

### <a name="uipickerview-and-uidatepicker-changes"></a>UIPickerView 和 UIDatePicker 更改

在 iOS 9 之前, [UIPickerView](https://developer.xamarin.com/api/type/UIKit.UIPickerView/)和[UIDatePicker](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/)控件无法调整大小，将自动调整大小以填充其容器 （通常的宽度的 iOS 设备和应用程序的宽度在上运行）。

在 iOS 9 中，此自动调整大小不再发生，并且控件将呈现在所有的 iOS 设备，而不考虑屏幕的大小和方向上 320 点宽度。

若要更正这种情况下，使用自动布局和大小类，以固定控件与父容器 （视图） 的边缘的宽度，并指定所需的高度。 请参阅我们[简介统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)文档在 Xamarin.iOS 应用程序中使用自动布局和大小类的详细信息。

### <a name="new-uitextinputassistantitem-class"></a>新 UITextInputAssistantItem 类

使用新`UITextInputAssistantItem`到布局栏按钮组中的类_快捷工具栏_。 快捷工具栏是可用于提供键入快捷方式软键盘的新区域。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 简介](~/ios/platform/introduction-to-ios9/index.md)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [什么是在 iOS 9.0 新增](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
