---
title: Xamarin.Essentials 指南针
description: 指南针类允许您监视设备的磁北标题。
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 9e0d0a709006a0d185a0c79b7b03d1672f06c4b6
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials 指南针

![预发行 NuGet](~/media/shared/pre-release.png)

**指南针**类允许您监视设备的磁北标题。

## <a name="using-compass"></a>使用指南针

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

指南针功能的工作方式是调用`Start`和`Stop`方法来侦听对指南针更改。 任何更改发送回通过`ReadingChanged`事件。 下面是一个示例：

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(CompassChangedEventArgs e)
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
            // Some other exception has occured
        }
    }
}
```

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[传感器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 尽可能 （但不保证在 UI 线程上返回） 快获取传感器数据。
- **游戏**– 速率适用于 （但不保证在 UI 线程上返回） 的游戏。
- **正常**– 适用于屏幕方向更改的默认速率。
- **Ui** – 速率适用于常规用户界面。

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android 不提供用于检索指南针方向的 API。 我们使用加速和磁力计来计算磁北标题、 google 建议这样做。 

在极少数情况下，你可能看到不一致的结果因为传感器需要校准，涉及到图 8 动态移动你的设备。 这是为了打开 Google 图，点击你的位置的点，然后选择这样做的最佳方式**校准指南针**。

请注意，在同一时间从您的应用程序中运行多个传感器可能调整传感器速度。

--------------

## <a name="api"></a>API

- [指南针源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/Compass)
- [指南针 API 文档](xref:Xamarin.Essentials.Compass)
