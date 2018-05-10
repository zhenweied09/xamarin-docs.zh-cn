---
title: Xamarin.Essentials 设备显示信息
description: DeviceDisplay 类提供有关设备的屏幕指标应用程序的信息正在其上运行。
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 570fb6cf3eadfbbc7e189f875498d3cea0bc1514
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials 设备显示信息

![预发行 NuGet](~/media/shared/pre-release.png)

**DeviceDisplay**类提供有关设备的屏幕度量值的信息的应用程序运行。

## <a name="using-devicedisplay"></a>使用 DeviceDisplay

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>屏幕度量值

除了基本的设备信息**DeviceDisplay**类包含有关设备的屏幕和方向的信息。

```csharp
// Get Metrics
var metrics = DeviceDisplay.ScreenMetrics;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = metrics.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = metrics.Rotation;

// Width (in pixels)
var width = metrics.Width;

// Height (in pixels)
var height = metrics.Height;

// Screen density
var density = metrics.Density;
```

**DeviceDisplay**类还公开和可订阅触发事件时任何屏幕度量值的更改的事件：

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanaged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChanagedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="api"></a>API

- [DeviceDisplay 源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/DeviceDisplay)
- [DeviceDisplay API 文档](xref:Xamarin.Essentials.DeviceDisplay)
