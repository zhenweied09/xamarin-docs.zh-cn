---
title: Xamarin.Essentials 文件系统帮助器
description: FileSystem 类包含一系列的帮助器以查找应用程序的缓存和数据目录，并打开应用包内的文件。
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 797c74bf6afa1d072d72e695df44e52658131265
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials 文件系统帮助器

![预发行 NuGet](~/media/shared/pre-release.png)

**FileSystem**类包含一系列的帮助器以查找应用程序的缓存和数据目录，并打开应用包内的文件。

## <a name="using-file-system-helpers"></a>使用文件系统帮助器

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要获取应用程序的目录来存储**缓存数据**。 缓存数据可以用于任何需要保留超过临时数据，但不是应正确操作所需的数据的数据。

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

若要获取的任何文件都不用户数据文件的应用程序的顶级 diredctory。 随操作系统一起同步 framework 备份这些文件。 请参阅下面的平台实现细节。

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

若要打开的文件，打包为应用程序包：

```csharp
 using (var stream = await FileSystem.OpenAppPackageFileAsync(templateFileName))
 {
    using (var reader = new StreamReader(stream))
    {
        var fileContents = await reader.ReadToEndAsync();
    }
 }
```

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory** – 返回[CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir)的当前上下文。
- **AppDataDirectory** – 返回[FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir)的当前上下文和是否使用备份[Autu 备份](https://developer.android.com/guide/topics/data/autobackup.html)启动上 API 23 及更高版本。

添加到任何文件**资产**文件夹中的 Android 项目，然后将标记为生成操作**AndroidAsset**以将它与`OpenAppPackageFileAsync`。

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory** – 返回[库/缓存](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)目录。
- **AppDataDirectory** – 返回[库](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)由 iTunes 和 iCloud 备份的目录。

添加到任何文件**资源**文件夹中的 iOS 项目，然后将标记为生成操作**BundledResource**以将它与`OpenAppPackageFileAsync`。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- **CacheDirectory** – 返回[LocalCacheFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder)目录...
- **AppDataDirectory** – 返回[LocalFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder)备份到云中的目录。

将任何文件添加到 UWP 项目的根节点并将标记为生成操作**内容**以将它与`OpenAppPackageFileAsync`。

--------------

## <a name="api"></a>API

- [文件系统帮助器源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/FileSystem)
- [文件系统 API 文档](xref:Xamarin.Essentials.FileSystem)
