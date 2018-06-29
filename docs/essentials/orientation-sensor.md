---
title: 'Xamarin.Essentials: OrientationSensor'
description: OrientationSensor 类允许您监视三维空间中的设备的方向。
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: c7bbc849e5fa0b901f5b54e77d548b28bc2a72c6
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080439"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![预发行 NuGet](~/media/shared/pre-release.png)

**OrientationSensor**类允许您监视的三个维空间中的设备方向。

> [!NOTE]
> 此类是设备的用于确定在三维空间中的方向。 如果你需要确定设备的视频显示纵向或横向模式中，使用`Orientation`属性`ScreenMetrics`对象可从[ `DeviceDisplay` ](device-display.md)类。

## <a name="using-orientationsensor"></a>使用 OrientationSensor

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

`OrientationSensor`通过调用`Start`方法通过调用监视更改设备的方向和已禁用`Stop`方法。 任何更改发送回通过`ReadingChanged`事件。 下面是示例用法：

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Orientation.X}, Y: {data.Orientation.Y}, Z: {data.Orientation.Z}, W: {data.Orientation.W}");
        // Process Orientation quaternion (X, Y, Z, and W)
    }

    public void ToggleOrientationSensor()
    {
        try
        {
            if (OrientationSensor.IsMonitoring)
                OrientationSensor.Stop();
            else
                OrientationSensor.Start(speed);
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

`OrientationSensor` 读取的形式报告回[ `Quaternion` ](xref:System.Numerics.Quaternion)描述基于两个 3D 坐标系统的设备的方向：

设备 （通常手机或平板电脑） 具有三维坐标系具有以下轴：

- 使用 X 轴的显示中纵向模式右侧的点的正数。
- Y 轴的正指向纵向模式中的设备的顶部。
- 正的 Z 轴点外屏幕。

地球 3D 坐标系具有以下轴：

- 使用的正数 X 轴与地球表面相切并且东部点。
- 正 Y 轴还有切线到地球和北部点的图面。
- 正的 Z 轴是垂直到地球和注册点的图面。

`Quaternion`描述相对于地球坐标系的设备的坐标系统的旋转角度。

A`Quaternion`与轴的旋转非常密切相关值。 如果旋转的轴是规范化的向量 (<sub>x</sub>、<sub>y</sub>、<sub>z</sub>)，和的旋转角度是 Θ，则 （X、 Y、 Z 和 W） 四元数的组件是：

(<sub>x</sub>·sin(Θ/2)，<sub>y</sub>·sin(Θ/2)，<sub>z</sub>·sin(Θ/2)、 cos(Θ/2))

这些是右侧坐标系统，因此使用的方向旋转轴的正向右滚动块，指的曲线指示的方向旋转为正的角度。

示例：

* 当其正面朝上与指向上方，（中纵向模式） 的设备顶部屏幕设备在于平面上表对齐两个坐标系统。 `Quaternion`值表示 identity 四元数 （0，0，0，1）。 所有旋转可以相对于此位置进行都分析。

* 当设备位于平面对表使用其正面朝上的屏幕和西部、 指向 （中纵向模式） 的设备顶部时`Quaternion`值 （0、 0、 0.707、 0.707）。 设备已旋转 90 度围绕 Z 轴地球。

* 当设备持有竖向，以便向 sky，点 （中纵向模式） 的顶部和设备的背面北部面向时，设备已围绕 X 轴的旋转 90 度。 `Quaternion`值 （0.707、 0、 0、 0.707）。

* 如果设备位于其左边的缘位于一个表，从而顶部点北部，设备已转动&ndash;90 度围绕 Y 轴 （或围绕负的 Y 轴的 90 度）。 `Quaternion`值 （0、-0.707、 0、 0.707）。

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[传感器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 尽可能 （但不保证在 UI 线程上返回） 快获取传感器数据。
- **游戏**– 速率适用于 （但不保证在 UI 线程上返回） 的游戏。
- **正常**– 适用于屏幕方向更改的默认速率。
- **Ui** – 速率适用于常规用户界面。

如果事件处理程序不能保证在 UI 线程上运行和事件处理程序需要访问用户界面元素，如果使用[ `MainThread.BeginInvokeOnMainThread` ](main-thread.md)方法在 UI 线程上运行该代码。

## <a name="api"></a>API

- [OrientationSensor 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [OrientationSensor API 文档](xref:Xamarin.Essentials.OrientationSensor)
