---
title: 位置服务
description: 本指南介绍位置感知 Android 应用程序中，并演示如何以获取使用用于 Google 位置服务 API 的 Android 位置服务 API，以及可用的融合的位置提供程序的用户的位置。
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/22/2018
ms.openlocfilehash: b509f6892b27afa053a6ee913826d913d7ad54a8
ms.sourcegitcommit: 4f646dc5c51db975b2936169547d625c78a22b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2018
ms.locfileid: "34546133"
---
# <a name="location-services"></a>位置服务

_本指南介绍位置感知 Android 应用程序中，并演示如何以获取使用用于 Google 位置服务 API 的 Android 位置服务 API，以及可用的融合的位置提供程序的用户的位置。_

## <a name="location-services-overview"></a>位置服务概述

Android 提供对各种位置技术，如单元格立式位置、 Wi-fi、 和 GPS 访问。 每个位置技术的详细信息抽象化通过*位置提供程序*，允许应用程序可以获取相同的方式而不考虑使用的提供程序的位置。 本指南介绍融合的位置提供程序，Google Play 服务后，这可以智能地确定获得基于哪些提供程序可以使用和如何使用设备的设备的位置的最佳方法的一部分。 Android 位置服务 API 并演示如何与系统位置通信服务中使用`LocationManager`。 本指南的第二部分介绍了 Android 位置服务 API 使用`LocationManager`。
 
作为一个常规的经验法则，应用程序应首选使用融合的位置提供程序，回退旧 Android 位置服务 API 仅在必要时。

## <a name="location-fundamentals"></a>位置基础知识

在 Android 中，重要的使用位置数据选择哪些 API，几个概念保持不变。本部分介绍位置提供商和位置相关的权限。

### <a name="location-providers"></a>位置提供程序

内部使用多种技术，以找出用户的位置。 使用硬件依赖于的一种*位置提供程序*选择收集的数据的作业。 Android 使用三个位置提供程序：

-   **GPS 提供程序** &ndash; GPS 提供最准确的位置，使用了最高的能力，并户外效果最佳。 该提供程序使用 GPS 和辅助的 GPS 的组合 ([aGPS](http://en.wikipedia.org/wiki/Assisted_GPS))，它返回收集的移动电话 towers GPS 数据。

-   **网络提供程序**&ndash;组合提供一套 WiFi 和移动电话网络数据，包括收集的单元格 towers aGPS 数据。 它使用较少的电量比 GPS 的提供程序，但返回位置数据的不同的准确性。

-   **被动的提供程序**&ndash;使用请求的其他应用程序或服务的提供程序来生成应用程序中的位置数据的"非法携带"选项。 这是一个不太可靠但节能选项非常适合于应用程序不需要常量的位置更新工作。

位置提供程序并非始终可用。 例如，我们可能想要 GPS 用于我们的应用程序，但在设置中，情况下，GPS 可能已关闭，或者设备可能不会在所有具有 GPS。 如果特定的提供程序不可用，则选择该提供程序可能会返回`null`。

### <a name="location-permissions"></a>位置的权限

位置识别应用程序需要访问设备的硬件传感器可以获得 GPS、 Wi-fi，以及移动电话网络数据。 访问控制通过应用程序的 Android 清单中的适当权限。
没有可用的两个权限&ndash;根据应用程序的要求以及你选择的 API，你将想要允许一：

-   `ACCESS_FINE_LOCATION` &ndash; 允许对 GPS 的应用程序访问权限。
    所需的*GPS 提供程序*和*被动的提供程序*选项 (*被动的提供程序需要许可才能访问由另一个应用程序或服务收集的 GPS 数据*)。 可选权限*网络提供商*。

-   `ACCESS_COARSE_LOCATION` &ndash; 允许对蜂窝网络和 Wi-fi 位置的应用程序访问权限。 所需的*网络提供商*如果`ACCESS_FINE_LOCATION`未设置。

对于面向 API 版本 21 （Android 5.0 棒糖形） 应用，或更高版本，你可以启用`ACCESS_FINE_LOCATION`且仍在没有 GPS 硬件的设备上运行。 如果你的应用需要 GPS 硬件，你应显式添加`android.hardware.location.gps``uses-feature`到 Android 清单的元素。 有关详细信息，请参阅 Android[使用功能](https://developer.android.com/guide/topics/manifest/uses-feature-element.html)元素引用。

若要设置权限，请展开**属性**文件夹中的**解决方案 Pad**双击**AndroidManifest.xml**。 将列出的权限下**所需的权限**:

[![Android 清单所需的权限设置的屏幕截图](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

设置这些权限任一告诉 Android 应用程序需要从用户的权限才能访问位置提供程序。 设备的运行 API 级别 22 (Android 5.1) 或更低时将要求用户每次安装应用程序授予这些权限。 在运行 API 设备上级别 23 (Android 6.0) 或更高版本，应用程序应执行运行时权限检查前发出位置提供程序的请求。 

> [!NOTE]
>注意： 设置`ACCESS_FINE_LOCATION`意味着对这两个粗陋和正常位置数据的访问。 你应永远不需要设置这两个权限，仅*最小*你的应用需要工作的权限。

此代码段演示了如何检查应用程序必须具有权限`ACCESS_FINE_LOCATION`权限：

```csharp
 if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.AccessFineLocation) == Permission.Granted)
{
    StartRequestingLocationUpdates();
    isRequestingLocationUpdates = true;
}
else
{
    // The app does not have permission ACCESS_FINE_LOCATION 
}
```

应用必须容错的方案，其中该用户将不会授予权限 （或已吊销权限），并能够适当地处理这种情况。 请参阅[权限指南](~/android/app-fundamentals/permissions.md)实现运行时权限的更多详细信息将签入 Xamarin.Android。


## <a name="using-the-fused-location-provider"></a>使用融合的位置提供程序

融合的位置提供程序是 Android 应用程序以接收来自设备的位置更新，因为它将有效地选择位置提供程序在运行时提供电池有效的方式的最佳位置信息的首选的方式。 例如，用户能够在户外围绕遍历获取使用 GPS 读取的最佳位置。 如果用户然后遍历室内，其中 GPS 处理不当 （如果根本） 时，融合的位置提供程序可能会自动切换到 WiFi，哪些工作方式更好的室内。
 
融合的位置提供程序 API 提供了各种其他工具来让位置识别应用程序，包括地理围栏和活动监视。 在此部分中，我们将重点介绍的基础知识的设置`LocationClient`，建立提供程序，以及获取用户的位置。

融合的位置提供程序的一部分[Google Play 服务](http://developer.android.com/google/play-services/index.html)。
必须安装 Google Play 服务包，并将其正确配置，在融合的位置提供程序 API 使应用程序工作，并且设备必须具有 Google Play 服务 APK 安装。

在之前 Xamarin.Android 应用程序可以使用融合的位置提供程序，它必须添加**Xamarin.GooglePlayServices.Maps**到项目的包。 此外，以下`using`语句应添加到任何引用如下所述的类的源文件：

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>检查是否已安装 Google Play 服务

Xamarin.Android 崩溃如果它尝试在未安装 Google Play 服务时使用融合的位置提供程序 （或已过期） 将运行时异常，会发生。  如果未安装 Google Play 服务，然后应用程序应回退到 Android 位置服务上文所述。 Google Play 服务已过期，如果应用程序无法向用户要求他们更新安装的 Google Play 服务的版本显示一条消息。

此代码段演示了如何对 Android 活动可以通过编程方式检查是否已安装 Google Play 服务的：

```csharp
bool IsGooglePlayServicesInstalled()
{
    var queryResult = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
    if (queryResult == ConnectionResult.Success)
    {
        Log.Info("MainActivity", "Google Play Services is installed on this device.");
        return true;
    }

    if (GoogleApiAvailability.Instance.IsUserResolvableError(queryResult))
    {
        // Check if there is a way the user can resolve the issue
        var errorString = GoogleApiAvailability.Instance.GetErrorString(queryResult);
        Log.Error("MainActivity", "There is a problem with Google Play Services on this device: {0} - {1}",
                  queryResult, errorString);
                  
        // Alternately, display the error to the user.
    }

    return false;
}
```

### <a name="fusedlocationproviderclient"></a>FusedLocationProviderClient

若要与融合的位置提供程序进行交互，Xamarin.Android 应用程序必须具有的实例`FusedLocationProviderClient`。 此类公开位置更新订阅以及如何检索设备的最后一个已知的位置所需的方法。

`OnCreate`一个活动的方法是一个合适的地方，获取对引用`FusedLocationProviderClient`，如下面的代码段中所示：

```csharp
public class MainActivity: AppCompatActivity
{
    FusedLocationProviderClient fusedLocationProviderClient;

    protected override void OnCreate(Bundle bundle) 
    {
        fusedLocationProviderClient = LocationServices.GetFusedLocationProviderClient(this);
    }
}
```

### <a name="getting-the-last-known-location"></a>获取最后一个已知的位置

`FusedLocationProviderClient.GetLastLocationAsync()`方法为提供的简单、 非阻塞方法 Xamarin.Android 应用程序以快速获取具有最少的编码开销的设备的最后一个已知的位置。

此代码段演示如何使用`GetLastLocationAsync`方法来检索设备的位置：

```csharp
async Task GetLastLocationFromDevice()
{
    // This method assumes that the necessary run-time permission checks have succeeded.
    getLastLocationButton.SetText(Resource.String.getting_last_location);
    Android.Locations.Location location = await fusedLocationProviderClient.GetLastLocationAsync();

    if (location == null)
    {
        // Seldom happens, but should code that handles this scenario
    }
    else
    {
        // Do something with the location 
        Log.Debug("Sample", "The latitude is " + location.Latitude);
    }
}
```

### <a name="subscribing-to-location-updates"></a>订阅到位置更新

Xamarin.Android 应用程序还可以从使用的融合的位置提供程序订阅到位置更新`FusedLocationProviderClient.RequestLocationUpdatesAsync`方法，此代码段中所示：

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```
此方法采用两个参数：

-   **`Android.Gms.Location.LocationRequest`** &ndash; A`LocationRequest`对象是 Xamarin.Android 应用程序如何将参数传递融合的位置提供程序的工作原理。 `LocationRequest`保存信息此类如何应对进行频繁请求或的准确位置更新应如何重要。 例如，重要位置请求将使该设备时要使用 GPS，并因此更多的能力，确定位置。 此代码段演示如何创建`LocationRequest`对于高精度的位置，检查大约每五分钟的位置更新 （但不是早于请求之间的两分钟）。 融合的位置提供程序将使用`LocationRequest`作为要以确定设备位置时尝试使用的位置提供程序的指导原则：

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; 为了接收位置更新，Xamarin.Android 应用程序必须子类`LocationProvider`抽象类。 此类公开两个方法可能调用融合的位置提供程序来更新应用程序的位置信息。 这将在下面更详细地讨论。

若要通知的位置更新 Xamarin.Android 应用程序，请融合的位置提供程序将调用`LocationCallBack.OnLocationResult(LocationResult result)`。 `Android.Gms.Location.LocationResult`参数将包含更新的位置信息。

当融合的位置提供程序检测到的位置数据的可用性的更改时，它将调用`LocationProvider.OnLocationAvaibility(LocationAvailability
locationAvailability)`方法。 如果`LocationAvailability.IsLocationAvailable`属性返回`true`，然后可以假定设备位置结果报告的`OnLocationResult`为准确且为最新所需的`LocationRequest`。 如果`IsLocationAvailable`为 false，则不将返回的任何位置结果`OnLocationResult`。

此代码片段是的示例实现`LocationCallback`对象：

```csharp
public class FusedLocationProviderCallback : LocationCallback
{
    readonly MainActivity activity;

    public FusedLocationProviderCallback(MainActivity activity)
    {
        this.activity = activity;
    }

    public override void OnLocationAvailability(LocationAvailability locationAvailability)
    {
        Log.Debug("FusedLocationProviderSample", "IsLocationAvailable: {0}",locationAvailability.IsLocationAvailable);
    }

    public override void OnLocationResult(LocationResult result)
    {
        if (result.Locations.Any())
        {
            var location = result.Locations.First();
            Log.Debug("Sample", "The latitude is :" + location.Latitude);
        }
        else
        {
            // No locations to work with.
        }
    }
}
```

## <a name="using-the-android-location-service-api"></a>使用 Android 位置服务 API

Android 位置服务是用于在 Android 上使用位置信息的较旧的 API。 硬件传感器收集位置数据并将其访问的应用程序中的系统服务收集`LocationManager`类和`ILocationListener`。

位置服务是最适合应用程序必须在没有安装的 Google Play 服务的设备上运行。

位置服务是一种特殊类型的[服务](http://developer.android.com/guide/components/services.html)受系统管理。 系统服务与设备硬件交互，因此始终运行。 点击到我们的应用程序中的位置更新，我们将订阅位置更新系统位置服务使用从`LocationManager`和`RequestLocationUpdates`调用。

若要获取用户的位置使用 Android 位置服务涉及几个步骤：

1.  获取对引用`LocationManager`服务。
2.  实现`ILocationListener`接口和句柄位置更改时的事件。
3.  使用`LocationManager`于请求的指定的提供程序的位置更新。 `ILocationListener`从前面的步骤将用于接收从回调`LocationManager`。
4.  当应用程序最好不再接收更新，请停止位置更新。

### <a name="location-manager"></a>位置管理器

我们就可以访问的实例的系统位置服务`LocationManager`类。 `LocationManager` 是一个特殊的类，可让我们与系统位置服务进行交互并对其调用方法。 应用程序可以获取对引用`LocationManager`通过调用`GetSystemService`并传入服务类型，如下所示：

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` 是一个好的要获取对引用`LocationManager`。
它是保留一个好办法`LocationManager`作为类变量，以便我们可以在活动的生命周期的各个点调用它。

### <a name="request-location-updates-from-the-locationmanager"></a>从 LocationManager 请求位置更新

一旦应用程序后，对引用`LocationManager`，它需要告诉`LocationManager`哪种类型的位置信息所必需的并更新该信息的频率。 执行此操作通过调用`RequestionLocationUpdates`上`LocationManager`对象，并传入的更新和回调将接收位置更新某些条件。 是一种类型，必须实现此回调`ILocationListener`接口 （在本指南后面的更多详细信息中所述）。

`RequestionLocationUpdates`方法告知系统位置服务，你的应用程序想要开始接收位置更新。 此方法允许你指定的提供程序，以及时间和距离阈值来控制更新频率。 例如，以下请求位置下的方法中更新 GPS 位置提供程序每隔 2000年毫秒，并仅当位置更改多个 1 metre:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

应用程序应仅根据需要应用程序可以很好地运行请求位置更新。 这可以保存的电池使用时间，并创建用户更好的体验。

### <a name="responding-to-updates-from-the-locationmanager"></a>响应来自 LocationManager 的更新

一旦应用程序已请求更新从`LocationManager`，它也可以通过实现从服务接收信息[ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)接口。 此接口提供四个方法为侦听位置服务和位置提供程序， `OnLocationChanged`。 系统将呼叫`OnLocationChanged`用户的位置更改足够才会被视为根据设置请求位置更新时的条件的位置更改时。 

下面的代码演示中的方法`ILocationListener`接口：

```csharp
public class MainActivity : AppCompatActivity, ILocationListener
{
    TextView latitude;
    TextView longitude;
    
    public void OnLocationChanged (Location location)
    {
        // called when the location has been updated.
    }
    
    public OnProviderDisabled(string locationProvider)
    {
        // called when the user disables the provider
    }
    
    public OnProviderEnabled(string locationProvider)
    {
        // called when the user enables the provider
    }
    
    public OnStatusChanged(string locationProvider, Availability status, Bundle extras)
    {
        // called when the status of the provider changes (there are a variety of reasons for this)
    }
}
```

### <a name="unsubscribing-to-locationmanager-updates"></a>取消订阅 LocationManager 更新

为了节省系统资源，应用程序应取消对位置更新越早越好。 `RemoveUpdates`方法告知`LocationManager`停止将更新发送到我们的应用程序。  例如，活动可以调用`RemoveUpdates`中`OnPause`方法，以便我们能够节省电源，如果应用程序不需要位置更新其活动未在屏幕上时：

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

如果你的应用程序需要获取在后台的位置更新，你将需要创建订阅系统位置服务的自定义服务。 请参阅[Backgrounding 与 Android 服务](~/android/app-fundamentals/services/index.md)指南以获取详细信息。

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>确定用于 LocationManager 的最佳位置提供程序

上面的应用程序将 GPS 设置为位置提供程序。 但是，GPS 可能不能在所有情况下，例如设备是否室内或不具有 GPS 接收方。 如果这是这种情况，则结果是`null`返回提供程序。

若要获取你应用程序处理 GPS 不可用时，你可以使用`GetBestProvider`方法以应用启动时的最佳可用 （设备支持和用户启用） 位置提供程序要求。 而是在特定的提供程序中传递，你可以判断`GetBestProvider`-如准确性和 power-具有的提供程序的要求[`Criteria`对象](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)。 `GetBestProvider` 返回对给定条件的最佳提供程序。

下面的代码演示如何获取最佳的提供程序并请求位置更新时使用它：

```csharp
Criteria locationCriteria = new Criteria();   
locationCriteria.Accuracy = Accuracy.Coarse;
locationCriteria.PowerRequirement = Power.Medium;

locationProvider = locationManager.GetBestProvider(locationCriteria, true);

if(locationProvider != null)
{
    locationManager.RequestLocationUpdates (locationProvider, 2000, 1, this);
}
else
{
    Log.Info(tag, "No location providers available");
}
```

> [!NOTE]
>  如果用户已禁用所有位置提供程序，`GetBestProvider`将返回`null`。 若要查看此代码在真实设备上的工作方式，请务必启用 GPS、 Wi-fi、 和下的移动电话网络**Google 设置 > 位置 > 模式**此屏幕截图中所示：

[![在 Android 手机上的设置位置模式屏幕](location-images/location-02.png)](location-images/location-02.png#lightbox)

下面的屏幕截图演示了如何位置应用程序正在运行使用`GetBestProvider`:

[![GetBestProvider 应用显示、 纬度和提供程序](location-images/location-03.png)](location-images/location-03.png#lightbox)

请记住，`GetBestProvider`不会动态更改提供程序。 然而，它将活动生命周期中一次确定最佳的提供程序。 如果提供程序状态发生更改之后已设置，应用程序将需要附加代码`ILocationListener`方法&ndash; `OnProviderEnabled`， `OnProviderDisabled`，和`OnStatusChanged`&ndash;来处理所有与相关的可能性提供程序开关。

## <a name="summary"></a>总结

本指南涵盖获取使用 Android 位置服务和融合的位置提供程序从 Google 位置服务 API 的用户的位置。


## <a name="related-links"></a>相关链接

- [位置 （示例）](https://developer.xamarin.com/samples/Location/)
- [FusedLocationProvider （示例）](https://developer.xamarin.com/samples/FusedLocationProvider/)
- [Google Play Services](http://developer.android.com/google/play-services/index.html)
- [条件类](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)
- [LocationManager 类](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)
- [LocationListener 类](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)
- [LocationClient API](http://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](http://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
