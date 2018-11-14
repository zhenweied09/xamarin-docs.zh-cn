---
title: Xamarin.Essentials：Preferences
description: 本文档介绍 Xamarin.Essentials 中的 Preferences 类，此类将应用程序首选项保存在键/值存储中。 本文还讨论了如何使用类和可以存储的数据类型。
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3562ec840f824f6a8aeed1a61c7b27985a5ddf72
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675466"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials：Preferences

![预发行版 NuGet](~/media/shared/pre-release.png)

Preferences 类帮助将应用程序首选项存储在键/值存储中。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-preferences"></a>使用 Preferences

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

将给定密匙的值保存在首选项中：

```csharp
Preferences.Set("my_key", "my_value");
```

从首选项检索值或检索默认值（如果未设置）：

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

从首选项删除密钥：

```csharp
Preferences.Remove("my_key");
```

删除所有首选项：

```csharp
Preferences.Clear();
```

除了这些方法外，每个方法都采用一个可选的 `sharedName`，可用于创建首选的其他容器。 查看下面的平台实现细节。

## <a name="supported-data-types"></a>支持的数据类型

以下数据类型在 Preferences 中受到支持：

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="implementation-details"></a>实现详细信息

`DateTime` 的值是使用 `DateTime` 类定义的两种方法以 64 位二进制（长整型）格式存储的：[`ToBinary`](xref:System.DateTime.ToBinary) 方法用于对 `DateTime` 值进行编码，[`FromBinary`](xref:System.DateTime.FromBinary(System.Int64)) 方法对值进行解码。 当存储的 `DateTime` 不是协调世界时 (UTC) 值时，请参阅这些方法的相关文档以了解如何进行对值解码的调整。

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

所有数据都存储到[共享首选项](https://developer.android.com/training/data-storage/shared-preferences.html)中。 如果未指定 `sharedName`，则使用默认的共享首选项，否则此名称将用于获取具有指定名称的私有共享首选项。

# <a name="iostabios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/user-defaults) 用于将值存储在 iOS 设备上。 如果未指定 `sharedName`，则使用 `StandardUserDefaults`，否则此名称将用于创建具有用于 `NSUserDefaultsType.SuiteName` 的指定名称的新 `NSUserDefaults`。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdatacontainer) 用于将值存储在设备上。 如果未指定 `sharedName`，则使用 `LocalSettings`，否则此名称将用于在 `LocalSettings` 内创建新容器。

--------------

## <a name="persistence"></a>持久性

卸载应用程序将导致所有首选项被删除。 对此有一个例外，即面向使用[__自动备份__](https://developer.android.com/guide/topics/data/autobackup)的 Android 6.0（API 级别 23）或更高版本并在其上运行的应用。 此功能默认启用并且会保留应用数据，包括共享首选项（即 Preferences API 使用的内容）。 可以遵循 Google 的[文档](https://developer.android.com/guide/topics/data/autobackup)禁用此功能。

## <a name="limitations"></a>限制

当存储一个字符串时，此 API 用于存储少量文本。  如果尝试将其用于存储大量文本，则可能会导致性能欠佳。

## <a name="api"></a>API

- [Preferences 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [Preferences API 文档](xref:Xamarin.Essentials.Preferences)
