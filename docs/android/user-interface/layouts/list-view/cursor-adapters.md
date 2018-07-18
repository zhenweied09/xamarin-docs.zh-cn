---
title: 使用 CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/25/2017
ms.openlocfilehash: 20311cc50c87638391d8b078c405bc61baceb373
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766658"
---
# <a name="using-cursoradapters"></a>使用 CursorAdapters


## <a name="overview"></a>概述

Android 提供专门用于显示 SQLite 数据库查询中的数据的适配器类：

 **SimpleCursorAdapter** – 类似到`ArrayAdapter`因为不子类化的情况下可以使用它。 只需提供构造函数中的所需的参数 （如光标和布局信息中），然后将分配到`ListView`。

 **CursorAdapter** – 基类继承时你需要更多控制权限的数据绑定值复制到布局控件 （例如，显示/隐藏控件或更改其属性）。

光标适配器提供的高性能方法来滚动 SQLite 中存储的数据的较长的列表。 使用的代码必须定义中的 SQL 查询`Cursor`对象，然后介绍如何创建并填充每个行的视图。


## <a name="creating-an-sqlite-database"></a>创建一个 SQLite 数据库

若要演示光标适配器需要简单的 SQLite 数据库实现。 中的代码**SimpleCursorTableAdapter/VegetableDatabase.cs**包含代码和 SQL 以创建一个表并填充某些数据。
完整`VegetableDatabase`类如下所示：

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
   public static readonly string create_table_sql =
       "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
   public static readonly string DatabaseName = "vegetables.db";
   public static readonly int DatabaseVersion = 1;
   public VegetableDatabase(Context context) : base(context, DatabaseName, null, DatabaseVersion) { }
   public override void OnCreate(SQLiteDatabase db)
   {
       db.ExecSQL(create_table_sql);
       // seed with data
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Vegetables')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Fruits')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Flower Buds')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Legumes')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Bulbs')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Tubers')");
   }
   public override void OnUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
   {   // not required until second version :)
       throw new NotImplementedException();
   }
}
```

`VegetableDatabase`类将在实例化`OnCreate`方法`HomeScreen`活动。 `SQLiteOpenHelper`基类管理数据库文件的安装程序，并确保在 SQL 其`OnCreate`方法只能运行一次。 在以下两个示例中为使用此类`SimpleCursorAdapter`和`CursorAdapter`。

游标查询*必须*具有整数列`_id`为`CursorAdapter`工作。 如果基础表没有名为的整数列`_id`然后将其用于另一个中的唯一整数列别名`RawQuery`组成光标。 请参阅[Android 文档](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)有关进一步信息。


### <a name="creating-the-cursor"></a>创建光标

示例使用`RawQuery`若要打开到 SQL 查询`Cursor`对象。 返回从光标的列列表定义可用于在光标适配器中显示的数据列。 创建的数据库中的代码**SimpleCursorTableAdapter/HomeScreen.cs** `OnCreate`方法如下所示：

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

调用任何代码都`StartManagingCursor`还应调用`StopManagingCursor`。 示例使用`OnCreate`若要开始，和`OnDestroy`要关闭游标。 `OnDestroy`方法包含此代码：

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

应用程序都有一个 SQLite 数据库可用并已创建的光标对象，如所示之后, 它可以利用`SimpleCursorAdapter`或的子类`CusorAdapter`以显示中的行`ListView`。


## <a name="using-simplecursoradapter"></a>使用 SimpleCursorAdapter

`SimpleCursorAdapter` 就像`ArrayAdapter`，但专门用于与 SQLite 一起使用。 它不需要子类化 – 只需在创建对象时设置一些简单的参数，然后将其分配给`ListView`的`Adapter`属性。

SimpleCursorAdapter 构造函数的参数包括：

 **上下文**– 对包含的活动的引用。

 **布局**– 要使用行视图的资源 ID。

 **ICursor** – 包含要显示的数据的 SQLite 查询的游标。

 **从**字符串数组 – 对应于游标中的列的名称的字符串数组。

 **到**整数数组-数组的对应行布局中控件的布局 Id。 在指定的列的值`from`数组将绑定到此相同索引处的数组中指定了 ControlID。

`from`和`to`数组必须具有相同数量的条目，因为它们将形成从数据源到布局控件的映射视图中。

**SimpleCursorTableAdapter/HomeScreen.cs**向上示例代码电线`SimpleCursorAdapter`如下所示：

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` 是快速而简单的方法，以显示中的 SQLite 数据`ListView`。 主要限制是，它仅可以绑定列值，以显示控件，它不允许你更改 （例如，显示/隐藏控件或更改属性） 的行布局的其他方面。


## <a name="subclassing-cursoradapter"></a>子类化 CursorAdapter

A`CursorAdapter`子类具有相同的性能优点作为`SimpleCursorAdapter`为显示数据从 SQLite，但它还为你提供对创建和布局的每个行视图的完全控制。 `CursorAdapter`实现与很大差异子类化`BaseAdapter`因为它不会覆盖`GetView`， `GetItemId`，`Count`或`this[]`索引器。

提供一个有效的 SQLite 数据库，则只需重写两个方法来创建`CursorAdapter`子类：

- **BindView** – 给定的视图，更新它以提供游标中显示的数据。

- **新建**– 时调用`ListView`需要新视图以显示。 `CursorAdapter`将会负责的回收视图 (与不同`GetView`正则适配器上的方法)。

前面的示例适配器子类具有方法来返回的行数和检索当前项 –`CursorAdapter`不需要这些方法，因为可以从此游标本身中收集该信息。 通过将拆分的创建和填充到这两种方法，每个视图`CursorAdapter`强制执行视图重新使用。 这是与此相反的正则适配器尽可能若要忽略`convertView`参数`BaseAdapter.GetView`方法。


### <a name="implementing-the-cursoradapter"></a>实现 CursorAdapter

中的代码**CursorTableAdapter/HomeScreenCursorAdapter.cs**包含`CursorAdapter`子类。 它存储传递到构造函数，以便它可以访问的上下文引用`LayoutInflater`中`NewView`方法。 完整类类似如下所示：

```csharp
public class HomeScreenCursorAdapter : CursorAdapter {
   Activity context;
   public HomeScreenCursorAdapter(Activity context, ICursor c)
       : base(context, c)
   {
       this.context = context;
   }
   public override void BindView(View view, Context context, ICursor cursor)
   {
       var textView = view.FindViewById<TextView>(Android.Resource.Id.Text1);
       textView.Text = cursor.GetString(1); // 'name' is column 1 in the cursor query
   }
   public override View NewView(Context context, ICursor cursor, ViewGroup parent)
   {
       return this.context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, parent, false);
   }
}
```


### <a name="assigning-the-cursoradapter"></a>分配 CursorAdapter

在`Activity`将显示`ListView`，创建该游标和`CursorAdapter`然后将其分配给列表视图。

执行此操作中的代码**CursorTableAdapter/HomeScreen.cs** `OnCreate`方法如下所示：

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

`OnDestroy`方法包含`StopManagingCursor`前面所述的方法调用。



## <a name="related-links"></a>相关链接

- [SimpleCursorTableAdapter （示例）](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter （示例）](https://developer.xamarin.com/samples/CursorTableAdapter/)
