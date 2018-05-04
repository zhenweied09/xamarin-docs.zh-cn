---
title: 共享代码选项
description: 本文档会比较的跨平台项目间共享代码的不同方法： 共享项目、 可移植类库和.NET 标准，包括的优点和各自的优缺点。
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 97e6167304ebf50a149fce379eb34e8a46c8d3e8
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="sharing-code-overview"></a>共享代码概述

_本文档会比较的跨平台项目间共享代码的不同方法： 共享项目、 可移植类库和.NET 标准，包括的优点和各自的优缺点。_

有三个用于跨平台应用程序间共享代码的替代方法：

-   [**共享项目**](#Shared_Projects) -使用共享资产项目类型来组织你的源代码，并根据需要使用 #if 编译器指令，以管理特定于平台的要求。
-   [**可移植类库**](#Portable_Class_Libraries) – 创建可移植类库 (PCL) 针对的平台，你想要支持，并使用接口来提供特定于平台的功能。
-   [**.NET 标准库**](#Net_Standard) – 标准.NET 项目的工作方式与 Pcl，需使用接口将特定于平台的功能类似。

代码共享策略的目标是支持的单个基本代码可以利用多个平台此图中所示的体系结构。

 ![](code-sharing-images/conceptualarchitecture.png "共享的代码应用程序体系结构")

本文将帮助你选择你的应用程序的正确的项目类型的三个方法进行比较。

<a name="Shared_Projects" />

## <a name="shared-projects"></a>共享的项目

共享代码文件的最简单方法是使用[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)。

此屏幕快照显示 （用于 Android、 iOS 和 Windows Phone），包含三个应用程序项目的解决方案文件与**共享**包含常见 C# 源代码文件的项目：

 ![](code-sharing-images/sharedsolution.png "共享的项目解决方案")

概念体系结构下图中，其中每个项目包括所有的共享的源文件所示：

 ![](code-sharing-images/sharedassetproject.png "共享项目关系图")


### <a name="example"></a>示例

支持 iOS、 Android 和 Windows Phone 的跨平台应用程序将需要为每个平台的应用程序项目。 通用代码驻留在共享项目中。

一个示例解决方案将包含以下文件夹和项目 （项目名称为已选定表现力，你的项目不需要遵循以下命名准则）：

-   **共享**– 包含普遍适用于所有项目的代码的共享的项目。
-   **AppAndroid** – Xamarin.Android 应用程序项目。
-   **AppiOS** – Xamarin.iOS 应用程序项目。
-   **AppWinPhone** – Windows Phone 应用程序项目。


在这种方式中的三个应用程序项目共享相同的源代码 （C# 中的文件共享）。 共享代码的任何编辑将在所有三个项目之间共享。


### <a name="benefits"></a>优点

-  可以在多个项目间共享代码。
-  可以根据使用 （如编译器指令的平台分支共享的代码 使用`#if __ANDROID__`、 中所述[生成跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文档)。
-  应用程序项目可以包括可以利用共享的代码的特定于平台的引用 (例如，使用`Community.CsharpSqlite.WP7`在用于 Windows Phone 的 Tasky 示例)。



### <a name="disadvantages"></a>缺点

-  与大多数其他项目类型，共享项目产生没有输出的程序集。 在编译期间，这些文件进行视为引用的项目的一部分并编译为该程序集。 如果你想要共享作为程序集代码可移植类库或标准.NET 则更好的解决方案。
-  影响内 'inactive' 编译器指令的代码的重构不会更新代码。


 <a name="Shared_Remarks" />

### <a name="remarks"></a>备注

应用程序开发人员编写的代码，仅适用于其应用中共享 （和不将其分发给其他开发人员） 很好的解决方案。

 <a name="Portable_Class_Libraries" />


## <a name="portable-class-libraries"></a>可移植类库


可移植类库是[此处详细地讨论](~/cross-platform/app-fundamentals/pcl.md)。

 ![](code-sharing-images/portableclasslibrary.png "可移植库关系图")


### <a name="benefits"></a>优点

-  可以在多个项目间共享代码。
-  重构操作将始终更新所有受影响的引用。


### <a name="disadvantages"></a>缺点

-  不能使用编译器指令。
-  只有.NET framework 的子集可用于，由选择的配置文件 (请参阅[简介 PCL](~/cross-platform/app-fundamentals/pcl.md)有关详细信息)。


### <a name="remarks"></a>备注

如果你打算与其他开发人员共享生成的程序集，一个不错的解决方案。



<a name="Net_Standard" />

## <a name="net-standard-libraries"></a>.NET 标准库

.NET 标准是[此处详细地讨论](~/cross-platform/app-fundamentals/net-standard.md)。

![](code-sharing-images/netstandard.png ".NET 标准关系图")

### <a name="benefits"></a>优点

-  可以在多个项目间共享代码。
-  重构操作将始终更新所有受影响的引用。
-  更大的外围应用的.NET 基类库 (BCL) 是比 PCL 配置文件可用。

### <a name="disadvantages"></a>缺点

 -  不能使用编译器指令。

### <a name="remarks"></a>备注

.NET 标准是类似于 PCL 中，但有更简单的模型平台支持和更多的 BCL 的类。



## <a name="summary"></a>总结

将你面向的平台推动代码共享你选择的策略。 选择最适合你的项目的方法。

PCL 或.NET 标准是用于构建可共享代码库 （特别在 NuGet 上发布） 的很好选择。 共享的项目适用于应用程序开发人员计划在其跨平台应用中使用大量 plaform 特有的功能。


## <a name="related-links"></a>相关链接

- [生成跨平台应用程序 （主文档）](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [可移植类库](~/cross-platform/app-fundamentals/pcl.md)
- [共享项目](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky 示例 (github)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Tasky 示例使用 PCL (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
