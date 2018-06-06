---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: 常见任务比较
description: 本文档会比较如何在 WPF 和 Xamarin.Forms 上执行各种常见任务。 它查找在按钮、 计时器、 字体大小，打开一个 URI，并显示一个操作表。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 2991e74ec59e3c43c665941706c2d9ac94bfb9ad
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780471"
---
# <a name="common-tasks-comparison"></a>常见任务比较

| 任务 | WPF | Xamarin.Forms |
|--- |--- |--- |
|使用按钮在屏幕上显示一条消息|`MessageBox`|`Page.DisplayAlert`|
|创建一个计时器|`DispatcherTimer` 类|`Device.StartTimer` 静态方法|
|获取默认字体大小|`SystemFonts` 静态类|`Device.GetNamedSize` 静态方法|
|打开 URI/URL|`Process.Start`|`Device.OpenUri`|
|显示操作表 （列表的按钮）|n/a|`Page.DisplayActionSheet`|
