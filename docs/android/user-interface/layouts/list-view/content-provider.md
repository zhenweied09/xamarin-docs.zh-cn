---
title: 使用 ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d1ec628de3481820f320a5a8e6ef88fcbaab75a6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122109"
---
# <a name="using-a-contentprovider"></a>使用 ContentProvider

此外可以使用 CursorAdapters 以显示来自 ContentProvider 数据。
Contentprovider 允许你访问由其他应用程序公开的数据 (包括 Android 系统数据，如联系人、 媒体和日历信息)。

访问 ContentProvider 的首选的方法是使用 LoaderManager CursorLoader 使用。 在 Android 3.0 （API 级别 11，Honeycomb） 移动关闭主线程正在阻塞的任务中引入了 LoaderManager，并使用 CursorLoader 允许要绑定到 ListView 以便显示之前在线程中加载数据。

请参阅[简介 Contentprovider](~/android/platform/content-providers/index.md)有关详细信息。

