---
title: 在 Xamarin – 内部 tvOS
description: 在 Xamarin，基于 Xamarin.iOS 上描述 tvOS 的内部工作原理的文档。 链接内容讨论了程序集，目标框架和相关 iOS 概念。
ms.prod: xamarin
ms.assetid: 8C076FED-9C03-44DE-9723-0E20272DD16B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 0132eac4edd4ecb9f693828bd58288dfbcb1c008
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789140"
---
# <a name="tvos-in-xamarin--internals"></a>在 Xamarin – 内部 tvOS 

##  <a name="assembliesiostvosinternalsassembliesmd"></a>[程序集](~/ios/tvos/internals/assemblies.md)

Xamarin 支持为 Xamarin.tvOS 应用程序的程序集的列表。

##  <a name="target-frameworksiostvosinternalsframeworksmd"></a>[目标框架](~/ios/tvos/internals/frameworks.md)

本文介绍可用于 Xamarin.tvOS 和选择 Xamarin.tvOS 应用程序的特定目标的含义的目标框架 （基类库） 的类型。

## <a name="related-ios-articles"></a>相关的 iOS 项目

以下文章是特定于 iOS 的但与 tvOS （因为 tvOS 9 为 iOS 9 的子集）。

###  <a name="unified-apicross-platformmaciosunifiedindexmd"></a>[Unified API](~/cross-platform/macios/unified/index.md)

引入了新统一的 Api 允许有关 Apple TV 和 iOS 之间共享的代码更简单的基本代码以及引入支持 64 位 Api 和 64 位编译。  

###  <a name="api-designiosinternalsapi-designindexmd"></a>[API 设计](~/ios/internals/api-design/index.md)

说明背后的 API 绑定的设计原则。

###  <a name="limitationsiosinternalslimitationsmd"></a>[限制](~/ios/internals/limitations.md)

本部分说明了缺陷和限制需要注意的使用方面 Xamarin.iOS，其中许多是适用于 Xamarin.tvOS。

###  <a name="linkeriosdeploy-testlinkermd"></a>[链接器](~/ios/deploy-test/linker.md)

说明如何链接器配合工作以确保最小的可能的应用程序包，以及如何修改其设置和使用情况。

###  <a name="localization-and-internationalizationiosapp-fundamentalslocalizationindexmd"></a>[本地化和国际化](~/ios/app-fundamentals/localization/index.md)

此指南介绍了如何添加到 Xamarin.iOS 应用程序以支持国际化的编码。

###  <a name="mtouchiosdeploy-testmtouchmd"></a>[mtouch](~/ios/deploy-test/mtouch.md)

有关 mtouch.exe 的注意事项和信息，该命令行工具用于将项目内置到 iOS 可使用的应用程序中。

###  <a name="linking-native-librariesiosplatformnative-interopmd"></a>[链接的本机库](~/ios/platform/native-interop.md)

Xamarin.iOS 支持本机 C 库和 Objective-C 库链接。 本文档讨论了如何将链接与你的 Xamarin.iOS 项目你本机 C 库。 有关执行相同的 Objective-C 库的信息，请参阅&nbsp;[绑定 Objective-C 类型](~/ios/platform/binding-objective-c/index.md)&nbsp;文档。

##  <a name="objective-c-selectorsiosinternalsobjective-c-selectorsmd"></a>[Objective-C 的选择器](~/ios/internals/objective-c-selectors.md)

说明并且直接调用 Objective-C 的选择器 （方法） 的使用情况。

###  <a name="systemdataiosdata-cloudsystemdatamd"></a>[System.Data](~/ios/data-cloud/system.data.md)

信息和说明使用 System.Data 访问内置的 SQLite 数据库系统。

###  <a name="threadingiosapp-fundamentalsthreadingmd"></a>[线程处理](~/ios/app-fundamentals/threading.md)

使用线程 Xamarin.iOS 应用程序中的说明。

###  <a name="xib-code-generationiosinternalsxib-code-generationmd"></a>[XIB 代码生成](~/ios/internals/xib-code-generation.md)

Visual Studio for Mac如何与Xcode的Interface Builder集成以允许您使用Interface Builder来设计UI。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
