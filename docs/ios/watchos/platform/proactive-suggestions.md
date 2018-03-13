---
title: "主动建议"
description: "这篇文章演示如何在驱动器 engagement watchOS 3 应用程序中使用主动的建议，通过允许系统以主动向用户自动地提供有用的信息。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: f9711cc39662a7e77d926551a0d2b49363d8ec4d
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="proactive-suggestions"></a>主动建议

_这篇文章演示如何在驱动器 engagement watchOS 3 应用程序中使用主动的建议，通过允许系统以主动向用户自动地提供有用的信息。_


新 watchOS 3，用户若要与 Xamarin.iOS 应用程序沟通通过自动向用户在适当时间的主动存在有用的信息的主动建议存在新闻方法。


## <a name="about-proactive-suggestions"></a>有关主动建议

到 watchOS 3，新`NSUserActivity`包括`MapItem`属性，它允许该应用程序提供可以在其他上下文中使用的位置信息。 例如，如果应用显示酒店介绍并提供了`MapItem`位置，如果用户切换为地图应用，它们只需查看酒店则位置应为可用。

应用程序公开此功能到使用技术的集合，如系统`NSUserActivity`，MapKit、 Media Player 和 UIKit。 此外，通过为应用程序提供主动建议支持，它可以获取更深入地 Siri 集成免费。

## <a name="location-based-suggestions"></a>基于位置的建议

到 watchOS 3，新`NSUserActivity`类包括`MapItem`允许开发人员提供可以在其他上下文中使用的位置信息的属性。 例如，如果应用显示餐馆评论，开发人员可以设置`MapItem`属性的应用中查看用户餐馆的位置。 如果用户切换到地图应用时，会自动提供餐馆的位置。

如果应用程序支持应用程序搜索，它可以使用的新地址组件`CSSearchableItemAttributesSet`类用于指定用户可能想要访问的位置。 通过设置`MapItem`属性外，其他属性都自动填入。

除了设置`Latitude`和`Longitude`地址组件属性时，建议应用程序提供`NamedLocation`和`PhoneNumbers`属性太，因此使用 Siri 可以发起呼叫的位置。

## <a name="contextual-siri-reminders"></a>上下文 Siri 提醒

允许使用 Siri 来快速使应用程序的提醒才能查看的内容在以后的日子当前正在应用中查看用户。 例如，如果它们已在应用中查看餐馆评论，它们无法调用 Siri，说*"再提醒我有关此当我回到家。"* 在应用程序也使用 Siri 会生成提醒，其中链接添加到评审。

## <a name="implementing-proactive-suggestions"></a>实现主动建议

添加主动建议向 Xamarin.iOS 应用程序的支持是通常一样轻松，实现几个 Api 或几个 api 的应用程序可能已实现扩展。

主动建议处理应用在三个方面：

- **`NSUserActivity`** -可帮助了解哪些用户当前正在使用在屏幕的信息的系统。
- **位置建议**-如果应用程序提供或使用基于位置信息，这些 API 扩展提供的新方法以在应用间共享此信息。

和中应用程序通过实现以下受支持：

- **上下文 Siri 提醒**-在 iOS 10，`NSUserActivity`已扩展为允许使用 Siri 来快速使应用程序的提醒才能查看的内容在以后的日子当前正在应用中查看。
- **位置建议**-iOS 10 增强了`NSUserActivity`捕获查看在应用程序内的位置并将它们升级在整个系统的多个位置。
- **上下文 Siri 请求** -  `NSUserActivity`提供到显示的信息在应用程序内部使用 Siri，从而让用户可以获得指导性内容或呼叫的位置调用 Siri 从应用中的上下文。

所有这些功能有一个共同点，它们都使用`NSUserActivity`中一个窗体或另一个用来提供相应的功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如前所述，`NSUserActivity`可帮助了解哪些用户当前正在使用在屏幕的信息的系统。 `NSUserActivity` 轻量状态缓存机制它们浏览应用程序中捕获用户的活动。 例如，查看餐馆应用程序：

[![](proactive-suggestions-images/activity02.png "餐馆应用程序")](proactive-suggestions-images/activity02.png#lightbox)

以下的交互：

1. 用户会使用应用程序，如`NSUserActivity`创建以重新创建的应用程序更高版本的状态。
2. 如果一家餐厅搜索用户，则遵循同一模式，创建活动。
3. 和同样，当用户查看结果。 此最后一种情况，用户正在查看位置且在 iOS 10 中，系统更了解某些概念 （如位置或通信交互）。

要进一步查看在最后一个屏幕中：

[![](proactive-suggestions-images/activity03.png "NSUserActivity 负载")](proactive-suggestions-images/activity03.png#lightbox)

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

确保此路径是相同的活动类型标识符 (`com.xamarin.platform`) 作为上面创建的活动。 该应用使用中存储的信息`NSUserActivity`以返回到用户的地方开始还原状态。

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
4. 如果用户切换到地图应用时，餐馆的地址自动建议作为目标。
5. 这甚至适用于第三方应用程序 (该支持`NSUserActivity`)，因此用户可以切换到持续一段时间共享应用程序和餐馆的地址自动建议作为那里的目标以及。
6. 它还提供了上下文的 Siri，以便用户可以在餐馆应用内调用 Siri 并询问*"获取路线..."* ，并使用 Siri 将提供到餐馆查看用户的说明。

所有上述功能共同具有相同的一件事情，它们都指示建议最初来源于何处。 对于上述示例中，它是虚构的餐馆查看应用程序。

watchOS 3 已经过增强，为一些小的修改并添加到现有框架通过应用程序启用此功能：

- `NSUserActivity` 已为捕获查看在应用程序内的位置信息的其他字段。
- 几项补充进行了 MapKit 和 CoreSpotlight 捕获位置。
- 位置感知功能已添加到使用 Siri、 地图、 多任务和其他系统中的应用。

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

然后，在基于的描述所需的文本的文本基于实例 （例如 QuickType 键盘中）：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

纬度和经度是可选的但确保用户被路由到应用程序想要将它们发送到的确切位置：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

通过设置电话号码，应用程序可以访问使用 Siri 以便用户可通过说类似，从应用调用 Siri *"调用该邻居":

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最后，应用程序可以指示实例是否适合导航和电话呼叫：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```
## <a name="activities-best-practices"></a>活动的最佳做法

在处理活动时，Apple 提供的以下最佳实践建议：

- 使用`NeedsSave`延迟负载更新。
- 确保保留对当前活动的强引用。
- 仅传输包括刚好够用的信息，以还原状态的小负载。
- 确保活动类型标识符是唯一的描述性，通过使用反向 DNS 表示法来指定它们。 

## <a name="consuming-location-suggestions"></a>使用位置建议

此下一节将介绍使用具有来自系统 （如地图应用程序） 或其他第三方应用程序的其他部分的位置建议。

## <a name="routing-apps-and-locations-suggestions"></a>路由的应用程序和位置建议

本部分将看看在路由的应用内使用直接从位置建议。 对于路由以添加此功能的应用，开发人员将利用现有`MKDirectionsRequest`framework，如下所示：

- 若要将提升的应用程序中多任务。
- 若要将应用程序注册为路由的应用程序。
- 若要处理启动应用程序与 MapKit`MKDirectionsRequest`对象。
- 使 watchOS 能够了解如何建议应用程序中基于用户参与度。

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

新在 watchOS 3，应用程序可以将发送的导致开发人员需要将编码地址中不具有地域坐标的地址：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="summary"></a>摘要

本文已涉及主动建议，并介绍了如何开发人员可以使用它们将驱动器流量发送到一个 Xamarin.iOS 应用程序 watchOS。 它涵盖实现主动建议的步骤，并显示使用准则。


## <a name="related-links"></a>相关链接

- [watchOS 示例](https://developer.xamarin.com/samples/watchos/all/)
- [SiriKit 编程指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
