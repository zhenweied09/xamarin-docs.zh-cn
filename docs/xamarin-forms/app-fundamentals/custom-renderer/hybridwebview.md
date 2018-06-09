---
title: 实现 HybridWebView
description: 本文演示如何创建用于 HybridWebView 自定义控件，其演示了如何增强特定于平台的 web 控件，若要从 JavaScript 中允许 C# 代码调用自定义呈现器。
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: d2cce7598fde4cf59a91940161e605860847623e
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241294"
---
# <a name="implementing-a-hybridwebview"></a>实现 HybridWebView

_Xamarin.Forms 自定义用户界面控件应派生自视图类，用于放置布局和在屏幕上的控件。本文演示如何创建用于 HybridWebView 自定义控件，其演示了如何增强特定于平台的 web 控件，若要从 JavaScript 中允许 C# 代码调用自定义呈现器。_

Xamarin.Forms 中的每个视图已随附的呈现器针对每个平台创建的本机控件的实例。 当[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) Xamarin.Forms 应用程序在 iOS 中，呈现`ViewRenderer`该类进行实例化，这反过来实例化一个本机`UIView`控件。 在 Android 平台上，`ViewRenderer`类实例化`View`控件。 在通用 Windows 平台 (UWP)，`ViewRenderer`类实例化一个本机`FrameworkElement`控件。 有关呈现器和 Xamarin.Forms 控件映射到的本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明之间的关系[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)和相应的本机控件实现它：

![](hybridwebview-images/view-classes.png "视图类和其实现的本机类之间的关系")

呈现进程可以用于通过创建自定义呈现器实现特定于平台的自定义项[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)每个平台上。 执行此操作的过程如下所示：

1. [创建](#Creating_the_HybridWebView)`HybridWebView`自定义控件。
1. [使用](#Consuming_the_HybridWebView)`HybridWebView`从 Xamarin.Forms。
1. [创建](#Creating_the_Custom_Renderer_on_each_Platform)有关的自定义呈现器`HybridWebView`每个平台上。

每个项将现在讨论反过来实现`HybridWebView`增强了特定于平台的 web 控件，若要从 JavaScript 中允许 C# 代码调用的呈现器。 `HybridWebView`实例将用于显示要求用户输入其名称的 HTML 页。 然后，当用户单击 HTML 按钮，将调用 JavaScript 函数的 C#`Action`显示一个弹出窗口，它包含的用户名称。

调用 C# 从 JavaScript 中为该过程的详细信息，请参阅[调用 C# 从 JavaScript](#Invoking_C_from_JavaScript)。 有关 HTML 页面的详细信息，请参阅[创建网页](#Creating_the_Web_Page)。

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>创建 HybridWebView

`HybridWebView`可以通过子类化来创建自定义控件[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)类，如下面的代码示例中所示：

```csharp
public class HybridWebView : View
{
  Action<string> action;
  public static readonly BindableProperty UriProperty = BindableProperty.Create (
    propertyName: "Uri",
    returnType: typeof(string),
    declaringType: typeof(HybridWebView),
    defaultValue: default(string));

  public string Uri {
    get { return (string)GetValue (UriProperty); }
    set { SetValue (UriProperty, value); }
  }

  public void RegisterAction (Action<string> callback)
  {
    action = callback;
  }

  public void Cleanup ()
  {
    action = null;
  }

  public void InvokeAction (string data)
  {
    if (action == null || data == null) {
      return;
    }
    action.Invoke (data);
  }
}
```

`HybridWebView`自定义控件在标准.NET 类库项目中创建并定义控件以下 API:

- A`Uri`属性，指定要加载的网页的地址。
- A`RegisterAction`方法是否注册`Action`与该控件。 将从 JavaScript 中通过引用的 HTML 文件包含调用的已注册的操作`Uri`属性。
- A`CleanUp`移除的已注册的引用的方法`Action`。
- `InvokeAction`调用注册方法`Action`。 从每个特定于平台的项目中的自定义呈现器将调用此方法。

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>使用 HybridWebView

`HybridWebView`自定义控件可以引用 XAML 中的标准.NET 库项目中通过声明其位置的命名空间和自定义控件上使用的命名空间前缀。 下面的代码示例演示如何`HybridWebView`自定义控件可供 XAML 页：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <local:HybridWebView x:Name="hybridWebView" Uri="index.html"
          HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
    </ContentPage.Content>
</ContentPage>
```

`local`命名空间前缀可以命名任何内容。 但是，`clr-namespace`和`assembly`值必须匹配自定义控件的详细信息。 一旦声明的命名空间，前缀用于引用自定义控件。

下面的代码示例演示如何`HybridWebView`自定义控件可供 C# 页：

```csharp
public class HybridWebViewPageCS : ContentPage
{
  public HybridWebViewPageCS ()
  {
    var hybridWebView = new HybridWebView {
      Uri = "index.html",
      HorizontalOptions = LayoutOptions.FillAndExpand,
      VerticalOptions = LayoutOptions.FillAndExpand
    };
    ...
    Padding = new Thickness (0, 20, 0, 0);
    Content = hybridWebView;
  }
}
```

`HybridWebView`实例将用于显示每个平台上的本机 web 控件。 它具有`Uri`属性设置为某一 HTML 文件存储在每个特定于平台的项目，并将由本机 web 控件。 呈现的 HTML 要求用户输入其名称中，使用 JavaScript 函数调用的 C#`Action`以响应 HTML 按钮单击。

`HybridWebViewPage`注册要从 JavaScript 中调用的操作，如下面的代码示例中所示：

```csharp
public partial class HybridWebViewPage : ContentPage
{
  public HybridWebViewPage ()
  {
    ...
    hybridWebView.RegisterAction (data => DisplayAlert ("Alert", "Hello " + data, "OK"));
  }
}
```

此操作调用[ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/)方法以显示一个模式的弹出窗口显示通过显示的 HTML 页面中输入的名称`HybridWebView`实例。

自定义呈现器现在可以添加到每个应用程序项目以允许 C# 代码要从 JavaScript 中调用，从而增强特定于平台的 web 控件。

<a nane="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

用于创建自定义呈现器类的过程如下所示：

1. 创建一个子类`ViewRenderer<T1,T2>`呈现自定义控件的类。 第一个类型参数应为自定义控件呈现器，在这种情况下是`HybridWebView`。 第二个类型参数应将实现自定义视图的本机控件。
1. 重写`OnElementChanged`呈现它进行自定义的自定义控件和写入的逻辑的方法。 创建相应的 Xamarin.Forms 自定义控件时，调用此方法。
1. 添加`ExportRenderer`到自定义呈现器类，以指定，它将用于呈现 Xamarin.Forms 自定义控件属性。 此属性用于与 xamarin.forms 结合注册自定义呈现器。

> [!NOTE]
> 对于大多数 Xamarin.Forms 元素，它是可选提供每个平台项目中的自定义呈现器。 如果自定义呈现器未注册，则将使用控件的基类的默认呈现器。 但是，自定义呈现器需要每个平台项目中呈现时[视图](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)元素。

下图说明了示例应用程序，以及它们之间的关系中的每个项目的责任：

![](hybridwebview-images/solution-structure.png "HybridWebView 自定义呈现器项目职责")

`HybridWebView`自定义控件呈现特定于平台的呈现器类，该类的所有派生自通过`ViewRenderer`为每个平台的类。 这将导致每个`HybridWebView`自定义控件呈现与特定于平台的 web 控件，如以下屏幕截图中所示：

![](hybridwebview-images/screenshots.png "每个平台上的 HybridWebView")

`ViewRenderer`类会公开`OnElementChanged`方法，该创建 Xamarin.Forms 自定义控件呈现相应的本机 web 控件时，调用方法。 此方法采用`ElementChangedEventArgs`参数，其中包含`OldElement`和`NewElement`属性。 这些属性表示 Xamarin.Forms 元素的呈现器*已*相连，和 Xamarin.Forms 元素的呈现器*是*附加，分别。 在示例应用程序`OldElement`属性将为`null`和`NewElement`属性将包含对引用`HybridWebView`实例。

重写的版本`OnElementChanged`中每个特定于平台的呈现器类，方法是执行本机 web 控件实例化和自定义项的位置。 `SetNativeControl`方法应用于实例化本机 web 控件，并且此方法还将分配到的控件引用`Control`属性。 此外，可以通过获取对呈现 Xamarin.Forms 控件的引用`Element`属性。

在某些情况下`OnElementChanged`可以多次调用方法。 因此，若要防止内存泄漏，必须格外小心时实例化一个新的本机控件。 下面的代码示例中演示了在自定义呈现器中实例化新的本机控件时要使用的方法：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control and assign it to the Control property with
    // the SetNativeControl method
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

当 `Control` 属性是 `null` 时，新的本机控件只应实例化一次。 仅当自定义呈现器附加到新 Xamarin.Forms 元素时，才应配置该控件并订阅事件处理程序。 同样，仅当呈现器所附加到的元素更改时，才应取消订阅任何订阅的事件处理程序。 采用此方法将有助于创建不会受到内存泄漏的高性能的自定义呈现器。

每个自定义呈现器类用修饰`ExportRenderer`与 xamarin.forms 结合注册呈现器的属性。 该属性采用两个参数 – 呈现，Xamarin.Forms 自定义控件的类型名称和自定义呈现器的类型名称。 `assembly`到属性的前缀指定特性应用于整个程序集。

以下各节讨论网页上加载的每个本机 web 控件、 的过程调用 C# 从 JavaScript 和在每个特定于平台的自定义呈现器类的此实现的结构。

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>创建 Web 页

下面的代码示例演示将显示的网页`HybridWebView`自定义控件：

```html
<html>
<body>
<script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>
<h1>HybridWebView Test</h1>
<br/>
Enter name: <input type="text" id="name">
<br/>
<br/>
<button type="button" onclick="javascript:invokeCSCode($('#name').val());">Invoke C# Code</button>
<br/>
<p id="result">Result:</p>
<script type="text/javascript">
function log(str)
{
    $('#result').text($('#result').text() + " " + str);
}

function invokeCSCode(data) {
    try {
        log("Sending Data:" + data);
        invokeCSharpAction(data);
    }
    catch (err){
          log(err);
    }
}
</script>
</body>
</html>
```

网页，用户可以输入在其名称`input`元素，并提供`button`元素，将会调用 C# 代码时单击。 实现此目的的过程如下所示：

- 当用户单击`button`元素，`invokeCSCode`调用 JavaScript 函数时，值为`input`传递给函数的元素。
- `invokeCSCode`函数调用`log`函数来显示它发送到 C# 的数据`Action`。 然后，它调用`invokeCSharpAction`方法以调用 C# `Action`，并将传递参数从收到`input`元素。

`invokeCSharpAction` JavaScript 函数未定义在 web 页中，并将通过每个自定义呈现器注入到它。

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>调用 C# 从 JavaScript

对于调用 C# 从 JavaScript 的过程是在每个平台上完全相同：

- 自定义呈现器创建本机 web 控件，并加载指定的 HTML 文件`HybridWebView.Uri`属性。
- 网页已加载，请插入自定义呈现器`invokeCSharpAction`到网页的 JavaScript 函数。
- 当用户输入其名称并单击 HTML`button`元素，`invokeCSCode`调用函数时，这反过来调用`invokeCSharpAction`函数。
- `invokeCSharpAction`函数调用中的自定义呈现器，这反过来调用的方法`HybridWebView.InvokeAction`方法。
- `HybridWebView.InvokeAction`方法调用的已注册`Action`。

下列各节将讨论如何在每个平台上实现此过程。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上创建自定义呈现器

下面的代码示例演示为 iOS 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer (typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, WKWebView>, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        protected override void OnElementChanged (ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged (e);

            if (Control == null) {
                userController = new WKUserContentController ();
                var script = new WKUserScript (new NSString (JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
                userController.AddUserScript (script);
                userController.AddScriptMessageHandler (this, "invokeAction");

                var config = new WKWebViewConfiguration { UserContentController = userController };
                var webView = new WKWebView (Frame, config);
                SetNativeControl (webView);
            }
            if (e.OldElement != null) {
                userController.RemoveAllUserScripts ();
                userController.RemoveScriptMessageHandler ("invokeAction");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup ();
            }
            if (e.NewElement != null) {
                string fileName = Path.Combine (NSBundle.MainBundle.BundlePath, string.Format ("Content/{0}", Element.Uri));
                Control.LoadRequest (new NSUrlRequest (new NSUrl (fileName, false)));
            }
        }

        public void DidReceiveScriptMessage (WKUserContentController userContentController, WKScriptMessage message)
        {
            Element.InvokeAction (message.Body.ToString ());
        }
    }
}
```

`HybridWebViewRenderer`类加载中指定的 web 页`HybridWebView.Uri`属性转换成一个本机[ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/)控件，与`invokeCSharpAction`JavaScript 函数插入到网页。 用户输入其名称并单击 HTML 后`button`元素，`invokeCSharpAction`执行 JavaScript 函数，与`DidReceiveScriptMessage`从网页上接收消息后调用的方法。 反过来，此方法调用`HybridWebView.InvokeAction`方法，将调用已注册的操作，以显示弹出窗口。

此功能来实现，如下所示：

- 假设`Control`属性是`null`，执行以下操作：
  - A [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/)创建实例，这样，发布消息和将用户脚本注入到网页。
  - A [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/)创建实例将注入`invokeCSharpAction`JavaScript 函数导入到 web 页后加载的网页。
  - [ `WKUserContentController.AddScript` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddUserScript/p/WebKit.WKUserScript/)方法将添加[ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/)到内容的控制器实例。
  - [ `WKUserContentController.AddScriptMessageHandler` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddScriptMessageHandler/p/WebKit.IWKScriptMessageHandler/System.String/)方法将添加名为的脚本消息处理`invokeAction`到[ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/)实例，这将导致 JavaScript 函数`window.webkit.messageHandlers.invokeAction.postMessage(data)`以在所有中定义将使用的所有 web 视图中的帧`WKUserContentController`实例。
  - A [ `WKWebViewConfiguration` ](https://developer.xamarin.com/api/type/WebKit.WKWebViewConfiguration/)创建实例时，与[ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/)正在将设置为内容控制器的实例。
  - A [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/)实例化控件，与`SetNativeControl`调用方法来分配对引用`WKWebView`控制转移到`Control`属性。
- 提供自定义呈现器附加到新 Xamarin.Forms 元素：
  - [ `WKWebView.LoadRequest` ](https://developer.xamarin.com/api/member/WebKit.WKWebView.LoadRequest/p/Foundation.NSUrlRequest/)方法将加载指定的 HTML 文件`HybridWebView.Uri`属性。 该代码指定文件存储在`Content`项目文件夹中的。 一旦将显示网页，`invokeCSharpAction`将插入到网页的 JavaScript 函数。
- 当元素呈现器附加到更改：
  - 释放资源。

> [!NOTE]
> `WKWebView` IOS 8 及更高版本中才支持类。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

下面的代码示例显示了 Android 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Android.Webkit.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                var webView = new Android.Webkit.WebView(_context);
                webView.Settings.JavaScriptEnabled = true;
                SetNativeControl(webView);
            }
            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }
            if (e.NewElement != null)
            {
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl(string.Format("file:///android_asset/Content/{0}", Element.Uri));
                InjectJS(JavaScriptFunction);
            }
        }

        void InjectJS(string script)
        {
            if (Control != null)
            {
                Control.LoadUrl(string.Format("javascript: {0}", script));
            }
        }
    }
}
```

`HybridWebViewRenderer`类加载中指定的 web 页`HybridWebView.Uri`属性转换成一个本机[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)控件，与`invokeCSharpAction`JavaScript 函数注入网页上，加载网页之后,与`InjectJS`方法。 用户输入其名称并单击 HTML 后`button`元素，`invokeCSharpAction`执行 JavaScript 函数。 此功能来实现，如下所示：

- 假设`Control`属性是`null`，执行以下操作：
  - 一个本机[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)创建实例，并在控件中启用 JavaScript。
  - `SetNativeControl`调用方法来为本机分配一个引用[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)控制转移到`Control`属性。
- 提供自定义呈现器附加到新 Xamarin.Forms 元素：
  - [ `WebView.AddJavascriptInterface` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/)方法插入一个新`JSBridge`到主框架 WebView 的 JavaScript 上下文，其命名的实例`jsBridge`。 这样中的方法`JSBridge`类从 JavaScript 中访问。
  - [ `WebView.LoadUrl` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/)方法将加载指定的 HTML 文件`HybridWebView.Uri`属性。 该代码指定文件存储在`Content`项目文件夹中的。
  - `InjectJS`调用方法将注入`invokeCSharpAction`到网页的 JavaScript 函数。
- 当元素呈现器附加到更改：
  - 释放资源。

当`invokeCSharpAction`执行 JavaScript 函数，它会调用`JSBridge.InvokeAction`方法，下面的代码示例中所示：

```csharp
public class JSBridge : Java.Lang.Object
{
  readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

  public JSBridge (HybridWebViewRenderer hybridRenderer)
  {
    hybridWebViewRenderer = new WeakReference <HybridWebViewRenderer> (hybridRenderer);
  }

  [JavascriptInterface]
  [Export ("invokeAction")]
  public void InvokeAction (string data)
  {
    HybridWebViewRenderer hybridRenderer;

    if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget (out hybridRenderer)) {
      hybridRenderer.Element.InvokeAction (data);
    }
  }
}
```

类必须派生自`Java.Lang.Object`，并向 JavaScript 公开的方法必须使用修饰`[JavascriptInterface]`和`[Export]`属性。 因此，当`invokeCSharpAction`注入到 web 页和执行的 JavaScript 功能，它将调用`JSBridge.InvokeAction`方法，因为正在使用修饰`[JavascriptInterface]`和`[Export("invokeAction")]`属性。 反过来，`InvokeAction`方法调用`HybridWebView.InvokeAction`方法，其将调用已注册的操作，以显示弹出窗口。

> [!NOTE]
> 项目使用`[Export]`属性必须包括对引用`Mono.Android.Export`，或将导致编译器错误。

请注意，`JSBridge`类维护`WeakReference`到`HybridWebViewRenderer`类。 这是为了避免创建两个类之间的循环引用。 有关详细信息请参阅[弱引用](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx)MSDN 上。

> [!IMPORTANT]
> 确保 Android Oreo 的 Android 清单设置**目标 Android 版本**到**自动**。 否则，运行此代码会导致错误消息"invokeCSharpAction 未定义"。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上创建自定义呈现器

下面的代码示例显示了适用于 UWP 的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Windows.UI.Xaml.Controls.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                SetNativeControl(new Windows.UI.Xaml.Controls.WebView());
            }
            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri(string.Format("ms-appx-web:///Content//{0}", Element.Uri));
            }
        }

        async void OnWebViewNavigationCompleted(WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            Element.InvokeAction(e.Value);
        }
    }
}
```

`HybridWebViewRenderer`类加载中指定的 web 页`HybridWebView.Uri`属性转换成一个本机`WebView`控件，与`invokeCSharpAction`JavaScript 函数注入网页上，加载网页之后, 与`WebView.InvokeScriptAsync`方法。 用户输入其名称并单击 HTML 后`button`元素，`invokeCSharpAction`执行 JavaScript 函数，与`OnWebViewScriptNotify`网页上收到通知后调用的方法。 反过来，此方法调用`HybridWebView.InvokeAction`方法，将调用已注册的操作，以显示弹出窗口。

此功能来实现，如下所示：

- 假设`Control`属性是`null`，执行以下操作：
  - `SetNativeControl`调用方法来实例化一个新的本机`WebView`控制并分配给它的引用`Control`属性。
- 提供自定义呈现器附加到新 Xamarin.Forms 元素：
  - 事件处理程序`NavigationCompleted`和`ScriptNotify`注册事件。 `NavigationCompleted`时将激发事件是本机`WebView`控件完成加载当前内容，或如果具有失败的导航。 `ScriptNotify`时将激发事件的本机内容`WebView`控件使用 JavaScript 将字符串传递给应用程序。 网页激发`ScriptNotify`通过调用的事件`window.external.notify`时传递`string`参数。
  - `WebView.Source`属性设置为指定的 HTML 文件的 URI`HybridWebView.Uri`属性。 该代码假定该文件存储在`Content`项目文件夹中的。 一旦将显示网页，`NavigationCompleted`事件将激发和`OnWebViewNavigationCompleted`将调用方法。 `invokeCSharpAction` JavaScript 函数然后将其插入包含网页`WebView.InvokeScriptAsync`方法，提供程序已成功完成的导航。
- 当元素呈现器附加到更改：
  - 事件是取消订阅。

## <a name="summary"></a>总结

本文演示了如何创建自定义呈现器`HybridWebView`自定义控件，它演示了如何以增强特定于平台的 web 控件，若要从 JavaScript 中允许 C# 代码调用。


## <a name="related-links"></a>相关链接

- [CustomRendererHybridWebView （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)
- [从 JavaScript 中调用 C#](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript/)
