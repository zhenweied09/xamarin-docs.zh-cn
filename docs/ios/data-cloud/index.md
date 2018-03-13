---
title: "数据和云服务"
description: "稳定和部署指南"
ms.topic: article
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/13/2017
ms.openlocfilehash: b5e250c7c505958c8293970321b6173e6086e7b1
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="data-and-cloud-services"></a>数据和云服务


##  <a name="data-accessiosdata-clouddataindexmd"></a>[数据访问](~/ios/data-cloud/data/index.md)

大多数应用程序具有一些要求将数据保存在本地设备上。 除非是非常小的数据量，这通常需要一个数据库和数据层应用程序管理数据库的访问权限中。 iOS Sqlite 数据库引擎"内置"，由 Xamarin 的平台，它附带 SQLite 数据提供程序简化对数据的访问。

##  <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

在 iOS 5 中的 iCloud 存储 API 允许应用程序将用户文档和应用程序特定数据保存到一个中心位置，并从用户的所有设备访问这些项。

##  <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

该访问 iCloud，CloudKit framework 简化了开发应用程序。 这包括应用程序数据和资产权限，以及能够安全地存储应用程序信息的检索。 此工具包提供用户的匿名层通过允许访问其 iCloud Id 与应用程序而不用共享个人信息。