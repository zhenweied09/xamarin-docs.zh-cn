---
title: "绑定 Objective C"
ms.topic: article
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: e836081d79d904e2d0952386e536eefdabe361e1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="binding-objective-c"></a>绑定 Objective C

本部分包括各种涵盖创建绑定到 Objective C 库，因此它们可以从 C# 应用程序使用 Xamarin.iOS 或 Xamarin.Mac 创建调用的文档。

##  <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[概述](~/cross-platform/macios/binding/overview.md)

本文档包含的内部绑定发生一些。 这是某些技术信息高级的文档。

##  <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[绑定 Objective C 库](~/cross-platform/macios/binding/objective-c-libraries.md)

本文档介绍用于创建 C# 的 OBJECTIVE-C 的 Api 和 Objective C 中的习语如何映射到在.NET 中使用习语绑定的过程。
如果你正在绑定仅 C Api，你应为此，P/Invoke framework 使用的标准.NET 机制。

##  <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[绑定定义参考指南](~/cross-platform/macios/binding/binding-types-reference.md)

这是描述绑定作者驱动器绑定生成过程到可用的属性的所有参考指南。


## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[目标 Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

目标 Sharpie 是有助于引导绑定的第一个传递的命令行工具。 它分析本机库映射到的公共 API 的标头文件的工作原理[绑定定义](~/cross-platform/macios/binding/objective-c-libraries.md)（也可以手动执行该过程）。

## <a name="ios"></a>iOS

[IOS 绑定页](~/ios/platform/binding-objective-c/index.md)链接回这些常见的绑定资源，此外到下面的示例。

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[演练： 将绑定 Objective C 库](~/ios/platform/binding-objective-c/walkthrough.md)

本文提供了创建使用开放源代码绑定项目的分步演练[InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective C 项目作为示例。 InfColorPicker 库提供了允许用户选择一种颜色基于其 HSB 表示形式，使颜色选择更加友好的用户的可重用的视图控制器。 目标 Sharpie 将用于协助绑定过程中。

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[绑定示例](https://github.com/mono/monotouch-bindings)

创建新绑定项目时，可以是第三方绑定的集合将使用引用。

## <a name="mac"></a>Mac

从历史上看[Mac 绑定](~/mac/platform/binding.md)已非常手动过程。 目前尚[可下载预览](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview)的将来版本的 Visual Studio for mac。 Mac 绑定项目支持



## <a name="related-links"></a>相关链接

- [iOS 绑定](~/ios/platform/binding-objective-c/index.md)
- [Mac 绑定](~/mac/platform/binding.md)
