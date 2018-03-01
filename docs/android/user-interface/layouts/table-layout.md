---
title: TableLayout
ms.topic: article
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 80147de0f639b4b597e11b41a6854f550edd61a9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="tablelayout"></a>TableLayout

[`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) 是[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)显示子[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)行和列中的元素。

启动一个名为的新项目**HelloTableLayout**。

打开**Resources/Layout/Main.axml**文件中，插入以下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:stretchColumns="1">

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Open..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-O"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save As..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-Shift-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Import..."
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Export..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-E"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Quit"
            android:padding="3dip"/>
    </TableRow>
</TableLayout>
```

请注意如何，这类似于 HTML 表的结构。 [ `TableLayout` ](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/)元素是类似于 HTML`<table>`元素;[ `TableRow` ](https://developer.xamarin.com/api/type/Android.Widget.TableRow/)类似`<tr>`元素; 但对于单元格，你可以使用任何类型的[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)元素。 在此示例中， [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/)用于每个单元格。 之间的一些行，还有一个基本[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)，用于绘制一条横线。

请确保你**HelloTableLayout**活动加载中的此布局[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)方法：

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32))方法加载的布局文件[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)，按资源 ID 指定&mdash;`Resource.Layout.Main`指**资源/布局 /Main.axml**布局文件。

运行该应用程序。 你应看到以下信息：

[![显示多个表行的 TableLayout 应用的示例屏幕快照](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png)


<a name="References" />

## <a name="references"></a>参考资料

-   [`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) 

-   [`TableRow`](https://developer.xamarin.com/api/type/Android.Widget.TableRow/) 

-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*此页的部分是基于工作创建和共享的 Android Open Source Project 的根据条款中所述修改*
[*Creative Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/).
