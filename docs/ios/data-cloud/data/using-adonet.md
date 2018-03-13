---
title: "使用 ADO.NET"
ms.topic: article
ms.prod: xamarin
ms.assetid: 79078A4D-2D24-44F3-9543-B50418A7A000
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: b53f98206c100ed76f601937844bf182a6dc146c
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="using-adonet"></a>使用 ADO.NET

Xamarin 提供内置支持可在 iOS 中，公开使用熟悉的类似于 ADO.NET 的语法上的 SQLite 数据库。 使用这些 Api 需要你编写 SQL 语句处理的 SQLite，如`CREATE TABLE`，`INSERT`和`SELECT`语句。

## <a name="assembly-references"></a>程序集引用

若要使用访问通过 ADO.NET 必须添加 SQLite`System.Data`和`Mono.Data.Sqlite`引用您的 iOS 项目，如下所示 （适用于 Mac 和 Visual Studio 的 Visual Studio 中的示例）：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

 ![](using-adonet-images/image4.png "适用于 Mac 的 Visual Studio 中的程序集引用")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  ![](using-adonet-images/image6.png "Visual Studio 中的程序集引用")

-----

右键单击**引用 > 编辑引用...**然后单击以选择所需的程序集。

## <a name="about-monodatasqlite"></a>有关 Mono.Data.Sqlite

我们将使用`Mono.Data.Sqlite.SqliteConnection`类以创建空数据库文件，然后实例化`SqliteCommand`对象，我们可以使用执行 SQL 对数据库的说明。


1. **创建一个空数据库**-调用`CreateFile`使用一个有效的方法 (即。 可写) 文件路径。 你应检查是否调用此方法之前已存在此文件，否则将旧的基础上创建新的 （空） 数据库和旧的文件中的数据将会丢失：

    `Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`

    > [!NOTE]
> **注意：** dbPath 变量，必须根据本文档前面所述的规则来确定。

2. **创建数据库连接**-SQLite 数据库文件已被创建后可以创建用于访问数据的连接的对象。 使用的连接字符串，即采用的形式构造连接`Data Source=file_path`，如下所示：

    ```csharp
    var connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open();
    // do stuff
    connection.Close();
    ```

    如前所述，连接不应是重复使用跨不同的线程。 如果有疑问，创建作为所需的连接并将其关闭，完成后;但要执行此超过通常太需要注意。
    
3. **创建和执行数据库命令**-一旦我们有了我们可以执行对其任意 SQL 命令的连接。 下面的代码显示正在执行的 CREATE TABLE 语句。

    ```csharp
    using (var command = connection.CreateCommand ()) {
        command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
        var rowcount = command.ExecuteNonQuery ();
    }
    ```

直接对数据库执行 SQL 时，应执行正常的预防措施不以无效的请求，例如，尝试创建已存在一个表。 跟踪的数据库的结构，以便不会导致 SqliteException，例如"已存在 SQLite 错误表 [项目]"。

## <a name="basic-data-access"></a>基本数据访问

*DataAccess_Basic*在 iOS 上运行时，本文档的示例代码如下所示：

 ![](using-adonet-images/image9.png "iOS ADO.NET 示例")

下面的代码演示如何执行简单的 SQLite 操作，并显示应用程序的主窗口中的文本中的结果。

你将需要包含这些命名空间：

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

下面的代码示例显示了一个整个数据库交互：

1.  创建数据库文件
2.  插入一些数据
3.  查询数据

这些操作将通常会出现在多个位置在整个代码，例如可能创建的数据库文件和表，你的应用程序首次启动时，还可以在单个屏幕中将数据读取和写入在你的应用程序中执行。 在下面的示例已被分组到单个方法对于此示例：

```csharp
public static SqliteConnection connection;
public static string DoSomeDataAccess ()
{
    // determine the path for the database file
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "adodemo.db3");

    bool exists = File.Exists (dbPath);

    if (!exists) {
        Console.WriteLine("Creating database");
        // Need to create the database before seeding it with some data
        Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);
        connection = new SqliteConnection ("Data Source=" + dbPath);

        var commands = new[] {
            "CREATE TABLE [Items] (_id ntext, Symbol ntext);",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'AAPL')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('2', 'GOOG')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('3', 'MSFT')"
        };
        // Open the database connection and create table with data
        connection.Open ();
        foreach (var command in commands) {
            using (var c = connection.CreateCommand ()) {
                c.CommandText = command;
                var rowcount = c.ExecuteNonQuery ();
                Console.WriteLine("\tExecuted " + command);
            }
        }
    } else {
        Console.WriteLine("Database already exists");
        // Open connection to existing database file
        connection = new SqliteConnection ("Data Source=" + dbPath);
        connection.Open ();
    }

    // query the database to prove data was inserted!
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT [_id], [Symbol] from [Items]";
        var r = contents.ExecuteReader ();
        Console.WriteLine("Reading data");
        while (r.Read ())
            Console.WriteLine("\tKey={0}; Value={1}",
                              r ["_id"].ToString (),
                              r ["Symbol"].ToString ());
    }
    connection.Close ();
}
```

## <a name="more-complex-queries"></a>更复杂的查询

因为 SQLite 允许对数据运行任意 SQL 命令，你可以执行任何创建、 插入、 更新、 删除或选择你喜欢的语句。 你可以阅读 SQLite 支持 Sqlite 网站上的 SQL 命令。 使用三种方法之一在 SqliteCommand 对象上运行 SQL 语句：

-  **ExecuteNonQuery** – 通常用于表创建或数据插入。 某些操作的返回值是受影响的行数，否则它为-1。
-  **ExecuteReader** – 时一个行集合应返回为使用`SqlDataReader`。
-  **ExecuteScalar** – 检索单个值 （例如聚合）。


### <a name="executenonquery"></a>EXECUTENONQUERY

INSERT、 UPDATE 和 DELETE 语句将返回受影响的行数。 所有其他 SQL 语句将返回-1。

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

下面的方法演示的 SELECT 语句的 WHERE 子句。 代码编写一个完整的 SQL 语句，因为它必须注意进行转义保留的字符，如字符串周围的引号 （'）。

```csharp
public static string MoreComplexQuery ()
{
    var output = "";
    output += "\nComplex query example: ";
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal), "ormdemo.db3");

    connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open ();
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT * FROM [Items] WHERE Symbol = 'MSFT'";
        var r = contents.ExecuteReader ();
        output += "\nReading data";
        while (r.Read ())
            output += String.Format ("\n\tKey={0}; Value={1}",
                    r ["_id"].ToString (),
                    r ["Symbol"].ToString ());
    }
    connection.Close ();

    return output;
}
```

ExecuteReader 方法返回 SqliteDataReader 对象。 在示例中所示的读取方法，除了其他有用属性包括：

-  **RowsAffected** – 查询受影响的行计数。
-  **HasRows** – 是否没有返回任何行。


### <a name="executescalar"></a>EXECUTESCALAR

这用于返回单个值 （如聚合） 的 SELECT 语句。

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

`ExecuteScalar`方法的返回类型是`object`– 你应根据数据库查询结果进行转换。 结果可能是 COUNT 查询的一个整数或字符串中单个列的 SELECT 查询。 请注意，这不同于其他 Execute 方法返回一个读取器对象或受影响的行数的计数。


## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据配方](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
