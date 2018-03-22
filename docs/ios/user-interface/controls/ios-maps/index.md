---
title: "映射"
description: "iOS 包括 MapKit 框架，可以轻松地添加将映射到应用程序。 使用地图工具包，则可以将 iOS 应用程序添加支持功能，如平移和缩放、 在地图上显示用户的位置和分层丰富图形的交互式地图。 本文深入的几项映射工具包的功能，显示如何充分利用它们来构建到应用程序的地理功能"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 3fecf17a4f70e44ca169c825bf0dd34a5127cec8
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="maps"></a>映射

_iOS 包括 MapKit 框架，可以轻松地添加将映射到应用程序。使用地图工具包，则可以将 iOS 应用程序添加支持功能，如平移和缩放、 在地图上显示用户的位置和分层丰富图形的交互式地图。本文深入的几项映射工具包的功能，显示如何充分利用它们来构建到应用程序的地理功能_

地图是所有现代移动操作系统中的共同功能。 iOS 提供映射支持本机通过映射工具包框架。 使用地图工具包应用程序可以轻松地添加丰富、 交互式的地图。 在有许多种情况下，例如添加批注，以将标记上一个代码图，位置和覆盖的任意形状的图形中，可以自定义这些映射。 Map 工具包甚至具有用于显示设备的当前位置的内置支持。

## <a name="adding-a-map"></a>添加地图

将地图添加到应用程序可通过添加完成`MKMapView`实例查看层次结构，如下所示：

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

 `MKMapView` 是`UIView`显示的映射的子类。 只需添加地图使用上面的代码产生的交互式映射：

 ![](images/00-map.png "示例映射")

## <a name="map-style"></a>样式所映射

 `MKMapView` 支持的映射 3 不同的样式。 若要应用地图样式，只需设置`MapType`属性的值从`MKMapType`枚举：
 ```
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
 ```
  下面的屏幕截图显示可用的不同的映射样式：

 ![](images/01-mapstyles.png "此屏幕截图中显示可用的不同的映射样式")

## <a name="panning-and-zooming"></a>平移和缩放

 `MKMapView` 包括映射交互功能的支持，如所示：

-  通过捏合手势缩放
-  平移通过平移笔势


这些功能可以启用或禁用通过只需设置`ZoomEnabled`和`ScrollEnabled`属性`MKMapView`实例，默认值为 true。 例如，若要显示的静态映射，只需设置适当的属性为 false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>用户位置

除了用户交互，`MKMapView`还具有内置支持显示设备的位置。 之所以如此使用*核心位置*framework。 你可以访问用户的位置之前，必须提示用户。 若要执行此操作，创建的实例`CLLocationManager`并调用`RequestWhenInUseAuthorization`。

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

请注意，在 iOS 8.0，尝试进行调用之前的版本`RequestWhenInUseAuthorization`将导致错误。 请确保在该调用，如果你想要支持 8 之前的版本之前检查的 iOS 版本。

访问用户的位置还需要修改**Info.plist**。 应设置如下与位置数据相关的键：

- **NSLocationWhenInUseUsageDescription** - 用于用户在和你的应用互动时访问用户的位置。
- **NSLocationAlwaysUsageDescription** - 用于应用在后台访问用户的位置时。

你还可以通过打开添加这些键**Info.plist**并选择*源*底部的编辑器中。

一旦你已更新**Info.plist**并提示用户输入其位置的访问权，你可以在图上显示用户的位置，通过设置`ShowsUserLocation`属性为 true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "允许位置访问警报")
 
## <a name="annotations"></a>批注

 `MKMapView` 此外支持显示图像，称为批注，在地图上。 这些可以是自定义映像或系统定义的各种颜色的 pin。 例如，下面的屏幕截图显示具有这两个 pin 的映射和自定义映像：

 ![](images/03-annotations.png "此屏幕截图显示了具有两个 pin 的映射和自定义映像")

### <a name="adding-an-annotation"></a>添加批注

批注本身由两部分组成：

-  `MKAnnotation`对象，其中包括有关批注，如标题和注释的位置的模型数据。
-  `MKAnnotationView` ，其中包含显示，或者显示了当用户点击批注标注的映像。


Map 工具包使用 iOS 委派模式将批注添加到一个代码图，其中`Delegate`属性`MKMapView`设置的实例为`MKMapViewDelegate`。 它是负责返回的此委托实现`MKAnnotationView`批注。

若要添加批注，首先添加批注，是通过调用`AddAnnotations`上`MKMapView`实例：

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

当批注的位置将成为在地图上可见`MKMapView`将调用其委托`GetViewForAnnotation`方法以获取`MKAnnotationView`以显示。

例如，以下代码将返回系统提供`MKPinAnnotationView`:

```csharp
string pId = "PinAnnotation";

public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
{
    if (annotation is MKUserLocation)
        return null;

    // create pin annotation view
    MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);

    if (pinView == null)
        pinView = new MKPinAnnotationView (annotation, pId);

    ((MKPinAnnotationView)pinView).PinColor = MKPinAnnotationColor.Red;
    pinView.CanShowCallout = true;

    return pinView;
}
```

### <a name="reusing-annotations"></a>重复使用批注

以节省内存，`MKMapView`允许批注视图的可存入池中以备重用，类似于表格单元格会重复使用的方式。 从池中获取的批注视图通过调用`DequeueReusableAnnotation`:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>显示标注

如前所述，批注可以根据需要显示标注。 若要只显示标注将`CanShowCallout`为 true 的`MKAnnotationView`。 这会导致批注的标题时，不显示点击批注，如所示：

 ![](images/04-callout.png "正在显示的批注标题")

### <a name="customizing-the-callout"></a>自定义标注

标注也可以将自定义以显示向左和右附件视图，如下所示：

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

此代码将导致以下的标注：

 ![](images/05-callout-accessories.png "示例标注")

若要处理用户点击右附件，只需实现`CalloutAccessoryControlTapped`中的方法`MKMapViewDelegate`:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>叠加

在地图上的层图形到另一种方法使用叠加。 叠加支持它被放大随着与地图的绘制图形内容。 iOS 提供支持几种类型的叠加，包括：

-  多边形-通常用于突出显示在地图上的某些区域。
-  折线-往往时显示一个路由。
-  圆形-用来突出显示的地图的圆形区域。


此外，可以创建自定义叠加以显示与精细、 自定义绘图代码的任意几何图形。 例如，天气雷达图将自定义的覆盖区上的良好候选。

#### <a name="adding-an-overlay"></a>添加一个覆盖区

类似于批注，添加一个覆盖区涉及 2 部分：

-  创建一个用于在覆盖区上的模型对象和将其添加到`MKMapView`。
-  创建视图中为覆盖`MKMapViewDelegate`。


在覆盖区上的模型可以是任何`MKShape`子类。 Xamarin.iOS 包括`MKShape`子类的多边形、 折线和圆形，通过`MKPolygon`，`MKPolyline`和`MKCircle`分别类。

例如，下面的代码用于添加`MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

覆盖区上的视图是`MKOverlayView`实例返回`GetViewForOverlay`中`MKMapViewDelegate`。 每个`MKShape`都有一个相应`MKOverlayView`知道如何显示给定的形状中。 有关`MKPolygon`没有`MKPolygonView`。 同样，`MKPolyline`对应于`MKPolylineView`，和`MKCircle`没有`MKCircleView`。

例如，下面的代码返回`MKCircleView`为`MKCircle`:

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

这在地图上显示一个圆形，如所示：

 ![](images/06-circle-overlay.png "在地图上显示一个圆圈")

## <a name="local-search"></a>本地搜索

iOS 包括本地搜索 API 使用映射工具包，可用于指定地理区域中的关注点的异步搜索。

若要执行本地搜索，应用程序必须执行以下步骤：

1.  创建`MKLocalSearchRequest`对象。
1.  创建`MKLocalSearch`对象`MKLocalSearchRequest`。
1.  调用`Start`方法`MKLocalSearch`对象。
1.  检索`MKLocalSearchResponse`回调的对象。


本地搜索 API 本身不提供用户界面。 它甚至不需要一个映射，以便使用。 但是，若要实际使用的本地搜索，应用程序需要提供一些方式来指定搜索查询并显示结果。 此外，由于结果将包含位置数据，因此它通常会导致意义上，可以在地图上显示它们。

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>添加本地搜索 UI

一种方法接受搜索输入是与`UISearchBar`，其中提供`UISearchController`并将表中显示结果。

下面的代码添加`UISearchController`（具有搜索栏属性） 中`ViewDidLoad`方法`MapViewController`:

```csharp
//Creates an instance of a custom View Controller that holds the results
var searchResultsController = new SearchResultsViewController (map);

//Creates a search controller updater
var searchUpdater = new SearchResultsUpdator ();
searchUpdater.UpdateSearchResults += searchResultsController.Search;
            
//add the search controller
searchController = new UISearchController (searchResultsController) {
                SearchResultsUpdater = searchUpdater
            };

//format the search bar
searchController.SearchBar.SizeToFit ();
searchController.SearchBar.SearchBarStyle = UISearchBarStyle.Minimal;
searchController.SearchBar.Placeholder = "Enter a search query";

//the search bar is contained in the navigation bar, so it should be visible
searchController.HidesNavigationBarDuringPresentation = false;

//Ensure the searchResultsController is presented in the current View Controller 
DefinesPresentationContext = true;

//Set the search bar in the navigation bar
NavigationItem.TitleView = searchController.SearchBar;

```csharp
Note that you are responsible for incorporating the search bar object into the user interface. In this example, we assigned it to the TitleView of the navigation bar, but if you do not use a navigation controller in your application you will have to find another place to display it.

In this code snippet, we created another custom view controller – `searchResultsController` –  that displays the search results and then we used this object to create our search controller object. We also created a new search updater, which becomes active when the user interacts with the search bar. It receives notifications about searches with each keystroke and is responsible for updating the UI.
We will take a look at how to implement both the `searchResultsController` and the `searchResultsUpdater` later in this guide.

This results in a search bar displayed over the map as shown below:

 ![](images/07-searchbar.png "A search bar displayed over the map")
 


### Displaying the Search Results

To display search results, we need to create a custom View Controller; normally a `UITableViewController`. As shown above, the `searchResultsController` is passed to the constructor of the `searchController` when it is being created.
The following code is an example of how to create this custom View Controller:

```csharp
public class SearchResultsViewController : UITableViewController
{
    static readonly string mapItemCellId = "mapItemCellId";
    MKMapView map;

    public List<MKMapItem> MapItems { get; set; }

    public SearchResultsViewController (MKMapView map)
    {
        this.map = map;

        MapItems = new List<MKMapItem> ();
    }

    public override nint RowsInSection (UITableView tableView, nint section)
    {
        return MapItems.Count;
    }

    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        var cell = tableView.DequeueReusableCell (mapItemCellId);

        if (cell == null)
            cell = new UITableViewCell ();

        cell.TextLabel.Text = MapItems [indexPath.Row].Name;
        return cell;
    }

    public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
    {
        // add item to map
        CLLocationCoordinate2D coord = MapItems [indexPath.Row].Placemark.Location.Coordinate;
        map.AddAnnotations (new MKPointAnnotation () {
            Title = MapItems [indexPath.Row].Name,
            Coordinate = coord
        });

        map.SetCenterCoordinate (coord, true);

        DismissViewController (false, null);
    }

    public void Search (string forSearchString)
    {
        // create search request
        var searchRequest = new MKLocalSearchRequest ();
        searchRequest.NaturalLanguageQuery = forSearchString;
        searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

        // perform search
        var localSearch = new MKLocalSearch (searchRequest);

        localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
            if (response != null && error == null) {
                this.MapItems = response.MapItems.ToList ();
                this.TableView.ReloadData ();
            } else {
                Console.WriteLine ("local search error: {0}", error);
            }
        });


    }
}
```

### <a name="updating-the-search-results"></a>更新搜索结果

`SearchResultsUpdater`充当之间中介`searchController`的搜索栏和搜索结果。 

在此示例中我们必须首先创建中的搜索方法`SearchResultsViewController`。 若要这样做我们必须创建`MKLocalSearch`对象，并使用它来颁发搜索`MKLocalSearchRequest`，传递给回调中检索结果`Start`方法`MKLocalSearch`对象。 然后返回结果的顺序`MKLocalSearchResponse`对象，其中包含的数组`MKMapItem`对象：

```csharp
public void Search (string forSearchString)
{
    // create search request
    var searchRequest = new MKLocalSearchRequest ();
    searchRequest.NaturalLanguageQuery = forSearchString;
    searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

    // perform search
    var localSearch = new MKLocalSearch (searchRequest);

    localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
        if (response != null && error == null) {
            this.MapItems = response.MapItems.ToList ();
            this.TableView.ReloadData ();
        } else {
            Console.WriteLine ("local search error: {0}", error);
        }
    });


}
```

然后，在我们`MapViewController`我们将创建的自定义实现`UISearchResultsUpdating`，分配给`SearchResultsUpdater`属性我们`searchController`中[添加本地搜索 UI](#Adding_a_Local_Search_UI)部分：

```csharp
public class SearchResultsUpdator : UISearchResultsUpdating
{
    public event Action<string> UpdateSearchResults = delegate {};

    public override void UpdateSearchResultsForSearchController (UISearchController searchController)
    {
        this.UpdateSearchResults (searchController.SearchBar.Text);
    }
}
```

上述实现批注向地图中添加时从结果中，选择项如下所示：

 ![](images/08-search-results.png "从结果选择项目时添加到映射批注")
 
> [!IMPORTANT]
> `UISearchController` 已在 iOS 8 中实现。 如果你想要支持设备早于此操作，则将需要使用`UISearchDisplayController`。



## <a name="summary"></a>总结

检查这篇文章*映射**工具包*框架，用于 iOS。 它首先，按照如何看起来`MKMapView`类允许交互式的地图，要包括在应用程序。 然后，它演示了如何使用批注和覆盖层的地图中进一步自定义。 最后，它会检查本地搜索功能已添加到映射工具包 ios 6.1，用于演示如何使用基于位置查询进行的关注点，并将其添加到图。

## <a name="related-links"></a>相关链接

- [SearchController](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/)
- [MapDemo （示例）](https://developer.xamarin.com/samples/monotouch/MapDemo)
