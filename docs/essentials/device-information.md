---
title: Xamarin.Essentials：设备信息
description: 本文档介绍 Xamarin.Essentials 中的 DeviceInfo 类，此类提供有关运行应用程序的设备的信息。
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 530b04446703d78452357b2c9f9089e59ebf6e6c
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674808"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials：设备信息

![预发行版 NuGet](~/media/shared/pre-release.png)

DeviceInfo 类提供有关运行应用程序的设备的信息。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-deviceinfo"></a>使用 DeviceInfo

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过 API 公开了以下信息：

```csharp
// Device Model (SMG-950U, iPhone10,6)
var device = DeviceInfo.Model;

// Manufacturer (Samsung)
var manufacturer = DeviceInfo.Manufacturer;

// Device Name (Motz's iPhone)
var deviceName = DeviceInfo.Name;

// Operating System Version Number (7.0)
var version = DeviceInfo.VersionString;

// Platform (Android)
var platform = DeviceInfo.Platform;

// Idiom (Phone)
var idiom = DeviceInfo.Idiom;

// Device Type (Physical)
var deviceType = DeviceInfo.DeviceType;
```

## <a name="platformsxrefxamarinessentialsdeviceinfoplatforms"></a>[平台](xref:Xamarin.Essentials.DeviceInfo.Platforms)

`DeviceInfo.Platform` 与映射到操作系统的一个常量字符串相关联。 可以使用 `Platforms` 类检查以下值：

- **DeviceInfo.Platforms.iOS** - iOS
- **DeviceInfo.Platforms.Android** - Android
- **DeviceInfo.Platforms.UWP** - UWP
- **DeviceInfo.Platforms.Unsupported** - 不受支持

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[习惯用语](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` 与映射到运行应用程序的设备类型的一个常量字符串相关联。 可以使用 `Idioms` 类检查以下值：

- **DeviceInfo.Idioms.Phone** - 手机
- **DeviceInfo.Idioms.Tablet** - 平板电脑
- **DeviceInfo.Idioms.Desktop** - 桌面
- **DeviceInfo.Idioms.TV** - 电视
- **DeviceInfo.Idioms.Unsupported** - 不受支持

## <a name="device-type"></a>设备类型

`DeviceInfo.DeviceType` 关联一个枚举以确定应用程序是在物理设备还是虚拟设备上运行。 虚拟设备是指模拟器或仿真程序。

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="iostabios"></a>[iOS](#tab/ios)

iOS 不会向开发人员公开一个 API 来获取特定 iOS 设备的名称。 而是会返回一个硬件标识符，例如 iPhone10,6，这是指这些标识符的 iPhone X. A 映射不是由 Apple 提供的，但可以在 [The iPhone Wiki](https://www.theiphonewiki.com/wiki/Models)（一个非官方来源）上找到。

--------------

## <a name="api"></a>API

- [DeviceInfo 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [DeviceInfo API 文档](xref:Xamarin.Essentials.DeviceInfo)
