---
title: Xamarin.Essentials： 加速
description: Xamarin.Essentials 中的加速类允许您监视设备的加速传感器，该值指示在三个维空间中设备的加速。
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 99529f08348254dff7577b7e82da739fabd63a14
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781860"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials： 加速

![预发行 NuGet](~/media/shared/pre-release.png)

**加速**类允许您监视设备的加速传感器指示的三个维空间中的设备以加速。

## <a name="using-accelerometer"></a>使用加速

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

加速功能的工作方式是调用`Start`和`Stop`方法来侦听加速的更改。 任何更改发送回通过`ReadingChanged`事件。 下面是示例用法：

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

加速读数将报告中。A G 是一套万有引力强制等于该地球引力字段所施加 (9.81 m/s ^2)。

坐标系统定义相对于其默认方向的电话屏幕。 设备的屏幕方向更改时，不会交换轴。

X 轴为水平方向和右侧的点，Y 轴为垂直向上箭头和 Z 轴指向屏幕的正面的外部。 在此系统中，屏幕后台坐标具有负的 Z 值。

示例：

* 如果设备位于平面上一个表，并在其左侧向右推送，x 加速值为正。

* 加速值时设备位于平面上表中，为 + 1.00 G 或 (+ 9.81 m/s ^2)，这对应于设备的加速 (0 的 m s ^2) 减去引力 (-9.81 m/s ^2) 和规范化如下所示 g。

* 当设备位于平面上一个表并向使用 m/s 加速 sky 推送 ^2，加速值是否等于 + 9.81 它们分别对应于设备的加速 (+ m/s ^2) 引力减号 (-9.81 m/s ^2) 和规范化中。 

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[传感器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 尽可能 （但不保证在 UI 线程上返回） 快获取传感器数据。
- **游戏**– 速率适用于 （但不保证在 UI 线程上返回） 的游戏。
- **正常**– 适用于屏幕方向更改的默认速率。
- **Ui** – 速率适用于常规用户界面。

## <a name="api"></a>API

- [加速源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [加速 API 文档](xref:Xamarin.Essentials.Accelerometer)
