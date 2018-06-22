---
title: 移植对 C# 的 Java
description: 在 Xamarin.Android 应用程序中使用 Java 的第三个选项是将 Java 源代码移植到 C#。
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/05/2016
ms.openlocfilehash: c2d05b101c627dab42dc1343eab2a408d1bd010f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762592"
---
# <a name="porting-java-to-c"></a>移植对 C# 的 Java

_在 Xamarin.Android 应用程序中使用 Java 的第三个选项是将 Java 源代码移植到 C#。_

## <a name="overview"></a>概述

这种方法可能感兴趣的组织的：

-  **正在技术堆栈从 Java 向 C#。**
-  **必须维护的 C# 和相同的产品的 Java 版本。**
-  **想要有常用的 Java 库的.NET 版本。**


有两种方法 Java 代码移植到 C#。 第一种方法是手动移植代码。 这涉及到技术精湛的开发人员了解.NET 和 Java 和熟悉的每种语言正确惯例。 这种方法最有意义为少量的代码，或想要完全离开 Java 移动到 C# 的组织。

第二个迁移方法是尝试并自动执行的过程，通过使用代码转换器如[锐化](https://github.com/mono/sharpen)。 [强化](https://github.com/mono/sharpen)是最初用于端口的代码的 Versant 从开放源代码转换器*db4o*通过向 C# 的 Java。 db4o 是一种面向对象的数据库 Versant 在 Java 中，开发和然后移植到.NET。 使用代码转换器可能有意义的项目，必须存在于这两种语言，并且需要这两者之间的一些奇偶校验。

中可以看到的自动化的代码转换工具意义示例[ngit](https://github.com/mono/ngit)项目。
Ngit 是 Java 项目的端口[jgit](http://eclipse.org/)。
Jgit 本身是的 Java 实现[Git](http://git-scm.com/)源代码管理系统中。 若要从 Java 生成 C# 代码，请 ngit 程序员使用自定义的自动的系统以从 jgit 提取的 Java 代码，将以适应转换过程中，某些修补程序应用，然后运行锐化，生成的 C# 代码。 这允许 ngit 项目能够受益于在 jgit 完成的连续和不间断工作。

没有通常非普通少量工作涉及引导的自动化的代码转换工具，这可能证实障碍，从而将使用。 在许多情况下，它可能是更简单、 更轻松地向 C# 的端口 Java 手动。



## <a name="related-links"></a>相关链接

- [强化转换工具](https://github.com/mono/sharpen)
