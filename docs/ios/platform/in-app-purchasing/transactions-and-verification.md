---
title: 事务和验证
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: c8d86d0ce3119b3e104a65a170ab141484af44a7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="transactions-and-verification"></a>事务和验证

## <a name="restoring-past-transactions"></a>还原过去的事务

如果你的应用程序支持可还原的产品类型，则必须包含某些用户界面元素，以允许用户还原这些购买。
此功能允许客户将产品添加到其他设备，还是将被擦除干净后还原到同一设备的产品或删除并重新安装应用。 以下产品类型是可还原的：

-  非耗材产品
-  自动续订订阅
-  免费订阅


还原过程应更新的记录在满足您的产品的设备上保留。 客户可以选择要在任何时候，在任何其设备上还原。 还原过程重新发送为该用户; 所有之前的事务然后，应用程序代码必须确定要使用该信息 （例如，如果已在设备上，该购买后的记录和不是，创建的购买记录检查和启用用户产品） 执行的操作。

### <a name="implementing-restore"></a>实现还原

用户界面**还原**按钮调用以下方法的触发器 RestoreCompletedTransactions `SKPaymentQueue`。

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit 将以异步方式将还原请求发送到 Apple 的服务器中。   
   
   
   
 因为`CustomPaymentObserver`注册为事务观察程序，它将接收消息，Apple 的服务器响应时。 响应将包含此用户所 （跨所有设备） 执行此应用程序中的所有事务。 该代码将遍历每个事务，检测到还原状态，并调用`UpdatedTransactions`方法，以处理它，如下所示：

```csharp
// called when the transaction status is updated
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
       case SKPaymentTransactionState.Purchased:
          theManager.CompleteTransaction(transaction);
           break;
       case SKPaymentTransactionState.Failed:
          theManager.FailedTransaction(transaction);
           break;
       case SKPaymentTransactionState.Restored:
           theManager.RestoreTransaction(transaction);
           break;
default:
           break;
       }
   }
}
```

如果没有为用户，可还原产品`UpdatedTransactions`不调用。   
   
   
   
 最简单的可能代码还原在此示例中给定的事务将执行相同的操作作为时购买发生，但`OriginalTransaction`属性用于访问产品 ID:

```csharp
public void RestoreTransaction (SKPaymentTransaction transaction)
{
   // Restored Transactions always have an 'original transaction' attached
   var productId = transaction.OriginalTransaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId); // it's as though it was purchased again
   FinishTransaction(transaction, true);
}
```

更复杂的实现可能检查其他`transaction.OriginalTransaction`属性，例如原始日期和回执数。 此信息将非常有用的某些产品类型 （如订阅）。

#### <a name="restore-completion"></a>还原完成

`CustomPaymentObserver`有两个时在还原过程已完成 （成功或失败），将由 StoreKit 调用的其他方法如下所示：

```csharp
public override void PaymentQueueRestoreCompletedTransactionsFinished (SKPaymentQueue queue)
{
   Console.WriteLine(" ** RESTORE Finished ");
}
public override void RestoreCompletedTransactionsFailedWithError (SKPaymentQueue queue, NSError error)
{
   Console.WriteLine(" ** RESTORE FailedWithError " + error.LocalizedDescription);
}
```

在示例中这些方法不执行任何操作，但实际的应用程序可能会选择实现消息发送到用户或某些其他功能。

## <a name="securing-purchases"></a>保护购买

中的两个示例文档使用`NSUserDefaults`跟踪购买：   
   
 **耗材**– 信用购买的平衡是一个简单`NSUserDefaults`递增每个购买的整数值。   
   
 **非耗材**– 每个照片筛选器购买存储中的键 / 值对为`NSUserDefaults`。

使用`NSUserDefaults`会示例代码保持简单，但不提供非常安全解决方案，因为它可能非常从技术上讲注重节省成本的用户可以更新 （跳过付款机制） 的设置。   
   
注意： 实际应用程序应采用用于存储购买不进行用户篡改的内容的安全机制。 这可能涉及加密和/或其他技术，包括远程服务器身份验证。   
   
 机制还应旨在充分利用 iOS、 iTunes 和 iCloud 的内置备份和恢复功能。 这将确保，用户还原备份后其以前的购买将立即可用。   
   
   
 请参阅 Apple 的安全编码指南的多个特定于 iOS 的指南。

## <a name="receipt-verification-and-server-delivered-products"></a>回执验证和服务器传递产品

到目前为止，本文档中的示例包括单独的应用程序直接与应用商店服务器进行通信来执行购买交易，取消锁定功能或已编码到应用的功能。   
   
   
   
 Apple 提供的额外级别的购买安全通过允许购买回执独立要验证由另一个服务器，可用于交付数字内容作为一部分购买之前验证请求 (如数字簿或杂志）。   
   
   
   
 **内置产品**– 如本文档中的示例，作为随应用程序的功能将存在要购买的产品。 应用内购买使用户能够访问该功能。
产品 Id 是硬编码。   
   
 **服务器传递产品**– 产品包含的可下载之前成功事务导致要下载的内容存储在远程服务器的内容。
示例可能包括丛书或杂志问题。 产品 Id 通常来自外部服务器 （其中也托管产品内容）。 应用程序必须实现可靠的录制时事务已完成，因此，如果内容下载失败可以重新尝试同时不会混淆用户的方式。

### <a name="server-delivered-products"></a>服务器传递产品

某些产品的内容，如丛书和杂志 （或甚至比赛级） 需要在购买过程从远程服务器中下载。 这意味着需要增加一个服务器来存储和交付产品内容，它购买后。

#### <a name="getting-prices-for-server-delivered-products"></a>获取服务器传递产品的价格

由于远程传递产品，因此它还有可能要添加的产品越多随着时间的推移 （而不更新应用程序代码），例如添加更多的丛书或杂志的新问题。 以便应用程序可以发现这些新闻产品，并显示给用户，附加的服务器应存储，并提供此信息。   
   
   
   
 [![](transactions-and-verification-images/image38.png "获取服务器传递产品的价格")](transactions-and-verification-images/image38.png#lightbox)   
   
   
   
 1. 产品信息必须存储在多个位置： 在你的服务器上并在 iTunes Connect 中。 此外，每个产品都将具有与之关联的内容文件。 在成功购买之后，将传递这些文件。   
   
 2. 当用户想要购买产品时，应用程序必须确定哪些产品有。 此信息可能会缓存，但应传递从远程服务器存储产品的主列表。   
   
 3. 服务器返回要分析的应用程序的产品 Id 的列表。   
   
 4. 然后，应用程序确定哪个这些产品 Id，发送给 StoreKit 以检索价格和描述。   
   
 5. StoreKit 向 Apple 的服务器发送的产品 Id 的列表。   
   
 6. 使用有效的产品信息 （说明和当前价格） 进行响应的 iTunes 服务器。   
   
 7. 应用程序的`SKProductsRequestDelegate`显示的产品信息传递给用户。

#### <a name="purchasing-server-delivered-products"></a>购买服务器传递产品

因为远程服务器要求某种方式来验证一个内容请求是否有效 (即。 出于已支付)，回执信息将传递身份验证。 远程服务器该将数据转发到 iTunes 以进行验证，并且，如果成功，包括产品内容到应用程序的响应中。   
   
 [![](transactions-and-verification-images/image39.png "购买服务器传递产品")](transactions-and-verification-images/image39.png#lightbox)   
   
 1. 应用程序将添加`SKPayment`到队列。 如果需要将系统提示你输入其 Apple ID，并要求确认付款用户。   
   
 2. StoreKit 将请求发送到服务器进行处理。   
   
 3. 在事务完成时，服务器将通过事务回执。   
   
 4. `SKPaymentTransactionObserver`子类接收接收并处理它。 由于必须从服务器下载产品，应用程序将启动到远程服务器的网络请求。   
   
 5. 下载请求伴随接收数据，以便远程服务器可以验证其有权访问内容。 应用程序的网络客户端等待对此请求的响应。   
   
 6. 当服务器接收内容请求时，它分析出接收的数据，并发送与 iTunes 服务器直接请求，以验证该回执为有效的事务。 服务器应使用一些逻辑来确定是否将请求发送到的生产或沙盒 URL。 Apple 提供的建议始终使用生产 URL，并且如果切换到沙盒状态： 21007 （沙盒回执发送到生产服务器） – 指[技术注意 2259年常见问题 #16](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)。   
   
 7. iTunes 将检查该回执，是否有效，则返回零状态。   
   
 8. 服务器等待 iTunes 的响应。 如果接收到有效响应时，代码应将放置要包括到应用程序的响应中的关联的产品内容文件。   
   
 9. 应用程序接收并分析该响应，将产品内容保存到设备的文件系统。   
   
 10. 应用程序启用该产品，然后调用 StoreKit 的`FinishTransaction`。 应用程序可能会根据需要显示购买的内容 （例如，显示第一页的购买的书籍或杂志问题）。

非常大的产品内容文件的备用实现可能涉及只需将事务回执存储在步骤 #9，以便可以快速完成事务，并提供要下载实际的产品内容的用户的用户界面在随后的某个时间。 后续的下载请求可以重新发送访问所需的产品内容文件的存储的回执。

### <a name="writing-server-side-receipt-verification-code"></a>编写服务器端接收验证代码

可通过一个简单 HTTP POST 请求/响应包含步骤 #5 至工作流关系图中的 #8 验证在服务器端代码接收。   
   
   
   
 提取`SKPaymentTansaction.TransactionReceipt`应用程序中的属性。 这是需要发送到 iTunes 以进行验证 （步骤 5） 的数据。

Base64 编码的事务回执数据 （不管是在步骤 #5 或 6）。

创建简单的 JSON 负载如下：

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP POST 到 JSON [ https://buy.itunes.apple.com/verifyReceipt ](https://buy.itunes.apple.com/verifyReceipt)用于生产或[ https://sandbox.itunes.apple.com/verifyReceipt ](https://sandbox.itunes.apple.com/verifyReceipt)用于测试。   
   
 JSON 响应将包含以下项：

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

零状态指示有效回执。 你的服务器可以继续完成购买的产品的内容。 该回执密钥包含与相同的属性的 JSON 字典`SKPaymentTransaction`收到的应用程序中，以便该服务器代码可查询以检索信息，如 product_id 和购买数量此字典的对象。

请参阅 Apple 的[验证存储回执](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/StoreKitGuide/VerifyingStoreReceipts/VerifyingStoreReceipts.html#//apple_ref/doc/uid/TP40008267-CH104-SW1)文档的其他信息。

#### <a name="using-aspnet"></a>使用 ASP.NET

对于 C# 开发人员没有一个名为的有用的开源项目[APNS 清晰](https://github.com/Redth/APNS-Sharp)其包含在 ASP.NET 中的工作原理的接收验证码。 具体而言，`Receipt.cs`和`ReceiptVerification.cs`文件中[ `Jdsoft.Apple.AppStore` ](https://github.com/Redth/APNS-Sharp/tree/master/JdSoft.Apple.AppStore)在将目录添加到.NET 网站，以轻松实现从 Server-Delivered 产品工作流关系图中的步骤 #6 至 #8。   
   
   
   
 与 iTunes 服务器的通信使用 JSON，轻松地在 C# 中处理。   
   
   
   
 请参阅 Apple 的应用内购买[回执验证](https://developer.apple.com/library/ios/#releasenotes/StoreKit/IAP_ReceiptValidation/_index.html)有关如何验证回执是有效的详细信息的文档。

### <a name="3rd-party-receipt-verification"></a>第三方回执验证

没有提供用于接收验证 （和其他操作） 可以使用而不是生成你自己的服务器的平台的公司。 Xamarin 不认可这些服务; 示例：它们是只需此处提及的引用。

#### <a name="urban-airship"></a>城市 Airship

城市 Airship 提供多个 iOS 应用，包括回执验证和推送通知的不同后端服务。   
   
 [http://urbanairship.com/products/in-app-purchase/](http://urbanairship.com/products/in-app-purchase/)



