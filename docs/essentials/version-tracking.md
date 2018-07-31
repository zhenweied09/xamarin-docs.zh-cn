---
title: Xamarin.Essentials： 版本跟踪
description: 在 Xamarin.Essentials VersionTracking 类允许您检查应用程序版本和生成号以及出现此类的其他信息，如同它是第一个时间不断启动应用程序，或有关最新版本，获取上一个生成信息和详细信息。
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 81dc67fa5a4975f31d0fbf9f7219637596a827ce
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353654"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials： 版本跟踪

![预发行版 NuGet](~/media/shared/pre-release.png)

**VersionTracking**类允许你检查应用程序版本和生成号以及出现此类的其他信息，如同它是第一个时间不断启动应用程序，或有关最新版本，获取上一个生成信息、 和的详细信息。

## <a name="using-version-tracking"></a>使用版本跟踪

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

首次使用**VersionTracking**类，它将开始跟踪最新版本。 必须调用`Track`早期仅在你的应用程序每次加载以确保跟踪当前的版本信息：

```csharp
VersionTracking.Track();
```

首字母后`Track`调用可以读取版本信息：

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

## <a name="platform-implementation-specifics"></a>平台实现的细节

使用存储的所有版本信息[首选项](preferences.md)Xamarin.Essentials 中的 API 和都存储的文件名 **[您的应用包的 ID].xamarinessentials.versiontracking**并遵循相同数据暂留中所述[首选项](preferences.md#persistence)文档。

## <a name="api"></a>API

- [版本跟踪源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/VersionTracking)
- [版本跟踪 API 文档](xref:Xamarin.Essentials.VersionTracking)
