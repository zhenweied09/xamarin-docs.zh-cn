---
title: 章 28 的摘要。 位置和映射
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 40d2b67f1a1655ec1d731493446f320b8aef17ca
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-28-location-and-maps"></a>章 28 的摘要。 位置和映射

Xamarin.Forms 支持[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)元素派生自`View`。 由于使用地图所涉及的特殊平台要求，因此它们实现在一个单独的程序集， **Xamarin.Forms.Maps**，并且涉及不同的命名空间： `Xamarin.Forms.Maps`。

## <a name="the-geographic-coordinate-system"></a>地理坐标系统

地理坐标系统标识球状 （或几乎球状） 的对象，如地球上的位置。 坐标组成同时*纬度*和*经度*用表示的角度。

一个很好圈调用`equator`正好处于两个极地通过其地球轴从概念上讲扩展个中间。

### <a name="parallels-and-latitude"></a>Parallels 和纬度

度量的角单位南北地球标记的中心从赤道的相等纬度称为行*parallels*。 这些范围是从 0 度在赤道到在北部和南极地 90 度。 按照约定，表示赤道以北纬度为正值，和这些表示赤道以南是负值。

### <a name="longitude-and-meridians"></a>经度和经线

从北极到南极的出色圆圈半相等经度的行也被称为*经线*。 这些是相对于在英国格林威治子午线。 按照约定，子午线以东经度正值从 0 度至 180 度，并且子午线以西经度值将成为负数从 0 度到&ndash;180 度。

### <a name="the-equirectangular-projection"></a>Equirectangular 投影

地球任何平面映射引入了失真。 如果的纬度和经度的所有行都是直线，并且如果相等差异纬度和经度角度对应于在地图上相等的距离，则结果是*equirectangular 投影*。 此地图扭曲更接近到极地的区域，因为它们水平拉伸。

### <a name="the-mercator-projection"></a>Mercator 投影

常用*Mercator 投影*尝试弥补水平拉伸通过还垂直延伸这些区域。 这会导致极地附近的区域显示得较大，实际上是，但任何本地区域非常接近符合与实际的区域的映射。

### <a name="map-services-and-tiles"></a>映射服务和磁贴

映射服务使用的变体 Mercator 投影调用`Web Mercator`。 映射服务将位图磁贴传递到客户端根据位置和缩放级别。

## <a name="getting-the-users-location"></a>获取用户的位置

Xamarin.Forms`Map`类不包括一个工具用于获取用户的地理位置，但这通常是需要时使用的地图，因此依赖服务必须处理它。

### <a name="the-location-tracker-api"></a>位置跟踪器 API

[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解决方案包含位置跟踪器 API 的代码。 [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs)结构封装纬度和经度。 [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs)接口定义两种方法来启动和暂停位置跟踪程序，以及一个可用的新位置时的事件。

#### <a name="the-ios-location-manager"></a>IOS 位置管理器

IOS 实现`ILocationTracker`是[ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs)类，该类使使用的 ios [ `CLLocationManager` ](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/)。

#### <a name="the-android-location-manager"></a>Android 位置管理器

Android 实现`ILocationTracker`是[ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs)利用 Android 类[ `LocationManager` ](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)类。

#### <a name="the-windows-runtime-geo-locator"></a>Windows 运行时异地定位符

Windows 运行时实现`ILocationTracker`是[ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs)利用了 UWP 的类[ `Geolocator` ](https://msdn.microsoft.com/library/windows/apps/br225534)。

### <a name="display-the-phones-location"></a>显示电话的位置

[ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI)示例使用的位置跟踪来在文本和 equirectangular 地图上显示电话的位置。

### <a name="the-required-overhead"></a>所需的开销

一些开销，才能使用**WhereAmI**若要使用的位置跟踪。 首先，所有在项目**WhereAmI**解决方案必须具有对中的相应项目的引用**Xamarin.FormsBook.Platform**，和每个**WhereAmI**项目必须调用`Toolkit.Init`方法。

更多的特定于平台的开销，在窗体的位置的权限，是必需的。

#### <a name="location-permission-for-ios"></a>适用于 iOS 的位置权限

对于 iOS， **info.plist**文件必须包含项包含文本的一个问题，询问用户允许获取该用户的位置。

#### <a name="location-permissions-for-android"></a>适用于 Android 的位置权限

获取用户的位置的 android 应用程序必须 ACCESS_FILE_LOCATION 权限 AndroidManifest.xml 文件中。

#### <a name="location-permissions-for-the-windows-runtime"></a>Windows 运行时的位置权限

Windows 或 Windows Phone 应用程序必须具有`location`在 Package.appxmanifest 文件中标记为设备功能。

## <a name="working-with-xamarinformsmaps"></a>使用 Xamarin.Forms.Maps

使用涉及几个要求`Map`类。

### <a name="the-nuget-package"></a>NuGet 包

**Xamarin.Forms.Maps** NuGet 库必须添加到应用程序解决方案。 版本号应该是相同**Xamarin.Forms**当前安装的包。

### <a name="initializing-the-maps-package"></a>初始化地图包

应用程序项目必须调用`Xamarin.FormsMaps.Init`方法之后调用`Xamarin.Forms.Forms.Init`。

### <a name="enabling-map-services"></a>启用地图服务

因为`Map`可以获取用户的位置，则应用程序必须获取这一章前面所述的方式中的用户的权限：

#### <a name="enabling-ios-maps"></a>启用 iOS 映射

IOS 应用程序中使用`Map`需要 info.plist 文件中的两个行。

#### <a name="enabling-android-maps"></a>启用 Android 映射

使用身份验证密钥是必需的使用 Google 映射服务。 在中插入此密钥**AndroidManifest.xml**文件。 此外， **AndroidManifest.xml**文件所需的`manifest`标记参与获取用户的位置。

#### <a name="enabling-windows-runtime-maps"></a>启用 Windows 运行时映射

Windows 运行时应用程序需要使用身份验证密钥，以使用 Bing 地图。 此密钥传递的自变量作为`Xamarin.FormsMaps.Init`方法。 此外必须为定位服务启用应用程序。

### <a name="the-unadorned-map"></a>未修饰的映射

[ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos)示例组成[MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml)文件和[MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs)代码隐藏文件允许导航到各种演示程序。

[BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml)文件演示如何显示[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)视图。 默认情况下它将显示罗马城市，但该映射可操作的用户。

若要禁用水平和垂直滚动，设置[ `HasScrollEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.HasScrollEnabled/)属性`false`。 若要禁用缩放，设置[ `HasZoomEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.HasZoomEnabled/)到`false`。 这些属性可能不适用于所有平台。

### <a name="streets-and-terrain"></a>街道和地形

你可以通过设置来显示不同类型的地图`Map`属性[ `MapType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.MapType/)类型的[ `MapType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapType/)，一个包含三个成员的枚举：

- [`Street`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Street/)默认值
- [`Satellite`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Satellite/)
- [`Hybrid`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Hybrid/)

[MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml)文件演示如何使用单选按钮选择地图类型。 它使用[ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库和类基于[MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml)文件。

### <a name="map-coordinates"></a>地图坐标

程序可以获取当前区域的`Map`显示通过[ `VisibleRegion` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.VisibleRegion/)属性。 此属性是*不*由可绑定的属性，并且没有任何通知机制，以指示何时发生变化，因此想要监视该属性的程序应为该目的可能使用一个计时器。

`VisibleRegion` 类型[ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/)，具有四个只读属性的类：

- [`Center`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.Center/) 类型 [`Position`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/)
- [`LatitudeDegrees`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.LatitudeDegrees/) 类型的`double`，指示地图的显示区域的高度
- [`LongitudeDegrees`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.LongitudeDegrees/) 类型的`double`，指示的地图的显示区域宽度
- [`Radius`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.Radius/) 类型的[ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/)，，该值指示在地图上可见的最大圆形区域的大小

`Position` 和`Distance`是这两个结构。 `Position` 定义通过设置的两个只读属性[`Position`构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Position.Position/p/System.Double/System.Double/):

- [`Latitude`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Position.Latitude/)
- [`Longitude`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Position.Longitude/)

`Distance` 旨在通过度量值和英语单位之间进行转换提供独立于单位的距离。 A`Distance`值可以创建以下几种方式：

- [`Distance` 构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Distance.Distance/p/System.Double/)与以米为单位的距离
- [`Distance.FromMeters`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromMeters/p/System.Double/) 静态方法
- [`Distance.FromKilometers`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromKilometers/p/System.Double/) 静态方法
- [`Distance.FromMiles`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromMiles/p/System.Double/) 静态方法

值是可从三个属性：

- [`Meters`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Meters/) 类型 `double`
- [`Kilometers`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Kilometers/) 类型 `double`
- [`Miles`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Miles/) 类型 `double`

[MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml)文件包含多个`Label`元素用于显示`MapSpan`信息。 [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs)代码隐藏文件使用计时器将更新用户操作映射的信息。

### <a name="position-extensions"></a>位置扩展

本书名为的新库[ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps)包含映射特定但独立于平台的类型。 [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)类具有`ToString`方法`Position`，并计算两个之间的距离的方法`Position`值。

### <a name="setting-an-initial-location"></a>设置的初始位置

你可以调用[ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion/p/Xamarin.Forms.Maps.MapSpan/)方法`Map`以编程方式在地图上设置的位置和缩放级别。 自变量为类型`MapSpan`。 你可以创建`MapSpan`对象使用下列操作之一：

- [`MapSpan` 构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.MapSpan.MapSpan/p/Xamarin.Forms.Maps.Position/System.Double/System.Double/)与`Position`，和纬度和经度跨度
- [`MapSpan.FromCenterAndRadius`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius/p/Xamarin.Forms.Maps.Position/Xamarin.Forms.Maps.Distance/) 与`Position`和 radius

还有可能创建一个新`MapSpan`使用的方法从现有[ `ClampLatitude` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.ClampLatitude/p/System.Double/System.Double/)或[ `WithZoom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.WithZoom/p/System.Double/)。

[WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml)文件和[WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs)代码隐藏文件演示如何使用`MoveToRegion`方法以显示状态怀俄明州。

也可以使用[`Map`构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Map.Map/p/Xamarin.Forms.Maps.MapSpan/)与`MapSpan`对象初始化映射的位置。 [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml)文件演示如何执行此操作完全在 XAML 中旧金山显示 Xamarin 的总部。

### <a name="dynamic-zooming"></a>动态缩放

你可以使用`Slider`来动态缩放地图。 [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml)文件和[RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs)代码隐藏文件演示如何更改基于映射的半径`Slider`值。

[LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml)文件和[LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs)代码隐藏文件显示在 Android，效果更好的替代方法，但这两种做法很好地对 Windows 起作用平台。

### <a name="the-phones-location"></a>电话的位置

[ `IsShowingUser` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.IsShowingUser/)属性`Map`其工作方式略与三个平台上[ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml)文件演示：

- 在 iOS 上则蓝点指示电话的位置，但你必须手动导航到那里
- 在 Android 上，将显示一个图标，当推送移动映射到电话的位置
- UWP 类似于 iOS，但有时自动导航到的位置

**MapDemos**项目尝试通过第一个定义基于一个基于图标的按钮模拟 Android 方法[MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml)文件和[MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs)代码隐藏文件。

[GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml)文件和[GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs)代码隐藏文件使用此按钮可以导航到电话的位置。

### <a name="pins-and-science-museums"></a>Pin 和科学博物馆

最后，`Map`类定义[ `Pins` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.Pins/)类型的属性`IList<Pin>`。 [ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/)类定义四个属性：

- [`Label`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Label/) 类型的`string`、 所需属性
- [`Address`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Address/) 类型的`string`，可选的用户可读地址
- [`Position`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Position/) 类型的`Position`，，该值指示 pin 图上的显示位置
- [`Type`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Type/) 类型的[ `PinType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.PinType/)，枚举中，它不使用

**MapDemos**项目包含文件[ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml)，其中列出了在美国国内的科学博物馆和[ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs)和[`Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs)用于反序列化此数据的类。

[ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml)文件和[ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs)科学博物馆映射中的代码隐藏文件显示 pin。 当用户点击 pin 时，将显示地址和博物馆的网站。

### <a name="the-distance-between-two-points"></a>两个点之间的距离

[ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)类包含[ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88)使用简化的两个地理位置之间的距离计算的方法。

这在中使用[LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml)文件和[LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs)代码隐藏文件，还显示距离到博物馆从用户的位置：

[![本地博物馆页面的三个屏幕截图](images/ch28fg28-small.png "到的位置的距离")](images/ch28fg28-large.png#lightbox "到的位置的距离")

该程序还演示如何动态限制的基于映射的位置的 pin 的数量。

## <a name="geocoding-and-back-again"></a>地理编码和回

[ **Xamarin.Forms.Maps** ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)程序集还包含[ `Geocoder` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Geocoder/)类，该类具有[ `GetPositionsForAddressAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync/p/System.String/)将转换的方法零或更多可能的地理位置和另一种方法将文本地址[ `GetAddressesForPositionAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync/p/Xamarin.Forms.Maps.Position/) ，用于将其他方向。

[GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml)文件和[GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs)代码隐藏文件演示此工具。



## <a name="related-links"></a>相关链接

- [章 28 的全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [章 28 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [地图控件](~/xamarin-forms/user-interface/map.md)
