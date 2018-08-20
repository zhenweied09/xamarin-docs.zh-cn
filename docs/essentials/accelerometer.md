---
title: Xamarin.Essentials：Accelerometer
description: Xamarin.Essentials 中的 Accelerometer 类可用于监视设备的加速计传感器，指示设备在三维空间内的加速度。
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 53e7ca70184270662d27043387da836ad44432fe
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2018
ms.locfileid: "40184424"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials：Accelerometer

![预发行版 NuGet](~/media/shared/pre-release.png)

Accelerometer 类可用于监视设备的加速计传感器，指示设备在三维空间内的加速度。

## <a name="using-accelerometer"></a>使用 Accelerometer

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

Accelerometer 功能通过调用 `Start` 和 `Stop` 方法来侦听加速的变化。 然后通过 `ReadingChanged` 事件反馈任何变化。 示例用法如下：

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(object sender, AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Acceleration.X}, Y: {data.Acceleration.Y}, Z: {data.Acceleration.Z}");
        // Process Acceleration X, Y, and Z
    }

    public void ToggleAccelerometer()
    {
        try
        {
            if (Accelerometer.IsMonitoring)
              Accelerometer.Stop();
            else
              Accelerometer.Start(speed);
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

Accelerometer 读数以 G 为单位反馈回来。G 是等于地球重力加速度 (9.81 m/s^2) 的重力单位。

相对于手机屏幕的默认方向定义坐标系。 设备的屏幕方向更改时，不会交换轴。

X 轴水平向右，Y 轴垂直向上，Z 轴从屏幕正面指向外。 在此坐标系中，屏幕后方的坐标具有负 Z 值。

示例：

* 当设备平放在桌面上，并从左侧向右推时，X 轴加速值为正。

* 当设备平放在桌面上，加速值为 +1.00 G (+ 9.81 m/s^2)，对应于设备的加速度 (0 m/s^2) 减去重力加速度 (-9.81 m/s ^2)，以 G 为单位规范化。

* 当设备平放在桌面上，并以 A m/s^2 的加速度向上推时，加速值等于 A+9.81，对应于设备的加速度 (+A m/s^2) 减去重力加速度 (-9.81 m/s ^2)，并以 G 为单位规范化。

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Accelerometer 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Accelerometer API 文档](xref:Xamarin.Essentials.Accelerometer)
