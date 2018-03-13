---
title: Toolbar
description: "工具栏是一种操作栏组件，它提供更大的灵活性比默认操作栏： 可以在应用程序中任意位置放置，可以更改其大小，并且它可以使用不同于应用程序的主题配色方案。 此外，每个应用程序屏幕可以有多个工具栏。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 30b1cb280c2817f55d73e10ff8b4d7942011bf2c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="toolbar"></a>Toolbar

_工具栏是一种操作栏组件，它提供更大的灵活性比默认操作栏： 可以在应用程序中任意位置放置，可以更改其大小，并且它可以使用不同于应用程序的主题配色方案。此外，每个应用程序屏幕可以有多个工具栏。_

 
## <a name="overview"></a>概述

任何 Android 活动的设计的关键元素是*操作栏*。 操作栏是用于导航、 搜索、 菜单和品牌 Android 应用中的 UI 组件。 在 Android 5.0 棒糖，操作栏之前的 Android 版本 (也称为*应用栏*) 已提供此功能的建议的组件。 

`Toolbar`可以将小组件 （在 Android 5.0 棒糖形中引入） 看作操作栏接口的泛化&ndash;它旨在替换操作栏。 `Toolbar`可以使用应用的布局中中的任意位置，并且它可操作栏比得多自定义。 下面的屏幕截图演示的自定义`Toolbar`在本指南中创建的示例： 

[![与编辑，工具栏的示例屏幕快照保存和溢出菜单项](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

有一些重要差异`Toolbar`和操作栏： 

-   A`Toolbar`可以放置在用户界面中的任何地方。

-   可以在同一屏幕上显示多个工具栏。

-   如果使用片段，每个片段都有其自己`Toolbar`。 

-   A`Toolbar`可以配置为跨仅屏幕的部分的宽度。 

-   因为`Toolbar`未绑定到活动的窗口装饰的配色方案，它可以直观地不同的颜色方案。 

-   与操作栏中，不同`Toolbar`不包括在左侧的图标。 在右侧其菜单使用较少的空间。 

-   `Toolbar`高度进行调整。 

-   其他视图可以包含在`Toolbar`。 

A`Toolbar`可以包含一个或多个以下元素： 

-   导航按钮

-   品牌的徽标图像

-   标题和副标题

-   自定义视图

-   操作菜单

-   溢出菜单

Google[材料设计准则](https://material.google.com/)建议利用类元素，以便为应用提供各自的外观 （而不是完全依赖于一个应用程序图标和标题）。 

本指南包含一些最常用的`Toolbar`方案：

-   替换与活动的默认操作栏`Toolbar`。 

-   添加第二个`Toolbar`为活动。

-   使用**Android 支持库 v7 AppCompat**库 (称为*AppCompat*本指南的其余部分中) 来部署`Toolbar`在早期版本的 Android。 

 
 
## <a name="requirements"></a>惠?

`Toolbar` 在 Android 5.0 棒糖形 (API 21) 上并且更高版本才可用。 当面向 Android 早于 Android 5.0 释放时，使用[Android 支持库 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)，该属性提供向后兼容`Toolbar`支持 NuGet 程序包中。 
[工具栏兼容性](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md)说明如何使用此库。 




## <a name="related-links"></a>相关链接

- [棒糖形工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
