---
title: ListView
description: ListView 是重要的 Android 应用程序; UI 组件它用于无处不在从短菜单选项的列表到冗长的联系人或 internet 收藏夹列表。 它提供一种简单的方法，以提供滚动列表中可以使用内置样式格式化或全面地自定义的行。
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/21/2018
ms.openlocfilehash: 8499b9f186c12df22518893b6677cab22f0a3568
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="listview"></a>ListView

_ListView 是重要的 Android 应用程序; UI 组件它用于无处不在从短菜单选项的列表到冗长的联系人或 internet 收藏夹列表。它提供一种简单的方法，以提供滚动列表中可以使用内置样式格式化或全面地自定义的行。_


## <a name="overview"></a>概述

在 Android 应用程序的最基本的构建基块包括列表视图和适配器。 `ListView`类提供一种灵活的方式来显示数据是否短菜单或长的滚动列表。 它提供了快速滚动，索引和单个或多个选择来帮助你为你的应用程序生成适合移动应用用户界面之类的可用性功能。 `ListView` 实例需要*适配器*，以向它馈送行视图中包含的数据。

本指南介绍如何实现`ListView`和各种`Adapter`Xamarin.Android 中的类。 它还演示了如何自定义的外观`ListView`，还讨论了行重复使用以减少内存占用的重要性。 另外，还有活动生命周期将如何影响的一些讨论`ListView`和`Adapter`使用。 如果你正在使用 Xamarin.iOS，跨平台应用程序上`ListView`控件在结构上类似到 iOS `UITableView` (和 Android`Adapter`类似于`UITableViewSource`)。

首先，短教程介绍`ListView`与基本代码示例。 接下来，提供更高级主题的链接可帮助你使用`ListView`真实的应用程序中。


> [!NOTE]
> `RecyclerView`小组件是的更高级并更灵活版本`ListView`。 因为`RecyclerView`旨在作为的后继`ListView`(和`GridView`)，我们建议你使用`RecyclerView`而非`ListView`新的应用程序开发。 有关详细信息，请参阅[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。



## <a name="listview-tutorial"></a>ListView 教程

[`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) 是[ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)创建的可滚动的项的列表。 列表项自动插入到列表使用[ `IListAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.IListAdapter/)。

在本教程中，你将创建从字符串数组中读取的国家/地区名称的可滚动列表。 选中的列表项时的 toast 消息将显示在列表中的项的位置。


启动一个名为的新项目**HelloListView**。

创建一个名为的 XML 文件**list_item.xml**和将其保存在**资源/布局/**文件夹。 插入以下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

此文件定义将被放入每个项的布局[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)。

打开`MainActivity.cs`和修改类，以扩展[ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/) (而不是[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)):

```csharp
public class MainActivity : ListActivity
{
```

插入以下代码[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);

    ListView.TextFilterEnabled = true;

    ListView.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args)
    {
        Toast.MakeText(Application, ((TextView)args.View).Text, ToastLength.Short).Show();
    };
}
```

请注意这不会为该活动加载布局文件 (通常执行此操作与[ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)))。
相反，设置[ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)属性会自动添加[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)以填充整个屏幕的[ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/)。
此方法采用[ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)，用于管理将被放入的列表项的数组[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)。
[ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)构造函数采用应用程序[ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/)，每个列表项 （在上一步中创建） 的布局说明和`T[]`或[`Java.Util.IList<T>` ](https://developer.xamarin.com/api/type/Java.Util.IList/)要在中插入的对象的数组[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) （下一步定义）。

[ `TextFilterEnabled` ](https://developer.xamarin.com/api/property/Android.Widget.AbsListView.TextFilterEnabled/)属性打开文本筛选[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)，以便当用户开始键入时，将筛选列表。

[ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)事件可用于订阅的单击处理程序。 中的项[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)是单击，会调用处理程序和[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)使用中的被单击的项的文本显示消息。

你可以使用而不是定义你自己布局文件平台提供的列表项设计[ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)。
例如，尝试使用`Android.Resource.Layout.SimpleListItem1`而不是`Resource.Layout.list_item`。

添加以下`using`语句：

```csharp
using System;
```
接下来，将下面的字符串数组添加为成员`MainActivity`:

```csharp
static readonly string[] countries = new String[] {
    "Afghanistan","Albania","Algeria","American Samoa","Andorra",
    "Angola","Anguilla","Antarctica","Antigua and Barbuda","Argentina",
    "Armenia","Aruba","Australia","Austria","Azerbaijan",
    "Bahrain","Bangladesh","Barbados","Belarus","Belgium",
    "Belize","Benin","Bermuda","Bhutan","Bolivia",
    "Bosnia and Herzegovina","Botswana","Bouvet Island","Brazil","British Indian Ocean Territory",
    "British Virgin Islands","Brunei","Bulgaria","Burkina Faso","Burundi",
    "Cote d'Ivoire","Cambodia","Cameroon","Canada","Cape Verde",
    "Cayman Islands","Central African Republic","Chad","Chile","China",
    "Christmas Island","Cocos (Keeling) Islands","Colombia","Comoros","Congo",
    "Cook Islands","Costa Rica","Croatia","Cuba","Cyprus","Czech Republic",
    "Democratic Republic of the Congo","Denmark","Djibouti","Dominica","Dominican Republic",
    "East Timor","Ecuador","Egypt","El Salvador","Equatorial Guinea","Eritrea",
    "Estonia","Ethiopia","Faeroe Islands","Falkland Islands","Fiji","Finland",
    "Former Yugoslav Republic of Macedonia","France","French Guiana","French Polynesia",
    "French Southern Territories","Gabon","Georgia","Germany","Ghana","Gibraltar",
    "Greece","Greenland","Grenada","Guadeloupe","Guam","Guatemala","Guinea","Guinea-Bissau",
    "Guyana","Haiti","Heard Island and McDonald Islands","Honduras","Hong Kong","Hungary",
    "Iceland","India","Indonesia","Iran","Iraq","Ireland","Israel","Italy","Jamaica",
    "Japan","Jordan","Kazakhstan","Kenya","Kiribati","Kuwait","Kyrgyzstan","Laos",
    "Latvia","Lebanon","Lesotho","Liberia","Libya","Liechtenstein","Lithuania","Luxembourg",
    "Macau","Madagascar","Malawi","Malaysia","Maldives","Mali","Malta","Marshall Islands",
    "Martinique","Mauritania","Mauritius","Mayotte","Mexico","Micronesia","Moldova",
    "Monaco","Mongolia","Montserrat","Morocco","Mozambique","Myanmar","Namibia",
    "Nauru","Nepal","Netherlands","Netherlands Antilles","New Caledonia","New Zealand",
    "Nicaragua","Niger","Nigeria","Niue","Norfolk Island","North Korea","Northern Marianas",
    "Norway","Oman","Pakistan","Palau","Panama","Papua New Guinea","Paraguay","Peru",
    "Philippines","Pitcairn Islands","Poland","Portugal","Puerto Rico","Qatar",
    "Reunion","Romania","Russia","Rwanda","Sqo Tome and Principe","Saint Helena",
    "Saint Kitts and Nevis","Saint Lucia","Saint Pierre and Miquelon",
    "Saint Vincent and the Grenadines","Samoa","San Marino","Saudi Arabia","Senegal",
    "Seychelles","Sierra Leone","Singapore","Slovakia","Slovenia","Solomon Islands",
    "Somalia","South Africa","South Georgia and the South Sandwich Islands","South Korea",
    "Spain","Sri Lanka","Sudan","Suriname","Svalbard and Jan Mayen","Swaziland","Sweden",
    "Switzerland","Syria","Taiwan","Tajikistan","Tanzania","Thailand","The Bahamas",
    "The Gambia","Togo","Tokelau","Tonga","Trinidad and Tobago","Tunisia","Turkey",
    "Turkmenistan","Turks and Caicos Islands","Tuvalu","Virgin Islands","Uganda",
    "Ukraine","United Arab Emirates","United Kingdom",
    "United States","United States Minor Outlying Islands","Uruguay","Uzbekistan",
    "Vanuatu","Vatican City","Venezuela","Vietnam","Wallis and Futuna","Western Sahara",
    "Yemen","Yugoslavia","Zambia","Zimbabwe"
  };
```

这是将被放入的字符串的数组[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)。

运行该应用程序。 你可以向下滚动列表中，或键入以筛选，然后单击要看到一条消息的项。 将显示如下所示的内容：

[![与国家/地区名称的 ListView 示例屏幕截图](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

请注意，使用硬编码字符串数组不是最佳的设计做法。 一个用于在为简单起见，本教程演示[ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)小组件。 更好的做法是引用一个字符串数组，包含所定义的外部资源，如`string-array`项目中的资源**Resources/Values/Strings.xml**文件。 例如：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloListView</string>
  <string-array name="countries_array">
    <item>Bahrain</item>
    <item>Bangladesh</item>
    <item>Barbados</item>
    <item>Belarus</item>
    <item>Belgium</item>
    <item>Belize</item>
    <item>Benin</item>
  </string-array>
</resources>
```

若要使用这些资源字符串[ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)，替换原始[ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)替换为以下行：

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```
运行该应用程序。 将显示如下所示的内容：

[![示例的屏幕截图 ListView 的名称的较小列表](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)


## <a name="going-further-with-listview"></a>继续与 ListView

（按以下链接） 的其余主题一下全面使用`ListView`类和适配器类型可用于不同类型。 该结构如下所示：

-   **可视外观**&ndash;组成部分`ListView`控件以及它们如何工作。

-   **类**&ndash;用来显示类概述`ListView`。

-   **在 ListView 中显示数据**&ndash;如何显示数据的简单列表; 如何实现`ListView's`可用性功能; 如何使用不同的内置行布局; 以及如何适配器内存重新使用保存行视图。

-   **自定义外观**&ndash;的样式更改`ListView`使用自定义布局、 字体和颜色。

-   **使用 SQLite** &ndash;如何显示与一个 SQLite 数据库中的数据`CursorAdapter`。

-   **活动的生命周期**&ndash;时实现的设计注意事项`ListView`活动，包括其中生命周期中应填充你的数据，以及何时释放资源。

讨论 （分为六个部件） 开头的概述`ListView`类本身之前简介如何使用它的渐进式更复杂的示例。

-   [ListView 部件和功能](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [填充数据的 ListView](~/android/user-interface/layouts/list-view/populating.md)
-   [自定义 ListView 的外观](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [使用 CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [使用 ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView 和活动生命周期](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>总结

这组主题引入`ListView`并提供如何使用内置的功能的一些示例`ListActivity`。 它讨论的自定义实现`ListView`允许彩色布局，并使用 SQLite 数据库，并简要接触到了活动生命周期的相关性你`ListView`实现。


## <a name="related-links"></a>相关链接

- [AccessoryViews （示例）](https://developer.xamarin.com/samples/AccessoryViews/)
- [BasicTableAndroid （示例）](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (sample)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [BuiltInViews （示例）](https://developer.xamarin.com/samples/BuiltInViews/)
- [CustomRowView （示例）](https://developer.xamarin.com/samples/CustomRowView/)
- [FastScroll （示例）](https://developer.xamarin.com/samples/FastScroll/)
- [SectionIndex （示例）](https://developer.xamarin.com/samples/SectionIndex/)
- [SimpleCursorTableAdapter （示例）](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter （示例）](https://developer.xamarin.com/samples/CursorTableAdapter/)
- [活动生命周期教程](~/android/app-fundamentals/activity-lifecycle/index.md)
- [使用表中和单元格 （Xamarin.iOS)](~/ios/user-interface/controls/tables/index.md)
- [ListView 类引用](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [ListActivity 类引用](https://developer.xamarin.com/api/type/Android.App.ListActivity/)
- [BaseAdapter 类引用](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
- [基于 ArrayAdapter 类引用](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
- [CursorAdapter 类引用](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
