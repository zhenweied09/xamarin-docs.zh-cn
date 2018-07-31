---
title: Xamarin.Essentials： 地理编码
description: Xamarin.Essentials 中的地理编码类提供 Api 对这两个地理编码为位置坐标 placemark 和到 placemark 反向地理编码坐标。
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a4d6e4d9b32e665893d82693a3c858630b63d372
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353670"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials： 地理编码

![预发行版 NuGet](~/media/shared/pre-release.png)

**地理编码**类提供 Api 到地理编码为位置坐标 placemark 和到 placemark 反向地理编码坐标。

## <a name="getting-started"></a>入门

访问**地理编码**功能以下平台特定的安装程序是必需的。

# <a name="androidtabandroid"></a>[Android](#tab/android)

无需其他设置。

# <a name="iostabios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

使用地理编码功能需要必应地图 API 密钥。 免费注册[必应地图](https://www.bingmapsportal.com/)帐户。 下**我的帐户 > 我的密钥**创建新密钥，并填写基于应用程序类型的信息 (它应该是**公共 Windows 应用 (UWP，8.x 及更早版本)** 适用于 UWP 应用)。

在早期中调用任何之前的应用程序的生命**地理编码**方法设置 API 密钥：

```csharp
Geocoding.MapKey = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>使用地理编码

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

获取[位置](xref:Xamarin.Essentials.Location)地址的坐标：

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
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

海拔高度始终不可用。 如果不可用，`Altitude`属性可能会`null`或的值可能为零。 如果可用的海拔高度，值为以米为单位以上高度。

获取[placemarks](xref:Xamarin.Essentials.Placemark)的一组现有的坐标：

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

## <a name="distance-between-two-locations"></a>两个位置之间的距离

[ `Location` ](xref:Xamarin.Essentials.Location)并[ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions)类定义方法来计算两个位置之间的距离。 请参阅文章[ **Xamarin.Essentials： 地理位置**](geolocation.md#calculate-distance)有关的示例。

## <a name="api"></a>API

- [地理编码的源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [地理编码 API 文档](xref:Xamarin.Essentials.Geocoding)
