---
title: 选项卡式的布局
description: 在 Android 中的选项卡式布局事件的概述
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/08/2017
ms.openlocfilehash: 53ed5f91583d43839e96388194aea8c0d6ac5315
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
ms.locfileid: "34149255"
---
# <a name="tabbed-layouts"></a>选项卡式的布局


## <a name="overview"></a>概述

选项卡是由于其简单性和可用性的移动应用程序中的常见用户界面模式。 它们提供一致的轻松地在应用程序的各种屏幕之间导航。 Android 有几个 API 的选项卡式接口： 

-   **其中**&ndash;这是一组新的 API 的 Android 3.0 （API 级别 11） 中引入的目标是提供一个一致的一部分导航和视图切换接口。 已使用的 Android 2.2 （API 级别 8） 到返回移植[Android 支持库 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)。 

-   **PagerTabStrip** &ndash;指示当前、 下一步，和上一个页面的`ViewPager`。 `ViewPager` 可仅通过[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。
     有关详细信息`PagerTabStrip`，请参阅[ViewPager](~/android/user-interface/controls/view-pager/index.md)。

-   **工具栏** &ndash; `Toolbar`是取代的更新、 更灵活操作栏组件`ActionBar`。 `Toolbar` 在 Android 5.0 棒糖形中可用或更高版本，它也是适用于较旧版本的 Android 通过[Android 支持库 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet 包。 
    `Toolbar` 目前可以使用 Android 应用中的建议的操作栏组件。
    有关详细信息，请参阅[工具栏](~/android/user-interface/controls/tool-bar/index.md)。 



## <a name="related-links"></a>相关链接

- [材料设计-选项卡](https://material.io/guidelines/components/tabs.html)- [操作栏](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支持库 v7 AppCompat NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 库](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
