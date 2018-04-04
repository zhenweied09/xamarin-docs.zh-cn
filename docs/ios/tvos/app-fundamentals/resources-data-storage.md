---
title: 资源和数据存储
description: 本文介绍如何使用资源和 Xamarin.tvOS 应用中的持久数据存储。
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: b4d96ef50498b454da583a955169b9d51c29dd01
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="resources-and-data-storage"></a>资源和数据存储

_本文介绍如何使用资源和 Xamarin.tvOS 应用中的持久数据存储。_

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>tvOS 资源限制

与 iOS 设备不同新 Apple TV 的 tvOS 应用或数据提供非常有限的持久的本地存储。 很小的项目 （例如用户首选项），您 tvOS 的应用程序仍有权访问`NSUserDefaults`与[限制为 500 KB 的数据](https://forums.developer.apple.com/message/50696#50696)。 但是，如果您 Xamarin.tvOS 的应用程序需要保留大量的信息，它必须存储和检索该数据免遭[iCloud](#iCloud-Data-Storage)。

此外，tvOS 限制为 200 MB 的 Apple TV 应用程序的大小。 如果你的应用程序需要超过此大小的资源，他们必须先将打包并使用加载[按需资源](#On-Demand-Resources)（最多额外的 2 GB)。 给定这些限制，则很关键你正确时间下载其他资产的应用程序的用户提供最佳体验。 有关详细信息，请参阅 Apple 的[按需资源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>非持久性下载

每个 tvOS 应用提供其他资源及其资产下载到一个临时缓存目录。 将保留此目录，只要该应用程序仍在运行。 但是，随着 Apple TV 需求以释放空间以进行其他应用程序或系统使用情况，则可以删除此缓存。

因此，你的应用程序不能依赖于处于可用下次启动以前下载的内容。 Xamarin.tvOS 应用程序应始终检查所需的资源存在，并根据需要下载它们。

> [!IMPORTANT]
> 虽然有下载其他资产和所需的资源的能力，Apple 警告针对处理所有应用程序的缓存中的空间，因为它将导致不可预知的结果。




<a name="Managing-Resources" />

## <a name="managing-resources"></a>管理资源

如前所述，由于有限，非持久性存储的信息可用于 tvOS 应用这些限制需要仔细规划，以创建应用程序 Xamarin.tvOS 出色的用户体验。

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>iCloud 数据存储

由于 Apple TV 上的存储是有限的不只是存在非常有限的持久的本地存储，你的应用程序没有保证的下次运行时，将用以前下载的任何信息。

因此，您 Xamarin.tvOS 的应用程序必须 iCloud 数据存储区中存储任何用户数据。 Apple 提供 tvOS 应用的两个基于 iCloud 的数据存储选项：

- **iCloud 键-值存储 (KVS)** -有关的信息 (小于 1 MB)，你的应用程序可能需要 （如用户首选项），你可以使用 iCloud KVS 存储小的部分。 iCloud KVS 数据自动同步到云的所有用户的设备运行的相同应用。 有关详细信息请参阅[键-值存储](~/ios/data-cloud/introduction-to-icloud.md)一部分我们[简介 iCloud](~/ios/data-cloud/introduction-to-icloud.md)文档或 Apple 的[为键 / 值数据中 iCloud 设计](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7)文档。
- **CloudKit** -对于存储的更大信息段 （大于 1 MB），使用 Apple 的 CloudKit 框架。 与 iCloud KVS 存储，可以在你的应用 （以及正在专用于单个用户） 的所有用户之间共享 CloudKit 数据。 窗体的详细信息，请参阅我们[简介 CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)文档或 Apple 的[CloudKit 快速启动](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)。

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>按需资源

按需资源提供应用内容和资产 （独立于应用程序捆绑），托管应用商店中并下载所需的应用程序。 最多额外的 2 GB 的数据可通过处理按需资源。 它们使初始应用程序下载较小 （tvOS 应用限于 200 MB 的最大值），同时仍提供根据需要的丰富资产。

当 tvOS 应用请求按需资源时，系统将自动管理的下载和存储到应用程序的缓存目录的此内容。 你的应用程序必须等待此内容才能下载并可才能继续。

这些资源可能会继续在多个启动你的应用，加快发布周期整个 Apple TV 上缓存。 但是，你的应用程序不能依赖于处于可用的下次启动任何以前下载内容。 请参阅[非持久下载](#Non-Persistent-Downloads)上面部分以了解更多详细信息。

Xcode 用于创建与给定资源标记相关联的相关内容 （如游戏级别 2 的所有资产） 的捆绑包。 稍后你的应用程序将通过指定此资源标记请求按需资源。 你的应用程序应提供 UI 向用户表明该内容下载。 有关详细信息，请参阅 Apple 的[按需资源指南](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)。

> [!IMPORTANT]
> 应格外小心，以达到最佳平衡的应用程序必须下载按需资源的次数和单独的下载的大小之间。 如果在玩游戏不断地中断，若要下载新内容或下载一次时间太多时间，用户可能会成为沮丧与你的应用。




<a name="Summary" />

## <a name="summary"></a>总结

本文已覆盖置于 Xamarin.tvOS 应用上的 tvOS 系统的大小、 资源和数据存储限制。 它提供了选项，若要解决这些限制和建议以创建你的应用程序提供出色的用户的体验。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
