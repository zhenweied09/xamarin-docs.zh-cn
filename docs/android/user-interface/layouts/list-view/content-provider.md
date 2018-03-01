---
title: "使用 ContentProvider"
ms.topic: article
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 5c90b140719e0dafb36dad1da75f52ba27e75e25
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="using-a-contentprovider"></a>使用 ContentProvider

CursorAdapters 还可用来显示 ContentProvider 中的数据。
ContentProviders 使你能够访问其他应用程序公开的数据 (包括 Android 系统数据，如联系人，媒体和日历信息)。

访问 ContentProvider 的首选的方法是使用 LoaderManager CursorLoader 使用。 在 Android 3.0 （API 级别 11，Honeycomb） 将阻止任务关闭主线程中，引入了 LoaderManager 并使用 CursorLoader 允许要绑定到显示 ListView 之前在线程中加载的数据。

请参阅[简介 ContentProviders](~/android/platform/content-providers/index.md)有关详细信息。

