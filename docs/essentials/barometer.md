---
title: Xamarin.Essentials：Barometer
description: Xamarin.Essentials 中的 Barometer 类可用于监视设备的气压计传感器，该传感器可测量压力。
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 08/16/2018
ms.openlocfilehash: 0217b89bc3f45347acecbdb3b8cdccfeed751744
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130292"
---
# <a name="xamarinessentials-barometer"></a>Xamarin.Essentials：Barometer

![预发行版 NuGet](~/media/shared/pre-release.png)

Barometer 类可用于监视设备的气压计传感器，该传感器可测量压力。

## <a name="using-barometer"></a>使用 Barometer

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

Barometer 功能通过调用 `Start` 和 `Stop` 方法来侦听气压计压力读数的变化（以千帕为单位）。 然后通过 `ReadingChanged` 事件反馈任何变化。 示例用法如下：

```csharp

public class BarometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public BarometerTest()
    {
        // Register for reading changes.
        Barometer.ReadingChanged += Barometer_ReadingChanged;
    }

    void Barometer_ReadingChanged(object sender, BarometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Pressure
        Console.WriteLine($"Reading: Pressure: {data.Pressure} kilopascals");
    }

    public void ToggleBarometer()
    {
        try
        {
            if (Barometer.IsMonitoring)
              Barometer.Stop();
            else
              Barometer.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

无特定于平台的实现细节。

# <a name="iostabios"></a>[iOS](#tab/ios)

此 API 使用 [CMAltimeter](https://developer.apple.com/documentation/coremotion/cmaltimeter#//apple_ref/occ/cl/CMAltimeter) 监视压力变化，这是已添加到 iPhone 6 及更高版本设备的硬件功能。 不支持高度计的设备将引发 `FeatureNotSupportedException`。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无特定于平台的实现细节。

-----

## <a name="api"></a>API

- [Barometer 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Barometer)
- [Barometer API 文档](xref:Xamarin.Essentials.Barometer)
