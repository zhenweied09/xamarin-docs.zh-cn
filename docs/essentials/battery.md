---
title: Xamarin.Essentials：Battery
description: 本文档介绍 Xamarin.Essentials 中的 Battery 类，此类使你能够查看设备的电池信息并监视更改。
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6a14c939064538a405a1fe64061e0bb2e903fedd
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675427"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials：Battery

![预发行版 NuGet](~/media/shared/pre-release.png)

Battery 类使你能够查看设备的电池信息并监视更改。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问 Battery 功能，需要以下特定于平台的设置。

# <a name="androidtabandroid"></a>[Android](#tab/android)

需要具有 `Battery` 权限，并且必须在 Android 项目中进行配置。 可以通过以下方法添加此权限：

打开 Properties 文件夹下的 AssemblyInfo.cs 文件并添加：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

或更新 Android 清单：

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码。

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

或右键单击 Android 项目并打开项目的属性。 在“Android 清单”下找到“所需权限:”区域，然后选中“Battery”权限。 这样会自动更新 AndroidManifest.xml 文件。

# <a name="iostabios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无需其他设置。

-----

## <a name="using-battery"></a>使用 Battery

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

查看当前的电池信息：

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
    case BatteryPowerSource.AC:
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

每当电池的任一属性发生更改时，将触发一个事件：

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryChanged += Battery_BatteryChanged;
    }

    void Battery_BatteryChanged(object sender, BatteryChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

## <a name="platform-differences"></a>平台差异

# <a name="androidtabandroid"></a>[Android](#tab/android)

无平台差异。

# <a name="iostabios"></a>[iOS](#tab/ios)

* 必须使用设备来测试 API。 
* 将仅为 `PowerSource` 返回 `AC` 或 `Battery`。
* 无法取消振动。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* 将仅为 `PowerSource` 返回 `AC` 或 `Battery`。

-----

## <a name="api"></a>API

- [Battery 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Battery API 文档](xref:Xamarin.Essentials.Battery)
