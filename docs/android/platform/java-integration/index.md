---
title: Java 集成概述
description: Java 生态系统包括多样化和巨大的组件集合。 许多这些组件可用来减少开发 Android 应用程序所花费的时间。 本文档将介绍并提供了几种开发人员可以使用这些现有的 Java 组件来提高其 Xamarin.Android 应用程序开发体验的高级概述。
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/18/2017
ms.openlocfilehash: dbaf17479ae077fced425df5ac31bdbbc4e06b64
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766941"
---
# <a name="java-integration-overview"></a>Java 集成概述

_Java 生态系统包括多样化和巨大的组件集合。许多这些组件可用来减少开发 Android 应用程序所花费的时间。本文档将介绍并提供了几种开发人员可以使用这些现有的 Java 组件来提高其 Xamarin.Android 应用程序开发体验的高级概述。_


## <a name="overview"></a>概述

给定的 Java 生态系统的范围，则很很有可能在 Java 中已编码 Xamarin.Android 应用程序所需的任何给定的功能。 因此，很有吸引力尝试并创建 Xamarin.Android 应用程序时重用这些现有的库。 

有三个可能的方法可重复使用在 Xamarin.Android 应用程序的 Java 库： 

-   **创建 Java 绑定库**&ndash;凭借此技术，则使用 Xamarin.Android 项目创建 C# Java 类型包装。 Xamarin.Android 应用程序可以然后引用此项目创建的 C# 包装，然后使用`.jar`文件。 

-   **Java 本机接口** &ndash; *Java 本机**接口*(JNI) 是一个框架，允许非 Java 代码 （如 c + + 或 C# 中） 调用或由在 JVM 内运行的 Java 代码. 

-   **将代码移植**&ndash;此方法包括 Java 源代码，以及然后将其转换为 C#。 这可以手动或通过使用如锐化的自动化的工具。 

前两个方法的核心是*Java 本机接口*(JNI)。 JNI 是一个框架，允许未用 Java 编写的应用程序与在 Java 虚拟机中运行的 Java 代码进行交互。 Xamarin.Android 使用 JNI 创建*绑定*对 C# 代码。 

第一项技术是绑定 Java 库的自动化程度更高的声明性方法。 它涉及到使用 Visual Studio 用于 Mac 或 Visual Studio 项目类型提供的 Xamarin.Android &ndash; Java 绑定库。 要成功创建这些绑定，Java 绑定库，可能仍需要一些手动修改，但没有那么多一样将纯 JNI 方法。 请参阅[绑定 Java 库](~/android/platform/binding-java-library/index.md)Java 绑定库有关的详细信息。 

第二种方法，使用 JNI，工作在多较低级别，但可以提供更精细的控制并访问将通常无法访问通过 Java 绑定库的 Java 方法。 

第三个方法是通过从根本上不同于前面的两种： 移植对 C# 通过 Java 代码。 将代码移植从一种语言到另一个可能是一个非常费力的过程，但可以减少的工具的帮助的工作量称为*锐化*。 强化是开源工具，它是 Java-到-C# 转换器。 



## <a name="summary"></a>总结

本文档提供一些不同的方式，可以在 Xamarin.Android 应用程序中重用通过 Java 库的高级的概述。 它引入了绑定的概念和管理可调用包装器，并探讨了用于 Java 将代码移植到 C# 选项。 


## <a name="related-links"></a>相关链接

- [体系结构](~/android/internals/architecture.md)
- [绑定 Java 库](~/android/platform/binding-java-library/index.md)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [锐化](https://github.com/slluis/sharpen)
- [Java 本机接口](http://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
