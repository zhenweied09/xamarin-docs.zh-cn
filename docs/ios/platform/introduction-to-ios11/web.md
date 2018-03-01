---
title: "在 iOS 11 中的 web 更改"
ms.topic: article
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/12/2016
ms.openlocfilehash: 224a64b39f9761ca520117a23dfeb7ee08cae719
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="web-changes-in-ios-11"></a>在 iOS 11 中的 web 更改

iOS 11 引入了 Safari web 浏览器 – Safari 11.0 – 其中包括对 WebKit 和 SafariServices 更改的新版本。 本指南介绍了这些更改。

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` 中引入了 iOS 9 作为用于显示 web 内容或从你的应用程序的用户进行身份验证选项。 其功能的详细信息可在[Web 视图](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller)指南。

引入了 iOS 11 样式更新到 Safari 视图控制器，为你的用户提供应用程序和 web 之间的更无缝体验。 例如，在地址栏现在提供 Safari 视图控制器应用内浏览器中，而不是最小化浏览器的感觉的删除。 此外可以自定义配色方案，以适应你的应用程序的配色方案通过设置`preferredBarTintColor`和`PreferredControlTintColor`属性：

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

下面的代码段将会显示在下图中呈现的紫色和白色中的栏：

![SFSafariViewController 条以紫色和空白呈现](web-images/image1.png)

此外可以通过设置更改 Safari 视图控制器中提供消除按钮`DismissButtonStyle`属性为`Done`， `Close`，或`Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![关闭按钮文本更改](web-images/image2.png)

可以更改此值时`SFSafariViewController`显示。


具体取决于在 Safari 视图控制器内部显示内容，可能有必要，以确保菜单栏不折叠当用户滚动。 这通过设置新`BarCollapsedEnabled`属性`false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![折叠禁用栏](web-images/image3.png)

Apple 还具有对 iOS 11 中的 Safari 视图控制器中的隐私进行更新。 现在，浏览数据，例如 cookie 和本地存储仅存在基于每个应用程序，而不是跨 Safari 视图控制器的所有实例。 这将保持用户浏览活动私有内您的应用程序。

其他功能如拖放支持 Url，支持`window.open()`还添加了`SFSafariViewController`iOS 11 中存在。 你可以找到有关中的新功能的详细信息[Apple 的 SFSafariViewController 文档](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor)。


## <a name="webkit"></a>易于使用的功能

`WKWebView` 是作为 iOS 8 作为一种方法或对你的用户显示 web 内容的易于使用的功能的一部分引入。 它可比得多自定义`SFSafariViewController`，使你可以创建你自己的导航和用户界面。

Apple 引入了三个主要在哪些方面改善`WKWebView`ios 11: 

- 管理 cookie 的能力
- 内容筛选
- 自定义资源加载。 

通过新执行 cookie 管理[ `WKHttpCookieStore` ](https://developer.apple.com/documentation/webkit/wkhttpcookiestore)类，该类允许你可以添加和删除 cookie，若要获取存储在 WKWebView 中的所有 cookie，并观察更改存储的 cookie。

内容筛选让你可以管理你的用户将看到的内容的类型允许你以确保它是安全的系列友好，并且如有必要，只对一组选定的用户可用。 这通过新实现[ `WKContentRuleList` ](https://developer.apple.com/documentation/webkit/wkcontentrulelist)类，通过提供的触发器和 JSON 中的操作对。 这些触发器和操作的详细信息可在 Apple 的[内容阻止规则](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html)指南。

iOS 11 现在允许你自定义`WKWebView`与加载你的 web 内容的自定义资源。 这通过实现`IWKUrlSchemeHandler`接口，这样就可以处理不是本机到 Web 工具包的 URL 方案。 此接口具有必须实现的启动和停止方法：

```csharp
public class MyHandler : NSObject, IWKUrlSchemeHandler {

    [Export("webView:startURLSchemeTask:")]
    public void StartUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        
        // Implement a IWKUrlSchemeTask here
        var response = new NSUrlResponse(urlSchemeTask.Request.Url, "text/html", ContentLength, null);
        urlSchemeTask.DidReceiveResponse(response);
        urlSchemeTask.DidReceiveData(someData);
        urlSchemeTask.DidFinish();
    }

    [Export("webView:stopURLSchemeTask:")]
    public void StopUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        throw new NotImplementedException();
    }

}
``` 

一旦实现了处理程序，使用它来设置`SetUrlSchemeHandler`属性`WKWebViewConfiguration`。 然后，将加载使用自定义方案的内容的 URL:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```

