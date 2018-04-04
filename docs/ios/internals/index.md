---
title: 高级概念和内部的 iOS
description: 本主题探讨 MonoTouch API 设计、 程序集和类从.NET 基类库 (BCL)，以及适用于 Mac 的 Visual Studio 与 Xcode 的接口生成器和 Apple 的工具链的集成方式。
ms.prod: xamarin
ms.assetid: 951713CD-D6AD-981C-A09E-4F2C98588D8B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 6befe8dcbfda74e19d7967e4804c530fe866cebe
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="ios-advanced-concepts-and-internals"></a>高级概念和内部的 iOS

_本主题探讨 MonoTouch API 设计、 程序集和类从.NET 基类库 (BCL)，以及适用于 Mac 的 Visual Studio 与 Xcode 的接口生成器和 Apple 的工具链的集成方式。_




##  <a name="api-designiosinternalsapi-designindexmd"></a>[API 设计](~/ios/internals/api-design/index.md)

说明背后的 API 绑定的设计原则。




##  <a name="available-assembliescross-platforminternalsavailable-assembliesmd"></a>[可用程序集](~/cross-platform/internals/available-assemblies.md)

列出可用的程序集和类从.NET 基类库 (BCL)。




##  <a name="xib-code-generationiosinternalsxib-code-generationmd"></a>[XIB 代码生成](~/ios/internals/xib-code-generation.md)

它还介绍如何针对 Mac 和 Xcode 的接口生成器的 Visual Studio 允许你设计 UI 使用接口生成器。

> [!IMPORTANT]
> 本文档介绍 Visual Studio 使用 Xcode 的接口生成器的 Mac 的集成。 有关 iOS 设计器的详细信息，请查看[iOS 设计器](~/ios/user-interface/designer/index.md)文档。



##  <a name="ios-architectureiosinternalsarchitecturemd"></a>[iOS 体系结构](~/ios/internals/architecture.md)

Xamarin.iOS 应用程序运行在单声道执行环境中，并使用完整继续操作的时间 (AOT) 编译编译到 ARM 程序集语言的 C# 代码。 本指南介绍了较低级别的 Xamarin.iOS

##  <a name="objective-c-selectorsiosinternalsobjective-c-selectorsmd"></a>[Objective-C 的选择器](~/ios/internals/objective-c-selectors.md)

说明并且直接调用 Objective-C 的选择器 （方法） 的使用情况。


##  <a name="limitationslimitationsmd"></a>[限制](limitations.md)

缺陷和限制需要注意的使用 Xamarin.iOS。