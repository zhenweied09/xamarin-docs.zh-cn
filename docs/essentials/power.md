---
title: Xamarin.Essentials： 电源能源保护程序状态
description: Power 类允许要获取的能源节省状态来确定设备是否在低功耗模式下运行的程序。
ms.assetid: C176D177-8B77-4A9C-9F3B-27852A8DCD5F
author: charlespetzold
ms.author: chape
ms.date: 06/27/2018
ms.openlocfilehash: 760a305280269734034a817182a8c2a07894ca2b
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353485"
---
# <a name="xamarinessentials-power-energy-saver-status"></a>Xamarin.Essentials： 电源能源保护程序状态

![预发行版 NuGet](~/media/shared/pre-release.png)

**电源**类提供了有关设备的能源节省状态，指示设备是否正在运行在低功耗模式下的信息。 如果设备的能源节省状态上，应用程序应避免后台处理。

## <a name="background"></a>背景

使用电池运行的设备可以置于低功耗精力模式。 有时设备是此模式时会自动切换，例如，电池电量降至低于 20%的容量。 操作系统响应能源节省模式通过减少往往会消耗电池的活动。 通过避免后台处理或其他高功率设备的活动，在打开能源节省模式时，可帮助应用程序。

适用于 Android 设备**电源**类返回有意义的信息仅为 Android 版本 5.0 (Lollipop) 及更高版本。

## <a name="using-the-power-class"></a>使用 Power 类

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

获取当前的能源保护程序状态的设备使用静态`Power.EnergySaverStatus`属性：

```csharp
// Get energy saver status
var status = Power.EnergySaverStatus;
```

此属性返回的成员`EnergySaverStatus`枚举，它可以是`On`， `Off`，或`Unknown`。 如果属性返回`On`，应用程序应避免后台处理或可能会消耗提供了强大的其他活动。

应用程序还应安装一个事件处理程序。 **电源**类公开的能源节省状态发生更改时触发的事件：

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

如果能源保护程序状态将更改为`On`，应用程序应停止执行后台处理。 如果状态将变为`Unknown`或`Off`，应用程序可以继续在后台处理。

## <a name="api"></a>API

- [Power 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Power)
- [Power API 文档](xref:Xamarin.Essentials.Power)
