---
title: "使用 Web 标记搜索"
description: "添加可以链接回你的应用程序的基于 web 的搜索结果。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 63bc1f0ed13fe65b36e95978da9ccc2ea8d4481c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="search-with-web-markup"></a>使用 Web 标记搜索

用于提供对 web 站点通过其内容的访问的应用程序 (不只从在应用内)，web 内容可标记为特殊的链接，将通过 Apple 爬网，并提供到用户的 iOS 9 设备上应用的深层链接。

如果 iOS 应用程序已支持移动的深层链接并在你的网站呈现中你的应用，Apple 的内容的深层链接_Applebot_ web 爬网程序将此内容编制索引并自动将其添加到其云索引：

[ ![](web-markup-images/webmarkup01.png "云索引概述")](web-markup-images/webmarkup01.png)

Apple 将呈现在 Spotlight 搜索和 Safari 搜索结果中的这些结果。
如果用户点击其中一种结果 （并且它们具有您安装的应用程序） 则它们将执行你的应用程序中的内容：

[ ![](web-markup-images/webmarkup02.png "从搜索结果中的网站链接的深度")](web-markup-images/webmarkup02.png)

## <a name="enabling-web-content-indexing"></a>启用 Web 内容索引

有可供搜索使用 Web 标记应用的内容所需的四个步骤：

1. 确保 Apple 可以发现和索引通过定义其作为你的应用网站**支持**或**市场营销**在 iTunes Connect 中的网站。
2. 请确保您的应用程序的网站包含所需的标记以实现移动深层链接。 请参阅下列各部分，以更多详细信息。
3. 启用 iOS 应用程序中处理的深层链接。
4. 添加标记显示你的应用网站向最终用户提供丰富且与结果的结构化数据。 虽然此步骤不是绝对必需的强烈建议通过 Apple。

下列各节将会通过这些详细信息中的步骤。

## <a name="make-your-apps-website-discoverable"></a>使您的应用程序的网站可发现

具有 Apple 查找您的应用程序的网站的最简单方法是使用为**支持**或**市场营销**网站时提交到 Apple 应用程序通过 iTunes Connect。

## <a name="using-smart-app-banners"></a>使用智能应用横幅

在你的网站以提供清楚的链接到你的应用提供智能应用程序标题。 如果尚未安装应用程序，Safari 将自动提示用户安装您的应用程序。 使用可以点击否则**视图**链接以启动你的应用从网站。 例如，若要创建智能应用横幅，你可以使用下面的代码：

```xml
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

有关详细信息，请参阅 Apple 的[提升智能应用程序所使用的应用程序](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html)文档。

## <a name="using-universal-links"></a>使用通用的链接

新通用的链接提供更好的选择智能应用横幅或现有的自定义 URL 方案 iOS 9，，通过提供以下：

- **唯一**– 相同的 URL 不能声明的多个网站。
- **安全**– 签名的证书是必需的对于网站，以确保网站拥有由你和有效地链接到你的应用程序。
- **灵活**– 最终用户可以控制 URL 启动的网站或应用程序。
- **通用**– 可以用来定义你的网站和应用程序的内容的相同 URL。

## <a name="using-twitter-cards"></a>使用 Twitter 卡

你可以提供深层链接应用程序的内容使用 Twitter 卡。 例如:

```xml
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

有关详细信息，请参阅 Twitter 的[Twitter 卡协议](http://dev.twitter.com/cards/mobile)文档。

## <a name="using-facebook-app-links"></a>使用 Facebook 应用程序链接

你可以提供深层链接使用 Facebook 应用程序链接的应用程序的内容。 例如:

```xml
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

有关详细信息，请参阅 Facebook 的[应用链接](http://applinks.org)文档。

## <a name="opening-deep-links"></a>打开深层链接

你需要添加对打开，并在你的 Xamarin.iOS 应用程序中显示的深层链接的支持。 编辑**AppDelegate.cs**文件，并重写`OpenURL`方法来处理自定义的 URL 格式。 例如:

```csharp
public override bool OpenUrl (UIApplication application, NSUrl url, string sourceApplication, NSObject annotation)
{

    // Handling a URL in the form http://company.com/appname/?123
    try {
        var components = new NSUrlComponents(url,true);
        var path = components.Path;
        var query = components.Query;

        // Is this a known format?
        if (path == "/appname") {
            // Display the view controller for the content
            // specified in query (123)
            return ContentViewController.LoadContent(query);
        }
    } catch {
        // Ignore issue for now
    }

    return false;
}
```

在上面的代码中，我们正在寻找一个 URL，其中包含`/appname`并传递的值`query`(`123`在此示例中) 到我们的应用程序以向用户显示请求的内容的自定义视图控制器。

## <a name="providing-rich-results-with-structured-data"></a>提供与结构化数据的格式结果

通过包括结构化数据标记可以向不再局限于只需标题和描述最终用户提供丰富的搜索结果。 包括图像、 应用程序特定数据 （例如分级） 和使用结构化数据标记的结果的操作。

丰富结果更加融入，并可以帮助提高在云中你排名基于搜索索引通过诱使更多用户能够与它们进行交互。

提供结构化数据标记的一个选项是通过使用开放图谱。 例如:

```xml
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

有关详细信息，请参阅[开放图谱](http://ogp.me)网站。

结构化数据标记的另一种常见格式是 schema.org 的 Microdata 格式。 例如:

```xml
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
    <span itemprop="ratingValue">4** stars -
    <span itemprop="reviewCount">255** reviews


```

可以以 schema.org 的 JSON LD 格式表示相同的信息：

```xml
<script type="application/ld+json">
    "@content":"http://schema.org",
    "@type":"AggregateRating",
    "ratingValue":"4",
    "reviewCount":"255"
</script>
```

下面显示从您的网站向最终用户提供丰富的搜索结果的元数据示例：

[ ![](web-markup-images/deeplink01.png "大范围搜索通过结构化数据标记的结果")](web-markup-images/deeplink01.png)

Apple 当前支持以下架构类型从 schema.org:

 - AggregateRating
 - ImageObject
 - InteractionCount
 - 提供
 - 组织
 - PriceRange
 - 原因之一
 - SearchAction

有关这些方案类型的详细信息，请参阅[schema.org](http://schema.org)。

## <a name="providing-actions-with-structured-data"></a>提供与结构化数据的操作

特定类型的结构化数据便能够被最终用户可操作搜索结果。 当前支持以下操作：

 - 拨打电话号码。
 - 获取映射到给定的地址的方向。
 - 播放音频或视频文件。

例如，定义要拨打的电话号码的操作可能如下所示：

```xml
<div itemscope itemtype="http://schema.org/Organization">
    <span itemprop="telephone">(408) 555-1212**


```

当向最终用户显示此搜索结果时，结果中将显示一个小 phone 图标。 如果用户点击该图标，将调用指定的数字。

以下 HTML 要添加要播放音频文件从搜索结果的操作：

```xml
<div itemscope itemtype="http://schema.org/AudioObject">
    <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**


```

最后，以下 HTML 要添加要从搜索结果中获取说明的操作：

```xml
<div itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">1 Infinite Loop**
    <span itemprop="addressLocality">Cupertino**
    <span itemprop="addressRegion">CA**
    <span itemprop="postalCode">95014**


```

有关详细信息，请参阅 Apple 的[应用搜索开发人员网站](http://developer.apple.com/ios/search/)。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [应用程序搜索编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
