---
title: 在 Xamarin.iOS 的搜索 Api
description: 本文介绍如何使用 iOS 9 提供的新应用程序搜索 Api 以允许用户搜索信息和内部 Xamarin.iOS 应用程序的功能。
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: bc62ad34af0b9b98f0475599a08946122badd21e
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788171"
---
# <a name="search-apis-in-xamarinios"></a>在 Xamarin.iOS 的搜索 Api

_本文介绍如何使用 iOS 9 提供的应用程序搜索 Api 以允许用户搜索信息和内部 Xamarin.iOS 应用程序的功能。_

搜索已在 iOS 9，以提供了全新的方法，以访问信息和功能在 Xamarin.iOS 应用程序中扩展。 使用新的应用程序搜索 Api，应用程序内容成为可搜索访问 Spotlight 和 Safari 搜索结果，Handoff 和 Siri 提醒和建议。 这样用户就可以快速访问活动和深层应用中的信息。

此外，新的搜索 Api 更加轻松地集成应用程序而无需在前一搜索实现体验中的搜索。 因此，Apple 声明它通常用几个小时，使 iOS 9 应用的内容普遍可供搜索，使用应用程序的搜索。

[![](images/intro01.png "下面举例说明普遍可搜索使用应用程序搜索的 iOS 9 应用内容")](images/intro01.png#lightbox)

应用程序搜索由组成三个单独的 Api:

1. [**NSUserActivity** ](nsuseractivity.md) -这是 Apple 在 iOS 8 中发布的 Handoff api 扩展。 它用于使应用程序交互历史记录可供搜索，同时公开和私下） 用户。

2. [**核心 Spotlight** ](corespotlight.md) -允许应用在搜索结果中呈现其内容编制索引。 其工作原理类似数据库 API 其中可以添加、 删除项，它是在应用内的索引私人内容的最佳方式。

3. [**WebMarkup** ](web-markup.md) -用于提供对 web 界面通过其内容的访问的应用程序 (不只从在应用内)。 Web 内容可标记为特殊的链接，将通过 Apple 爬网，并提供到用户的 iOS 9 设备上的应用的深层链接。

## <a name="selecting-an-app-search-approach"></a>选择应用程序搜索方法

决定要实现这些方法取决于你的应用提供的交互的类型和它将提供的内容类型。

使用以下准则：

- [**NSUserActivity** ](nsuseractivity.md) – 此框架用于提供可搜索性同时公钥和私钥的内容和您的应用程序内的导航点的可搜索性。

- [**核心 Spotlight** ](corespotlight.md) – 此框架用于提供对设备上存储的专用数据可搜索性。

- [**Web 标记**](web-markup.md) – 使用此框架的应用程序存在不仅可以从其内容内的应用程序中，而从应用程序的网站也提供可搜索性。

使用每个应用程序搜索方法不同，并且可以单独进行，但是 Apple 将它们设计为协同工作。 使用多个方法时要编制索引的特定项，请确保使用相同**项 ID**上每种方法，因此该个人链接工作在一起。

使用多个方法不仅可确保你的内容将会找到被最终用户，但还有助于提高从搜索中的项的排名。

中的分级过程时向开发人员，近乎透明与给定项的用户交互转变为通过权衡很大程度时此级别 （例如用户点链接）。
通过提供丰富、 带有提示性的项，你可以确保，用户将被 enticed 进行交互与你的内容，因此引发其排名。

## <a name="what-content-to-index"></a>内容索引

Apple 提供了有关哪些内容和操作以下建议，以提供您的应用程序中的搜索索引：

 - 任何内容查看、 创建或策展用户从应用中。
 - 导航点和在应用内的功能。
 - 像新消息，内容或其他类型的最近下载到设备的项显示你的应用。

## <a name="app-search-enhancements"></a>应用程序搜索增强功能

在 iOS 10 中的核心 Spotlight 如提供对应用程序搜索的多项增强功能：

- **Crowdsourced 深层链接受欢迎程度 （与差异隐私）** -提供一种方法来升级在搜索结果中的深层链接应用内容。
- **应用内搜索**-使用新`CSSearchQuery`类以提供应用内 Spotlight 搜索功能类似于邮件、 消息和注释应用的工作原理。
- **搜索延续**-允许用户在 Spotlight 或 Safari，启动搜索，然后打开应用程序，并继续了搜索。
- **可视化效果的验证结果**-Apple 的[应用搜索 API 验证工具](https://search.developer.apple.com/appsearch-validation-tool)时 preforming 测试现在显示可视表示形式网站的标记和深层链接。
- **消息应用映像共享**-允许为共享 （通过一个消息应用程序扩展） 的消息才会显示在 Spotlight 搜索提供的常用应用内映像。

若要了解详细信息，请参阅我们[应用搜索增强功能](~/ios/platform/search/app-search-enhancements.md)指南。

### <a name="proactive-suggestions"></a>主动建议

iOS 10 通过允许系统以主动有用的信息自动向用户显示在适当的时间显示推动用户参与策略应用到的新方式。 为 iOS 9 提供的功能添加到 Spotlight、 Handoff 和 Siri 建议使用的 iOS 10 应用可以公开可以从以下位置中的系统通过向用户显示的功能的应用的深层搜索：

- 应用程序切换器
- 锁定屏幕
- CarPlay
- 映射
- Siri 交互
- QuickType 建议 

应用程序公开此功能到使用技术的集合，如系统[NSUserActivity](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/)，web 标记中，核心 Spotlight、 MapKit、 Media Player 和 UIKit。

若要了解详细信息，请参阅我们[主动建议](~/ios/platform/search/proactive-suggestions.md)指南。

## <a name="summary"></a>总结

本文已覆盖新搜索 API 功能该 iOS 9 提供有关 Xamarin.iOS 应用程序。 它涵盖[NSUserActivity](nsuseractivity.md)，[核心 Spotlight](corespotlight.md)和[Web 标记](web-markup.md)索引内容的方法。 它已完成，但应在何时使用给定的搜索方法和类型的内容应该是什么的短讨论编制索引。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [应用程序搜索编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
