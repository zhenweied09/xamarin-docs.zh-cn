---
title: "应用内购买基础知识和配置"
ms.topic: article
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 302bb1225067ad401f97ee6bad88b4cd16c6dc95
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="in-app-purchase-basics-and-configuration"></a>应用内购买基础知识和配置

实现应用内购买要求应用程序利用 StoreKit API 在设备上。 StoreKit 管理与 Apple 的 iTunes 服务器以获取产品信息并执行事务的所有通信。 必须配置预配配置文件应用内购买并且必须在 iTunes Connect 中输入产品信息。

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit 管理与 Apple 的所有通信，在此图中所示")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

使用应用商店提供的应用内购买需要以下设置和配置：

-  **iTunes Connect** – 配置要销售的产品和设置来测试购买的沙盒用户帐户。 你必须还提供你的银行和税务信息向 Apple 以便它们可以汇款替你收集中涉及的金额。
-   **iOS 设置门户**– 创建捆绑标识符，并启用你的应用程序的应用商店访问权限。
-  **存储工具包**– 将代码添加到显示产品、 购买产品和还原的事务的应用程序。
-  **自定义代码**– 若要将跟踪客户的购买情况和提供的产品或它们已购买的服务。 你可能还需要实现的服务器端进程验证回执，如果您的产品包含的服务器 （例如丛书和杂志问题） 从下载的内容。


有两种存储工具包"服务器环境":

-  **生产**– 与真正的现金交易。 仅可通过已提交并批准 apple 应用程序访问。 此外必须评审和批准它们可在生产环境之前应用内购买产品。
-  **沙盒**– 如果你的测试情况。 产品后提供此处立即创建 （审批过程仅适用于生产环境）。 在沙盒中使用的事务需要测试用户 (不实际 Apple Id)，以执行事务。

## <a name="in-app-purchase-rules"></a>应用内购买规则

无法接受其他形式的付款的数字的产品或服务在你的应用，也不涉及这些或参考你的用户从其在应用内。 这意味着您不能接受信用卡或 PayPal 应用内购买时的最合适的付款机制。 一种特殊情况购买应用外的数字产品，但对于使用在应用程序，例如购买网站上与特定"登录"相关联的丛书和使用应用程序中的"登录名"，允许用户访问权限的购买的书籍。
不允许的应用程序运行这种方式提及或链接到外部购买功能 – 开发人员必须进行通信，此功能为其用户 （可能通过电子邮件市场营销或其他直接的通道） 的其他方式。

但是，由于不能使用应用内购买物理货物上，因为允许你的情况下使用备用的付款机制 （如。 信用卡、 PayPal） 从在应用内。

Apple 必须批准每个产品，然后它就会在销售 – 名称、 描述和的产品屏幕快照，才能查看。 产品评审时间都是与应用程序评审相同。

你无法为产品选择任何价格 – 你可能只需选择价格层 Apple 支持每个国家/地区/币种具有特定值。 不同的市场中，不能有不同的价格层。

## <a name="configuration"></a>配置

在编写任何应用内购买代码之前，你必须执行在 iTunes Connect 中的某些设置工作 ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) 和 iOS 设置门户 ( [developer.apple.com/iOS](http://developer.apple.com/iOS))。

在编写任何代码之前，应完成以下三个步骤：

-  **Apple 开发人员帐户**– 你银行和税务将信息提交到 Apple。
-  **iOS 设置门户**– 确保你的应用程序具有有效的应用程序 ID (不带星号通配符 * 中它) 并应用购买中启用了。
-  **iTunes Connect 应用程序管理**– 将产品添加到你的应用程序。


### <a name="apple-developer-account"></a>Apple 开发人员帐户

生成和分发免费应用程序需要在配置很少[iTunes Connect](https://itunesconnect.apple.com)，但是要销售付费应用或应用内购买你需要 Apple 提供银行和税务信息。 单击**协议、 Tax 和银行**从主菜单此处所示：

 [![](in-app-purchase-basics-and-configuration-images/image2.png "协议、 Tax 和银行从主菜单上单击")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

你的开发人员帐户应具有**iOS 付费应用程序**协定实际上，此屏幕截图中所示：

 [![](in-app-purchase-basics-and-configuration-images/image3.png "开发人员帐户应具有的 iOS 付费应用程序实际上协定")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

你将不能测试任何 StoreKit 功能，直到你**iOS 付费应用程序**协定 – 在代码中的 StoreKit 调用将会失败直到处理完 Apple 你**协定、 税收和银行**信息。

### <a name="ios-provisioning-portal"></a>iOS 预配门户

在中设置新的应用程序**应用 Id**部分**iOS 设置门户**。 若要创建新的应用程序 ID，请转到[成员中心的 ios 设置门户](https://developer.apple.com/membercenter/index.action)，导航到**证书、 标识符和配置文件**该门户中，然后单击在上的一节**标识符**下*iOS 应用程序*。 然后，单击"+"在顶部权限生成新的应用程序 id。


创建新的窗体**应用 Id**

 如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image4.png "用于创建新的应用 Id 窗体")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

输入适合于*说明*，因此你可以轻松地识别此列表中的应用程序 ID。 有关*应用 ID 前缀*，选择团队 id。

#### <a name="bundle-identifierapp-id-suffix-format"></a>捆绑标识符 / 应用 ID 后缀格式

您可以使用你喜欢的任意字符串你**捆绑标识符**（只要它是在你的帐户中唯一的），但是 Apple 建议遵循反向 DNS 格式，而不是使用任何任意字符串。 本文附带的示例应用程序用于 com.xamarin.storekit.testing 捆绑标识符，但同样有效，以使用如 my_store_example 标识符 （即使 Apple 不建议这样做）。

> [!IMPORTANT]
> **请注意**: Apple 还允许通配符格式星号要添加到末尾**捆绑标识符**，以便单个应用程序 ID 可以使用多个应用程序，但是_-通配符应用 Id 不能用于在 AppPurchase_。 通配符捆绑标识符可能 com.xamarin.* 示例

#### <a name="enabling-app-services"></a>启用应用程序服务

请注意，**应用内购买**将在服务列表中自动启用：

 [![](in-app-purchase-basics-and-configuration-images/image5.png "将在服务列表中自动启用应用内购买")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>预配配置文件

通常情况下，选择你已设置为应用内购买的应用程序 ID 创建开发和生产预配配置文件。 请参阅[iOS 设备设置](~/ios/get-started/installation/device-provisioning/index.md)和[发布到应用商店](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)有关详细信息的指南。

## <a name="itunes-connect"></a>iTunes Connect

单击**我的应用**在 iTunes Connect 来创建或编辑一个 iOS 应用程序项目中。 应用程序概述页如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image6.png "应用程序的概述页")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

单击**应用内购买**来创建或编辑您的产品的销售。 此屏幕截图显示与已添加的多个产品的示例应用程序：

 [![](in-app-purchase-basics-and-configuration-images/image7.png "示例应用程序与已添加的有好几种产品")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

该过程以添加新产品具有两个步骤：

1.   选择的产品类型： [ ![ ](in-app-purchase-basics-and-configuration-images/image8.png "选择的产品类型")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2.   输入产品的属性，包括产品 Id，定价层和本地化的说明： [ ![ ](in-app-purchase-basics-and-configuration-images/image9.png "输入的产品属性")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

每个应用内购买产品所需的字段的描述如下：


### <a name="reference-name"></a>引用名称

向你的用户; 不显示引用名称它仅供内部使用，并仅出现在 iTunes Connect。

### <a name="product-id-format"></a>产品 ID 格式

产品标识符只能包含字母数字 (A-Z、 a-z、 0-9)，下划线 (_) 字符和句点 （.） 字符。 虽然你标识符，可以使用任何字符串，Apple 将建议反向 DNS 格式。 例如，示例应用程序使用此捆绑标识符：

 `com.xamarin.storekit.testing`

因此若要找出应用内购买产品的约定将如下所示：

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

不强制此命名约定，只需一个建议，帮助你管理你的产品。 此外，尽管遵循相同的反向 DNS 约定，产品标识符是*不相关*到的捆绑标识符和是否不需要以相同的字符串开头。 它仍然是有效使用如 photo_product_greyscale 标识符 （即使 Apple 不建议这样做）。

产品 ID 不会显示给用户，但它用于引用应用程序代码中的产品。

### <a name="product-type"></a>产品类型

有五种类型的应用内购买产品，你可以提供：

1.  **可使用**– 事，使用了，如中游戏玩家可以花费的货币。 如果用户执行备份/还原或否则刷新其设备后，可使用的事务不会不获取还原也 （这将有效地让玩家再次重新一样的优势）。 应用程序代码必须确保事务完成时，就会立即提供可使用 item。
1.  **非耗材**– 用户拥有一次购买的产品，如数字杂志问题或游戏级别。
1.  **自动续订订阅**– 只需实际杂志订阅，如在订阅期结束 Apple 自动再次收费客户和永久或客户之前显式扩展订阅术语中，取消它。 这是 Newsstand 应用的首选的付款方法 （事实上，应用程序必须支持这种付款方式 Newsstand 分发批准）。
1.  **免费订阅**– 只可以提供在 Newsstand 启用应用中，并在其设备上允许客户在访问订阅内容。 免费订阅永不过期。
1.  **非续订订阅**– 应该用于出售的限时访问静态内容，例如照片存档一个月的访问权。


 *当前，本文档介绍只能使用第一个两个产品类型 （耗材和非耗材）。*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>价格层

应用商店不允许你选择为你的产品任意价格 – Apple 提供可以从选择的固定的价格层。 价格中每个货币、 修复和 Apple 有权调整相对价格 （例如，在特定的货币和美元之间相对外 exchange 速率持续更改）。

Apple 提供的价格矩阵来帮助你选择所需的货币/价格正确的层。 价格矩阵 (2012 年 8 月) 的一段摘录所示：

 [![](in-app-purchase-basics-and-configuration-images/image10.png "2012 年 8 月的价格矩阵的摘要")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

在撰写本文时 (2013 年 6 月)，有 87 价格 （美元） 从层到美元 999.99 0.99。 定价矩阵显示价格，将付费客户和还将收到来自 Apple – 这量小于其 30%的电量以及任何本地税款这些项需要收集 （请注意，在美国和加拿大卖方接收 99 c p 70 c 的示例产品，不过澳大利亚卖方获得仅 63 c 由于货物&amp;服务税务还要缴纳按销售价格)。

可以在任何时候，包括在未来某个日期生效的计划的价格更改更新您的产品的定价。 此屏幕截图显示如何添加将来日期价格更改 – 价格将暂时更改从第 1 层为第 3 层年 9 月的月份仅：

 [![](in-app-purchase-basics-and-configuration-images/image11.png "其中价格将暂时更改从第 1 层为第 3 层年 9 月的月份仅将来日期价格更改")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>不支持的可用产品

Apple 提供了 Newsstand 应用特殊免费订阅选项，尽管它不是可以为任何其他应用内购买类型设置为零 （免费） 价格。 虽然您可以编辑 (即。 较低) 的销售促销价格，不能进行应用内购买免费，通过 iTunes Connect。

### <a name="localization"></a>本地化

在 iTunes Connect 中可以为任意数量的支持的语言输入不同的名称和说明文本。 每种语言可以添加/编辑在通过一个弹出窗口：

 [![](in-app-purchase-basics-and-configuration-images/image12.png "每种语言可以添加/编辑在通过一个弹出窗口")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 显示你的应用程序中的产品信息，本地化的文本时，可供你通过 StoreKit 显示。 此外必须将货币显示本地化以显示正确的符号和十进制格式设置 – 文档后面部分中介绍了此格式设置。

### <a name="app-store-review"></a>应用商店评审

相同应用-每个产品评审 apple 然后才允许转上销售。 产品可能会被拒绝的不适宜内容中的名称或描述，或者 Apple 可能决定，你选择的错误的产品类型 （如。 你已创建书本或杂志问题但使用的可使用的产品类型）。 产品评论可能需要长达应用查看。

第一次使用应用内购买启用 （无论它是一个新的应用，还是功能已添加到现有） 提交应用程序还必须选择某些产品，它具有提交。 ITunes Connect 门户将提示你这样做，请在此屏幕截图所示：

 [![](in-app-purchase-basics-and-configuration-images/image13.png "在 iTunes Connect 门户将提示你提交以及某些产品")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 应用程序和应用内购买将会审查在一起，以便它们都获得批准一次 （因此该应用程序不会转到已批准的任何产品的商店 ！）。

第一个版本的应用内购买功能获得批准后，可以添加更多的产品，并将其提交供随时查看。 你还可以选择提交新版本特定的应用内购买产品，以及使用**版本详细信息**页面为提示提供的建议。

请参阅[应用商店查看准则](https://developer.apple.com/appstore/guidelines.html)有关详细信息。

 [第 2 部分-存储工具包概述和检索产品信息](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
