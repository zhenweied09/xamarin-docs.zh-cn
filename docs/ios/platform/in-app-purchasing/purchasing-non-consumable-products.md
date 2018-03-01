---
title: "购买非耗材产品"
ms.topic: article
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: f5fbd2282dc2f1d5e9a3fa6d29e7b74fde89fc42
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="purchasing-non-consumable-products"></a>购买非耗材产品

非耗材产品拥有由客户。 应当是他们将始终拥有访问到它们，即使当其设备丢失/被盗或者它们购买一个新。 它们可用于丛书、 杂志问题、 游戏级别、 照片筛选器，pro 功能，等等。一旦用户购买了非耗材产品，它们永远不需要再次支付相关费用。 如果你的代码会意外地允许他们尝试，StoreKit 将显示一条消息，已购买。

## <a name="non-consumable-products-sample"></a>非耗材产品示例

[InAppPurchaseSample 代码](https://developer.xamarin.com/samples/monotouch/StoreKit/)包含一个名为项目*NonConsumables*。 代码示例演示如何实现非耗材产品使用照片筛选器作为示例。 一旦你已购买筛选器则你可以将其反复应用至的照片。 从不需要以重新购买它。   
   
   
   
 购买过程所示这一系列的屏幕截图 –**购买**按钮将变为功能激活按钮：   
   
   
   
 [ ![](purchasing-non-consumable-products-images/image34.png "这一系列的屏幕截图所示购买过程")](purchasing-non-consumable-products-images/image34.png)   
   
   
   
 购买过程是一种耗材; 相同主要差异是在购买的应用程序代码中的跟踪方式。 在此示例中购买按钮才可用，如果尚未购买产品，否则按钮激活该功能本身。   
   
   
   

下图显示了类和要执行非耗材产品购买的应用商店服务器之间的交互：   
   
   
   
 [ ![](purchasing-non-consumable-products-images/image35.png "类与应用程序存储服务器执行非耗材产品之间的交互购买")](purchasing-non-consumable-products-images/image35.png)   
   
   
   
 可使用的示例中的主要区别是，完成购买后用户界面更新以防止重新购买。 在此示例中，成功的事务通知更新用户界面，以便**购买**按钮转换为激活该功能本身的按钮。

## <a name="re-purchasing-non-consumable-products"></a>重新购买非耗材产品

通常，你的代码应隐藏或产品具有已成功购买后，若要防止用户尝试再次购买产品购买按钮重新调整其用途。 示例应用程序会通过更改**购买**到使工作的示例照片筛选器的按钮的按钮。   
   
   
   
 在一些情况下即应用程序无法判断是否已购买非耗材产品：

-  如果应用程序被删除并重新安装到设备上，所有购买记录都将消失，（除非 / 直到用户执行备份还原）。 
-  如果用户具有应用程序将两个 （或多个） 的设备上安装，以及购买商品，一个设备。 与其他设备将继续显示可用于购买产品。 
-  如果客户试图重新购买非耗材产品在这些情况下，应用商店将完成再次免费产品。 首先将显示用户界面来执行购买 （例如，显示确认警报并且将需要 Apple ID） 但是用户然后会看到一条消息提示，已购买产品。  
   
   
   
 在此方案中的代码路径是完全与正则购买相同，唯一的区别在于：

-  用户在该产品不再次获取收费。
-  `SKPaymentTransaction`对象传递到应用程序将具有`OriginalTransaction`最初购买产品时生成的事务是指的属性。 
-  销售非耗材产品的应用程序还必须实现 StoreKit 的**还原**功能，可帮助用户检索现有购买。 
