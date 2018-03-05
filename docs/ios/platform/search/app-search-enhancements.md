---
title: "应用程序搜索增强功能"
description: "本文介绍如何增强功能对应用程序搜索所做的 Apple 已在 iOS 10 以及如何在 Xamarin.iOS 实现它们。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: 95f7ad5069abfe4dff82659c0fbc79eef2125e15
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="app-search-enhancements"></a>应用程序搜索增强功能

_本文介绍如何增强功能对应用程序搜索所做的 Apple 已在 iOS 10 以及如何在 Xamarin.iOS 实现它们。_

在 iOS 10 中，Apple 具有对应用搜索如 Crowdsourced 深层链接、 应用内搜索、 搜索延续和可视化效果的验证结果进行多项增强功能。 本文将介绍在 Xamarin.iOS 应用程序中实现这些功能。

## <a name="about-app-search-enhancements"></a>有关应用程序搜索增强功能

在 iOS 10 中的核心 Spotlight 如提供对应用程序搜索的多项增强功能：

- **Crowdsourced 深层链接受欢迎程度 （与差异隐私）** -提供一种方法来升级在搜索结果中的深层链接应用内容。
- **应用内搜索**-使用新`CSSearchQuery`类以提供应用内 Spotlight 搜索功能类似于邮件、 消息和注释应用的工作原理。
- **搜索延续**-允许用户在 Spotlight 或 Safari，启动搜索，然后打开应用程序，并继续了搜索。
- **可视化效果的验证结果**-Apple 的[应用搜索 API 验证工具](https://search.developer.apple.com/appsearch-validation-tool)时 preforming 测试现在显示可视表示形式网站的标记和深层链接。
- **消息应用映像共享**-允许为共享 （通过一个消息应用程序扩展） 的消息才会显示在 Spotlight 搜索提供的常用应用内映像。

下列各节将介绍这些主题的更多详细信息。

## <a name="crowdsourced-deep-link-popularity"></a>Crowdsourced 深层链接受欢迎程度

iOS 10 提供了一种机制来计数到应用的常用深层链接后跟用户和使用此信息以改进应用程序中的排名的搜索结果中的内容时仍使用保护用户的标识的频率*差异隐私*。

为应用程序的使用`NSUserActivity`对象以提供深层链接 Url 并且具有`EligibleForPublicIndexing`属性设置为`true`，iOS 10 提交的子集*差异隐私哈希*到 Apple 的服务器。 此信息然后用于提升搜索结果中的常用中应用内容。

有关在 Xamarin.iOS 应用程序中实现深层链接的详细信息，请参阅我们[使用 NSUserActivity 搜索](~/ios/platform/search/nsuseractivity.md)文档。

## <a name="in-app-searching"></a>应用内搜索

通过实现新[CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery)类，应用程序可提供聚焦的搜索和匹配规则技术，以找到内本身，而无需用户无需离开应用程序 （类似于如何邮件、 消息和说明应用程序的内容工作）。

通常情况下，应用程序支持`CSSearchQuery`无需维护自己单独的搜索索引。 

## <a name="search-continuation"></a>搜索延续

在 iOS 9，Apple 引入了搜索 Api (如核心 Spotlight`NSUserActivity`和 web 标记) 以提供深层-喜欢的应用程序以允许用户搜索使用 Spotlight 和 Safari 搜索接口该内容中的内容。 请参阅我们[新搜索 Api](~/ios/platform/search/index.md)更多详细信息的文档。

在 iOS 中 10 Apple 通过在构建此功能允许用户在 Spotlight 或 Safari，启动搜索，然后打开应用程序时继续搜索。 

若要实现此功能，请编辑应用程序的`Info.plist`文件中，添加`CoreSpotlightContinuation`类型的密钥**布尔**并将其值设置为`YES`:

[[ide name="xs]]

[ ![](app-search-enhancements-images/search01.png "编辑 CoreSpotlightContinuation Info.plist 文件中")](app-search-enhancements-images/search01.png)

[[/ide]]

[[ide name="vs]]

[ ![](app-search-enhancements-images/searchw01.png "编辑 CoreSpotlightContinuation Info.plist 文件中")](app-search-enhancements-images/search01.png)

[[/ide]]

若要响应用户继续搜索结果 (`NSUserActivity`)，编辑`AppDelegate.cs`文件，并重写`ContinueUserActivity`方法。 例如:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchQuery.ContinuationActionType) {
            var search = userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString);
            // Continue user's search here...
        }
        break;
    }

    return true;
}
```

此代码查找查询延续操作类型 (`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`)，然后读取用户的当前查询从`NSUserActivity`类的用户信息字典 (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`)。 从此处，应用程序需要采取措施来继续用户的搜索。

有关使用在 Xamarin.iOS 应用程序中进行搜索的详细信息，请参阅我们[使用核心 Spotlight 搜索](~/ios/platform/search/corespotlight.md)文档。

## <a name="visualization-of-validation-results"></a>可视化效果的验证结果

Apple 的[应用搜索 API 验证工具](https://search.developer.apple.com/appsearch-validation-tool)现在显示可视表示形式网站的标记和深层链接 (包括标记如定义在[Schema.org](http://schema.org/)) 时 preforming 测试。

通过使用验证工具，开发人员可以查看如标题、 说明、 URL 和任何其他站点中具有已编制索引 Applebot Web 爬网程序的信息受支持的元素。

有关使用 Web 标记的详细信息，请参阅我们[搜索与 Web 标记](~/ios/platform/search/web-markup.md)文档。

## <a name="message-app-image-sharing"></a>共享的消息应用映像

如果消息应用扩展提供用于在消息中共享的映像，可以配置扩展为允许用户执行 Spotlight 搜索从的消息中的常用映像而无需退出应用程序。

若要启用此功能，请执行以下操作：

1. 创建消息应用扩展。
2. 添加`com.apple.developer.associated-domains`到应用的权利和包括承载共享消息应用扩展的映像的 web 域的列表。 对于每个域中，指定`spotlight-image-search`服务。
3. 添加`apple-app-site-association`到的映像所在的网站的文件。 此文件包括为字典`spotlight-image-search`服务并包括应用程序的 ID，即团队 ID 或应用程序 ID 前缀后跟捆绑 id。 该文件可以包含最多 500 条路径和模式，将按 Spotlight 建立索引并包括在常用映像搜索中。 有关详细信息，请参阅 Apple 的[创建并上载该关联文件](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4)文档。
4. 允许 Applebot 进行爬网在网站上找到。 请参阅 Apple 的[有关 Applebot](https://support.apple.com/en-us/HT204683)文档。

请参阅我们[消息应用集成](~/ios/platform/message-app-integration/index.md)更多详细信息的文档。

## <a name="summary"></a>摘要

本文已覆盖增强功能对应用程序搜索所做的 Apple 已在 iOS 10 以及如何在 Xamarin.iOS 实现它们。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
