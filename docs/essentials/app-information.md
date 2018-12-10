---
title: Xamarin.Essentials：应用信息
description: 本文档介绍 Xamarin.Essentials 中的 AppInfo 类，此类提供应用程序的相关信息。 例如，它会公开应用名称和版本。
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 3e67b605e485b724ec11f2ac94dcf3d1aa77d5cf
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057287"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials：应用信息

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

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

应用信息提取自 `AndroidManifest.xml` 的以下字段：

- 版本 – `manifest` 节点中的 `android:versionCode`
- 名称 - `application` 节点中的 `android:label`
- PackageName：`manifest` 节点中的 `package`
- VersionString – `application` 节点中的 `android:versionName`

# <a name="iostabios"></a>[iOS](#tab/ios)

应用信息提取自 `Info.plist` 的以下字段：

- 版本 – `CFBundleVersion`
- 名称 - `CFBundleDisplayName`如果已设置否则为 `CFBundleName`
- PackageName：`CFBundleIdentifier`
- VersionString – `CFBundleShortVersionString`

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

应用信息提取自 `Package.appxmanifest` 的以下字段：

- 版本 – 使用 `Identity` 节点上 `Version` 中的 `Build`
- 名称 - `Properties` 节点上的 `DisplayName`
- PackageName：`Identity` 节点上的 `Name`
- VersionString – `Identity` 节点上的 `Version`


--------------

## <a name="api"></a>API

- [AppInfo 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 文档](xref:Xamarin.Essentials.AppInfo)
