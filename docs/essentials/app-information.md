---
title: Xamarin.Essentials：应用信息
description: 本文档介绍 Xamarin.Essentials 中的 AppInfo 类，此类提供应用程序的相关信息。 例如，它会公开应用名称和版本。
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 62ecf890ba6b3276db89e93c2699e0406dbe4d45
ms.sourcegitcommit: a635312ffec816ba357a92b66c8c5221c8d9044c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50965612"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials：应用信息

![预发行版 NuGet](~/media/shared/pre-release.png)

AppInfo 类提供应用程序的相关信息。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-appinfo"></a>使用 AppInfo

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>获取应用程序信息：

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

AppInfo 类还可以显示由操作系统为应用程序维护的设置页面：

```csharp
// Display settings page
AppInfo.ShowSettingsUI();
```

此设置页面使用户能够更改应用程序权限，并执行其他特定于平台的任务。

## <a name="api"></a>API

- [AppInfo 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 文档](xref:Xamarin.Essentials.AppInfo)
