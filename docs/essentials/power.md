---
title: Xamarin.Essentials：节能模式状态
description: Power 类允许程序获取节能模式状态来确定设备是否在低功耗模式下运行。
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: jamesmontemagno
ms.author: jamont
ms.date: 06/27/2018
ms.openlocfilehash: 96b4aef3a8df571392d43836d46b03b025c80888
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675375"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials：节能模式状态

![预发行版 NuGet](~/media/shared/pre-release.png)

Power 类提供有关设备的节能模式状态的信息，指示设备是否在低功耗模式下运行。 如果设备的节能模式状态已打开，则应用程序应避免后台处理。

## <a name="background"></a>背景

使用电池运行的设备可以置于低功耗节能模式。 有时，设备会自动切换到此模式，例如，当电池电量降到 20% 以下时。 操作系统通过减少往往会消耗电池的活动来响应节能模式。 打开节能模式时，应用程序有助于避免后台处理或其他高功率活动。

对于 Android 设备，Power 类仅为 Android 版本 5.0 (Lollipop) 及更高版本返回有意义的信息。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-the-power-class"></a>使用 Power 类

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

使用静态 `Power.EnergySaverStatus` 属性获取设备的当前节能状态：

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

此属性会返回 `EnergySaverStatus` 枚举的成员，可以是 `On`、`Off` 或 `Unknown`。 如果该属性返回 `On`，则应用程序应避免后台处理或可能会消耗大量电力的其他活动。

应用程序还应安装事件处理程序。 Power 类会公开节能模式状态发生更改时触发的事件：

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Power.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

如果节能模式状态更改为 `On`，则应用程序应停止执行后台处理。 如果状态更改为 `Unknown` 或 `Off`，则应用程序可以继续执行后台处理。

## <a name="api"></a>API

- [Power 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Power API 文档](xref:Xamarin.Essentials.Power)
