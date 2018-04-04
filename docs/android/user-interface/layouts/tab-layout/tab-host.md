---
title: 带 TabHost 的选项卡布局
description: 本文将提供的高级别概述 TabHost，较旧的 API 用来在 Xamarin.Android 应用程序中创建选项卡式的布局。
ms.prod: xamarin
ms.assetid: 77B890A4-27A6-41DF-81BA-22C6116A8FB2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/25/2017
ms.openlocfilehash: ae5b9020e08575bcd453703f3df14f63b288d2f5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="tab-layout-with-tabhost"></a>带 TabHost 的选项卡布局

_本文将提供的高级别概述 TabHost，较旧的 API 用来在 Xamarin.Android 应用程序中创建选项卡式的布局。_


## <a name="overview"></a>概述

> [!NOTE]
> `TabHost` 是已否决 Google 的旧 API。 若要生成使用的选项卡式应用程序，鼓励开发人员[其中](~/android/user-interface/controls/action-bar.md)。 `ActionBar`在所有版本的 Android 中可用。 它首先 Android 3.0 （API 级别 11） 中引入并返回已移植到 Android 2.2 （API 级别 8） 和 Android 2.3 （API 级别 10） 中[V7 AppCompat 库](http://developer.android.com/tools/support-library/features.html#v7-appcompat)，可供通过 Xamarin.Android [XamarinAndroid 支持库-V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)包。

`TabHost`是创建选项卡式的用户 interfacesIt 是适合于 Xamarin.Android 应用程序必须支持 Android 2.2 和 Android 2.3 并且不能使用旧的、 原始 API **ActionBarSherlock**。
以下五个组件是与涉及到`TabHost`API:

-  **TabActivity** &ndash;这是专用的视图，充当容器`TabHost`（如下所述）。

-  **TabHost** &ndash;这是选项卡式 UI 的容器。 它承载两个子级： 一个列表的选项卡标签的和一`FrameLayout`的显示选项卡中的内容。

-  **TabWidget** &ndash;此控件显示的选项卡标签，另一个用于在每个选项卡列表`TabHost`。 在每个选项卡`TabHost`由表示`TabHost.TabSpec`对象。 此对象包含有关每个选项卡的元数据。当用户选择一个选项卡，`TabHost`通过显示相应的选项卡来对所选内容进行响应。

-  **FrameLayout** &ndash;选项卡式的 UI 必须具有`FrameLayout`内包含`TabHost`。 它用于显示为选项卡的内容。

-  **活动或视图**&ndash;选中一个选项卡时，它显示活动或中的视图`FrameLayout`。

下图显示了所有这些组件如何关联在一起：

![示意图中内 TabHost TabWidget 帧布局](tab-host-images/image03.png)

选项卡内容可能是活动或视图。 视图是相对较轻量和简单，但是可能会导致大量的不相关的代码 co habitating 活动中。 这将导致不佳的问题和难以维护的臃肿的类分离。 与此相反，活动需要系统资源，但允许通过其自己的不同类中封装每个选项卡的逻辑实现更加模块化方法。


## <a name="summary"></a>总结

本文所述的较旧的高级别组件`TabHost`适用于 Android 和所有它们如何一起相关的 API。



## <a name="related-links"></a>相关链接

- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [TabHost](https://developer.xamarin.com/api/type/Android.Widget.TabHost/)
- [TabHost.TabSpec](https://developer.xamarin.com/api/type/Android.Widget.TabHost+TabSpec/)
- [TabWidget](https://developer.xamarin.com/api/type/Android.Widget.TabWidget/)
- [TabActivity](https://developer.xamarin.com/api/type/Android.App.TabActivity/)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支持库 v7 AppCompat NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 库](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
