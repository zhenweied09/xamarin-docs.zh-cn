---
title: 布局
description: 定义 Xamarin.Android 应用程序的可视结构
ms.prod: xamarin
ms.assetid: 2BA72B0E-230D-4F98-B4D5-4EFB0D479789
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/18/2017
ms.openlocfilehash: 01ea244c7480082f2b3b39c0bdae2bad6807aa6d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="layouts"></a>布局

布局用于排列构成屏幕 （如活动） 的 UI 接口的元素。 以下各节说明如何在 Xamarin.Android 应用程序中使用最常用的布局。

-   [LinearLayout](~/android/user-interface/layouts/linear-layout.md)是垂直或水平方向线性，显示子视图元素的视图组。

    ![线性布局示例](images/linear-layout.png)

-   [RelativeLayout](~/android/user-interface/layouts/relative-layout.md)是查看组中的相对位置显示子视图元素。 相对于同级元素，可以指定视图的位置。

    ![相对布局示例](images/relative-layout.png)

-   [TableLayout](~/android/user-interface/layouts/table-layout.md)是一种组视图在行和列中显示子视图元素。

    ![示例表布局](images/table-layout.png)

-   [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)是 UI 元素，它显示为列表或网格中，使用户可以滚动浏览集合中的项的集合。

    ![示例 Recycler 视图](images/recycler-view.png)

-   [ListView](~/android/user-interface/layouts/list-view/index.md)是创建可滚动的项列表的一组视图。 列表项自动插入到使用列表适配器列表中。 `ListView`是 Android 应用程序的一个重要 UI 组件，因为它使用无处不在从短菜单选项的列表到冗长的联系人或 internet 收藏夹列表。 它提供一种简单的方法，以提供滚动列表中可以使用内置样式格式化或全面地自定义的行。 ListView 实例需要的适配器，才能与源行视图中包含的数据。

    ![示例列表视图](images/list-view.png)

-   [GridView](~/android/user-interface/layouts/grid-view.md)是可滚动的二维网格中显示的项的 UI 元素。

    ![示例网格视图](images/grid-view.png)

-   [GridLayout](~/android/user-interface/layouts/grid-layout.md)是视图支持的组的布局类似于 HTML 表的二维网格中的视图。

    ![网格布局示例](images/grid-layout.png)

-   [选项卡式布局](~/android/user-interface/layouts/tab-layout/index.md)由于其简单性和可用性是在移动应用程序的常见用户界面模式。 它们提供一致的轻松地在应用程序的各种屏幕之间导航。

    ![选项卡式的布局示例](images/tabbed-layout.png)
 
