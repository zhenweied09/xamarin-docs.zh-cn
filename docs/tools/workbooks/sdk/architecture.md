---
title: 体系结构概述
description: 本文档介绍了 Xamarin 的工作簿，检查交互式代理和交互的客户端如何协同工作的体系结构。
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 3cd0d3e8cc47dd7fa43dfa0b910c9f09740fcc9a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794002"
---
# <a name="architecture-overview"></a>体系结构概述

Xamarin 的工作簿功能必须相互一起工作的两个主要组件：_代理_和_客户端_。

## <a name="interactive-agent"></a>交互式代理

代理组件是一个小的.NET 应用程序的上下文中运行的特定于平台的汇编。

Xamarin 的工作簿提供对大量的平台，例如，iOS、 Android、 Mac 和 WPF 的预建"空"应用程序。 这些应用程序显式托管代理。

在实时检查 （Xamarin 检查器），期间通过 IDE 调试器到现有应用程序的常规开发和调试工作流一部分注入代理。

## <a name="interactive-client"></a>交互式客户端

客户端是承载 web 浏览器图面提供工作簿/REPL 接口的本机 shell （Cocoa Mac 上的，在 Windows 上的 WPF）。 从 SDK 的角度看，所有客户端集成在中实现 JavaScript 和 CSS。

客户端 （通过 Roslyn) 负责将源代码编译成小的程序集和通过发送给连接的代理进行执行。 执行结果发送回客户端呈现。 工作簿中的每个单元格生成一个程序集，即在引用上一个单元格的程序集。

因为代理可以在任何类型的.NET 平台上运行，并且具有访问任何内容中运行的应用程序，必须格外小心以平台无关的方式序列化结果。
