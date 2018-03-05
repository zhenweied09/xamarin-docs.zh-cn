---
title: ViewPager
description: "ViewPager 是使您可以实现动作导航布局管理器。 动作导航允许用户轻扫，左侧和右侧逐句通过数据页。 本指南说明如何实现动作导航 ViewPager，与使用和不使用片段。 它还描述如何添加使用 PagerTitleStrip 和 PagerTabStrip 页指示器。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 80ba525b87d2008f290e32fde56265630bac729a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="viewpager"></a>ViewPager

_ViewPager 是使您可以实现动作导航布局管理器。动作导航允许用户轻扫，左侧和右侧逐句通过数据页。本指南说明如何实现动作导航 ViewPager，与使用和不使用片段。它还描述如何添加使用 PagerTitleStrip 和 PagerTabStrip 页指示器。_

<a name="overview" />
 
## <a name="overview"></a>概述

应用程序开发中的常见情况是，需要为用户提供具有同级视图之间的动作导航。 在此方法中，用户刷左或向右对访问页面 （例如，在安装向导或幻灯片放映） 的内容。 你可以通过创建这些轻扫视图`ViewPager`小组件中，位于[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。 `ViewPager`是布局小组件的多个子视图组成，其中每个子视图构成布局中的页： 

[![使用水平轻扫示例屏幕快照的 TreePager 应用程序](images/01-intro-sml.png)](images/01-intro.png)

通常情况下，`ViewPager`结合使用[片段](https://developer.xamarin.com/guides/android/platform_features/fragments/); 但是，某些情况下，你可能想要使用`ViewPager`而无需提高的复杂性`Fragment`s。

`ViewPager` 使用适配器模式来提供要显示的视图。 此处使用的适配器不从概念上讲类似于使用[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash;提供的实现`PagerAdapter`生成页，`ViewPager`向用户显示。 显示的页`ViewPager`可以是`View`s 或`Fragment`s。 当`View`显示，适配器子类 Android 的`PagerAdapter`基类。 如果`Fragment`显示，适配器子类 Android 的`FragmentPagerAdapter`。 Android 支持库还包括`FragmentPagerAdapter`(的一个子类`PagerAdapter`) 来帮助进行连接的详细信息`Fragment`的数据。 

本指南演示了这两种方法： 

-   在[与视图 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-views.md)、 [TreePager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/)开发出应用来演示如何使用`ViewPager`若要显示的树目录 （落叶和长期有效树的图像库） 的视图。 
    `PagerTabStrip`  和`PagerTitleStrip`用于显示标题，可帮助完成页面导航。

-   在[使用片段 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md)，略显复杂[FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/)开发出应用来演示如何使用`ViewPager`与`Fragment`以生成显示为数学问题的应用程序flash 卡，并响应用户输入。 

<a name="requirements" />

## <a name="requirements"></a>惠?

若要使用`ViewPager`在应用程序项目中，你必须安装[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)包。 有关安装 NuGet 包的详细信息，请参阅[演练： 在你的项目包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。 

<a name="architecture" />
 
## <a name="architecture"></a>体系结构

用于三个组件实现动作导航与`ViewPager`:

-   ViewPager
-   适配器
-   页导航指示器

下面概述了每个组件。


<a name="viewpager" />

### <a name="viewpager"></a>ViewPager

`ViewPager` 是布局管理器，显示的集合`View`s 一次。 其作业是检测用户的滑动手势并导航到适当的下一步或上一个视图。 例如，下面的屏幕截图演示了`ViewPager`以响应用户手势进行到下一个图像中的转换： 

[![显示视图之间的转换的特写的 TreePager 应用](images/02-transition-sml.png)](images/02-transition.png)


<a name="adapter" />

### <a name="adapter"></a>适配器

`ViewPager` 将其数据从拉*适配器*。 适配器的作业是创建`View`s 显示`ViewPager`，根据需要为他们提供。 下图阐释了这一概念&ndash;适配器创建和填充`View`s 并向用户提供到`ViewPager`。 作为`ViewPager`检测到用户的轻扫笔势，系统会要求提供相应的适配器`View`以显示： 

[![说明如何适配器连接图像和名称到 ViewPager 的关系图](images/03-adapter-sml.png)](images/03-adapter.png)

在此特定示例中，每个`View`之前传递给构造的树图像和树名称从`ViewPager`。 


<a name="indicator" />

### <a name="pager-indicator"></a>页导航指示器

`ViewPager` 可用于显示大型数据集 （例如，图像库可能包含数百个映像）。 若要帮助用户导航大型数据集，`ViewPager`通常伴随*页导航指示器*显示的字符串。 字符串可能映像标题、 标题或只需在数据集内的当前视图的位置。 

有两个视图可以生成此导航信息，供你：`PagerTabStrip`和`PagerTitleStrip.`每个顶部显示的字符串`ViewPager`，并每个将从其数据拉`ViewPager`的适配器，以便它始终保持与同步当前显示`View`。 它们之间的区别在于`PagerTabStrip`包括"当前"字符串时的可视指示器`PagerTitleStrip`未不 （如这些屏幕截图中所示）： 

[![使用 PagerTitleStrip 和 PagerTabStrip TreePager 应用的屏幕快照](images/04-comparison-sml.png)](images/04-comparison.png)

本指南演示如何 immplement `ViewPager`，指示器应用程序组件和适配器，并将它们以支持动作导航集成。 



## <a name="related-links"></a>相关链接

- [TreePager （示例）](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
- [FlashCardPager (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
