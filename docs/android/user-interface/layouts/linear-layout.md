---
title: LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 417593a32ce99393bddeb912833fdd02e4439026
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="linearlayout"></a>LinearLayout

[`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 是[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)显示子[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)元素按线性方向垂直或水平。

你应谨慎过度使用[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)。
如果你开始嵌套多个[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s，你可能需要考虑使用[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)相反。

启动一个名为的新项目**HelloLinearLayout**。

打开**Resources/Layout/Main.axml**和插入以下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "fill_parent"
    android:layout_height=    "fill_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "fill_parent"
      android:layout_height=    "fill_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "fill_parent"
    android:layout_height=    "fill_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

请仔细检查此 XML。 没有根[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)定义自己的方向为垂直&ndash;所有子[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)s （它具有两个） 将堆积垂直。 第一个子级是另一种[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)使用水平方向和第二个子级是[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)使用垂直方向。 其中每个嵌套[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s 包含几个[ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/)元素，用于定义其父级的方式与每个其他面向[ `LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).

现在打开**HelloLinearLayout.cs**并确保它加载**Resources/Layout/Main.axml**中的布局[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)方法：

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32))方法加载的布局文件[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)，按资源 ID 指定&ndash;`Resources.Layout.Main`指**资源/布局 /Main.axml**布局文件。

运行该应用程序。 你应看到以下信息：

[![屏幕快照的第一个 LinearLayout 水平排列的应用程序中，第二个垂直](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

请注意如何在 XML 特性定义每个视图的行为。 尝试试验不同的值`android:layout_weight`若要查看如何分发屏幕的实际空间基于每个元素的权重。 请参阅[公共布局对象](http://developer.android.com/guide/topics/ui/declaring-layout.html)文档详细了解[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)句柄`android:layout_weight`属性。


## <a name="references"></a>参考资料

-   [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*此页的部分是基于工作创建和共享的 Android Open Source Project 的根据条款中所述修改*
[*Creative Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/).

