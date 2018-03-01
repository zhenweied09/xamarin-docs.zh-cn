---
title: ".NET 嵌入"
description: ".NET 嵌入允许你现有的.NET 代码 (C#、 F # 中，和其他人） 使用从其他编程语言"
ms.topic: article
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 29c34ede0b59620b8951109f8571a08a960182d1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="net-embedding"></a>.NET 嵌入

![预览](~/media/shared/preview.png)

.NET 嵌入允许你现有的.NET 代码 (C#、 F # 中，和其他） 可以从其他编程语言，且在各种不同的环境中使用。

这意味着，如果您具有想要使用它从现有的 iOS 应用程序的.NET 库，你可以执行该操作。   或者，如果你想要将其链接使用本机 c + + 库，你可以执行该操作。   或使用通过 Java 的.NET 代码。

## <a name="environments-and-languages"></a>环境和语言

该工具是同时注意它将使用的环境，以及将使用它的语言。   例如，iOS 平台不允许在实时 (JIT) 编译，以便让 embeddinator 以静态方式将你的.NET 代码编译到可用于 iOS 的本机代码。  其他环境允许 JIT 编译和在这些环境中，我们选择对 JIT 编译。

支持各种语言使用者，因此，它显示为目标语言的惯用代码的.NET 代码。   目前，这是支持的语言的列表：

- [**Objective C** ](objective-c/index.md) – 将.NET 映射到惯用 OBJECTIVE-C 的 Api。
- [**Java** ](android/index.md) – 将.NET 映射到惯用 Java Api。
- **C**： 将.NET 映射到的面向对象的类似 C Api。

多个语言将产生更高版本的原因。

## <a name="getting-started"></a>入门

若要开始，请检查我们的指南为每个当前支持的语言之一：

- [**Objective C** ](get-started/objective-c/index.md) – 介绍 macOS 和 iOS。
- [**Java** ](get-started/java/index.md) – 介绍 macOS 和 Android。
- [**C** ](get-started/c.md) – 介绍如何在桌面平台上的 C 语言。


## <a name="related-links"></a>相关链接

- [在 GitHub 上的 Embeddinator 4000](https://github.com/mono/Embeddinator-4000)
