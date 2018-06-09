---
title: Xamarin.Forms 本地数据库
description: Xamarin.Forms 支持使用 SQLite 数据库引擎，这样就可以用于加载和保存在共享代码中的对象的数据库驱动的应用程序。 本指南介绍了如何读取和将数据写入到本地 SQLite 数据库使用 SQLite.Net Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 91df4d36dd8d98712063a30773f927a82676b18e
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243605"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.Forms 本地数据库

_Xamarin.Forms 支持使用 SQLite 数据库引擎，这样就可以用于加载和保存在共享代码中的对象的数据库驱动的应用程序。本指南介绍了如何读取和将数据写入到本地 SQLite 数据库使用 SQLite.Net Xamarin.Forms 应用程序。_

## <a name="overview"></a>概述

Xamarin.Forms 应用程序可以使用[SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/)包以合并到的数据库操作通过引用共享代码`SQLite`到 nuget 提供的类。 可以将路径返回到与数据库存储位置的特定于平台的项目与 Xamarin.Forms 解决方案，.NET 标准库项目中定义数据库操作。

随附[示例应用程序](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo)是一个简单的 Todo 列表应用程序。 以下屏幕截图显示示例每个平台上的显示方式：

[![Xamarin.Forms 数据库示例屏幕快照](databases-images/todo-list-sml.png "TodoList 第一页屏幕截图")](databases-images/todo-list.png#lightbox "TodoList 第一页屏幕截图") [ ![Xamarin.Forms 数据库示例屏幕快照](databases-images/todo-list-sml.png "TodoList 第一页屏幕截图")](databases-images/todo-list.png#lightbox "TodoList 第一页屏幕快照")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>使用 SQLite

本部分演示如何添加 SQLite.Net NuGet 包到 Xamarin.Forms 解决方案，编写方法以便执行数据库操作，而使用[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)可确定每个平台上存储数据库的位置。

<a name="XamarinForms_PCL_Project" />

### <a name="xamarinsforms-net-standard-or-pcl-project"></a>Xamarins.Forms.NET 标准或 PCL 项目

若要将 SQLite 支持添加到 Xamarin.Forms 项目，使用 NuGet 的搜索功能查找**sqlite net pcl**和安装该程序包：

![添加 NuGet SQLite.NET PCL 包](databases-images/vs2017-sqlite-pcl-nuget.png "添加 NuGet SQLite.NET PCL 包")

有大量的具有类似名称的 NuGet 包，正确的包具有以下属性：

- **创建的：** Frank A.Krueger
- **Id:** sqlite net pcl
- **NuGet 链接：** [sqlite net pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!TIP]
> 使用**sqlite net pcl**标准.NET 项目中甚至的 NuGet。

已添加引用后，编写一个特定于平台的功能，它是确定数据库文件的位置的抽象的接口。 示例中使用该接口所定义的一个方法：

```csharp
public interface IFileHelper
{
  string GetLocalFilePath(string filename);
}
```

一旦定义了接口，使用[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)以获取实现并获取本地文件路径 （请注意，此接口具有尚未实现）。 下面的代码获取实现`App.Database`属性：

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(DependencyService.Get<IFileHelper>().GetLocalFilePath("TodoSQLite.db3"));
    }
    return database;
  }
}
```

`TodoItemDatabase`构造函数如下所示：

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

此方法创建应用程序运行，因此避免左括号和右每次执行数据库操作的数据库文件的开销时保持打开状态的单个数据库连接。

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

所有用 PCL 项目，以在所有平台之间共享编写数据访问代码。 仅获取数据库的本地文件路径需要特定于平台的代码，如以下各节所述。

<a name="PCL_iOS" />

### <a name="ios-project"></a>iOS 项目

若要配置 iOS 应用程序，请将相同的 NuGet 包添加到 iOS 项目使用*NuGet*窗口：

![添加 NuGet SQLite.NET PCL 包](databases-images/vsmac-sqlite-nuget.png "添加 NuGet SQLite.NET PCL 包")

所需的唯一代码是`IFileHelper`确定数据文件路径的实现。 下面的代码将在该 SQLite 数据库文件放**库/数据库**在应用程序的沙盒中的文件夹。 请参阅[iOS 使用文件系统](~/ios/app-fundamentals/file-system.md)文档可供存储使用不同的目录的详细信息。

```csharp
[assembly: Dependency(typeof(FileHelper))]
namespace Todo.iOS
{
  public class FileHelper : IFileHelper
  {
    public string GetLocalFilePath(string filename)
    {
      string docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
      string libFolder = Path.Combine(docFolder, "..", "Library", "Databases");

      if (!Directory.Exists(libFolder))
      {
        Directory.CreateDirectory(libFolder);
      }

      return Path.Combine(libFolder, filename);
    }
  }
}
```

请注意，代码包括`assembly:Dependency`属性，以便此实现是通过可发现`DependencyService`。

<a name="PCL_Android" />

### <a name="android-project"></a>Android 项目

若要配置 Android 应用程序，请将相同的 NuGet 包添加到 Android 项目使用*NuGet*窗口：

![](databases-images/vsmac-sqlite-nuget.png "添加 NuGet SQLite.NET PCL 包")

已添加此引用，所需的唯一代码是`IFileHelper`确定数据文件路径的实现。

```csharp
[assembly: Dependency(typeof(FileHelper))]
namespace Todo.Droid
{
  public class FileHelper : IFileHelper
  {
    public string GetLocalFilePath(string filename)
    {
        string path = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
        return Path.Combine(path, filename);
    }
  }
}
```

<a name="PCL_UWP" />

### <a name="windows-10-universal-windows-platform-uwp"></a>Windows 10 通用 Windows 平台 (UWP)

若要配置 UWP 应用程序，请将相同的 NuGet 包添加到 UWP 项目使用*NuGet*窗口：

![添加 NuGet SQLite.NET PCL 包](databases-images/vs2017-sqlite-uwp-nuget.png "添加 NuGet SQLite.NET PCL 包")

一旦添加引用时，实现`IFileHelper`接口使用特定于平台的`Windows.Storage`API 来确定数据文件路径。

```csharp
using Windows.Storage;
...

[assembly: Dependency(typeof(FileHelper))]
namespace Todo.UWP
{
  public class FileHelper : IFileHelper
  {
    public string GetLocalFilePath(string filename)
    {
      return Path.Combine(ApplicationData.Current.LocalFolder.Path, filename);
    }
  }
}
```

## <a name="summary"></a>总结

Xamarin.Forms 支持使用 SQLite 数据库引擎，这样就可以用于加载和保存在共享代码中的对象的数据库驱动的应用程序。

本文着重**访问**使用 Xamarin.Forms 的 SQLite 数据库。 有关使用 SQLite.Net 本身的详细信息，请参阅[在 Android 上的 SQLite.NET](~/android/data-cloud/data-access/using-sqlite-orm.md)或[在 iOS 上的 SQLite.NET](~/ios/data-cloud/data/using-sqlite-orm.md)文档。 跨所有平台; 可共享的大多数 SQLite.Net 代码是仅配置 SQLite 数据库文件的位置，需要特定于平台的功能。

## <a name="related-links"></a>相关链接

- [Todo 示例](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [数据库工作簿](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/database/database.workbook)
