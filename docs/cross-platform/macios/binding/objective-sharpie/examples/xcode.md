---
title: 使用 Xcode 项目的实际示例
description: 本文档介绍如何使用 Xcode 项目作为直接输入到目标 Sharpie，简化创建 C# 绑定到 Objective C 代码的过程。
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 850c351f91c9a09a6654c876167e035f751e9504
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781112"
---
# <a name="real-world-example-using-an-xcode-project"></a>使用 Xcode 项目的实际示例


**此示例使用[POP 库从 Facebook](https://github.com/facebook/pop)。**

新 3.0 版开始，在目标 Sharpie 作为输入支持 Xcode 项目。 这些项目指定的正确的标头文件和需要编译本机库，并因此需要将它太绑定的编译器标志。 目标 Sharpie 将选择第一个_目标_和项目中，如果不指示以其他处理其默认配置。

目标 Sharpie 试图分析的项目和标头文件之前，它必须对它进行生成。 项目通常具有将正确结构外部使用和集成，标头文件，因此最好始终尝试将其绑定前生成完整的项目的生成阶段。

<pre>$ <b>git clone https://github.com/facebook/pop.git</b>
Cloning into 'pop'...
   <em>(more git clone output)</em>

$ <b>cd pop</b>
$ <b>sharpie bind pop.xcodeproj -sdk iphoneos9.0</b></pre>

