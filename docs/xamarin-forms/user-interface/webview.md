---
title: WebView
description: 提供本地或网络 web 内容和文档。
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: ed37e723d4b1a7997890c41886df8d117425e270
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2018
---
# <a name="webview"></a>WebView

[WebView](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)是视图，以显示 web 和 HTML 内容中你的应用程序。 与不同`OpenUri`，这可以使用户转到 web 浏览器在设备上，`WebView`显示你的应用程序中的 HTML 内容。

本指南由以下部分组成：

- **[内容](#Content)** &ndash; WebView 支持各种内容的源，包括嵌入的 HTML、 web 页面和 HTML 字符串。
- **[导航](#Navigation)** &ndash; WebView 包括对导航到特定页和追溯支持。
- **[事件](#Events)** &ndash;侦听和响应 WebView 中用户所执行的操作。
- **[性能](#Performance)** &ndash;了解每个平台上的 WebView 的性能特征。
- **[权限](#Permissions)** &ndash;了解如何设置权限，以便 web 视图将在你的应用程序中工作。
- **[布局](#Layout)** &ndash; WebView 具有某些非常特定的要求的布局方式。了解如何确保 WebView 正确显示：

![](webview-images/in-app-browser.png "在应用程序浏览器中")

## <a name="content"></a>内容

WebView 附带了对以下类型的内容的支持：

- HTML & CSS 网站&ndash;WebView 具有对网站使用 HTML 和 CSS，包括支持 JavaScript 编写的完整支持。
- 文档&ndash;WebView WebView 每个平台上使用本机组件实现的因为它能够显示每个平台可以看到的文档。 这意味着在 iOS 和 Android 上工作的 PDF 文件。
- HTML 字符串&ndash;WebView 可以显示从内存的 HTML 字符串。
- 本地文件&ndash;WebView 可以显示上述内容类型的任何嵌入在应用程序。

> [!NOTE]
> `WebView` 在 Windows 上不支持 Silverlight、 Flash 或任何 ActiveX 控件，即使它们在该平台上支持由 Internet Explorer。

### <a name="websites"></a>网站

若要显示从 internet 的网站，设置`WebView`的[ `Source` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebViewSource/)到字符串的 URL 的属性：

```csharp
var browser = new WebView {
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Url 必须完全与指定的协议格式 （即它必须具有"http://"或"https://"附加到它）。

#### <a name="ios-and-ats"></a>iOS 和 ATS

自版本 9，iOS 将只允许你的应用程序与默认情况下实现最佳实践安全性的服务器进行通信。 值必须在设置`Info.plist`实现与不安全的服务器通信。

> [!NOTE]
> 如果你的应用程序需要连接到不安全的网站，你应始终输入域作为异常使用`NSExceptionDomains`而不是关闭 ATS 完全使用`NSAllowsArbitraryLoads`。 `NSAllowsArbitraryLoads` 仅应在极端紧急情况下使用。

下面演示了如何启用特定域 （在此案例 xamarin.com) 跳过 ATS 要求：

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSExceptionDomains</key>
        <dict>
            <key>xamarin.com</key>
            <dict>
                <key>NSIncludesSubdomains</key>
                <true/>
                <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
                <true/>
                <key>NSTemporaryExceptionMinimumTLSVersion</key>
                <string>TLSv1.1</string>
            </dict>
        </dict>
    </dict>
```

最好只启用某些域绕过 ATS，让你能够使用受信任的站点上不受信任域的附加安全性获益时也是如此。 下面演示了不安全的应用程序禁用 ATS 方法：

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
```

请参阅[应用传输安全](~/ios/app-fundamentals/ats.md)有关 iOS 9 中此新功能的详细信息。

### <a name="html-strings"></a>HTML 字符串

如果你想要显示的代码中动态定义的 HTML 字符串，你将需要创建的实例[ `HtmlWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.HtmlWebViewSource/):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![](webview-images/html-string.png "WebView 显示的 HTML 字符串")

在上面的代码中，`@`用于将以字符串形式的 HTML 标记文本，这意味着，将忽略所有常见的转义字符。

### <a name="local-html-content"></a>本地 HTML 内容

WebView 可以显示内容从 HTML、 CSS 和 Javascript 嵌入在应用内。 例如：

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamrin.Forms</h1>
    <p>This is an iOS web page.</p>
    <img src="XamarinLogo.png" />
  </body>
</html>
```

CSS:

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

请注意，在上面的 CSS 中指定的字体将需要进行自定义的每个平台，如不是每个平台都有相同的字体。

显示本地内容使用`WebView`，你将需要打开像任何其他 HTML 文件然后将内容加载到字符串形式`Html`属性`HtmlWebViewSource`。 打开文件的详细信息，请参阅[使用文件](~/xamarin-forms/app-fundamentals/files.md)。

以下屏幕快照显示的每个平台上显示本地内容的结果：

![](webview-images/local-content.png "WebView 显示本地内容")

已加载的第一页，尽管`WebView`不能识别的 HTML 来自何处。 处理引用本地资源的页时，这是个问题。 为每个其他页面使本地页链接使用的一个单独的 JavaScript 文件，或页链接到 CSS 样式表时，将包括时，可能发生这种情况的示例。  

若要解决此问题，你需要告知`WebView`在哪里可以找到文件系统上的文件。 执行此操作通过设置`BaseUrl`属性`HtmlWebViewSource`由`WebView`。

由于每个操作系统上的文件系统不同，你需要确定每个平台上的该 URL。 Xamarin.Forms 公开`DependencyService`用于解决在每个平台上运行时的依赖关系。

若要使用`DependencyService`，首先定义一个可以在每个平台实现的接口：

```csharp
public interface IBaseUrl { string Get(); }
```

请注意，每个平台上实现该接口，直到应用程序将不会运行。 在常见的项目中，请确保你记住设置`BaseUrl`使用`DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

然后必须提供每个平台接口的实现。

#### <a name="ios"></a>iOS

在 iOS 上的 web 内容应放置在项目的根目录或**资源**目录生成操作*BundleResource*，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/ios-vs.png "在 iOS 上的本地文件")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](webview-images/ios-xs.png "在 iOS 上的本地文件")

-----

`BaseUrl`应设置为主要的捆绑包的路径：

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS{
  public class BaseUrl_iOS : IBaseUrl {
    public string Get() {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

在 Android 上，HTML、 CSS 和映像的文件夹中放资产与生成操作*AndroidAsset*如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/android-vs.png "在 Android 上的本地文件")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](webview-images/android-xs.png "在 Android 上的本地文件")

-----

在 Android 上，`BaseUrl`应设置为`"file:///android_asset/"`:

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android {
  public class BaseUrl_Android : IBaseUrl {
    public string Get() {
      return "file:///android_asset/";
    }
  }
}
```

在 Android 上，文件中**资产**文件夹还可以访问通过当前公开的 Android 上下文`MainActivity.Instance`属性：

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html"))) {
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP) 项目中，放在 HTML、 CSS 和图像中的项目根目录位置设置为的生成操作*内容*。

`BaseUrl`应设置为`"ms-appx-web:///"`:

```csharp
[assembly: Dependency(typeof(BaseUrl))]
namespace WorkingWithWebview.UWP
{
    public class BaseUrl : IBaseUrl
    {
        public string Get()
        {
            return "ms-appx-web:///";
        }
    }
}
```

## <a name="navigation"></a>导航

WebView 支持通过多个方法和属性，它使可导航：

- **GoForward()** &ndash;如果`CanGoForward`为 true，调用`GoForward`向前导航到访问过的下一页。
- **GoBack()** &ndash;如果`CanGoBack`为 true，调用`GoBack`将导航到已访问的最后一页。
- **CanGoBack** &ndash; `true`是否存在页导航回，`false`浏览器是否在启动 URL。
- **CanGoForward** &ndash; `true`如果用户已向后导航，并可以向前移动到已经访问的页面。

在页中，`WebView`不支持多点触控笔势。 务必要确保该内容移动优化，且显示而无需缩放。

很常见的应用程序以显示中的链接`WebView`，而不是设备的浏览器。 在这些情况下，它可用于允许正常导航窗格中，但当用户命中后端上的起始链接时，应用程序应返回到正常的应用程序视图。

使用内置的导航方法和属性来启用此方案。

首先创建浏览器视图的页：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="WebViewDemo.InAppDemo"
Title="In App Browser">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout Orientation="Horizontal" Padding="10,10">
                <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="backClicked" />
                <Button Text="Forward" HorizontalOptions="End" Clicked="forwardClicked" />
            </StackLayout>
            <WebView x:Name="Browser" WidthRequest="1000" HeightRequest="1000" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在我们的代码隐藏：

```csharp
public partial class InAppDemo : ContentPage
{
  //sets the URL for the browser in the page at creation
    public InAppDemo (string URL)
    {
        InitializeComponent ();
        Browser.Source = URL;
    }


    private void backClicked(object sender, EventArgs e)
    {
    // Check to see if there is anywhere to go back to
        if (Browser.CanGoBack) {
            Browser.GoBack ();
        } else { // If not, leave the view
            Navigation.PopAsync ();
        }
    }

    private void forwardClicked(object sender, EventArgs e)
    {
        if (Browser.CanGoForward) {
            Browser.GoForward ();
        }
    }
}
```

就这么简单！

![](webview-images/in-app-browser.png "WebView 导航按钮")

## <a name="events"></a>事件

WebView 引发两个事件，来帮助你响应的状态更改：

- **导航** &ndash; WebView 开始加载新页时引发事件。
- **导航**&ndash;页面加载和导航已停止时引发事件。

如果你预计使用需要很长时间才能加载的网页，请考虑使用这些事件来实现状态指示器。 例如：

我们 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="WebViewDemo.LoadingDemo" Title="Loading Demo">
    <ContentPage.Content>
    <StackLayout>
      <Label x:Name="LoadingLabel"
        Text="Loading..."
        HorizontalOptions="Center"
        isVisible="false" />
      <WebView x:Name="Browser"
      HeightRequest="1000"
      WidthRequest="1000"
      Navigating="webOnNavigating"
      Navigated="webOnEndNavigating" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```
我们的两个事件处理程序：

```csharp
void webOnNavigating (object sender, WebNavigatingEventArgs e)
{
            LoadingLabel.IsVisible = true;
}

void webOnEndNavigating (object sender, WebNavigatedEventArgs e)
{
            LoadingLabel.IsVisible = false;
}
```

这将导致下面的输出 （加载）：

![](webview-images/loading-start.png "WebView 导航事件示例")

完成的加载：

![](webview-images/loading-end.png "WebView 导航事件示例")

## <a name="performance"></a>性能

最新技术进步已了解每个流行的 web 浏览器中采用技术，如硬件加速呈现和 JavaScript 编译。 遗憾的是，由于安全限制，大部分这些改进中未提供的 iOS equaivalent `WebView`， `UIWebView`。 Xamarin.Forms`WebView`使用`UIWebView`。 如果这是个问题，你将需要编写自定义呈现器，则使用`WKWebView`，它支持更快地浏览。 请注意，`WKWebView`仅支持 iOS 8 和更高版本。

在默认情况下的 Android 上的 WebView 是大约与对内置浏览器一样快。

[UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view)使用 Microsoft Edge 呈现引擎。 台式机和平板电脑设备应看到与使用 Edge 浏览器本身相同的性能。

## <a name="permissions"></a>权限

为了使`WebView`工作，你必须确保为每个平台设置权限。 请注意，在某些平台上`WebView`处于调试模式，但不是在为发布生成时将工作。 这是因为为如果处于调试模式的 Mac，默认情况下，由 Visual Studio 的某些权限，如用于在 Android 上，internet 访问设置。

- **UWP** &ndash;显示网络内容时需要 Internet （客户端和服务器） 功能。
- **Android** &ndash;需要`INTERNET`仅显示网络中的内容时。 本地内容需要任何特殊权限。
- **iOS** &ndash;需要任何特殊权限。

## <a name="layout"></a>布局

与大多数其他 Xamarin.Forms 视图，不同`WebView`要求`HeightRequest`和`WidthRequest`时包含在 StackLayout 或 RelativeLayout 所指定的。 如果您不能指定这些属性，`WebView`将不会呈现。

下面的示例演示商合作的布局呈现`WebView`s:

使用 WidthRequest & HeightRequest StackLayout:

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

使用 WidthRequest & HeightRequest RelativeLayout:

```xaml
<RelativeLayout>
    <Label Text="test"
        RelativeLayout.XConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=10}"
        RelativeLayout.YConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=20}" />
    <WebView Source="http://www.xamarin.com/"
        RelativeLayout.XConstraint="{ConstraintExpression Type=Constant,
                                     Constant=10}"
        RelativeLayout.YConstraint="{ConstraintExpression Type=Constant,
                                     Constant=50}"
        WidthRequest="1000" HeightRequest="1000" />
</RelativeLayout>
```

AbsoluteLayout*而无需*WidthRequest 和 HeightRequest:

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

网格*而无需*WidthRequest 和 HeightRequest。 网格是一种不需要指定请求的高度和宽度的几个版式。:

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="100" />
        <RowDefinition Height="*" />
    </Grid.RowDefinitions>
    <Label Text="test" Grid.Row="0" />
    <WebView Source="http://www.xamarin.com/" Grid.Row="1" />
</Grid>
```


## <a name="related-links"></a>相关链接

- [使用 WebView （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/)
- [WebView （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView)
