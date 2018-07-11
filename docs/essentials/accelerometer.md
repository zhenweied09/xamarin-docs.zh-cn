---
title: Xamarin.Essentials： 加速感应器
description: Xamarin.Essentials 中的加速感应器类可让你监视设备的加速度传感器，指示三个维空间中的设备的加速度。
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 15e2cb69806f281e88e226b7bcd87a20e149d508
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947304"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials： 加速感应器

![预发行版 NuGet](~/media/shared/pre-release.png)

**加速感应器**类可让你监视设备的加速度传感器指示三个维空间中的设备的加速度。

## <a name="using-accelerometer"></a>使用加速感应器

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

加速感应器功能适用于通过调用`Start`和`Stop`方法来侦听对加速的更改。 任何更改发送回通过`ReadingChanged`事件。 下面是示例用法：

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Acceleration.X}, Y: {data.Acceleration.Y}, Z: {data.Acceleration.Z}");
        // Process Acceleration X, Y, and Z
    }

    public void ToggleAcceleromter()
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

加速感应器读数报告回中。G 是万有引力的一项强制等于所施加的地球引力字段 (9.81 m/s ^2)。

坐标系统定义相对于其默认方向在手机的屏幕。 设备的屏幕方向更改时，不会交换轴。

X 轴是水平的并且点向右，Y 轴为垂直和向上箭头和 Z 轴指向屏幕的正面的外部。 在此系统中，隐藏在屏幕坐标具有负 Z 值。

示例：

* 当设备位于平面上一个表，并在其左侧向右推入时，x 加速值为正。

* 当设备位于平面上一个表时，加速值是 + 1.00 之间 G 或 (+ 9.81 m/s ^2)，对应于设备的加速度 (0 m/s ^2) 减去引力 (-9.81 m/s ^2) 和如下所示 G.规范化

* 当设备位于平面上一个表并被推向 m/秒的加速天空 ^2，加速值是否等于一个 + 9.81 它们分别对应于设备的加速度 (+ m/s ^2) 的重力强制减 (-9.81 m/s ^2) 和规范化中。 

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [加速感应器源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [加速感应器 API 文档](xref:Xamarin.Essentials.Accelerometer)
