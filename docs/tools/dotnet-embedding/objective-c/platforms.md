---
title: OBJECTIVE-C 的平台
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: a783dc554f54922f4fa4f99a43d83c4c864ef681
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="objective-c-platforms"></a>OBJECTIVE-C 的平台


生成 Objective C 代码时，.NET 嵌入可以面向各种平台：

* macOS
* iOS
* tvOS
* watchOS [尚未实现]

通过选择平台`--platform=<platform>`embeddinator 命令行自变量。

为 iOS、 tvOS 和 watchOS 平台生成时，embeddinator 将始终创建嵌入 Xamarin.iOS，因为 Xamarin.iOS 包含大量的运行时支持代码，则需要在这些平台上的框架。

但是，为 macOS 平台生成时，则可以选择是否生成的 framework 是否应嵌入 Xamarin.Mac。 可以不嵌入 Xamarin.Mac，如果绑定的程序集未引用 Xamarin.Mac.dll （直接或间接），并且此选项将被传递`--platform=macOS`到 embeddinator。

如果绑定的程序集包含对 Xamarin.Mac.dll 的引用，它必须嵌入 Xamarin.Mac，，此外 embeddinator 必须知道要使用的目标框架。

有三个可能的 Xamarin.Mac 目标框架： `modern` (以前称为`mobile`)，`full`和`system`(彼此之间的差异述 Xamarin.Mac 的[目标框架][ 1]文档)，并通过选择的每`--platform=macOS-modern`，`--platform=macOS-full`或`--platform=macOS-system`到 embeddinator。

[1]: ~/mac/platform/target-framework.md
