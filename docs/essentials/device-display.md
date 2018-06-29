---
title: Xamarin.Essentials： 设备显示信息
description: 本文档介绍中 Xamarin.Essentials，提供屏幕指标，在其运行应用程序的设备的 DeviceDisplay 类。
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3060d56e14fb0d3801a96ec0fe6e24c9efda4dac
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080308"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials： 设备显示信息

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

**DeviceDisplay**类还公开可订阅，每当任何屏幕度量值的更改就会触发的事件：

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanaged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChangedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="api"></a>API

- [DeviceDisplay 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [DeviceDisplay API 文档](xref:Xamarin.Essentials.DeviceDisplay)
