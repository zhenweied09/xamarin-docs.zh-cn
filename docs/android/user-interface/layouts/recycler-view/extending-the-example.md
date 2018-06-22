---
title: 扩展 RecyclerView 示例
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 83147261a2d5458272f7e2bc105154da4308f4b0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769258"
---
# <a name="extending-the-recyclerview-example"></a>扩展 RecyclerView 示例


基本应用程序中所述[基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)实际上并未执行其他操作&ndash;它只需滚动，并显示照片项，以便浏览的固定的列表。 在实际应用程序，用户希望能够通过点击中显示的项与应用程序进行交互。 此外，可以更改基础数据源 （或应用程序通过更改），以及显示的内容必须保持与这些更改相一致。 在以下部分中，你将了解如何处理项单击事件和更新`RecyclerView`当基础数据源发生更改。


### <a name="handling-item-click-events"></a>处理项单击事件

当用户接触中的项`RecyclerView`，生成项的单击事件以通知应用程序对于哪些接触到项。 此事件不由生成`RecyclerView`&ndash;相反，项视图 （它包装在视图持有者） 检测到收尾工作，并报告这些收尾工作，如 click 事件。

为了说明如何处理项单击事件，下列步骤说明如何将基本照片查看应用程序修改为哪些照片具有已接触的用户的报表。 项的单击事件发生时的示例应用中，按以下顺序发生：

1.  照片的`CardView`检测到的项 click 事件，并通知适配器。

2.  适配器将 （具有项位置信息） 的事件转发到活动的项的单击处理程序。

3.  对项的单击事件的响应活动的项的单击处理程序。

首先，调用事件处理程序成员`ItemClick`添加到`PhotoAlbumAdapter`类定义：

```csharp
public event EventHandler<int> ItemClick;
```

接下来，将项单击事件处理程序方法添加到`MainActivity`。
此处理程序会显示简短，该值指示哪个照片项已接触 toast:

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

接下来，需要的代码行来注册`OnItemClick`处理程序替换`PhotoAlbumAdapter`。 执行此操作的好时机是在后立即`PhotoAlbumAdapter`创建 (在主活动`OnCreate`方法):

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

`PhotoAlbumAdapter` 现在将调用`OnItemClick`当它收到项 click 事件。 下一步是创建处理程序中引发这适配器`ItemClick`事件。 下面的方法， `OnClick`，适配器的后立即添加`ItemCount`方法：

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

这`OnClick`方法是适配器的*侦听器*从项视图的项的单击事件。 可以与项的视图 （通过项视图的视图持有者），注册此侦听器之前`PhotoViewHolder`构造函数必须修改以接受作为其他参数，此方法并注册`OnClick`与项视图`Click`事件。
下面是已修改`PhotoViewHolder`构造函数：

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

`itemView`参数包含对引用`CardView`，已由用户接触。 请注意视图持有者基类知道项的布局位置 (`CardView`)，它表示 (通过`LayoutPosition`属性)，并且此位置传递给适配器的`OnClick`发生某项的单击事件的方法。 适配器的`OnCreateViewHolder`方法修改传递适配器的`OnClick`视图人的构造函数的方法：

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

现在当生成和运行示例照片查看应用，点击在显示照片将导致显示报表的照片已接触 toast:

[![点击照片卡时，将显示的示例 Toast](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

此示例演示用于实现事件处理程序替换的只是一种方法`RecyclerView`。 无法在此处使用的另一种方法是将事件放在视图持有者并让订阅这些事件的适配器。 如果示例照片应用程序提供编辑功能的照片，单独的事件所需`ImageView`和`TextView`在每个`CardView`： 涉及`TextView`将会启动`EditView`使用户可以编辑的对话框标题和上的收尾工作`ImageView`将会启动允许用户裁剪或旋转照片的照片修饰工具。 根据你的应用程序的需求，则必须设计用于处理和响应触控事件的最佳做法。

为了演示如何`RecyclerView`可以修改数据集发生更改，示例照片查看应用程序随机选取数据源中的照片和换用第一张照片时可以更新。 首先，**随机选取**按钮添加到此示例照片应用**main.axml**布局：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/randPickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="Random Pick" />
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

接下来，在主活动的末尾添加代码`OnCreate`方法来查找`Random Pick`布局按钮，然后将处理程序附加到它：

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        // Randomly swap a photo with the first photo:
        int idx = mPhotoAlbum.RandomSwap();
    }
};

```

此处理程序调用照片唱片集的`RandomSwap`方法时**随机选取**点击按钮。 `RandomSwap`方法随机交换与数据源中的第一个照片的照片，然后返回随机交换照片的索引。 在编译和运行示例应用使用此代码时，点击**随机选取**因为，按钮不会导致显示更改`RecyclerView`不知道的对数据源的更改。

若要保留`RecyclerView`更新后的数据源的更改，**随机选取**单击处理程序必须修改以调用适配器的`NotifyItemChanged`已更改集合中每个项的方法 （在这种情况下，两个项都具有更改： 第一张照片和交换的照片)。 这将导致`RecyclerView`更新，以便它与数据源的新状态一致的显示：

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        int idx = mPhotoAlbum.RandomSwap();

        // First photo has changed:
        mAdapter.NotifyItemChanged(0);

        // Swapped photo has changed:
        mAdapter.NotifyItemChanged(idx);
    }
};

```

现在，当**随机选取**点击按钮，`RecyclerView`更新显示器以显示，照片进一步向下集合中已换用与集合中第一张照片：

[![交换之后, 的第二个屏幕快照前的第一个屏幕截图](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

当然，`NotifyDataSetChanged`无法调用而不是进行两次调用`NotifyItemChanged`，但这样做因此将强制`RecyclerView`刷新整个集合，即使更改了集合中的只有两个项。 调用`NotifyItemChanged`显著比调用效率更高`NotifyDataSetChanged`。


## <a name="related-links"></a>相关链接

- [RecyclerViewer （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [一个基本的 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
