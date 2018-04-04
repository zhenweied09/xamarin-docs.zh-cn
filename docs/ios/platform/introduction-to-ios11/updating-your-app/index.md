---
title: 更新应用到 iOS 11
description: 浏览 iOS 11 的新功能
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 2581f729d85787021763f50f005e84d6bbb5db01
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="updating-your-app-to-ios-11"></a>更新应用到 iOS 11

_浏览 iOS 11 的新功能_

在 iOS 11 中，体系结构更新、 新的可视更改和更新的 iTunes Connect 过程引入了 Apple。 本指南探讨了上述每项更改，从而帮助你获取更新适用于 iOS 11 你 Xamarin.iOS 应用程序。

## <a name="architecture-changesarchitecture-changesmd"></a>[体系结构更改](architecture-changes.md)

你应注意与 iOS 11 的最大更改之一是弃用对于应用程序，如所述的 32 位支持[Apple 的](https://developer.apple.com/news/?id=06282017b)按版本。

本指南将指导您完成更新适用于 64 位应用程序。

## <a name="visual-design-updatesvisual-designmd"></a>[可视化设计更新](visual-design.md)

在 iOS 11 中，Apple 引入了新的可视更改，包括更新的导航栏、 搜索栏和表视图。 除了已得到改进，允许通过边距和全屏内容进行更大的灵活性。 本指南中介绍了这些更改。

## <a name="app-store-changesapp-store-changesmd"></a>[App Store 更改](app-store-changes.md)

IOS 应用商店已完成的重新设计，这不仅使用户能够高效地导航应用商店中，而且还允许你，作为开发人员，以提升用户你的应用。 这些提升包括应用内购买的更新和更新产品页。 iOS 11 还将添加有关如何与用户进行通信、 如何添加你的应用程序图标，以及如何发布你的应用为公共的更新。

## <a name="app-icon-updates"></a>更新应用程序图标

> [!NOTE]
> 应用程序图标现在应由_资产目录_。 

有关使用资产目录信息，请参阅[应用存储图标](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。 有关迁移的帮助从 Info.plist 图标添加到资产目录中，请参阅[Info.plist 从迁移到资产目录](~/ios/app-fundamentals/images-icons/app-icons.md)指南。

名为资产目录中的所需的图标**应用商店**，应为 1024 x 1024 的大小。 Apple 已声明，资产目录中的应用存储图标不能是透明的，也不能包含 alpha 通道。

![应用程序将图标位置存储在资产目录。](images/image1.png)

## <a name="related-links"></a>相关链接

- [什么是新建 iOS 11 (Apple)](https://developer.apple.com/ios/)
- [更新的应用商店产品页 (Apple)](https://developer.apple.com/app-store/product-page/)
- [更新你的应用程序适用于 iOS 11 (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/204/)
