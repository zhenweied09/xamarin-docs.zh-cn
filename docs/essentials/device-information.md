---
title: Xamarin.Essentials： 设备信息
description: 本文档介绍中 Xamarin.Essentials，提供有关设备上运行应用程序的信息的 DeviceInfo 类。
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b7246afca19607ef2f70288d4643696f4ac35d52
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831482"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials： 设备信息

![预发行版 NuGet](~/media/shared/pre-release.png)

**DeviceInfo**类提供了有关设备上运行应用程序的信息。

## <a name="using-deviceinfo"></a>使用 DeviceInfo

在类中添加对 Xamarin.Essentials 的引用：

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

`DeviceInfo.Platform` 将关联到映射到操作系统的常量字符串。 可以使用检查值`Platforms`类：

- **DeviceInfo.Platforms.iOS** – iOS
- **DeviceInfo.Platforms.Android** -Android
- **DeviceInfo.Platforms.UWP** – UWP
- **DeviceInfo.Platforms.Unsupported** – 不受支持

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[习惯用语](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` 关联应用程序映射到的设备类型的常量字符串上运行。 可以使用检查值`Idioms`类：

- **DeviceInfo.Idioms.Phone** – 电话
- **DeviceInfo.Idioms.Tablet** – 平板电脑
- **DeviceInfo.Idioms.Desktop** -桌面
- **DeviceInfo.Idioms.TV** – 电视
- **DeviceInfo.Idioms.Unsupported** – 不受支持

## <a name="device-type"></a>设备类型

`DeviceInfo.DeviceType` 将枚举来确定该应用程序是否运行在物理或虚拟设备相关联。 虚拟设备是模拟器或仿真程序。

## <a name="api"></a>API

- [DeviceInfo 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [DeviceInfo API 文档](xref:Xamarin.Essentials.DeviceInfo)
