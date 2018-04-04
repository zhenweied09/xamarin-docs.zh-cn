---
title: 在应用程序中使用数据
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: dd2a0a58a3a8c10671609aa385629d4754ca5378
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="using-data-in-an-app"></a>在应用程序中使用数据

**DataAccess_Adv**示例显示的工作应用程序允许用户输入和 CRUD （创建、 读取、 更新和删除） 的数据库功能。 该应用程序包含两个屏幕： 列表和一个数据输入窗体。 所有数据访问代码都是重用在 iOS 和 Android 中而不进行修改。

添加一些数据后的应用程序屏幕如下所示在 Android 上：

![Android 示例列表](using-data-in-an-app-images/image11.png "Android 示例列表")

![Android 示例详细信息](using-data-in-an-app-images/image12.png "Android 示例详细信息")

Android 项目如下所示&ndash;本节中所示的代码包含在**Orm**目录：

![Android 项目树](using-data-in-an-app-images/image14.png "Android 项目树")

超出了本文档的范围是 Android 中的活动的本机 UI 代码。 请参阅[Android Listview 和适配器](~/android/user-interface/layouts/list-view/index.md)指南以获取对 UI 控件的详细信息。

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

Android 呈现数据作为`ListView`。

## <a name="create-and-update"></a>创建和更新

若要简化应用程序代码，一条存储方法是提供执行插入或更新根据 PrimaryKey 是否已设置。 因为`Id`属性将标有`[PrimaryKey]`不应在代码中设置它的属性。 此方法将检测是否已将值以前保存 （通过检查主键属性），并且插入或相应地更新该对象：

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

实际应用程序通常将需要一些验证 （如必填的字段，最小长度或其他业务规则）。 尽可能多的验证逻辑尽可能传递备份到该平台的功能根据显示的 UI 的验证错误，在共享代码中实现良好的跨平台应用程序。

## <a name="delete"></a>删除

与不同`Insert`和`Update`方法，`Delete<T>`方法可以接受只的主键值而不是完整`Stock`对象。 在此示例中`Stock`对象传递到方法，但仅 Id 属性传递给`Delete<T>`方法。

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>使用预先填充的 SQLite 数据库文件

某些应用程序附带已用数据填充数据库。 你可以轻松地通过完成此移动应用程序中发布你的应用程序与现有的 SQLite 数据库文件，在访问它之前将其复制到可写目录。 由于 SQLite 是一种标准文件格式，可在多个平台上，有许多工具可用于创建一个 SQLite 数据库文件：

-   **SQLite Manager Firefox Extension** &ndash;适用于 Mac 和 Windows 并生成与 iOS 和 Android 兼容的文件。

-   **命令行**&ndash;请参阅[www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) 。

在创建分发数据库文件使用你的应用，注意使用命名的表和列以确保它们匹配你的代码的要求，尤其是如果你使用 SQLite.NET 这应以满足您的 C# 类和属性的名称 （或关联的自定义属性）。

若要确保在 Android 应用程序中的其他部分之前，运行某些代码，你可以将它放在要加载的第一个活动，也可以创建`Application`加载之前的任何活动的子类。 下面的代码显示`Application`将现有数据库文件复制的子类**data.sqlite**外**/Resources/Raw/**目录。

```csharp
[Application]
public class YourAndroidApp : Application {
    public override void OnCreate ()
    {
        base.OnCreate ();
        var docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
        Console.WriteLine ("Data path:" + Database.DatabaseFilePath);
        var dbFile = Path.Combine(docFolder, "data.sqlite"); // FILE NAME TO USE WHEN COPIED
        if (!System.IO.File.Exists(dbFile)) {
            var s = Resources.OpenRawResource(Resource.Raw.data);  // DATA FILE RESOURCE ID
            FileStream writeStream = new FileStream(dbFile, FileMode.OpenOrCreate, FileAccess.Write);
            ReadWriteStream(s, writeStream);
        }
    }
    // readStream is the stream you need to read
    // writeStream is the stream you want to write to
    private void ReadWriteStream(Stream readStream, Stream writeStream)
    {
        int Length = 256;
        Byte[] buffer = new Byte[Length];
        int bytesRead = readStream.Read(buffer, 0, Length);
        // write the required bytes
        while (bytesRead > 0)
        {
            writeStream.Write(buffer, 0, bytesRead);
            bytesRead = readStream.Read(buffer, 0, Length);
        }
        readStream.Close();
        writeStream.Close();
    }
}
```


## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 数据配方](https://developer.xamarin.com/recipes/android/data/)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
