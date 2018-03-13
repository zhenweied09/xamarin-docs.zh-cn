---
title: "介绍"
ms.topic: article
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: bd3f02ab4fd5b6e96db8bd986e5a698ce30a6b74
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="introduction"></a>介绍

## <a name="when-to-use-a-database"></a>何时使用数据库

虽然移动设备的存储和处理功能不断增加，手机和平板电脑仍将滞后于他们的桌面&amp;便携式计算机的对应项。 出于此原因值得花费一些时间来计划您的应用程序的数据存储体系结构，而不是只需假设数据库正确答案的时间。 有大量的不同选项的适应不同的需求，如：

-  **首选项**– iOS 提供内置机制来存储数据的简单的键 / 值对。 如果你要存储简单的用户的设置或少量数据 （例如个性化信息） 然后使用该平台的本机功能来存储此类型的信息。 适用于 iOS 你还可以利用的此数据，同时进行备份和同步具有多个设备的用户的 iCloud 同步。
-  **文本文件**– 用户输入或缓存的下载内容 （如。 可以直接在文件系统上存储 HTML)。 使用适当的文件命名约定来帮助你组织的文件和查找数据。
-  **序列化数据文件**– 对象可以在文件系统上保留为 XML 或 JSON。 .NET framework 包括进行序列化和反序列化对象轻松的库。 使用相应的名称来组织数据文件。
-  **数据库**– SQLite 数据库引擎是可用的 iOS，并可用于存储结构化的数据，你需要查询、 排序或以其他方式操作。 数据库存储适用于具有许多属性数据的列表。
-  **图像文件**– 但也可以在移动设备上的数据库中存储二进制数据，建议将它们存储在文件系统中直接。 如有必要您可以存储在数据库可与其他数据关联的映像文件名。 在处理大型图像或大量映像，很好的做法计划删除不再需要避免使用用户的所有存储空间的文件的缓存策略。


如果数据库是用于你的应用程序的适当的存储机制，此文档的其余部分将讨论如何在 Xamarin 平台上使用 SQLite。

## <a name="advantages-of-using-a-database"></a>使用数据库的优点

有很多在你的移动应用中使用的 SQL 数据库的优点：

-  SQL 数据库允许结构化数据的高效的存储设备。
-  可以使用复杂查询提取特定的数据。
-  查询结果可以进行排序。
-  可以聚合查询结果。
-  使用现有数据库技能的开发人员可以利用知识，以设计数据库和数据访问代码。
-  从连接的应用程序的服务器组件的数据模型可能重新用于 （整体或部分） 在移动应用程序中。


## <a name="sqlite-database-engine"></a>SQLite 数据库引擎

SQLite 是已由 Apple 采用的其移动平台的开放源代码数据库引擎。 SQLite 数据库引擎是内置于 iOS 的因此开发人员可以利用它的任何额外工作。 SQLite 是适合于跨平台移动开发的因为：

-  数据库引擎是小型、 快速且轻松地移植。
-  数据库存储在单个文件，可以轻松管理移动设备上。
-  跨平台易于使用的文件格式是： 是否 32 位或 64 位和大或很少-endian 系统。
-  它可实现大部分标准 sql92 功能。


SQLite 旨在作为小型和快速，因为它的使用有一些需要注意的问题：

-  不支持某些外部联接语法。
-  仅限于表重命名和 ADDCOLUMN 受支持。 无法执行对架构进行其他修改。
-  视图是只读的。


你可以了解有关 SQLite 网站-上[SQLite.org](http://SQLite.org) -但你需要通过 Xamarin 使用 SQLite 的所有信息包含在本文档，并且关联示例。 SQLite 数据库引擎是内置于 iOS 的所有版本。
尽管未涵盖在本章中，也是可用于在 Windows Phone 和 Windows 应用程序上使用 SQLite。

## <a name="windows-and-windows-phone"></a>Windows 和 Windows Phone

尽管本文档未涵盖这些平台，则还可以在 Windows 平台上使用 SQLite。
更多信息，请参阅[Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)和[Tasky Pro](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/case_study%3A_tasky)案例研究，并查看[Tim Heuer 博客](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx)。



## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据配方](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
