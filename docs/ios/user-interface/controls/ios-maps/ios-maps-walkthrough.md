---
title: "批注和覆盖层"
description: "本文提供了演示如何使用地图工具包的批注和覆盖功能的分步演练。 它演示如何向 Xamarin 发展 2013年大会，位置显示一个批注和覆盖区上的应用程序中添加地图。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 807ae6554d029120ad28f8fb8cc6c57ba3b58c8c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="annotations-and-overlays--walkthrough"></a>批注和覆盖层 – 演练

我们将在本演练中生成应用程序如下所示：

 [![](ios-maps-walkthrough-images/00-map-overlay.png "示例 MapKit 应用")](ios-maps-walkthrough-images/00-map-overlay.png#lightbox)
 
你可以查找中的已完成的代码**MapsWalkthroughComplete**文件夹内的[映射演示示例](https://developer.xamarin.com/samples/monotouch/MapDemo/)。

让我们首先创建一个新**iOS 空项目**，并为其提供一个相应的名称。 我们将通过将代码添加到我们的视图控制器，以显示 MapView，从开始，并且然后将为我们 MapDelegate 和自定义批注中创建新类。 为此，请按照以下步骤进行操作：

## <a name="viewcontroller"></a>ViewController


1. 添加以下命名空间到`ViewController`:

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. 添加`MKMapView`实例变量到类，以及`MapDelegate`实例。 我们将创建`MapDelegate`很快：

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. 在控制器的`LoadView`方法，添加`MKMapView`和将其设置为`View`控制器的属性：

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    接下来，我们将添加代码以初始化中的映射 ViewDidLoad ' 方法。

1. 在`ViewDidLoad`添加代码以设置地图类型，显示用户位置并使缩放和平移：

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;
    
    ```

1. 接下来，添加代码以定位地图中心并设置它的区域：

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;
    
    ```

1. 创建的新实例`MapDelegate`和将其分配给`Delegate`的`MKMapView`。 同样，我们将 implcodeent`MapDelegate`很快：

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;     
    ```

1. 截至 iOS 8，你应会请求授权从你的用户使用其位置，因此，让我们添加到我们的示例。 首先，定义`CLLocationManager`类级变量：

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. 在`ViewDidLoad`方法，我们想要检查是否运行应用程序的设备正在使用 iOS 8，以及如果它是当应用程序正在使用中，我们将请求授权：

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
                    locationManager.RequestWhenInUseAuthorization ();
                }
    ```

1. 最后，我们需要编辑**Info.plist**文件，告知用户请求其位置的原因。 在**源**菜单**Info.plist**，添加以下项：
    
    `NSLocationWhenInUseUsageDescription` 
    
    和字符串： 

    `Maps Demo`。


## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs – 自定义批注的类


1. 我们将使用自定义的类调用的批注`ConferenceAnnotation`。 向项目中添加以下类：

    ```csharp
    using System;
    using CoreLocation;
    using MapKit;
    
    namespace MapDemo
    {
        public class ConferenceAnnotation : MKAnnotation
        {
            string title;
            CLLocationCoordinate2D coord;
    
            public ConferenceAnnotation (string title,
            CLLocationCoordinate2D coord)
            {
                this.title = title;
                this.coord = coord;
            }
    
            public override string Title {
                get {
                    return title;
                }
            }
    
            public override CLLocationCoordinate2D Coordinate {
                get {
                    return coord;
                }
            }
        }
    }   
    ```

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController-添加批注和覆盖

1. 与`ConferenceAnnotation`就地我们可以将其添加到代码图。 返回`ViewDidLoad`方法`ViewController`，添加批注，请在地图的中心坐标上：

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter)); 
    ```

1. 我们还需要让酒店覆盖。 添加以下代码以创建`MKPolygon`坐标用于酒店提供，并将其添加到映射通过调用`AddOverlay`:

    ```csharp
    // add an overlay of the hotel
    MKPolygon hotelOverlay = MKPolygon.FromCoordinates(
        new CLLocationCoordinate2D[]{
        new CLLocationCoordinate2D(30.2649977168594, -97.73863627705),
        new CLLocationCoordinate2D(30.2648461170005, -97.7381627734755),
        new CLLocationCoordinate2D(30.2648355402574, -97.7381750192576),
        new CLLocationCoordinate2D(30.2647791309417, -97.7379872505988),
        new CLLocationCoordinate2D(30.2654525150319, -97.7377341711021),
        new CLLocationCoordinate2D(30.2654807195004, -97.7377994819399),
        new CLLocationCoordinate2D(30.2655089239607, -97.7377994819399),
        new CLLocationCoordinate2D(30.2656428950368, -97.738346460207),
        new CLLocationCoordinate2D(30.2650364981811, -97.7385709662122),
        new CLLocationCoordinate2D(30.2650470749025, -97.7386199493406)
    });
    
    map.AddOverlay (hotelOverlay);  
    ```
此步骤中的代码将完成`ViewDidLoad`。 现在我们需要实现我们`MapDelegate`类以处理创建批注并分别覆盖视图。


## <a name="mapdelegate"></a>MapDelegate

1. 创建一个名为类`MapDelegate`继承自`MKMapViewDelegate`和包括`annotationId`变量以使用与重复使用标识符的批注：

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```
    我们仅具有一个注释此处以便重复使用代码不是绝对必需的但它是将其包含一个好办法。

1. 实现`GetViewForAnnotation`方法返回的视图`ConferenceAnnotation`使用**conference.png**随附本演练的映像：

    ```csharp
    public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
    {
        MKAnnotationView annotationView = null;
    
        if (annotation is MKUserLocation)
            return null; 
    
        if (annotation is ConferenceAnnotation) {
    
            // show conference annotation
            annotationView = mapView.DequeueReusableAnnotation (annotationId);
    
            if (annotationView == null)
                annotationView = new MKAnnotationView (annotation, annotationId);
        
            annotationView.Image = UIImage.FromFile ("images/conference.png");
            annotationView.CanShowCallout = true;
        } 
    
        return annotationView;
    }
    ```

1. 当用户点击在批注上时，我们想要显示图像显示 Austin 城市。 添加到以下变量`MapDelegate`为图像视图以显示它：

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. 接下来，若要显示图像，点击批注时，实现`DidSelectAnnotation`方法，如下所示：

    ```csharp
    public override void DidSelectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // show an image view when the conference annotation view is selected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView = new UIImageView ();
            venueView.ContentMode = UIViewContentMode.ScaleAspectFit;
            venueImage = UIImage.FromFile ("image/venue.png");
            venueView.Image = venueImage;
            view.AddSubview (venueView);
    
            UIView.Animate (0.4, () => {
            venueView.Frame = new CGRect (-75, -75, 200, 200); });
        }
    }
    ```

1. 若要隐藏图像，用户取消批注选择通过点击图上的其他任何位置时，实现`DidSelectAnnotationView`方法，如下所示：

    ```csharp
    public override void DidDeselectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // remove the image view when the conference annotation is deselected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView.RemoveFromSuperview ();
            venueView.Dispose ();
            venueView = null;
        }
    }
    ```
    现在，我们会就地有批注的代码。 剩下的就是将代码添加到`MapDelegate`创建在酒店覆盖区上的视图。

1. 添加以下实现的`GetViewForOverlay`到`MapDelegate`:

    ```csharp
    public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
    {
        // return a view for the polygon
        MKPolygon polygon = overlay as MKPolygon;
        MKPolygonView polygonView = new MKPolygonView (polygon);
        polygonView.FillColor = UIColor.Blue;
        polygonView.StrokeColor = UIColor.Red;
        return polygonView;
    }
    ```

运行该应用程序。 我们现在有交互式的地图使用自定义批注和一个覆盖区 ！ 在批注上点击并显示 Austin 的映像，如下所示：

 [![](ios-maps-walkthrough-images/01-map-image.png "在批注上点击和显示 Austin 的图像")](ios-maps-walkthrough-images/01-map-image.png#lightbox)

## <a name="summary"></a>摘要

在本文中我们介绍了如何将批注添加到图，以及如何将添加指定的多边形的覆盖层。 我们还演示了如何将触摸屏支持添加到要在地图上对映像进行动画处理的批注。


## <a name="related-links"></a>相关链接

- [映射演示 （示例）](https://developer.xamarin.com/samples/monotouch/MapDemo/)
- [iOS 地图](~/ios/user-interface/controls/ios-maps/index.md)
