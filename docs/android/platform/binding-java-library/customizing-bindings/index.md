---
title: "自定义绑定"
description: "你可以通过编辑控制绑定过程的元数据自定义 Xamarin.Android 绑定。 这些手动修改往往有必要来解决生成错误状态，供你调整所获得的 API，以便与 C# 更加一致 /.NET。 这些指南介绍了此元数据的结构、 如何修改的元数据，以及如何使用 JavaDoc 恢复方法参数的名称。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 09/25/2017
ms.openlocfilehash: 14372c3ca42d1ba4a8ade1248f3c5f3210cc7e46
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="customizing-bindings"></a>自定义绑定

_你可以通过编辑控制绑定过程的元数据自定义 Xamarin.Android 绑定。这些手动修改往往有必要来解决生成错误状态，供你调整所获得的 API，以便与 C# 更加一致 /.NET。这些指南介绍了此元数据的结构、 如何修改的元数据，以及如何使用 JavaDoc 恢复方法参数的名称。_


## <a name="overview"></a>概述
 
Xamarin.Android 自动执行大部分绑定过程中;但是，在某些情况下手动修改，必须先解决以下问题：

-   解决生成错误引起的缺少类型、 经过模糊处理的类型、 重复名称、 类可见性问题和无法解析其他情况下通过 Xamarin.Android 工具。 

-   更改 Xamarin.Android 使用将 Android API 绑定到 C# 中的不同类型的映射 (例如，许多开发人员喜欢映射 Java`int`向 C# 常量`enum`常量)。

-   删除未使用不需要绑定的类型。 

-   添加具有基础 Java API 中没有对应项的类型。 

通过修改控制绑定过程的元数据，可以使某些或所有这些更改。


## <a name="guides"></a>参考线

以下指南描述控制绑定过程的元数据，还介绍了如何修改此元数据以解决这些问题：

-   [Java 绑定元数据](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供进入 Java 绑定的元数据的概述。
    描述各种手动步骤，有时所需完成 Java 绑定库，以及它还说明了如何调整 API 公开的一个绑定以更严格遵循.NET 设计准则。

-   [命名参数与 Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md)介绍了如何通过使用生成从绑定的 Java 项目的 Javadoc 恢复的 Java 绑定项目中的参数名称。


 

