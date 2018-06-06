---
title: 在 Xamarin.iOS 的 Apple 付费
description: 本指南介绍了设置 Xamarin.iOS 环境以用于 Apple Pay 以支付每个物理商品，如食品、 娱乐和通过您的应用程序的成员身份。 它包括所需的标识符、 证书和授权信息。
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 7033373cddb2503e5912eb17b1e72ece759cc3ad
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786686"
---
# <a name="apple-pay-in-xamarinios"></a>在 Xamarin.iOS 的 Apple 付费

_本指南介绍了设置 Xamarin.iOS 环境以用于 Apple Pay 以支付每个物理商品，如食品、 娱乐和通过您的应用程序的成员身份。它包括所需的标识符、 证书和授权信息。_

Apple Pay 引入了 iOS 8，以及使用户能够支付每个物理货物如食品、 娱乐和通过其 iOS 设备的成员身份。 适用于 iPhone 6 和 iPhone 6 的加号，并还可以与应用商店中购买行为 Apple Watch 配对。 当使用在 iPhone 上，它将使用 Touch ID，作为一种方式进行确认和授权用户的信用卡或借记卡到事务。

## <a name="requirements"></a>要求

Apple Pay 才可用内 iOS 8 和更高版本，并因此需要 Xcode 6 的最少。

此外需要集成到你的应用的 Apple Pay 的以下项：

 - 付款处理器平台
 - 商人标识符
 - Apple Pay 的证书
 - Apple Pay 授权

本文档将介绍更多详细信息中的这些项。

## <a name="differences-between-apple-pay-and-iap"></a>Apple 支付和、 IAP 之间的差异

Apple Pay 的主要区别和*应用内购买*(、 IAP)，与它们出售的产品。 *物理*通过 Apple Pay 售货物; 食品、 住宿和物理娱乐 （如影院票证） 都是此示例。 与此相反，、 IAP 销售*虚拟*商品，如高级或其他内容，以及订阅 – 思考加个月的流式处理服务或在游戏中的额外生活。

使用的框架也是主要的差异;[PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)是用于 Apple Pay，而[StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) 、 IAP 提供框架 API。

与 Apple Pay，Apple[状态](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf)[，它"不对] 收费用户、 商人或开发人员用于 Apple 支付付款"。 相比之下，、 IAP 具有每个事务 30%费用。 此外，与 Apple Pay，事务不会通过 Apple 根本，相反，它将经历支付平台。

## <a name="using-a-payment-processor-platform"></a>使用付款处理器平台

Apple Pay 的基本组成部分之一是一种付款的处理。 虽然可能自行执行此操作，但它需要的重要知识的加密
- Apple 的详见[付款处理指南](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html)。
付款处理平台，另一方面，处理您，以便您能够集中精力生成应用程序的这些操作。

两个选项包括：

- **条带**-注册[Stripe.com](https://stripe.com/)访问其 Api。

- **JudoPay** -签出其[github 上的 Xamarin 示例代码](https://github.com/Judopay/Xamarin-Sample-App)，并在注册[JudoPay.com](https://www.judopay.com/)。

## <a name="provisioning-for-apple-pay"></a>设置 Apple 付费

将应用配置为使用 Apple Pay 需要安装程序在 Apple 开发人员门户和你的应用程序中。 有大量的以成功设置你的应用程序 Apple 支付，应遵循的步骤：

1. 创建商家 ID:
    - 请按照步骤[此处](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. 创建应用程序 ID 的应用付费功能，并向其中添加 merchant:
    - 请按照步骤[此处](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. 为商家 ID 来生成证书：
    - 请按照步骤[此处](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. 生成新创建的应用程序 ID 的配置文件设置：
    - 请按照步骤[此处](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. 添加 Apple Pay 权利：
    - 选择作为详细的 Apple 支付授权[此处](~/ios/deploy-test/provisioning/entitlements.md)，或手动将键/值对添加到文件中，从[此处](~/ios/deploy-test/provisioning/entitlements.md)

## <a name="working-with-apple-pay"></a>使用 Apple 付费

Apple 具有对 Apple Pay 进行多项增强功能，允许用户从网站和通过与 Siri 和映射进行交互的安全付款的 iOS 10。

使用 iOS 10，采用了 iOS 和 watchOS 以支持动态付款网络和一个新的沙盒测试环境已添加了几个新的 Api。

### <a name="apple-pay-website-integration"></a>Apple 支付网站集成

新到 iOS 10，开发人员可以将合并 Apple Pay 直接到其网站使用**ApplePay JS**。 浏览使用 Safari 中 iOS 或 macOS 的网站的用户可以通过验证事务在他们的 iPhone 或 Apple Watch 上使具有 Apple Pay 付款。 有关详细信息，请参阅 Apple 的[ApplePay JP Framework 参考](https://developer.apple.com/reference/applepayjs)。

### <a name="passkit-framework-enhancements"></a>PassKit Framework 增强功能

在 iOS 10 中，已扩展 PassKit framework 以支持 Apple Pay 之外`UIKit`和允许卡颁发者提供在其应用程序中的从其自己卡。


#### <a name="supporting-apple-pay-outside-of-uikit"></a>支持外部 UIKit Apple 付费

通过使用[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)和[PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)，应用程序可以支持提供的相同功能[PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller)而无需使用 UIKit。 需要支持 Apple Pay Apple Watch 上 （和以及特定意向） 这个新的 API 时，它是在其他情况下 （例如现有的应用） 可选的。 但是，Apple 提供的建议将移动到新的 API 越早越好广泛 Apple Pay 的支持，在所有开发人员的应用提供的单个基本代码。 有关意向的详细信息和使用 Siri 集成，请参阅我们[简介 SiriKit](~/ios/platform/sirikit/index.md)文档。

#### <a name="presenting-issuer-cards-from-within-apps"></a>演示从应用中的颁发者卡

与 iOS 10，新的功能已添加到允许卡颁发者提供在其自己的应用程序中从其卡 PassKit framework。 开发人员可以添加`PKPaymentButtonTypeInStore`UIButton 到应用程序的用户界面将显示为卡 Apple Pay 按钮。

`PresentPaymentPass`方法[PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary)类还可以用于以编程方式显示卡。

### <a name="new-payment-network-support"></a>新的付款网络支持

新到 iOS 10，应用程序可以自动支持新的付款网络而无需开发人员无需修改、 重新编译应用程序和将其重新提交到应用商店变为可用时。

新[AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks)方法`PKPaymentNetwork`类允许应用程序以发现在运行时的用户的设备上可用的网络。 此外， [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks)已扩展属性才能作为自变量的付款提供商的名称。 使用这些方法，应用程序可以自动支持付款提供商支持的任何网络。

有关详细信息，请参阅我们[Apple 支付配置](~/ios/platform/apple-pay.md)和 Apple 的[Apple 支付指南](https://developer.apple.com/apple-pay/)。

### <a name="new-testing-environment"></a>新的测试环境

IOS 10，与 Apple 引入了新的测试环境，允许开发人员设置直接在 iOS 设备上的测试支付卡。 然后，此新的测试环境将加密的测试付款数据返回给应用程序。

若要启用新的测试环境，请执行以下操作：

1. 在 iTunes Connect 中创建新的测试 iCloud 帐户。
2. 登录到新的测试帐户的 iOS 设备。
3. 设置所需的区域，若要测试的应用程序中。
4. 使用从测试付款卡之一[Apple 支付指南](https://developer.apple.com/apple-pay/)进行付款。

> [!IMPORTANT]
> 通过切换 iCloud 帐户，该设备将自动切换到新的测试环境。 但是，Apple 仍**需要**与实际测试的应用程序在生产环境中之前提交到 iTunes 应用商店卡。

## <a name="summary"></a>总结

在本文中，我们探讨了您的应用程序中，使用 Apple Pay 所需的不同项。 如何创建一个商家 ID，以及如何使用在我们查看**Entitlements.plist**，需要手动修改。

## <a name="related-links"></a>相关链接

- [应用内购买](~/ios/platform/in-app-purchasing/index.md)
- [PassKit 简介](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium （示例）](https://developer.xamarin.com/samples/monotouch/ios9/Emporium/)
