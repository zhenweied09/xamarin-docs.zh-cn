---
title: Xamarin.Essentials：Maps
description: Xamarin.Essentials 中的 Maps 类允许应用程序将已安装的地图应用程序打开到特定位置或地标。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: fb4cbc2fd334d574abc57a3359fa346bc6795408
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674747"
---
# <a name="xamarinessentials-maps"></a>Xamarin.Essentials：Maps

![预发行版 NuGet](~/media/shared/pre-release.png)

Maps 类允许应用程序将已安装的地图应用程序打开到特定位置或地标。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-maps"></a>使用 Maps

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

Maps 功能通过调用具有 `Location` 或 `Placemark` 的 `OpenAsync` 方法来使用可选的 `MapsLaunchOptions` 打开。

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(location, options);
    }
}
```

使用 `Placemark` 打开时，需要以下信息：

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var placemark = new Placemark
            {
                CountryName = "United States",
                AdminArea = "WA",
                Thoroughfare = "Microsoft Building 25",
                Locality = "Redmond"
            };
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(placemark, options);
    }
}
```

## <a name="extension-methods"></a>扩展方法

如果已有对 `Location` 或 `Placemark` 的引用，则可以使用具有可选的 `MapsLaunchOptions` 的内置扩展方法 `OpenMapsAsync`：

```csharp
public class MapsTest
{
    public async Task OpenPlacemarkOnMaps(Placemark placemark)
    {
        await placemark.OpenMapsAsync();
    }
}
```

## <a name="directions-mode"></a>方向模式

如果调用不带任何 `MapsLaunchOptions` 的 `OpenMapsAsync`，则地图将启动到指定位置。 （可选）可以有从设备的当前位置开始计算的导航路线。 这是通过设置 `MapsLaunchOptions` 上的 `MapDirectionsMode` 来完成的：

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { MapDirectionsMode = MapDirectionsMode.Driving };

        await Maps.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>平台差异

# <a name="androidtabandroid"></a>[Android](#tab/android)

- `MapDirectionsMode` 支持骑行、驾车和步行。

# <a name="iostabios"></a>[iOS](#tab/ios)

- `MapDirectionsMode` 支持驾车、公交和步行。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- `MapDirectionsMode` 支持驾车、公交和步行。

--------------

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android 使用 `geo:` URI 方案启动设备上的地图应用程序。 这可能会提示用户从支持此 URI 方案的现有应用程序中进行选择。  Xamarin.Essentials 使用支持此方案的 Google 地图进行测试。

# <a name="iostabios"></a>[iOS](#tab/ios)

无特定于平台的实现细节。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无特定于平台的实现细节。

--------------

## <a name="api"></a>API

- [Maps 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Maps)
- [Maps API 文档](xref:Xamarin.Essentials.Maps)
