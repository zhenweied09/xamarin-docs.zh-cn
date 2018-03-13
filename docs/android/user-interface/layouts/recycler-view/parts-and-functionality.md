---
title: "RecyclerView 部件和功能"
ms.topic: article
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 89679f7d825422ab34dd77b31a7a3fde60f36e99
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="recyclerview-parts-and-functionality"></a>RecyclerView 部件和功能


`RecyclerView` 某些任务内部 （如滚动和回收视图），但它的句柄是实质上是协调帮助程序类来显示集合的管理器。 `RecyclerView` 向以下帮助器类的委托任务：

-   **`Adapter`** &ndash; 放大项布局 （实例化布局文件的内容），并将数据绑定到在中显示的视图`RecyclerView`。 适配器还报告项单击事件。

-   **`LayoutManager`** &ndash; 测量并置于中的项视图`RecyclerView`和管理视图回收的策略。

-   **`ViewHolder`** &ndash; 查找并存储视图的引用。 视图持有者还有助于检测项视图单击。

-   **`ItemDecoration`** &ndash; 允许应用将特殊的绘制和布局偏移量添加到用于绘制项、 突出显示和可视化分组边界之间的分隔线的特定视图。

-   **`ItemAnimator`** &ndash; 定义动画项操作期间发生或进行更改时的适配器。

之间的关系`RecyclerView`， `LayoutManager`，和`Adapter`采用下图中描述了类：

![RecyclerView 包含 LayoutManager、 适配器用于访问数据集的关系图](parts-and-functionality-images/01-recyclerview-diagram.png)

此图所示，`LayoutManager`可视为之间的媒介`Adapter`和`RecyclerView`。 `LayoutManager`到调用`Adapter`方法代表`RecyclerView`。 例如，`LayoutManager`调用`Adapter`方法时创建的新视图中的特定项位置`RecyclerView`。 `Adapter`放大该项的布局，并创建`ViewHolder`对该位置处的视图的缓存引用 （未显示） 的实例。 当`LayoutManager`调用`Adapter`要绑定到数据集的特定项`Adapter`定位该选项的数据，检索的数据集中，并将其复制到关联的项目视图。

使用时`RecyclerView`在你的应用，创建派生的类型的以下类将需要：

-   **`RecyclerView.Adapter`** &ndash; 提供从你的应用的数据集 （这是特定于你的应用程序） 绑定到在中显示的项视图`RecyclerView`。 适配器知道如何将在每个项视图位置相关联`RecyclerView`到数据源中的特定位置。 此外，该适配器处理其中每个单个项视图的内容的布局，并创建每个视图的视图持有者。 适配器还报告检测到的项视图的项单击事件。

-   **`RecyclerView.ViewHolder`** &ndash; 缓存对项布局文件中的视图的引用，以便不会不必要地重复的资源查找。 查看项单击事件，以在用户点击视图人的关联的项视图时将其转发到适配器还排列视图持有者。

-   **`RecyclerView.LayoutManager`** &ndash; 定位中的项`RecyclerView`。 你可以使用几个预定义的布局管理器之一，或者可以实现你自己的自定义布局管理器。
    `RecyclerView` 委托布局策略应用到布局管理器，因此，您可以插入不同的布局管理器而无需进行重大更改为你的应用程序。

此外，你可以选择性地扩展以下类更改的外观和感觉`RecyclerView`应用程序中：

-   **`RecyclerView.ItemDecoration`**
-   **`RecyclerView.ItemAnimator`**

如果不扩展`ItemDecoration`和`ItemAnimator`， `RecyclerView` ，则使用默认实现。 本指南并不说明如何创建自定义`ItemDecoration`和`ItemAnimator`类; 有关这些类的详细信息，请参阅[RecyclerView.ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html)和[RecyclerView.ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).


<a name="recycling" />

### <a name="how-view-recycling-works"></a>如何查看回收工作原理

`RecyclerView` 不会为数据源中的每个项分配的项目视图。 相反，它分配只在屏幕上显示的项视图的数量，它会重用当用户滚动这些项布局。 当视图首先滚动不可见时，它将经历如下图中所示的回收过程：

[![图示视图回收的六个步骤](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1.  当视图滚动不可见，并且不会再显示时，它将变为*报废视图*。

2.  报废视图放在池中，并成为*回收视图*。
    此池是显示相同类型的数据的视图的缓存。

3.  当新项是要显示时，视图将使用池中的线程回收以供重复使用。 此视图必须重新绑定适配器在显示之前，因为它称为*脏视图*。

4.  已更新的视图是回收： 适配器定位要显示的下一项的数据，并将此数据复制到此项目的视图。 从与回收视图关联的视图持有检索这些视图的引用。

5.  回收的视图添加到中项的列表`RecyclerView`，将要转屏幕上。

6.  回收的视图转屏幕上作为用户滚动`RecyclerView`到列表中的下一项。 同时，另一个视图不可见中滚动，并回收根据上述步骤。

除了项视图重用`RecyclerView`还将使用另一个效率优化： 查看的持有者。 A*视图持有者*是缓存查看引用一个简单的类。 适配器放大项布局文件，每次它还将创建相应的视图持有者。 视图持有人使用`FindViewById`膨胀的项布局文件内获取对视图的引用。 这些引用用于将新数据加载到视图中，每次布局时回收以显示新的数据。
 


### <a name="the-layout-manager"></a>布局管理器

布局管理器负责定位中的项`RecyclerView`显示; 但由于它确定演示文稿类型 （列表或网格）、 （是否项都显示垂直或水平） 的方向，和应显示哪些方向项（在正常顺序或按相反的顺序）。 布局管理器程序还负责计算的大小和位置中每一项**RecycleView**显示。

布局管理器都有其他用途： 它确定何时回收不再对用户可见的项视图的策略。
由于布局管理器已注意到了哪些视图可见 （以及哪些不可用），则它处于要决定的视图可以回收的最佳位置。 若要回收视图，布局管理器通常会对适配器调用，以将回收视图的内容替换为不同的数据，如前面所述[视图回收的工作原理](#recycling)。

你可以扩展`RecyclerView.LayoutManager`若要创建您自己的布局管理器中，也可以使用预定义的布局管理器。 `RecyclerView` 提供了以下预定义的布局管理器：

-   **`LinearLayoutManager`** &ndash; 在可垂直滚动的列或可水平滚动的行中的项排列。

-   **`GridLayoutManager`** &ndash; 在网格中显示的项。

-   **`StaggeredGridLayoutManager`** &ndash; 显示在种交错式网格中，其中某些项具有不同的高度和宽度的项。

若要指定布局管理器，实例化你选的布局管理器，并将其传递到`SetLayoutManager`方法。 请注意你*必须*指定布局管理器&ndash;`RecyclerView`不会按默认选择预定义的布局管理器。

有关布局管理器的详细信息，请参阅[RecyclerView.LayoutManager 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html)。


### <a name="the-view-holder"></a>视图持有者

视图持有者是为缓存查看引用定义的类。 适配器使用这些视图引用绑定到其内容的每个视图。 中的所有项`RecyclerView`具有关联的视图持有者实例进行缓存查看该项的引用。 若要创建视图持有者，请使用以下步骤来定义一个类来保存每个项的视图的准确集合：

1.  子类`RecyclerView.ViewHolder`。
2.  实现查找，并将存储的视图引用的构造函数。
3.  实现该适配器可用于访问这些引用的属性。

详细的示例`ViewHolder`中提供实现[基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)。
有关详细信息`RecyclerView.ViewHolder`，请参阅[RecyclerView.ViewHolder 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。


### <a name="the-adapter"></a>适配器

大部分"繁重任务"的`RecyclerView`适配器中发生的集成代码。 `RecyclerView` 需要您提供派生自的适配器`RecyclerView.Adapter`访问您的数据源并填充数据源中的内容的每个项。
由于数据源是特定于应用程序，则必须实现适配器知道如何访问您的数据的功能。 适配器从数据源提取信息并将其加载到每个项`RecyclerView`集合。

如下图所示适配器如何映射到单个视图中每个行项中的数据源视图持有人通过中的内容`RecyclerView`:

[![图示适配器连接到 ViewHolders 的数据源](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

适配器加载每个`RecyclerView`具有特定行项的数据行。 有关行位置*P*，例如，适配器定位位于关联的数据*P*行到此数据在位置中的数据源和副本项*P*中`RecyclerView`集合。
在上面的绘图中，例如，适配器使用视图持有者查找的引用`ImageView`和`TextView`位于该位置，因此无需重复调用该`FindViewById`这些视图以用户为滚动集合和重复使用视图。

当实现适配器时，你必须重写以下`RecyclerView.Adapter`方法：

-   **`OnCreateViewHolder`** &ndash; 实例化项布局文件和视图持有者。

-   **`OnBindViewHolder`** &ndash; 将位于指定位置的数据加载到其引用存储在给定的视图持有者的视图。

-   **`ItemCount`** &ndash; 在数据源中返回的项数。

布局管理器调用这些方法，它定位中的项时`RecyclerView`。 



### <a name="notifying-recyclerview-of-data-changes"></a>通知 RecyclerView 的数据更改

`RecyclerView` 时，不自动更新其显示其数据的内容源发生更改;适配器必须通知`RecyclerView`在数据集中的更改时。 数据集可能会更改在许多方面;例如，可以更改在某个项的内容或数据的整体结构可能会更改。
`RecyclerView.Adapter` 提供大量可以调用的方法，以便`RecyclerView`响应数据更改中的最有效方式：

-  **`NotifyItemChanged`** &ndash; 位于指定位置处的项已更改的通知。

-  **`NotifyItemRangeChanged`** &ndash; 将指定范围中的位置的项已更改的通知。

-  **`NotifyItemInserted`** &ndash; 中的指定位置的项已插入新的通知。

-  **`NotifyItemRangeInserted`** &ndash; 指定范围中的位置的项已插入新的通知。

-  **`NotifyItemRemoved`** &ndash; 用信号通知已移除指定位置中的项。

-  **`NotifyItemRangeRemoved`** &ndash; 将指定范围中的位置的项目均已删除的信号。

-  **`NotifyDataSetChanged`** &ndash; 数据集已更改的信号 （强制执行完整的更新）。

如果你知道如何数据集已更改的确切，你可以调用适当的方法更高版本以刷新`RecyclerView`以最有效方式。 如果你不知道如何数据集已更改的确切，则可以调用`NotifyDataSetChanged`，这是最低效因为`RecyclerView`必须刷新是对用户可见的所有视图。 有关这些方法的详细信息，请参阅[RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。

在下一步的主题中，[基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)，实现示例应用程序是为了演示的部件和上面所述的功能的真实代码示例。


## <a name="related-links"></a>相关链接

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [一个基本的 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
