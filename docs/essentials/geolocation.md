---
title: Xamarin.Essentials： 地理位置
description: 本文档介绍 Xamarin.Essentials，提供 Api 以检索设备的当前地理位置坐标中的地理位置类。
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0aeb2ed96e6c21def69eb2e6f305b26e2e478825
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353849"
---
# <a name="xamarinessentials-geolocation"></a>Xamarin.Essentials： 地理位置

![预发行版 NuGet](~/media/shared/pre-release.png)

**地理位置**类提供 Api 以检索设备的当前地理位置坐标。

## <a name="getting-started"></a>入门

访问**地理位置**功能，以下特定于平台的安装程序是必需的：

# <a name="androidtabandroid"></a>[Android](#tab/android)

粗糙和正常位置的权限所需，必须在 Android 项目中配置。 此外，如果你的应用面向 Android 5.0 （API 级别 21） 或更高版本，您必须声明您的应用程序清单文件中使用的硬件功能。 这可以通过以下方法添加：

打开**AssemblyInfo.cs**文件下**属性**文件夹并添加：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

或更新 Android 清单：

打开**AndroidManifest.xml**文件下**属性**文件夹，并添加以下的内部**清单**节点：

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

或右键单击 Android 项目，然后打开项目的属性。 下**Android 清单**查找**所需的权限：** 区域并检查**ACCESS_COARSE_LOCATION**和**ACCESS_FINE_LOCATION**权限。 这将自动更新**AndroidManifest.xml**文件。

# <a name="iostabios"></a>[iOS](#tab/ios)

你的应用**Info.plist**必须包含`NSLocationWhenInUseUsageDescription`密钥才能访问设备的位置。

打开 plist 编辑器，并添加**隐私-位置时在使用情况说明**属性并填写要显示的用户的值。

或手动编辑该文件并添加以下：

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

必须设置`Location`应用程序的权限。 这可以通过打开**Package.appxmanifest** ，然后选择**功能**选项卡并检查**位置**。

-----

## <a name="using-geolocation"></a>使用地理位置

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

Geoloation API 还会提示用户输入时必需的权限。

可以获取上次已知[位置](xref:Xamarin.Essentials.Location)的设备通过调用`GetLastKnownLocationAsync`方法。 这通常是更快地执行完整的查询，但可能会不太准确。

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
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

海拔高度始终不可用。 如果不可用，`Altitude`属性可能会`null`或的值可能为零。 如果可用的海拔高度，值为以米为单位以上高度。 

若要查询当前设备[位置](xref:Xamarin.Essentials.Location)坐标，`GetLocationAsync`可用。 最好是将完整`GeolocationRequest`和`CancellationToken`因为可能要花一些时间才能获取设备的位置。

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
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

## <a name="geolocation-accuracy"></a>地理位置的准确性

下表概述了每个平台的准确性：

### <a name="lowest"></a>最低

| 平台 | 距离 （以米为单位） |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1000-5000 |

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

<a name="calculate-distance" />

## <a name="distance-between-two-locations"></a>两个位置之间的距离

[ `Location` ](xref:Xamarin.Essentials.Location)并[ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions)类定义`CalculateDistance`方法，可用于计算两个地理位置之间的距离。 此计算距离算法不考虑道路或其他途径，并也称为是只是沿曲面的地球的两个点之间的最短距离_大圆距离_或通俗地，距离"crow 飞行。"

以下是一个示例：

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

`Location`构造函数具有纬度和经度的参数的顺序。 纬度值为赤道以北，正经度值偏东子午线为正。 使用到的最后一个参数`CalculateDistance`英里或公里为单位指定。 `Location`类还定义了`KilometersToMiles`和`MilesToKilometers`两个单位之间进行转换的方法。

## <a name="api"></a>API

- [地理位置的源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation)
- [地理位置 API 文档](xref:Xamarin.Essentials.Geolocation)
