---
title: "浮点"
ms.topic: article
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 73681a37f15f3dd93c85bafb6bb9d71ab30af85c
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="floating-point"></a>浮点

Xamarin.iOS 默认情况下将执行 32 位和 64 位浮点运算使用 ARM 上的 64 位精度。  

更接近于的开发人员希望从浮点 C# 中的操作，在桌面上上移动，, 此较高的精度时可以显著的性能影响。

可编译 32 位浮点点代码以使用 32 位浮点运算。  若要执行此操作，你需要至少使用 Xamarin.iOS 8.10 并在你的 iOS 设置生成选项的面板"mtouch 多余的自变量"条目行的以下值：

     --aot-options=-O=float32

这会通知静态编译器 （Mono 的内置静态编译器或提供 LLVM 支持一个） 以执行浮点运算使用 32 位浮点数。
