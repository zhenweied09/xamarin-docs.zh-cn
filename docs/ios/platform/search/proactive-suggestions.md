---
title: "主动建议简介"
description: "这篇文章演示如何在驱动器参与到 Xamarin.iOS 应用程序中使用主动的建议，通过允许系统以主动向用户自动地提供有用的信息。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 15f649440b2a855189acff33afcef5e8272a0769
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="introduction-to-proactive-suggestions"></a>主动建议简介

_这篇文章演示如何在驱动器参与到 Xamarin.iOS 应用程序中使用主动的建议，通过允许系统以主动向用户自动地提供有用的信息。_

新 iOS 10、 主动建议存在新闻方法来通过自动向用户在适当时间的主动存在有用的信息与 Xamarin.iOS 应用程序的用户。

iOS 10 通过允许系统以主动有用的信息自动向用户显示在适当的时间显示推动用户参与策略应用到的新方式。 为 iOS 9 提供能够向使用 Spotlight、 Handoff 和 Siri 建议应用程序添加深层搜索 (请参阅[新搜索 Api](~/ios/platform/search/index.md))，应用可以与 iOS 10 公开可向用户通过展示系统中的功能以下位置：

- 应用程序切换器
- 锁定屏幕
- CarPlay
- 映射
- Siri 交互
- QuickType 建议

应用程序公开此功能到使用技术的集合，如系统`NSUserActivity`，web 标记中，核心 Spotlight、 MapKit、 Media Player 和 UIKit。 此外，通过为应用程序提供主动建议支持，它可以获取更深入地 Siri 集成免费。

## <a name="location-based-suggestions"></a>基于位置的建议

到 iOS 10，新`NSUserActivity`类包括`MapItem`允许开发人员提供可以在其他上下文中使用的位置信息的属性。 例如，如果应用显示餐馆评论，开发人员可以设置`MapItem`属性的应用中查看用户餐馆的位置。 如果用户切换到地图应用时，会自动提供餐馆的位置。

如果应用程序支持应用程序搜索，它可以使用的新地址组件`CSSearchableItemAttributesSet`类用于指定用户可能想要访问的位置。 通过设置`MapItem`属性外，其他属性都自动填入。

除了设置`Latitude`和`Longitude`地址组件属性时，建议应用程序提供`NamedLocation`和`PhoneNumbers`属性太，因此使用 Siri 可以发起呼叫的位置。

## <a name="web-markup-based-suggestions"></a>Web 标记基于建议

iOS 9 添加到功能来丰富用户在 Spotlight 和 Safari 搜索结果中看到的内容的网站中包含结构化的数据标记 (请参阅[使用 Web 标记搜索](~/ios/platform/search/web-markup.md))。 iOS 10 添加了包括基于位置的标记的功能 (如[省略](http://schema.org/PostalAddress)由定义[Schema.org](http://schema.org/)) 来进一步增强用户体验。 例如，如果用户视图的位置标记网站上的页面，系统可以建议在打开地图时相同的位置。

## <a name="text-based-suggestions"></a>基于文本的建议

已在 iOS 10 包括扩展 UIKit [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype)属性[UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits)类可在文本区域中指定的内容的语义含义。 使用此信息就地，系统可通常自动选择相应的键盘类型、 改进自动更正建议和主动集成从其他应用程序和网站的信息。

例如，如果用户标记的文本字段中输入文本时`UITextContentType.FullStreetAddress`，系统可以建议最近查看用户的位置的自动填充的字段。

## <a name="media-based-suggestions"></a>媒体基于建议

如果应用程序可以播放媒体使用[MPPlayableContentManager](https://developer.xamarin.com/api/type/MediaPlayer.MPPlayableContentManager/) API，iOS 10 允许用户查看唱片集画面和在锁屏上播放媒体通过应用程序。

## <a name="contextual-siri-reminders"></a>上下文 Siri 提醒

允许使用 Siri 来快速使应用程序的提醒才能查看的内容在以后的日子当前正在应用中查看用户。 例如，如果它们已在应用中查看餐馆评论，它们无法调用 Siri，说*"再提醒我有关此当我回到家。"* 在应用程序也使用 Siri 会生成提醒，其中链接添加到评审。

## <a name="contact-based-suggestions"></a>基于联系人的建议

允许此应用的联系人 （和联系人的相关的信息） 显示在**联系**存储在系统中所有用户的联系人以及 iOS 设备上应用程序。

## <a name="ride-sharing-based-suggestions"></a>写共享基于的建议

如果持续一段时间共享应用程序使用[MKDirectionsRequest](https://developer.xamarin.com/api/type/MapKit.MKDirectionsRequest/) API，iOS 10 将显示为应用程序切换器中的一个选项有时时用户很可能需要持续一段时间。 应用程序必须还注册为持续一段时间共享应用程序通过指定`MKDirectionsModeRideShare`为[MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html)中的键其`Info.plist`文件。

如果应用程序仅支持共享持续一段时间，以将开始系统建议*"Get 到持续一段时间..."*，如果支持其他类型的路由方向 （如 Walking 或自行车），则系统将使用*"获取路线到..."*

> [!IMPORTANT]
> **注意：** [MKMapItem](https://developer.xamarin.com/api/type/MapKit.MKMapItem/)应用将会接收的对象可能不包括经度和纬度的信息，并且需要进行地理编码。

## <a name="implementing-proactive-suggestions"></a>实现主动建议

添加主动建议向 Xamarin.iOS 应用程序的支持是通常一样轻松，实现几个 Api 或几个 api 的应用程序可能已实现扩展。

主动建议处理应用在三个方面：

- **`NSUserActivity` 和 Schema.org**  -  `NSUserActivity`可帮助了解哪些用户当前正在使用在屏幕的信息的系统。 Schema.org 将类似的功能添加到网页。
- **位置建议**-如果应用程序提供或使用基于位置信息，这些 API 扩展提供的新方法以在应用间共享此信息。
- **媒体应用程序建议**-系统时，才能升级应用程序和其媒体内容基于与 iOS 设备的用户的交互的上下文。

和中应用程序通过实现以下受支持：

- **Handoff**  -  `NSUserActivity`已添加到 iOS 8 以支持 Handoff，允许开发人员可以在一个设备上启动活动，然后继续在另一台中 (请参阅[简介 Handoff](~/ios/platform/handoff.md))。
- **聚光灯搜索**-iOS 9 添加应用程序内容将从提升内使用对 Spotlight 搜索结果的能力`NSUserActivity`(请参阅[使用核心 Spotlight 搜索](~/ios/platform/search/corespotlight.md))。
- **上下文 Siri 提醒**-在 iOS 10，`NSUserActivity`已扩展为允许使用 Siri 来快速使应用程序的提醒才能查看的内容用户当前正在查看在应用程序在更高版本的日期。
- **位置建议**-iOS 10 增强了`NSUserActivity`捕获查看在应用程序内的位置并将它们升级在整个系统的多个位置。
- **上下文 Siri 请求** -  `NSUserActivity`提供到显示的信息在应用程序内部使用 Siri，从而让用户可以获得指导性内容或呼叫的位置调用 Siri 从应用中的上下文。
- **联系交互**-iOS 10 中的新增功能`NSUserActivity`允许通信应用程序从作为备用的通信方法 （在联系人应用程序中） 联系卡提升。

所有这些功能有一个共同点，它们都使用`NSUserActivity`中一个窗体或另一个用来提供相应的功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如前所述，`NSUserActivity`可帮助了解哪些用户当前正在使用在屏幕的信息的系统。 `NSUserActivity` 轻量状态缓存机制它们浏览应用程序中捕获用户的活动。 例如，查看餐馆应用程序：

[![](proactive-suggestions-images/activity02.png "缓存机制 NSUserActivity 轻量状态")](proactive-suggestions-images/activity02.png#lightbox)

以下的交互：

1. 用户会使用应用程序，如`NSUserActivity`创建以重新创建的应用程序更高版本的状态。
2. 如果一家餐厅搜索用户，则遵循同一模式，创建活动。
3. 和同样，当用户查看结果。 此最后一种情况，用户正在查看位置且在 iOS 10 中，系统更了解某些概念 （如位置或通信交互）。

要进一步查看在最后一个屏幕中：

[![](proactive-suggestions-images/activity03.png "NSUserActivity 详细信息")](proactive-suggestions-images/activity03.png#lightbox)

此处创建应用程序`NSUserActivity`和更高版本重新创建状态的信息填充。 应用程序还包含一些元数据，例如位置的名称和地址。 与此活动中创建，应用可以让 iOS 知道它表示用户的当前状态。

应用程序然后确定是否将播发无线以便提交，另存为位置建议的临时值或添加到在搜索结果中显示的设备上的 Spotlight 索引活动。

有关 Handoff 和 Spotlight 搜索的详细信息，请参阅我们[简介 Handoff](~/ios/platform/handoff.md)和[iOS 9 新搜索 Api](~/ios/platform/search/index.md)指南。

### <a name="creating-an-activity"></a>创建活动

在创建活动之前, 活动类型标识符将需要创建来标识它。 活动类型标识符是添加到一个短字符串`NSUserActivityTypes`的应用程序的数组`Info.plist`用于唯一标识给定的用户活动类型的文件。 在数组的应用程序支持，并公开给应用程序搜索的每个活动中，将一个项。 请参阅我们[创建活动类型标识符引用](~/ios/platform/search/nsuseractivity.md)有关详细信息。

看一个活动的一个示例：

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Inform system of Activity
activity.BecomeCurrent();
```

使用活动类型标识符创建一个新的活动。 接下来，定义活动某些元数据创建因此可以在以后还原此状态。 然后，该活动指定有意义的标题以及连接到的用户信息。 最后，一些功能已启用，并且活动发送到系统。

上面的代码中进一步得到增强，包括通过进行以下更改提供给活动的上下文的元数据：

```csharp
...

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Inform system of Activity
activity.BecomeCurrent();
```

如果开发人员能够显示与应用相同的信息的网站，应用程序可以包括 URL 和可以在没有安装 （通过 Handoff) 的应用程序的其他设备上显示的内容：

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>还原活动

若要响应用户点击搜索结果 (`NSUserActivity`) 对于应用程序中，编辑**AppDelegate.cs**文件，并重写`ContinueUserActivity`方法。 例如:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    }

    return true;
}
```

开发人员将需要确保此路径是相同的活动类型标识符 (`com.xamarin.platform`) 作为上面创建的活动。 该应用使用中存储的信息`NSUserActivity`以返回到用户的地方开始还原状态。

### <a name="benefits-of-creating-an-activity"></a>创建活动的优点

使用上面介绍的代码的最少工作量，应用现已能够充分利用的三个新的 iOS 10 功能：

- **Handoff**
- **Spotlight 搜索**
- **上下文 Siri 提醒**

以下部分将看一看启用两个其他新的 iOS 10 功能：

- **位置建议**
- **上下文 Siri 请求**

### <a name="location-based-suggestions"></a>基于位置的建议 

需要餐馆搜索应用上面的示例。 如果已实现`NSUserActivity`和正确填充所有的元数据和属性，用户将可以执行以下操作：

1. 在应用程序，他们希望以满足在友元中找到一家餐厅。
2. 当用户移动应用中使用多任务应用程序切换器离开时，系统将自动显示建议 （位于屏幕底部） 以获取到餐馆使用其收藏导航布局应用程序的方向。
3. 如果用户将切换到邮件应用并开始键入*"让我们在符合"*，QuickType 键盘将自动建议在餐馆的地址中粘贴。
4. 如果用户切换到地图应用时，餐馆的地址自动建议作为目标。
5. 这甚至适用于第三方应用程序 (该支持`NSUserActivity`)，因此用户可以切换到持续一段时间共享应用程序和餐馆的地址自动建议作为那里的目标以及。
6. 它还提供了上下文的 Siri，以便用户可以在餐馆应用内调用 Siri 并询问*"获取路线..."* ，并使用 Siri 将提供到餐馆查看用户的说明。

所有上述功能共同具有相同的一件事情，它们都指示建议最初来源于何处。 对于上述示例中，它是虚构的餐馆查看应用程序。

iOS 10 已得到增强，若要为通过一些小的修改并添加到现有框架的应用程序启用此功能：

- `NSUserActivity` 已为捕获查看在应用程序内的位置信息的其他字段。
- 几项补充进行了 MapKit 和 CoreSpotlight 捕获位置。
- 位置感知功能已添加到使用 Siri、 地图、 键盘、 多任务和系统中的其他应用。

若要实现基于位置的建议，请使用上面介绍的相同活动代码启动：

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");

// Inform system of Activity
activity.BecomeCurrent();
```

如果应用程序使用 MapKit，所以只要添加当前 map`MKMapItem`到的活动：

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

如果应用程序不使用 MapKit，它可以采用应用搜索，并指定位置的以下新属性：

```csharp
// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
...

attributes.NamedLocation = "Apple Inc.";
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

详细了解上面的代码。 首先，每个实例中，要求的位置名称：

```csharp
attributes.NamedLocation = "Apple Inc.";
```

然后，基于文本说明的位置时需要基于文本实例 （例如 QuickType 键盘中）：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

纬度和经度是可选的但确保用户被路由到应用程序想要将它们发送到，因此它应该包含的确切位置：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

通过设置电话号码，应用程序可以访问使用 Siri 以便用户可通过说类似，从应用调用 Siri *"调用该邻居"*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最后，应用程序可以指示实例是否适合导航和电话呼叫：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>实现联系人交互

新 iOS 10 中, 通信应用深度集成到联系人卡片中的联系人应用程序。 对于已实现联系人交互的应用程序，用户可以将给定应用程序的信息添加到特定人员他们的联系人。 并且如果，例如，它们按顶部发送消息的卡的快速操作按钮，将作为一个选项，将从消息发送列出附加应用程序。

如果选择第三方应用程序，则它可以记住和显示为消息的下次用户想要与他们联系的给定的人员的默认方法。

联系人交互实现在应用程序中使用`NSUserActivity`和在 iOS 10 中引入的新意向 framework。 有关使用意向的详细信息，请参阅我们[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)和[实现 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)指南。

#### <a name="donating-interactions"></a>捐赠交互

看看如何应用程序可以指定用于交互：

[![](proactive-suggestions-images/activity04.png "捐赠交互概述")](proactive-suggestions-images/activity04.png#lightbox)

应用程序创建`INInteraction`对象，其中包含**意向**(`INIntent`)，**参与者**和**元数据**。 **意向**表示用户操作，例如视频呼叫或发送短信。 **参与者**包含接收通信的人员。 **元数据**定义添加信息，例如成功发送消息，等等。

开发人员永远不会直接创建的实例`INIntent`或`INIntentResponse`，它们将使用 （基于代表用户完成应用程序的任务） 的特定子类之一，从这些父类继承。 例如，`INSendMessageIntent`和`INSendMessageIntentResponse`用于发送短信。 

完全填充交互之后, 调用`DonateInteraction`方法来通知系统的交互是可供使用。

交互时用户与联系人卡片中的应用程序交互时，获取与捆绑`NSUserActivity`，然后用于启动应用程序：

[![](proactive-suggestions-images/activity05.png "获取与用于启动应用程序 NSUserActivity 捆绑在交互")](proactive-suggestions-images/activity05.png#lightbox)

看一看发送的消息意图的下面的示例：

```csharp
using System;
using Foundation;
using UIKit;
using Intents;

namespace MonkeyNotification
{
    public class DonateInteraction
    {
        #region Constructors
        public DonateInteraction ()
        {
        }
        #endregion

        #region Public Methods
        public void SendMessageIntent (string text, INPerson from, INPerson[] to)
        {

            // Create App Activity
            var activity = new NSUserActivity ("com.xamarin.message");

            // Define details
            var info = new NSMutableDictionary ();
            info.Add (new NSString ("message"), new NSString (text));

            // Populate Activity
            activity.Title = "Sent MonkeyChat Message";
            activity.UserInfo = info;

            // Enable capabilities
            activity.EligibleForSearch = true;
            activity.EligibleForHandoff = true;
            activity.EligibleForPublicIndexing = true;

            // Inform system of Activity
            activity.BecomeCurrent ();

            // Create message Intent
            var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);

            // Create Intent Reaction
            var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);

            // Create interaction
            var interaction = new INInteraction (intent, response);

            // Donate interaction to the system
            interaction.DonateInteraction ((err) => {
                // Handle donation error
                ...
            });
        }
        #endregion
    }
}
```

查看详细信息在此代码，它会创建并填充的实例`NSUserActivity`(中所示[创建活动](#Creating-an-Activity)上面一节)。 接下来，它创建的实例`INSendMessageIntent`(其继承自`INIntent`) 并使用填充它要发送的消息的详细信息：

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

`INSendMessageIntentResponse`来创建和传递`NSUserActivity`上面创建：

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

`INInteraction`从发送消息意图生成 (`INSendMessageIntent`) 和响应 (`INSendMessageIntentResponse`) 只需创建：

```csharp
var interaction = new INInteraction (intent, response);
```

最后，系统是在交互的通知：

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
    // Handle donation error
    ...
});
```

完成处理程序传入应用程序可以响应捐赠成功或失败。

### <a name="activities-best-practices"></a>活动的最佳做法

在处理活动时，Apple 提供的以下最佳实践建议：

- 使用`NeedsSave`延迟负载更新。
- 确保保留对当前活动的强引用。
- 仅传输包括刚好够用的信息，以还原状态的小负载。
- 确保活动类型标识符是唯一的描述性，通过使用反向 DNS 表示法来指定它们。 

## <a name="schemaorg"></a>Schema.org

如上所示，`NSUserActivity`可帮助了解哪些用户当前正在使用在屏幕的信息的系统。 Schema.org 将类似的功能添加到网页。

Schema.org 可以提供相同类型的基于位置到网站的交互。 Apple 设计新的位置建议，以与它们在本机应用程序在 Safari 中查看时同样工作。

某些 Schema.org 背景信息：

- 它提供了一种打开 web 标记词汇标准。
- 它通过包括网页上的结构化元数据适用。
- 有超过 500 的架构表示可用的各种概念。
- 通过实现其网站上，开发人员可以获取的一些的使用好处`NSUserActivity`中的本机应用。

架构在类似结构，其中特定类型，如树中布置*餐馆*，继承更泛型类型如*本地业务*。 有关详细信息，请参阅[Schema.org](http://schema.org)。

例如，如果 web 页包含以下数据：

```xml
<script type="application/ld+json>
{
    "@context":"http://schema.org",
    "@type":"Restaurant",
    "telephone":"(415) 781-1111",
    "url":"https://www.yanksing.com",
    "address":{
        "@type":"PostalAddress",
        "streetAddress":"101 Spear St",
        "addressLocality":"San Francisco",
        "postalCode":"94105",
        "addressRegion":"CA"
    },
    "aggregateRating":{
        "@type":"AggregateRating",
        "ratingValue":"3.5",
        "reviewCount":"2022"
    }
}
</script>
```

如果用户查看过此页在 Safari 中，然后切换到另一个应用程序，将捕获位置信息的页面，并将其作为提供位置建议在系统的其他部分 （如上面的活动中所示） 中。

Safari 将提取符合以下架构属性的任何 web 页面上的任何内容：

- **PostalAddress**
- **GeoCoordinates**
- 一个电话属性中。

有关详细信息，请参阅我们[使用 Web 标记搜索](~/ios/platform/search/web-markup.md)指南。

## <a name="consuming-location-suggestions"></a>使用位置建议

此下一节将介绍使用具有来自系统 （如地图应用程序） 或其他第三方应用程序的其他部分的位置建议。

有两种主要方法应用程序可以使用位置建议：

- 通过 QuickType 键盘。
- 直接通过使用路由应用中的位置信息。

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>位置建议和 QuickType 键盘

如果应用程序处理中基于文本格式的地址，该应用可以利用的位置通过 QuickType UI 的建议。 iOS 10 展开 QuickType UI 具有以下功能：

- 应用程序可以在 UI 中添加有关文本字段的语义目的的提示。
- 应用程序可以在应用程序获取主动的建议。
- 应用程序可以受益于增强自动更正。

新`TextContentType`iOS 10 中的文本字段控件的属性允许开发人员定义的值的用户将在给定字段中输入的语义意图。 例如:

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

将告诉系统应用程序要求用户在给定字段中输入完整街道地址。 这将允许 QuickType 键盘自动提供键盘位置建议，当用户在此字段中输入一个值。

以下是一些较常见的类型可供开发人员在`UITextContentType`静态类：

* `Name`
* `GivenName`
* `FamilyName`
* `Location`
* `FullStreetAddress`
* `AddressCityAndState`
* `TelephoneNumber`
* `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>路由的应用程序和位置建议

本部分将看看在路由的应用内使用直接从位置建议。 对于路由以添加此功能的应用，开发人员将利用现有`MKDirectionsRequest`framework，如下所示：

- 若要将提升的应用程序中多任务。
- 若要将应用程序注册为路由的应用程序。
- 若要处理启动应用程序与 MapKit`MKDirectionsRequest`对象。
- 若要使 iOS 能够了解如何在适当的时间，建议向用户应用程序基于用户参与度。

如果应用程序启动时 MapKit`MKDirectionsRequest`对象，它应自动启动提供的用户说明进行操作的请求的位置，或提供的用户界面可轻松地让用户开始获取说明。 例如:


```csharp
using System;
using Foundation;
using UIKit;
using MapKit;
using CoreLocation;

namespace MonkeyChat
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate, IUISplitViewControllerDelegate
    {
        ...
        
        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            if (MKDirectionsRequest.IsDirectionsRequestUrl (url)) {
                var request = new MKDirectionsRequest (url);
                var coordinate = request.Destination?.Placemark.Location?.Coordinate;
                var address = request.Destination.Placemark.AddressDictionary;
                if (coordinate.IsValid()) {
                    var geocoder = new CLGeocoder ();
                    geocoder.GeocodeAddress (address, (place, err) => {
                        // Handle the display of the address

                    });
                }
            }

            return true;
        }
    }       
}
```

详细了解此代码。 它测试以查看它是否是有效的目标请求：

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

如果是，则它会创建`MKDirectionsRequest`从 URL:

```csharp
var request = new MKDirectionsRequest(url);
```

新在 iOS 10，应用程序可以将发送的导致开发人员需要将编码地址中不具有地域坐标的地址：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="media-app-suggestions"></a>媒体应用程序建议

如果应用程序处理任何类型的媒体如播客应用或流式处理的媒体内容，如音频或视频，ios 10，它可以利用媒体建议的系统中。

对于处理媒体的应用程序，iOS 支持以下行为：

- iOS 提升的用户很可能想使用基于其以前的行为的应用。
- Spotlight 和今天视图中，将显示与应用的建议。
- 如果应用满足了以下触发器之一，它可能被提升到锁定屏幕建议：
    - 后插入耳机或蓝牙设备建立连接。
    - 后一辆汽车中获取。
    - 在到达在家或工作。 

通过在 iOS 10 中包括的简单 API 调用，开发人员可以创建媒体应用程序的用户提供更加吸引人的锁定屏幕体验。 通过使用`MPPlayableContentManager`类来管理播放媒体、 完全媒体控件 （如那些由音乐应用） 将显示应用程序在锁定屏幕上。


```csharp
using System;
using MediaPlayer;
using UIKit;

namespace MonkeyPlayer
{
    public class PlayableContentDelegate : MPPlayableContentDelegate
    {
        #region Constructors
        public PlayableContentDelegate ()
        {
        }
        #endregion

        #region Override methods
        public override void InitiatePlaybackOfContentItem (MPPlayableContentManager contentManager, Foundation.NSIndexPath indexPath, Action<Foundation.NSError> completionHandler)
        {
            // Access the media item to play
            var item = LoadMediaItem (indexPath);

            // Populate the lock screen
            PopulateNowPlayingItem (item);

            // Prep item to be played
            var status = PreparePlayback (item);

            // Call completion handler
            completionHandler (null);
        }
        #endregion

        #region Public Methods
        public MPMediaItem LoadMediaItem (Foundation.NSIndexPath indexPath)
        {
            var item = new MPMediaItem ();

            // Load item from media store
            ...

            return item;
        }

        public void PopulateNowPlayingItem (MPMediaItem item)
        {
            // Get Info Center and album art
            var infoCenter = MPNowPlayingInfoCenter.DefaultCenter;
            var albumArt = (item.Artwork == null) ? new MPMediaItemArtwork (UIImage.FromFile ("MissingAlbumArt.png")) : item.Artwork;

            // Populate Info Center
            infoCenter.NowPlaying.Title = item.Title;
            infoCenter.NowPlaying.Artist = item.Artist;
            infoCenter.NowPlaying.AlbumTitle = item.AlbumTitle;
            infoCenter.NowPlaying.PlaybackDuration = item.PlaybackDuration;
            infoCenter.NowPlaying.Artwork = albumArt;
        }

        public bool PreparePlayback (MPMediaItem item)
        {
            // Prepare media item for playback
            ...

            // Return results
            return true;
        }
        #endregion
    }
}
```

## <a name="summary"></a>摘要

本文已涉及主动建议，并介绍了开发人员可以如何使用它们驱动器流量传送到 Xamarin.iOS 应用程序。 它涵盖实现主动建议的步骤，并显示使用准则。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [SiriKit 编程指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
