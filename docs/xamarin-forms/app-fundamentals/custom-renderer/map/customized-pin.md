---
title: 自定义图钉
description: 本文演示如何创建地图控件，每个平台上将显示具有自定义的 pin 和固定数据的自定义的视图的本机映射的自定义呈现器。
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: ab5315d169615430f5f5a733c0fa8c2ca9caa4b0
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172296"
---
# <a name="customizing-a-map-pin"></a>自定义图钉

_本文演示如何创建地图控件，每个平台上将显示具有自定义的 pin 和固定数据的自定义的视图的本机映射的自定义呈现器。_

每个 Xamarin.Forms 视图都会随附的呈现器为每个平台创建本机控件的实例。 当[ `Map` ](xref:Xamarin.Forms.Maps.Map) Xamarin.Forms 应用程序在 iOS 中，呈现`MapRenderer`类实例化时，这反过来实例化本机`MKMapView`控件。 在 Android 平台上`MapRenderer`类实例化本机`MapView`控件。 在通用 Windows 平台 (UWP)，`MapRenderer`类实例化本机`MapControl`。 有关呈现器和 Xamarin.Forms 控件映射到的本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明了之间的关系[ `Map` ](xref:Xamarin.Forms.Maps.Map)和相应的本机控件实现它：

![](customized-pin-images/map-classes.png "地图控件和实现的本机控件之间的关系")

呈现过程可用于通过创建自定义呈现器为实现特定于平台的自定义[ `Map` ](xref:Xamarin.Forms.Maps.Map)每个平台上。 执行此操作的过程如下所示：

1. [创建](#Creating_the_Custom_Map)Xamarin.Forms 自定义地图。
1. [使用](#Consuming_the_Custom_Map)Xamarin.Forms 中的自定义映射。
1. [创建](#Creating_the_Custom_Renderer_on_each_Platform)映射每个平台上的自定义呈现器。

每个项将现在讨论反过来，若要实现`CustomMap`呈现器的每个平台上将显示具有自定义的 pin 和固定数据的自定义的视图的本机映射。

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) 必须初始化和使用之前配置。 有关详细信息，请参阅 [`Maps Control`](~/xamarin-forms/user-interface/map.md)。

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>创建自定义地图

可以通过子类化创建一个自定义地图控件[ `Map` ](xref:Xamarin.Forms.Maps.Map)类，如下面的代码示例中所示：

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

`CustomMap`控制在.NET Standard 库项目中创建和定义自定义地图的 API。 自定义地图公开`CustomPins`表示的集合属性`CustomPin`将呈现的每个平台上本机地图控件的对象。 `CustomPin`类下面的代码示例中所示：

```csharp
public class CustomPin : Pin
{
  public string Url { get; set; }
}
```

此类定义`CustomPin`为继承的属性[ `Pin` ](xref:Xamarin.Forms.Maps.Pin)类，并添加`Url`属性。

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>使用自定义地图

`CustomMap`控件可以在 XAML 中引用.NET Standard 库项目通过声明其位置的命名空间和自定义地图控件上使用的命名空间前缀。 下面的代码示例演示如何将`CustomMap`控件可供 XAML 页：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer">
    <ContentPage.Content>
        <local:CustomMap x:Name="myMap" MapType="Street"
          WidthRequest="{x:Static local:App.ScreenWidth}"
          HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

`local`命名空间前缀可以命名任何内容。 但是，`clr-namespace`和`assembly`值必须匹配的自定义地图的详细信息。 一旦声明的命名空间，前缀用于引用自定义映射。

下面的代码示例演示如何将`CustomMap`控件可供 C# 页：

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

`CustomMap`实例将用于显示每个平台上的本机映射。 它具有[ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType)属性设置的显示样式[ `Map` ](xref:Xamarin.Forms.Maps.Map)，与可能的值中定义[ `MapType` ](xref:Xamarin.Forms.Maps.MapType)枚举。 对于 iOS 和 Android，宽度和高度的代码图设置的属性通过`App`初始化特定于平台的项目中的类。

映射和球瓶的位置它包含，如下面的代码示例中所示进行初始化：

```csharp
public MapPage ()
{
  ...
  var pin = new CustomPin {
    Type = PinType.Place,
    Position = new Position (37.79752, -122.40183),
    Label = "Xamarin San Francisco Office",
    Address = "394 Pacific Ave, San Francisco CA",
    Id = "Xamarin",
    Url = "http://xamarin.com/about/"
  };

  customMap.CustomPins = new List<CustomPin> { pin };
  customMap.Pins.Add (pin);
  customMap.MoveToRegion (MapSpan.FromCenterAndRadius (
    new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
}
```

此初始化添加自定义的 pin，并将置于具有地图的视图[ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)方法，以通过创建更改的位置和地图的缩放级别[ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) 从[`Position` ](xref:Xamarin.Forms.Maps.Position)和一个[ `Distance` ](xref:Xamarin.Forms.Maps.Distance)。

自定义呈现器现在可以添加到自定义本机地图控件的每个应用程序项目。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

创建自定义呈现器类的过程如下所示：

1. 创建一个子类`MapRenderer`呈现自定义地图的类。
1. 重写`OnElementChanged`呈现的自定义地图和写入逻辑来其进行自定义的方法。 创建相应的 Xamarin.Forms 自定义地图时，调用此方法。
1. 添加`ExportRenderer`到自定义呈现器类，以指定它将用于呈现 Xamarin.Forms 自定义映射特性。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 若要提供每个平台项目中的自定义呈现器可以选择它。 如果未注册的自定义呈现器，则将使用默认的呈现器的控件的基类。

下图演示了示例应用程序，以及它们之间的关系的每个项目的职责：

![](customized-pin-images/solution-structure.png "CustomMap 自定义呈现器项目职责")

`CustomMap`控件呈现的特定于平台的呈现器类，派生自`MapRenderer`为每个平台的类。 这会导致每个`CustomMap`控制呈现与特定于平台的控件，如以下屏幕截图中所示：

![](customized-pin-images/screenshots.png "每个平台上的 CustomMap")

`MapRenderer`类公开`OnElementChanged`创建 Xamarin.Forms 自定义地图来呈现相应的本机控件时调用的方法。 此方法采用`ElementChangedEventArgs`参数，其中包含`OldElement`和`NewElement`属性。 这些属性表示 Xamarin.Forms 元素的呈现器*已*附加到，和 Xamarin.Forms 元素的呈现器*是*附加到分别。 在示例应用程序`OldElement`属性将为`null`并`NewElement`属性将包含对引用`CustomMap`实例。

重写的版本`OnElementChanged`中每个特定于平台的呈现器类，方法是执行的本机控件自定义的位置。 可以通过访问对正在使用在平台上的本机控件的类型化的引用`Control`属性。 此外，通过获取对所呈现的 Xamarin.Forms 控件的引用`Element`属性。

订阅中的事件处理程序时必须小心`OnElementChanged`方法，如下面的代码示例中所示：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

应配置本机控件，并仅在自定义呈现器附加到新 Xamarin.Forms 元素时事件处理程序订阅。 同样，任何已订阅的事件处理程序应仅当呈现器附加到的元素发生更改时取消订阅。 采用此方法将有助于创建不会遭受内存泄漏的自定义呈现器。

每个自定义呈现器类用修饰`ExportRenderer`与 Xamarin.Forms 结合注册呈现器的属性。 该属性采用两个参数 – 正在呈现的 Xamarin.Forms 自定义控件的类型名称和自定义呈现器的类型名称。 `assembly`到的属性的前缀指定特性应用于整个程序集。

以下各节讨论每个特定于平台的自定义呈现器类的实现。

### <a name="creating-the-custom-renderer-on-ios"></a>在 ios 设备上创建自定义呈现器

下面的屏幕截图显示地图之前和之后自定义项：

![](customized-pin-images/map-layout-ios.png "之前和之后自定义地图控件")

在 iOS 上调用 pin*批注*，可以是自定义映像或不同颜色的系统定义的 pin。 可以选择性地显示批注*标注*，其中显示以响应用户选择批注。 显示标注`Label`并`Address`的属性`Pin`具有可选左和右附件视图实例。 在上面的屏幕截图，左附件视图的 monkey、 图像是右附件视图所*信息*按钮。

下面的代码示例显示了为 iOS 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        UIView customPinView;
        List<CustomPin> customPins;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveAnnotations(nativeMap.Annotations);
                    nativeMap.GetViewForAnnotation = null;
                    nativeMap.CalloutAccessoryControlTapped -= OnCalloutAccessoryControlTapped;
                    nativeMap.DidSelectAnnotationView -= OnDidSelectAnnotationView;
                    nativeMap.DidDeselectAnnotationView -= OnDidDeselectAnnotationView;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                customPins = formsMap.CustomPins;

                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
                nativeMap.CalloutAccessoryControlTapped += OnCalloutAccessoryControlTapped;
                nativeMap.DidSelectAnnotationView += OnDidSelectAnnotationView;
                nativeMap.DidDeselectAnnotationView += OnDidDeselectAnnotationView;
            }
        }
        ...
    }
}
```

`OnElementChanged`方法执行的以下配置[ `MKMapView` ](https://developer.xamarin.com/api/type/MapKit.MKMapView/)实例，前提是自定义呈现器附加到新 Xamarin.Forms 元素：

- [ `GetViewForAnnotation` ](https://developer.xamarin.com/api/property/MapKit.MKMapView.GetViewForAnnotation/)属性设置为`GetViewForAnnotation`方法。 调用此方法时[批注的位置将成为图上可见](#Displaying_the_Annotation)，和用于自定义批注前显示。
- 事件处理程序`CalloutAccessoryControlTapped`， `DidSelectAnnotationView`，和`DidDeselectAnnotationView`注册事件。 这些事件时触发用户[点击的标注正确附件](#Tapping_on_the_Right_Callout_Accessory_View)，以及何时用户[选择](#Selecting_the_Annotation)和[取消选择](#Deselecting_the_Annotation)批注，分别。 仅当元素呈现器附加到更改时，已取消订阅事件。

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>显示批注

`GetViewForAnnotation`时的批注位置变得可见在映射中，以及用于自定义批注前显示调用方法。 批注由两部分组成：

- `MkAnnotation` – 包括标题、 副标题和批注的位置。
- `MkAnnotationView` – 包含图像来表示该批注，和 （可选） 显示了当用户点击批注的标注。

`GetViewForAnnotation`方法接受`IMKAnnotation`，其中包含批注的数据，返回`MKAnnotationView`显示在映射中，并在下面的代码示例所示：

```csharp
protected override MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null) {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Id.ToString());
    if (annotationView == null) {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Id.ToString());
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Id = customPin.Id.ToString();
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

此方法可确保该批注将显示为自定义映像，而不是作为系统定义的 pin，且点击批注标注会显示包含左侧和右侧的批注标题和地址的其他内容. 实现这一点，如下所示：

1. `GetCustomPin`调用方法以返回批注的自定义的 pin 数据。
1. 为了节省内存，批注的视图已入池以供重复使用通过调用[ `DequeueReusableAnnotation` ](https://developer.xamarin.com/api/member/MapKit.MKMapView.DequeueReusableAnnotation/(System.String)/)。
1. `CustomMKAnnotationView`类用于扩展`MKAnnotationView`类与`Id`并`Url`对应于中的相同属性的属性`CustomPin`实例。 新实例`CustomMKAnnotationView`创建，前提是该批注是`null`:
    - `CustomMKAnnotationView.Image`属性设置为将表示在地图上的批注的图像。
    - `CustomMKAnnotationView.CalloutOffset`属性设置为`CGPoint`，它指定将在标注居中上面批注。
    - `CustomMKAnnotationView.LeftCalloutAccessoryView`属性设置为批注标题和地址的左侧将出现 monkey 的映像。
    - `CustomMKAnnotationView.RightCalloutAccessoryView`属性设置为*信息*批注标题和地址右侧将显示的按钮。
    - `CustomMKAnnotationView.Id`属性设置为`CustomPin.Id`属性返回`GetCustomPin`方法。 这样，使其可以识别的批注[可进一步自定义标注](#Selecting_the_Annotation)，如果所需的。
    - `CustomMKAnnotationView.Url`属性设置为`CustomPin.Url`属性返回`GetCustomPin`方法。 URL 将导航到时用户[点击右标注附件视图中显示的按钮](#Tapping_on_the_Right_Callout_Accessory_View)。
1. [ `MKAnnotationView.CanShowCallout` ](https://developer.xamarin.com/api/property/MapKit.MKAnnotationView.CanShowCallout/)属性设置为`true`以便标注批注点击时显示。
1. 批注返回要显示在代码图上。

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>选择批注

当用户点击该批注，`DidSelectAnnotationView`事件触发时，后者将执行`OnDidSelectAnnotationView`方法：

```csharp
void OnDidSelectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  customPinView = new UIView ();

  if (customView.Id == "Xamarin") {
    customPinView.Frame = new CGRect (0, 0, 200, 84);
    var image = new UIImageView (new CGRect (0, 0, 200, 84));
    image.Image = UIImage.FromFile ("xamarin.png");
    customPinView.AddSubview (image);
    customPinView.Center = new CGPoint (0, -(e.View.Frame.Height + 75));
    e.View.AddSubview (customPinView);
  }
}
```

此方法通过添加扩展 （其中包含向左和右附件视图） 的现有标注`UIView`实例与它包含 Xamarin 徽标的图像，前提是所选的批注都有其`Id`属性设置为`Xamarin`. 这样可以对不同批注显示不同标注的位置的方案。 `UIView`将上面的现有标注中间位置显示实例。

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>点击右标注附件视图

当在用户点击*信息*在右标注附件视图中，按钮`CalloutAccessoryControlTapped`事件触发时，后者将执行`OnCalloutAccessoryControlTapped`方法：

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

此方法打开 web 浏览器并导航到存储中的地址`CustomMKAnnotationView.Url`属性。 请注意，在创建时已定义地址`CustomPin`.NET Standard 库项目中的集合。

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>取消选中批注

该批注显示并在映射中，在用户点击`DidDeselectAnnotationView`事件触发时，后者将执行`OnDidDeselectAnnotationView`方法：

```csharp
void OnDidDeselectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  if (!e.View.Selected) {
    customPinView.RemoveFromSuperview ();
    customPinView.Dispose ();
    customPinView = null;
  }
}
```

此方法可确保在未选择现有的标注、 标注 （Xamarin 徽标的图像） 的扩展的部分也将停止正在显示，并将释放其资源时。

有关自定义的详细信息`MKMapView`实例，请参阅[iOS 地图](~/ios/user-interface/controls/ios-maps/index.md)。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

下面的屏幕截图显示地图之前和之后自定义项：

![](customized-pin-images/map-layout-android.png "之前和之后自定义地图控件")

在 Android 上调用 pin*标记*，并且可以自定义映像或系统定义的各种颜色标记。 标记可以显示*信息窗口*，其中显示在用户点击针对标记的响应。 信息窗口显示`Label`并`Address`的属性`Pin`实例，并可自定义以包括其他内容。 但是，只能有一个信息窗口可以显示在一次。

下面的代码示例显示了 Android 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.Droid
{
    public class CustomMapRenderer : MapRenderer, GoogleMap.IInfoWindowAdapter
    {
        List<CustomPin> customPins;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                NativeMap.InfoWindowClick -= OnInfoWindowClick;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                customPins = formsMap.CustomPins;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(GoogleMap map)
        {
            base.OnMapReady(map);

            NativeMap.InfoWindowClick += OnInfoWindowClick;
            NativeMap.SetInfoWindowAdapter(this);
        }
        ...
    }
}
```

提供自定义呈现器附加到新 Xamarin.Forms 元素，`OnElementChanged`方法调用`MapView.GetMapAsync`方法，获取基础`GoogleMap`与视图。 一次`GoogleMap`实例不可用，`OnMapReady`将调用重写。 此方法注册的事件处理程序`InfoWindowClick`事件，则会激发[任务栏信息窗口](#Clicking_on_the_Info_Window)，并仅当元素呈现器附加到更改时从取消订阅。 `OnMapReady`重写还会调用`SetInfoWindowAdapter`方法，以指定的`CustomMapRenderer`类实例将提供方法以自定义信息窗口。

`CustomMapRenderer`类实现`GoogleMap.IInfoWindowAdapter`接口[自定义的信息窗口](#Customizing_the_Info_Window)。 此接口指定必须实现以下方法：

- `public Android.Views.View GetInfoWindow(Marker marker)` – 此方法调用返回一个标记的自定义的信息窗口。 如果它返回`null`，则将使用默认的窗口呈现。 如果它返回`View`，然后，`View`置于信息窗口框架。
- `public Android.Views.View GetInfoContents(Marker marker)` – 此方法调用以返回`View`包含内容的信息窗口中，并将只有当调用`GetInfoWindow`方法将返回`null`。 如果它返回`null`，则将使用的信息窗口内容的默认呈现。

在示例应用程序，自定义信息的窗口内容，因此`GetInfoWindow`方法将返回`null`要启用此功能。

#### <a name="customizing-the-marker"></a>自定义标记

可以通过调用自定义用于表示标记的图标`MarkerOptions.SetIcon`方法。 这可以通过重写`CreateMarker`方法，为每个调用`Pin`，添加到映射：

```csharp
protected override MarkerOptions CreateMarker(Pin pin)
{
    var marker = new MarkerOptions();
    marker.SetPosition(new LatLng(pin.Position.Latitude, pin.Position.Longitude));
    marker.SetTitle(pin.Label);
    marker.SetSnippet(pin.Address);
    marker.SetIcon(BitmapDescriptorFactory.FromResource(Resource.Drawable.pin));
    return marker;
}
```

此方法创建一个新`MarkerOption`为每个实例`Pin`实例。 设置位置、 标签和标记的地址后, 其图标设置为`SetIcon`方法。 此方法采用`BitmapDescriptor`对象，其中包含必要的数据以与呈现图标`BitmapDescriptorFactory`类，用于提供帮助器方法来简化创建`BitmapDescriptor`。 有关使用详细信息`BitmapDescriptorFactory`类，以自定义标记，请参阅[自定义标记](~/android/platform/maps-and-location/maps/maps-api.md)。

> [!NOTE]
> 如果需要，请`GetMarkerForPin`方法可以调用以检索在地图呈现器中`Marker`从`Pin`。

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>自定义信息窗口

在用户点击标记上时`GetInfoContents`执行方法时，提供的`GetInfoWindow`方法将返回`null`。 下面的代码示例演示`GetInfoContents`方法：

```csharp
public Android.Views.View GetInfoContents (Marker marker)
{
  var inflater = Android.App.Application.Context.GetSystemService (Context.LayoutInflaterService) as Android.Views.LayoutInflater;
  if (inflater != null) {
    Android.Views.View view;

    var customPin = GetCustomPin (marker);
    if (customPin == null) {
      throw new Exception ("Custom pin not found");
    }

    if (customPin.Id.ToString() == "Xamarin") {
      view = inflater.Inflate (Resource.Layout.XamarinMapInfoWindow, null);
    } else {
      view = inflater.Inflate (Resource.Layout.MapInfoWindow, null);
    }

    var infoTitle = view.FindViewById<TextView> (Resource.Id.InfoWindowTitle);
    var infoSubtitle = view.FindViewById<TextView> (Resource.Id.InfoWindowSubtitle);

    if (infoTitle != null) {
      infoTitle.Text = marker.Title;
    }
    if (infoSubtitle != null) {
      infoSubtitle.Text = marker.Snippet;
    }

    return view;
  }
  return null;
}
```

此方法返回`View`包含信息窗口的内容。 实现这一点，如下所示：

- 一个`LayoutInflater`检索实例。 这用于实例化到其对应的布局 XML 文件`View`。
- `GetCustomPin`调用方法以返回信息窗口中的自定义的 pin 数据。
- `XamarinMapInfoWindow`如果被放大布局`CustomPin.Id`属性等于`Xamarin`。 否则为`MapInfoWindow`放大布局。 这样可以显示为不同的标记的信息不同窗口布局的位置的方案。
- `InfoWindowTitle`和`InfoWindowSubtitle`膨胀的布局中，从检索资源及其`Text`属性设置为相应的数据从`Marker`实例，前提是不是资源`null`。
- `View`实例返回要显示在代码图上。

> [!NOTE]
> 为非活动的信息窗口`View`。 相反，Android 会将转换`View`为静态位图和显示的为图像。 这意味着一个 click 事件，它不能响应任何触控事件或手势，并在信息窗口中的各个控件不能响应到其自身可以响应的信息窗口时，单击事件。

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>单击信息窗口

当用户在信息窗口中，单击`InfoWindowClick`事件触发时，后者将执行`OnInfoWindowClick`方法：

```csharp
void OnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
  var customPin = GetCustomPin (e.Marker);
  if (customPin == null) {
    throw new Exception ("Custom pin not found");
  }

  if (!string.IsNullOrWhiteSpace (customPin.Url)) {
    var url = Android.Net.Uri.Parse (customPin.Url);
    var intent = new Intent (Intent.ActionView, url);
    intent.AddFlags (ActivityFlags.NewTask);
    Android.App.Application.Context.StartActivity (intent);
  }
}
```

此方法打开 web 浏览器并导航到存储中的地址`Url`检索到的属性`CustomPin`实例`Marker`。 请注意，在创建时已定义地址`CustomPin`.NET Standard 库项目中的集合。

有关自定义的详细信息`MapView`实例，请参阅[地图 API](~/android/platform/maps-and-location/maps/maps-api.md)。

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>在通用 Windows 平台上创建自定义呈现器

下面的屏幕截图显示地图之前和之后自定义项：

![](customized-pin-images/map-layout-uwp.png "之前和之后自定义地图控件")

在 UWP 上调用 pin*结构图图标*，并且可以自定义映像或系统定义的默认图像。 图图标可以显示`UserControl`，以响应用户点击的地图图标上显示。 `UserControl`可以显示任何内容，包括`Label`并`Address`的属性`Pin`实例。

下面的代码示例显示了 UWP 自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        MapControl nativeMap;
        List<CustomPin> customPins;
        XamarinMapOverlay mapOverlay;
        bool xamarinOverlayShown = false;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                nativeMap.MapElementClick -= OnMapElementClick;
                nativeMap.Children.Clear();
                mapOverlay = null;
                nativeMap = null;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                nativeMap = Control as MapControl;
                customPins = formsMap.CustomPins;

                nativeMap.Children.Clear();
                nativeMap.MapElementClick += OnMapElementClick;

                foreach (var pin in customPins)
                {
                    var snPosition = new BasicGeoposition { Latitude = pin.Pin.Position.Latitude, Longitude = pin.Pin.Position.Longitude };
                    var snPoint = new Geopoint(snPosition);

                    var mapIcon = new MapIcon();
                    mapIcon.Image = RandomAccessStreamReference.CreateFromUri(new Uri("ms-appx:///pin.png"));
                    mapIcon.CollisionBehaviorDesired = MapElementCollisionBehavior.RemainVisible;
                    mapIcon.Location = snPoint;
                    mapIcon.NormalizedAnchorPoint = new Windows.Foundation.Point(0.5, 1.0);

                    nativeMap.MapElements.Add(mapIcon);                    
                }
            }
        }
        ...
    }
}
```

`OnElementChanged`方法，提供自定义呈现器附加到新 Xamarin.Forms 元素将执行以下操作：

- 它将清除`MapControl.Children`若要从映射中，在注册的事件处理程序之前删除任何现有的用户界面元素的集合`MapElementClick`事件。 当用户在点击或单击时将引发此事件`MapElement`上`MapControl`，并仅当元素呈现器附加到更改时从取消订阅。
- 中的每个 pin`customPins`集合显示在地图上的正确地理位置，如下所示：
  - Pin 的位置创建为`Geopoint`实例。
  - 一个`MapIcon`创建实例来表示 pin。
  - 图像用于表示`MapIcon`通过设置指定`MapIcon.Image`属性。 但是，地图图标的图像是不能始终保证要显示，因为它可能会被在地图上其他元素遮挡。 因此，映射图标`CollisionBehaviorDesired`属性设置为`MapElementCollisionBehavior.RemainVisible`，以确保它仍显示。
  - 位置`MapIcon`通过设置指定`MapIcon.Location`属性。
  - `MapIcon.NormalizedAnchorPoint`属性设置为将指针悬停在图像的近似位置。 如果此属性保持不变的 (0，0)，它表示图像的左上的角，其默认值中的地图的缩放级别的更改可能会导致指向其他位置的映像。
  - `MapIcon`实例添加到`MapControl.MapElements`集合。 这会导致上显示的地图图标`MapControl`。

> [!NOTE]
> 对于多个地图图标，使用相同的映像时`RandomAccessStreamReference`为了获得最佳性能，应在页面或应用程序级别声明实例。

#### <a name="displaying-the-usercontrol"></a>显示此用户控件

在用户点击映射图标时`OnMapElementClick`执行方法。 下面的代码示例显示了此方法：

```csharp
private void OnMapElementClick(MapControl sender, MapElementClickEventArgs args)
{
    var mapIcon = args.MapElements.FirstOrDefault(x => x is MapIcon) as MapIcon;
    if (mapIcon != null)
    {
        if (!xamarinOverlayShown)
        {
            var customPin = GetCustomPin(mapIcon.Location.Position);
            if (customPin == null)
            {
                throw new Exception("Custom pin not found");
            }

            if (customPin.Id.ToString() == "Xamarin")
            {
                if (mapOverlay == null)
                {
                    mapOverlay = new XamarinMapOverlay(customPin);
                }

                var snPosition = new BasicGeoposition { Latitude = customPin.Pin.Position.Latitude, Longitude = customPin.Pin.Position.Longitude };
                var snPoint = new Geopoint(snPosition);

                nativeMap.Children.Add(mapOverlay);
                MapControl.SetLocation(mapOverlay, snPoint);
                MapControl.SetNormalizedAnchorPoint(mapOverlay, new Windows.Foundation.Point(0.5, 1.0));
                xamarinOverlayShown = true;
            }
        }
        else
        {
            nativeMap.Children.Remove(mapOverlay);
            xamarinOverlayShown = false;
        }
    }
}
```

此方法创建`UserControl`显示 pin 有关的信息的实例。 实现这一点，如下所示：

- `MapIcon`检索实例。
- `GetCustomPin`调用方法以返回要显示的自定义的 pin 数据。
- 一个`XamarinMapOverlay`创建实例以显示自定义的 pin 数据。 此类是一个用户控件。
- 要显示的地理位置`XamarinMapOverlay`实例上`MapControl`将创建为`Geopoint`实例。
- `XamarinMapOverlay`实例添加到`MapControl.Children`集合。 此集合包含将地图显示的 XAML 用户界面元素。
- 地理位置`XamarinMapOverlay`通过调用设置在地图上的实例`SetLocation`方法。
- 上的相对位置`XamarinMapOverlay`实例中，对应于指定位置，将通过调用`SetNormalizedAnchorPoint`方法。 这可确保，在中的映射结果的缩放级别更改`XamarinMapOverlay`实例始终显示在正确位置。

或者，如果已经在地图上显示有关 pin 信息，在地图上的点击中移除`XamarinMapOverlay`实例与`MapControl.Children`集合。

#### <a name="tapping-on-the-information-button"></a>点击信息按钮

当在用户点击*信息*按钮`XamarinMapOverlay`用户控件`Tapped`事件触发时，后者将执行`OnInfoButtonTapped`方法：

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

此方法打开 web 浏览器并导航到存储中的地址`Url`属性的`CustomPin`实例。 请注意，在创建时已定义地址`CustomPin`.NET Standard 库项目中的集合。

有关自定义的详细信息`MapControl`实例，请参阅[地图和位置概述](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx)MSDN 上。

## <a name="summary"></a>总结

本文演示了如何创建自定义呈现器`Map`控件，使开发人员能够重写其自己特定于平台的自定义的默认本机呈现。 Xamarin.Forms.Maps 提供跨平台抽象，用于显示可使用本机 Api 每个平台上，以提供快速、 熟悉的映射体验的地图进行用户的映射。


## <a name="related-links"></a>相关链接

- [地图控件](~/xamarin-forms/user-interface/map.md)
- [iOS 地图](~/ios/user-interface/controls/ios-maps/index.md)
- [地图 API](~/android/platform/maps-and-location/maps/maps-api.md)
- [自定义的 Pin （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)
