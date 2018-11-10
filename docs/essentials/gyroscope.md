---
title: Xamarin.Essentials：Gyroscope
description: Xamarin.Essentials 中的 Gyroscope 类使你能够监控设备的陀螺仪传感器，此传感器测量围绕设备三个主轴的旋转。
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 1d42658160855e260e0d159c58a1f95e7a8c7d4c
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674699"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials：Gyroscope

![预发行版 NuGet](~/media/shared/pre-release.png)

Gyroscope 类使你能够监控设备的陀螺仪传感器，此传感器测量围绕设备三个主轴的旋转。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-gyroscope"></a>使用 Gyroscope

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过调用 `Start` 和 `Stop` 方法来使用 Gyroscope 功能以侦听陀螺仪的变化。 然后通过 `ReadingChanged` 事件反馈任何变化。 示例用法如下：

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(object sender, GyroscopeChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Angular Velocity X, Y, and Z
        Console.WriteLine($"Reading: X: {data.AngularVelocity.X}, Y: {data.AngularVelocity.Y}, Z: {data.AngularVelocity.Z}");
    }

    public void ToggleGyroscope()
    {
        try
        {
            if (Gyroscope.IsMonitoring)
              Gyroscope.Stop();
            else
              Gyroscope.Start(speed);
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

## <a name="api"></a>API

- [Gyroscope 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Gyroscope API 文档](xref:Xamarin.Essentials.Gyroscope)
