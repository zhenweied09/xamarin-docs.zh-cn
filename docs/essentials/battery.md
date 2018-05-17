---
title: Xamarin.Essentials 电池
description: 电池类允许你检查设备的电池信息和监视的更改。
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 984f6f5eeeba3d5c8162ab6ce0c83dbf03981b5f
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials 电池

![预发行 NuGet](~/media/shared/pre-release.png)

**电池**类允许你检查设备的电池信息和监视的更改。

## <a name="getting-started"></a>入门

访问**电池**功能以下平台特定的安装程序是必需的。

# <a name="androidtabandroid"></a>[Android](#tab/android)

`Battery`权限是必需的必须在 Android 项目中配置。 这可以通过以下方式添加：

打开**AssemblyInfo.cs**文件下**属性**文件夹并添加：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Battery)]
```

或更新 Android 清单：

打开**AndroidManifest.xml**文件下**属性**文件夹并添加以下内的**清单**节点。

```xml
<uses-permission android:name="android.permission.BATTERY" />
```

或右键单击 Anroid 项目并打开项目的属性。 下**Android 清单**查找**所需的权限：** 区域并检查**电池**权限。 这将自动更新**AndroidManifest.xml**文件。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他的安装程序。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不需要其他的安装程序。

-----

## <a name="using-battery"></a>使用电池

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

检查当前电池信息：

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or -1.0 if unable to determine.

var state = Battery.State;

switch (state)
{
    case BatteryState.Charging:
        // Currently charging
        break;
    case BatteryState.Full:
        // Battery is full
        break;
    case BatteryState.Discharging:
    case BatteryState.NotCharging:
        // Currently discharging battery or not being charged
        break;
    case BatteryState.NotPresent:
        // Battery doesn't exist in device (desktop computer)
    case BatteryState.Unknown:
        // Unable to detect battery state
        break;
}

var source = Battery.PowerSource;

switch (source)
{
    case BatteryPowerSource.Battery:
        // Being powered by the battery
        break;
    case BatteryPowerSource.Ac:
        // Being powered by A/C unit
        break;
    case BatteryPowerSource.Usb:
        // Being powered by USB cable
        break;
    case BatteryPowerSource.Wireless:
        // Powered via wireless charging
        break;
    case BatteryPowerSource.Unknown:
        // Unable to detect power source
        break;
}
```

每当时将触发任何电池的属性更改事件：

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryChanged += Battery_BatteryChanged;
    }

    void Battery_BatteryChanged(BatteryChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

## <a name="platform-differences"></a>平台的差异

| 平台 | 差异 |
| --- | --- |
| iOS | 设备必须用于测试 Api。 |
| iOS | 仅将返回 Ac 或电池 PowerSource。 |
| UWP | 仅将返回 Ac 或电池 PowerSource。 |

## <a name="api"></a>API

- [电池源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [电池 API 文档](xref:Xamarin.Essentials.Battery)
