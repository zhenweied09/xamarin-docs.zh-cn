---
title: RecyclerView
description: "RecyclerView 用于显示集合; 是一组视图它被旨在作为较旧的视图组，如 ListView 和 GridView 的更灵活替换。  本指南说明如何使用和自 RecyclerView 定义 Xamarin.Android 应用程序中。"
ms.topic: article
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/03/2018
ms.openlocfilehash: ec8b3a4655c8e8d9e492c9f7a1807dd64ecc6ae7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView 用于显示集合; 是一组视图它被旨在作为较旧的视图组，如 ListView 和 GridView 的更灵活替换。本指南说明如何使用和自 RecyclerView 定义 Xamarin.Android 应用程序中。_

## <a name="recyclerview"></a>RecyclerView

许多应用程序需要显示集合的相同的类型 （例如消息、 联系人、 图像或歌曲）;通常情况下，此集合为太大，无法容纳在屏幕上，因此集合也会出现在一个小型窗口，可以顺利滚动集合中的所有项。
`RecyclerView` 是 Android 小组件显示为列表或网格中，使用户可以滚动浏览集合中的项的集合。 以下是示例使用的应用程序的屏幕快照`RecyclerView`显示垂直滚动列表中的电子邮件收件箱内容：

[ ![使用 RecyclerView 到列表中的收件箱邮件的示例应用程序](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png)

`RecyclerView` 提供两个极具吸引力的功能：

-  它具有灵活的体系结构，你可以通过插入你首选的组件来修改其行为。

-  它是大型集合高效的因为它会重用项视图并需要使用*查看的持有者*到缓存查看的引用。

本指南说明如何使用`RecyclerView`在 Xamarin.Android 应用程序中; 它还说明了如何添加`RecyclerView`包到你的 Xamarin.Android 项目，并描述如何`RecyclerView`典型的应用程序中的函数。 提供了真实代码示例来演示如何集成`RecyclerView`到你的应用程序、 如何实现项视图，请单击，以及如何刷新`RecyclerView`其基础数据的更改时。 本指南假定你熟悉 Xamarin.Android 开发。


### <a name="requirements"></a>惠?

尽管`RecyclerView`是通常与 Android 5.0 棒糖形相关联，它作为提供支持库&ndash;`RecyclerView`作用于应用该目标 API 级别 (Android 2.1) 7 及更高版本。 使用所需的以下`RecyclerView`基于 Xamarin 的应用程序中：

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更高版本必须安装并配置了 Visual Studio 或 Visual Studio for mac。

-  应用程序项目必须包括**Xamarin.Android.Support.v7.RecyclerView**包。 有关安装 NuGet 包的详细信息，请参阅[演练： 在你的项目包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。


### <a name="overview"></a>概述

`RecyclerView` 可视为代替`ListView`和`GridView`Android 中的小组件。 如其前置任务，`RecyclerView`都设计为在小窗口中，显示大型数据集，但`RecyclerView`提供详细的布局选项，并更好地优化用于显示大型集合。 如果你熟悉`ListView`，有几个重要区别之间`ListView`和`RecyclerView`:

-   `RecyclerView` 会稍微复杂，使用： 你必须编写更多代码以使用`RecyclerView`相比`ListView`。

-   `RecyclerView` 不提供的预定义的适配器;您必须实现访问您的数据源的适配器代码。 但是，Android 包括使用的几个预定义的适配器`ListView`和`GridView`。

-   `RecyclerView` 不提供在用户点击项; 时项 click 事件相反，项单击事件处理由帮助器类。 与此相反，`ListView`提供项 click 事件。

-   `RecyclerView` 通过回收视图和通过强制视图持有者模式，从而消除了不必要的布局的资源查找，可增强性能。 中的视图持有者模式的使用是可选`ListView`。

-   `RecyclerView` 基于轻松地自定义的模块化设计。 例如，您可以在不同的布局策略而无需大量代码更改插入到你的应用。
    与此相反，`ListView`是相对较整体结构中。

-   `RecyclerView` 包括内置的动画的项添加和删除。 `ListView` 动画付出应用开发人员部分一些额外的工作量。


### <a name="sections"></a>部分

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

本主题说明如何`Adapter`， `LayoutManager`，和`ViewHolder`一起作为帮助器类以支持工作`RecyclerView`。
它提供的每个这些帮助器类的高级概述，并解释了如何在应用中使用它们。

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[一个基本的 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

本主题中提供的信息生成[RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)通过提供真实代码示例说明了如何各种`RecyclerView`元素实现生成实际照片浏览应用程序。

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

本主题将额外的代码添加到示例应用程序中提供[基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)来演示如何处理项单击事件并更新`RecyclerView`当基础数据源发生更改。


### <a name="summary"></a>摘要

本指南引入 Android`RecyclerView`小组件; 它解释如何添加`RecyclerView`如何支持到 Xamarin.Android 项目中，库`RecyclerView`回收视图，如何它会强制实施的持有者的视图模式为提高效率，以及如何各种帮助程序类构成`RecyclerView`协作显示集合。 它提供示例代码，以演示如何`RecyclerView`集成到应用程序，它解释如何定制`RecyclerView`的布局策略通过插入不同的布局管理器和它所述如何处理项单击事件并通知`RecyclerView`的数据源更改。

有关详细信息`RecyclerView`，请参阅[RecyclerView 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)。


## <a name="related-links"></a>相关链接

- [RecyclerViewer （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [棒糖形简介](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
