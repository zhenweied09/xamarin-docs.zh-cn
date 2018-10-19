---
title: Xamarin.Forms 本地数据库
description: Xamarin.Forms 支持使用 SQLite 数据库引擎，这使得可以加载并将对象保存在共享代码中的数据库驱动的应用程序。 本文介绍了 Xamarin.Forms 应用程序如何读取和写入数据到使用 SQLite.Net 的本地 SQLite 数据库。
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 05c77c4c47841a897d0d1de5c3ba004db524ea2d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2018
ms.locfileid: "36310135"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.Forms 本地数据库

_Xamarin.Forms 支持使用 SQLite 数据库引擎，这使得可以加载并将对象保存在共享代码中的数据库驱动的应用程序。本文介绍了 Xamarin.Forms 应用程序如何读取和写入数据到使用 SQLite.Net 的本地 SQLite 数据库。_

## <a name="overview"></a>概述

Xamarin.Forms 应用程序可以使用[SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/)程序包来整合数据库操作组合到通过引用共享代码`SQLite`在 NuGet 中提供的类。 .NET Standard 库项目中的 Xamarin.Forms 解决方案，可以定义数据库操作。

附随[示例应用程序](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo)是一个简单的待办事项列表应用程序。 以下屏幕截图显示此示例每个平台上的显示方式：

[![Xamarin.Forms 数据库示例屏幕截图](databases-images/todo-list-sml.png "TodoList 第一页屏幕截图")](databases-images/todo-list.png#lightbox "TodoList 第一页屏幕截图") [ ![Xamarin.Forms 数据库示例屏幕截图](databases-images/todo-list-sml.png "TodoList 第一页屏幕截图")](databases-images/todo-list.png#lightbox "TodoList 第一页屏幕截图")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>使用 SQLite

若要将 SQLite 支持添加到 Xamarin.Forms.NET Standard 库，使用 NuGet 的搜索功能查找**sqlite net pcl**并安装最新的包：

![添加 NuGet SQLite.NET PCL 包](databases-images/vs2017-sqlite-pcl-nuget.png "添加 NuGet SQLite.NET PCL 包")

有多种具有类似名称的 NuGet 包，正确的包具有这些属性：

- **创建的：** Frank A.Krueger
- **Id:** sqlite net pcl
- **NuGet 链接：** [sqlite net pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> 尽管包名称，使用**sqlite net pcl**甚至在.NET Standard 项目中的 NuGet 包。

添加引用后的属性添加到`App`返回用于存储数据库的本地文件路径的类：

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

`TodoItemDatabase`构造函数，使用作为参数的数据库文件的路径，如下所示：

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

利用公开为单一实例是在创建单个数据库连接的数据库，保持打开状态时应用程序运行，因此避免銷打开和关闭数据库文件每次数据库操作执行。

其余部分`TodoItemDatabase`类包含跨平台运行 SQLite 查询。 示例查询代码如下所示 (有关语法的更多详细信息可在[使用 Xamarin.iOS 使用 SQLite.NET](~/ios/data-cloud/data/using-sqlite-orm.md)。

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
> 使用异步 SQLite.Net API 的优点是该数据库操作移到后台线程。 此外，没有必要编写其他并发处理代码，因为 API 将处理它。

## <a name="summary"></a>总结

Xamarin.Forms 支持使用 SQLite 数据库引擎，这使得可以加载并将对象保存在共享代码中的数据库驱动的应用程序。

本文重点**访问**使用 Xamarin.Forms 的 SQLite 数据库。 有关使用 SQLite.Net 本身的详细信息，请参阅[在 Android 上的 SQLite.NET](~/android/data-cloud/data-access/using-sqlite-orm.md)或[SQLite.NET 在 iOS 上的](~/ios/data-cloud/data/using-sqlite-orm.md)文档。

## <a name="related-links"></a>相关链接

- [待办事项示例](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)

