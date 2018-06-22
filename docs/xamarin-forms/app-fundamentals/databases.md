---
title: Xamarin.Forms 本地数据库
description: Xamarin.Forms 支持使用 SQLite 数据库引擎，这样就可以用于加载和保存在共享代码中的对象的数据库驱动的应用程序。 本指南介绍了如何读取和将数据写入到本地 SQLite 数据库使用 SQLite.Net Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: feec4993a0719a083d713e084552b18aead8ee42
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310135"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.Forms 本地数据库

_Xamarin.Forms 支持使用 SQLite 数据库引擎，这样就可以用于加载和保存在共享代码中的对象的数据库驱动的应用程序。本指南介绍了如何读取和将数据写入到本地 SQLite 数据库使用 SQLite.Net Xamarin.Forms 应用程序。_

## <a name="overview"></a>概述

Xamarin.Forms 应用程序可以使用[SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/)包以合并到的数据库操作通过引用共享代码`SQLite`到 nuget 提供的类。 可以在 Xamarin.Forms 解决方案的标准.NET 库项目中定义数据库操作。

随附[示例应用程序](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo)是一个简单的 Todo 列表应用程序。 以下屏幕截图显示示例每个平台上的显示方式：

[![Xamarin.Forms 数据库示例屏幕快照](databases-images/todo-list-sml.png "TodoList 第一页屏幕截图")](databases-images/todo-list.png#lightbox "TodoList 第一页屏幕截图") [ ![Xamarin.Forms 数据库示例屏幕快照](databases-images/todo-list-sml.png "TodoList 第一页屏幕截图")](databases-images/todo-list.png#lightbox "TodoList 第一页屏幕快照")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>使用 SQLite

若要将 SQLite 支持添加到 Xamarin.Forms.NET 标准库，使用 NuGet 的搜索功能查找**sqlite net pcl**并安装最新的包：

![添加 NuGet SQLite.NET PCL 包](databases-images/vs2017-sqlite-pcl-nuget.png "添加 NuGet SQLite.NET PCL 包")

有大量的具有类似名称的 NuGet 包，正确的包具有以下属性：

- **创建的：** Frank A.Krueger
- **Id:** sqlite net pcl
- **NuGet 链接：** [sqlite net pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> 尽管包名称、 使用**sqlite net pcl**标准.NET 项目中甚至的 NuGet 包。

已添加引用后，将属性添加到`App`返回一个用于存储数据库的本地文件路径的类：

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(
        Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "TodoSQLite.db3"));
    }
    return database;
  }
}
```

`TodoItemDatabase`构造函数，它采用作为自变量的数据库文件的路径，如下所示：

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

利用单个数据库连接，将创建单一实例原样公开数据库，保持打开状态时应用程序运行，因此避免开销打开和关闭数据库文件每次数据库操作不执行。

其余部分`TodoItemDatabase`类包含运行跨平台的 SQLite 查询。 示例查询代码如下所示 (语法的详细信息可在[使用 SQLite.NET](~/cross-platform/app-fundamentals/index.md)文章):

```csharp
public Task<List<TodoItem>> GetItemsAsync()
{
  return database.Table<TodoItem>().ToListAsync();
}

public Task<List<TodoItem>> GetItemsNotDoneAsync()
{
  return database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
}

public Task<TodoItem> GetItemAsync(int id)
{
  return database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
}

public Task<int> SaveItemAsync(TodoItem item)
{
  if (item.ID != 0)
  {
    return database.UpdateAsync(item);
  }
  else {
    return database.InsertAsync(item);
  }
}

public Task<int> DeleteItemAsync(TodoItem item)
{
  return database.DeleteAsync(item);
}
```

> [!NOTE]
> 使用异步 SQLite.Net API 的优点是该数据库操作将被移动到后台线程。 此外，没有必要编写处理代码，因为该 API 将对其负责的其他并发。

## <a name="summary"></a>总结

Xamarin.Forms 支持使用 SQLite 数据库引擎，这样就可以用于加载和保存在共享代码中的对象的数据库驱动的应用程序。

本文着重**访问**使用 Xamarin.Forms 的 SQLite 数据库。 有关使用 SQLite.Net 本身的详细信息，请参阅[在 Android 上的 SQLite.NET](~/android/data-cloud/data-access/using-sqlite-orm.md)或[在 iOS 上的 SQLite.NET](~/ios/data-cloud/data/using-sqlite-orm.md)文档。

## <a name="related-links"></a>相关链接

- [Todo 示例](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [数据库工作簿](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/database/database.workbook)
