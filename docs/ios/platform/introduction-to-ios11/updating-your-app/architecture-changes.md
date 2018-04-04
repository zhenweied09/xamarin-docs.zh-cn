---
title: 体系结构更改
description: 浏览 iOS 11 的新功能
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 6dd874fd803d2b3de21b0b604cbc2bafa5bf1aec
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="architecture-changes"></a>体系结构更改

_浏览 iOS 11 的新功能_

你应注意与 iOS 11 的最大更改之一是弃用对于应用程序，如所述的 32 位支持[Apple 的](https://developer.apple.com/news/?id=06282017b)按版本。 所有新的应用和更新到现有应用程序必须支持 64 位。 32 位应用**将不启动**iOS 11 中存在。

若要更新你的应用在 Visual Studio 中的 Mac，请使用以下步骤：

1. 右键单击项目名称以打开**项目选项**。
2. 浏览到**iOS 生成**选项卡。
3. 设置**支持的体系结构**下拉到**x86_64**为**调试 | iPhoneSimulator**和**版本 | iPhoneSimulator**:

    ![更改模拟器体系结构下拉列表](architecture-changes-images/image1.png)

4. 对于 iOS 设备，将配置更改为**调试 | iPhone**并设置**支持的体系结构**下拉到**ARM64**:

    ![更改设备体系结构下拉列表](architecture-changes-images/image2.png)

5. 将配置更改为**版本 | iPhone**并设置**支持的体系结构**下拉到**ARM64**。

在 32 位和 64 位体系结构的详细信息，请参阅[32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md#ios)指南。

## <a name="related-links"></a>相关链接

- [什么是新建 iOS 11 (Apple)](https://developer.apple.com/ios/)
- [更新的应用商店产品页 (Apple)](https://developer.apple.com/app-store/product-page/)
- [更新你的应用程序适用于 iOS 11 (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/204/)
