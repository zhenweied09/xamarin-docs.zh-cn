---
title: 使用 ADO.NET
ms.prod: xamarin
ms.assetid: F6ABCEF1-951E-40D8-9EA9-DD79123C2650
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: a2f7a7a0c282284d7a45fb81c134d300aef5afba
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="using-adonet"></a>使用 ADO.NET

Xamarin 提供内置支持，可在 Android 上提供可以使用熟悉的类似于 ADO.NET 的语法公开的 SQLite 数据库。 使用这些 Api 需要你编写 SQL 语句处理的 SQLite，如`CREATE TABLE`，`INSERT`和`SELECT`语句。

## <a name="assembly-references"></a>程序集引用

若要使用访问通过 ADO.NET 必须添加 SQLite`System.Data`和`Mono.Data.Sqlite`引用为 Android 项目，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin) 

![Visual Studio 中的 android 引用](using-adonet-images/image7.png "Android 引用 Visual Studio 中") 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac) 

![适用于 Mac 的 Visual Studio 中的 android 引用](using-adonet-images/image5.png "Android 引用 Visual Studio 中的 Mac") 

-----


右键单击**引用 > 编辑引用...**然后单击以选择所需的程序集。

## <a name="about-monodatasqlite"></a>有关 Mono.Data.Sqlite

我们将使用`Mono.Data.Sqlite.SqliteConnection`类以创建空数据库文件，然后实例化`SqliteCommand`对象，我们可以使用执行 SQL 对数据库的说明。

**创建一个空数据库**&ndash;调用`CreateFile`使用一个有效的方法 (即。 可写) 文件路径。 你应检查是否调用此方法之前已存在此文件，否则将旧的基础上创建新的 （空） 数据库和旧的文件中的数据将会丢失。
`Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);` `dbPath`应根据本文档前面所述的规则确定变量。

**创建数据库连接** &ndash; SQLite 数据库文件创建后可以创建连接对象来访问数据。 使用的连接字符串，即采用的形式构造连接`Data Source=file_path`，如下所示：

```csharp
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open();
// do stuff
connection.Close();
```

如前所述，连接不应是重复使用跨不同的线程。 如果有疑问，创建作为所需的连接并将其关闭，完成后;但要执行此超过通常太需要注意。

**创建和执行数据库命令**&ndash;一旦我们有一个连接我们可以执行对其任意 SQL 命令。 下面的代码显示`CREATE TABLE`正在执行的语句。

```csharp
using (var command = connection.CreateCommand ()) {
    command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
    var rowcount = command.ExecuteNonQuery ();
}
```

直接对数据库执行 SQL 时，应执行正常的预防措施不以无效的请求，例如，尝试创建已存在一个表。 跟踪的数据库的结构，以便不会导致`SqliteException`如**SQLite 错误表 [项目] 已存在**。

## <a name="basic-data-access"></a>基本数据访问

*DataAccess_Basic*在 Android 上运行时，本文档的示例代码如下所示：

![Android ADO.NET 示例](using-adonet-images/image8.png "Android ADO.NET 示例")

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

因为 SQLite 允许对数据运行任意 SQL 命令，你可以执行任何`CREATE`， `INSERT`， `UPDATE`， `DELETE`，或`SELECT`您喜欢的语句。 你可以阅读 SQLite 支持 Sqlite 网站上的 SQL 命令。 使用三种方法之一上运行 SQL 语句`SqliteCommand`对象：

-   **ExecuteNonQuery** &ndash;通常用于表创建或数据插入。 某些操作的返回值是受影响的行数，否则它为-1。

-   **ExecuteReader** &ndash;时一个行集合应返回为使用`SqlDataReader`。

-   **ExecuteScalar** &ndash;检索单个值 （例如聚合）。


### <a name="executenonquery"></a>EXECUTENONQUERY

`INSERT``UPDATE`，和`DELETE`语句将返回受影响的行数。 所有其他 SQL 语句将返回-1。

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

下面的方法演示`WHERE`中的子句`SELECT`语句。
代码编写一个完整的 SQL 语句，因为它必须注意进行转义保留的字符，如字符串周围的引号 （'）。

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

`ExecuteReader` 方法返回 `SqliteDataReader` 对象。 除了`Read`方法在示例中，显示其他有用的属性包括：

-   **RowsAffected** &ndash;查询受影响的行计数。

-   **HasRows** &ndash;是否没有返回任何行。


### <a name="executescalar"></a>EXECUTESCALAR

使用此窗体`SELECT`返回单个值 （如聚合） 的语句。

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

`ExecuteScalar`方法的返回类型是`object`&ndash;应根据数据库查询将结果强制转换。 一个整数，可能会导致`COUNT`查询或单个列中的字符串`SELECT`查询。 请注意，这不同于其他`Execute`方法返回一个读取器对象或受影响的行数的计数。



## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 数据配方](https://developer.xamarin.com/recipes/android/data/)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
