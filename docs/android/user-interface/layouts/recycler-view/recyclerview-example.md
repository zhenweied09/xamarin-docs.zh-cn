---
title: "一个基本的 RecyclerView 示例"
ms.topic: article
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 44ebc8098250da26762538cddf5a89ffac709d8e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="a-basic-recyclerview-example"></a>一个基本的 RecyclerView 示例


若要了解如何`RecyclerView`在典型的应用程序中的工作原理，本主题将探讨[RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/)示例应用程序，使用一个简单代码示例`RecyclerView`要显示的照片的大型集合： 

[![使用 CardViews 来显示照片的 RecyclerView 应用的两个屏幕快照](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer**使用[卡片视图-](~/android/user-interface/controls/card-view.md)实施中的每个照片项`RecyclerView`布局。 由于`RecyclerView`的性能优势，此示例应用程序是能够顺畅地不会明显延迟快速滚动的照片的大型集合。


### <a name="an-example-data-source"></a>示例数据源

在此示例应用中，"相册"数据源 (由表示`PhotoAlbum`类) 提供`RecyclerView`项内容。
`PhotoAlbum` 是的照片具有标题; 集合当实例化它时，会得到现成 32 照片集合：

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

在每个照片实例`PhotoAlbum`显示属性，可用于读取其图像资源 ID， `PhotoID`，以及其标题字符串， `Caption`。 照片集合是如此组织可按照索引器访问每张照片。 例如，以下代码行访问的图像资源 ID 和集合中的第 10 个照片标题：

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` 此外提供了`RandomSwap`可以调用要交换第一张照片集合中与集合中其他位置的随机选择照片的方法：

```csharp
mPhotoAlbum.RandomSwap ();
```

因为的实现详细信息`PhotoAlbum`不了解相关`RecyclerView`、`PhotoAlbum`本文未介绍表示源代码。 源代码到`PhotoAlbum`位于[PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs)中[RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/)示例应用程序。


### <a name="layout-and-initialization"></a>布局和初始化

布局文件， **main.axml**，包含单个`RecyclerView`内`LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

请注意，你必须使用完全限定名称**android.support.v7.widget.RecyclerView**因为`RecyclerView`打包在支持库中。 `OnCreate`方法`MainActivity`初始化此布局、 实例化适配器，并准备基础数据源：

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Prepare the data source:
        mPhotoAlbum = new PhotoAlbum ();

        // Instantiate the adapter and pass in its data source:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);

        // Set our view from the "main" layout resource:
        SetContentView (Resource.Layout.Main);

        // Get our RecyclerView layout:
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug the adapter into the RecyclerView:
        mRecyclerView.SetAdapter (mAdapter);
```

此代码执行以下任务：

1. 实例化`PhotoAlbum`数据源。

2. 将照片唱片集数据源传递给构造函数的适配器， `PhotoAlbumAdapter` （在本指南后面定义）。 
   请注意它被视为最佳做法将数据源作为参数传递给构造函数的适配器。 

3. 获取`RecyclerView`从布局。

4. 插入到适配器`RecyclerView`实例通过调用`RecyclerView``SetAdapter`方法如上所示。

### <a name="layout-manager"></a>布局管理器

在每个项`RecyclerView`组成`CardView`包含照片图和照片标题 (中将详细介绍[视图持有者](#view-holder)下面一节)。 预定义`LinearLayoutManager`用于进行布局每`CardView`垂直滚动排列中：

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

此代码驻留在主活动`OnCreate`方法。 布局管理器的构造函数需要*上下文*，因此`MainActivity`使用传递`this`如上图所示。

而不是使用 predefind `LinearLayoutManager`，您可以插入自定义布局管理器中的显示两个`CardView`项端并行，实现遍历的照片集合 page-turning 动画效果。 稍后在本指南中，你将看到举例说明如何通过交换在不同的布局管理器修改布局。

<a name="view-holder" />

### <a name="view-holder"></a>视图持有者

视图持有者类称为`PhotoViewHolder`。 每个`PhotoViewHolder`实例将保留对引用`ImageView`和`TextView`的布局中的相关的行项`CardView`如此处上：

[![卡片视图-包含 ImageView 和 TextView 的图示](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` 派生自`RecyclerView.ViewHolder`和包含要存储到的引用属性`ImageView`和`TextView`上述布局中所示。
`PhotoViewHolder` 由两个属性和一个构造函数：

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```
在此代码示例中，`PhotoViewHolder`构造函数传递对父项视图的引用 ( `CardView`)，`PhotoViewHolder`包装。 请注意，你始终转发父项视图到基构造函数。 `PhotoViewHolder`构造函数调用`FindViewById`对父项视图，其子视图引用，每个`ImageView`和`TextView`，存储中的结果`Image`和`Caption`属性，分别。 适配器更高版本查看引用这些属性将时从检索它更新此`CardView`的子视图使用新数据。

有关详细信息`RecyclerView.ViewHolder`，请参阅[RecyclerView.ViewHolder 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。


### <a name="adapter"></a>适配器

适配器加载每个`RecyclerView`具有特定面部照片的数据行。 有关行位于给定面部照片*P*，例如，适配器定位位于关联的数据*P*行到此数据在位置中的数据源和副本项*P*中`RecyclerView`集合。 适配器使用视图持有者查找的引用`ImageView`和`TextView`位于该位置，因此无需重复调用该`FindViewById`的那些视图当用户滚动照片集合和重复使用视图。

在**RecyclerViewer**，一种适配器类派生自`RecyclerView.Adapter`创建`PhotoAlbumAdapter`:

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;

    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }
    ...
}
```

`mPhotoAlbum`成员包含数据源 （相册） 传递到构造函数; 构造函数将照片唱片集复制到此成员变量。 下列必需`RecyclerView.Adapter`方法来实现：

-   **`OnCreateViewHolder`** &ndash; 实例化项布局文件和视图持有者。

-   **`OnBindViewHolder`** &ndash; 将位于指定位置的数据加载到其引用存储在给定的视图持有者的视图。

-   **`ItemCount`** &ndash; 在数据源中返回的项数。

布局管理器调用这些方法，它定位中的项时`RecyclerView`。 下列部分中检查这些方法的实现。


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

布局管理器调用`OnCreateViewHolder`时`RecyclerView`需要新的视图所有者，以表示一项。 `OnCreateViewHolder` 放大视图的布局文件从项视图，并将包装在新视图`PhotoViewHolder`实例。 `PhotoViewHolder`构造函数查找，并将对子视图的引用存储布局中，如前面所述[视图持有者](#view-holder)。

每个行项都由`CardView`包含`ImageView`（对于照片） 和一个`TextView`（对于标题）。 此布局命名为驻留在文件**PhotoCardView.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:card_view="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <android.support.v7.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        card_view:cardElevation="4dp"
        card_view:cardUseCompatPadding="true"
        card_view:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Caption"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="4dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</FrameLayout>
```

此布局表示中的单个行项`RecyclerView`。 `OnBindViewHolder`方法 （如下所述） 将数据复制到数据源从`ImageView`和`TextView`的此布局。
`OnCreateViewHolder` 放大此布局中的给定的照片位置`RecyclerView`和实例化一个新`PhotoViewHolder`实例 (其中定位并缓存对引用`ImageView`和`TextView`中关联的子视图`CardView`布局):

```csharp
public override RecyclerView.ViewHolder
    OnCreateViewHolder (ViewGroup parent, int viewType)
{
    // Inflate the CardView for the photo:
    View itemView = LayoutInflater.From (parent.Context).
                Inflate (Resource.Layout.PhotoCardView, parent, false);

    // Create a ViewHolder to hold view references inside the CardView:
    PhotoViewHolder vh = new PhotoViewHolder (itemView);
    return vh;
}

```

生成的视图持有者实例， `vh`，返回到调用方 （布局管理器）。


#### <a name="onbindviewholder"></a>OnBindViewHolder

布局管理器时可以立即显示的特定视图中`RecyclerView`的屏幕可视区域中，它会调用适配器的`OnBindViewHolder`方法来填充指定的行位置中的数据源的内容的项。 `OnBindViewHolder` 获取指定的行位置 （照片的图像资源和照片的题注的字符串） 的照片信息并将此数据复制到关联的视图。 视图位于通过视图持有者对象中存储的引用 (这通过传入`holder`参数):

```csharp
public override void
    OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
{
    PhotoViewHolder vh = holder as PhotoViewHolder;

    // Load the photo image resource from the photo album:
    vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);

    // Load the photo caption from the photo album:
    vh.Caption.Text = mPhotoAlbum[position].Caption;
}
```

传入的视图持有者对象必须首先将转换为派生的视图持有者类型 (在这种情况下， `PhotoViewHolder`) 使用它之前。
适配器将图像资源加载到引用的视图持有者的视图`Image`属性，也将标题的文本复制到引用的视图持有者的视图`Caption`属性。 这*将绑定*与它的数据关联的视图。

请注意，`OnBindViewHolder`是直接处理的数据结构的代码。 在这种情况下，`OnBindViewHolder`理解如何将映射`RecyclerView`项中的数据源为其关联的数据项的位置。 映射非常简单在这种情况下因为位置可用作到相册; 数组索引但是，更复杂的数据源可能需要额外的代码来建立这样的映射。


#### <a name="itemcount"></a>ItemCount

`ItemCount`方法返回数据集合中项的数目。 在示例照片中的查看器应用程序项计数是相册中的照片数：

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

有关详细信息`RecyclerView.Adapter`，请参阅[RecyclerView.Adapter 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。


### <a name="putting-it-all-together"></a>汇总所有内容

生成`RecyclerView`示例照片应用程序实现组成`MainActivity`创建数据源、 布局管理器和适配器的代码。 `MainActivity` 创建`mRecyclerView`实例，实例化的数据源和适配器，并可插入的布局管理器和适配器：

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        mPhotoAlbum = new PhotoAlbum();
        SetContentView (Resource.Layout.Main);
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug in the linear layout manager:
        mLayoutManager = new LinearLayoutManager (this);
        mRecyclerView.SetLayoutManager (mLayoutManager);

        // Plug in my adapter:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
        mRecyclerView.SetAdapter (mAdapter);
    }
}

```

`PhotoViewHolder` 查找并缓存查看引用：

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

`PhotoAlbumAdapter` 实现三种所需的方法重写：

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;
    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }

    public override RecyclerView.ViewHolder
        OnCreateViewHolder (ViewGroup parent, int viewType)
    {
        View itemView = LayoutInflater.From (parent.Context).
                    Inflate (Resource.Layout.PhotoCardView, parent, false);
        PhotoViewHolder vh = new PhotoViewHolder (itemView);
        return vh;
    }

    public override void
        OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
    {
        PhotoViewHolder vh = holder as PhotoViewHolder;
        vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);
        vh.Caption.Text = mPhotoAlbum[position].Caption;
    }

    public override int ItemCount
    {
        get { return mPhotoAlbum.NumPhotos; }
    }
}
```

此代码在编译和运行，将创建基本的照片查看应用程序，如以下屏幕截图中所示：

[![查看使用垂直滚动照片卡应用程序的照片的两个屏幕快照](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

此基本应用程序仅支持浏览的照片唱片集。 它没有响应项触控事件，也不会处理基础数据中的更改。 此功能添加中[扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)。


### <a name="changing-the-layoutmanager"></a>更改 LayoutManager

由于`RecyclerView`的灵活性，很容易地修改应用程序以使用不同的布局管理器。 在下面的示例中，它将修改以显示照片唱片集具有的网格布局的水平滚动而不是具有垂直线性布局。 若要执行此操作，布局管理器实例化改为使用`GridLayoutManager`，如下所示：

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

此代码更改替换垂直`LinearLayoutManager`与`GridLayoutManager`这将显示在水平方向上滚动的两个行组成的网格。 当你编译并再次运行该应用时，你将看到在网格中显示照片和滚动是水平，而不是垂直：

[![使用水平滚动的网格中的照片应用程序的示例屏幕快照](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

通过更改只有一个代码行，可以修改照片查看应用不同的行为与使用不同的布局。
请注意，既不适配器代码，也不布局 XML 必须修改要更改布局样式。 

在下一步的主题中，[扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)，此基本示例应用程序扩展处理项单击事件和更新`RecyclerView`当基础数据源发生更改。



## <a name="related-links"></a>相关链接

- [RecyclerViewer （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
