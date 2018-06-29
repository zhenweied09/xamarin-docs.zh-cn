---
title: Xamarin.Essentials： 电源能量保护程序状态
description: Power 类允许程序来获取要确定设备是否在低功耗模式下运行的能量保护程序状态。
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: charlespetzold
ms.author: chape
ms.date: 06/27/2018
ms.openlocfilehash: 6d8ccb5be69eb1ea7ea63d3f5c373d9284089679
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080446"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials： 电源能量保护程序状态

![预发行 NuGet](~/media/shared/pre-release.png)

**Power**类提供有关设备的能量保护程序状态，指示设备是否正在运行在低功耗模式下的信息。 如果设备的能量保护程序状态位于应用程序应避免后台处理。

## <a name="background"></a>背景

在电池运行的设备可以放入低功耗能量保护程序模式。 有时设备会自动切换为进入此模式，例如，电池低于 20%的容量时。 操作系统响应能量保护模式通过减少往往会消耗电池的活动。 应用程序可帮助避免后台处理或其他高功率设备的活动上能量保护模式时。

对于 Android 设备， **Power**类返回有意义的信息仅为 Android 版本 5.0 （棒糖形） 和更高版本。

## <a name="using-the-power-class"></a>使用 Power 类

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

获取当前的能量保护程序状态的设备使用静态`Power.EnergySaverStatus`属性：

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

此属性返回的成员`EnergySaverStatus`枚举，它可以是`On`， `Off`，或`Unknown`。 如果该属性返回`On`，应用程序应避免后台处理或其他可能消耗大量的电源的活动。

应用程序还应安装一个事件处理程序。 **Power**类公开的能量保护程序状态发生更改时触发的事件：

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Power.EnergySaverStatusChanaged += OnEnergySaverStatusChanaged;
    }

    private void OnEnergySaverStatusChanaged(EnergySaverStatusChanagedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

如果能量保护程序状态将更改为`On`，应用程序应停止执行后台处理。 如果状态将更改为`Unknown`或`Off`，应用程序可以继续在后台处理。

## <a name="api"></a>API

- [Power 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Power API 文档](xref:Xamarin.Essentials.Power)
