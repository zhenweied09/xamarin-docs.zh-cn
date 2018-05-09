---
title: Xamarin.Essentials 应用程序信息
description: AppInfo 类提供有关你的应用程序信息。
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 5b235fdeb666c3f8f2c1b52a9691c931724ce2b8
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials 应用程序信息

![预发行 NuGet](~/media/shared/pre-release.png)

**AppInfo**类提供有关你的应用程序的信息。

## <a name="using-appinfo"></a>使用应用程序信息

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

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

## <a name="api"></a>API

- [AppInfo 源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/AppInfo)
- [AppInfo API 文档](xref:Xamarin.Essentials.AppInfo)
