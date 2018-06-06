---
title: Apple Pay 中 Xamarin watchOS 上
description: 本文介绍如何增强功能在 watchOS 3 以及如何实现它们在 Xamarin.iOS 转换为 Apple Watch 对 Apple Pay 所做 Apple。
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 75d660ad0699b6fac3b1ae43046f322f380872b3
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791070"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay 中 Xamarin watchOS 上

Apple 具有对 Apple Pay 进行多项增强功能，增加了对应用内付款支持的 watchOS 3。 这允许用户安全地提供付款和联系信息直接从 Apple Watch 对物理产品和服务付费。


## <a name="about-apple-pay-enhancements"></a>有关 Apple 支付增强功能

作为上述 Stated，Apple 具有对 Apple Pay 进行多项增强功能，watchOS 3 允许安全付款和联系信息直接从 Apple Watch 付费物理产品和服务中。 通过修改 PassKit framework 提供了这些增强功能。

使用 iOS 10 和 watchOS 3，采用了 iOS 和 watchOS 以支持动态付款网络和一个新的沙盒测试环境已添加了几个新的 Api。

## <a name="passkit-framework-enhancements"></a>PassKit Framework 增强功能

在 iOS 10 中，已扩展 PassKit framework 以支持 Apple Pay 之外`UIKit`和允许卡颁发者提供在其应用程序中的从其卡。 

### <a name="supporting-apple-pay-outside-of-uikit"></a>支持外部 UIKit Apple 付费

通过使用[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)和[PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)，应用程序可以支持提供的相同功能[PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller)而无需使用 UIKit。 需要支持 Apple Pay Apple Watch 上 （和以及特定意向） 这个新的 API 时，它是在其他情况下 （例如现有的应用） 可选的。 但是，Apple 提供的建议将移动到新的 API 越早越好广泛 Apple Pay 的支持，在所有开发人员的应用提供的单个基本代码。 有关意向的详细信息和使用 Siri 集成，请参阅我们[简介 SiriKit](~/ios/platform/sirikit/index.md)文档。

### <a name="presenting-issuer-cards-from-within-apps"></a>演示从应用中的颁发者卡

使用 iOS 10 和 watchOS 3，新的功能已添加到允许卡颁发者提供在其自己的应用程序中从其支付卡 PassKit framework。 开发人员可以添加`PKPaymentButtonTypeInStore`UIButton 到应用程序的用户界面将显示为卡 Apple Pay 按钮。

`PresentPaymentPass`方法[PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary)类还可以用于以编程方式显示卡。

## <a name="new-payment-network-support"></a>新的付款网络支持

新到 iOS 10 和 watchOS 3，应用程序可以自动支持新的付款网络而无需开发人员无需修改、 重新编译应用程序和将其重新提交到应用商店变为可用时。

新[AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks)方法`PKPaymentNetwork`类允许应用程序以发现在运行时的用户的设备上可用的网络。 此外， [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks)已扩展属性才能作为自变量的付款提供商的名称。 使用这些方法，应用程序可以自动支持付款提供商支持的任何网络。

有关详细信息，请参阅我们[Apple 支付配置](~/ios/platform/apple-pay.md)和 Apple 的[Apple 支付指南](https://developer.apple.com/apple-pay/)。

## <a name="new-testing-environment"></a>新的测试环境

使用 iOS 10 和 watchOS 3，Apple 引入了新的测试环境，允许开发人员设置直接在 iOS 设备上的测试支付卡。 然后，此新的测试环境将加密的测试付款数据返回给应用程序。

若要启用新的测试环境，请执行以下操作：

1. 在 iTunes Connect 中创建新的测试 iCloud 帐户。
2. 登录到新的测试帐户的 iOS 设备。
3. 设置所需的区域，若要测试的应用程序中。
4. 使用从测试付款卡之一[Apple 支付指南](https://developer.apple.com/apple-pay/)进行付款。

> [!NOTE]
> 通过切换 iCloud 帐户，该设备将自动切换到新的测试环境。 但是，Apple 仍**需要**与实际测试的应用程序在生产环境中之前提交到 iTunes 应用商店卡。

## <a name="summary"></a>总结

本文已覆盖增强功能 Apple 已到 Apple Pay watchOS 3 以及如何在 Xamarin.iOS 实现它们。
