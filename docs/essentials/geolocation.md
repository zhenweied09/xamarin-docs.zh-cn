---
title: Xamarin.Essentials 地理位置
description: 地理位置类可提供 Api 来检索设备的当前地理位置坐标。
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 399dcd54d574875bcb5e491e87731b817e840e54
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials 地理编码

![预发行 NuGet](~/media/shared/pre-release.png)

**地理位置**类可提供 Api 来检索设备的当前地理位置坐标。

## <a name="getting-started"></a>入门

访问**地理位置**功能以下平台特定的安装程序是必需的。

# <a name="androidtabandroid"></a>[Android](#tab/android)

粗糙和正常位置的权限所需，必须在 Android 项目中配置。 此外，如果你的应用面向 Android 5.0 （API 级别 21） 或更高版本，您必须声明你的应用程序清单文件中使用的硬件功能。 这可以通过以下方式添加：

打开**AssemblyInfo.cs**文件下**属性**文件夹并添加：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

或更新 Android 清单：

打开**AndroidManifest.xml**文件下**属性**文件夹并添加以下内的**清单**节点。

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

或右键单击 Anroid 项目并打开项目的属性。 下**Android 清单**查找**所需的权限：**区域并检查**ACCESS_COARSE_LOCATION**和**ACCESS_FINE_LOCATION**权限。 这将自动更新**AndroidManifest.xml**文件。

# <a name="iostabios"></a>[iOS](#tab/ios)

你的应用需要拥有密钥你**Info.plist**为 NSLocationWhenInUseUsageDescription 才能访问设备的位置。

打开 plist 编辑器并添加**隐私-位置时在使用使用说明**属性，并填写要显示的用户的一个值。

或手动编辑文件并添加以下代码：

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

必须设置`Location`应用程序的权限。 这可以通过打开**Package.appxmanifest**和 selecing**功能**选项卡和检查**位置**。

-----

## <a name="using-geolocation"></a>使用地理位置

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

Geoloation API 还会提示用户输入权限在必要时。

你可以获取上次已知[位置](xref:Xamarin.Essentials.Location)通过调用设备的`GetLastKnownLocationAsync`方法。 这通常是更快然后执行完整的查询，但可能会不太准确。

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

若要查询当前设备[位置](xref:Xamarin.Essentials.Location)坐标`GetLocationAsync`可用。 建议在完整传递`GeolocationRequest`和`CancellationToken`由于它可能需要一些时间，以获取设备的位置。

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

## <a name="geolocation-accuracy"></a>地理位置准确性

下表概述了每个平台的准确性

### <a name="lowest"></a>最低

| 平台 | 距离 （以米为单位） |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1000-5000 之间 |

### <a name="low"></a>低

| 平台 | 距离 （以米为单位） |
| --- | --- |
| Android | 500 |
| iOS | 1000 |
| UWP | 300-3000 |

### <a name="medium-default"></a>中 （默认值）

| 平台 | 距离 （以米为单位） |
| --- | --- |
| Android | 100-500 |
| iOS | 100 |
| UWP | 30-500 |

### <a name="high"></a>高

| 平台 | 距离 （以米为单位） |
| --- | --- |
| Android | 0-100 |
| iOS | 10 |
| UWP | < = 10 |

### <a name="best"></a>最佳

| 平台 | 距离 （以米为单位） |
| --- | --- |
| Android | 0-100 |
| iOS | ~0 |
| UWP | < = 10 |

## <a name="api"></a>API

- [地理位置的源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/Geolocation)
- [地理位置 API 文档](xref:Xamarin.Essentials.Geolocation)
