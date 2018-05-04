---
title: 在 Xamarin.Android 中使用 RelativeLayout
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: cd2d7537036978e30c97b5776155e429178b6dac
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) 是[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)显示子[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)相对位置中的元素。 位置[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)可以指定相对于同级元素 （如有关左侧的或给定元素的下方） 或在位置相对于[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)区域 （如对齐到下、 左的中心）。

A [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)是一个非常强大的工具，用于设计用户界面，因为它可以消除嵌套[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)s。 如果你发现自己使用几个嵌套[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)组，你可能能够将其替换为单个[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)。

启动一个名为的新项目**HelloRelativeLayout**。

打开**Resources/Layout/Main.axml**文件中，插入以下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="@android:drawable/editbox_background"
        android:layout_below="@id/label"/>
    <Button
        android:id="@+id/ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/entry"
        android:layout_alignParentRight="true"
        android:layout_marginLeft="10dip"
        android:text="OK" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toLeftOf="@id/ok"
        android:layout_alignTop="@id/ok"
        android:text="Cancel" />
</RelativeLayout>
```

请注意每个`android:layout_*`特性，例如`layout_below`， `layout_alignParentRight`，和`layout_toLeftOf`。
使用时[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)，你可以使用这些属性来描述你想要将每个定位[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)。 这些属性中的每个定义一种不同的相对位置。 某些特性，请使用同级的资源 ID [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)来定义其自己的相对位置。 例如，最后一个[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)被定义为位于左侧的和对齐-使用--top-的[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)由 ID 标识`ok`(即以前[`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)).

在中定义的所有可用的布局属性[ `RelativeLayout.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)。

请确保加载中的此布局[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)方法：

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/p/System.Int32/)方法加载的布局文件[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)，按资源 ID 指定&mdash;`Resource.Layout.Main`指**资源/布局 /Main.axml**布局文件。

运行该应用程序。 你应看到以下的布局：

[![TextView、 EditText，与两个按钮的相对布局的屏幕截图](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)


## <a name="resources"></a>资源

-   [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
-   [`RelativeLayout.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
-   [`EditText`](https://developer.xamarin.com/api/type/Android.Widget.EditText/)
-   [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)


*此页的部分是基于工作创建和共享的 Android Open Source Project 的根据条款中所述修改*
[*Creative Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/).
