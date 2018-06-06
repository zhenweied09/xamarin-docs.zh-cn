---
title: 使用在 Xamarin.iOS 的核心 Spotlight 搜索
description: 本文档介绍如何在 Xamarin.iOS 应用程序中使用核心 Spotlight 提供在应用程序内容的链接。 它讨论如何创建、 还原、 更新和删除可搜索的项。
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: a8bc3aaa43d7830b0a3baa0768d495458b1ecfad
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788034"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>使用在 Xamarin.iOS 的核心 Spotlight 搜索

核心聚焦是一个新的框架，用于 iOS 9 提供类似于数据库的 API 来添加、 编辑或删除你的应用程序中内容链接。 使用核心 Spotlight 已添加的项都可在 iOS 设备上的 Spotlight 搜索中。

有关类型的内容的示例，可以使用核心 Spotlight 编制索引，看 Apple 的消息、 邮件、 日历和说明应用程序。 当前，它们都使用核心 Spotlight 来提供搜索结果。

## <a name="creating-an-item"></a>创建项目

下面是创建项目和对其使用核心 Spotlight 进行索引的示例：

```csharp
using CoreSpotlight;
...

// Create attributes to describe an item
var attributes = new CSSearchableItemAttributeSet();
attributes.Title = "App Center Test";
attributes.ContentDescription = "Automatically test your app on 1,000 devices in the cloud.";

// Create item
var item = new CSSearchableItem ("1", "products", attributes);

// Index item
CSSearchableIndex.DefaultSearchableIndex.Index (new CSSearchableItem[]{ item }, (error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

此信息将显示在搜索结果如下：

[![](corespotlight-images/corespotlight01.png "核心 Spotlight 搜索结果概述")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>还原的项目

当用户点击对某项添加到你的应用，通过核心 Spotlight 的搜索结果`AppDelegate`方法`ContinueUserActivity`称为 (此方法还用于`NSUserActivity`)。 例如：

```csharp
public override bool ContinueUserActivity (UIApplication application,
   NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchableItem.ActionType.ToString ()) {
            // Display content for searchable item...
        }
        break;
    }

    return true;
}
```

请注意，这次我们检查活动具有`ActivityType`的`CSSearchableItem.ActionType`。

## <a name="updating-an-item"></a>更新项

可能时使用核心 Spotlight 我们创建索引项需要修改，例如标题或缩略图的更改为所需的时间。 若要进行此更改，我们使用相同的方法使用最初创建索引。
我们创建一个新`CSSearchableItem`使用相同的 ID，因为用于创建该项目，将附加新`CSSearchableItemAttributeSet`包含已修改的属性：

[![](corespotlight-images/corespotlight02.png "更新项概述")](corespotlight-images/corespotlight02.png#lightbox)

当此项写入可搜索的索引时，使用新信息更新现有项目。

## <a name="deleting-an-item"></a>删除项

核心 Spotlight 提供多种方式来在不再需要时删除某个索引项。

首先，你可以删除某个项按标识符，例如：

```csharp
// Delete Items by ID
CSSearchableIndex.DefaultSearchableIndex.Delete(new string[]{"1","16"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

接下来，你可以通过其域名删除一组索引项。 例如：

```csharp
// Delete by Domain Name
CSSearchableIndex.DefaultSearchableIndex.DeleteWithDomain(new string[]{"domain-name"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

最后，你可以删除所有索引项替换为以下代码：

```csharp
// Delete all index items
CSSearchableIndex.DefaultSearchableIndex.DeleteAll((error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```
## <a name="additional-core-spotlight-features"></a>其他核心 Spotlight 功能

核心 Spotlight 具有以下功能，帮助保持准确和最新的索引：

- **批处理更新支持**– 如果你的应用程序需要创建或修改在同一时间的一大组索引，可以将整批发送到`Index`方法`CSSearchableIndex`一次调用的类。
- **响应索引更改**– 使用`CSSearchableIndexDelegate`您的应用程序可以响应更改和通知的可搜索的索引。
- **应用数据保护**– 使用数据保护类，你可以实施安全在项上，你将添加到使用核心 Spotlight 搜索索引。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [应用程序搜索编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
