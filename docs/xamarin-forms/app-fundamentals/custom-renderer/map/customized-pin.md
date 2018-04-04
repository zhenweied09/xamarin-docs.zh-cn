---
title: 自定义地图 Pin
description: 本文演示如何创建自定义呈现器地图控件，每个平台上将显示自定义的 pin 与 pin 数据的自定义的视图的本机映射。
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 353575bad91c9bade0207a0aa271d9de7ec50240
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="customizing-a-map-pin"></a>自定义地图 Pin

_本文演示如何创建自定义呈现器地图控件，每个平台上将显示自定义的 pin 与 pin 数据的自定义的视图的本机映射。_

Xamarin.Forms 中的每个视图已随附的呈现器针对每个平台创建的本机控件的实例。 当[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) Xamarin.Forms 应用程序在 iOS 中，呈现`MapRenderer`该类进行实例化，这反过来实例化一个本机`MKMapView`控件。 在 Android 平台上，`MapRenderer`类实例化一个本机`MapView`控件。 在通用 Windows 平台 (UWP)，`MapRenderer`类实例化一个本机`MapControl`。 有关呈现器和 Xamarin.Forms 控件映射到的本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明之间的关系[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)和相应的本机控件实现它：

![](customized-pin-images/map-classes.png "地图控件和实现的本机控件之间的关系")

呈现进程可以用于通过创建自定义呈现器实现特定于平台的自定义项[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)每个平台上。 执行此操作的过程如下所示：

1. [创建](#Creating_the_Custom_Map)Xamarin.Forms 自定义地图。
1. [使用](#Consuming_the_Custom_Map)Xamarin.Forms 中的自定义映射。
1. [创建](#Creating_the_Custom_Renderer_on_each_Platform)映射每个平台上的自定义呈现器。

每个项将现在讨论反过来，若要实现`CustomMap`具有自定义的 pin 和固定数据的自定义的视图的本机映射显示每个平台的呈现器。

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) 必须初始化，并在使用之前配置。 有关详细信息，请参阅 [`Maps Control`](~/xamarin-forms/user-interface/map.md)。

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>创建自定义地图

可以通过子类化创建一个自定义地图控件[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)类，如下面的代码示例中所示：

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

`CustomMap`控制在可移植类库 (PCL) 项目中创建和定义用于自定义地图的 API。 自定义地图公开`CustomPins`表示的集合的属性`CustomPin`都将由每个平台上本机地图控件呈现的对象。 `CustomPin`类下面的代码示例中所示：

```csharp
public class CustomPin : Pin
{
  public string Url { get; set; }
}
```

此类定义`CustomPin`为继承的属性[ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/)类，并将添加`Url`属性。

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>使用自定义地图

`CustomMap`控件可以引用 XAML 中 PCL 项目中通过声明其位置的命名空间和自定义地图控件上使用的命名空间前缀。 下面的代码示例演示如何`CustomMap`控件可供 XAML 页：

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

下面的代码示例演示如何`CustomMap`控件可供 C# 页：

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

`CustomMap`实例将用于在每个平台上显示本机映射。 它具有[ `MapType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.MapType/)属性设置的显示样式[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)，与可能正在定义中的值[ `MapType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapType/)枚举。 对于 iOS 和 Android 的宽度和高度映射的设置的属性通过`App`在特定于平台的项目中初始化的类。

映射和 pin 的位置它包含，如下面的代码示例中所示初始化：

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

此初始化添加自定义的 pin 并置于具有地图的视图[ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/)方法，通过创建更改的位置和映射的缩放级别[ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/)从[`Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/)和[ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/)。

自定义呈现器现在可以添加到每个应用程序项目，以自定义的本机地图控件。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

用于创建自定义呈现器类的过程如下所示：

1. 创建一个子类`MapRenderer`呈现自定义地图的类。
1. 重写`OnElementChanged`呈现自定义地图和写逻辑它进行自定义的方法。 创建相应的 Xamarin.Forms 自定义映射时，调用此方法。
1. 添加`ExportRenderer`到自定义呈现器类，以指定它将可用于呈现 Xamarin.Forms 自定义映射特性。 此属性用于与 xamarin.forms 结合注册自定义呈现器。

> [!NOTE]
> 它是可选若要提供每个平台项目中的自定义呈现器。 如果自定义呈现器未注册，则将使用控件的基类的默认呈现器。

下图说明了示例应用程序，以及它们之间的关系中的每个项目的责任：

![](customized-pin-images/solution-structure.png "CustomMap 自定义呈现器项目职责")

`CustomMap`时呈现控件，通过特定于平台的呈现器类，该类派生自`MapRenderer`为每个平台的类。 这将导致每个`CustomMap`控制呈现使用特定于平台的控件，如以下屏幕截图中所示：

![](customized-pin-images/screenshots.png "每个平台上的 CustomMap")

`MapRenderer`类会公开`OnElementChanged`方法，该 Xamarin.Forms 自定义地图创建呈现相应的本机控件时，调用方法。 此方法采用`ElementChangedEventArgs`参数，其中包含`OldElement`和`NewElement`属性。 这些属性表示 Xamarin.Forms 元素的呈现器*已*相连，和 Xamarin.Forms 元素的呈现器*是*附加，分别。 在示例应用程序`OldElement`属性将为`null`和`NewElement`属性将包含对引用`CustomMap`实例。

重写的版本`OnElementChanged`中每个特定于平台的呈现器类，方法是执行本机控件自定义的位置。 可以通过访问在平台上使用本机控件的类型化的引用`Control`属性。 此外，可以通过获取对呈现 Xamarin.Forms 控件的引用`Element`属性。

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

应配置本机控件，并仅在自定义呈现器附加到新 Xamarin.Forms 元素时事件处理程序订阅。 同样，任何已订阅的事件处理程序应仅在呈现器附加到的元素发生变化时取消订阅。 采用此方法将有助于创建不会受到内存泄漏的自定义呈现器。

每个自定义呈现器类用修饰`ExportRenderer`与 xamarin.forms 结合注册呈现器的属性。 该属性采用两个参数 – 呈现，Xamarin.Forms 自定义控件的类型名称和自定义呈现器的类型名称。 `assembly`到属性的前缀指定特性应用于整个程序集。

以下各节讨论每个特定于平台的自定义呈现器类的实现。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上创建自定义呈现器

以下屏幕截图显示地图之前和之后自定义项：

![](customized-pin-images/map-layout-ios.png "地图控件之前和之后自定义项")

在 iOS 上 pin 称为*批注*，并且可以自定义映像或系统定义的各种颜色 pin。 （可选） 可以显示批注*标注*，显示以响应用户选择批注。 标注显示`Label`和`Address`属性`Pin`实例，具有可选左和右附件的视图。 在上面的屏幕截图，左附件视图的 monkey 图像是右附件视图为*信息*按钮。

下面的代码示例演示为 iOS 平台的自定义呈现器：

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

- [ `GetViewForAnnotation` ](https://developer.xamarin.com/api/property/MapKit.MKMapView.GetViewForAnnotation/)属性设置为`GetViewForAnnotation`方法。 当调用此方法[批注的位置将成为在地图上可见](#Displaying_the_Annotation)，和用于自定义批注之前显示。
- 事件处理程序`CalloutAccessoryControlTapped`， `DidSelectAnnotationView`，和`DidDeselectAnnotationView`注册事件。 这些事件激发时用户[点击的标注正确附件](#Tapping_on_the_Right_Callout_Accessory_View)，以及何时用户[选择](#Selecting_the_Annotation)和[取消选择](#Deselecting_the_Annotation)批注，分别。 仅当元素呈现器附加到更改时，事件均取消订阅。

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>显示批注

`GetViewForAnnotation`批注的位置将变得可见在映射上，以及用于自定义显示批注之前调用方法。 批注有两个部分：

- `MkAnnotation` – 包括标题、 副标题和注释的位置。
- `MkAnnotationView` – 包含要表示批注，和 （可选） 将显示当用户点击批注标注的图像。

`GetViewForAnnotation`方法接受`IMKAnnotation`，其中包含批注的数据，返回`MKAnnotationView`以便在地图上显示和下面的代码示例所示：

```csharp
MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
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

此方法可确保批注将显示为一个自定义映像，而不是作为系统定义的 pin，并且当点击批注标注将显示包含其他内容的左侧和右侧的批注标题和地址. 这被实现的如下所示：

1. `GetCustomPin`方法调用可返回的批注的自定义 pin 数据。
1. 以节省内存，批注的视图存入池中以备调用重用[ `DequeueReusableAnnotation` ](https://developer.xamarin.com/api/member/MapKit.MKMapView.DequeueReusableAnnotation/(System.String)/)。
1. `CustomMKAnnotationView`类扩展`MKAnnotationView`类，该类具有`Id`和`Url`属性对应于中的相同属性`CustomPin`实例。 新实例`CustomMKAnnotationView`假设的批注不创建`null`:
  - `CustomMKAnnotationView.Image`属性设置为将表示地图上的批注的图像。
  - `CustomMKAnnotationView.CalloutOffset`属性设置为`CGPoint`，它指定，将上面批注中间标注的位置。
  - `CustomMKAnnotationView.LeftCalloutAccessoryView`属性设置为左侧的批注标题和地址将出现 monkey 的映像。
  - `CustomMKAnnotationView.RightCalloutAccessoryView`属性设置为*信息*将显示的批注标题和地址右侧的按钮。
  - `CustomMKAnnotationView.Id`属性设置为`CustomPin.Id`属性返回`GetCustomPin`方法。 这样，使其具有标识的批注[可以进一步自定义标注](#Selecting_the_Annotation)，如果所需的。
  - `CustomMKAnnotationView.Url`属性设置为`CustomPin.Url`属性返回`GetCustomPin`方法。 URL 将导航到时用户[点击按钮显示在右标注附件视图](#Tapping_on_the_Right_Callout_Accessory_View)。
1. [ `MKAnnotationView.CanShowCallout` ](https://developer.xamarin.com/api/property/MapKit.MKAnnotationView.CanShowCallout/)属性设置为`true`以便点击批注时，将显示该标注。
1. 批注返回要显示在地图上。

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>选择批注

当用户点击该批注，`DidSelectAnnotationView`事件激发，后者反过来执行`OnDidSelectAnnotationView`方法：

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

此方法通过添加扩展 （其中包含向左和右附件视图） 的现有标注`UIView`到它的实例，它包含 Xamarin 徽标的图像，前提是所选的批注都有其`Id`属性设置为`Xamarin`. 这使得可以对不同批注显示不同的标注的位置的方案。 `UIView`实例将显示现有标注上面中间位置。

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>在右标注附件视图上的点击

当用户点击*信息*在右标注附件视图中，按钮`CalloutAccessoryControlTapped`事件激发，后者反过来执行`OnCalloutAccessoryControlTapped`方法：

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

此方法打开 web 浏览器并导航到存储中的地址`CustomMKAnnotationView.Url`属性。 请注意，在创建时已定义地址`CustomPin`PCL 项目中的集合。

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>取消选中批注

当该批注显示并且用户在映射上，点击`DidDeselectAnnotationView`事件激发，后者反过来执行`OnDidDeselectAnnotationView`方法：

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

此方法确保未选择现有的标注、 标注 （Xamarin 徽标的映像） 的扩展的部分也将停止显示，并且将释放其资源。

有关自定义的详细信息`MKMapView`实例，请参阅[iOS 地图](~/ios/user-interface/controls/ios-maps/index.md)。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

以下屏幕截图显示地图之前和之后自定义项：

![](customized-pin-images/map-layout-android.png "地图控件之前和之后自定义项")

在 Android 上 pin 称为*标记*，并且可以自定义映像或系统定义的各种颜色标记。 标记可以显示*信息窗口*，以响应用户点击在标记上显示。 信息窗口显示`Label`和`Address`属性`Pin`实例，并可以自定义为包括其他内容。 但是，只有一个信息窗口可以显示在一次。

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

假设自定义呈现器附加到新的 Xamarin.Forms 元素，`OnElementChanged`方法调用`MapView.GetMapAsync`方法，获取基础`GoogleMap`与视图。 一次`GoogleMap`实例不可用，`OnMapReady`将调用替代。 此方法注册的事件处理程序`InfoWindowClick`事件，则会激发[任务栏信息窗口](#Clicking_on_the_Info_Window)，并仅在元素呈现器附加到更改时，才从取消订阅。 `OnMapReady`还重写调用`SetInfoWindowAdapter`方法来指定`CustomMapRenderer`类实例将提供的方法，以自定义信息窗口。

`CustomMapRenderer`类实现`GoogleMap.IInfoWindowAdapter`接口[自定义信息窗口](#Customizing_the_Info_Window)。 此接口指定必须实现以下方法：

- `public Android.Views.View GetInfoWindow(Marker marker)` – 此方法调用可返回一个标记的自定义的信息窗口。 如果它返回`null`，则将使用默认的窗口呈现。 如果它返回`View`，然后，`View`将置于信息窗口框架。
- `public Android.Views.View GetInfoContents(Marker marker)` – 此方法调用可返回`View`包含内容的信息窗口中，并且如果将仅调用`GetInfoWindow`方法返回`null`。 如果它返回`null`，则将使用的信息窗口内容的默认呈现。

在示例应用程序，自定义信息的窗口内容，因此`GetInfoWindow`方法返回`null`要启用此功能。

#### <a name="customizing-the-marker"></a>自定义标记

可以通过调用自定义用于表示一个标记的图标`MarkerOptions.SetIcon`方法。 这可以通过重写实现`CreateMarker`方法，每个调用`Pin`，它将添加到映射：

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

此方法创建一个新`MarkerOption`每个实例`Pin`实例。 设置位置、 标签和地址的标记后, 图标将其设置为`SetIcon`方法。 此方法采用`BitmapDescriptor`对象，其中包含所需数据以便呈现图标，`BitmapDescriptorFactory`类，用于提供帮助器方法来简化创建`BitmapDescriptor`。

有关使用`BitmapDescriptorFactory`类以自定义标记，请参阅[自定义标记](~/android/platform/maps-and-location/maps/maps-api.md)。

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>自定义信息窗口

在用户点击该标记上时`GetInfoContents`执行方法，前提`GetInfoWindow`方法返回`null`。 下面的代码示例演示`GetInfoContents`方法：

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

此方法返回`View`包含信息窗口中的内容。 这被实现的如下所示：

- A`LayoutInflater`检索实例。 这用来实例化到其对应的布局 XML 文件`View`。
- `GetCustomPin`调用方法以返回信息窗口的自定义 pin 数据。
- `XamarinMapInfoWindow`布局被放大如果`CustomPin.Id`属性等于`Xamarin`。 否则为`MapInfoWindow`放大布局。 这样可以显示不同的标记的不同的信息窗口布局的位置的方案。
- `InfoWindowTitle`和`InfoWindowSubtitle`资源检索从膨胀布局中，且其`Text`属性设置为相应的数据从`Marker`实例，前提是在资源`null`。
- `View`实例返回要显示在地图上。

> [!NOTE]
> 信息窗口不是实时`View`。 相反，会将转换 Android`View`到静态位图和显示为图像。 这意味着可以响应 click 事件，它无法响应任何触控事件或笔势和信息窗口中的单个控件无法响应其自己的信息窗口时，单击事件。

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>单击信息窗口

当用户在信息窗口上，单击`InfoWindowClick`事件激发，后者反过来执行`OnInfoWindowClick`方法：

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

此方法打开 web 浏览器并导航到存储中的地址`Url`属性检索到`CustomPin`实例`Marker`。 请注意，在创建时已定义地址`CustomPin`PCL 项目中的集合。

有关自定义的详细信息`MapView`实例，请参阅[地图 API](~/android/platform/maps-and-location/maps/maps-api.md)。

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>在通用 Windows 平台上创建自定义呈现器

以下屏幕截图显示地图之前和之后自定义项：

![](customized-pin-images/map-layout-uwp.png "地图控件之前和之后自定义项")

在 UWP 上 pin 称为*图图标*，并且可以自定义映像或系统定义的默认图像。 图图标可以显示`UserControl`，以响应用户点击图图标上显示。 `UserControl`可以显示任何内容，包括`Label`和`Address`属性`Pin`实例。

下面的代码示例演示 UWP 自定义呈现器：

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

`OnElementChanged`方法将执行以下操作，提供自定义呈现器附加到新 Xamarin.Forms 元素：

- 它将清除`MapControl.Children`集合以从映射中，在注册的事件处理程序之前删除任何现有的用户界面元素`MapElementClick`事件。 在用户点击或单击时激发此事件`MapElement`上`MapControl`，并仅在元素呈现器附加到更改时，才从取消订阅。
- 在每个 pin`customPins`集合显示在图上的正确地理位置，如下所示：
  - Pin 的位置创建为`Geopoint`实例。
  - A`MapIcon`创建实例来表示 pin。
  - 用于表示的图像`MapIcon`通过将设置指定`MapIcon.Image`属性。 但是，映射图标的图像是不始终保证会显示，因为它可能被遮盖地图上的其他元素。 因此，图图标的`CollisionBehaviorDesired`属性设置为`MapElementCollisionBehavior.RemainVisible`，以确保它仍显示。
  - 位置`MapIcon`通过将设置指定`MapIcon.Location`属性。
  - `MapIcon.NormalizedAnchorPoint`属性设置为在图像上指针的近似位置。 如果此属性保留其默认值为 (0，0)，它表示图像的左上的角，地图的缩放级别中的更改可能会导致指向不同的位置的映像。
  - `MapIcon`实例添加到`MapControl.MapElements`集合。 这将导致显示在图图标`MapControl`。

> [!NOTE]
> 对于多个映射图标，使用同一个映像时`RandomAccessStreamReference`为了获得最佳性能，应在页面或应用程序级别声明实例。

#### <a name="displaying-the-usercontrol"></a>显示此用户控件

在用户点击映射图标上时`OnMapElementClick`执行方法。 下面的代码示例演示此方法：

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

此方法创建`UserControl`显示有关 pin 的信息的实例。 这被实现的如下所示：

- `MapIcon`检索实例。
- `GetCustomPin`调用方法以返回自定义 pin 数据将显示。
- A`XamarinMapOverlay`创建实例以显示自定义 pin 数据。 此类是一个用户控件。
- 显示从该处的地理位置`XamarinMapOverlay`实例上`MapControl`作为创建`Geopoint`实例。
- `XamarinMapOverlay`实例添加到`MapControl.Children`集合。 此集合包含将在地图显示的 XAML 用户界面元素。
- 地理位置`XamarinMapOverlay`通过调用设置代码图上的实例`SetLocation`方法。
- 上的相对位置`XamarinMapOverlay`通过调用设置实例，对应于指定的位置，`SetNormalizedAnchorPoint`方法。 这可确保，在中的映射结果的缩放级别更改`XamarinMapOverlay`实例始终显示在正确的位置。

或者，如果已经在地图上显示有关 pin 信息，在地图上的点击删除`XamarinMapOverlay`实例从`MapControl.Children`集合。

#### <a name="tapping-on-the-information-button"></a>点击信息按钮

当用户点击*信息*按钮`XamarinMapOverlay`用户控件，`Tapped`事件激发，后者反过来执行`OnInfoButtonTapped`方法：

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

此方法打开 web 浏览器并导航到存储中的地址`Url`属性`CustomPin`实例。 请注意，在创建时已定义地址`CustomPin`PCL 项目中的集合。

有关自定义的详细信息`MapControl`实例，请参阅[地图和位置概述](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx)MSDN 上。

## <a name="summary"></a>总结

这篇文章演示了如何创建自定义呈现器`Map`控件，使开发人员能够重写默认本机呈现与自己特定于平台的自定义项。 Xamarin.Forms.Maps 提供跨平台抽象显示为用户可使用每个平台上的 Api 以提供快速且熟悉映射体验本机地图的地图。


## <a name="related-links"></a>相关链接

- [地图控件](~/xamarin-forms/user-interface/map.md)
- [iOS 地图](~/ios/user-interface/controls/ios-maps/index.md)
- [地图 API](~/android/platform/maps-and-location/maps/maps-api.md)
- [自定义的 Pin （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)
