---
title: Xamarin.Essentials： 陀螺
description: 中 Xamarin.Essentials 的陀螺类允许您监视设备的陀螺传感器，测量设备的三个主轴的旋转。
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6b2df3b6c5061464a06730ad09cbbbfdbceeb247
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080447"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials： 陀螺

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

如果事件处理程序不能保证在 UI 线程上运行和事件处理程序需要访问用户界面元素，如果使用[ `MainThread.BeginInvokeOnMainThread` ](main-thread.md)方法在 UI 线程上运行该代码。

## <a name="api"></a>API

- [陀螺源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [陀螺 API 文档](xref:Xamarin.Essentials.Gyroscope)
