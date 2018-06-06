---
title: 在 Xamarin.iOS PassKit
description: 钱包是一个系统 iOS 应用，将存储并显示条形码和链接在真实其手机上的客户事务的其他信息。
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 0a4fd39e312cf96ac59eae97b1212f001c4ef799
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788343"
---
# <a name="passkit-in-xamarinios"></a>在 Xamarin.iOS PassKit

_钱包是一个系统 iOS 应用，将存储并显示条形码和链接在真实其手机上的客户事务的其他信息。_

钱包是一款应用对 Iphone 和 iPod 触摸 ios 6。 它将存储并显示条形码和链接在真实其手机上的客户事务的其他信息。 传递由商人生成，并且将其发送给客户通过电子邮件，Url 或 merchant 自己 iOS 应用程序。 钱包存储和组织的 phone 上的所有传递，并根据日期/时间或设备的位置在锁定屏幕上显示传递提醒。

本文档介绍钱包，传递工具包 API 使用 Xamarin.iOS，并讨论如何在你的服务器上实现传递。

 [![](passkit-images/image1.png "钱包存储和组织在电话上的所有传递")](passkit-images/image1.png#lightbox)


## <a name="requirements"></a>要求

本文档中讨论的应用商店工具包功能需要 iOS 6 和 Xcode 4.5，以及 Xamarin.iOS 6.0。


## <a name="introduction"></a>介绍

传递工具包可解决的关键问题是条形码的分发和管理。 如何条形码当前使用的一些实际应用示例包括：

-   **购买在线电影票证**– 客户通常电子邮件发送表示其票证的条形码。 打印并转到影院条目扫描此条形码。
-   **会员卡**– 客户执行大量不同的特定于应用商店的卡，在其钱包或钱包，用于显示和扫描时它们购买商品。
-   **优惠券**– 优惠券通过电子邮件，以可打印网页，通过建立信箱报纸和杂志中的条形码，以及分发。 客户将它们的扫描，以接收货物、 服务或折扣返回的存储区。
-   **火车传递**– 类似于购买电影票证。


传递工具包提供的每种方案的一种替代方法：

-   **电影票证**– 之后购买，客户将添加一个事件票证阶段 （通过电子邮件或网站链接）。 作为电影方法的时间，通过将自动出现在锁屏提醒一下，在到达影院传递是方便地检索并钱包中显示的扫描。
-   **会员卡**– 而不是 （或除了） 提供物理卡，存储可以发出 （在网站登录后，或通过电子邮件） 存储卡传递。 存储可以提供其他功能，例如更新的上推送通知，通过传递的帐户之间的平衡，并使用地理位置服务传递无法自动锁定屏幕上时出现客户附近存储位置。
-   **优惠券**– 优惠券传递可以轻松地生成具有唯一的特征有助于跟踪，并通过电子邮件或网站链接分发。 当用户是附近的特定位置和/或在给定日期 （例如，当接近到期日期） 时，下载的优惠券可以会自动出现在锁定屏幕。 因为优惠券已存储在用户的电话号码，它们始终是很方便，并且不执行获取错误放置。 优惠券可能鼓励客户下载的配套应用程序，因为应用商店的链接可以合并到增加的客户合作的过程。
-   **火车传递**– online 签入过程之后，客户会收到通过电子邮件或链接其初始启用传递。 由传输提供程序提供辅助应用程序可能包括签入过程中，并还允许客户执行其他功能，例如选择其座位或顿饭。 传输提供程序可以使用推送通知来更新传递的如果延迟或取消传输。 作为上客时间方法传递会出现在锁定屏幕中，作为提醒并传递提供快速访问。


就其核心而言，传递工具包提供简单方便的方法来存储及显示 iPhone 或 iPod 触摸设备上的条形码。 额外的时间和位置锁定屏幕集成，推送通知和助理应用程序将其集成提供了一个非常复杂的销售 foundation 票务和计费服务。


## <a name="pass-kit-ecosystem"></a>传递工具包生态系统

传递工具包不只是在 CocoaTouch API，而是它是应用程序、 数据和服务，以方便安全共享和管理的条形码以及其他数据的更大生态系统的一部分。 此高级别关系图显示中创建和使用传递的不同实体可以涉及：

 [![](passkit-images/image2.png "此高级别关系图显示实体所涉及到创建和使用传递")](passkit-images/image2.png#lightbox)

生态系统的每个段都具有明确定义的角色：

-   **钱包**– Apple 的内置的 iOS 应用 （适用于 iPhone 和 iPod touch)，将存储并显示传递。 这是将呈现在现实世界 （即条形码会显示，以及在通过中的所有本地化数据） 中使用的唯一位置。
-   **伴生应用**– iOS 6 生成的传递提供程序来扩展它们发出，例如将值添加到存储卡，更改的座位上客传递或其他业务特定的函数传递的功能的应用程序。 助理应用不需要的过程，以很有用。
-   **你的服务器**– 安全服务器可以生成并签名分发传递。 辅助应用程序可能会连接到服务器以生成新的传递或请求更新到现有的传递。 你可以根据需要实现钱包将调用以更新传递的 web 服务 API。
-   **APNS 服务器**– 你的服务器能够通知钱包给定设备使用 APNS 上传递的更新。 将通知推送到钱包其中然后将与你的服务器联系以更改的详细信息。 助理应用不需要此功能实现 APNS (它们可侦听`PKPassLibraryDidChangeNotification`)。
-   **管道应用**– 应用程序，不直接操作 （如伴随应用执行操作） 的传递，但这可以通过识别传递，并允许他们添加到钱包中提高其实用程序。 邮件客户端，社交网络浏览器和其他数据聚合应用所有可能遇到的附件或传递到链接。


因此，很值得注意某些组件是可选的并且变得更简单的传递工具包实现是可行的整个生态系统看起来很复杂。

## <a name="what-is-a-pass"></a>什么是传递？

处理过程的票证、 优惠券或卡表示的数据集合。 它可能供个人使用一次 （并因此包含详细信息，例如航班数和座位分配） 或它可能也是可由任意数量的用户 （例如折扣优惠券） 共享的多个使用令牌。 详细的说明位于 Apple 的[有关传递文件](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)文档。


### <a name="types"></a>类型

当前五个支持的类型，可由传递的布局和顶部边缘区分钱包应用程序中：

-  **事件票证**– 小半圆切除。
-   **初始启用传递**– 凹口另一方面，特定于传输的图标中可以指定 （例如。 bus、 训练、 飞行）。
-   **存储卡**– 舍入前，如信用卡或借记卡。
-  **优惠券**– 穿孔顶部。
-  **泛型**– 与存储卡，并舍入顶部相同。


五个传递的类型显示在此屏幕截图 (按顺序： 优惠券，泛型类型，存储卡、 上客传递和事件票证):

 [![](passkit-images/image3.png "五个传递的类型显示在此屏幕截图")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>文件结构

传递文件是实际使用的 ZIP 存档 **.pkpass**扩展，包含某些特定 JSON 文件 （必需）、 各种图像文件 （可选） 以及本地化的字符串 （也是可选的）。

-   **pass.json** -必需。 包含传递的所有信息。
-   **manifest.json** -必需。 包含每个文件中的签名文件除外的阶段和 (manifest.json) 此文件的 SHA1 哈希的值。
-   **签名**-必需。 创建的签名`manifest.json`iOS 设置门户中生成的证书文件。
-  **logo.png** – 可选。
-  **background.png** – 可选。
-  **icon.png** – 可选。
-  **可本地化的字符串文件**– 可选。


传递文件的目录结构如下所示 （这是 ZIP 存档的内容）：

 [![](passkit-images/image4.png "传递文件的目录结构如下所示")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.json

JSON 是格式，因为传递通常在服务器上创建 – 则意味着生成代码是与平台无关的服务器上。 在每个传递的三个主要部分是信息的：

-   **teamIdentifier** – 这链接到你的应用商店帐户生成的所有传递。 此值是在 iOS 设置门户中可见。
-   **passTypeIdentifier** – 在预配门户添加到组中的寄存器将一起传递 （如果生成多个类型）。 例如，咖啡店可能会创建存储卡传递类型以使其客户能够获得会员信用额度，但还单独优惠券传递的类型来创建和分发折扣优惠券。 该相同咖啡店甚至可能容纳实时音乐事件，并为那些颁发事件票证传递。
-   **serialNumber** – 在此的唯一字符串`passTypeidentifier`。 值是不透明的钱包，但对于跟踪特定传递与你的服务器通信时非常重要。


没有大量其他 JSON 中的键的每个阶段中，它的一个示例所示：

``` 
{
   "passTypeIdentifier":"com.xamarin.passkitdoc.banana",  //Type Identifier (iOS Provisioning Portal)
   "formatVersion":1,                                     //Always 1 (for now)
   "organizationName":"Xamarin",                          //The name which appears on push notifications
   "serialNumber":"12345436XYZ",                          //A number for you to identify this pass
   "teamIdentifier":"XXXAAA1234",                         //Your Team ID
   "description":"Xamarin Demo",                          //
   "foregroundColor":"rgb(54,80,255)",                    //color of the data text (note the syntax)
   "backgroundColor":"rgb(209,255,247)",                  //color of the background
   "labelColor":"rgb(255,15,15)",                         //color of label text and icons
   "logoText":"Banana ",                                  //Text that appears next to logo on top
   "barcode":{                                            //Specification of the barcode (optional)
      "format":"PKBarcodeFormatQR",                       //Format can be QR, Text, Aztec, PDF417
      "message":"FREE-BANANA",                            //What to encode in barcode
      "messageEncoding":"iso-8859-1"                      //Encoding of the message
   },
   "relevantDate":"2012-09-15T15:15Z",                    //When to show pass on screen. ISO8601 formatted.
  /* The following fields are specific to which type of pass. The name of this object specifies the type, e.g., boardingPass below implies this is a boarding pass. Other options include storeCard, generic, coupon, and eventTicket */
   "boardingPass":{
/*headerFields, primaryFields, secondaryFields, and auxiliaryFields are arrays of field object. Each field has a key, label, and value*/
      "headerFields":[          //Header fields appear next to logoText
         {
            "key":"h1-label",   //Must be unique. Used by iOS apps to get the data.
            "label":"H1-label", //Label of the field
            "value":"H1"        //The actual data in the field
         },
         {
            "key":"h2-label",
            "label":"H2-label",
            "value":"H2"
         }
      ],
      "primaryFields":[       //Appearance differs based on pass type
         {
            "key":"p1-label",
            "label":"P1-label",
            "value":"P1"
         }
      ],
      "secondaryFields":[     //Typically appear below primaryFields
         {
            "key":"s1-label",
            "label":"S1-label",
            "value":"S1"
         }
      ],
      "auxiliaryFields":[    //Appear below secondary fields
         {
            "key":"a1-label",
            "label":"A1-label",
            "value":"A1"
         }
      ],
      "transitType":"PKTransitTypeAir"  //Only present in boradingPass type. Value can
                                        //Air, Bus, Boat, or Train. Impacts the picture
                                        //that shows in the middle of the pass.
   }
}
```

### <a name="barcodes"></a>条形码

支持仅二维格式： PDF417、 Aztec、 QR。 Apple 声明 1d 条形码是背光 phone 屏幕上扫描到不适合。

备用条形码下面显示的文本是可选 – 有些店主想要能够手动读取/类型。

ISO 8859-1 编码是将读取你传递的扫描系统使用的编码的最常见，检查。

### <a name="relevancy-lock-screen"></a>相关性 （锁定屏幕）

有两种类型的可能会导致传递要锁定屏幕上显示的数据：

 **位置**

在通过中，例如客户经常访问时，存储或影院或机场的位置，可以指定最多 10 个位置。 客户无法将设置为通过助理应用这些位置，或提供程序无法确定其使用情况数据 （如果收集与客户的权限）。

在锁定屏幕上显示传递，则围墙计算，以便当用户离开区域传递隐藏的锁定屏幕。 Radius 绑定传递样式以防止滥用行为。

 **日期和时间**

传递中，可以指定只有一个日期/时间。 日期和时间可用于触发锁定屏幕上客传递和事件票证的提醒。

可以通过推送或更新通过 PassKit API，以便无法在 （如影院或体育复杂季节票证） 的多个使用票证的情况下更新的日期/时间。

### <a name="localization"></a>本地化

将转换为多个语言传递是类似于本地化 iOS 应用程序 – 创建具有特定目录的语言`.lproj`扩展，并将放在本地化的元素。 文本翻译应输入到`pass.strings`文件，而本地化的映像应具有与它们将传递根目录中的图像相同的名称。

## <a name="security"></a>安全性

通过使用在 iOS 设置门户中生成的私有证书进行签名。 对传递进行签名的步骤如下：

1.  计算传递目录中的每个文件的 SHA1 哈希 (不包括`manifest.json`或`signature`文件，都不应在仍存在这一阶段)。
1.  编写`manifest.json`作为其哈希与每个文件名 JSON 键/值列表。
1.  使用证书进行签名`manifest.json`文件并将结果写入到名为的文件`signature`。
1.  压缩的所有内容，然后为生成的文件提供`.pkpass`文件扩展名。


因为你的私匙需要登录传递，应仅是您控制的安全服务器上完成此过程。 不能分发你的键，以尝试并生成应用程序中传递。

 
## <a name="configuration-and-setup"></a>配置和安装程序

本部分包含说明，帮助设置你设置的详细信息并创建第一次传递。

### <a name="provisioning-passkit"></a>设置 PassKit

为了使过程，以输入应用商店，必须链接到开发人员帐户。 这需要两个步骤：

1.  必须使用的唯一标识符，称为传递类型 id。 注册传递
1.  必须生成有效的证书进行签名与开发人员的数字签名的过程。

若要创建传递类型 ID 执行以下。


<a name="create-passid"/>

#### <a name="create-a-pass-type-id"></a>创建传递类型 ID

第一步是设置传递类型 ID 为每个不同_类型_的过程，以支持。 传递的 ID （或传递类型标识符） pass 中创建的唯一标识符。 我们将使用此 ID 将其与你使用的证书的开发人员帐户链接传递。

1. 在[证书、 标识符和配置文件一部分的 iOS 设置门户](https://developer.apple.com/account/overview.action)，导航到**标识符**和选择**传递类型 Id** 。 然后选择**+** 按钮以创建新的传递类型： [ ![ ](passkit-images/passid.png "创建新的传递类型")](passkit-images/passid.png#lightbox)

2.   提供**说明**（名称） 和**标识符**（唯一字符串） 作为要传递。 请注意，传递的所有类型 Id 必须以字符串都开头`pass.`在此示例中，我们使用`pass.com.xamarin.coupon.banana`: [ ![ ](passkit-images/register.png "提供的说明和标识符")](passkit-images/register.png#lightbox)


3.   通过按确认传递 ID**注册**按钮。


<a name="generate" />

#### <a name="generate-a-certificate"></a>生成证书

若要为此传递类型 ID 创建新证书，请执行以下操作：

1.  从列表中，选择新创建的传递 ID，然后单击**编辑**: [ ![ ](passkit-images/pass-done.png "从列表中选择新的传递 ID")](passkit-images/pass-done.png#lightbox)

    然后，选择**创建证书...** :

    [![](passkit-images/cert-dist.png "选择创建证书")](passkit-images/cert-dist.png#lightbox)


2.  按照步骤创建证书签名请求 (CSR)。
  
3. 按**继续**按钮的开发人员门户，然后上载 CSR 以生成您的证书。

4. 下载证书，并双击它以将其安装在你的密钥链。


现在，我们已为此传递类型 ID 创建证书下, 一节将介绍如何手动构建一个传递。

有关钱包的设置的详细信息，请参阅[使用功能](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md)指南。

 <a name="Create_a_Pass_Manually" />

### <a name="create-a-pass-manually"></a>手动创建一个传递

现在，我们已创建传递类型我们开发人员可以手动生成过程，以在模拟器或设备上进行测试。 若要创建一个传递的步骤如下：

-  创建一个目录来包含传递文件。
-  创建包含所有所需的数据的 pass.json 文件。
-  （如果需要），请在文件夹中包括图像。
-  计算在文件夹中，每个文件的 SHA1 哈希值并将写入 manifest.json。
-  使用下载的证书.p12 文件进行签名 manifest.json。
-  压缩目录的内容，并使用.pkpass 扩展重命名。


可以用于生成传递这篇文章的示例代码中有一些源文件。 使用中的文件`CouponBanana.raw`CreateAPassManually 目录的目录。 以下文件存在：

 [![](passkit-images/image18.png "这些文件存在")](passkit-images/image18.png#lightbox)

打开 pass.json 并编辑 JSON。 至少必须更新`passTypeIdentifier`和`teamIdentifer`以匹配你的 Apple 开发人员帐户。

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

然后必须计算每个文件的哈希，并创建`manifest.json`文件。 完成后，它将如下所示：

```csharp
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

接下来必须为使用的证书 （.p12 文件） 来生成此传递类型 id。 此文件中生成签名

 <a name="Signing_On_a_Mac" />


#### <a name="signing-on-a-mac"></a>在 Mac 上的签名

下载**钱包种子支持材料**从[Apple 下载](https://developer.apple.com/downloads/index.action?name=Passbook)站点。 使用`signpass`工具要将转变为 （此功能还将计算 SHA1 哈希，ZIP 输出到.pkpass 文件） 传递你的文件夹。

 <a name="Signing_On_a_PC" />


#### <a name="signing-on-a-pc"></a>在 PC 上签名

在示例中为存在此项目的代码是一个名为项目`signpassnet`，在.NET 中在 Windows 上运行。 它将尝试模拟 Apple 的工具，但它包含要少得多的验证代码。

 <a name="Testing" />


#### <a name="testing"></a>正在测试

如果你是检查这些工具的输出 （通过设置为.zip 的文件名，然后打开它），你会看到以下文件 (请注意添加`manifest.json`和`signature`文件):

 [![](passkit-images/image19.png "检查这些工具的输出")](passkit-images/image19.png#lightbox)

已签名，ZIPped 并重命名文件 （如后 到`BananaCoupon.pkpass`) 可以将它拖到模拟器以测试，或电子邮件发送给你自己检索在真实设备上。 您应看到到一个屏幕**添加**的传递，如下：

 [![](passkit-images/image20.png "添加传递屏幕")](passkit-images/image20.png#lightbox)

通常情况下该进程将自动在服务器上，但是手动传递创建可能的小型企业仅创建不需要后端服务器的支持的优惠券。 一个选项。

 <a name="Wallet" />

## <a name="wallet"></a>Wallet

钱包是传递工具包生态系统的中央。 此屏幕截图中显示空钱包，和传递列表和单个传递的外观：

 [![](passkit-images/image21.png "此屏幕截图中显示空钱包，和传递列表和单个传递的外观")](passkit-images/image21.png#lightbox)

钱包的功能包括：

-  它是传递呈现使用其用于扫描条形码的唯一位置。
-  用户可以更改更新的设置。 如果启用，推送通知可以触发对在通过数据的更新。
-  用户可以启用或禁用锁定屏幕集成。 如果启用，这允许传递来自动显示在其锁定屏幕中，基于相关的时间和位置数据嵌入在传递。
-  如果 web 服务器 URL 提供传递 JSON 中，传递的反向端支持刷新请求。
-  可以打开 （或下载） 助理应用如果传递 JSON 中提供应用程序的 ID。
-  传递 （随刻意清除动画） 一起删除。


 <a name="Getting_Passes_into_Wallet" />

## <a name="adding-passes-into-wallet"></a>添加到钱包传递

传递可以通过以下方式添加到钱包：

* **管道应用**– 这些不会直接操作传递，它们只是通过将文件加载并向用户提供将它们添加到钱包的选项。 

* **伴生应用**– 这些写入的提供程序将分发传递，以及提供附加功能以浏览或对其进行编辑。 Xamarin.iOS 应用程序具有完全访问权限传递工具包 API 来创建和操作传递。 然后，可以添加传递到钱包使用`PKAddPassesViewController`。 中的更详细地描述了此过程**伴侣应用**本文档部分。

### <a name="conduit-applications"></a>通道应用程序

通道应用程序是中间应用程序可能会收到传递代表用户，并且应进行编程，从而识别其内容类型，并提供将添加到钱包功能。 管道的应用程序的示例包括：

-   **邮件**– 识别附件作为一个传递。
-   **Safari** – 单击传递 URL 链接时识别传递内容类型。
-   **其他自定义应用**– 任何应用程序接收附件或打开链接 （社交媒体客户端、 邮件读取器等）。


此屏幕截图显示如何**邮件**在 iOS 中 6 识别传递附件和 （如果接触） 提供给**添加**到钱包。

 [![](passkit-images/image22.png "此屏幕截图显示如何在 iOS 6 中的邮件识别传递附件")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "此屏幕截图显示如何提供邮件以添加到钱包传递附件")](passkit-images/image23.png#lightbox)

如果你要构建的应用程序可能是用于传递通道，它们能够被识别：

-  **文件扩展名**-.pkpass
-  **MIME 类型**-application/vnd.apple.pkpass
-  **实用程序**– com.apple.pkpass


通道应用程序的基本操作是检索传递文件，并调用传递工具包`PKAddPassesViewController`若要为用户提供的选项将传递添加到其钱包。 此视图控制器的实现上，在下一节中介绍**伴侣应用**。

通道应用程序不需要为特定的传递类型 ID 设置助理应用程序可以做的一样。

## <a name="companion-applications"></a>助理应用程序

助理应用程序提供与应用程序关联的处理通过，包括创建一个传递、 更新与密码关联的信息和否则管理传递的其他功能。

助理应用程序不应尝试重复钱包的功能。 它们不应显示传递的扫描。

此本部分的其余部分介绍如何生成的基本助理应用交互使用传递的工具包。

### <a name="provisioning"></a>预配

由于钱包是存储技术，应用程序需要单独设置，并且不能使用团队预配配置文件或通配符应用 id。 请参阅[使用功能](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md)指南钱包应用程序中创建唯一的应用程序 ID 和预配配置文件。

### <a name="entitlements"></a>权利

**Entitlements.plist**文件应包含在所有最近的 Xamarin.iOS 项目。 若要添加新 Entitlements.plist 文件，请按照中的步骤[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。

若要设置授权执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

双击**Entitlements.plist**解决方案板打开 Entitlements.plist 编辑器中的文件：

![](passkit-images/image31.png "Entitlements.plst 编辑器")

在钱包部分下，选择**启用钱包**选项

![](passkit-images/image32.png "启用钱包授权")


默认选项适用于你的应用程序允许所有传递类型。 但是，很可能要限制你的应用，并仅允许团队传递类型的子集。 若要启用此，请选择**允许子集的团队传递类型**并输入你希望允许的子集的传递类型标识符。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

双击**Entitlements.plist**文件以打开 XML 源文件。

若要添加的钱包授权，设置**属性**到`Passbook Identifiers`在下拉列表中，它将自动设置**类型** `Array`。 然后，设置字符串**值**到`$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "启用钱包授权")

这会使应用允许所有凭证类型。 若要限制你的应用，并仅允许团队传递类型的子集，请将字符串值设置为：

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

其中`pass.$(CFBundleIdentifier)`是已创建的传递 ID[上面](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>调试

如果必须部署应用程序的问题，请检查你正在使用正确**预配配置文件**且`Entitlements.plist`被选为**自定义授权**中文件**iPhone 捆绑签名**选项。

如果在部署时，你会遇到此错误：

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

则`pass-type-identifiers`权利数组不正确 (或不匹配**预配配置文件**)。 验证传递类型 Id 和你的团队 ID 是否正确。

 <a name="Classes" />

## <a name="classes"></a>类

下面的传递工具包类是可用于应用程序访问传递：

-  **PKPass** – 传递的实例。
-  **PKPassLibrary** – 提供 API 来访问设备上的传递。
-  **PKAddPassesViewController** – 用于显示用户后，将保存其钱包传递。
-  **PKAddPassesViewControllerDelegate** – Xamarin.iOS 开发人员


## <a name="example"></a>示例

请参阅本文的示例代码中的 PassLibrary 项目。 它演示了需要在钱包助理应用程序中的以下常见函数：

### <a name="check-that-wallet-is-available"></a>检查钱包可用

钱包不在 iPad 上可用，因此应用程序应检查然后再尝试访问传递工具包功能。

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>创建传递库实例

传递工具包库不是 singleton，应用程序应创建和存储以及要访问传递工具包 API 实例。

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>获取传递的列表

从库中，应用程序可以请求传递的列表。 这样您仅可以看到的传递已替换为你的团队 ID 创建的以及列出的在你授权中，将传递工具包自动筛选此列表。

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

请注意，因此此方法应始终在真实设备上进行测试，模拟器不筛选返回，传递的列表。 添加两个优惠券后，此列表可以显示在 UITableView，示例应用程序如下所示：

 [![](passkit-images/image29.png "示例应用看上去像这样后已添加两个优惠券")](passkit-images/image29.png#lightbox)


### <a name="displaying-passes"></a>显示传递

一组有限的信息仅供在助理应用程序中传递的呈现。

选择此组标准属性从显示的通过，列表的示例代码一样。

```csharp
string passInfo =
                "Desc:" + pass.LocalizedDescription
                + "\nOrg:" + pass.OrganizationName
                + "\nID:" + pass.PassTypeIdentifier
                + "\nDate:" + pass.RelevantDate
                + "\nWSUrl:" + pass.WebServiceUrl
                + "\n#" + pass.SerialNumber
                + "\nPassUrl:" + pass.PassUrl;
```

此字符串显示为此示例中的警报：

 [![](passkit-images/image30.png "在此示例中的优惠券选择警报")](passkit-images/image30.png#lightbox)

你还可以使用`LocalizedValueForFieldKey()`方法来从已设计传递中的字段中检索数据 （因为你将知道哪些域应该会存在）。 示例代码不演示此。

### <a name="loading-a-pass-from-a-file"></a>从文件加载了过程

因为一次过程只能添加到钱包与用户的权限，则必须提供视图控制器以允许这些决定。 此代码用于**添加**在示例中，加载嵌入在应用程序 （你应将此替换为一个已签名） 的预建的传递的按钮：

```csharp
NSData nsdata;
using ( FileStream oStream = File.Open (newFilePath, FileMode.Open ) ) {
        nsdata = NSData.FromStream ( oStream );
}
var err = new NSError(new NSString("42"), -42);
var newPass = new PKPass(nsdata,out err);
var pkapvc = new PKAddPassesViewController(newPass);
NavigationController.PresentModalViewController (pkapvc, true);
```

出现传递**添加**和**取消**选项：

 [![](passkit-images/image20.png "显示的添加和取消的选项传递")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>将一个现有的阶段

替换现有传递不需要用户的权限，但是如果传递尚不存在，它将失败。

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>编辑传递

PKPass 不可变，，因此不能在代码中更新传递对象。 若要更改的数据的应用程序必须有权访问 web 服务器传递可保留的传递的记录并生成新的传递文件与应用程序可以下载该更新后的值。

必须在服务器上执行传递文件创建，因为传递必须使用专用性和安全性必须保持的证书进行签名。

一旦已生成更新的传递文件，使用`Replace`方法覆盖的设备上的旧数据。

### <a name="display-a-pass-for-scanning"></a>显示一个传递的扫描

如前文所述，仅钱包可以显示一个传递的扫描。 可以使用显示传递`OpenUrl`方法如下所示：

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>接收更改通知

应用程序可以侦听对传递库所做的更改使用`PKPassLibraryDidChangeNotification`。 更改可能引起触发更新在后台，因此很好的做法侦听这些应用程序中的通知。

```csharp
noteCenter = NSNotificationCenter.DefaultCenter.AddObserver (PKPassLibrary.DidChangeNotification, (not) => {
    BeginInvokeOnMainThread (() => {
        new UIAlertView("Pass Library Changed", "Notification Received", null, "OK", null).Show();
        // refresh the list
        var passlist = library.GetPasses ();
        table.Source = new TableSource (passlist, library);
        table.ReloadData ();
    });
}, library);  // IMPORTANT: must pass the library in
```

请务必时注册通知，因为 PKPassLibrary 不是 singleton 传递库实例。

## <a name="server-processing"></a>服务器处理

生成服务器应用程序以支持传递工具包的详细的讨论不在此介绍性文章的范围。

中提供的.NET 代码*signpassnet*示例无法用作可以生成传递的服务器端方法的基础。

视图[WWDC 视频： 引入 Passbook，第 2 部分](https://developer.apple.com/videos/wwdc/2012/?include=309#309)从 27:00 分钟，有关详细信息。

### <a name="other-resources"></a>其他资源

请参阅[dotnet passbook](https://github.com/tomasmcguinness/dotnet-passbook)打开源 C# 服务器端代码。

## <a name="push-notifications"></a>推送通知

使用推送通知更新传递的详细的讨论不在此介绍性文章的范围。

你需要实现 apple 响应 web 请求从钱包需要更新时定义的类似于 REST 的 API。 中提供的.NET 代码*signpassnet*示例无法用作用于生成新的传递，由于这些请求的基础。

视图[WWDC 视频： 引入 Passbook，第 2 部分](https://developer.apple.com/videos/wwdc/2012/?include=309#309)从 27:00 分钟，有关详细信息。

## <a name="summary"></a>总结

这篇文章引入传递工具包，所述的某些原因，它为什么很有用，说明必须为完整传递工具包解决方案实现的不同部分。 它所述配置你的 Apple 开发人员帐户，以创建通过，使一个传递手动以及如何从 Xamarin.iOS 应用程序访问传递工具包 Api 的过程所需的步骤。

## <a name="related-links"></a>相关链接

- [CreateAPassManually （示例）](https://developer.xamarin.com/samples/PassKit/)
- [PassKit 示例](https://developer.xamarin.com/samples/monotouch/PassKit/)
- [iOS 6 简介](~/ios/platform/introduction-to-ios6/index.md)
- [使用 passbook 编程指南](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Conceptual/PassKit_PG/Chapters/Introduction.html)
- [开发人员的使用 passbook](https://developer.apple.com/passbook/)
- [有关传递文件](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [工具包 Framework 引用传递](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [工具包 Framework 引用传递](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [使用 passbook Web 服务引用](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
