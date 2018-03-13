---
title: "应用分发概述"
description: "本文档概述了可用于 Xamarin.tvOS 应用程序的分发技术，并充当指向主题的更详细文档。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 3e96e98f90c7f4c849a9f679b2de819ccaabfec0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="app-distribution-overview"></a>应用分发概述

_本文档概述了可用于 Xamarin.tvOS 应用程序的分发技术，并充当指向主题的更详细文档。_


一旦您 Xamarin.tvOS 的应用程序已开发完毕，软件开发生命周期的下一步是分发给用户，应用程序在下图突出显示部分所示：


[![软件开发生命周期概述](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)


Apple 提供了以下的方式来分发 tvOS 应用，这受 Xamarin.tvOS:

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**In-House（企业）**](#In-House-Distribution) 
2. [**Ad Hoc**](#Ad_Hoc_Distribution) 

所有这些情况都要求使用相应的配置文件预配应用程序。 配置文件是一种文件，其中包含了代码签名信息，以及应用程序标识和预期分发机制。 对于非 App Store 分发，该文件还包含可部署应用的设备的相关信息。

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Apple 电视应用商店分发

这是 tvOS 应用将分配给 Apple TV 设备上的使用者的主要方式。 提交到 Apple 电视应用商店的所有应用程序需要通过 Apple 的批准。

应用通过名为 iTunes Connect 的门户提交到 App Store。 请参阅我们[在 iTunes Connect 中配置你的应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南以获取有关以下主题的信息：

- 管理协议、 税收和银行。
- 创建 iTunes Connect 记录。
- 管理应用视频和屏幕快照。
- 管理应用程序名称、 描述、 最新内容、 关键字和 Url。
- 维护常规应用程序信息。
- 维护 Game Center 信息。
- 维护应用程序查看信息。
- 维护定价信息。
- 维护应用内购买信息。
- 查看应用程序评审。

而不是专门为 tvOS 编写，本文档将提供有关如何设置和使用此门户来准备用于 Apple 电视应用商店中发布的应用程序的信息。 许多相同的步骤所需是否设置了 iOS 或 tvOS 的应用程序。

完成所有上面列出的步骤操作后，请参阅我们[在 iTunes Connect 中配置你 tvOS 应用](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md)添加将需要的 tvOS 应用程序特定信息。

务必注意，只有属于 **Apple 开发人员计划**的开发人员有权访问 iTunes Connect。 **Apple 开发人员企业计划**成员没有访问权限。

如果你遇到的问题提交到 Apple 电视 App Store 你 Xamarin.tvOS 的应用，请参阅我们[故障排除](~/ios/tvos/troubleshooting.md)指南。 它包含可能会遇到的一些已知的问题和如何解决这些 Xamarin.tvOS 中。

有关详细信息，请访问[到 Apple 电视应用商店发布](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md)指南。

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>In-House 分发

In-House 分发（有时称为企业分发）允许 **Apple 开发人员企业计划**成员在内部向同一组织内的其他成员分发应用。 In-House 分发的优势在于无需 App Store 审核，且没有可安装应用程序的设备数量限制。 但是，值得注意的是，**Apple 开发人员企业计划**成员**没有**权限访问 iTunes Connect，因此许可证持有人负责分发应用。

有关获取设置以及如何内部发布应用程序的详细信息，请参阅[内部分发指南](~/ios/deploy-test/app-distribution/in-house-distribution.md)。 本文档是特定于 iOS 的但同样的技术用于 tvOS 应用。

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>Ad Hoc 分发

可以通过即席分发，这是上都可用的用户测试 Xamarin.tvOS 应用**Apple 开发人员计划**，和**Apple Developer Enterprise Program**，并允许最多 100 个 Apple TV 设备要测试。 即席分发的最佳用例时 iTunes Connect 不是选项，将你公司内的分发。

有关获取设置以及如何内部分发应用程序的详细信息，请参阅[即席分发指南](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)。 同样，本文档是特定于 iOS 的但同样的技术用于 tvOS 应用。

<a name="Summary" />

## <a name="summary"></a>摘要

本文提供可用于 Xamarin.tvOS 应用分发机制的简要概述。 它引入 Apple 电视应用商店，即席和内部部署，并提供指向更详细的信息。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
