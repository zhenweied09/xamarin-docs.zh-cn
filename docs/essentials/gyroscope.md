---
title: Xamarin.Essentials 陀螺
description: 陀螺类允许您监视设备的陀螺传感器即围绕设备的三个主轴的旋转角度。
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0fa6ed6a0ced97c2600a24860c4f42aee1a24161
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials 陀螺

![预发行 NuGet](~/media/shared/pre-release.png)

**陀螺**类允许您监视设备的陀螺传感器即围绕设备的三个主轴的旋转角度。

## <a name="using-gyroscope"></a>使用陀螺

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

陀螺功能的工作方式是调用`Start`和`Stop`方法来侦听对陀螺更改。 任何更改发送回通过`ReadingChanged`事件。 下面是示例用法：

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(GyroscopeChangedEventArgs e)
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

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[传感器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 尽可能 （但不保证在 UI 线程上返回） 快获取传感器数据。
- **游戏**– 速率适用于 （但不保证在 UI 线程上返回） 的游戏。
- **正常**– 适用于屏幕方向更改的默认速率。
- **Ui** – 速率适用于常规用户界面。

## <a name="api"></a>API

- [陀螺源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/Gyroscope)
- [陀螺 API 文档](xref:Xamarin.Essentials.Gyroscope)
