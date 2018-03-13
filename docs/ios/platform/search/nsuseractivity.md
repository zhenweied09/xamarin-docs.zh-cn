---
title: "使用 NSUserActivity 搜索"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 8376ce2ccff6732fa0c89d6030b9af36d29c5085
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="search-with-nsuseractivity"></a>使用 NSUserActivity 搜索

`NSUserActivity` 在 iOS 8 中引入，用于为 Handoff 提供数据。
它允许你在应用，然后可传递到另一个实例在不同的 iOS 设备上运行的应用程序的特定部分中创建活动。 接收设备然后可以继续在前一个设备，选取右用户离开的位置上启动的活动。 有关使用切换的详细信息，请参阅我们[简介 Handoff](~/ios/platform/handoff.md)文档。

为 iOS 9，新`NSUserActivity`可以建立索引 （公开和私有） 和搜索从 Spotlight 搜索和 Safari。 通过将标记`NSUserActivity`作为搜索和添加可建立索引的元数据，可以在 iOS 设备上搜索结果中列出的活动。

[![](nsuseractivity-images/apphistory01.png "应用历史记录概述")](nsuseractivity-images/apphistory01.png#lightbox)

如果用户选择从你的应用程序所属活动的搜索结果，将启动应用程序以及活动中说明的`NSUserActivity`将重新启动并向用户显示。

下列属性`NSUserActivity`用于支持应用程序搜索：

 - `EligibleForHandoff` -如果`true`，可以在提交操作中使用此活动。
 - `EligibleForSearch` -如果`true`，此活动将添加到设备上的索引，并在搜索结果中显示。
 - `EligibleForPublicIndexing` -如果`true`，此活动将添加到 Apple 的基于云的索引，并呈现给尚未安装你的应用程序在其 iOS 设备 （通过搜索） 的用户。 请参阅[公共搜索索引](#Public-Search-Indexing)下面部分以了解更多详细信息。
 - `Title` – 对您的活动提供标题和显示在搜索结果。 用户还可以搜索的文本本身的标题。
 - `Keywords` – 是一个用于描述活动将建立索引并由最终用户可搜索的字符串数组。
 - `ContentAttributeSet` – 是`CSSearchableItemAttributeSet`用于进一步描述你的活动详细信息中并提供丰富的搜索结果中的内容。
 - `ExpirationDate` – 如果你想用于才会显示到给定的日期的活动，你可以提供该日期此处。
 - `WebpageURL` – 如果可以在 web 上查看活动，或者如果你的应用程序支持 Safari 的深层链接，你可以设置该链接可以访问此处。

## <a name="nsuseractivity-quickstart"></a>NSUserActivity 快速入门

按照这些说明来实现可搜索`NSUserActivity`应用程序中：

- [创建活动类型标识符](#creatingtypeid)
- [创建活动](#createactivity)
- [响应的活动](#respondactivity)
- [公共搜索索引](#indexing)

有一些[其他好处](#benefits)使用`NSUserActivity`使你的内容可供搜索。

<a name="creatingtypeid" />

## <a name="creating-activity-type-identifiers"></a>创建活动类型标识符

你可以创建搜索活动之前，你将需要创建_活动类型标识符_来标识它。 活动类型标识符是添加到一个短字符串`NSUserActivityTypes`的应用程序的数组**Info.plist**用于唯一标识给定的用户活动类型的文件。 在数组的应用程序支持，并公开给应用程序搜索的每个活动中，将一个项。 

Apple 提供的建议使用活动类型标识符的反向 DNS 样式表示法以避免冲突。 例如：`com.company-name.appname.activity`对于特定的应用程序基于活动或`com.company-name.activity`可以运行跨多个应用的活动。

在创建时使用活动类型标识符`NSUserActivity`实例标识活动的类型。 当活动继续作为用户点击搜索结果的结果时, （以及团队的应用程序的 ID) 的活动类型将确定哪些应用程序以启动继续活动。

若要创建所需的活动类型标识符，以支持此行为，编辑**Info.plist**文件，并切换到**源**视图。 添加`NSUserActivityTypes`密钥，然后创建标识符采用以下格式：

[![](nsuseractivity-images/type01.png "NSUserActivityTypes 键和 plist 编辑器中的所需的标识符")](nsuseractivity-images/type01.png#lightbox)

在上面的示例中，我们创建一个新的活动类型标识符搜索活动 (`com.xamarin.platform`)。 创建你自己的应用时, 的内容替换`NSUserActivityTypes`阵列特定于活动的活动类型标识符您的应用程序支持。

<a name="createactivity" />

## <a name="creating-an-activity"></a>创建活动

下面是创建用于托管的设备上的索引搜索的活动的示例：

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.BecomeCurrent();
```

我们无法添加更多详细信息通过设置`ContentAttributeSet`属性我们`NSUserActivity`，如下所示：

[![](nsuseractivity-images/apphistory02.png "添加搜索详细信息概述")](nsuseractivity-images/apphistory02.png#lightbox)

通过使用`ContentAttributeSet`你可以创建吸引最终用户与之交互的丰富的搜索结果。

<a name="respondactivity" />

## <a name="responding-to-an-activity"></a>响应的活动

若要响应用户点击搜索结果 (`NSUserActivity`) 对于我们的应用程序编辑**AppDelegate.cs**文件，并重写`ContinueUserActivity`方法。 例如:

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

请注意，这是用于对 Handoff 请求作出响应的相同方法重写。 现在如果用户单击我们 Spotlight 搜索结果中的应用程序中的链接，将置于前台 （或启动如果尚未运行） 我们的应用程序并将显示的内容、 导航或由该链接的功能：

[![](nsuseractivity-images/apphistory03.png "从搜索还原以前的状态")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing" />

## <a name="public-search-indexing"></a>公共搜索索引

正如我们所看到上面，iOS 9 轻松地以提供对应用内容和功能的用户已发现并在给定的 iOS 设备上使用的搜索访问。 使用公共索引，iOS 9 提供一种用户人员尚未尚未发现内容或功能 （或人员甚至尚未安装应用程序） 若要在其搜索也获取这些结果。

公共索引编制的工作方式如下：

1. 为你的应用程序创建活动时你可以将它标记为公共。
2. 公共活动进行发送到 Apple 和在云中索引。
3. 为更多用户与你在设备上的应用进行交互，而使用的特定功能或由活动的内容，它将增加秩。
4. 即使他们没有安装的应用程序，常用公共结果将会对其他用户可用。
5. 这些公共结果将显示在 Spotlight 搜索和 Safari （如果该活动包含一个 URL）。

我们可以需要更高版本，我们创建的专用搜索活动并将其是公共的扩展：

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.EligibleForPublicIndexing = true;
activity.BecomeCurrent();
```

只是因为已将活动设置为公共索引通过设置`EligibleForPublicIndexing = true`，它并不意味着，它将自动添加到 Apple 的公有云索引。 必须首先满足以下条件：

1. 它必须显示在搜索结果中，选择多个用户。 结果都将保持私有之前已满足了活动的用户参与策略阈值。
2. 应用程序设置阻止从正在建立索引并使其成为公共任何特定于用户的数据。

<a name="benefits" />

## <a name="additional-benefits"></a>其他好处

通过采用通过应用搜索`NSUserActivity`中你的应用，则还可以获取以下功能：

- **Handoff** -由于应用程序搜索公开了内容，导航和/或功能使用相同的机制，作为 Handoff (`NSUserActivity`)，你可以轻松地允许应用程序的用户启动另一台设备上的活动，然后继续在另一台。
- **Siri 建议**-Siri 建议通常使标准建议，以及从应用程序的在职可以自动建议。
- **Siri 智能提醒**-用户将能够询问 Siri 提醒他们有关从应用程序的活动。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [应用程序搜索编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [博客文章和示例](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
