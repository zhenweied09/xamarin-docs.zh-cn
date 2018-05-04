---
title: .NET 嵌入限制
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: a99d4f06b68e645ab1d0fc1423facb827b31959d
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="net-embedding-limitations"></a>.NET 嵌入限制

本文档介绍.NET 嵌入的限制，并尽可能为它们提供解决方法。

## <a name="general"></a>常规

### <a name="use-more-than-one-embedded-library-in-a-project"></a>使用多个项目中的嵌入的库

不能有两个单声道的运行时对相同的应用程序中共存。 这意味着你无法使用两个不同.NET 嵌入生成库对相同的应用程序中。

**解决方法：**可用生成器来创建单个库包含多个程序集 （来自不同项目）。

### <a name="subclassing"></a>子类化

.NET 嵌入简化应用程序内的单声道运行时集成了通过公开一组为目标语言和平台的准备就绪，可以使用 Api。

但这不是双向集成，例如不能子类托管类型，和期待托管的代码将回调在本机代码，内部，因为你的托管的代码不知道此共同存在。

具体取决于你的需求，就有可能的解决方法部分的此限制例如

* 你的托管的代码可以将 p/invoke 到你的本机代码。 这需要自定义为允许自定义从本机代码; 托管的代码

* 使用产品，如 Xamarin.iOS 和公开这样的托管的库，这会让 Objective C （在这种情况下） 子类化某些托管 NSObject 子类。

## <a name="objective-c-generated-code"></a>Objective C 生成的代码

### <a name="nullability"></a>可为 null

没有任何元数据，以告诉我们是否可以接受空引用的.NET 或为 API。 大多数 Api 将引发`ArgumentNullException`如果它们不能应对`null`自变量。 因为 OBJECTIVE-C 的处理的异常是更好的避免内容，这会产生问题。

因为我们不能在标头文件中生成准确的为 null 性批注并想要最大程度减少托管的异常我们默认为非空自变量 (`NS_ASSUME_NONNULL_BEGIN`) 并添加某些特定的可能精度时，可为 null 批注。

### <a name="bitcode-ios"></a>Bitcode (iOS)

当前.NET 嵌入不支持 bitcode 在 iOS 中，它启用一些 Xcode 项目模板。 这将需要禁用到已成功生成的链接框架。

![Bitcode 选项](images/ios-bitcode-option.png)
