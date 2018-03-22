---
title: "查看请求应用程序"
description: "本文介绍如何 RequestReview 方法添加到 iOS 10 以及如何在 Xamarin.iOS 中实现该该 Apple。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 2e63f2c47bbcd6da0f0d5370ebfc231d19a10e7d
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="request-app-review"></a>查看请求应用程序

_本文介绍如何 RequestReview 方法添加到 iOS 10 以及如何在 Xamarin.iOS 中实现该该 Apple。_

新到 iOS 10.3`RequestReview()`方法允许 iOS 应用程序要求用户进行评估或对其进行评审。 当用户在已安装从应用商店传送应用程序中调用此方法时，iOS 10 将处理整个分级和开发人员查看过程。 因为此过程受应用商店策略，则警报可能或可能不会显示。

![](request-app-review-images/review01.png "示例评审请求的警报")

## <a name="requesting-a-rating-or-review"></a>请求的分级或查看

虽然`RequestReview()`静态方法`SKStoreReviewController`可以调用类在有意义的用户体验中的任何时刻，控制和由应用商店策略审查流程。 因此，此方法可能或可能不会显示警报，应永远不会调用以响应用户操作，如点击按钮。

例如，应用可能会请求检查后，将启动给定的次数或游戏玩家完成级别后可能会请求进行评审。

对请求评论只要 Xamarin.iOS 应用程序完成启动，进行以下更改到`AppDelegate.cs`文件：

```csharp
using Foundation;
using StoreKit;
using UIKit;

namespace iOSTenThree
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        ...

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Request a review from the user
            SKStoreReviewController.RequestReview ();

            return true;
        }
        
        ...
        
    }
}
```

> [!NOTE]
> 调用`RequestReview()`在未得到充分开发应用程序将始终显示分级以及查看对话框，以便可以测试它。 这不适用于通过 TestFlight，其中将忽略方法调用已分发的应用。

当`RequestReview()`方法调用中的传送应用程序用户在已安装从应用商店，iOS 10 为开发人员处理的整个分级和查看过程。 同样，因为此过程受应用商店策略，则警报可能，或可能不会显示。

## <a name="linking-to-an-app-store-product-page"></a>将链接到应用商店产品页 

除了新`RequestReview`方法，开发人员仍可以提供在应用内从应用商店中的应用程序的产品页的深层链接。 通过追加`action=write-review`到产品页 URL 的末尾，页面将打开用户可以自动编写的应用程序查看。 

## <a name="summary"></a>总结

本文已覆盖 RequestReview 方法添加到 iOS 10 以及如何在 Xamarin.iOS 中实现该该 Apple。



## <a name="related-links"></a>相关链接

- [iOSTenThree 示例](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
