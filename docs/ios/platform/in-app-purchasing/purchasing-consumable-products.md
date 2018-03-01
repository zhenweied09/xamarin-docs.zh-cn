---
title: "购买耗材"
ms.topic: article
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 7366a4ce5cbb6a3026a7445a03f03b45d89d9210
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="purchasing-consumable-products"></a>购买耗材

可使用的产品是最简单的方法实现，因为不存在还原的要求。 它们可用于产品，如中游戏货币或单一用途的功能。 用户可以重新再次购买耗材转移和转移。

## <a name="built-in-product-delivery"></a>内置产品交付

本文档附带的示例代码演示了内置产品 – 的产品 Id 是硬编码到应用程序，因为它们紧密耦合付款之后解锁功能的代码。 在购买过程可以可视化如下：   
   
[ ![购买过程可视化效果](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png)     
   
 基本工作流是：   
   
   
   
 1. 应用程序将添加`SKPayment`到队列。 如果需要将系统提示你输入其 Apple ID，并要求确认付款用户。   
   
 2. StoreKit 将请求发送到服务器进行处理。   
   
 3. 在事务完成时，服务器将通过事务回执。   
   
 4. `SKPaymentTransactionObserver`子类接收接收并处理它。   
   
 5. 应用程序使产品 (通过更新`NSUserDefaults`或某种其他机制)，然后调用 StoreKit 的`FinishTransaction`。

没有其他类型的工作流 – *Server-Delivered 产品*–，它是文档的更高版本中所述 (请参阅明*回执验证和 Server-Delivered 产品*)。

## <a name="consumable-products-example"></a>可使用产品示例

[InAppPurchaseSample 代码](https://developer.xamarin.com/samples/monotouch/StoreKit/)包含一个名为项目*耗材*实现基本中的游戏货币 （称为"调整信用额度"）。 此示例演示如何实现这两种应用内购买产品，以允许用户购买许多"monkey 信用额度"按他们所希望 – 在实际应用中将还会有的支出它们某种方式 ！   
   
   
   
 应用程序显示在这些屏幕截图中 – 每个购买将多个"monkey 信用额度"添加到用户的平衡：   
   
   
   
 [ ![每个购买将多个 monkey 信用额度添加到用户平衡](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png)   
   
   
   
 自定义类，StoreKit 和应用商店之间的交互如下所示：   
   
   
   
 [ ![自定义类，StoreKit 和应用商店之间的交互](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png)

&nbsp;

### <a name="viewcontroller-methods"></a>ViewController 方法

除了属性和检索产品信息所需的方法中，视图控制器需要其他通知观察者以侦听与购买相关通知。 这些只是`NSObjects`，它会注册并删除在`ViewWillAppear`和`ViewWillDisappear`分别。

```csharp
NSObject succeededObserver, failedObserver;
```

构造函数还将创建`SKProductsRequestDelegate`子类 ( `InAppPurchaseManager`)，反过来创建并注册`SKPaymentTransactionObserver`( `CustomPaymentObserver`)。   
   
   
   
 处理应用内购买事务的第一部分是处理按钮按下，当用户想要购买某件事情中的示例应用程序中的以下代码所示：

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

   
   
 用户界面的第二部分正在处理通知，则事务成功，在这种情况下通过更新显示的平衡：

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

如果事务已取消出于某种原因，用户界面的最后一部分显示一条消息。 示例代码中一条消息只是写入输出窗口：

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

上的视图控制器，这些方法，除了耗材采购事务也需要代码上`SKProductsRequestDelegate`和`SKPaymentTransactionObserver`。

### <a name="inapppurchasemanager-methods"></a>InAppPurchaseManager 方法

示例代码实现的数购买 InAppPurchaseManager 类上的相关的方法包括`PurchaseProduct`创建方法`SKPayment`实例，并将其添加到队列中等待处理：

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

添加到队列的付款是一个异步操作。 应用程序重新获得控件时 StoreKit 处理事务，并将其发送到 Apple 的服务器。 它在此时该 iOS 将验证用户在登录到应用商店，然后如果需要，则会提示她输入 Apple ID 和密码。   
   
   
   
 假设用户成功向应用商店进行身份验证并同意事务，`SKPaymentTransactionObserver`将接收 StoreKit 的响应并调用以下方法以完成事务，并完成它。

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

最后一步是确保，通知 StoreKit 已成功完成事务，通过调用`FinishTransaction`:

```csharp
public void FinishTransaction(SKPaymentTransaction transaction, bool wasSuccessful)
{
   // remove the transaction from the payment queue.
   SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);  // THIS IS IMPORTANT - LET'S APPLE KNOW WE'RE DONE !!!!
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {transaction},new NSObject[] {new NSString("transaction")});
       if (wasSuccessful) {
           // send out a notification that we've finished the transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionSucceededNotification, this, userInfo);
       } else {
           // send out a notification for the failed transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionFailedNotification, this, userInfo);
       }
   }
}
```

一旦产品交付，`SKPaymentQueue.DefaultQueue.FinishTransaction`必须调用以从付款队列中删除该事务。

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>SKPaymentTransactionObserver (CustomPaymentObserver) 方法

StoreKit 调用`UpdatedTransactions`方法在它从 Apple 的服务器接收响应并传递的数组时`SKPaymentTransaction`为你的代码，若要检查的对象。 该方法循环访问每个事务，并执行不同的功能 （如此处所示），可基于事务状态：

```csharp
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
           default:
               break;
       }
   }
}
```

`CompleteTransaction`本节前面部分涵盖方法 – 它将保存到购买详细信息`NSUserDefaults`、 完成的事务，以及 StoreKit 和最后通知 UI 以更新。

### <a name="purchasing-multiple-products"></a>购买多个产品

如果应用程序购买多个产品中，意义使用`SKMutablePayment`类，然后设置数量字段：

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

处理已完成的事务的代码也必须查询要正确完成购买的数量属性：

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   var qty = transaction.Payment.Quantity;
   if (productId == ConsumableViewController.Buy5ProductId)
       CreditManager.Add(5 * qty);
   else if (productId == ConsumableViewController.Buy10ProductId)
       CreditManager.Add(10 * qty);
   else
       Console.WriteLine ("Shouldn't happen, there are only two products");
   FinishTransaction(transaction, true);
}
```

当用户购买多个数量时，StoreKit 确认警报将反映的数量、 单价和它们将会付费，总价格，如下面的屏幕截图中所示：

[ ![确认购买](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png)

## <a name="handling-network-outages"></a>处理网络中断

应用内购买需要 StoreKit 与 Apple 的服务器进行通信的工作网络连接。 如果网络连接不可用，则应用内购买将不可用。

### <a name="product-requests"></a>产品请求

如果在网络出现故障时进行`SKProductRequest`、`RequestFailed`方法`SKProductsRequestDelegate`子类 ( `InAppPurchaseManager`) 将会调用，如下所示：

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {error},new NSObject[] {new NSString("error")});
       // send out a notification for the failed transaction
       NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerRequestFailedNotification, this, userInfo);
   }
}
```

然后，ViewController 侦听通知，并显示一个消息以购买按钮：

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

因为网络连接可能暂时性移动设备上，应用程序可能想要监视使用 SystemConfiguration 框架中，网络状态和重试时网络连接不可用。 请参阅 Apple 的或可使用它。

### <a name="purchase-transactions"></a>购买事务

StoreKit 付款队列将存储和转发采购请求如果可能，以便在购买过程失败的网络时，网络中断的影响有所不同具体取决于。   
   
   
   
 如果在事务期间发生了错误`SKPaymentTransactionObserver`子类 ( `CustomPaymentObserver`) 将具有`UpdatedTransactions`调用方法和`SKPaymentTransaction`类将处于失败状态。

```csharp
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
           default:
               break;
       }
   }
}
```

`FailedTransaction`方法检测的错误是否是由于用户取消，如下所示：

```csharp
public void FailedTransaction (SKPaymentTransaction transaction)
{
   //SKErrorPaymentCancelled == 2
   if (transaction.Error.Code == 2) // user cancelled
       Console.WriteLine("User CANCELLED FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   else // error!
       Console.WriteLine("FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   FinishTransaction(transaction,false);
}
```

即使事务失败，`FinishTransaction`必须调用方法以从付款队列中删除事务：

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

示例代码，以使 ViewController 可以显示一条消息，然后发送通知。 应用程序将不会显示其他消息如果用户已取消事务。 可能会出现其他错误代码包括：

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>处理限制

**设置 > 常规 > 限制**的 iOS 功能允许用户锁定其设备的某些功能。   
   
   
   
 您可以查询是否允许用户以通过应用内购买`SKPaymentQueue.CanMakePayments`方法。 如果此方法返回 false 用户无法访问应用内购买。 如果尝试购买，StoreKit 自动将向用户显示一条错误消息。 通过检查此值可以改为隐藏购买按钮你的应用程序，或采取其他措施来帮助用户。   
   
   
   
 在`InAppPurchaseManager.cs`文件`CanMakePayments`方法会包装 StoreKit 函数如下：

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

若要测试此方法，使用**限制**iOS 若要禁用的功能**应用内购买**:   
   
   
   
 [ ![使用 iOS 限制功能禁用应用内购买](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png)   
   
   
   
 此代码示例从`ConsumableViewController`响应`CanMakePayments`通过显示若返回 false **AppStore 禁用**上禁用的按钮的文本。

```csharp
// only if we can make payments, request the prices
if (iap.CanMakePayments()) {
   // now go get prices, if we don't have them already
   if (!pricesLoaded)
       iap.RequestProductData(products); // async request via StoreKit -> App Store
} else {
   // can't make payments (purchases turned off in Settings?)
   // the buttons are disabled by default, and only enabled when prices are retrieved
   buy5Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
   buy10Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
}
```

应用程序的外观这样**应用内购买**功能是受限 – 购买按钮被禁用。   
   
   
   
 [ ![功能是在应用内购买限制按钮被禁用，在购买时，则应用程序类似如下所示](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png)   
   
   
   

产品信息仍可以为请求时`CanMakePayments`是 false，因此应用程序仍可以检索和显示价格。 这意味着如果我们删除`CanMakePayments`选中此项从代码购买按钮将仍是活动状态，但是尝试购买时用户将看到一条消息，**不允许应用内购买**（由 StoreKit 生成当付款队列访问时）：   
   
   
   
 [ ![不允许应用内购买](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png)   
   
   
   
 实际应用程序可能采用不同的方法来处理此限制，如完全隐藏按钮，并可能提供比 StoreKit 会自动显示的警报的更详细的消息。

