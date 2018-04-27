---
title: 映射
description: Xamarin.Forms 使用每个平台上本机映射 Api。
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: e296ca79ee03e7fc61532758219b65946a8d4381
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2018
---
# <a name="map"></a>映射

_Xamarin.Forms 使用每个平台上本机映射 Api。_

Xamarin.Forms.Maps 每个平台上使用本机映射 Api。 这为用户提供快速、 熟悉的地图体验，但意味着需要一些配置步骤遵循每个平台的特定 API 要求。
一次配置，`Map`控制就像任何其他 Xamarin.Forms 元素的公共代码中的工作原理。

* [映射初始化](#Maps_Initialization)-使用`Map`需要在启动时的附加初始化代码。
* [平台配置](#Platform_Configuration)-每个平台所需代码图来处理一些配置。
* [在 C# 中使用地图](#Using_Maps)-显示映射和固定使用 C#。
* [在 XAML 中使用地图](#Using_Xaml)-显示具有 XAML 的映射。

已用于地图控件[MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/)如下所示的示例。

 [![MobileCRM 示例中的映射](map-images/maps-zoom-sml.png "地图控件示例")](map-images/maps-zoom.png#lightbox "地图控件示例")

映射功能可以进一步增强了创建[映射自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)。

<a name="Maps_Initialization" />

## <a name="maps-initialization"></a>地图初始化

向 Xamarin.Forms 应用程序中，添加地图时**Xamarin.Forms.Maps**是单独的 NuGet 包，你应该添加到解决方案中的每个项目。
在 Android 上，这也对 GooglePlayServices (另一个 NuGet) 添加 Xamarin.Forms.Maps 时自动下载有依赖关系。

在安装 NuGet 包后, 不需要某些初始化的代码中的每个应用程序项目，*后*`Xamarin.Forms.Forms.Init`方法调用。 对于 iOS 使用下面的代码：

```csharp
Xamarin.FormsMaps.Init();
```

在 Android 上，你必须传递与相同的参数`Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

有关通用 Windows 平台 (UWP) 使用以下代码：

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

在每个平台的以下文件中添加此调用：

-  **iOS** -AppDelegate.cs 文件，请在`FinishedLaunching`方法。
-  **Android** -MainActivity.cs 文件，请在`OnCreate`方法。
-  **UWP** -MainPage.xaml.cs 文件，请在`MainPage`构造函数。

已添加的 NuGet 包并在每个应用程序内调用初始化方法后`Xamarin.Forms.Maps`Api 可以在常见的 PCL 或共享的项目代码中使用。

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>平台配置

代码图将显示之前，需要在某些平台上执行其他配置步骤。

### <a name="ios"></a>iOS

若要访问在 iOS 上的定位服务，必须设置以下项**Info.plist**:

- iOS 11
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – 在应用程序正在使用中时使用位置服务
    - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) – 在任何时候使用位置服务
- iOS 10 及更早版本
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – 在应用程序正在使用中时使用位置服务
    - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) – 在任何时候使用位置服务    
    
若要支持 iOS 11 及更早版本，你可以包括所有三个密钥： `NSLocationWhenInUseUsageDescription`， `NSLocationAlwaysAndWhenInUseUsageDescription`，和`NSLocationAlwaysUsageDescription`。

在这些密钥的 XML 表示形式**Info.plist**如下所示。 你应更新`string`值以反映你的应用程序如何使用位置信息：

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

**Info.plist**还可在中添加条目**源**视图编辑时**Info.plist**文件：

![对于 iOS 8 的 Info.plist](map-images/ios8-map-permissions.png "iOS 8 所需 Info.plist 条目")


### <a name="android"></a>Android

若要使用[Google 地图 API v2](https://developers.google.com/maps/documentation/android/) ，你必须在 Android 上生成 API 密钥并将其添加到你的 Android 项目。
中的 Xamarin 文档的说明并遵照[获取 Google 地图 API v2 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。
按照这些说明操作之后, 将在 API 密钥粘贴**Properties/AndroidManifest.xml**文件 （查看源和查找/更新下面的元素）：

```xml
<meta-data
        android:name="com.google.android.geo.API_KEY"
        android:value="YOUR_API_KEY"/>
```

如果没有有效的 API 密钥地图控件将显示为灰色的框，在 Android 上。

> [!NOTE]
> 生成使用用于对上载的任何应用程序的版本进行签名的密钥库文件的另一个密钥，请务必到 Google Play 商店。 用于开发生成的密钥，调试将无法工作，并从 Google Play 下载的应用将破坏了地图显示。 另请记住要重新生成密钥时的应用程序的**包名称**更改。

你还需要通过右键单击 Android 项目，然后选择启用适当的权限**选项 > 生成 > Android 应用程序**和计时以下：

* `AccessCoarseLocation`
* `AccessFineLocation`
* `AccessLocationExtraCommands`
* `AccessMockLocation`
* `AccessNetworkState`
* `AccessWifiState`
* `Internet`

其中一些是下面的屏幕截图所示：

![针对 Android 所需权限](map-images/android-map-permissions.png "适用于 Android 的所需的权限")

最后两个是必需的因为应用程序需要网络连接才能下载地图数据。 阅读有关 Android[权限](http://developer.android.com/reference/android/Manifest.permission.html)若要了解详细信息。

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要在通用 Windows 平台上使用地图必须生成的授权令牌。 有关详细信息，请参阅[请求地图身份验证密钥](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx)MSDN 上。

然后应中指定的身份验证令牌`FormsMaps.Init("AUTHORIZATION_TOKEN")`方法调用，以便与 Bing 地图应用进行身份验证。

<a name="Using_Maps" />

## <a name="using-maps"></a>使用映射

请参阅[MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) MobileCRM 示例有关可以如何在代码中使用地图控件的示例中。 一个简单`MapPage`类可能如下所示此-请注意，新`MapSpan`创建来定位地图的视图：

```csharp
public class MapPage : ContentPage {
    public MapPage() {
        var map = new Map(
            MapSpan.FromCenterAndRadius(
                    new Position(37,-122), Distance.FromMiles(0.3))) {
                IsShowingUser = true,
                HeightRequest = 100,
                WidthRequest = 960,
                VerticalOptions = LayoutOptions.FillAndExpand
            };
        var stack = new StackLayout { Spacing = 0 };
        stack.Children.Add(map);
        Content = stack;
    }
}
```

### <a name="map-type"></a>映射类型

此外可以通过设置更改地图内容`MapType`属性，以显示正则街道映射 （默认值）、 卫星照片或两者的组合。

```csharp
map.MapType == MapType.Street;
```

有效`MapType`的值为：

-  混合
-  附属
-  街道 （默认值）


### <a name="map-region-and-mapspan"></a>地图区域和 MapSpan

上面的代码段中所示，提供`MapSpan`映射构造函数的实例设置的初始视图 （中心点和缩放级别） 的映射时加载它。 `MoveToRegion`映射类方法然后可以用于更改映射的位置或缩放级别。 有两种方法来创建一个新`MapSpan`实例：

-  **MapSpan.FromCenterAndRadius()** 的静态方法，用于创建从跨度`Position`并指定`Distance`。
-  **新 MapSpan （)** -使用的构造函数`Position`和的纬度和经度以显示 degress。


若要更改地图的缩放级别，而无需更改位置，创建一个新`MapSpan`使用从当前位置`VisibleRegion.Center`地图控件的属性。 A`Slider`无法用于 （但缩放直接在地图控件中当前不能更新将滑块的值） 控制地图缩放如下：

```csharp
var slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) => {
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

 [![与 zoom 的地图](map-images/maps-zoom-sml.png "地图控件缩放")](map-images/maps-zoom.png#lightbox "地图控件缩放")

### <a name="map-pins"></a>映射 Pin

位置可以标记与图上`Pin`对象。

```csharp
var position = new Position(37,-122); // Latitude, Longitude
var pin = new Pin {
            Type = PinType.Place,
            Position = position,
            Label = "custom pin",
            Address = "custom detail info"
        };
map.Pins.Add(pin);
```

 `PinType` 可以设置为下面的值，从而可能影响在 pin 呈现 （具体取决于平台） 的方法之一：

-  泛型
-  位置
-  SavedPin
-  SearchResult


<a name="Using_Xaml" />

## <a name="using-xaml"></a>使用 Xaml

此代码段中所示，还可以在 Xaml 布局中定位地图。

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
    x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map WidthRequest="320" HeightRequest="200"
            x:Name="MyMap"
            IsShowingUser="true"
            MapType="Hybrid"
        />
    </StackLayout>
</ContentPage>
```

`MapRegion`和`Pins`可以在代码中使用设置`MyMap`引用 （或任何映射为）。 请注意，其他`xmlns`引用 Xamarin.Forms.Maps 控件所需的命名空间定义。

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

<a name="Summary" />

## <a name="summary"></a>总结

Xamarin.Forms.Maps 是必须添加到 Xamarin.Forms 解决方案中的每个项目的单独 NuGet。 附加初始化代码是必需的为 iOS、 Android 和 UWP 和某些配置步骤。

一次配置映射 API 可以用于呈现带 pin 标记，只需几行代码中的地图。 可以使用进一步增强地图[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)。


## <a name="related-links"></a>相关链接

- [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/)
- [映射自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
