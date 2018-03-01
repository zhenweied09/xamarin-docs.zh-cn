---
title: "高级的概念和内部机制"
description: "后面 Xamarin.Android 和它的 API 设计的基础体系结构。"
ms.topic: article
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/13/2017
ms.openlocfilehash: d120398d4c59e51cee8da5e8ed2fbe0994ceca76
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="advanced-concepts-and-internals"></a>高级的概念和内部机制


##  <a name="architectureandroidinternalsarchitecturemd"></a>[体系结构](~/android/internals/architecture.md)

此文章介绍了 Xamarin.Android 应用程序背后的基础体系结构。 它说明如何 Xamarin.Android 应用程序环境中运行单声道执行一起与 Android 运行时虚拟机，并解释了此类作为 Android 可调用包装器和管理可调用包装器的关键概念。 



##  <a name="api-designandroidinternalsapi-designmd"></a>[API 设计](~/android/internals/api-design.md)

除了属于 Mono 的基类库的核心，Xamarin.Android 附带的各种 Android Api 允许开发人员使用 Mono 创建本机 Android 应用程序的绑定。

存在 Xamarin.Android 的核心是互操作的引擎与 Java 领域这样桥 C# 的环境并允许开发人员访问对 Java Api 通过 C# 或其他.NET 语言。



##  <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[程序集](~/cross-platform/internals/available-assemblies.md)

Xamarin.Android 附带几个程序集。 就像 Silverlight 是桌面的.NET 程序集扩展的子集，Xamarin.Android 还有几个 Silverlight 和桌面.NET 程序集的扩展的子集。 

