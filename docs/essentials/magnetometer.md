---
title: Xamarin.Essentials： 磁力计
description: 中 Xamarin.Essentials 的磁力计类允许您监视设备的磁力计传感器，指示相对于地球磁字段与设备方向。
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2c02188b5282949559e0abc5fa1b61b6b451fc8e
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080437"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials： 磁力计

![预发行 NuGet](~/media/shared/pre-release.png)

**磁力计**类允许您监视设备的磁力计传感器指示相对于地球磁字段与设备方向。

## <a name="using-magnetometer"></a>使用磁力计

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

磁力计功能的工作方式是调用`Start`和`Stop`方法来侦听到磁力计的更改。 任何更改发送回通过`ReadingChanged`事件。 下面是示例用法：

```csharp

public class MagnetometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometerr_ReadingChanged(MagnetometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process MagneticField X, Y, and Z
        Console.WriteLine($"Reading: X: {data.MagneticField.X}, Y: {data.MagneticField.Y}, Z: {data.MagneticField.Z}");
    }

    public void ToggleMagnetometer()
    {
        try
        {
            if (Magnetometer.IsMonitoring)
              Magnetometer.Stop();
            else
              Magnetometer.Start(speed);
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

在 microteslas 中返回所有数据。

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[传感器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 尽可能 （但不保证在 UI 线程上返回） 快获取传感器数据。
- **游戏**– 速率适用于 （但不保证在 UI 线程上返回） 的游戏。
- **正常**– 适用于屏幕方向更改的默认速率。
- **Ui** – 速率适用于常规用户界面。

如果事件处理程序不能保证在 UI 线程上运行和事件处理程序需要访问用户界面元素，如果使用[ `MainThread.BeginInvokeOnMainThread` ](main-thread.md)方法在 UI 线程上运行该代码。

## <a name="api"></a>API

- [磁力计源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Magnetometer)
- [磁力计 API 文档](xref:Xamarin.Essentials.Magnetometer)
