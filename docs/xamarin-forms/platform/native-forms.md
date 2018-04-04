---
title: 本机窗体
description: 本机窗体允许 Xamarin.Forms 内容页派生页可供本机 Xamarin.iOS 和 Xamarin.Android，通用 Windows 平台 (UWP) 项目。 本机项目可以使用直接添加到项目中，或从可移植类库 (PCL)、.NET 标准库时或共享项目的内容页派生的页。 此文章介绍了如何使用直接添加到本机项目的内容页派生页以及如何在它们之间导航。
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/11/2018
ms.openlocfilehash: 9d0d4e69228ce8a1d9944833bff80b22ea5f9ddd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="native-forms"></a>本机窗体

_本机窗体允许 Xamarin.Forms 内容页派生页可供本机 Xamarin.iOS 和 Xamarin.Android，通用 Windows 平台 (UWP) 项目。本机项目可以使用直接添加到项目中，或从可移植类库 (PCL)、.NET 标准库时或共享项目的内容页派生的页。此文章介绍了如何使用直接添加到本机项目的内容页派生页以及如何在它们之间导航。_

通常，Xamarin.Forms 应用程序包括一个或多个派生自的页[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)，并将这些页面 PCL、.NET 标准库时或共享项目中共享的所有平台。 但是，本机窗体允许`ContentPage`-派生页添加直接到本机 Xamarin.iOS 和 Xamarin.Android，UWP 应用程序。 相比具有使用本机项目`ContentPage`-派生的页从 PCL、.NET 标准库时或共享项目中，直接向本机项目添加页面的优点是页面可以用本机视图进行扩展。 然后可以在 XAML 中使用名为本机视图`x:Name`和引用从代码隐藏。 有关本机视图的详细信息，请参阅[本机视图](~/xamarin-forms/platform/native-views/index.md)。

使用 Xamarin.Forms 的过程[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生中页的本机项目就是，如下所示：

1. 对本机项目中添加的 Xamarin.Forms NuGet 包。
1. 添加[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生页上，以及任何依赖项，对本机项目。
1. 调用 `Forms.Init` 方法。
1. 构造的实例[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生页，并将其转换为适当的本机类型使用以下的扩展方法之一：`CreateViewController`对于 iOS，`CreateFragment`或`CreateSupportFragment`对于 Android，或`CreateFrameworkElement`适用于 UWP。
1. 导航到的本机类型表示形式[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生页面使用本机导航 API。

必须通过调用初始化 Xamarin.Forms`Forms.Init`方法之前本机项目可以构造[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生页。 选择何时执行此操作主要取决于你的应用程序流中最方便时 – 它无法在应用程序启动时，或之前执行`ContentPage`-构造派生的页面。 在本文中和随附的示例应用程序，`Forms.Init`方法调用在应用程序启动。

> [!NOTE]
> **NativeForms**示例应用程序解决方案不包含任何 Xamarin.Forms 项目。 相反，它包含的 Xamarin.iOS 项目、 Xamarin.Android 项目中和 UWP 项目。 每个项目是使用本机窗体使用的本机项目[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生页。 但是，没有的理由为何无法使用本机项目`ContentPage`-派生自 PCL、.NET 标准库时或共享项目的页。

当使用本机窗体，Xamarin.Forms 功能如[ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/)， [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)，和数据绑定引擎，所有仍工作。

## <a name="ios"></a>iOS

在 iOS 上`FinishedLaunching`中重写`AppDelegate`类中，通常可以执行应用程序启动相关的任务。 它称为应用程序已启动，并通常重写以配置主窗口，并查看控制器之后。 下面的代码示例演示`AppDelegate`示例应用程序中的类：

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;

    UIWindow _window;
    UINavigationController _navigation;

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        var mainPage = new PhonewordPage().CreateViewController();
        mainPage.Title = "Phoneword";

        _navigation = new UINavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    ...
}
```

`FinishedLaunching`方法执行以下任务：

- 通过调用初始化 Xamarin.Forms`Forms.Init`方法。
- 对引用`AppDelegate`类存储在`static``Instance`字段。 这是为了提供其他类来调用方法中定义的机制`AppDelegate`类。
- `UIWindow`，这主容器中本机 iOS 应用程序，视图的创建。
- `PhonewordPage`类，方法是 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生在 XAML 中定义的页，进行构造并转换为`UIViewController`使用`CreateViewController`扩展方法。
- `Title`属性`UIViewController`设置，这将显示在`UINavigationBar`。
- A`UINavigationController`创建用于管理层次结构导航。 `UINavigationController`类所管理的视图控制器，堆栈与`UIViewController`传递到构造函数将显示最初时`UINavigationController`加载。
- `UINavigationController`实例设置为顶级`UIViewController`为`UIWindow`，和`UIWindow`被设置为应用程序密钥的窗口和可见。

一次`FinishedLaunching`方法已执行，Xamarin.Forms 中定义的 UI`PhonewordPage`将显示类，如下面的屏幕截图中所示：

[![](native-forms-images/ios-phonewordpage.png "iOS PhonewordPage")](native-forms-images/ios-phonewordpage-large.png#lightbox "iOS PhonewordPage")

用户界面时，例如通过点击交互[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)，将导致在中的事件处理程序`PhonewordPage`隐藏代码执行。 例如，当用户点击**调用历史记录**执行按钮，以下事件处理程序：

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToCallHistoryPage();
}
```

`static` `AppDelegate.Instance`字段允许`AppDelegate.NavigateToCallHistoryPage`要调用方法，下面的代码示例中所示：

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateViewController();
    callHistoryPage.Title = "Call History";
    _navigation.PushViewController(callHistoryPage, true);
}
```

`NavigateToCallHistoryPage`方法将转换 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生页`UIViewController`与`CreateViewController`扩展方法，并设置`Title`属性`UIViewController`。 `UIViewController`然后被推送到`UINavigationController`通过`PushViewController`方法。 因此，Xamarin.Forms 中定义的 UI`CallHistoryPage`将显示类，如下面的屏幕截图中所示：

[![](native-forms-images/ios-callhistorypage.png "iOS CallHistoryPage")](native-forms-images/ios-callhistorypage-large.png#lightbox "iOS CallHistoryPage")

时`CallHistoryPage`点击背面的显示会弹出箭头`UIViewController`为`CallHistoryPage`类`UINavigationController`，返回到用户`UIViewController`为`PhonewordPage`类。

## <a name="android"></a>Android

在 Android 上，`OnCreate`中重写`MainActivity`类中，通常可以执行应用程序启动相关的任务。 下面的代码示例演示`MainActivity`示例应用程序中的类：

```csharp
public class MainActivity : AppCompatActivity
{
    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Phoneword";

        var mainPage = new PhonewordPage().CreateFragment(this);
        FragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

`OnCreate`方法执行以下任务：

- 通过调用初始化 Xamarin.Forms`Forms.Init`方法。
- 对引用`MainActivity`类存储在`static``Instance`字段。 这是为了提供其他类来调用方法中定义的机制`MainActivity`类。
- `Activity`从布局资源将内容设置。 在示例应用程序，布局包括`LinearLayout`包含`Toolbar`，和一个`FrameLayout`充当片段容器。
- `Toolbar`检索并将其设置为操作栏`Activity`，并设置操作栏标题。
- `PhonewordPage`类，方法是 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生在 XAML 中定义的页，进行构造并转换为`Fragment`使用`CreateFragment`扩展方法。
- `FragmentManager`类创建并提交事务，用于替换`FrameLayout`实例与`Fragment`为`PhonewordPage`类。

有关片段的详细信息，请参阅[片段](~/android/platform/fragments/index.md)。

> [!NOTE]
> 除了`CreateFragment`扩展方法，Xamarin.Forms 还包括`CreateSupportFragment`方法。 `CreateFragment`方法创建`Android.App.Fragment`，可以使用的应用程序面向 API 11 和更高版本。 `CreateSupportFragment`方法创建`Android.Support.V4.App.Fragment`，可以使用面向 11 之前的 API 版本的应用程序中。

一次`OnCreate`方法已执行，Xamarin.Forms 中定义的 UI`PhonewordPage`将显示类，如下面的屏幕截图中所示：

[![](native-forms-images/android-phonewordpage.png "Android PhonewordPage")](native-forms-images/android-phonewordpage-large.png#lightbox "Android PhonewordPage")

用户界面时，例如通过点击交互[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)，将导致在中的事件处理程序`PhonewordPage`隐藏代码执行。 例如，当用户点击**调用历史记录**执行按钮，以下事件处理程序：

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToCallHistoryPage();
}
```

`static` `MainActivity.Instance`字段允许`MainActivity.NavigateToCallHistoryPage`要调用方法，下面的代码示例中所示：

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateFragment(this);
    FragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, callHistoryPage)
        .Commit();
}
```

`NavigateToCallHistoryPage`方法将转换 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生页`Fragment`与`CreateFragment`扩展方法，并将添加`Fragment`到片段后退堆栈。 因此，Xamarin.Forms 中定义的 UI`CallHistoryPage`将显示，如下面的屏幕截图中所示：

[![](native-forms-images/android-callhistorypage.png "Android CallHistoryPage")](native-forms-images/android-callhistorypage-large.png#lightbox "Android CallHistoryPage")

当`CallHistoryPage`点击背面的显示弹出箭头将`Fragment`为`CallHistoryPage`从片段后退堆栈，返回到用户`Fragment`为`PhonewordPage`类。

### <a name="enabling-back-navigation-support"></a>启用后退导航支持

`FragmentManager`类具有`BackStackChanged`片段后堆栈的内容更改时，将引发的事件。 `OnCreate`中的方法`MainActivity`类包含对于此事件匿名事件处理程序：

```csharp
FragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = FragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Call History" : "Phoneword";
};
```

此事件处理程序的操作栏上显示后退按钮，前提是没有一个或多个`Fragment`实例在片段上的后退堆栈。 点击后退按钮的响应由`OnOptionsItemSelected`重写：

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && FragmentManager.BackStackEntryCount > 0)
    {
        FragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

`OnOptionsItemSelected`每当选择在选项菜单项就会调用替代。 此实现弹出的当前片段中的片段后堆栈，前提是已选择后退按钮，并且有一个或多个`Fragment`实例在片段上的后退堆栈。

### <a name="multiple-activities"></a>多个活动

当应用程序由多个活动， [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生的页可以嵌入到每个活动。 在此方案中，`Forms.Init`需要仅在调用方法`OnCreate`的第一个重写`Activity`嵌入 Xamarin.Forms `ContentPage`。 但是，这有以下影响：

- 值`Xamarin.Forms.Color.Accent`来自`Activity`调用`Forms.Init`方法。
- 值`Xamarin.Forms.Application.Current`将与关联`Activity`调用`Forms.Init`方法。

### <a name="choosing-a-file"></a>选择一个文件

嵌入时[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生使用的页[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) ，需要到支持的 HTML"选择文件"按钮，则`Activity`将需要重写`OnActivityResult`方法：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

在 UWP，本机`App`类中，通常可以执行应用程序启动相关的任务。 Xamarin.Forms 通常在初始化时，在 Xamarin.Forms UWP 应用中，在`OnLaunched`重写以本机`App`类中，以传递`LaunchActivatedEventArgs`参数`Forms.Init`方法。 因此，本机 UWP 应用程序使用 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生的页面可以最轻松地在调用`Forms.Init`方法从`App.OnLaunched`方法。

默认情况下，本机`App`类将启动`MainPage`作为第一页的应用程序的类。 下面的代码示例演示`MainPage`示例应用程序中的类：

```csharp
public sealed partial class MainPage : Page
{
    public static MainPage Instance;

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        this.Content = new Phoneword.UWP.Views.PhonewordPage().CreateFrameworkElement();
    }
    ...
}
```

`MainPage`构造函数将执行以下任务：

- 页上，为启用了缓存，以便新`MainPage`不构造当某个用户导航回页。
- 对引用`MainPage`类存储在`static``Instance`字段。 这是为了提供其他类来调用方法中定义的机制`MainPage`类。
- `PhonewordPage`类，方法是 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生在 XAML 中定义的页，进行构造并转换为`FrameworkElement`使用`CreateFrameworkElement`扩展方法，然后将其设置为的内容`MainPage`类。

一次`MainPage`构造函数已执行，Xamarin.Forms 中定义的 UI`PhonewordPage`将显示类，如下面的屏幕截图中所示：

[![](native-forms-images/uwp-phonewordpage.png "UWP PhonewordPage")](native-forms-images/uwp-phonewordpage-large.png#lightbox "UWP PhonewordPage")

用户界面时，例如通过点击交互[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)，将导致在中的事件处理程序`PhonewordPage`隐藏代码执行。 例如，当用户点击**调用历史记录**执行按钮，以下事件处理程序：

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    Phoneword.UWP.MainPage.Instance.NavigateToCallHistoryPage();
}
```

`static` `MainPage.Instance`字段允许`MainPage.NavigateToCallHistoryPage`要调用方法，下面的代码示例中所示：

```csharp
public void NavigateToCallHistoryPage()
{
    this.Frame.Navigate(new CallHistoryPage());
}
```

使用通常执行 UWP 中的导航`Frame.Navigate`方法，后者采用`Page`自变量。 Xamarin.Forms 定义`Frame.Navigate`采用的扩展方法[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生页实例。 因此，当`NavigateToCallHistoryPage`执行方法时，Xamarin.Forms 中定义 UI`CallHistoryPage`将显示，如下面的屏幕截图中所示：

[![](native-forms-images/uwp-callhistorypage.png "UWP CallHistoryPage")](native-forms-images/uwp-callhistorypage-large.png#lightbox "UWP CallHistoryPage")

当`CallHistoryPage`点击背面的显示会弹出箭头`FrameworkElement`为`CallHistoryPage`从应用内后退堆栈，返回到用户`FrameworkElement`为`PhonewordPage`类。

### <a name="enabling-back-navigation-support"></a>启用后退导航支持

在 UWP，应用程序必须跨不同设备的外形因素启用所有硬件和软件后退按钮，后退导航。 这可以通过注册的事件处理程序来实现`BackRequested`事件，可以在执行`OnLaunched`方法的本机`App`类：

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;

    if (rootFrame == null)
    {
        ...      
        // Place the frame in the current Window
        Window.Current.Content = rootFrame;

        SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
    }
    ...
}
```

当启动应用程序时，`GetForCurrentView`方法检索`SystemNavigationManager`对象与当前视图中，关联，然后注册事件处理程序`BackRequested`事件。 如果它是前景应用程序，并在响应中，调用后，应用程序将仅收到此事件`OnBackRequested`事件处理程序：

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
    }
}
```

`OnBackRequested`事件处理程序调用`GoBack`方法上的应用程序和集的根帧`BackRequestedEventArgs.Handled`属性`true`将该事件标记为已处理。 将该事件标记为已处理的失败可能导致系统离开 （上的移动设备系列） 的应用程序或忽略 （上的桌面版设备系列） 的事件。

应用程序依赖于在手机上，提供系统后退按钮，但可以选择是否要在桌面版设备上的标题栏上显示后退按钮。 这可通过设置`AppViewBackButtonVisibility`属性之一`AppViewBackButtonVisibility`枚举值：

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

`OnNavigated`在响应中执行的事件处理程序`Navigated`页面导航发生时，如果事件激发，更新的标题栏后退按钮的可见性。 这可确保标题栏后退按钮时才应用内后退堆栈不为空，可见，或删除从标题栏中，应用内后退堆栈是否为空。

在 UWP 上后退导航支持的详细信息，请参阅[导航历史记录和向后导航适用于 UWP 应用](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/)。

## <a name="summary"></a>总结

本机窗体允许 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生页将使用的本机 Xamarin.iOS 和 Xamarin.Android，通用 Windows 平台 (UWP) 项目。 可以使用本机项目`ContentPage`-派生直接添加到项目中，或从 PCL、.NET 标准库时或共享项目的页面。 本文介绍了如何使用`ContentPage`-派生直接添加到本机项目，以及如何在它们之间导航的页。


## <a name="related-links"></a>相关链接

- [NativeForms （示例）](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)
- [本机视图](~/xamarin-forms/platform/native-views/index.md)
