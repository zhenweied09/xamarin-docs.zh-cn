---
title: 在 iOS 应用程序中使用数据
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: eded0918c6994481cdc82fe7662a985ea243b24d
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2018
---
# <a name="using-data-in-an-ios-app"></a>在 iOS 应用程序中使用数据

**DataAccess_Adv**示例显示的工作应用程序允许用户输入和*CRUD* （创建、 读取、 更新和删除） 的数据库功能。 该应用程序包含两个屏幕： 列表和一个数据输入窗体。 所有数据访问代码都是重用在 iOS 和 Android 中而不进行修改。

添加一些数据后的应用程序屏幕如下所示在 iOS 上：

 ![](using-data-in-an-app-images/image9.png "iOS 示例列表")

 ![](using-data-in-an-app-images/image10.png "iOS 示例详细信息")

IOS 项目所示 – 在本部分中所示的代码包含在**Orm**目录：

 ![](using-data-in-an-app-images/image13.png "iOS 项目树")

在 iOS 中 ViewControllers 的本机 UI 代码是超出了本文档的范围。
请参阅[iOS 处理表和单元格](~/ios/user-interface/controls/tables/index.md)指南以获取对 UI 控件的详细信息。

## <a name="read"></a>读取

有几个示例中的读取操作：

-  读取列表
-  读取单个记录


中的两个方法`StockDatabase`类：

```csharp
public IEnumerable<Stock> GetStocks ()
{
    lock (locker) {
        return (from i in Table<Stock> () select i).ToList ();
    }
}
public Stock GetStock (int id)
{
    lock (locker) {
        return Table<Stock>().FirstOrDefault(x => x.Id == id);
    }
}
```

iOS 呈现数据以不同方式为`UITableView`。

## <a name="create-and-update"></a>创建和更新

若要简化应用程序代码，一条存储方法是提供执行插入或更新根据 PrimaryKey 是否已设置。 因为`Id`属性将标有`[PrimaryKey]`不应在代码中设置它的属性。
此方法将检测是否已将值以前保存 （通过检查主键属性），并且插入或相应地更新该对象：

```csharp
public int SaveStock (Stock item)
{
    lock (locker) {
        if (item.Id != 0) {
            Update (item);
            return item.Id;
    } else {
            return Insert (item);
        }
    }
}
```



实际应用程序通常将需要一些验证 （如必填的字段，最小长度或其他业务规则）。
尽可能多的验证逻辑尽可能传递备份到该平台的功能根据显示的 UI 的验证错误，在共享代码中实现良好的跨平台应用程序。

## <a name="delete"></a>删除

与不同`Insert`和`Update`方法，`Delete<T>`方法可以接受只的主键值而不是完整`Stock`对象。
在此示例中`Stock`对象传递到方法，但仅 Id 属性传递给`Delete<T>`方法。

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>使用预先填充的 SQLite 数据库文件

某些应用程序附带已用数据填充数据库。
你可以轻松地通过完成此移动应用程序中发布你的应用程序与现有的 SQLite 数据库文件，在访问它之前将其复制到可写目录。 由于 SQLite 是一种标准文件格式，可在多个平台上，有许多工具可用于创建一个 SQLite 数据库文件：

-  **SQLite Manager Firefox Extension** – 适用于 Mac 和 Windows 并生成与 iOS 和 Android 兼容的文件。
-  **命令行**– 请参阅[www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) 。


在创建分发数据库文件使用你的应用，注意使用命名的表和列以确保它们匹配你的代码的要求，尤其是如果你使用 SQLite.NET 这应以满足您的 C# 类和属性的名称 （或关联的自定义属性）。

对于 iOS，将 sqlite 文件包括在你的应用程序，并确保它标记了**生成操作： 内容**。 将代码放置在`FinishedLaunching`将文件复制到可写目录*之前*你调用的任何数据方法。 下面的代码将复制现有数据库调用**data.sqlite**，仅当它尚不存在。

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

任何数据访问代码 (是否 ADO.NET 或使用 SQLite.NET) 之后，则此项已完成的将有权访问的预填充数据的执行。


## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据配方](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
