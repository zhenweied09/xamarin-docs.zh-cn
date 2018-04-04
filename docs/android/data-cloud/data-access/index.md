---
title: Xamarin.Android 数据访问
description: 大多数应用程序具有一些要求将数据保存在本地设备上。 除非是非常小的数据量，这通常需要一个数据库和数据层应用程序管理数据库的访问权限中。  Android 有内置 SQLite 数据库引擎和 Xamarin 的平台由简化访问存储和检索数据。 本文档演示如何以跨平台方式访问的 SQLite 数据库。
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 508a8f54723bfdd30b1c8ea8d4b6c1d422ae24e9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinandroid-data-access"></a>Xamarin.Android 数据访问

_大多数应用程序具有一些要求将数据保存在本地设备上。除非是非常小的数据量，这通常需要一个数据库和数据层应用程序管理数据库的访问权限中。Android 有内置 SQLite 数据库引擎和 Xamarin 的平台由简化访问存储和检索数据。本文档演示如何以跨平台方式访问的 SQLite 数据库。_

## <a name="data-access-overview"></a>数据访问概述

大多数应用程序具有一些要求将数据保存在本地设备上。 除非是非常小的数据量，这通常需要一个数据库和数据层应用程序管理数据库的访问权限中。 Android 这两个具有"内置"Sqlite 数据库引擎和 Xamarin 的平台，它附带 SQLite 数据提供程序由简化对数据的访问。

Xamarin.Android 支持数据库访问 Api，例如：

-  ADO.NET 框架。
-  SQLite NET 第三方库。

此部分中的代码的大部分是完全跨平台的并将在 iOS 或 Android 上运行而不进行修改。 有两个示例应用程序所述：

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash;简单的数据操作写入结果显示为文本显示控件;

-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash;集成到一个小的工作应用程序列出并编辑简单的数据结构的数据操作。

这两个示例解决方案包含 iOS 和 Android 示例应用程序项目。

对于 Xamarin.Forms 应用程序，读取[使用数据库](~/xamarin-forms/app-fundamentals/databases.md)其中解释了如何使用 SQLite PCL 库中使用 Xamarin.Forms。

本部分中的主题讨论中使用 SQLite 作为数据库引擎的 Xamarin.Android 的数据访问。 可以使用 ADO.NET 的语法通过"直接"访问该数据库或可以包括 SQLite.NET ORM 和用 C# 执行数据操作。

评审两个样本： 包含非常简单的数据访问代码输出到文本字段中和的简单应用程序包括创建、 读取、 更新和删除功能。 线程处理和如何种子预填充的 SQLite 数据库上的应用程序还将讨论。

有关跨平台数据访问的其他示例，请参阅我们[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究。


## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 数据配方](https://developer.xamarin.com/recipes/android/data/)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
