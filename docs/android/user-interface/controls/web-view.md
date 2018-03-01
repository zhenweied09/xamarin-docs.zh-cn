---
title: "Web 视图"
ms.topic: article
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 234bd79754ae7f328d3207757156089441fc588c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="web-view"></a>Web 视图

[`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) 可以创建你自己的窗口用于查看 web 页 （或甚至开发完成浏览器）。 在本教程中，你将创建一个简单[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/) ，可以查看和中导航网页。

创建一个名为的新项目**HelloWebView**。

打开**Resources/Layout/Main.axml**和插入以下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

由于此应用程序将访问 Internet，你必须添加到 Android 适当的权限清单文件。 打开项目的属性，以指定你的应用程序所需操作的权限。 启用`INTERNET`权限如下所示：

![在 Android 清单中设置 INTERNET 权限](web-view-images/01-set-internet-permissions.png)

现在打开**MainActivity.cs**和添加一条 using 指令的易于使用的功能：

```csharp
using Android.Webkit;
```

在顶部`MainActivity`类中，声明[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)对象：

```csharp
WebView web_view;
```

当**WebView**是要求加载 URL 时，它会将默认情况下委托对默认浏览器的请求。 具有**WebView**加载 URL （而不是默认浏览器），则必须子类`Android.Webkit.WebViewClient`，并重写`ShouldOverriderUrlLoading`方法。 此自定义实例`WebViewClient`提供给`WebView`。 若要执行此操作，添加以下嵌套`HelloWebViewClient`类内`MainActivity`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    public override bool ShouldOverrideUrlLoading (WebView view, string url)
    {
        view.LoadUrl(url);
        return false;
    }
}
```

当`ShouldOverrideUrlLoading`返回`false`，它向 Android 发出信号，当前`WebView`实例处理请求，并且不需要任何进一步操作。 

如果你面向的 API 级别 24 或更高版本，请使用重载`ShouldOverrideUrlLoading`采用`IWebResourceRequest`的第二个参数而不是`string`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    // For API level 24 and later
    public override bool ShouldOverrideUrlLoading (WebView view, IWebResourceRequest request)
    {
        view.LoadUrl(request.Url.ToString());
        return false;
    }
}
```

接下来，使用以下代码[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    web_view = FindViewById<WebView> (Resource.Id.webview);
    web_view.Settings.JavaScriptEnabled = true;
    web_view.SetWebViewClient(new HelloWebViewClient());
    web_view.LoadUrl ("https://www.xamarin.com/university");
}
```

此值将初始化成员[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)替换为从[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)布局，并使 JavaScript 的[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)与[`JavaScriptEnabled` ](https://developer.xamarin.com/api/property/Android.Webkit.WebSettings.JavaScriptEnabled/) 
 `= true` (请参阅[调用 C\#从 JavaScript](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript)有关如何调用 C 的信息的原因之一\#从 JavaScript 函数)。 最后，初始网页加载与[ `LoadUrl(String)` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/%2fM%2fLoadUrl)。

生成并运行应用。 你应看到与下面的屏幕截图所示的简单的 web 页面查看器应用程序：

[![显示 WebView 应用示例](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png)

若要处理**回**按钮按键，添加以下 using 语句：

```csharp
using Android.Views;
```

接下来，添加以下方法`HelloWebView`活动：

```csharp
public override bool OnKeyDown (Android.Views.Keycode keyCode, Android.Views.KeyEvent e)
{
    if (keyCode == Keycode.Back && web_view.CanGoBack ())
    {
        web_view.GoBack ();
        return true;
    }
    return base.OnKeyDown (keyCode, e);
}
```

这[ `OnKeyDown(int, KeyEvent)` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnKeyDown/(Android.Views.Keycode%2cAndroid.Views.KeyEvent))当活动运行时按下按钮时，将调用回调方法。 内部使用条件[ `KeyEvent` ](https://developer.xamarin.com/api/type/Android.Views.KeyEvent/)以检查是否按下键是**回**按钮和是否[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)实际能够导航回 （如果它具有历史记录）。 如果都为 true，则[ `GoBack()` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.GoBack/)调用方法时，这将会定位后一个步骤中的[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)历史记录。 返回`true`指示已处理此事件。 如果不满足此条件，然后会将事件发送回系统。

再次运行该应用程序。 你现在应能够跟随链接并在向后导航页历史记录：

[![后退按钮在操作中的示例屏幕快照](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png)


*此页的部分是基于工作创建和共享的 Android Open Source Project 的根据条款中所述修改*
[*Creative Commons 2.5 归属许可证*](http://creativecommons.org/licenses/by/2.5/).


## <a name="related-links"></a>相关链接

- [从 JavaScript 中调用 C#](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript)
- [Android.Webkit.WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView)
- [KeyEvent](https://developer.xamarin.com/api/type/Android.Webkit.WebView/Client)
