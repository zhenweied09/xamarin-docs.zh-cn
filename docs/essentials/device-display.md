---
title: Xamarin.Essentials： 设备显示信息
description: 本文档介绍中 Xamarin.Essentials，提供在其运行应用程序的设备的屏幕指标的 DeviceDisplay 类。
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: cb42da4c8c2d0e381a5b00f7e60da6f427d19c66
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353823"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials： 设备显示信息

![预发行版 NuGet](~/media/shared/pre-release.png)

**DeviceDisplay**类提供有关设备的屏幕指标上运行应用程序的信息。

## <a name="using-devicedisplay"></a>使用 DeviceDisplay

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>屏幕指标

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

**DeviceDisplay**类还公开可以订阅任何屏幕上的度量值更改时触发的事件：

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanged += OnScreenMetricsChanged;
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
