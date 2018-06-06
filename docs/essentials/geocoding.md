---
title: Xamarin.Essentials： 地理编码
description: Xamarin.Essentials 中的地理编码类提供 Api 对这两个 geocode 到位置坐标 placemark 和到 placemark 反向 geocode 坐标。
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0b6cbf9ee5621466285656a5efee68ccc2c85211
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783023"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials： 地理编码

![预发行 NuGet](~/media/shared/pre-release.png)

**地理编码**类提供 Api 到 geocode 到位置坐标 placemark 和到 placemark 反向 geocode coordincates。

## <a name="getting-started"></a>入门

访问**地理编码**功能以下平台特定的安装程序是必需的。

# <a name="androidtabandroid"></a>[Android](#tab/android)

不需要其他的安装程序。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他的安装程序。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

使用地理编码 funcationality 需要必应地图 API 密钥。 注册免费的[必应地图](https://www.bingmapsportal.com/)帐户。 下**我的帐户 > 我的密钥**创建新密钥，并填写基于你的应用程序类型的信息。

在调用任何之前的应用程序的生命周期中在早期**地理编码**方法设置的 API 密钥：

```csharp
Geocoding.MapKey = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>使用地理编码

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

获取[位置](xref:Xamarin.Essentials.Location)坐标地址：

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occured in geocoding
}
```

获取[placemarks](xref:Xamarin.Essentials.Placemark)坐标的现有集：

```csharp
try
{
    var lat = 47.673988;
    var lon = -122.121513;

    var placemarks = await Geocoding.GetPlacemarksAsync(lat, lon);

    var placemark = placemarks?.FirstOrDefault();
    if (placemark != null)
    {
        var geocodeAddress =
            $"AdminArea:       {placemark.AdminArea}\n" +
            $"CountryCode:     {placemark.CountryCode}\n" +
            $"CountryName:     {placemark.CountryName}\n" +
            $"FeatureName:     {placemark.FeatureName}\n" +
            $"Locality:        {placemark.Locality}\n" +
            $"PostalCode:      {placemark.PostalCode}\n" +
            $"SubAdminArea:    {placemark.SubAdminArea}\n" +
            $"SubLocality:     {placemark.SubLocality}\n" +
            $"SubThoroughfare: {placemark.SubThoroughfare}\n" +
            $"Thoroughfare:    {placemark.Thoroughfare}\n";

        Console.WriteLine(geocodeAddress);
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

## <a name="api"></a>API

- [地理编码源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [地理编码 API 文档](xref:Xamarin.Essentials.Geocoding)
