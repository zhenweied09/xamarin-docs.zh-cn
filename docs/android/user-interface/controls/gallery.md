---
title: "库"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: a5315e0f55952872761308bd4cfc1d678585b6bc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="gallery"></a>库

[`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/) 是用于显示水平滚动列表中的项的布局小组件，并在视图中心置于当前所选内容。

> [!IMPORTANT]
> Android 4.1 （API 级别 16） 中已弃用此小组件。 

在本教程中，将创建的照片库，然后显示的 toast 消息选择库项每个时间。

后`Main.axml`布局设置对于内容视图，`Gallery`从带布局捕获[ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/)。
[ `Adapter` ](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)然后使用属性来设置自定义适配器 ( `ImageAdapter`) 作为要在 dallery 中显示的所有项的源。 `ImageAdapter`在下一步中创建。

若要在单击库中的项时执行某些操作，匿名委托订阅[ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)事件。 它显示[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)显示选项的索引位置 （从零开始） （在实际方案中，位置可以使用来获取的某个其他任务的完整大小的映像）。

首先，有几个成员变量，包括一个引用图像保存在可绘制 resources 目录中的 Id 数组 (**可资源/绘制**)。

接下来是类构造函数，其中[ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/)为`ImageAdapter`定义实例并将其保存到本地字段。
接下来，这将会实现继承自某些所需的方法[ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)。
构造函数和[ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/)属性都一目了然。 通常情况下， [ `GetItem(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/p/System.Int32/)应返回在适配器中的指定位置处的实际对象，但它对于此示例中，将忽略。 同样， [ `GetItemId(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/p/System.Int32/)应返回行 id 的项，但此处不需要它。

方法执行的工作，以通过应用到映像[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) ，将嵌入中[ `Gallery` ](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)在此方法中，成员[ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/)是用于创建一个新[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)。
[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)准备将映像应用于可绘制的资源，设置本地数组[ `Gallery.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.Gallery+LayoutParams/)高度和宽度的映像，设置缩放以适合[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)维度，然后再最后设置背景以使用 styleable 构造函数中获取的属性。

请参阅[ `ImageView.ScaleType` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView+ScaleType/)其他图像缩放选项的。

## <a name="walkthrough"></a>演练

启动一个名为的新项目*HelloGallery*。

![新的 Android 项目，在新的解决方案对话框的屏幕截图](gallery-images/hellogallery1.png)

查找你想要使用，某些照片或[下载这些示例图片](http://developer.android.com/shareables/sample_images.zip)。
将图像文件添加到项目的**资源/Drawable**目录。 在**属性**窗口中，为每个设置生成操作**AndroidResource**。

打开**Resources/Layout/Main.axml**和插入以下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

打开`MainActivity.cs`并插入以下代码[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    Gallery gallery = (Gallery) FindViewById<Gallery>(Resource.Id.gallery);

    gallery.Adapter = new ImageAdapter (this);

    gallery.ItemClick += delegate (object sender, Android.Widget.AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

创建新的类称为`ImageAdapter`该子类[ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
          context = c;
    }

    public override int Count { get { return thumbIds.Length; } }

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
          ImageView i = new ImageView (context);

          i.SetImageResource (thumbIds[position]);
          i.LayoutParameters = new Gallery.LayoutParams (150, 100);
          i.SetScaleType (ImageView.ScaleType.FitXy);

          return i;
    }

    // references to our images
    int[] thumbIds = {
            Resource.Drawable.sample_1,
            Resource.Drawable.sample_2,
            Resource.Drawable.sample_3,
            Resource.Drawable.sample_4,
            Resource.Drawable.sample_5,
            Resource.Drawable.sample_6,
            Resource.Drawable.sample_7
     };
}

```

运行该应用程序。 其外观应类似下面的屏幕截图：

![显示示例图像 HelloGallery 的屏幕截图](gallery-images/hellogallery3.png)


<a name="References" />

## <a name="references"></a>参考资料

-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
-   [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)

*此页的部分是基于工作创建和共享的 Android Open Source Project 的根据条款中所述修改*
[*Creative Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/).


