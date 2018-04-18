---
title: .NET 嵌入限制
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: d8995946966020955a1d9378dea631387ed5f4bd
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="net-embedding-limitations"></a>.NET 嵌入限制

本文档介绍.NET 嵌入 (Embeddinator-4000) 的限制，并尽可能为它们提供解决方法。

## <a name="general"></a>常规

### <a name="use-more-than-one-embedded-library-in-a-project"></a>使用多个项目中的嵌入的库

不能有两个单声道的运行时对相同的应用程序中共存。 这意味着你无法使用两个不同 embeddinator 4000 生成库对相同的应用程序中。

**解决方法：**可用生成器来创建单个库包含多个程序集 （来自不同项目）。

### <a name="subclassing"></a>子类化

Embeddinator 方便地通过公开一组为目标语言和平台的准备就绪，可以使用 Api 的应用程序内的单声道运行时集成。

但这不是两种方式集成，例如不能子类托管类型，和期待托管的代码将回调在本机代码，内部，因为你的托管的代码不知道此共同存在。

具体取决于你的需求，就有可能的解决方法部分的此限制例如

* 你的托管的代码可以将 p/invoke 到你的本机代码。 这需要自定义为允许自定义从本机代码; 托管的代码

* 使用产品，如 Xamarin.iOS 和公开这样的托管的库，这会让 ObjC （在这种情况下） 子类化某些托管的 NSObject 子类。


## <a name="objc-generated-code"></a>ObjC 生成代码

### <a name="nullability"></a>可为 null

.NET 中没有任何元数据，，，告诉我们是否为 API 是可接受的空引用。 大多数 Api 将引发`ArgumentNullException`如果它们不能应对`null`自变量。 如 ObjC 处理的异常的方法是更好的避免内容，这会产生问题。

因为我们不能在标头文件中生成准确的为 null 性批注并想要最大程度减少托管的异常我们默认为非空自变量 (`NS_ASSUME_NONNULL_BEGIN`) 并添加某些特定的可能精度时，可为 null 批注。
