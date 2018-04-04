---
title: 选项卡式的布局
description: 在 Android 中的选项卡式布局事件的概述
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/23/2017
ms.openlocfilehash: 4095944bb630637a2e761af18796dacdef17785c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="tabbed-layouts"></a>选项卡式的布局


## <a name="overview"></a>概述

选项卡是由于其简单性和可用性的移动应用程序中的常见用户界面模式。 它们提供一致的轻松地在应用程序的各种屏幕之间导航。 Android 有几个 API 的选项卡式接口： 

-   **TabHost** &ndash;这是用于创建选项卡式的用户界面的原始 API。 A`TabHost`小组件添加到布局，并且可作为选项卡式视图的容器。 此 API 已由于弃用并建议不要使用它的。 

-   **其中**&ndash;这是一组新的 API 的 Android 3.0 （API 级别 11） 中引入的目标是提供一个一致的一部分导航和视图切换接口。 已使用的 Android 2.2 （API 级别 8） 到返回移植[Android 支持库 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)。 

-   **PagerTabStrip** &ndash;指示当前、 下一步，和上一个页面的`ViewPager`。 `ViewPager` 可仅通过[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。
     有关详细信息`PagerTabStrip`，请参阅[ViewPager](~/android/user-interface/controls/view-pager/index.md)。

-   **工具栏** &ndash; `Toolbar`是取代的更新、 更灵活操作栏组件`ActionBar`。 `Toolbar` 在 Android 5.0 棒糖形中可用或更高版本，它也是适用于较旧版本的 Android 通过[Android 支持库 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet 包。 
    `Toolbar` 目前可以使用 Android 应用中的建议的操作栏组件。
    有关详细信息，请参阅[工具栏](~/android/user-interface/controls/tool-bar/index.md)。 


这些 API 以可视方式大不相同，并与相互不兼容。 以下屏幕图所示`TabHost`和`ActionBar`并排显示： 

![屏幕快照的 TabHost 左侧和右侧的操作栏](images/image01.png)

自 Android 3.0 （API 级别 11），由于重大 UI 更改存在这些不兼容的 API。 这些 UI 更改之一是[操作栏设计模式](http://www.androidpatterns.com/uap_pattern/action-bar)，一种模式旨在提供轻松访问应用程序中的导航和密钥的功能。 `ActionBar`引入 API 是为了支持此模式。 

`ActionBar` API 更简单且说提供更好的用户体验。 它已返回移植到 Android 2.2，并且是 Xamarin.Android 应用程序的首选的解决方案。 

`TabHost` API 兼容跨所有版本的 Android，但需要更多工作来使用并与当前不一致时[Android UI 准则](http://developer.android.com/design/index.html)。 开发人员我们建议您不要使用此 API，应 favour Xamarin.Android 应用程序的较新的操作栏。 



## <a name="actionbarsherlock"></a>ActionBarSherlock

其中 API 是向后移植到 Android 2.2，开发人员想其中 API 的较新的外观和感觉，但可以使用第三方库中之前[ActionBarSherlock](http://actionbarsherlock.com)。 ActionBarSherlock 是 Android 支持库的扩展设计为向后移植到 Android 2.x 的操作栏设计模式。 在运行 Android 3.0 或更高版本，ActionBarSherlock 将自动使用本机`ActionBar`由 Android 提供的 API。 较旧版本的 Android 将使用将模拟外观和感觉的较新的自定义实现而`ActionBar`API。 [ActionBarSherlock 组件](https://www.nuget.org/packages/xamstore-XamarinActionBarSherlock/)可以轻松地将 ActionBarSherlock 添加到 Xamarin.Android 应用程序。 



## <a name="related-links"></a>相关链接

- [TabHost 概述](tab-host.md)
- [TabHost 演练](~/android/user-interface/layouts/tab-layout/creating-a-tabbed-ui.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支持库 v7 AppCompat NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 库](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
