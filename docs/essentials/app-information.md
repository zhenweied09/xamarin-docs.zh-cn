---
title: Xamarin.Essentials： 应用程序信息
description: 本文档介绍中 Xamarin.Essentials，提供有关你的应用程序的信息的 AppInfo 类。 例如，它公开的应用程序名称和版本。
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7e79b3003f41b8de22950624e44e8c9e0e7e7e31
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831501"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials： 应用程序信息

![预发行版 NuGet](~/media/shared/pre-release.png)

**AppInfo**类提供了有关你的应用程序的信息。

## <a name="using-appinfo"></a>使用 AppInfo

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>获取应用程序的信息：

通过 API 公开了以下信息：

```csharp
// Application Name
var appName = AppInfo.Name;

// Package Name/Application Identifier (com.microsoft.testapp)
var packageName = AppInfo.PackageName;

// Application Version (1.0.0)
var version = AppInfo.VersionString;

// Application Build Number (1)
var build = AppInfo.BuildString;
```

## <a name="displaying-application-settings"></a>显示应用程序设置

**AppInfo**类也可以显示由适用于应用程序维护的设置页：

```csharp
// Display settings page
AppInfo.OpenSettings();
```

此设置页允许用户更改应用程序权限，以及执行其他特定于平台的任务。

## <a name="api"></a>API

- [AppInfo 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 文档](xref:Xamarin.Essentials.AppInfo)
