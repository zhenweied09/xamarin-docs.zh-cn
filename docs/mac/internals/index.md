---
title: "揭秘"
description: "一篇 Xamarin.Mac 的内部工作情况"
ms.topic: article
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: 2ba3ffb421dc64bba7df1e10a40125f14365f29e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="under-the-hood"></a>揭秘

_一篇 Xamarin.Mac 的内部工作情况_

## <a name="ahead-of-time-compilation-aotaotmd"></a>[继续操作的实时编译 (AOT)](aot.md)

继续操作 (AOT) 编译是时间的一种用于提高启动性能的功能强大的优化技术。 但是，它还会影响你的生成时间、 应用程序大小和程序执行深远方式，因此值得了解其工作原理。

## <a name="mac-architecturearchitecturemd"></a>[Mac 体系结构](architecture.md)

Objective C，包括概念，如编译、 选择器、 注册机构、 应用程序启动和生成器 Xamarin.Mac 的关系。

## <a name="xamarinmac-registrarregistrarmd"></a>[Xamarin.Mac 注册机构](registrar.md)

Xamarin.Mac 之间充当桥梁托管的领域中和 Cocoa 的运行时，允许托管的类来调用非托管的 OBJECTIVE-C 类并返回发生事件时调用。 若要执行此"幻"所需的工作由注册机构，但有时很有用了解发生了什么情况"在后台"。
