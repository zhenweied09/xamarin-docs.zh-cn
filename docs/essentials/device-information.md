---
title: Xamarin.Essentials： 设备信息
description: 本文档介绍中 Xamarin.Essentials，提供有关设备上运行应用程序的信息的 DeviceInfo 类。
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 18fe081372cc190e5ead2045f36d63652f8702c3
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353797"
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

`DeviceInfo.DeviceType` 将一个枚举以确定物理或虚拟设备上运行的应用程序相关联。 虚拟设备是模拟器或仿真程序。

## <a name="platform-implementation-specifics"></a>平台实现的细节

# <a name="iostabios"></a>[iOS](#tab/ios)

iOS 不公开的 API 为开发人员若要获取的特定 iOS 设备的名称。 而是如返回硬件标识符_iPhone10，6_指 iPhone X。这些标识符的映射不提供由 Apple，但可能位于[iPhone Wiki](https://www.theiphonewiki.com/wiki/Models) （非官方源源）。

--------------

## <a name="api"></a>API

- [DeviceInfo 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [DeviceInfo API 文档](xref:Xamarin.Essentials.DeviceInfo)
