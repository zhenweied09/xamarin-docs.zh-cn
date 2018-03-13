---
title: "订阅和报告"
ms.topic: article
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 237a986d6db2fb6984e99c6265fbbc212b35a351
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="subscriptions-and-reporting"></a>订阅和报告

## <a name="about-non-renewing-subscriptions"></a>有关非续订订阅

非续订订阅供表示的时间限制，如 （到导航应用程序的一周的访问权限） 或数据存档的限时访问包含服务的促销的产品。   
   
   
   
 非续订订阅与其他产品类型之间的主要差异：

-  在 iTunes Connect 中的产品定义不包括术语。 应用程序代码必须能够推断的有效期从产品 id。 
-  它们可以购买多个时间 （如一种耗材）。 应用程序都需要管理订阅术语/到期和续订，并防止用户购买重叠的订阅。 
-  StoreKit 还原函数不支持购买。 如果订阅应可跨所有用户的设备，应用程序将需要设计和实现此功能结合使用与远程服务器。 应用程序也要负责备份订阅状态的情况下当设备备份然后还原的从备份。 
-  实现概述
-  使用服务器发出工作流和管理像可使用的产品，则通常应实现非续订订阅。 


## <a name="about-free-subscriptions"></a>有关免费订阅

免费订阅允许开发人员将可用的内容放在 Newsstand 应用 （它们不能在非 Newsstand 应用中使用）。 启动免费订阅后它将在所有用户的设备上可用。 免费订阅永不过期;它们仅结束卸载应用程序。

### <a name="implementation-overview"></a>实现概述

免费订阅的行为类似于自动续订订阅得多。 应用程序必须在 iTunes Connect 中具有可在购买免费订阅产品。 如果购买的用户，免费订阅购买应验证如自动续订订阅产品。 可以还原免费订阅的事务。


## <a name="about-auto-renewable-subscriptions"></a>有关自动续订订阅

Newsstand 应用程序主要用于自动续订订阅。 它们表示的产品的一段给定时间的时间，在 iTunes Connect （设置到 1 年范围内的 7 天的期间） 中配置为授予用户访问动态内容的权限。 在每个订阅期间，末尾充电用户的 Apple ID，除非用户 opts 扩展订阅自动续订。此产品类型非常适用于杂志或新闻订阅，其中用户获取访问权限发布但其订阅有效的每个问题。

### <a name="implementation-overview"></a>实现概述

应使用 Server-Delivered 产品工作流实现自动续订订阅 (请参阅*回执验证和 Server-Delivered 产品*部分)。

#### <a name="shared-secret"></a>共享的机密

验证你的服务器上的自动续订订阅时，必须在 JSON 请求中使用在应用购买共享密钥。 共享的密钥是通过 iTunes Connect 创建/访问。

从 iTunes Connect 主页上选择**我的应用**:   
   
 [![](subscriptions-and-reporting-images/image2.png "选择我的应用")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
选择应用程序，然后单击**应用内购买**选项卡：

[![](subscriptions-and-reporting-images/image6.png "单击在应用内购买选项卡")](subscriptions-and-reporting-images/image6.png#lightbox)

从页面底部，选择**视图或生成一个共享的机密**:
   
 [![](subscriptions-and-reporting-images/image40.png "选择视图或生成一个共享的机密")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "生成共享的密钥")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 若要使用的共享的机密，请将其包含在验证自动续订订阅，如下的应用内购买回执时发送到 Apple 的服务器中的 JSON 负载：

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

响应的状态字段将为零，如果购买有效，作为与其他产品类型。

#### <a name="downloading-items-after-the-initial-subscription-term"></a>在初始订阅期限后下载项目

作为订阅的产品的一部分，代码应经常验证对 Apple 的服务器的最新已知的回执。 如果具有自动-续订订阅自最后一个验证，则 JSON 响应将包含通知发生的事务应用程序的其他字段 （应扩展订阅有效性）。 JSON 响应将包含：

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

如果状态为零然后订阅是否仍有效，并且其他字段保留有效的数据。 如果状态为 21006 订阅已过期。 请参阅[验证自动续订订阅回执](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html)其他错误代码的文档。

#### <a name="restoring-auto-renewable-subscriptions"></a>还原自动续订订阅

您就会得到多个事务 – 原始购买事务以及每个时间段内订阅已续订单独的事务。 你需要跟踪的开始日期和条款，若要了解在有效期。   
   
   
   
 SKPaymentTransaction 对象不包含订阅期限-应使用不同的产品 ID 的每个词和编写代码，可以推测下列订阅期间： 从购买日期的事务。

#### <a name="testing-auto-renewal"></a>测试自动续订

若要更加轻松地测试订阅，在沙盒中测试时，被压缩其持续时间。 1 周订阅续订每 3 分钟，订阅续订每隔一小时的 1 年。 订阅将自动续订最多个测试在沙盒中使用时的 6 次。

## <a name="reporting"></a>报表

iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) 提供：   
   
 **销售和趋势**– 显示详细信息的应用程序的下载更新和应用内购买。   
   
 **付款和财务报表**– 详细信息的应用程序以及与你和你所欠多少所做的列表付款获得收入。

销售和趋势报表的示例所示：   

 [![](subscriptions-and-reporting-images/image42.png "销售和趋势报表示例")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 此外，还有[**黑连接移动**iOS 应用 （iTunes 链接）](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8)。
对于某些可用的统计信息的 iPhone 屏幕快照所示：   
   
 [![](subscriptions-and-reporting-images/image43.png "对于某些可用的统计信息的 iPhone 屏幕快照")](subscriptions-and-reporting-images/image43.png#lightbox)
