---
title: 使用目标 Sharpie 创建绑定
description: 本部分提供目标 Sharpie，Xamarin 的命令行工具用于自动执行创建绑定到 Objective C 库的过程的简介
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: ae644038aa8b54f0d57b61767882dec8754040c8
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780752"
---
# <a name="creating-bindings-with-objective-sharpie"></a>使用目标 Sharpie 创建绑定

_本部分提供目标 Sharpie，Xamarin 的命令行工具用于自动执行创建绑定到 Objective C 库的过程的简介_

- [概述](#overview) & [历史记录](#history)
- [入门](get-started.md)
- [工具和命令](tools.md)
- [功能](platform/index.md)
- [示例](examples/index.md)
- [完成演练](~/ios/platform/binding-objective-c/walkthrough.md)
- [发布历史记录](releases.md)

## <a name="overview"></a>概述

目标 Sharpie 是有助于引导绑定的第一个传递的命令行工具。
它分析本机库映射到的公共 API 的标头文件的工作原理[绑定定义](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file)（以前已手动执行此操作的过程）。

目标 Sharpie 使用 Clang 分析标头文件，因此绑定是确切和尽可能全面。 这可以极大地降低的时间和生产质量绑定所用的工作量。

> [!IMPORTANT]
> 目标 Sharpie 是一个用于与 Objective C （以及通过扩展，C） 的高级知识的经验 Xamarin 开发人员工具。 在尝试将绑定 Objective C 库之前应具有纯色了解如何在命令行 （和熟悉的本机库的工作原理） 上生成本机库。

## <a name="history"></a>历史记录

我们已发展，目标 Sharpie 内部在使用 Xamarin 最后一个三年。 目标 Sharpie 开机证明，Api Xamarin.iOS 和 Xamarin.Mac 中引入自 iOS 8，Mac OS X 10.10，并完全使用目标 Sharpie 引导已 watchOS 2.0 启动它。 Xamarin 非常依赖于目标 Sharpie 内部用于生成其自己的产品。

但是，目标 Sharpie 是一个非常高级的工具，需要了解的 Objective C 和 C、 如何在命令行上使用 clang 编译器和通常如何本机库将进行组合的高级的知识。 由于此高栏中，我们认为具有 GUI 向导设置的错误的期望，并且在这种情况下，目标 Sharpie 目前仅提供作为命令行工具。

## <a name="related-links"></a>相关链接

- [目标 Sharpie 下载](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
- [演练： 将绑定 Objective C 库](~/ios/platform/binding-objective-c/walkthrough.md)
- [绑定 Objective-C 库](~/cross-platform/macios/binding/objective-c-libraries.md)
- [绑定详细信息](~/cross-platform/macios/binding/overview.md)
- [绑定类型参考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [面向 Objective-C 开发人员的 Xamarin](~/ios/get-started/objective-c-developers/index.md)
