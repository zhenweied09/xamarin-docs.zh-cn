---
title: GridView
ms.topic: article
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 2fb3133833dbaa0b174c4611d204f6c8ceb42a2b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="gridview"></a>GridView

[`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) 是[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)的二维，可滚动的网格中显示项。 网格项自动插入到布局使用[ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)。

在本教程中，你将创建缩略图的网格。 选择项目时的 toast 消息将显示图像的位置。

启动一个名为的新项目**HelloGridView**。

查找你想要使用，某些照片或[下载这些示例图片](http://developer.android.com/shareables/sample_images.zip)。 将图像文件添加到项目的**资源/Drawable**目录。 在**属性**窗口中，为每个设置生成操作**AndroidResource**。

打开**Resources/Layout/Main.axml**文件中，插入以下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gridview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:columnWidth="90dp"
    android:numColumns="auto_fit"
    android:verticalSpacing="10dp"
    android:horizontalSpacing="10dp"
    android:stretchMode="columnWidth"
    android:gravity="center"
/>
```

这[ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/)将填充整个屏幕。 而是可以自我说明包含以下属性。 有关有效的属性的详细信息，请参阅[ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/)引用。

打开`HelloGridView.cs`并插入以下代码[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    var gridview = FindViewById<GridView> (Resource.Id.gridview);
    gridview.Adapter = new ImageAdapter (this);

    gridview.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

后**main.axml**布局设置对于内容视图， [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/)从带布局捕获[ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/)。 [ `Adapter` ](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)然后使用属性来设置自定义适配器 (`ImageAdapter`) 作为要显示在网格中所有项目的源。 `ImageAdapter`在下一步中创建。

若要在单击网格中的项时执行某些操作，匿名委托订阅[ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)事件。
它显示[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)显示选定项的索引位置 （从零开始） （在实际方案中，位置可以使用来获取的某个其他任务的完整大小的映像）。 请注意 Java 样式侦听器类，可以使用而不是.NET 事件。

创建新的类称为`ImageAdapter`该子类[ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
        context = c;
    }

    public override int Count {
        get { return thumbIds.Length; }
    }

    public override Java.Lang.Object GetItem (int position)
    {
        return null;
    }

    public override long GetItemId (int position)
    {
        return 0;
    }

    // create a new ImageView for each item referenced by the Adapter
    public override View GetView (int position, View convertView, ViewGroup parent)
    {
        ImageView imageView;

        if (convertView == null) {  // if it's not recycled, initialize some attributes
            imageView = new ImageView (context);
            imageView.LayoutParameters = new GridView.LayoutParams (85, 85);
            imageView.SetScaleType (ImageView.ScaleType.CenterCrop);
            imageView.SetPadding (8, 8, 8, 8);
        } else {
            imageView = (ImageView)convertView;
        }

        imageView.SetImageResource (thumbIds[position]);
        return imageView;
    }

    // references to our images
    int[] thumbIds = {
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7
    };
}
```

首先，这将会实现继承自某些所需的方法[ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)。 构造函数和[ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/)属性都一目了然。 通常情况下， [ `GetItem(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/)应返回在适配器中的指定位置处的实际对象，但它对于此示例中，将忽略。 同样， [ `GetItemId(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/)应返回行 id 的项，但此处不需要它。

所需的第一个方法是[ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/)。
此方法创建一个新[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)为添加到每个图像`ImageAdapter`。 此调用时， [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)传递在中，这是正常情况下回收的对象 (至少一次具有已调用后)，因此会检查以查看该对象是否为 null。 如果它*是*null， [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)实例化并配置了映像演示文稿的所需属性：

- [`LayoutParams`](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) 高度和宽度设置为视图&mdash;这可确保，无论可绘制的大小，每个图像进行大小调整和剪裁以适合根据这些尺寸。

- [`SetScaleType()`](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetScaleType/) 声明，应修剪向中心的映像 （如果有必要）。

- [`SetPadding(int, int, int, int)`](https://developer.xamarin.com/api/member/Android.Views.View.SetPadding/) 定义每侧的填充。 （请注意，是否映像都具有不同的纵横比，然后更少填充将会导致多个裁剪图像的如果与提供给 ImageView 维度不匹配。）

如果[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)传递给[ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/)是*不*为 null，则本地[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)使用初始化回收[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)对象。

在结束[ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/)方法，`position`整数值传递到方法用于选择从一个映像`thumbIds`数组，它被设置为图像资源[ `ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).

剩下的就是定义`thumbIds`的可绘制的资源数组。

运行该应用程序。 网格布局应如下所示：

[![示例屏幕快照显示 15 图像的 GridView](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

尝试试验的行为[ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/)和[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)通过调整它们的属性的元素。 例如，而不是使用[ `LayoutParams` ](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/)请尝试使用[ `SetAdjustViewBounds()` ](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetAdjustViewBounds/)。


## <a name="references"></a>参考资料

-   [`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) 
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)。

*此页的部分是基于工作创建和共享的 Android Open Source Project 的根据条款中所述修改*
[*Creative Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/).
