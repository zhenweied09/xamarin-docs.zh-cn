---
title: "地图 API"
ms.topic: article
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: eddd723c07919db4749c63c5b4f1d05e9be81022
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="maps-api"></a>地图 API

使用地图应用程序非常有利，但有时你想要在你的应用程序中直接包含地图。 除了内置映射应用程序，还提供了 Google[适用于 Android 的本机映射 API](https://developers.google.com/maps/documentation/android/)。
地图 API 适合于你想要维护的映射体验的更好地控制的情况。 可能用于地图 API 的事件包括：

-  以编程方式更改地图的角度来看。
-  添加和自定义标记。
-  批注具有叠加的映射。

现在已弃用 Google 地图 Android API v1，与 Google 地图 Android API v2 属于[Google Play 服务](http://developer.android.com/google/play-services/index.html)。
因此，它是符合某些必需的必备项之前可以在 Xamarin.Android 应用程序中使用 Google 地图 Android API。

<a name="Configuring_Maps_API_Prerequisites" />

## <a name="google-maps-api-prerequisites"></a>Google 映射 API 先决条件

多个项需要配置，然后你可以使用地图 API，包括：

-  安装 Google Play 服务 SDK
-  使用 Google Api 创建一个仿真程序
-  获取地图 API 密钥
-  指定所需的权限


<a name="Google_APIs_Add-On" />

### <a name="install-the-google-play-services-sdk"></a>安装 Google Play 服务 SDK

Google Play 服务是一种技术将来自 Google，允许充分利用各种 Google Google +、 和等功能的应用内帐单、 地图 Android 应用程序。 这些功能都可在 Android 设备上访问作为后台服务包含在[Google Play 服务 APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)。

与 Google Play 服务进行交互 android 应用程序通过 Google Play 服务客户端库。 此库包含的接口和如地图的各个服务的类。 下图显示了一个 Android 应用程序和 Google Play 服务之间的关系：

![图示更新 Google Play 服务 APK Google Play 商店](maps-api-images/play-services-diagram.png)

Android 的地图 API 作为 Google Play 服务的一部分提供。
Xamarin.Android 应用程序可以使用地图 API 之前，必须安装 Google Play Services SDK，并将其绑定中。 通过 Android SDK 管理器安装 Google Play Services SDK。 下面的屏幕截图显示何处 Android SDK 管理器中找不到 Google Play 服务客户端：

![Google Play 服务会出现在其他功能中 Android SDK 管理器下](maps-api-images/image01.png)

> [!NOTE]
> **注意：** APK 是许可的产品，可能不是 Google Play 服务呈现在所有设备上。 如果未安装，然后 Google 映射不会在该设备。

<a name="Binding_Google_Play_Services" />

#### <a name="binding-google-play-services"></a>绑定 Google Play Services

安装 Google Play 服务客户端库后，它必须由 Xamarin.Android Java 绑定库绑定。 有两种方法来实现此目的：

-  **使用 Google Play Services 地图 NuGet 程序包**-这是 （仅在 Xamarin.Android 4.8 中可用或更高版本） 的最简单方法。
   安装[Xamarin Google Play Services 地图 NuGet](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps); 这还将安装所有 Google Play 服务依赖项包。
   本指南的其余部分重点介绍此方法。

-  **手动将绑定的 Google Play 服务客户端库**-这是更复杂的方法，是 Xamarin.Android 4.4 或 Xamarin.Android 4.6 绑定 Google Play Services SDK 的唯一方法。
   手动绑定 Google Play 服务客户端库不在本文档的范围，但可能在中找到举例说明如何执行此操作[地图和位置演示 v3 示例](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3)Github 上。

<a name="Adding_the_Google_Play_Services_Component" />

#### <a name="adding-the-google-play-services-map-package"></a>添加 Google Play 服务映射包

若要添加 Google Play 服务映射包，右键单击**引用**解决方案资源管理器中单击项目文件夹**管理 NuGet 包...**:

![在引用下的解决方案资源管理器中显示管理 NuGet 包上下文菜单项](maps-api-images/image02.png)

这将打开**NuGet 包管理器**。 单击**浏览**并输入**Xamarin Google Play 服务映射**搜索字段中。 选择**Xamarin.GooglePlayServices.Maps**单击**安装**。 (如果以前已安装此包，请单击**更新**。):

[![与选择的 Xamarin.GooglePlayServices.Maps 包的 NuGet 包管理器](maps-api-images/image03-sml.png)](maps-api-images/image03.png)

请注意以下依赖项包还会安装：

-   **Xamarin.GooglePlayServices.Base**
-   **Xamarin.GooglePlayServices.Basement**
-   **Xamarin.GooglePlayServices.Tasks**


<a name="Creating_an_Emulator_with_Google_APIs" />

### <a name="create-an-emulator-with-google-apis"></a>使用 Google Api 创建一个仿真程序

尽管不建议这样做，则可以设置的仿真程序以支持 Android 地图 API。 必须配置仿真程序为面向 Google API 级别 17 (Android 4.2.2) 或更高版本。 在以下屏幕截图中，为 API 级别 19 配置仿真程序映像： 

![使用 API 级别 19 为配置的 AVD android 模拟器管理器](maps-api-images/image04.png)


<a name="apikey" />

### <a name="obtain-a-google-maps-api-key"></a>获取一个 Google 地图 API 密钥

最后一步是获取 Google 地图 API 密钥 （请注意你无法重用从旧 Google 地图 v1 API 密钥）。 有关如何获取和 API 密钥用于 Xamarin.Android 的信息，请参阅[获取 Google 地图 API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。
 

<a name="Specify_Permissions" />

### <a name="specify-the-required-permissions"></a>指定所需的权限

必须在指定下列权限**AndroidManifest.XML** Google 地图 Android api:

-  **访问的网络状态**&ndash;地图 API 必须能够检查是否它可以下载地图图块。

-  **Internet 访问**&ndash;都下载地图图块和与 API 访问 Google 的播放服务器通信所需访问 Internet。

-  **OpenGL ES v2** &ndash;应用程序必须声明 OpenGL ES v2 的要求。

-  **Google 地图 API 密钥** &ndash; API 密钥用于确认应用程序已注册并有权使用 Google Play 服务。 请参阅[获取 Google 地图 API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)有关此密钥的详细信息。

-  **写入到外部存储** &ndash; Google 地图 Android API 将缓存到外部存储的下载磁贴。

-  **对基于 Google Web 服务的访问**&ndash;应用程序需要的权限来访问备份 Android 地图 API 的 Google 的 web 服务。

-  **Google Play 服务通知权限**&ndash;必须授予应用程序权限从 Google Play 服务接收远程通知。

-  **对位置提供程序的访问**&ndash;这些是可选的权限。
   它们将允许`GoogleMap`类图上显示设备的位置。


下面的代码段演示了必须添加到的设置**AndroidManifest.XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="14" android:targetSdkVersion="17" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

    <!-- We need to be able to download map tiles and access Google Play Services-->
    <uses-permission android:name="android.permission.INTERNET" />

    <!-- Allow the application to access Google web-based services. -->
    <uses-permission android:name="com.google.android.providers.gsf.permission.READ_GSERVICES" />

    <!-- Google Maps for Android v2 will cache map tiles on external storage -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

    <!-- Google Maps for Android v2 needs this permission so that it may check the connection state as it must download data -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!-- Permission to receive remote notifications from Google Play Services -->
    <!-- Notice here that we have the package name of our application as a prefix on the permissions. -->
    <uses-permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" />
    <permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" android:protectionLevel="signature" />

    <!-- These are optional, but recommended. They will allow Maps to use the My Location provider. -->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />


    <application android:label="@string/app_name">
        <!-- Put your Google Maps V2 API Key here. -->
        <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
        <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
    </application>
</manifest>
```

<a name="The_GoogleMap" />

## <a name="the-googlemap-class"></a>GoogleMap 类

一旦系统必备组件都能得到满足，就可以开始开发应用程序并使用 Android 地图 API。 [GoogleMap](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap)类是主要 Xamarin.Android 应用程序将用于显示和适用于 Android 与 Google 地图进行交互的 API。 此类具有以下职责：

-  与授权与 Google web 服务应用程序的 Google Play 服务交互。

-  下载、 缓存和显示地图图块。

-  显示 UI 控件，如平移和缩放到用户。

-  在地图上绘制标记和几何形状。

`GoogleMap`添加到在两种方式之一中的活动：

-  **MapFragment** - [MapFragment](http://developer.android.com/reference/com/google/android/gms/maps/MapFragment.html)充当主机是专用片段`GoogleMap`对象。 `MapFragment`需要 12 或更高版本的 Android API 级别。
   可以使用较旧版本的 Android [SupportMapFragment](http://developer.android.com/reference/com/google/android/gms/maps/SupportMapFragment.html)。

-  **MapView** - [MapView](https://developer.xamarin.com/api/type/Android.GoogleMaps.MapView/)是一个专门的视图子类，它可以充当主机`GoogleMap`对象。 此类的用户必须转发到的活动生命周期方法的所有`MapView`类。

每个这些容器公开`Map`返回的实例的属性`GoogleMap`。 首选项应授予给[MapFragment](http://developer.android.com/reference/com/google/android/gms/maps/MapFragment.html)类作为一种更简单的 API，减少了开发人员必须手动实现的量样板文件代码。

<a name="Adding_GoogleMap_To_An_Activity" />

### <a name="adding-a-mapfragment-to-an-activity"></a>将 MapFragment 添加到活动

下面的屏幕截图是一种非常简单`MapFragment`:

[![显示的映射片段的设备的屏幕截图](maps-api-images/image05-sml.png)](maps-api-images/image05.png)

与其他片段类相似，有两种方法地将其添加`MapFragment`为活动：

-   **以声明方式**-`MapFragment`活动可以通过 XML 布局文件中添加。 以下 XML 代码段显示了如何使用的示例`fragment`元素：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

-   **以编程方式**-`MapFragment`可以按下文所述以编程方式添加。

若要以编程方式添加`MapFragment`，你的活动必须实现`IOnMapReadyCallback`接口。 因为的初始化`GoogleMap`对象可能需要一些时间才能完成和 Google Play 通信 API，你必须提供通知您的应用程序的回调时`GoogleMap`准备。

首先，添加`IOnMapReadyCallback`到`Activity`类声明。
例如:

```csharp
public class MapWithMarkersActivity : Activity, IOnMapReadyCallback
```

接下来，在`OnCreate`方法，添加`MapFragment`下面的代码示例中所示 (`GoogleMapOptions`类在本指南后面所述):

```csharp
_mapFragment = FragmentManager.FindFragmentByTag("map") as MapFragment;
if (_mapFragment == null)
{
    GoogleMapOptions mapOptions = new GoogleMapOptions()
        .InvokeMapType(GoogleMap.MapTypeSatellite)
        .InvokeZoomControlsEnabled(false)
        .InvokeCompassEnabled(true);

    FragmentTransaction fragTx = FragmentManager.BeginTransaction();
    _mapFragment = MapFragment.NewInstance(mapOptions);
    fragTx.Add(Resource.Id.map, _mapFragment, "map");
    fragTx.Commit();
}
_mapFragment.GetMapAsync(this);
```

A`GoogleMap`必须使用获取`GetMapAsync`、 一端的前面的代码示例所示&ndash;这自动初始化地图系统和视图。 (请注意，此方法不使用`await` / `async`语义&ndash; `Async` android 实现行为。)当`GoogleMap`对象已准备就绪时，Android 调用你的应用`OnMapReady`方法 (作为的一部分必须实现该`IOnMapReadyCallback`接口)。 例如:

```csharp
public void OnMapReady (GoogleMap map)
{
    _map = map;
}
```

在上面的代码示例中，`OnMapReady`回调初始化`_map`变量创建`GoogleMap`对象。

作为示例，了解如何使用此结果时`OnResume`是调用，它可以检查以查看是否`_map`为非 null。 如果`_map`设置为`GoogleMap`对象，`OnResume`可以对其添加标记并将其相机移到指定的经度和纬度调用方法。 有关完整的代码示例，请参阅[SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)。


<a name="Map_Types" />

### <a name="map-types"></a>映射类型

有五种不同类型的地图来自 Google 映射 API 可用：

-  **正常**-这是默认的映射类型。 它显示道路和重要的自然功能，以及 （如建筑物和桥） 感兴趣的某些人为点。

-  **附属**-此地图显示附属照片。

-  **混合**-此地图显示附属摄影和道路映射。

-  **地形**-这主要显示了与某些公路地形功能。

-  **无**-此映射不会加载任何磁贴，呈现为一个空网格。


下图显示三个不同类型的地图，从左到右 （正常、 混合，地形）：

[![三个映射示例屏幕快照： Normal、 混合和地形](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png)

`GoogleMap.MapType`属性用于设置或更改显示哪种类型的映射。 下面的代码段演示如何显示附属映射。

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MapType = GoogleMap.MapTypeSatellite;
}
```

<a name="GoogleMap_Properties" />

### <a name="googlemap-properties"></a>GoogleMap 属性

`GoogleMap` 定义可以控制功能和地图外观的多个属性。 配置的初始状态的一种方法`GoogleMap`是将传递[GoogleMapOptions](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMapOptions.html)对象创建时`MapFragment`。 下面的代码段是一个示例使用`GoogleMapOptions`对象创建时`MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
_mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, _mapFragment, "map");
fragTx.Commit();
```

为配置的其他方式`GoogleMap`对象是通过设置值[UiSettings](http://developer.android.com/reference/com/google/android/gms/maps/UiSettings.html)映射对象属性。 下一步的代码示例演示如何配置`GoogleMap`显示缩放控件和指南针：

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.UiSettings.ZoomControlsEnabled = true;
    _map.UiSettings.CompassEnabled = true;
}
```

<a name="Interacting_with_the_Map" />

## <a name="interacting-with-the-map"></a>与地图进行交互

Android 的地图 API 提供的 API 的允许活动更改角度来看，添加标记，将自定义叠加或绘制几何形状。 本部分将讨论如何完成这些任务中 Xamarin.Android 的一些。

### <a name="changing-the-viewpoint"></a>更改视区

地图来模拟作为平面平面在屏幕上，根据 Mercator 投影。 地图视图是*相机*此平面上直接向下查找。 可以通过更改位置、 缩放、 倾斜，并影响控制的照相机的位置。 [CameraUpdate](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdate)类用于移动的照相机的位置。 `CameraUpdate` 不直接实例化对象，而是地图 API 提供[CameraUpdateFactory](http://developer.android.com/reference/com/google/android/gms/maps/CameraUpdateFactory.html)类。

一次`CameraUpdate`创建对象，它作为参数传递到[GoogleMap.MoveCamera](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.html#moveCamera(com.google.maps.CameraUpdate))或[GoogleMap.AnimateCamera](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.html#animateCamera(com.google.maps.CameraUpdate))方法。 `MoveCamera`方法更新立即时映射`AnimateCamera`方法提供平滑、 动画转换。

此代码片段是如何使用一个简单示例`CameraUpdateFactory`创建`CameraUpdate`，都会增加地图的缩放级别 1:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

地图 API 提供[CameraPosition](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html)其将聚合所有相机位置的可能值。 此类的实例可以提供给[CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition(com.google.android.gms.maps.model.CameraPosition))方法将返回`CameraUpdate`对象。 地图 API 还包括[CameraPosition.Builder](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) fluent API 提供用于创建的类`CameraPosition`对象。
下面的代码段演示创建的示例`CameraUpdate`从`CameraPosition`并将它更改上的照相机的位置`GoogleMap`:

```csharp
LatLng location = new LatLng(50.897778, 3.013333);
CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
builder.Target(location);
builder.Zoom(18);
builder.Bearing(155);
builder.Tilt(65);
CameraPosition cameraPosition = builder.Build();
CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MoveCamera(cameraUpdate);
}
```

在上面的代码段，在地图上的特定位置由[LatLng](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/LatLng)类。 缩放级别设置为 18。 影响是北部顺时针旋转的指南针测量。 倾斜属性控制的视角，并且指定从垂直角度为 25 度。 以下屏幕快照显示`GoogleMap`后执行前面的代码：

[![显示指定的位置并且用倾斜映射示例 Google 视角。](maps-api-images/image06-sml.png)](maps-api-images/image06.png)

<a name="Adding_Overlays_to_a_Map" />

### <a name="drawing-on-the-map"></a>在地图上绘制

Android 的地图 API 提供 API 的绘制地图上的以下各项：

-  **标记**-这些是用于标识在地图上的单个位置的特殊图标。

-  **叠加**-这是可以用于标识位置或在地图上的区域的集合的映像。

-  **线条、 多边形和圆形**-这些是允许活动在向地图添加形状的 Api。

<a name="markers" />

#### <a name="markers"></a>标记

地图 API 提供[标记](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Marker)类封装所有有关在地图上的单个位置的数据。 默认情况下，它们使用 Google 地图提供的标准图标。 很可能以自定义标记的外观并响应用户单击。

<a name="AddingAMarker" />

##### <a name="adding-a-marker"></a>添加标记

要向地图中添加一个标记，则有必要创建一个新[MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions)对象，然后调用[AddMarker](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker(com.google.android.gms.maps.model.MarkerOptions))方法`GoogleMap`实例。 此方法将返回[标记](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Marker)对象。

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    _map.AddMarker(marker1);
}
```

标记的标题将显示在*信息窗口*当用户点击在标记上。 下面的屏幕截图显示了此标记的外观：

[![将 Google 映射示例使用一个标记和 Vimy Ridge 信息窗口](maps-api-images/image07-sml.png)](maps-api-images/image07.png)

<a name="Customizing_A_Marker" />

##### <a name="customizing-a-marker"></a>自定义标记

可以自定义标记通过调用所用的图标`MarkerOptions.InvokeIcon`方法时将标记添加到图。
此方法采用[BitmapDescriptor](http://developer.android.com/reference/com/google/android/gms/maps/model/BitmapDescriptor.html)对象，其中包含所需呈现图标的数据。 [BitmapDescriptorFactory](https://developer.android.com/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory.html)类提供一些帮助器方法来简化创建`BitmapDescriptor`。 以下列表介绍了一些这些方法：

-   `DefaultMarker(float colour)` &ndash; 使用默认 Google 地图标记，但更改颜色。

-   `FromAsset(string assetName)` &ndash; 从指定文件的 Assets 文件夹中使用自定义的图标。

-   `FromBitmap(Bitmap image)` &ndash; 指定的位图用作图标。

-   `FromFile(string fileName` &ndash; 从指定路径中的文件创建自定义图标。

-   `FromResource(int resourceId)` &ndash; 从指定的资源创建一个自定义图标。

下面的代码段演示创建青色 coloured 的默认标记的示例：

```csharp
mapFrag.GetMapAsync(this);
...
if (_map != null)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    markerOpt1.InvokeIcon(BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan));
    _map.AddMarker(marker1);
}
```

<a name="Info_Windows" />

#### <a name="info-windows"></a>信息 Windows

*信息 windows*是该弹出窗口向用户显示信息，当用户点击特定标记的特殊的窗口。 默认情况下信息窗口将显示标记的标题的内容。 如果尚未分配标题，则会不显示任何信息窗口。 只有一个信息窗口可能会显示一次。

可以通过实现自定义信息窗口[GoogleMap.IInfoWindowAdapter](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter)接口。 此接口上有两个重要的方法：

-  `public View GetInfoWindow(Marker marker)` &ndash; 若要获取的标记的自定义的信息窗口，调用此方法。 如果它返回`null`，则将使用默认的窗口呈现。 如果此方法返回的视图，然后将该信息窗口框架内放置该视图。

-  `public View GetInfoContents(Marker marker)` &ndash; 将仅调用此方法，如果 GetInfoWindow 返回`null`。 此方法可以返回`null`如果要使用的信息窗口内容的默认呈现的值。 否则，此方法应返回信息窗口的内容视图。

信息窗口不是实时的视图-改为 Android 将转换为静态的位图的视图并在图像上显示的。 这意味着，信息窗口无法响应任何触控事件或手势，也不将它自动自我更新。 若要更新的信息窗口，才必须调用[GoogleMap.ShowInfoWindow](http://developer.android.com/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow())方法。

下图显示了某些自定义的信息窗口的一些示例。 在左侧的映像具有它的内容自定义，而右侧映像具有它的窗口和自定义的内容：

![墨尔本、 包括图标和填充的示例标记 windows。 右侧窗口具有圆角。](maps-api-images/marker-infowindows.png)

<a name="Adding_an_overlay" />

#### <a name="ground-overlays"></a>接地叠加

与标记，用于标识在地图上的特定位置时，不同[GroundOverlay](http://developer.android.com/reference/com/google/android/gms/maps/model/GroundOverlay.html)是用于标识位置或在地图上的区域的集合的映像。

<a name="AddingAGroundOverlay" />

##### <a name="adding-a-groundoverlay"></a>添加 GroundOverlay

向地图添加接地覆盖是非常类似于在向地图添加一个标记。 首先， [GroundOverlayOptions](http://developer.android.com/reference/com/google/android/gms/maps/model/GroundOverlayOptions.html)创建对象。 此对象然后传递作为参数传递给`GoogleMap.AddGroundOverlay`方法，它将返回`GroundOverlay`对象。 此代码片段是向地图添加完全覆盖的示例：

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = _map.AddGroundOverlay(groundOverlayOptions);
```

下面的屏幕截图显示地图上的此覆盖：

[![使用北极熊均图像映射示例](maps-api-images/image09-sml.png)](maps-api-images/image09.png)

<a name="Lines_Circles_and_Polygons" />

#### <a name="lines-circles-and-polygons"></a>线条、 圆形和多边形

有三个简单类型的几何可以添加到图的图表：

-  **折线**-这是一系列连接的线条段。 可以将标记在地图上的路径或窗体中任何所需的形状。

-  **多边形**-这是用于标记在地图上的区域闭合的形状。

-  **圆**-这将在地图上绘制圆形。


<a name="Polylines" />

##### <a name="polylines"></a>折线

A[折线](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Polyline)是一份连续`LatLng`对象指定的每个直线线段的顶点。 通过先创建创建一条折线`PolylineOptions`对象并向其中添加点。 `PolylineOption`对象随后会传递给`GoogleMap`对象通过调用`AddPolyline`方法。

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.
myMap.AddPolyline(rectOptions);
```

<a name="Polygons" />

##### <a name="polygons"></a>多边形

`Polygon`非常类似于`Polyline`s，但是它们未开放结束。 `Polygon`s 封闭的循环并且具有填充其内部。
`Polygon`在作为完全相同的方式进行创建`Polyline`，除[GoogleMap.AddPolygon](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions))方法调用。

与不同`Polyline`、`Polygon`自关闭。 当`AddPolygon`调用方法将自动关闭关闭通过绘制一条连接的第一个和最后一个点的直线多边形。 下面的代码段将通过相同区域中的上一个代码段创建一个实心矩形`Polyline`示例。

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon
myMap.AddPolygon(rectOptions);
```

<a name="Circles" />

##### <a name="circles"></a>圆形

圆形由第一个实例化[CircleOption](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/CircleOptions)将指定中心和的圆的半径米中的对象。 调用在地图上绘制圆[GoogleMap.AddCircle](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap#addCircle(com.google.android.gms.maps.model.CircleOptions))。
下面的代码段演示如何绘制一个圆：

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);
_map.AddCircle (CircleOptions);
```

<a name="RespondingToClicks" />

## <a name="responding-to-events"></a>对事件作出响应

有三种类型的一个用户可能具有一个带有地图的交互：

-  **标记单击**-用户单击一个标记。

-  **标记拖**-用户已长时间-单击 mparger

-  **信息窗口中单击**-用户已单击信息窗口上。

将下面更详细地讨论每个事件。

<a name="Marker_Click_Events" />

### <a name="marker-click-events"></a>单击事件标记

当用户单击在标记上`MarkerClick`将引发事件，和一个`GoogleMap.MarkerClickEventArgs`中传递。 此类包含两个属性：

-  `GoogleMap.MarkerClickEventArgs.Handled` &ndash; 此属性应设置为`true`以指示事件处理程序占用了该事件。 如果此值设置为`false`然后的默认行为将发生除了事件处理程序的自定义行为。

-  `P0` &ndash; 此效果不佳的 name 参数是对引发的标记的引用`MarkerClick`事件。


此代码段演示的示例`MarkerClick`那会将相机位置更改到代码图上的新位置：

```csharp
private void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;
    Marker marker = markerClickEventArgs.P0;
    if (marker.Id.Equals(MyMarkerId)) // The ID of a specific marker the user clicked on.
    {
        _map.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(new LatLng(20.72110, -156.44776), 13));
    }
    else
    {
        Toast.MakeText(this, String.Format("You clicked on Marker ID {0}", marker.Id), ToastLength.Short).Show();
    }
}
```

<a name="Marker_Drag_Events" />

### <a name="marker-drag-events"></a>标记拖动事件

当用户想要将标记拖动时，引发此事件。 默认情况下，标记不可拖动。 标记可以设置为可拖动通过设置`Marker.Draggable`属性`true`或通过调用`MarkerOptions.Draggable`方法替换`true`作为参数。

若要首先拖动标记，用户必须 long 类型的值并单击它并在地图上保留其手指。 当用户拖动其手指在屏幕时，将移动标记。 当用户的手指提起出屏幕中时，标记将保持不变。

以下列表说明的各种事件，将引发可拖动标记：

-   `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; 当用户第一次拖动标记时，引发此事件。

-   `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; 为所拖动标记，将引发此事件。

-   `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; 将引发此事件时在用户完成拖动标记。

每个`EventArgs`包含名为的单个属性`P0`到引用`Marker`对象拖动到。

<a name="Info_Window_Click_Events" />

### <a name="info-window-click-events"></a>信息窗口中单击事件

只有一个信息窗口可以显示一次。 当用户单击图中的信息窗口中时，将引发 map 对象`InfoWindowClick`事件。 下面的代码段演示如何将事件处理程序：

```csharp
private bool SetupMapIfNeeded()
{
    if (_map == null)
    {
        _map = _mapFragment.Map;
        if (_map != null)
        {
            _map.InfoWindowClick += MapOnInfoWindowClick;
            return true;
        }
        return false;
    }
    return true;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.P0;
    // Do something with marker.
}
```

回想一下信息窗口是一个静态`View`它呈现为地图上的图像。 如按钮、 复选框或放置在信息窗口内的文本视图任何小组件将静态，并且无法对任何其整型用户事件作出反应。



## <a name="related-links"></a>相关链接

- [Google Play Services](http://developer.android.com/google/play-services/index.html)
- [Google 映射 Android API v2](https://developers.google.com/maps/documentation/android/)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [获取 Google 地图 API 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [未更新，但 AVD 问题 57880: Google Play Services](https://code.google.com/p/android/issues/detail?id=57880)
