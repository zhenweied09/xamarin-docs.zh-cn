---
title: Xamarin.Essentials： 首选项
description: 本文档介绍 Xamarin.Essentials，将应用程序首选项保存在键/值存储中的首选项类。 它讨论了如何使用类和可以存储的数据的类型。
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 4a45587c79cfbbcd1198f100915e698289f74950
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353745"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials： 首选项

![预发行版 NuGet](~/media/shared/pre-release.png)

**首选项**类有助于为键/值存储中存储应用程序首选项。

## <a name="using-preferences"></a>使用首选项

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要保存的值给定_密钥_首选项中：

```csharp
Preferences.Set("my_key", "my_value");
```

若要检索首选项或默认值的值，如果未设置：

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

除了这些方法采用可选中`sharedName`可用于创建首选项的更多的容器。 请阅读下面的平台实现细节。

## <a name="supported-data-types"></a>支持的数据类型

中支持以下数据类型**首选项**:

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="implementation-details"></a>实现详细信息

值`DateTime`以使用两种方法定义的 64 位二进制文件 （长整型） 格式存储`DateTime`类： [ `ToBinary` ](xref:System.DateTime.ToBinary)方法用于编码`DateTime`值，以及[ `FromBinary` ](xref:System.DateTime.FromBinary(System.Int64))方法对值进行解码。 可能会为已解码的调整这些方法的文档值时，请参阅`DateTime`是，它是存储不是协调世界时 (UTC) 值。

## <a name="platform-implementation-specifics"></a>平台实现的细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

所有数据都存储到[共享首选项](https://developer.android.com/training/data-storage/shared-preferences.html)。 如果没有`sharedName`共享的默认首选项，否则名称用于获取指定**专用**共享具有指定名称的首选项。

# <a name="iostabios"></a>[iOS](#tab/ios)

[使用 NSUserDefaults](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/user-defaults)用于在 iOS 设备上存储的值。 如果没有`sharedName`指定了`StandardUserDefaults`是使用，否则名称用于创建一个新`NSUserDefaults`具有指定名称用于`NSUserDefaultsType.SuiteName`。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdatacontainer)用于在设备上存储的值。 如果没有`sharedName`指定了`LocalSettings`是使用，否则名称用于创建一个新容器内的`LocalSettings`。

--------------

## <a name="persistence"></a>持久性

卸载应用程序将导致所有_首选项_要删除。 没有为此，其中的应用面向并运行 Android 6.0 （API 级别 23） 或更高版本中使用的一个例外[__自动备份__](https://developer.android.com/guide/topics/data/autobackup)。 此功能在默认情况下，并保留应用程序数据，包括__共享首选项__，这是什么**首选项**API 利用。 您可以禁用此以下 google[文档](https://developer.android.com/guide/topics/data/autobackup)。

## <a name="limitations"></a>限制

在存储一个字符串，此 API 被用于存储少量文本。  性能可能比不上如果尝试使用它来存储大量的文本。

## <a name="api"></a>API

- [首选项的源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [首选项 API 文档](xref:Xamarin.Essentials.Preferences)
