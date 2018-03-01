---
title: "演练-使用背景位置"
ms.topic: article
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: ba460bee067162f8e42f84f230f93cb1cf98ba98
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="walkthrough---using-background-location"></a>演练-使用背景位置

在此示例中，我们将生成列显有关我们当前的位置信息的位置应用程序的 iOS:、 纬度和屏幕的其他参数。 此应用程序将演示如何正确执行位置更新，该应用程序时活动或 Backgrounded。

本演练说明了 backgrounding 概念，包括作为背景需要应用程序注册的应用程序、 挂起 UI 更新时应用程序 backgrounded，以及使用某些密钥`WillEnterBackground`和`WillEnterForeground``AppDelegate`方法.

## <a name="application-set-up"></a>设置应用程序


1. 首先，创建一个新**iOS > 应用程序 > 单视图应用程序 (C#)**。 调用它_位置_，并确保 iPad 和 iPhone 已选中。

1. 作为背景需要应用程序在 iOS 中限定位置应用程序。 注册应用程序作为位置的应用程序通过编辑**Info.plist**项目文件。

    在解决方案资源管理器下双击**Info.plist**文件来打开它，并滚动到列表的底部。 两个勾选**启用后台模式**和**位置更新**复选框。


    在适用于 Mac 的 Visual Studio，它将类似如下：

    [![](location-walkthrough-images/image7.png "通过启用后台模式和位置更新复选框选中")](location-walkthrough-images/image7.png)

    在 Visual Studio 中， **Info.plist**需要通过添加以下键/值对手动更新：

        ```csharp
        <key>UIBackgroundModes</key>
        <array>
            <string>location</string>
        </array>
        ```

1. 现在，注册该应用程序，它可以从设备中获取位置数据。 在 iOS 中，`CLLocationManager`类可用于访问位置的信息，并可以引发提供位置更新的事件。

1. 在代码中，创建新的类称为`LocationManager`，提供有关各种屏幕和代码以订阅位置更新了一个位置。 在`LocationManager`类中，创建实例`CLLocationManager`调用`LocMgr`:

```csharp
        public class LocationManager
        {
          protected CLLocationManager locMgr;

          public LocationManager (){
            this.locMgr = new CLLocationManager();
            this.locMgr.PausesLocationUpdatesAutomatically = false;

            // iOS 8 has additional permissions requirements
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
              locMgr.RequestAlwaysAuthorization (); // works in background
              //locMgr.RequestWhenInUseAuthorization (); // only in foreground
            }

            if (UIDevice.CurrentDevice.CheckSystemVersion (9, 0)) {
               locMgr.AllowsBackgroundLocationUpdates = true;
            }
          }

          public CLLocationManager LocMgr{
            get { return this.locMgr; }
          }
        }
```

    The code above sets a number of properties and permissions on the [CLLocationManager](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/) class:

    - `PausesLocationUpdatesAutomatically` – 这是一个布尔值，可以根据是否允许系统暂停位置更新设置。 在一些设备上，它默认为`true`，这可以导致设备停止获取大约 15 分钟后的位置更新的背景。
    - `RequestAlwaysAuthorization` -你应将此方法可以向应用程序用户授予允许要在后台中访问的位置的选项传递。 `RequestWhenInUseAuthorization` 此外可以传递如果你想要为用户提供的选项以允许仅当应用程序位于前台时才可访问的位置。
    - `AllowsBackgroundLocationUpdates` – 这是一个布尔值属性，可以设置为允许应用以接收位置更新时挂起的 iOS 9 中引入。

    > [!IMPORTANT]
> **警告**: iOS 8 （和更高版本） 还要求将项记入**Info.plist**文件以向用户授权请求的一部分。

1. 添加的键`NSLocationAlwaysUsageDescription`或`NSLocationWhenInUseUsageDescription`与将中的警报的请求位置数据的访问权限的用户显示的字符串。

1. iOS 9 要求时使用`AllowsBackgroundLocationUpdates` **Info.plist**包含密钥`UIBackgroundModes`值`location`。 如果你已完成本演练的步骤 2，这应已被 Info.plist 文件中。


1. 内部`LocationManager`类中，创建调用的方法`StartLocationUpdates`替换为以下代码。 此代码演示如何以开始接收来自的位置更新`CLLocationManager`:

    ```csharp
        if (CLLocationManager.LocationServicesEnabled) {
          //set the desired accuracy, in meters
          LocMgr.DesiredAccuracy = 1;
          LocMgr.LocationsUpdated += (object sender, CLLocationsUpdatedEventArgs e) =>
          {
              // fire our custom Location Updated event
              LocationUpdated (this, new LocationUpdatedEventArgs (e.Locations [e.Locations.Length - 1]));
          };
          LocMgr.StartUpdatingLocation();
        }
        ```

    There are several important things happening in this method. First, we perform a check to see if the application has access to location data on the device. We verify this by calling `LocationServicesEnabled` on the `CLLocationManager`. This method will return **false** if the user has denied the application access to location information.

1. Next, tell the location manager how often to update. `CLLocationManager` provides many options for filtering and configuring location data, including the frequency of updates. In this example, set the `DesiredAccuracy` to update whenever the location changes by a meter. For more information on configuring location update frequency and other preferences, refer to the [CLLocationManager Class Reference](http://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) in the Apple documentation.

1. Finally, call `StartUpdatingLocation` on the `CLLocationManager` instance. This tells the location manager to get an initial fix on the current location, and to start sending updates

So far, the location manager has been created, configured with the kinds of data we want to receive, and has determined the initial location. Now the code needs to render the location data to the user interface. We can do this with a custom event that takes a `CLLocation` as an argument:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

下一步是为订阅中的位置更新`CLLocationManager`，并引发自定义`LocationUpdated`新位置数据可用，在作为自变量位置中传递时的事件。 若要执行此操作，创建一个新类**LocationUpdateEventArgs.cs**。 此代码是在主应用程序中访问，并引发事件时返回设备位置：

```csharp
public class LocationUpdatedEventArgs : EventArgs
{
    CLLocation location;

    public LocationUpdatedEventArgs(CLLocation location)
    {
       this.location = location;
    }

    public CLLocation Location
    {
       get { return location; }
    }
}
```

## <a name="user-interface"></a>用户界面

1. 使用 iOS 设计器来生成将显示位置信息的屏幕。 双击**Main.storyboard**文件以开始。

    在情节提要，将多个标签拖屏幕以充当占位符的位置信息。 在此示例中，有纬度、 经度、 高度、 过程中，和速度的标签。

    布局应如下所示：

    ![](location-walkthrough-images/image8.png "在 iOS 设计器中的 UI 布局示例")

1. 在解决方案区中，双击`ViewController.cs`文件并编辑它以创建 LocationManager 并调用的新实例`StartLocationUpdates`在其上。
  更改代码以如下所示：

        #region Computed Properties
        public static bool UserInterfaceIdiomIsPhone {
                    get { return UIDevice.CurrentDevice.UserInterfaceIdiom == UIUserInterfaceIdiom.Phone; }
                }

        public static LocationManager Manager { get; set;}
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        // As soon as the app is done launching, begin generating location updates in the location manager
            Manager = new LocationManager();
            Manager.StartLocationUpdates();
        }

        #endregion

    尽管将不显示任何数据，这将启动位置更新应用程序启动。

1. 现在，接收到的位置更新时，请使用位置信息更新屏幕。 以下方法获取从位置我们`LocationUpdated`事件并将其显示在 UI 中：

        #region Public Methods
        public void HandleLocationChanged (object sender, LocationUpdatedEventArgs e)
        {
            // Handle foreground updates
            CLLocation location = e.Location;

            LblAltitude.Text = location.Altitude + " meters";
            LblLongitude.Text = location.Coordinate.Longitude.ToString ();
            LblLatitude.Text = location.Coordinate.Latitude.ToString ();
            LblCourse.Text = location.Course.ToString ();
            LblSpeed.Text = location.Speed.ToString ();

            Console.WriteLine ("foreground updated");
        }

        #endregion


我们仍需要订阅`LocationUpdated`我们 AppDelegate 中，并且调用新方法，以更新 UI 中的事件。 添加下面的代码`ViewDidLoad,`之后立即`StartLocationUpdates`调用：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


现在，当运行应用程序时，其外观应类似如下内容：

[![](location-walkthrough-images/image5.png "运行示例应用程序")](location-walkthrough-images/image5.png)

## <a name="handling-active-and-background-states"></a>处理活动和背景的状态

1. 在前台并处于活动状态时，应用程序输出位置更新。 为了演示在后台，应用程序进入时，会发生什么情况，重写`AppDelegate`状态更改跟踪应用程序的方法，以便它前台和后台之间进行转换时，应用程序将写入控制台：

        public override void DidEnterBackground (UIApplication application)
        {
          Console.WriteLine ("App entering background state.");
        }

        public override void WillEnterForeground (UIApplication application)
        {
          Console.WriteLine ("App will enter foreground");
        }

    添加下面的代码`LocationManager`持续打印更新的位置到应用程序输出中，若要验证的位置信息的数据是在后台中仍然可用：

        public class LocationManager
        {
          public LocationManager ()
          {
            ...
            LocationUpdated += PrintLocation;
          }
          ...

          //This will keep going in the background and the foreground
          public void PrintLocation (object sender, LocationUpdatedEventArgs e) {
            CLLocation location = e.Location;
            Console.WriteLine ("Altitude: " + location.Altitude + " meters");
            Console.WriteLine ("Longitude: " + location.Coordinate.Longitude);
            Console.WriteLine ("Latitude: " + location.Coordinate.Latitude);
            Console.WriteLine ("Course: " + location.Course);
            Console.WriteLine ("Speed: " + location.Speed);
          }
        }

1. 没有用的代码的一个剩余问题： 尝试更新 UI 时 backgrounded 应用程序将可能的原因 iOS 将终止它。 当应用程序将进入后台时，代码将需要取消订阅位置更新并停止更新 UI。

    iOS 为我们提供通知应用程序时有关转换到不同的应用程序状态。 在这种情况下，我们可以订阅`ObserveDidEnterBackground`通知。

    下面的代码段演示如何使用通知，让知道何时暂停 UI 更新的视图。 这将会在`ViewDidLoad`:

        UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
          Manager.LocationUpdated -= HandleLocationChanged;
        });

    当运行应用程序时，输出将如下所示：

    ![](location-walkthrough-images/image6.png "在控制台中的位置输出示例")

1. 应用程序时在前台，操作系统打印到屏幕的位置更新，并继续在后台运行时打印到应用程序输出窗口的数据。

仍然只有一个未完成的问题： 当首次加载应用程序，但它具有无法知道当应用程序已重新进入前台时，屏幕将启动 UI 更新。 如果 backgrounded 应用程序返回置于前台，不会继续更新 UI 的步骤。

若要解决此问题，嵌套的调用来启动另一个通知，应用程序进入活动状态时，会激发内的用户界面更新：

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

现在 UI 将开始更新时首次启动应用程序，并且恢复更新应用程序的任何时间再次置于前台。

在本演练中，我们生成的功能良好、 背景识别 iOS 应用程序将打印到屏幕和应用程序输出窗口的位置数据。


## <a name="related-links"></a>相关链接

- [位置 (第 4) 部分 （示例）](https://developer.xamarin.com/samples/monotouch/Location/)
- [核心位置 Framework 参考](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
