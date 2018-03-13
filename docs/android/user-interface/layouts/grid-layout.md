---
title: GridLayout
ms.topic: article
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: a8a9735845139da700959caf3639defa6594f307
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="gridlayout"></a>GridLayout

`GridLayout`是一种新`ViewGroup`子类支持布局中二维网格，类似于 HTML 表中，视图如下所示：

 [![裁剪 GridLayout 显示四个单元格](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` 适用于平面视图层次结构，其中子视图设置其位置在网格中通过指定的行和列应采用。 这样一来， *GridLayout*能够定位在网格中的视图，而无需任何中间视图提供的表结构，如在 TableLayout 中使用的表行所示。 通过维护平面的层次结构， *GridLayout*能够将更迅速布局其子视图。 让我们看看示例来演示这一概念的实际含义在代码中。


## <a name="creating-a-grid-layout"></a>创建网格布局

下面的 XML 添加了多种`TextView`控件添加到*GridLayout*。

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip" />
</GridLayout>
```

以便单元格可以容纳其内容，如下图所示布局会调整行和列大小：

 [![在左侧小于右侧显示两个单元格的布局的图示](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

这会导致应用程序中运行时的以下用户界面：

 [![显示四个单元格的屏幕截图的 GridLayoutDemo 应用](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)



## <a name="specifying-orientation"></a>指定方向

请注意，在 XML 更高版本，每个`TextView`未指定的行或列。 如果这些未指定，`GridLayout`分配顺序情况下，基于方向每个子视图。 例如，让我们将更改 GridLayout 的方向从默认情况下，水平、 为垂直如下：

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

现在，`GridLayout`将定位从顶部到底部的每个列，而不是从左到右中的单元格，如下所示：

 [![说明如何单元格都将放置在垂直方向的关系图](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

这会导致在运行时的以下用户界面：

 [![与单元格将放置在垂直方向排列的 GridLayoutDemo 屏幕快照](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)



### <a name="specifying-explicit-position"></a>指定显式位置

如果我们想要显式控制的子视图中的位置`GridLayout`，我们可以设置其`layout_row`和`layout_column`属性。 例如，下面的 XML 将导致第一个屏幕截图所示 （如上所示），而不考虑方向的布局。

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="1" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="1"  />
</GridLayout>
```



### <a name="specifying-spacing"></a>指定的间距

我们有几个选项可将提供子之间的间距的视图`GridLayout`。 我们可以使用`layout_margin`边距上设置每个子视图直接，如下所示的属性

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

此外，在 Android 4 中，新的通用间距视图调用`Space`现已可用。 若要使用它，只需将其添加为父子视图中。
例如，下面的 XML 将添加到一个附加行`GridLayout`通过设置其`rowcount`为 3，并将添加`Space`提供之间的间距的视图`TextViews`。

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="3"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"         
            android:layout_height="50dp" />    
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="1" />
</GridLayout>
```

此 XML 创建中的间距`GridLayout`如下所示：

 [![演示具有间距更大的单元格 GridLayoutDemo 的屏幕截图](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

使用新的好处`Space`视图是它允许间距，并且不需要我们可以对每个子视图设置属性。



### <a name="spanning-columns-and-rows"></a>跨越行和列

`GridLayout`还支持跨多个列和行的单元格。 例如，假设我们添加另一行包含到按钮`GridLayout`如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="4"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"        
            android:layout_height="50dp" />   
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"        
            android:layout_row="2"        
            android:layout_column="1" />
     <Button
            android:id="@+id/myButton"
            android:text="@string/hello"        
            android:layout_row="3"
            android:layout_column="0" />
</GridLayout>
```

这将导致的第一列`GridLayout`正在拉伸以适应大小此按钮时，正如我们在这里看到：

[![屏幕快照的 GridLayoutDemo 与跨越仅第一列的按钮](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

若要防止拉伸的第一列，我们可以设置按钮以通过设置其 columnspan 如下跨两个列：

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

执行此操作导致的布局`TextViews`类似于我们更早版本，在添加到底部的按钮的布局`GridLayout`如下所示：

 [![屏幕快照的 GridLayoutDemo 与跨这两个列的按钮](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)


## <a name="related-links"></a>相关链接

- [GridLayoutDemo (sample)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [引入冰激凌德桑威奇](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
