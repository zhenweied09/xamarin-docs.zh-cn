---
title: Xamarin.iOS 数据访问
description: 此文档链接到指南描述如何在 Xamarin.iOS 应用程序中的本地数据库使用。 链接的内容讨论 SQLite.NET、 ADO.NET，和的详细信息。
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: a986ea9931f62497e5a6863c84bd4041983d66d9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784571"
---
# <a name="xamarinios-data-access"></a>Xamarin.iOS 数据访问

Xamarin.iOS 支持数据库访问 Api，如：

-  ADO.NET 框架。
-  SQLite NET 第三方库。

本指南描述如何设置 ADO.NET 和 SQLite.NET 来访问你的 Xamarin.iOS 应用程序中的 SQLite 数据库之前在一般情况下提供数据库的高级概述。 

本文档中的代码的大部分是完全跨平台的并将在 iOS 或 Android 上运行而不进行修改。 有两个示例应用程序所述：

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) – 简单的数据操作写入结果显示为文本显示控件;
-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) – 集成到一个小的工作应用程序列出并编辑简单的数据结构的数据操作。

这两个示例解决方案包含 iOS 和 Android 示例应用程序项目。

对于 Xamarin.Forms 应用程序，读取[使用数据库](~/xamarin-forms/app-fundamentals/databases.md)其中解释了如何使用 SQLite PCL 库中使用 Xamarin.Forms。

## <a name="sections"></a>部分

-  [介绍](introduction.md)
-  [配置](configuration.md)
-  [使用 SQLite.NET ORM](using-sqlite-orm.md)
-  [使用 ADO.NET](using-adonet.md)
-  [在应用中使用数据](using-data-in-an-app.md)

## <a name="summary"></a>总结

这一章讨论中使用 SQLite 作为数据库引擎的 Xamarin.iOS 的数据访问。 可以使用 ADO.NET 语法"直接"访问该数据库或可以包括 SQLite.NET ORM 和用 C# 执行数据操作。

我们回顾了两个样本： 包含非常简单的数据访问代码输出到文本字段中和的简单应用程序包括创建、 读取、 更新和删除功能。 我们还讨论了线程处理和如何种子预填充的 SQLite 数据库上的应用程序。

有关跨平台数据访问的其他示例，请参阅我们[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究。

## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据配方](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
