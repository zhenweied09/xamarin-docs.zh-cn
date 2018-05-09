---
title: Xamarin.Essentials 设备信息
description: DeviceInfo 类提供有关设备应用程序的信息正在其上运行。
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3a9fc352336f67375b732247560f8c1d4dd45f70
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials 设备信息

![预发行 NuGet](~/media/shared/pre-release.png)

**DeviceInfo**类提供有关设备上运行应用程序的信息。

## <a name="using-deviceinfo"></a>使用 DeviceInfo

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过 API 公开了以下信息：

```csharp
// Device Model (SMG-950U)
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

`DeviceInfo.Platform` 对应于常量字符串映射到操作系统。 值可以检查与`Platforms`类：

- **DeviceInfo.Platforms.iOS** – iOS
- **DeviceInfo.Platforms.Android** – Android
- **DeviceInfo.Platforms.UWP** – UWP
- **DeviceInfo.Platforms.Unsupported** – 不受支持

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[习语](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` 关联常量字符串映射到的设备类型应用程序运行。 值可以检查与`Idioms`类：

- **DeviceInfo.Idioms.Phone** – 电话
- **DeviceInfo.Idioms.Tablet** – 平板电脑
- **DeviceInfo.Idioms.Desktop** – 桌面
- **DeviceInfo.Idioms.TV** – 电视
- **DeviceInfo.Idioms.Unsupported** – 不受支持

## <a name="device-type"></a>设备类型

`DeviceInfo.DeviceType` 关联的枚举，以确定该应用程序是否运行在物理上的或虚拟设备。 虚拟设备是模拟器或仿真程序。

## <a name="api"></a>API

- [DeviceInfo 源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/DeviceInfo)
- [DeviceInfo API 文档](xref:Xamarin.Essentials.DeviceInfo)
