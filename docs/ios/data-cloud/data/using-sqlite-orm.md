---
title: 使用 iOS SQLite.NET
description: SQLite.NET PCL NuGet 库提供了用于 Xamarin.iOS 应用程序的简单数据访问机制。
ms.prod: xamarin
ms.assetid: 79813B09-42D7-47DD-AE71-A605E6B9EF24
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/18/2018
ms.openlocfilehash: 861b024a7ff5dd07752662cc45306b6533cd38bd
ms.sourcegitcommit: a4c2a63ba76b839cda99e4474e7ab46fe307cd39
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34689496"
---
# <a name="using-sqlitenet-with-ios"></a>使用 iOS SQLite.NET

Xamarin 建议 SQLite.NET 库是基本 ORM，你可以存储和检索 iOS 设备上的本地 SQLite 数据库中的对象。
ORM 代表对象关系映射 – API，你可以保存并从数据库中检索"对象"，而无需编写 SQL 语句。

<a name="Usage"/>

## <a name="usage"></a>用法

若要包含 SQLite.NET 库中的 Xamarin 应用，请向项目中添加以下 NuGet 包：

- **包名称：** sqlite net pcl
- **作者：** Frank A.Krueger
- **Id:** sqlite net pcl
- **Url:** [nuget.org/packages/sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

[![SQLite.NET NuGet 包](using-sqlite-orm-images/image1a-sml.png "SQLite.NET NuGet 包")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> 有可用的大量不同的 SQLite 包 – 请务必选择正确的订阅 （它可能不搜索中的顶部结果）。

可用的 SQLite.NET 库之后，请按照以下三个步骤，若要使用它来访问数据库操作：

1. **添加 using 语句**-将以下语句添加到数据访问是必需的 C# 文件：

    ```csharp
    using SQLite;
    ```

1. **创建一个空数据库**-可以通过将文件路径传递 SQLiteConnection 类构造函数创建的数据库引用。 不需要检查如果该文件已存在 – 将自动创建该是否需要，否则将现有数据库文件将会打开。

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

    应根据本文档前面所述的规则确定 dbPath 变量。

1. **将数据保存**-创建 SQLiteConnection 对象，通过调用其方法，例如 CreateTable 和 Insert 如下执行命令的数据库后：

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

1. **检索数据**-若要检索的对象 （或对象的列表） 使用以下语法：

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>基本数据访问示例

*DataAccess_Basic*在 iOS 上运行时，本文档的示例代码如下所示。 该代码演示如何执行简单 SQLite.NET 操作，然后在应用程序的主窗口中以文本显示中的结果。

**iOS**

 [![iOS SQLite.NET 示例](using-sqlite-orm-images/image2-sml.png)](using-sqlite-orm-images/image2-sml.png#lightbox)

下面的代码示例演示使用 SQLite.NET 库封装对基础数据库的访问整个数据库交互。 显示：

1.  创建数据库文件
1.  创建对象，然后保存它们中插入一些数据
1.  查询数据

你将需要包含这些命名空间：

```csharp
using SQLite; // from the github SQLite.cs class
```

这要求你已添加到项目，突出显示部分的 SQLite[此处](#Usage)。 请注意，通过将属性添加到类定义的 SQLite 数据库表 (`Stock`类) 而不是 CREATE TABLE 命令。

```csharp
[Table("Items")]
public class Stock {
    [PrimaryKey, AutoIncrement, Column("_id")]
    public int Id { get; set; }
    [MaxLength(8)]
    public string Symbol { get; set; }
}
public static void DoSomeDataAccess () {
       Console.WriteLine ("Creating database, if it doesn't already exist");
   string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "ormdemo.db3");
   var db = new SQLiteConnection (dbPath);
   db.CreateTable<Stock> ();
   if (db.Table<Stock> ().Count() == 0) {
        // only insert the data if it doesn't already exist
        var newStock = new Stock ();
        newStock.Symbol = "AAPL";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "GOOG";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "MSFT";
        db.Insert (newStock);
    }
    Console.WriteLine("Reading data");
    var table = db.Table<Stock> ();
    foreach (var s in table) {
        Console.WriteLine (s.Id + " " + s.Symbol);
    }
}
```

使用`[Table]`属性而无需指定表名称参数将导致基础数据库表具有与类相同的名称 （在本例中为"Stock"）。 实际的表名称为重要的如果编写直接针对数据库的 SQL 查询，而不是使用 ORM 数据访问方法。 同样`[Column("_id")]`属性是可选的并且如果缺少列将添加到具有相同名称的类中属性表。

## <a name="sqlite-attributes"></a>SQLite 属性

你可以将应用于您的类来控制如何在基础数据库中存储的常见属性包括：

-  **[PrimaryKey]** – 此特性可以应用于一个整数属性以强制其为基础表的主键。 不支持复合主键。
-  **[AutoIncrement]** – 此特性将导致一整数属性的值为自动递增为每个新的对象插入到数据库
-  **[Column(name)]** – 提供可选`name`参数将覆盖默认值的基础数据库列的名称 （这是与属性相同）。
-  **[Table(name)]** – 将标记为能够存储在基础 SQLite 表的类。 指定可选的 name 参数将覆盖默认值的基础数据库表的名称 （这是与类名称相同）。
-  **[MaxLength(value)]** – 将文本属性的长度限制时尝试的数据库插入。 使用代码应在插入对象，因为此属性时，将仅检查的数据库插入或尝试更新操作之前验证此。
-  **[忽略]** – 导致 SQLite.NET，若要忽略此属性。 这是对具有无法在数据库中，存储的类型的属性或属性不能被自动解决的模型集合是 SQLite 特别有用。
-  **[Unique]** – 可确保基础数据库列中的值是否唯一。


大部分这些属性是可选的 SQLite 将使用默认值表和列名称。 应始终指定整数主键，以便选择和删除查询可以高效地执行对您的数据。

## <a name="more-complex-queries"></a>更复杂的查询

上的以下方法`SQLiteConnection`可以用于执行其他数据操作：

-  **插入**– 向数据库添加一个新的对象。
-  **获取<T>** – 尝试检索使用为主键的对象。
-  **表<T>** – 返回表中的所有对象。
-  **删除**– 删除对象使用其主键。
-  **查询<T>** -执行 SQL 查询返回的 （作为对象） 的行数。
-  **执行**– 使用此方法 (而不`Query`) 时不希望从 （例如插入、 更新和删除说明） SQL 返回的行。


### <a name="getting-an-object-by-the-primary-key"></a>通过主键获取对象

SQLite.Net 提供要检索单个对象基于其主键的 Get 方法。

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>选择使用 Linq 对象

返回集合的方法支持 IEnumerable<T>因此你可以使用 Linq 进行查询或排序表的内容。 下面的代码演示使用 Linq 筛选出所有以字母"A"开头的条目示例：

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>选择使用 SQL 对象

即使 SQLite.Net 可以提供对你的数据基于对象的访问，有时你可能需要执行更复杂的查询不是 Linq 允许 （或你可能需要更快的性能）。 您可以使用 Query 方法中，使用 SQL 命令，如下所示：

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!IMPORTANT]
> 直接编写 SQL 语句时你创建的表和您的数据库，其中已从您的类和属性生成的列的名称的依赖项。 如果在代码中更改这些名称必须记得更新任何手动编写的 SQL 语句。

### <a name="deleting-an-object"></a>删除对象

主要密钥用于删除行，如下所示：

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

你可以检查`rowcount`以确认多少行受影响 （在这种情况下删除）。

## <a name="using-sqlitenet-with-multiple-threads"></a>使用多个线程 SQLite.NET

SQLite 支持三种不同的线程模式：*单线程*，*多线程*，和*序列化*。 如果你想要从不受任何限制的多个线程访问数据库，则可以配置要使用的 SQLite**序列化**线程处理模式。 务必要提前在你的应用程序中设置此模式下 (例如，在开始`OnCreate`方法)。

若要更改线程的模式，请调用`SqliteConnection.SetConfig`。 例如，以下代码行将配置 SQLite for**序列化**模式：

```csharp
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
