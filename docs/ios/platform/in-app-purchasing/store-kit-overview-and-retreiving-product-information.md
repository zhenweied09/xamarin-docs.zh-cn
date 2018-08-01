---
title: StoreKit 概述和检索产品信息，请参阅 Xamarin.iOS
description: 本文档概述了 StoreKit。 它描述用于 StoreKit，测试 StoreKit 交互、 显示销售产品、 处理无效的产品和显示本地化的价格的类。
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 964b97e82db8e79cb32598d0c955fac3ab122314
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787219"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>StoreKit 概述和检索产品信息，请参阅 Xamarin.iOS

应用内购买的用户界面下面的屏幕截图所示。
发生任何事务之前，应用程序必须检索该产品的价格和显示的说明。 然后当用户按**购买**，应用程序管理的确认对话框和 Apple ID 登录名的 StoreKit 向发出请求。 假设然后事务成功，StoreKit 通知应用程序代码中，其必须存储事务结果，且用户提供访问其购买的产品。   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit 通知应用程序代码，从而必须将事务结果存储并为用户提供访问其购买的产品")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>类

实现应用内购买需要 StoreKit 框架中的以下类：   
   
 **SKProductsRequest** – 对 StoreKit 为已批准产品销售 （应用商店） 的请求。 可以使用大量产品 Id 的配置。

-   **SKProductsRequestDelegate** – 声明方法以处理产品请求和响应。 
-   **SKProductsResponse** – 从 StoreKit （应用商店） 发送回委托。 包含匹配 Id 发送与请求产品 SKProducts。 
-   **SKProduct** – 从 StoreKit （即你在 iTunes Connect 中配置） 检索产品。 包含有关该产品，如产品 ID、 标题、 描述和价格的信息。 
-   **SKPayment** – 使用产品 ID 创建并添加到要执行购买的付款队列。 
-   **SKPaymentQueue** -排队付款请求发送到 Apple。 通知是由于正在处理每个付款而触发的。 
-   **SKPaymentTransaction** – 表示已完成的事务 （采购请求已处理的应用商店并发送回你的应用程序通过 StoreKit）。 事务可能已采购、 还原或已失败。 
-   **SKPaymentTransactionObserver** – 响应由 StoreKit 付款队列生成的事件的自定义子类。 
-   **StoreKit 操作都是异步**– 启动 SKProductRequest 或 SKPayment 添加到队列，控制权返回给你的代码后。 从 Apple 的服务器接收数据时，StoreKit 将 SKProductsRequestDelegate 或 SKPaymentTransactionObserver 子类上调用方法。 


下图显示各种 StoreKit 类 （抽象类必须在你的应用程序中实现） 之间的关系：   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "必须在应用程序中实现各种 StoreKit 类抽象类之间的关系")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 在本文档后面的更详细地解释了这些类。

## <a name="testing"></a>正在测试

大多数 StoreKit 操作需要实际设备进行测试。 检索产品信息 （例如。 价格&amp;说明) 将在模拟器但购买并还原操作将返回一个错误 (如 FailedTransaction 代码 = 5002 出现未知错误)。

注意： StoreKit 在 iOS 模拟器中无法运行。 当在 iOS 模拟器中运行你的应用程序，StoreKit 将记录一个警告，如果你的应用程序尝试检索付款队列。 必须在实际设备上测试应用商店。   
   
   
   
 重要说明： 不使用登录你在设置应用程序中的测试帐户。 你可以使用设置应用程序登录超出任何现有的 Apple ID 帐户，则你必须等到系统会提示您*在您的应用内购买序列*登录使用测试 Apple id。   
   
   
   

如果你尝试登录到具有一个测试帐户的实际存储区，它将自动转换为实际的 Apple id。 该帐户将不再可用于测试。

若要测试 StoreKit 代码以链接到测试存储区 （在 iTunes Connect 中创建） 的特殊测试帐户都必须注销的正则 iTunes 测试帐户和登录名。 若要注销当前帐户访问**设置 > iTunes 应用商店和**如下所示：

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "若要注销的当前帐户访问设置 iTunes 和应用商店")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
然后使用测试帐户登录*StoreKit 在你的应用内进行请求时*:



若要创建在 iTunes Connect 中的测试用户，请单击**用户和角色**在主页上。

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "若要创建测试用户在 iTunes Connect 单击用户和角色主页上")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

选择**沙盒测试人员**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "选择沙盒测试人员")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

将显示现有用户的列表。 你可以将新用户添加或删除现有记录。 门户不 （当前） 让你查看或编辑现有测试用户，因此建议你保留的每个测试用户的创建 （尤其是你分配的密码） 的良好记录。 一旦删除测试用户的电子邮件地址不能重复使用另一个测试帐户。  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "现有用户的列表")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 新的测试用户具有类似属性到实际的 Apple ID （如名称、 密码、 机密问题和答案）。 记录在此处输入的所有详细信息。 **选择 iTunes 应用商店**字段将确定哪些货币，并在应用购买的语言将使用时登录以该用户身份。

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "选择 iTunes 应用商店字段将确定用户的货币和对其应用内购买的语言")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>检索产品信息

销售应用内购买产品的第一步显示它： 从显示应用商店中检索的当前价格和描述。   
   
   
   
 无论哪种类型的产品应用销售 （耗材，非耗材或类型），显示的产品信息中检索的过程是订阅的相同的。 本文附带的 InAppPurchaseSample 代码包含一个名为项目*耗材*演示如何检索生产显示的信息。 它演示如何：

-  创建的实现`SKProductsRequestDelegate`和实现`ReceivedResponse`抽象方法。 此代码示例调用此`InAppPurchaseManager`类。 
-  咨询 StoreKit 以查看是否有权付款 (使用`SKPaymentQueue.CanMakePayments`)。 
-  实例化`SKProductsRequest`具有已定义在 iTunes Connect 中的产品 Id。 这通过完成本示例的`InAppPurchaseManager.RequestProductData`方法。 
-  上调用 Start 方法`SKProductsRequest`。 这会触发对应用商店服务器的异步调用。 委托 ( `InAppPurchaseManager` ) 将调用后使用的结果。 
-  该委托的 ( `InAppPurchaseManager` )`ReceivedResponse`方法返回从应用商店 （产品价格和说明或无效的产品有关的消息） 的数据会更新 UI。 

总体的交互如下所示 ( **StoreKit**是内置于 iOS 的与**应用商店**表示 Apple 的服务器):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "检索产品信息图")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>显示产品信息示例

[InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *耗材*示例代码演示如何检索产品信息。 此示例的主屏幕将显示从应用程序存储中检索的两种产品的信息：   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "主屏幕将显示从应用商店中检索的信息产品")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 中更详细地描述了示例代码，以检索并显示产品信息。


#### <a name="viewcontroller-methods"></a>ViewController 方法

`ConsumableViewController`类将管理的产品 Id 为类中的硬编码的两种产品的价格的显示。

```csharp
public static string Buy5ProductId = "com.xamarin.storekit.testing.consume5credits",
   Buy10ProductId = "com.xamarin.storekit.testing.consume10credits";
List<string> products;
InAppPurchaseManager iap;
public ConsumableViewController () : base()
{
   // two products for sale on this page
   products = new List<string>() {Buy5ProductId, Buy10ProductId};
   iap = new InAppPurchaseManager();
}
```

在类级别还应存在 NSObject 声明将用于安装程序`NSNotificationCenter`观察程序：

```csharp
NSObject priceObserver;
```

ViewWillAppear 方法中观察者创建并分配使用默认通知中心：

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 在结束`ViewWillAppear`方法中，调用`RequestProductData`方法可开始 StoreKit 请求。 一旦建立了此请求，StoreKit 将以异步方式 Apple 的与服务器联系以获取信息，以及将它添加回你的应用程序。 这通过实现`SKProductsRequestDelegate`子类 ( `InAppPurchaseManager`)，为下一节中所述。

```csharp
iap.RequestProductData(products);
```

要显示的价格和描述的代码只需从 SKProduct 中检索信息，并将其分配给 UIKit 控件 (请注意，我们显示`LocalizedTitle`和`LocalizedDescription`– StoreKit 自动解决的正确的文本和价格基于用户的帐户设置）。 以下代码放入我们在上面创建的通知：

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
   var info = notification.UserInfo;
   if (info.ContainsKey(NSBuy5ProductId)) {
       var product = (SKProduct) info.ObjectForKey(NSBuy5ProductId);
       buy5Button.Enabled = true;
       buy5Title.Text = product.LocalizedTitle;
       buy5Description.Text = product.LocalizedDescription;
       buy5Button.SetTitle("Buy " + product.Price, UIControlState.Normal); // price display should be localized
   }
}
```

最后，`ViewWillDisappear`方法应确保删除观察者：

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>SKProductRequestDelegate (InAppPurchaseManager) 方法

`RequestProductData`方法调用时应用程序希望检索产品价格和其他信息。 它分析的产品 Id 到正确的数据类型的集合，然后创建`SKProductsRequest`利用该信息。 调用 Start 方法会导致不会对 Apple 的服务器的网络请求。 请求将以异步方式运行，并调用`ReceivedResponse`方法的委托时已成功完成。

```csharp
public void RequestProductData (List<string> productIds)
{
   var array = new NSString[productIds.Count];
   for (var i = 0; i < productIds.Count; i++) {
       array[i] = new NSString(productIds[i]);
   }
   NSSet productIdentifiers = NSSet.MakeNSObjectSet<NSString>(array);
   productsRequest = new SKProductsRequest(productIdentifiers);
   productsRequest.Delegate = this; // for SKProductsRequestDelegate.ReceivedResponse
   productsRequest.Start();
}
```

iOS 将自动将请求路由到的沙盒或生产应用商店，具体取决于哪些使用 – 运行该应用程序的预配配置文件的版本以便请求时你正在开发或测试你的应用程序将有权访问每个产品配置在 iTunes Connect （甚至那些已提交或 apple 批准） 中。 在生产环境中你的应用程序时，StoreKit 请求将只返回的信息**已批准**产品。   
   
   
   
 `ReceivedResponse` Apple 的服务器已做出了数据后调用重写的方法。 因为这将在后台调用，该代码应分析出有效的数据，并使用一条通知将产品信息发送到任何 ViewControllers 正在侦听的该通知。 要收集有效的产品信息并发送通知的代码所示：

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   SKProduct[] products = response.Products;
   NSDictionary userInfo = null;
   if (products.Length > 0) {
       NSObject[] productIdsArray = new NSObject[response.Products.Length];
       NSObject[] productsArray = new NSObject[response.Products.Length];
       for (int i = 0; i < response.Products.Length; i++) {
           productIdsArray[i] = new NSString(response.Products[i].ProductIdentifier);
           productsArray[i] = response.Products[i];
       }
       userInfo = NSDictionary.FromObjectsAndKeys (productsArray, productIdsArray);
   }
   NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerProductsFetchedNotification, this, userInfo);
}
```

尽管不显示在图中，`RequestFailed`应该也能重写方法，以便在应用商店服务器不可访问 （或某些其他错误发生） 的情况下，你可以向用户提供一些反馈。 示例代码只是将写入控制台，但实际的应用程序可能选择查询到`error.Code`属性和实现自定义行为 （如向用户警报）。

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

此屏幕截图显示了示例应用程序后立即加载 （当没有产品信息可用时）：

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "在加载时没有产品的信息后立即示例应用程序")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>无效的产品

`SKProductsRequest`也可能返回无效的产品 Id 的列表。 由于下列其中一项通常返回无效的产品：   
   
   
   
 **拼错产品 ID** – 接受仅有效的产品 Id。   
   
 **产品未获批准**– 测试时，应通过返回的所有产品的销售清除`SKProductsRequest`; 但是在生产中都返回已批准的产品。   
   
 **应用程序 ID 不是显式**– 通配符应用 Id （用星号） 不允许应用内购买。   
   
 **不正确的预配配置文件**– 如果进行了更改至你的应用程序配置的 provisioning portal （如启用应用内购买），请记得重新生成并使用正确的预配配置文件时生成的应用程序。   
   
 **iOS 付费应用程序协定没有到位**– StoreKit 功能才会生效根本没有为你的 Apple 开发人员帐户有效的协定。   
   
 **二进制文件处于已拒绝状态**– StoreKit 功能将无法工作，如果在 （不管是通过应用商店团队中，或由开发人员） 的已拒绝状态的以前提交的二进制文件。

`ReceivedResponse`方法中的示例代码输出到控制台无效的产品：

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>显示本地化的价格

价格层跨所有国际应用程序商店中指定每个产品的特定价格。 若要确保每个货币的价格正确显示，请使用下面的扩展方法 (在中定义`SKProductExtension.cs`) 而不是每个价格属性`SKProduct`:

```csharp
public static class SKProductExtension {
   public static string LocalizedPrice (this SKProduct product)
   {
       var formatter = new NSNumberFormatter ();
       formatter.FormatterBehavior = NSNumberFormatterBehavior.Version_10_4;  
       formatter.NumberStyle = NSNumberFormatterStyle.Currency;
       formatter.Locale = product.PriceLocale;
       var formattedString = formatter.StringFromNumber(product.Price);
       return formattedString;
   }
}
```

设置按钮的标题的代码使用扩展方法如下：

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

使用两个不同 iTunes 测试帐户 （一个 American 存储区），一个用于日语存储会导致以下屏幕快照：   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "两个不同 iTunes 测试帐户显示特定结果的语言")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 请注意，存储会影响设备的语言设置会影响标签和其他本地化的内容时使用的产品信息和价格货币的语言。   
   
   
   
 回想一下，若要使用不同的存储测试帐户必须**注销**中**设置 > iTunes 应用商店和**，然后重新启动该应用程序在使用不同的帐户登录。 若要更改设备的语言，请转到**设置 > 常规 > 国际 > 语言**。

