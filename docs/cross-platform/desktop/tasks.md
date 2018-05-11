---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: 常见任务比较
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: b4d45ae61c5d7a7093d51c4440d11dd883c157a4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="common-tasks-comparison"></a>常见任务比较

| 任务 | WPF | Xamarin.Forms |
|--- |--- |--- |
|使用按钮在屏幕上显示一条消息|`MessageBox`|`Page.DisplayAlert`|
|创建一个计时器|`DispatcherTimer` 类|`Device.StartTimer` 静态方法|
|获取默认字体大小|`SystemFonts` 静态类|`Device.GetNamedSize` 静态方法|
|打开 URI/URL|`Process.Start`|`Device.OpenUri`|
|显示操作表 （列表的按钮）|n/a|`Page.DisplayActionSheet`|
