---
title: 在 Xamarin.iOS System.Data
description: 本文档介绍如何使用 System.Data 和 Mono.Data.Sqlite.dll 访问 SQLite Xamarin.iOS 应用程序中的数据。
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: f20bdbdb9fe0d25e1ba545633e271af912aab3ba
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784711"
---
# <a name="systemdata-in-xamarinios"></a>在 Xamarin.iOS System.Data

Xamarin.iOS 8.10 增加了对支持[System.Data](https://developer.xamarin.com/api/namespace/System.Data/)，包括`Mono.Data.Sqlite.dll`ADO.NET 提供程序。 支持包括以下添加[程序集](~/cross-platform/internals/available-assemblies.md):

-  `System.Data.dll`
-  `System.Data.Service.Client.dll`
-  `System.Transactions.dll`
-  `Mono.Data.Tds.dll`
-  `Mono.Data.Sqlite.dll`

<a name="Example" />

## <a name="example"></a>示例

以下程序创建中的数据库`Documents/mydb.db3`，而且如果数据库不之前存在，它使用示例数据填充。 随后查询数据库，同时将输出写入到`stderr`。

### <a name="add-references"></a>添加引用

首先，右键单击**引用**节点，然后选择**编辑引用...** 然后选择`System.Data`和`Mono.Data.Sqlite`:

[![](system.data-images/edit-references-sml.png "添加新引用")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>代码示例

下面的代码演示一个简单的示例创建一个表并将插入行使用嵌入的 SQL 命令：

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;

class Demo {
    static void Main (string [] args)
    {
        var connection = GetConnection ();
        using (var cmd = connection.CreateCommand ()) {
            connection.Open ();
            cmd.CommandText = "SELECT * FROM People";
            using (var reader = cmd.ExecuteReader ()) {
                while (reader.Read ()) {
                    Console.Error.Write ("(Row ");
                    Write (reader, 0);
                    for (nint i = 1; i < reader.FieldCount; ++i) {
                        Console.Error.Write(" ");
                        Write (reader, i);
                    }
                    Console.Error.WriteLine(")");
                }
            }
            connection.Close ();
        }
    }

    static SqliteConnection GetConnection()
    {
        var documents = Environment.GetFolderPath (
                Environment.SpecialFolder.Personal);
        string db = Path.Combine (documents, "mydb.db3");
        bool exists = File.Exists (db);
        if (!exists)
            SqliteConnection.CreateFile (db);
        var conn = new SqliteConnection("Data Source=" + db);
        if (!exists) {
            var commands = new[] {
            "CREATE TABLE People (PersonID INTEGER NOT NULL, FirstName ntext, LastName ntext)",
            // WARNING: never insert user-entered data with embedded parameter values
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (1, 'First', 'Last')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (2, 'Dewey', 'Cheatem')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (3, 'And', 'How')",
            };
            conn.Open ();
            foreach (var cmd in commands) {
                using (var c = conn.CreateCommand()) {
                    c.CommandText = cmd;
                    c.CommandType = CommandType.Text;
                    c.ExecuteNonQuery ();
                }
            }
            conn.Close ();
        }
        return conn;
    }

    static void Write(SqliteDataReader reader, int index)
    {
        Console.Error.Write("({0} '{1}')",
                reader.GetName(index),
                reader [index]);
    }
}
```

> [!IMPORTANT]
> 如上面的代码示例中所述，它是在 SQL 命令中嵌入字符串，因为它使你的代码容易受到的做法不妥[SQL 注入](http://en.wikipedia.org/wiki/SQL_injection)。


### <a name="using-command-parameters"></a>使用命令参数

下面的代码演示如何使用命令参数 （即使文本包含特殊 SQL 字符，如单撇号） 安全地将插入数据库的用户输入的文本：

```csharp
// user input from Textbox control
var fname = fnameTextbox.Text;
var lname = lnameTextbox.Text;
// use command parameters to safely insert into database
using (var addCmd = conn.CreateCommand ()) {
    addCmd.CommandText = "INSERT INTO [People] (PersonID, FirstName, LastName) VALUES (@COL1, @COL2, @COL3)";
    addCmd.CommandType = System.Data.CommandType.Text;
    addCmd.AddParameterWithValue ("@COL1", 1);
    addCmd.AddParameterWithValue ("@COL2", fname);
    addCmd.AddParameterWithValue ("@COL3", lname);
    addCmd.ExecuteNonQuery ();
}
```

<a name="Missing_Functionality" />

## <a name="missing-functionality"></a>缺少的功能

同时**System.Data**和**Mono.Data.Sqlite**缺少一些功能。

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

功能中缺少**System.Data.dll**组成：

-  任何东西需要[System.CodeDom](https://developer.xamarin.com/api/namespace/System.CodeDom/) （例如 [System.Data.TypedDataSetGenerator](https://developer.xamarin.com/api/type/System.Data.TypedDataSetGenerator/) )
-  XML 配置文件支持 （例如 [System.Data.Common.DbProviderConfigurationHandler](https://developer.xamarin.com/api/type/System.Data.Common.DbProviderConfigurationHandler/) )
-   [System.Data.Common.DbProviderFactories](https://developer.xamarin.com/api/type/System.Data.Common.DbProviderFactories/) （取决于 XML 配置文件支持）
-   [System.Data.OleDb](https://developer.xamarin.com/api/namespace/System.Data.OleDb/)
-   [System.Data.Odbc](https://developer.xamarin.com/api/namespace/System.Data.Odbc/)
-  `System.EnterpriseServices.dll`依赖项已*删除*从`System.Data.dll`，这会导致在删除的[SqlConnection.EnlistDistributedTransaction(ITransaction)](https://developer.xamarin.com/api/member/System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction/(System.EnterpriseServices.ITransaction))方法。


<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

同时， **Mono.Data.Sqlite.dll**不遇到的任何源代码更改，但改为可能的大量主机*运行时*发出自`Mono.Data.Sqlite.dll`将绑定 SQLite 3.5。 iOS 8，同时，附带 SQLite 3.8.5。 点到为止您说，事项已更改两个版本之间。

旧版本的 iOS 附带的 SQLite 的以下版本：

- **iOS 7** -版本 3.7.13。
- **iOS 6** -版本 3.7.13。
- **iOS 5** -版本 3.7.7。
- **iOS 4** -版本 3.6.22。

最常见的问题看起来与数据库架构查询，例如确定在运行时列存在对给定表，如`Mono.Data.Sqlite.SqliteConnection.GetSchema`(重写[DbConnection.GetSchema](https://developer.xamarin.com/api/member/System.Data.Common.DbConnection.GetSchema/)) 和`Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable`(重写[DbDataReader.GetSchemaTable](https://developer.xamarin.com/api/member/System.Data.Common.DbDataReader.GetSchemaTable/))。 简单地说，它看起来的任何东西使用[DataTable](https://developer.xamarin.com/api/type/System.Data.DataTable/)是可能无法工作。

<a name="Data_Binding" />

## <a name="data-binding"></a>数据绑定

这次不支持数据绑定。

