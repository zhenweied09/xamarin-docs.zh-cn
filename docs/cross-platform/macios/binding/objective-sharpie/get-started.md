---
title: "入门"
ms.topic: article
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 01c390af08e59f3b10888a183df7fa6758c2609c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="getting-started"></a>入门

<style type="text/css"> .terminal 蓝色 {颜色： rgb(10,96,254);}.terminal 绿色 {颜色： rgb(12,156,26);}.terminal 洋红色 {颜色： rgb(152,12,103);} </style>


> [!IMPORTANT]
> **警告：**目标 Sharpie 是经验丰富的 Xamarin 开发人员提供高级知识 Objective C （以及通过扩展，C） 的工具。 在尝试将绑定 Objective C 库之前应具有纯色了解如何在命令行 （和熟悉的本机库的工作原理） 上生成本机库。

<a name="installing" />

# <a name="installing-objective-sharpie"></a>安装目标 Sharpie

目标 Sharpie 目前一个独立的命令行工具为 Mac OS X 10.10 和更高版本，，_不是完全受支持的 Xamarin 产品_。 它只应由高级开发人员可以帮助创建绑定项目中的第三方 OBJECTIVE-C 的库。

目标 Sharpie 可以作为标准的 OS X 程序包安装程序下载。
运行安装程序并按照屏幕上从安装向导的提示：

- **当前版本： 3.4**
  - [下载最新版本](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
  - [论坛公告](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> 💡 **提示：**使用`sharpie update`命令以更新为最新版本。

# <a name="basic-walkthrough"></a>基本演练

提供可帮助您进行创建的 Xamarin 通过定义要求将第三方 Objective C 库绑定到 C#，则目标 Sharpie 是一个命令行工具。
即使是在使用目标 Sharpie，开发人员*将*需要目标 Sharpie 完成以解决未无法通过该工具会自动处理任何问题后修改生成的文件。

如果可能，目标 Sharpie 将批注它可以用来包含有关如何将正确绑定某些不确定的 Api （在本机代码中的许多构造是不明确的）。
这些批注将显示为[`[Verify]`属性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)。

目标 Sharpie 的输出是一个对的文件- [ `ApiDefinition.cs`和`StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) -可用来创建绑定项目 Xamarin 应用程序可以使用哪些编译到库。

> [!IMPORTANT]
> 目标 Sharpie 附带有一个**主要**规则的正确用法： 你必须绝对通过它正确 clang 编译器命令行参数才能确保能够正确分析。 这是因为分析阶段目标 Sharpie 是只是一种工具[针对 clang libtooling API 实现](http://clang.llvm.org/docs/LibTooling.html)。

这意味着目标 Sharpie 具有 Clang （实际编译本机库将绑定的 C/目标的 C/c + + 编译器） 以及所有绑定的标头文件及其内部知识的完整功能。
而不是将转换的已分析[AST](http://en.wikipedia.org/wiki/Abstract_syntax_tree)到对象代码，目标 Sharpie 将转换到 C# 绑定"基架"适合输入到 AST`bmac`和`btouch`Xamarin 绑定工具。

如果中则意味着该 clang 误码出期间目标 Sharpie 错误分析期间，其分析阶段尝试构造 AST，并且你需要找出原因。

**NEW!** 版本 3.0 尝试解决某些这种复杂性通过直接支持 Xcode 项目。 如果本机库具有有效的 Xcode 项目，则目标 Sharpie 可以评估指定的目标和配置，以推导的必需输入的标头文件和编译器标志的项目。

没有 Xcode 项目是否可用，你将需要更熟悉项目通过推断出正确的输入标头文件、 标头文件搜索路径和其他必要的编译器标志。 请务必请注意，用于生成本机库的编译器标志必须传递给目标 Sharpie 相同。 这是一个手动程度更高的过程，以及需要知道如何编译 Clang 工具链在命令行上的本机代码执行少量的一个。

**NEW!** 版本 3.0 还引入了一个用于轻松地绑定工具[CocoaPods](https://cocoapods.org)通过`sharpie pod`命令。
如果你感兴趣的库是可用作 CocoaPod，我们建议通过尝试将与目标 Sharpie CocoaPod 绑定 （而不是尝试直接绑定对源） 开始。