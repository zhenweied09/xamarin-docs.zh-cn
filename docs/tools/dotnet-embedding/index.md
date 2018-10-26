---
title: .NET 嵌入
description: '.NET 嵌入允许现有.NET 代码 (C#、 F # 和其他人） 可供其他编程语言编写的代码。'
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 23233ea8b06e0db580ba99edf2705e3dae5b931f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107118"
---
# <a name="net-embedding"></a>.NET 嵌入

![预览](~/media/shared/preview.png)

.NET 嵌入允许现有.NET 代码 (C#、 F # 和其他人） 可以通过其他编程语言，且在各种不同环境中使用。

这意味着，如果有想要使用它从现有的 iOS 应用程序的.NET 库，您可以这样做。   或者，如果你想要将其链接与本机 c + + 库，则也可以做到。   或使用 Java 从.NET 代码。

.NET 嵌入基于[Embeddinator 4000](https://github.com/mono/Embeddinator-4000)开放源代码项目。

## <a name="environments-and-languages"></a>环境和语言

该工具是能够意识到它将使用的环境，以及使用它的语言。   例如，iOS 平台不允许在实时 (JIT) 编译，因此.NET 嵌入将以静态方式编译您的.NET 代码到本机代码可以在 iOS 中使用。  其他环境并允许 JIT 编译，并在这些环境中，我们选择 JIT 编译。

它支持各种语言使用者，因此它显示为目标语言中惯用代码的.NET 代码。   这是目前支持的语言列表：

- [**Objective C** ](objective-c/index.md) – 映射到惯用的 Objective C Api 的.NET
- [**Java** ](android/index.md) – 映射到惯用的 Java Api 的.NET
- [**C** ](get-started/c.md) – 映射到 C Api 等面向对象的.NET

稍后将推出更多语言。

## <a name="getting-started"></a>入门

若要开始，请我们为每个当前支持的语言的指南之一：

- [**Objective C** ](get-started/objective-c/index.md) – 介绍了在 macOS 和 iOS
- [**Java** ](get-started/java/index.md) – 介绍了在 macOS 和 Android
- [**C** ](get-started/c.md) – 包含在桌面平台上的 C 语言

## <a name="related-links"></a>相关链接

- [GitHub 上的 Embeddinator 4000](https://github.com/mono/Embeddinator-4000)
