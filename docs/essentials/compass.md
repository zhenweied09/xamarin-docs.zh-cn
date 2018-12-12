---
title: Xamarin.Essentials：Compass
description: 本文档介绍 Xamarin.Essentials 中的 Compass 类，此类使你能够监视设备的磁北航向。
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 55dd10bff21b7d082b225277d0100232d5efd4f3
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898779"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials：Compass

Compass 类使你能够监视设备的磁北航向。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-compass"></a>使用 Compass

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过调用 `Start` 和 `Stop` 方法来使用 Compass 功能以侦听罗盘的变化。 然后通过 `ReadingChanged` 事件反馈任何变化。 下面是一个示例：

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(object sender, CompassChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: {data.HeadingMagneticNorth} degrees");
        // Process Heading Magnetic North
    }

    public void ToggleCompass()
    {
        try
        {
            if (Compass.IsMonitoring)
              Compass.Stop();
            else
              Compass.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Some other exception has occurred
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android 不提供用于检索罗盘航向的 API。 我们使用 Google 推荐的方法，利用加速计和磁力计来计算磁北航向。

在极少数情况下，可能会看到不一致的结果，因为需要校准传感器，这就涉及到以 8 字形来移动设备。 进行此操作的最佳方式是打开 Google 地图，点击你所在的位置点，然后选择“校准罗盘”。

请注意，同时从应用运行多个传感器可能需要调整传感器速度。

## <a name="low-pass-filter"></a>低通筛选器

根据 Android 罗盘值的更新和计算方式，可能需要平滑处理这些值。 可以应用一个低通筛选器来平均角度的正弦和余弦值，并且可以使用接受 `Start` 参数的 `bool applyLowPassFilter` 方法来启用此筛选器：

```csharp
Compass.Start(SensorSpeed.UI, applyLowPassFilter: true);
```

该参数仅适用于 Android 平台，iOS 和 UWP 会忽略该参数。  可以在[此处](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860)查看详细信息。

--------------

## <a name="api"></a>API

- [Compass 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Campass API 文档](xref:Xamarin.Essentials.Compass)
