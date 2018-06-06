---
title: Xamarin.Essentials： 版本跟踪
description: 中 Xamarin.Essentials 的 VersionTracking 类允许你检查应用程序版本和内部版本号以及看到此类的其他信息，就像它是第一个时间不断启动应用程序，或对于最新版本，获取上一个生成信息和详细信息。
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2c092d6767045f0af956c5dab74801077dadb51f
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782997"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials： 版本跟踪

![预发行 NuGet](~/media/shared/pre-release.png)

**VersionTracking**类允许你检查应用程序版本和内部版本号以及看到此类的其他信息，就像它是第一个时间不断启动应用程序，或对于最新版本，获取上一个生成信息、 和的详细信息。

## <a name="using-version-tracking"></a>使用版本跟踪

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

首次使用**VersionTracking**它将开始跟踪最新版本的类。 必须调用`Track`早期仅在你的应用程序每次加载以确保系统会跟踪当前的版本信息：

```csharp
VersionTracking.Track();
```

首字母后`Track`称为可以读取版本信息：

```csharp

// First time ever launched application
var firstLaunch = VersionTracking.IsFirstLaunchEver;

// First time launching current version
var firstLaunchCurrent = VersionTracking.IsFirstLaunchForCurrentVersion;

// First time launching current build
var firstLaunchBuild = VersionTracking.IsFirstLaunchForCurrentBuild;

// Current app version (2.0.0)
var currentVersion = VersionTracking.CurrentVersion;

// Current build (2)
var currentBuild = VersionTracking.CurrentBuild;

// Previous app version (1.0.0)
var previousVersion = VersionTracking.PreviousVersion;

// Previous app build (1)
var previousBuild = VersionTracking.PreviousBuild;

// First version of app installed (1.0.0)
var firstVersion = VersionTracking.FirstInstalledVersion;

// First build of app installed (1)
var firstBuild = VersionTracking.FirstInstalledBuild;

// List of versions installed (1.0.0, 2.0.0)
var versionHistory = VersionTracking.VersionHistory;

// List of builds installed (1, 2)
var buildHistory = VersionTracking.BuildHistory;
```

## <a name="platform-implementation-specifics"></a>平台实现细节

所有版本信息都存储使用[首选项](preferences.md)中 Xamarin.Essentials API 和都存储的文件名 **[您的应用程序的包的 ID].xamarinessentials**。

卸载应用程序将导致_LocalSettings_，和跟踪信息要删除的所有版本。

## <a name="api"></a>API

- [版本跟踪源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/VersionTracking)
- [版本跟踪 API 文档](xref:Xamarin.Essentials.VersionTracking)
