---
title: Xamarin.Essentials：设备显示信息
description: 本文档介绍 Xamarin.Essentials 中的 DeviceDisplay 类，此类提供运行应用程序的设备的屏幕指标。
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ebe97cf7fbb78bff17196110e835bd35ff76b826
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674881"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials：设备显示信息

![预发行版 NuGet](~/media/shared/pre-release.png)

DeviceDisplay 类提供有关运行应用程序的设备的屏幕指标信息。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>使用 DeviceDisplay

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>屏幕指标

除了基本的设备信息外，DeviceDisplay 类还包含有关设备的屏幕和方向信息。

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

DeviceDisplay 类还会公开可以订阅的一个事件，每当任何屏幕指标更改时就会触发此事件：

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

## <a name="platform-differences"></a>平台差异

# <a name="androidtabandroid"></a>[Android](#tab/android)

没有差异。

# <a name="iostabios"></a>[iOS](#tab/ios)

* 必须在 UI 线程上访问 `ScreenMetrics`，否则将引发异常。 可以使用 [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) 方法在 UI 线程上运行该代码。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

没有差异。

--------------


## <a name="api"></a>API

- [DeviceDisplay 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [DeviceDisplay API 文档](xref:Xamarin.Essentials.DeviceDisplay)
