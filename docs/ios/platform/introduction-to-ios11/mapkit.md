---
title: "在 iOS 11 上 MapKit 中的新增功能"
ms.topic: article
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: 7d21d4ff8960a113cb79582d5cb0849d09f8aaa7
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="new-features-in-mapkit-on-ios-11"></a>在 iOS 11 上 MapKit 中的新增功能

iOS 11 到 MapKit 添加了以下新功能：

- [群集的批注](#clustering)
- [指南针按钮](#compass)
- [缩放视图](#scale)
- [用户跟踪按钮](#user-tracking)

![映射显示群集的标记和罗经按钮](mapkit-images/cyclemap-heading.png)

<a name="clustering" />

## <a name="automatically-grouping-markers-while-zooming"></a>自动缩放时分组标记

示例[MapKit 示例"Tandm"](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)演示如何实现新的 iOS 11 批注群集功能。

### <a name="1-create-an-mkpointannotation-subclass"></a>1.创建`MKPointAnnotation`子类

点批注类表示图上的每个标记。 可以使用单独添加它们`MapView.AddAnnotation()`或从数组使用`MapView.AddAnnotations()`。

点批注类没有可视表示形式，只需要它们来表示与标记关联的数据 (最重要的是，`Coordinate`属性，它是其纬度和经度图上的)，以及任何自定义属性：

```csharp
public class Bike : MKPointAnnotation
{
  public BikeType Type { get; set; } = BikeType.Tricycle;
  public Bike(){}
  public Bike(NSNumber lat, NSNumber lgn, NSNumber type)
  {
    Coordinate = new CLLocationCoordinate2D(lat.NFloatValue, lgn.NFloatValue);
    switch(type.NUIntValue) {
      case 0:
        Type = BikeType.Unicycle;
        break;
      case 1:
        Type = BikeType.Tricycle;
        break;
    }
  }
}
```

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2.创建`MKMarkerAnnotationView`单个标记的子类

该标记批注视图的可视表示形式的每个批注和如使用属性的样式：

- **MarkerTintColor** – 标记的颜色。
- **GlyphText** – 文本显示在标记中。
- **GlyphImage** – 设置了标记中显示的图像。
- **DisplayPriority** – 确定 z 顺序 （堆叠行为） 时映射是与标记拥挤。 使用之一`Required`， `DefaultHigh`，或`DefaultLow`。

若要支持自动群集，你还必须设置：

- **ClusteringIdentifier** – 此设置控制哪些标记获取聚集在一起。 你可以对所有标记，使用相同的标识符或使用不同的标识符来控制组合在一起的方式。

```csharp
[Register("BikeView")]
public class BikeView : MKMarkerAnnotationView
{
  public static UIColor UnicycleColor = UIColor.FromRGB(254, 122, 36);
  public static UIColor TricycleColor = UIColor.FromRGB(153, 180, 44);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;

      var bike = value as Bike;
      if (bike != null){
        ClusteringIdentifier = "bike";
        switch(bike.Type){
          case BikeType.Unicycle:
            MarkerTintColor = UnicycleColor;
            GlyphImage = UIImage.FromBundle("Unicycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultLow;
            break;
          case BikeType.Tricycle:
            MarkerTintColor = TricycleColor;
            GlyphImage = UIImage.FromBundle("Tricycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
            break;
        }
      }
    }
  }
```

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3.创建`MKAnnotationView`来表示群集的标记

尽管表示群集的标记的批注视图_无法_是简单的映像，用户希望应用程序提供有关多少标记具有组合在一起的视觉提示。

[示例代码](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)CoreGraphics 用于呈现在该群集，以及每个标记类型的比例的圆形图表示的标记数。

你还应设置：

- **DisplayPriority** – 确定 z 顺序 （堆叠行为） 时映射是与标记拥挤。 使用之一`Required`， `DefaultHigh`，或`DefaultLow`。
- **CollisionMode** –`Circle`或`Rectangle`。

```csharp
[Register("ClusterView")]
public class ClusterView : MKAnnotationView
{
  public static UIColor ClusterColor = UIColor.FromRGB(202, 150, 38);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;
      var cluster = MKAnnotationWrapperExtensions.UnwrapClusterAnnotation(value);
      if (cluster != null)
      {
        var renderer = new UIGraphicsImageRenderer(new CGSize(40, 40));
        var count = cluster.MemberAnnotations.Length;
        var unicycleCount = CountBikeType(cluster.MemberAnnotations, BikeType.Unicycle);

        Image = renderer.CreateImage((context) => {
          // Fill full circle with tricycle color
          BikeView.TricycleColor.SetFill();
          UIBezierPath.FromOval(new CGRect(0, 0, 40, 40)).Fill();
          // Fill pie with unicycle color
          BikeView.UnicycleColor.SetFill();
          var piePath = new UIBezierPath();
          piePath.AddArc(new CGPoint(20,20), 20, 0, (nfloat)(Math.PI * 2.0 * unicycleCount / count), true);
          piePath.AddLineTo(new CGPoint(20, 20));
          piePath.ClosePath();
          piePath.Fill();
          // Fill inner circle with white color
          UIColor.White.SetFill();
          UIBezierPath.FromOval(new CGRect(8, 8, 24, 24)).Fill();
          // Finally draw count text vertically and horizontally centered
          var attributes = new UIStringAttributes() {
            ForegroundColor = UIColor.Black,
            Font = UIFont.BoldSystemFontOfSize(20)
          };
          var text = new NSString($"{count}");
          var size = text.GetSizeUsingAttributes(attributes);
          var rect = new CGRect(20 - size.Width / 2, 20 - size.Height / 2, size.Width, size.Height);
          text.DrawString(rect, attributes);
        });
      }
    }
  }
  public ClusterView(){}
  public ClusterView(MKAnnotation annotation, string reuseIdentifier) : base(annotation, reuseIdentifier)
  {
    DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
    CollisionMode = MKAnnotationViewCollisionMode.Circle;
    // Offset center point to animate better with marker annotations
    CenterOffset = new CoreGraphics.CGPoint(0, -10);
  }
  private nuint CountBikeType(IMKAnnotation[] members, BikeType type) {
    nuint count = 0;
    foreach(Bike member in members){
      if (member.Type == type) ++count;
    }
    return count;
  }
}
```

### <a name="4-register-the-view-classes"></a>4.注册视图类

当正在创建地图视图控件，并添加到视图中，注册以启用自动聚类分析行为，因为映射被放大和缩小的批注视图类型：

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5.呈现地图 ！

地图呈现时，将群集批注标记，或将其呈现根据缩放级别中。 当缩放级别更改时，标记进行动画处理入和移出群集。

![在图上显示群集的标记的模拟器](mapkit-images/cyclemap-sml.png)

请参阅[映射部分](~/ios/user-interface/controls/ios-maps/index.md)有关使用 MapKit 显示数据的详细信息。

<a name="compass" />

## <a name="compass-button"></a>指南针按钮

iOS 11 添加了弹出外地图指南针并使其在其他位置的视图中的功能。 请参阅[Tandm 示例应用程序](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)有关示例。

创建一个按钮，如下所示指南针 （包括实时动画更改映射方向时），并将其呈现在另一个控件上。

![导航栏中的指南针按钮](mapkit-images/compass-sml.png)

下面的代码创建指南针按钮，并将其呈现在导航栏上：

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

`ShowsCompass`属性可以用于控制地图视图内默认指南针的可见性。

<a name="scale" />

## <a name="scale-view"></a>缩放视图

添加其他位置在视图中使用的小数位数`MKScaleView.FromMapView()`方法要获取其缩放视图在视图层次结构中其他位置添加的实例。

![叠加在地图上的缩放视图](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

`ShowsScale`属性可以用于控制地图视图内默认指南针的可见性。

<a name="user-tracking" />

## <a name="user-tracking-button"></a>用户跟踪按钮

用户跟踪按钮中心用户的当前位置的地图。 使用`MKUserTrackingButton.FromMapView()`方法以获取该按钮的实例，应用格式设置的更改，并在查看层次结构中其他位置添加。

![叠加在地图上的用户位置按钮](mapkit-images/user-location-sml.png)

```csharp
var button = MKUserTrackingButton.FromMapView(MapView);
button.Layer.BackgroundColor = UIColor.FromRGBA(255,255,255,80).CGColor;
button.Layer.BorderColor = UIColor.White.CGColor;
button.Layer.BorderWidth = 1;
button.Layer.CornerRadius = 5;
button.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(button); // constraints omitted for simplicity
```


## <a name="related-links"></a>相关链接

- [MapKit 示例 Tandm](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [什么是新建中 MapKit (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/237/)
