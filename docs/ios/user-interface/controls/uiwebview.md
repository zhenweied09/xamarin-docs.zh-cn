---
title: Xamarin.iOS 中的 web 视图
description: 本文档介绍 Xamarin.iOS 应用程序可以显示 web 内容的各种方法。 它讨论 UIWebView、 WKWebView、 SFSafariViewController、 Safari 和应用程序传输安全。
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: f720eae68415ab9efe021e53c9da4875209cd221
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790491"
---
# <a name="web-views-in-xamarinios"></a>Xamarin.iOS 中的 web 视图

IOS 的生存期内 Apple 发布了大量的应用程序开发人员将在其应用中的 web 视图功能的方式。 大多数用户利用内置 Safari web 浏览器在其 iOS 设备上，并因此从其他应用的 web 视图功能是与这种体验一致。 他们期望相同的手势，若要会在 par 和功能相同的性能。

在本文中，我们将探讨每个由 Apple 提供的三个 Web 视图： `UIWebView`， `WKWebview`，和`SFSafariViewController`、 其相似性和差异，和如何使用它们。 

iOS 11 引入了新的更改，同时向`WKWebView`和`SFSafariViewController`。 有关详细信息，请参阅[Web iOS 11 指南中的更改](~/ios/platform/introduction-to-ios11/web.md)指南。

## <a name="uiwebview"></a>UIWebView

`UIWebView` 是 Apple 的旧方式提供 web 内容在你的应用程序中。 它发布在 iOS 2.0 中，并从 8.0 开始已弃用。

如果你打算支持 iOS 版本早于 8.0 时，你将需要使用`UIWebView`。 因为这样一个事实：`UIWebView`小于针对进行优化性能比其他方案简便，建议你应检查用户的 iOS 版本。 如果它 8.0 或更高版本，使用任一选项在下面加以说明将创建更好的用户体验。
 
若要将 UIWebView 添加到你的 Xamarin.iOS 应用程序，请使用下面的代码：
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

这将产生以下 web 视图：

[![](uiwebview-images/webview.png "ScalesPagesToFit 的效果")](uiwebview-images/webview.png#lightbox)

有关详细信息使用`UIWebView`，请参阅以下配方：


- [加载 Web 页](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_a_web_page/)
- [加载本地内容](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_local_content/)
- [加载非 Web 文档](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_non-web_documents/)

## <a name="wkwebview"></a>WKWebView

`WKWebView` iOS 8 允许应用程序开发人员来实现一个 web 浏览接口类似于移动 Safari 中引入了。 这是因为，在情况下，这一事实，`WKWebView`使用 Nitro Javascript 引擎，由移动 Safari 使用的相同引擎。 `WKWebView` 应始终使用的转移 UIWebView 已由于[更高的性能](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview)，内置用户友好手势和 web 页面，你的应用程序之间的交互的易用性。
  
`WKWebView` 可以添加到你的应用方式几乎与 UIWebView，但是你作为开发人员可以通过 UI/UX 和功能的更多的控制。 创建并显示 web 视图对象将显示所请求的页面，但你可以控制视图中的显示方式、 如何在用户可以导航，以及如何在用户退出视图。  

下面的代码可以用于启动`WKWebView`Xamarin.iOS 应用程序中：

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

这将产生以下 web 视图：

[![](uiwebview-images/wkwebview.png "示例 web 视图不 ScalesPagesToFit")](uiwebview-images/wkwebview.png#lightbox)

务必请注意，`WKWebView`是易于使用的功能的命名空间，因此将需要添加以下 using 指令到你的类的顶部。

`WKWebView` 此外可以在 Xamarin.Mac 应用中使用，并且因此可能需要考虑使用它，如果要创建跨平台 Mac/iOS 应用程序。

[处理 JavaScript 警报](https://developer.xamarin.com/recipes/ios/content_controls/web_view/handle_javascript_alerts/)配方还介绍了在与 Javascript 一起使用 WKWebView

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController` 是提供从你的应用程序的 web 内容的最新方法，可在 iOS 9 及更高版本。 与不同`UIWebView`或`WKWebView`，`SFSafariViewController`是视图控制器，因此不能使用与其他视图。 你应呈现`SFSafariViewController`为新视图控制器，在相同的方式提供任何视图控制器。
 
 `SFSafariViewController` 是实质上是微型 safari 可嵌入到你的应用。 如 WKWebView 它使用相同的 Nitro Javascript 引擎，但还提供了一系列其他 Safari 功能，如自动填充、 读取器，和与移动 Safari 共享 cookie 和数据的能力。 用户之间的交互和`SFSafariViewController`不能访问你的应用程序。 你的应用程序不会对任何默认的 Safari 功能的访问。
 
它还，默认情况下，实现**完成**按钮，允许以用户轻松地返回到你的应用，并前进和后退导航按钮，允许你的用户可以浏览网页的堆栈。 此外，它还提供用户与在地址栏，从而在预期的网页上的高枕无忧。 地址栏中不允许用户更改 url。 

这些实现不能更改，因此`SFSafariViewController`是理想之选要用作默认浏览器，如果你的应用程序想要提供一个没有任何自定义项中的网页。

下面的代码可以用于启动`SFSafariViewController`Xamarin.iOS 应用程序中：

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

这将产生以下 web 视图：

[![](uiwebview-images/sfsafariviewcontroller.png "与 SFSafariViewController 示例 web 视图")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

它还可打开移动 Safari 中的应用程序在你的应用，通过使用下面的代码：

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

这将产生以下 web 视图：

[![](uiwebview-images/safari.png "在 Safari 中显示的网页")](uiwebview-images/safari.png#lightbox)

导航离开 Safari 你的应用的用户通常应当始终避免。 大多数用户将不期望导航外部应用程序，因此如果导航离开你的应用程序时，用户可能永远不会返回它，实质上终止用户参与策略。

iOS 9 改进允许用户轻松地返回到你的应用程序通过提供在 Safari 页的左上角的后退按钮。

## <a name="app-transport-security"></a>应用程序传输安全

应用程序传输安全，或*ATS*引入 apple 在 iOS 9，以确保所有 internet 通信都符合以确保连接安全最佳实践中。

有关 ATS 的详细信息，包括如何在您的应用程序中实现它指[应用传输安全](~/ios/app-fundamentals/ats.md)指南。

## <a name="related-links"></a>相关链接

- [WebViews （示例）](https://developer.xamarin.com/samples/monotouch/WebView/)
