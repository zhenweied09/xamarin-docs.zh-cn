---
title: Xamarin.Essentials 首选项
description: 首选项类将应用程序首选项保存在键/值存储。
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6cca9413cee40fde5b8bb8967db52db7a3a3382f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials 首选项

![预发行 NuGet](~/media/shared/pre-release.png)

**首选项**类可帮助在键/值存储中存储应用程序首选项。

## <a name="using-secure-storage"></a>使用安全存储

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要保存的值给定_密钥_首选项中：

```csharp
Preferences.Set("my_key", "my_value");
```

若要从首选项或默认检索一个值，如果不设置：

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

若要删除_密钥_从首选项：

```csharp
Preferences.Remove("my_key");
```

若要删除所有首选项：

```csharp
Preferences.Clear();
```

除了这些方法每个都采用一个可选中`sharedName`可用来创建其他容器的首选项。 读取以下平台实现细节。

## <a name="supported-data-types"></a>支持的数据类型

中支持以下数据类型**首选项**:

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

所有数据都存储到[共享首选项](https://developer.android.com/training/data-storage/shared-preferences.html)。 如果没有`sharedName`使用共享的默认首选项，否则使用名称来获取指定**私有**共享具有指定名称的首选项。

# <a name="iostabios"></a>[iOS](#tab/ios)

[使用 NSUserDefaults](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/user-defaults)用于 iOS 的设备上存储值。 如果没有`sharedName`指定`StandardUserDefaults`是使用，否则名称用于创建一个新`NSUserDefaults`具有指定名称用于`NSUserDefaultsType.SuiteName`。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdatacontainer)用于将值存储在设备上。 如果没有`sharedName`指定`LocalSettings`是使用，否则名称用于创建一个新容器内的`LocalSettings`。

--------------

## <a name="limitations"></a>限制

此 API 时存储一个字符串，用于存储少量的文本。  性能可能并未达标，如果你尝试使用它来存储大量的文本。

## <a name="api"></a>API

- [首选项源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/Preferences)
- [首选项 API 文档](xref:Xamarin.Essentials.Preferences)
