---
title: Xamarin.Essentials 映射
description: Xamarin.Essentials 中的映射类使应用程序以打开到特定位置或 placemark 已安装的地图应用程序。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 445e2da84e9a9aaf1ce4d836af11cfba963b8cbb
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353928"
---
# <a name="xamarinessentials-maps"></a>Xamarin.Essentials： 映射

![预发行版 NuGet](~/media/shared/pre-release.png)

**映射**类使应用程序以打开到特定位置或 placemark 已安装的地图应用程序。

## <a name="using-maps"></a>使用映射

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

映射功能适用于通过调用`OpenAsync`方法替换`Location`或`Placemark`以打开具有可选`MapsLaunchOptions`。

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

使用打开时`Placemark`以下信息是必需的：

* `CountryName`
* `AdminArea`
* `Thoroughfare`
* `Locality`

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

如果已有的引用`Location`或`Placemark`您可以使用内置的扩展方法`OpenMapsAsync`具有可选`MapsLaunchOptions`:

```csharp
public class MapsTest
{
    public async Task OpenPlacemarkOnMaps(Placemark placemark)
    {
        await placemark.OpenMapsAsync();
    }
}
```

## <a name="platform-differences"></a>平台差异

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `MapDirectionsMode` 不支持和不起作用。

# <a name="iostabios"></a>[iOS](#tab/ios)

* `MapDirectionsMode` 支持打开地图应用程序时设置的默认方向模式。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `MapDirectionsMode` 不支持和不起作用。

--------------

## <a name="platform-implementation-specifics"></a>平台实现的细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android 使用`geo:`Uri 方案，以启动设备上的地图应用程序。 这可能会提示用户选择从现有应用程序支持此 Uri 方案。  Xamarin.Essentials 是 Google 地图，它支持此方案中测试。

# <a name="iostabios"></a>[iOS](#tab/ios)

任何平台特定实现的详细信息。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

任何平台特定实现的详细信息。

--------------

## <a name="api"></a>API

- [源映射的代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Maps)
- [Maps API 文档](xref:Xamarin.Essentials.Maps)
