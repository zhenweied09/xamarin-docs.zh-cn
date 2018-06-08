---
title: 突出显示在地图上的路由
description: 此文章介绍了如何在向地图添加折线覆盖。 折线覆盖是一系列连接的线条通常用于上一个代码图，显示一个路由或窗体具有必需的任何形状的段。
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 8b80f9569e9377ca76798911cda64d8c0ee28d93
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846638"
---
# <a name="highlighting-a-route-on-a-map"></a>突出显示在地图上的路由

_此文章介绍了如何在向地图添加折线覆盖。折线覆盖是一系列连接的线条通常用于上一个代码图，显示一个路由或窗体具有必需的任何形状的段。_

## <a name="overview"></a>概述

覆盖是在地图上的分层的图形。 叠加支持它被放大随着与地图的绘制图形内容。 以下屏幕快照显示在向地图添加折线覆盖区上的结果：

![](polyline-map-overlay-images/screenshots.png)

当[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) Xamarin.Forms 应用程序，在 iOS 中呈现控件`MapRenderer`该类进行实例化，这反过来实例化一个本机`MKMapView`控件。 在 Android 平台上，`MapRenderer`类实例化一个本机`MapView`控件。 在通用 Windows 平台 (UWP)，`MapRenderer`类实例化一个本机`MapControl`。 呈现进程可以拍摄利用来实现特定于平台的映射自定义项创建的自定义呈现器`Map`每个平台上。 执行此操作的过程如下所示：

1. [创建](#Creating_the_Custom_Map)Xamarin.Forms 自定义地图。
1. [使用](#Consuming_the_Custom_Map)Xamarin.Forms 中的自定义映射。
1. [自定义](#Customizing_the_Map)通过每个平台上创建地图自定义呈现器映射。

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) 必须初始化，并在使用之前配置。 有关详细信息，请参阅 [`Maps Control`](~/xamarin-forms/user-interface/map.md)。

有关自定义地图使用自定义呈现器的信息，请参阅[自定义地图 Pin](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)。

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>创建自定义地图

创建一个子类[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)类，这将添加`RouteCoordinates`属性：

```csharp
public class CustomMap : Map
{
  public List<Position> RouteCoordinates { get; set; }

  public CustomMap ()
  {
    RouteCoordinates = new List<Position> ();
  }
}
```

`RouteCoordinates`属性将存储的定义以突出显示的路由的坐标集合。

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>使用自定义地图

使用`CustomMap`通过声明它的实例 XAML 页实例中的控件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MapOverlay;assembly=MapOverlay"
             x:Class="MapOverlay.MapPage">
    <ContentPage.Content>
        <local:CustomMap x:Name="customMap" MapType="Street" WidthRequest="{x:Static local:App.ScreenWidth}" HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

或者，使用`CustomMap`通过声明它的实例在 C# 页实例中的控件：

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS ()
    {
        var customMap = new CustomMap {
            MapType = MapType.Street,
            WidthRequest = App.ScreenWidth,
            HeightRequest = App.ScreenHeight
        };
        ...
        Content = customMap;
    }
}
```

初始化`CustomMap`根据需要控制：

```csharp
public partial class MapPage : ContentPage
{
  public MapPage ()
  {
    ...
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

此初始化指定一系列的纬度和经度坐标，来突出显示的映射上定义的路由。 它然后定位地图的视图[ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/)方法，通过创建更改的位置和映射的缩放级别[ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/)从[ `Position`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/)和[ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/)。

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>自定义地图

自定义呈现器必须现在添加到要添加到映射的折线覆盖每个应用程序项目中。

#### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上创建自定义呈现器

创建一个子类`MapRenderer`类并重写其`OnElementChanged`方法将添加折线覆盖：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolylineRenderer polylineRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polylineRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.RouteCoordinates.Count];
                int index = 0;
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var routeOverlay = MKPolyline.FromCoordinates(coords);
                nativeMap.AddOverlay(routeOverlay);
            }
        }
        ...
    }
}

```

此方法将执行下面的配置，提供自定义呈现器附加到新 Xamarin.Forms 元素：

- `MKMapView.OverlayRenderer`属性设置为对应的委托。
- 纬度和经度坐标的集合检索从`CustomMap.RouteCoordinates`属性和数组的形式存储`CLLocationCoordinate2D`实例。
- 通过调用静态创建折线`MKPolyline.FromCoordinates`方法，指定的纬度和经度的每个点。
- 折线添加到映射上，通过调用`MKMapView.AddOverlay`方法。

然后，实现`GetOverlayRenderer`方法以自定义在覆盖区上的呈现：

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolylineRenderer polylineRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polylineRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polylineRenderer = new MKPolylineRenderer(overlay as MKPolyline) {
              FillColor = UIColor.Blue,
              StrokeColor = UIColor.Red,
              LineWidth = 3,
              Alpha = 0.4f
          };
      }
      return polylineRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

创建一个子类`MapRenderer`类并重写其`OnElementChanged`和`OnMapReady`添加在折线覆盖区上的方法：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> routeCoordinates;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                routeCoordinates = formsMap.RouteCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polylineOptions = new PolylineOptions();
            polylineOptions.InvokeColor(0x66FF0000);

            foreach (var position in routeCoordinates)
            {
                polylineOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }

            NativeMap.AddPolyline(polylineOptions);
        }
    }
}
```

`OnElementChanged`方法检索集合中的纬度和经度坐标`CustomMap.RouteCoordinates`属性并将它们存储在成员变量。 然后，它调用`MapView.GetMapAsync`方法，获取基础`GoogleMap`，与视图，前提是自定义呈现器附加到新 Xamarin.Forms 元素。 一次`GoogleMap`实例不可用，`OnMapReady`将调用方法，其中折线创建方法是实例化`PolylineOptions`对象，它指定的纬度和经度的每个点。 折线随后通过调用添加到映射`NativeMap.AddPolyline`方法。

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>在通用 Windows 平台上创建自定义呈现器

创建一个子类`MapRenderer`类并重写其`OnElementChanged`方法将添加折线覆盖：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MapControl;

                var coordinates = new List<BasicGeoposition>();
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polyline = new MapPolyline();
                polyline.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polyline.StrokeThickness = 5;
                polyline.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polyline);
            }
        }
    }
}
```

此方法执行以下操作，前提是自定义呈现器附加到新 Xamarin.Forms 元素：

- 纬度和经度坐标的集合检索从`CustomMap.RouteCoordinates`属性和转换到`List`的`BasicGeoposition`坐标。
- 折线创建方法是实例化`MapPolyline`对象。 `MapPolygon`类用于在地图上显示一条线，通过设置其`Path`属性`Geopath`包含的行坐标的对象。
- 将其添加到代码图上呈现折线`MapControl.MapElements`集合。

## <a name="summary"></a>总结

本文介绍了如何将折线覆盖添加到一个图，以在一个代码图，显示一个路由或窗体具有必需的任何形状。


## <a name="related-links"></a>相关链接

- [折线映射 Ovlerlay （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polyline/)
- [自定义图钉](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)
