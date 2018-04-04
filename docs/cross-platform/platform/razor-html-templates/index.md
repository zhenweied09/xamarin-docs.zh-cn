---
title: 使用 Razor 模板生成 HTML 视图
description: " 使用全屏网页呈现 HTML 可能是一种简单而有效的方法来呈现复杂格式设置，以跨平台的方式，尤其是如果你已有的 HTML、 Javascript 和 CSS 从网站项目。"
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: fa361e48f8f7e236a3295deda2d80a02ef06b34d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="building-html-views-using-razor-templates"></a>使用 Razor 模板生成 HTML 视图

在移动开发环境中术语"混合应用"通常是指的应用程序以在托管的 web 查看器控件中的 HTML 页面显示部分 （或所有） 其屏幕。

有某些开发环境中，使您生成也限制了在平台中移动应用程序完全在 HTML 和 Javascript，但这些应用程序尝试以实现复杂的处理或用户界面效果时可能会造成性能问题，并且是它们可以访问的功能。

Xamarin 提供两个领域的最佳产品，尤其是在使用 Razor HTML 模板化引擎时。 使用 Xamarin 你可以灵活地生成跨平台模板化 HTML 视图使用 Javascript 和 CSS，但还具有完全访问权限的基础平台 Api 和使用 C# 快速处理。

本文档说明如何使用模板化引擎生成 HTML + Javascript + CSS 视图，可跨移动平台使用 Xamarin Razor。

## <a name="using-web-views-programmatically"></a>以编程方式使用 Web 视图

我们了解 Razor 之前本部分将介绍如何使用 web 视图以显示 HTML 内容直接-具体而言，将在应用程序生成的 HTML 内容。

Xamarin iOS 和 Android 上提供对基础平台 Api 的完全访问权限，因此很容易地创建和显示使用 C# 的 HTML。 每个平台的基本语法如下所示。

### <a name="ios"></a>iOS

在中 Xamarin.iOS UIWebView 控件中显示 HTML 还采用代码只需几的行：

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

请参阅[iOS UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/)配方使用 UIWebView 控件有关的详细信息。

### <a name="android"></a>Android

中的代码只需几行完成使用 Xamarin.Android 的 WebView 控件中显示 HTML:

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

请参阅[Android WebView](http://docs.xamarin.com/recipes/android/controls/webview/)配方使用 WebView 控件有关的详细信息。

### <a name="specifying-the-base-directory"></a>指定的基目录

在这两个平台上是一个参数，指定的 HTML 页的基目录。 这是用于解析相对引用到的资源，如图像和 CSS 文件的设备的文件系统上的位置。 例如，如标记

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

这些文件，请参阅： **style.css**， **monkey.jpg**和**jscript.js**。 基目录设置告知 web 视图，这些文件所在的位置以便它们可以是加载到页面中。

#### <a name="ios"></a>iOS

在 iOS 替换为以下的 C# 代码中呈现的模板输出：

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

基目录指定为`NSBundle.MainBundle.BundleUrl`它是指在安装了应用程序的目录。 中的所有文件**资源**文件夹复制到此位置，如**style.css**文件此处所示：

 ![iPhoneHybrid 解决方案](images/image1_240x163.png)

应为所有静态内容文件的生成操作**BundleResource**:

 ![iOS 项目的生成操作： BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android 还需要时在 web 视图中显示 html 字符串作为参数传递的基目录。

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

特殊字符串**file:///android_asset/**指的是你的应用，显示此处包含中的 Android Assets 文件夹**style.css**文件。

 ![AndroidHybrid 解决方案](images/image3_240x167.png)

应为所有静态内容文件的生成操作**AndroidAsset**。

 ![Android 项目的生成操作： AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>从 HTML 和 Javascript 调用 C#

当 html 页加载到 web 视图时，它将处理的链接和窗体那样从服务器加载页面。 这意味着，如果用户单击的链接，或提交表单时 web 视图将尝试导航到指定的目标。

如果链接是指向外部服务器 （例如 google.com) 然后 web 视图将尝试加载外部网站 （假定没有 internet 连接）。

```html
<a href="http://google.com/">Google</a>
```

如果链接是相对然后 web 视图将尝试从基目录中加载该内容。 显然，内容存储在设备上的应用，也不不需要此功能，任何网络连接。

```html
<a href="somepage.html">Local content</a>
```

窗体操作按照相同的规则。

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

如果您不想要承载客户端; 上的 web 服务器但是，可以使用相同的服务器通信技术今天的响应式设计模式中使用 HTTP GET，通过调用服务，还可以通过发出 Javascript 以异步方式处理响应 （或调用 Javascript 已托管在 web 视图中）。 这使您可以轻松地将数据从 HTML 传递回处理然后显示结果返回 HTML 页的 C# 代码。

IOS 和 Android 提供用于应用程序代码以截获这些导航事件，以便应用程序代码可以响应 （如果需要） 的机制。 此功能至关重要构建混合应用程序，因为它允许与 web 视图进行交互的本机代码。

#### <a name="ios"></a>iOS

可以重写在 iOS 中的 web 视图上的 ShouldStartLoad 事件，以允许应用程序代码来处理 （如链接单击） 的导航请求。 方法参数提供的所有信息

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

然后将分配事件处理程序：

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

在 Android 上只需子类 WebViewClient 然后实现代码以对导航请求做出响应。

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, string url) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

然后将客户端上的 web 视图：

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>从 C# 中调用 Javascript

除了告诉 web 视图，以便加载新的 HTML 页，C# 代码还可以运行 Javascript 内当前显示的页面。 可以使用 C# 字符串创建整个 Javascript 代码块，并将其执行，或者您就可以完成对已通过的页上提供的 Javascript 方法调用`script`标记。

#### <a name="android"></a>Android

创建 Javascript 代码来执行，然后它前面加"javascript:"，并指示要加载该字符串的 web 视图：

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

iOS web 视图提供专门要调用 Javascript 的方法：

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>总结

本部分中引入了在 Android 和 iOS，让我们构建使用 Xamarin，混合应用程序上的 web 视图控件的功能包括：

-  能够从在代码中，生成的字符串加载 HTML
-  能够引用本地文件 （CSS、 Javascript、 图像或其他 HTML 文件）
-  拦截在 C# 代码中，导航请求
-  能够从 C# 代码中调用 Javascript。


下一节介绍 Razor，可以轻松地创建 HTML 中以使用混合应用程序。

## <a name="what-is-razor"></a>什么是 Razor？

Razor 是中引入了 ASP.NET MVC，最初以在服务器上运行，并生成 HTML 到 web 浏览器提供一个模板化引擎。

Razor 模板化引擎扩展使用 C# 的标准 HTML 语法，使得可以 express 布局，还可以轻松地将 CSS 样式表和 Javascript 的合并。 模板可以引用一个模型类，这可以是任何自定义的类型和可以直接从模板访问其属性。 其主要优点之一是能够轻松地混合使用 HTML 和 C# 语法。

Razor 模板并不局限于服务器端使用，它们还可以包含 Xamarin 应用程序。 使用 Razor 模板以及能够以编程方式使用 web 视图使复杂的跨平台混合应用程序可以使用 Xamarin 生成。

### <a name="razor-template-basics"></a>Razor 模板基础知识

Razor 模板文件具有**.cshtml**文件扩展名。 它们都可添加到 Xamarin 项目的文本模板化一节中从**新文件**对话框：

 ![新文件-Razor 模板](images/image5_400x201.png)

简单的 Razor 模板 ( **RazorView.cshtml**) 如下所示。

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

请注意与常规的 HTML 文件的以下差异：

-  `@`符号在 Razor 模板中具有特殊含义 – 它表示以下表达式是 C# 要进行求值。
- `@model` 指令始终显示为 Razor 模板文件的第一行。
-  `@model`指令应跟一个类型。 在此示例中的简单字符串被传递给模板，但这可能是任何自定义类。
-  当`@Model`引用在整个模板，它提供对生成 （在本示例，它将是一个字符串） 时传递给模板对象的引用。
-  IDE 将自动生成分部类模板 (与文件**.cshtml**扩展)。 你可以查看此代码，但不是应进行编辑。
 ![RazorView.cshtml](images/image6_125x34.png)分部类名为 RazorView 以匹配.cshtml 模板文件的名称。 它是用于 C# 代码中的模板引用此名称。
- `@using` 语句还可包含在 Razor 模板包含其他命名空间的顶部。


然后可以使用下面的 C# 代码生成最终的 HTML 输出。 请注意，我们指定为字符串"Hello World"这将合并到的呈现的模板输出的模型。

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

下面是在 iOS 模拟器和 Android 仿真程序上的 web 视图中所示的输出：

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>更多的 Razor 语法

在本节中我们将介绍一些基本的 Razor 语法来帮助你开始使用它。 此部分中的示例填充以下类的数据，并将其使用 Razor 显示：

```csharp
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

所有示例都使用以下数据初始化代码

```csharp
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>显示模型属性

具有属性的类模型时，它们轻松引用 Razor 模板中此示例模板中所示：

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

这可以呈现为字符串使用以下代码：

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

最终输出所示，iOS 模拟器和 Android 仿真程序上的 web 视图中：

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>C# 语句

更复杂的 C# 可以包括在模板中，如模型属性更新和年龄在此示例中的计算：

```html
@model Monkey
<html>
    <body>
    @{
        Model.Name = "Rupert X. Monkey";
        Model.Birthday = new DateTime(2011,3,1);
    }
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    </body>
</html>
```

你可以通过括起的代码与编写复杂的单行 C# 表达式 （如格式年龄） `@()`。

多个 C# 语句可以编写通过括起它们与`@{}`。

#### <a name="if-else-statements"></a>如果其他语句

可以使用表示代码分支`@if`此模板的示例中所示。

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <p>@Model.FavoriteFoods.Count favorites</p>
    }
    </body>
</html>
```

#### <a name="loops"></a>循环

循环结构，比如`foreach`还可添加。 `@`循环变量上，可以使用前缀 (`@food`在这种情况下) 在 HTML 呈现它。

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <ul>
            @foreach (var food in @Model.FavoriteFoods) {
                <li>@food</li>
            }
        </ul>
    }
    </body>
</html>
```

将上述模板的输出显示在 iOS 模拟器和 Android 仿真程序上运行：

 ![Rupert X Monkey](images/image9_520x277.png)

本部分已覆盖使用 Razor 模板来呈现简单的只读视图的基础知识。 下一部分将说明如何构建使用 Razor 的可接受用户输入，并在 HTML 视图和 C# 中的 Javascript 之间进行互操作的更完整应用。

## <a name="using-razor-templates-with-xamarin"></a>通过 Xamarin 使用 Razor 模板

本部分说明如何使用自己的 mac。 使用 Visual Studio 中的解决方案模板的混合应用程序的生成 有三个模板的可用**文件 > 新建 > 解决方案...**窗口：

- **Android > 应用程序 > Android WebView 应用程序**
- **iOS > 应用程序 > WebView 应用程序**
- **ASP.NET MVC 项目**



**新解决方案**对于 iPhone 和 Android 的项目窗口类似如下所示-右侧的解决方案说明突出显示对 Razor 模板化引擎支持。

 ![创建 iPhone 和 Android 的解决方案](images/image13_1139x959.png)

请注意，你可以轻松添加**.cshtml** Razor 模板*任何*现有 Xamarin 项目，它不需要使用这些解决方案模板。 iOS 项目不需要以使用这两个; Razor 情节提要只需将 UIWebView 控件添加到任何视图以编程方式，并可以导致 Razor 模板整个 C# 代码中。

在用于 iPhone 和 Android 项目的默认模板解决方案内容如下所示：

 ![iPhone 和 Android 的模板](images/image10_428x310.png)

模板为你提供了已准备就绪的应用程序基础结构能够加载具有数据模型对象的 Razor 模板，处理用户输入并返回到用户通过 Javascript 进行通信。

解决方案的重要部分是：

-  静态内容，如**style.css**文件。
-  Razor.cshtml 模板文件喜欢**RazorView.cshtml** 。
-  模型如 Razor 模板中引用的类**ExampleModel.cs** 。
-  创建 web 视图并呈现的模板，如的特定于平台的类`MainActivity`在 Android 上和`iPhoneHybridViewController`在 iOS 上。


以下部分说明项目的工作方式。

### <a name="static-content"></a>静态内容

静态内容包括 CSS 样式表、 图像、 Javascript 文件或其他内容，可以从链接或由 web 视图中显示的 HTML 文件引用。

模板项目包括要演示如何在混合应用中包含静态内容的最小的样式表。 CSS 样式表引用模板与此相似：

```html
<link rel="stylesheet" href="style.css" />
```

你可以添加所需的样式表，你需要包括 JQuery 等框架的 Javascript 文件。

### <a name="razor-cshtml-templates"></a>Razor cshtml 模板

该模板包含 Razor **.cshtml**具有预编写的代码以帮助通信 HTML/Javascript 和 C# 之间的数据的文件。 这将允许您生成复杂的混合应用程序不只是显示只读数据从模型中，但还接受 HTML 中的用户输入并将其传递回处理或存储的 C# 代码。

#### <a name="rendering-the-template"></a>呈现模板

调用`GenerateString`在模板上呈现 HTML 供 web 视图中显示。 如果该模板使用一个模型，则它应在呈现之前提供。 此图说明了呈现的工作原理 – 未解析的 web 视图在运行时，使用提供的基目录查找指定的文件、 静态资源。

 ![Razor 流程图](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>从模板调用 C# 代码

从回调到 C# 的呈现的 web 视图中的通信，可以设置对于 web 视图中，URL，然后截获请求在 C# 中进行处理而不必重新加载 web 视图的本机请求的过程。

中如何处理 RazorView 的按钮，可以看到示例。 该按钮具有以下 HTML:

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

`InvokeCSharpWithFormValues` Javascript 函数将读取的所有值从 HTML 窗体和集`location.href`web 视图：

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

这尝试导航到与自定义方案 （如 URL web 视图 `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

当本机 web 视图处理此导航请求时，我们将有机会截获到它。 在 iOS 中，这是通过处理 UIWebView HandleShouldStartLoad 事件。 在 Android 中，我们只需子类 WebViewClient 在窗体，并重写 ShouldOverrideUrlLoading。

这些两个导航拦截器的内部结构实质上是相同的。

首先，请检查 URL web 视图尝试加载，并且如果它不会开始自定义方案 (`hybrid:`)，允许作为普通导航发生。

对自定义 URL 架构，方案之间的 URL 中的所有内容和"？" 是用于处理 （在此情况下，"UpdateLabel"） 的方法名称。 查询字符串中的所有内容将被视为方法调用的参数：

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel` 在此示例中，执行最少量的字符串操作 （将"C# 指出"的字符串） 的文本框中参数，然后调用到 web 视图的返回。

在处理 URL 后, 方法中止导航，以便 web 视图不会尝试完成导航到自定义 URL。

#### <a name="manipulating-the-template-from-c"></a>操作从 C# 模板

从 C# 与呈现的 HTML web 视图通信可通过调用 web 视图中的 Javascript。 在 iOS 上这通过调用`EvaluateJavascript`UIWebView 上：

```csharp
webView.EvaluateJavascript (js);
```

在 Android 上，Javascript 可在 web 视图中由调用加载为 URL 使用 Javascript `"javascript:"` URL 方案：

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>使应用真正的混合

这些模板不能使用的每个平台上的本机控件 – 使用单一 web 视图填充整个屏幕。

HTML 可以大幅提高原型制作的显示几种 web 最好在例如多格式文本和响应式布局。 例如，但是并非所有任务都适用于 HTML 和 Javascript – 滚动浏览的数据，较长的列表执行更好地在 Android 上使用如 (UITableView 在 iOS 上的) 或 ListView 的本机 UI 控件。

在模板中的 web 视图可以轻松地扩充与特定于平台的控件 – 只需编辑**MainStoryboard.storyboard** iOS 设计器中或**Resources/layout/Main.axml**在 Android 上。

### <a name="razortodo-sample"></a>RazorTodo 示例

[RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)储存库中包含两个不同的解决方案，以显示完全 HTML 驱动的应用程序和 HTML 结合本机控件的应用之间的差异：

-  **RazorTodo** -使用 Razor 模板的完全 HTML 驱动的应用程序。
-  **RazorNativeTodo** -用于 iOS 和 Android 的本机列表视图控件，但显示使用 HTML 和 Razor 编辑屏幕。


这些 Xamarin 应用在 iOS 和 Android，利用可移植类库 (Pcl) 共享公共代码，如数据库和模型类上运行。 Razor **.cshtml**模板也可以包含在 PCL 中以便轻松地跨平台共享。

这两个示例应用合并 Twitter 共享和演示，使用 Xamarin 混合应用程序仍可以访问的基本功能的所有从 HTML Razor 模板驱动视图的本机平台的文本到语音转换 Api。

**RazorTodo**的应用程序将 HTML Razor 模板用于列表和编辑视图。 这意味着我们可以生成的应用程序几乎完全中共享的可移植类库 (包括数据库和**.cshtml** Razor 模板)。 下面的屏幕截图显示 iOS 和 Android 应用。

 ![RazorTodo](images/Both_700x290.png)

**RazorNativeTodo**应用 HTML Razor 模板用于编辑视图中，但每个平台上实现的本机的滚动列表。 这提供了许多优点包括：

-  性能-本机滚动控件使用虚拟化以确保实现快速、 平稳滚动即使使用的数据很长的列表。
-  本机的体验的特定于平台的 UI 元素将轻松启用，如 iOS 和 Android 中的快速滚动索引支持。


生成使用 Xamarin 混合应用程序的主要优点是，你可以使用完全 HTML 驱动的用户界面 （如第一个示例） 开始，然后添加特定于平台的功能时需要 （如第二个示例所示）。 本机列表屏幕和 HTML Razor 编辑屏幕上两个 iOS 和 Android 如下所示。

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>总结

本文讲述了一些可用的 web 视图控件的功能在 iOS 和 Android 中有助于构建混合应用程序。

它然后讨论 Razor 模板化引擎以及可用于轻松地生成 HTML Xamarin 应用程序使用的语法。**cshtml** Razor 模板文件。 它还描述了为使您能够快速的解决方案模板要开始构建混合应用程序使用 Xamarin 的 Mac 的 Visual Studio。

最后，它引入 RazorTodo 示例，演示如何组合使用本机用户界面和 Api 的 web 视图。

### <a name="related-links"></a>相关链接

- [RazorTodo 示例](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3-Razor 视图引擎 (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [使用 Razor 语法 (Microsoft) 的 ASP.NET Web 编程简介](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)