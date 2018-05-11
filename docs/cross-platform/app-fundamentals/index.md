---
title: 共享代码
description: 应用程序的核心概念
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 4cca202627d1b901e00532c92598ffddd48b4853
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="sharing-code"></a>共享代码

本部分提供的指南上的一些较常见的操作任务或开发人员需要时应注意的开发移动应用程序的概念。

## <a name="code-sharing-overviewcode-sharingmd"></a>[代码共享概述](code-sharing.md)

了解不同的代码共享选项适用于 Xamarin 项目，包括可移植类库 (Pcl)、 共享项目和.NET 标准库。


##  <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[可移植类库](~/cross-platform/app-fundamentals/pcl.md)

可移植类库项目，可以生成和分发包含共享的代码以在多个平台上运行的程序集。 若要创建可移植类库 （或"PCL"） 第一次选择哪些平台为目标，然后编写代码针对.NET Framework 中定义这些平台的配置文件提供一子组。 本文档介绍如何创建和 Xamarin 中使用 Pcl。

##  <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)

共享的项目中，可以编写大量不同的应用程序项目的引用的常见代码。 该代码编译为每个引用的项目的一部分，并可以包括编译器指令来帮助整合共享的代码库中的特定于平台的功能。 本文讨论了共享项目的工作原理以及如何创建和使用它们与 Xamarin 项目。

##  <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET 标准是跨平台共享代码的新选项。 它的可移植类库; 类似的方式在工作代码针对特定版本 (当前 1.0 到 1.6) 构建的随后可使用其他支持该级别的项目或更高版本。 标准.NET 项目 Xamarin Studio 6.2、 Visual Studio 为 Windows 和 Visual Studio 中支持的 mac。

##  <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[NuGet 项目： 代码共享的多平台库](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

可以从 PCL 或.NET 标准项目; 自动生成 NuGet 包和共享项目可以打包到使用单独的 NuGet 项目类型"诱饵 and 交换机"NuGet 包。 本部分介绍如何创建每个代码共享的方案的 NuGet 包。

##  <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[手动为 Xamarin 创建 NuGet 包](~/cross-platform/app-fundamentals/nuget-manual.md)

用于创建使用 Xamarin 平台的 NuGet 程序包的技巧。
