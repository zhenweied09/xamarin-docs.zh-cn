---
title: Javadoc 的命名参数
description: 此文章介绍了如何通过使用生成从 Java 项目的 Javadoc 恢复 Java 绑定项目中的参数名称。
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/20/2017
ms.openlocfilehash: 7517e46c5b66123dc4e12fb5562c59f569f249aa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="naming-parameters-with-javadoc"></a>Javadoc 的命名参数

_此文章介绍了如何通过使用生成从 Java 项目的 Javadoc 恢复 Java 绑定项目中的参数名称。_


## <a name="overview"></a>概述

当绑定现有 Java 库时，一些有关绑定的 API 的元数据将丢失。 尤其是对方法的参数的名称。 参数名称将显示为`p0`， `p1`，等等。这是因为 Java`.class`文件不会保留参数名称中使用 Java 源代码。 

如果它可以访问的 Javadoc html 从原始库，Xamarin.Android Java 绑定项目可以提供参数名称。 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>集成到绑定项目 Java 的 Javadoc HTML

将 Javadoc HTML 集成到 Java 绑定项目是一个包含以下步骤的手动过程： 

1.  下载库 Javadoc
2.  编辑`.csproj`文件并添加`<JavaDocPaths>`属性：
3.  清除并重新生成项目

完成此操作后，原始 Java 参数名称应存在于通过 Java 绑定项目绑定的 Api。 


> [!NOTE]
> 没有大量 JavaDoc 输出中的变体。 。JAR 绑定工具链不支持每个单个可能排列，因此某些参数可能未正确命名。


## <a name="summary"></a>总结

本文介绍如何在 Java 绑定项目中使用 Javadoc 提供含义的参数名称用于绑定的 Api。 

