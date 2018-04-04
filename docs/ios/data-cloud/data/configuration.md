---
title: 配置
ms.prod: xamarin
ms.assetid: E5582F4B-AD74-420F-9E6D-B07CFB420B3A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: f4a1bc65fbba68b59196702978633eaf46bc44c5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="configuration"></a>配置

要在你将需要确定你的数据库文件的正确的文件位置的 Xamarin.iOS 应用程序中使用 SQLite。

## <a name="database-file-path"></a>数据库文件路径

无论使用哪种数据访问方法，你必须创建数据库文件，然后可以将随 SQLite 存储数据。 根据您面向的哪种平台的文件位置将有所不同。 对于 iOS 可用于环境类构造有效的路径，如下面的代码段中所示：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

没有要决定在何处存储数据库文件时考虑的其他事项。 在 iOS 上，你可能想要备份的自动 （或不） 的数据库。

如果你想要使用跨平台应用程序中的每个平台上的其他位置，可用编译器指令所示来生成每个平台的不同路径：

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

请参阅[使用文件系统](~/ios/app-fundamentals/file-system.md)文章，以在 iOS 上使用哪些文件位置的详细信息。 请参阅[生成跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)使用编译器指令写入每个平台的特定代码的详细信息的文档。

## <a name="threading"></a>线程

不应在多个线程使用相同的 SQLite 数据库连接。 小心地将其打开、 使用，然后关闭你在同一线程创建的任何连接。

若要确保你的代码不尝试从多个线程同时访问的 SQLite 数据库，手动执行锁时想要访问数据库，如下：

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

应使用同一个锁包装所有数据库访问 （读取、 写入、 更新等）。 必须格外小心，以免死锁情况通过确保在锁子句内的工作保持简单并不调用其他方法，可能还需要锁的 ！


## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据配方](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
