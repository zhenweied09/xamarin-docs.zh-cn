---
title: 'Xamarin.Essentials: OrientationSensor'
description: OrientationSensor 类可让你监视三维空间中设备的方向。
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: a15338795424885882ed9c86288342d196f6fda2
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353810"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![预发行版 NuGet](~/media/shared/pre-release.png)

**OrientationSensor**类可让你监视的三个维空间中的设备方向。

> [!NOTE]
> 此类是用于确定设备在 3D 空间中的方向。 如果您需要确定设备的视频显示器是处于纵向或横向模式，请使用`Orientation`的属性`ScreenMetrics`从可用对象[ `DeviceDisplay` ](device-display.md)类。

## <a name="using-orientationsensor"></a>使用 OrientationSensor

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

`OrientationSensor`启用通过调用`Start`方法，以监视更改的设备的方向和已禁用通过调用`Stop`方法。 任何更改发送回通过`ReadingChanged`事件。 下面是示例用法：

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(object sender, OrientationSensorChangedEventArgs e)
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

`OrientationSensor` 读数的形式报告回[ `Quaternion` ](xref:System.Numerics.Quaternion)描述设备基于两个三维坐标系统的方向：

设备 （通常在手机或平板电脑） 具有一个 3D 坐标系具有以下轴：

- X 轴点右侧在纵向模式下显示的正数。
- 正 Y 轴指向纵向模式中的设备的顶部。
- 正 Z 轴点移出屏幕。

地球的 3D 坐标系具有以下轴：

- 正 X 轴与地球表面相切和东部点。
- 正 Y 轴还是正切到地球和北部点的图面。
- 正 Z 轴是垂直于地球上的点的面。

`Quaternion`描述相对于地球的坐标系统的设备的坐标系统的旋转。

一个`Quaternion`绕轴旋转到紧密相关值。 如果旋转的轴是规范化的向量 (<sub>x</sub>、 一个<sub>y</sub>、 一个<sub>z</sub>)，并且旋转角度 Θ，则 （X、 Y、 Z、 W） 四元数的组件是：

(<sub>x</sub>·sin(Θ/2)，<sub>y</sub>·sin(Θ/2)，<sub>z</sub>·sin(Θ/2)、 cos(Θ/2))

这些是右侧的坐标系统，因此根手指的曲线具有指向在正向旋转轴的右侧的滚动块，表示正角度的旋转的方向。

示例：

* 如果设备位于平面上表与正面朝上，使用设备 （在纵向模式） 的顶部指向上方，其屏幕进行对齐两个坐标系统。 `Quaternion`值表示 identity 四元数 （0，0，0，1）。 可以相对于此位置分析所有的旋转。

* 当设备位于平放在表上使用正面朝上，其屏幕和西部、 指向设备 （在纵向模式） 的顶部时`Quaternion`值是 （0，0，0.707，0.707）。 设备已旋转 90 度围绕 Z 轴的地球。

* 时，以便向天空中，点的顶部 （以纵向模式） 和设备的背面的人脸北部，设备会保留竖直的情况下，设备已围绕 X 轴的旋转 90 度。 `Quaternion`值是 （0.707，0，0，0.707）。

* 如果设备位于其左边的缘所在的表，因此顶部点上方，设备已被旋转&ndash;90 度绕 Y 轴 （或 90 度围绕负 Y 轴）。 `Quaternion`值是 （0、-0.707、 0、 0.707）。

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [OrientationSensor 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [OrientationSensor API 文档](xref:Xamarin.Essentials.OrientationSensor)
