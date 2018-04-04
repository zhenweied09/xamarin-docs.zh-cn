---
title: 填充数据的 ListView
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: 83b398faf4fd634b7d492d372524b5fdd00163d0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="populating-a-listview-with-data"></a>填充数据的 ListView


## <a name="overview"></a>概述

若要将行添加到`ListView`你需要将其添加到你的布局和实现`IListAdapter`使用的方法，`ListView`调用来填充本身。 Android 包括内置`ListActivity`和`ArrayAdapter`不定义任何自定义布局 XML 或代码，你使用的类。 `ListActivity`类自动创建`ListView`并公开`ListAdapter`属性来提供要显示通过适配器的行视图。

内置适配器将作为一个参数，获取用于每个行视图资源 ID。 你可以使用内置的资源，如`Android.Resource.Layout`使你无需编写你自己。


## <a name="using-listactivity-and-arrayadapterltstringgt"></a>使用 ListActivity 和基于 ArrayAdapter&lt;字符串&gt;

该示例**BasicTable/HomeScreen.cs**演示如何使用这些类以显示`ListView`仅几行代码的代码：

```csharp
[Activity(Label = "BasicTable", MainLauncher = true, Icon = "@drawable/icon")]
public class HomeScreen : ListActivity {
   string[] items;
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       items = new string[] { "Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers" };
       ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItem1, items);
   }
   protected override void OnListItemClick(ListView l, View v, int position, long id)
}
```


### <a name="handling-row-clicks"></a>处理行单击

通常`ListView`还允许用户接触的行执行某些操作 （如播放一首歌曲，或调用一个联系人，或显示另一个屏幕）。 若要响应用户收尾工作需要有一个方法中实现`ListActivity` &ndash; `OnListItemClick` &ndash;如下所示：

[![SimpleListItem 的屏幕截图](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

现在用户可以按行和`Toast`将会出现警告：

[![屏幕快照的 Toast 当接触的行时显示](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>实现 ListAdapter

`ArrayAdapter<string>` 非常有利，因为其简易性，但它非常有限。 但是，通常情况下你具有业务实体，而不只是你想要绑定的字符串的集合。
例如，如果你的数据包含员工类的集合，你可能想要只显示每个雇员的名称的列表。 若要自定义的行为`ListView`来控制显示哪些数据必须实现的一个子类`BaseAdapter`重写以下四个项目：

-   **计数**&ndash;地分辨控件多少行数据中。

-   **GetView** &ndash;返回对于每一行，视图填充数据。
    此方法具有参数的`ListView`以供重复使用现有的未使用行中传递。

-   **GetItemId** &ndash;返回行标识符 （通常行数字，但也可以是任何您喜欢的长值）。

-   **此 [int]**索引器&ndash;返回与特定行号关联的数据。

中的代码示例**BasicTableAdapter/HomeScreenAdapter.cs**演示如何创建子类`BaseAdapter`:

```csharp
public class HomeScreenAdapter : BaseAdapter<string> {
   string[] items;
   Activity context;
   public HomeScreenAdapter(Activity context, string[] items) : base() {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
  {
       return position;
   }
   public override string this[int position] {  
       get { return items[position]; }
   }
   public override int Count {
       get { return items.Length; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       View view = convertView; // re-use an existing view, if one is available
      if (view == null) // otherwise create a new one
           view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
       view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
       return view;
   }
}
```


### <a name="using-a-custom-adapter"></a>使用自定义适配器

使用自定义适配器是类似于内置`ArrayAdapter`，并传入`context`和`string[]`要显示的值：

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

由于本示例使用相同的行布局 (`SimpleListItem1`) 生成的应用程序将看起来与前面的示例相同。


### <a name="row-view-re-use"></a>行视图重复使用

在此示例中有仅六个项。 因为屏幕可容纳八个，没有行重新使用所需。 在显示时数百或数千个行，但是，它将会浪费的内存来创建数百或数千个`View`对象时仅包括八种适合于屏幕一次。 若要避免此情况下，行从其视图放置在队列中的重复使用的屏幕上消失时。 当用户滚动，`ListView`调用`GetView`以请求新的视图以显示&ndash;如果它可用，它通过中的未使用的视图`convertView`参数。 如果此值为 null，则你的代码应创建新视图实例，否则为你可以重新设置该对象的属性，并重新使用它。

`GetView`方法到重新使用行视图应遵循以下模式：

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

自定义适配器实现应*始终*重新使用`convertView`之前创建新视图，以确保它们不运行内存不足时，显示较长的列表对象。

某些适配器实现 (如`CursorAdapter`) 没有`GetView`方法，而是它们需要两个不同的方法`NewView`和`BindView`的强制实施行重复使用隔开的职责`GetView`为两个方法。 没有`CursorAdapter`文档后面的示例。


## <a name="enabling-fast-scrolling"></a>启用快速滚动

快速滚动可帮助用户通过提供的其他 '的句柄，它就像直接访问列表的一部分的滚动条来滚动查看较长的列表。 此屏幕截图中显示的快速滚动句柄：

[![通过滚动句柄滚动快速查看的屏幕截图](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

导致要显示的快速滚动句柄非常简单，只设置`FastScrollEnabled`属性`true`:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>添加的节索引

部分索引为用户提供更多反馈，它们时的长列表快速滚动&ndash;它显示它们具有滚动到哪个节。 若要使其显示适配器子类必须实现的部分索引`ISectionIndexer`接口可提供具体取决于正在显示的行的索引文本：

[![显示以上部分开头 H H 的屏幕截图](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

若要实现`ISectionIndexer`需要三个方法添加到适配器：

-   **GetSections** &ndash;提供无法显示的索引标题的部分的完整列表。 此方法需要 Java 对象的数组，因此该代码需要创建`Java.Lang.Object[]`从.NET 集合。 在我们的示例中，它将返回为列表中的初始字符的列表`Java.Lang.String`。

-   **GetPositionForSection** &ndash;返回给定的节索引的第一个行位置。

-   **GetSectionForPosition** &ndash;返回部分索引，要为某一给定行显示。


该示例`SectionIndex/HomeScreenAdapter.cs`文件的构造函数中实现这些方法和一些附加代码。 构造函数生成的节索引： 循环访问每个行，并提取的标题 （项必须已进行排序的这种方式） 的第一个字符。

```csharp
alphaIndex = new Dictionary<string, int>();
for (int i = 0; i < items.Length; i++) { // loop through items
   var key = items[i][0].ToString();
   if (!alphaIndex.ContainsKey(key))
       alphaIndex.Add(key, i); // add each 'new' letter to the index
}
sections = new string[alphaIndex.Keys.Count];
alphaIndex.Keys.CopyTo(sections, 0); // convert letters list to string[]

// Interface requires a Java.Lang.Object[], so we create one here
sectionsObjects = new Java.Lang.Object[sections.Length];
for (int i = 0; i < sections.Length; i++) {
   sectionsObjects[i] = new Java.Lang.String(sections[i]);
}
```

与创建，数据结构`ISectionIndexer`方法都是非常简单：

```csharp
public Java.Lang.Object[] GetSections()
{
   return sectionsObjects;
}
public int GetPositionForSection(int section)
{
   return alphaIndexer[sections[section]];
}
public int GetSectionForPosition(int position)
{   // this method isn't called in this example, but code is provided for completeness
    int prevSection = 0;
    for (int i = 0; i < sections.Length; i++)
    {
        if (GetPositionForSection(i) > position)
        {
            break;
        }
        prevSection = i;
    }
    return prevSection;
}
```

部分索引标题无需将 1:1 映射到实际部分。 正因如此`GetPositionForSection`方法存在。
`GetPositionForSection` 使你能够将映射到任何部分是在列表视图中索引列表中的任何索引。 例如，您可能必须在索引中的"z"但您可能没有每个字母的表格部分，因此而不是"z"映射到 26 中，它可能映射到 25 或 24，或任何部分索引"z"应映射到。



## <a name="related-links"></a>相关链接

- [BasicTableAndroid （示例）](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (sample)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [FastScroll （示例）](https://developer.xamarin.com/samples/FastScroll/)
