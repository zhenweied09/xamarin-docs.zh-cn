---
title: "使用标识提供程序的用户进行身份验证"
description: "Xamarin.Auth 是跨平台 SDK 进行身份验证用户以及存储的帐户。 它包括提供对使用如 Google、 Microsoft、 Facebook 和 Twitter 标识提供程序支持的 OAuth 身份验证器。 此文章介绍了如何使用 Xamarin.Auth 管理 Xamarin.Forms 应用程序中的身份验证过程。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2017
ms.openlocfilehash: ff0403fedf75ab22986f5fc83d16db3dbf8d92b6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="authenticating-users-with-an-identity-provider"></a>使用标识提供程序的用户进行身份验证

_Xamarin.Auth 是跨平台 SDK 进行身份验证用户以及存储的帐户。它包括提供对使用如 Google、 Microsoft、 Facebook 和 Twitter 标识提供程序支持的 OAuth 身份验证器。此文章介绍了如何使用 Xamarin.Auth 管理 Xamarin.Forms 应用程序中的身份验证过程。_

OAuth 开放标准身份验证，并允许以通知资源提供程序应授予权限给第三方不共享的资源所有者标识的情况下访问其信息的资源所有者。 此示例将使用户能够通知权限应授予应用程序访问其数据，而不用共享用户的标识的标识提供程序 （如 Google、 Microsoft、 Facebook 或 Twitter）。 它通常用于作为一种方法为用户在登录网站和应用程序使用标识提供程序，但不公开其密码的网站或应用程序。

在使用 OAuth 标识提供程序的身份验证流的高级概述如下所示：

1. 应用程序导航到标识提供程序 URL 的浏览器。
1. 标识提供程序处理用户身份验证和授权代码返回到应用程序。
1. 应用程序与其交换从标识提供程序的访问令牌的授权代码。
1. 应用程序使用访问令牌访问 Api 的标识提供程序，例如一个 API，使请求基本用户数据。

示例应用程序演示如何使用 Xamarin.Auth 实现针对 Google 本机身份验证流。 虽然 Google 用作本主题中的标识提供程序，方法是同样适用于其他标识提供程序。 有关使用 Google 的 OAuth 2.0 终结点的身份验证的详细信息，请参阅[对访问 Google Api 使用 OAuth2.0](https://developers.google.com/identity/protocols/OAuth2) Google 网站上。

> [!NOTE]
> 在 iOS 9 及更高版本中，应用程序传输安全 (ATS) 强制实施安全连接之间 internet 资源 （如应用程序的后端服务器） 和应用程序，从而防止意外泄露的敏感信息。 由于默认情况下，生成的 ios 9 应用中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，则会失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议和安全的 internet 资源的通信。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="using-xamarinauth-to-authenticate-users"></a>使用 Xamarin.Auth 用户进行身份验证

Xamarin.Auth 支持两种方法应用程序与标识提供程序的授权终结点进行交互：

1. 使用嵌入式的 web 视图。 虽然这是常见的做法，不再建议原因如下：

    - 承载 web 视图的应用程序可以访问用户的完整的身份验证凭据，而不仅仅是针对应用程序的 OAuth 授权授予。 这违反了最低特权原则，如应用程序有权访问更强大的凭据不是它需要有可能增加应用程序的受攻击面。
    - 主机应用程序能捕获用户名和密码、 自动提交窗体和绕过用户同意，和复制会话 cookie 并将其用于以用户身份执行已经过身份验证的操作。
    - 嵌入的 web 视图不要与其他应用程序或设备的 web 浏览器，需要用户登录为每个授权请求被认为是较差的用户体验共享身份验证状态。
    - 某些授权终结点采取措施来检测和阻止来自 web 视图的授权请求。

1. 使用设备的 web 浏览器中，这是建议的方法。 OAuth 请求，使用设备浏览器可以提高应用程序的可用性，因为用户仅需要登录到标识提供程序一次每个设备，提高应用程序中的登录和授权流的转换率。 设备浏览器还提供了改进的安全性，如应用程序能够检查和修改在 web 视图中中的内容，但不是在浏览器中显示的内容。 这是用此文章和示例应用程序中的方法。

在下图中显示的示例应用程序如何使用 Xamarin.Auth 用户进行身份验证和检索其基本数据的高级概述：

![](oauth-images/google-auth.png "使用 Xamarin.Auth 进行 Google 身份验证")

应用程序发出对 Google 使用的身份验证请求`OAuth2Authenticator`类。 返回一个身份验证响应，用户已成功通过身份验证后与 Google 通过其在登录页面，其中包括访问令牌。 然后，应用程序提出的请求到 Google 基本用户数据，使用`OAuth2Request`类，使用包含在请求中的访问令牌。

### <a name="setup"></a>安装

必须创建一个 Google API 控制台项目来与 Xamarin.Forms 应用程序集成 Google 登录。 这可以通过以下操作实现：

1. 转到[Google API 控制台](http://console.developers.google.com)网站，并使用 Google 帐户凭据登录。
1. 从项目下拉列表，选择一个现有项目，或者创建一个新。
1. 在下"API 管理器"栏中，选择**凭据**，然后选择**OAuth 许可屏幕选项卡**。选择**电子邮件地址**，指定**向用户显示的产品名称**，按**保存**。
1. 在**凭据**选项卡上，选择**创建凭据**下拉列表，并选择**OAuth 客户端 ID**。
1. 下**应用程序类型**，选择将在运行移动应用的平台 (**iOS**或**Android**)。
1. 填写必需的详细信息，并选择**创建**按钮。

> [!NOTE]
> 客户端 ID 可以让应用程序访问启用的 Google Api，并且对于移动应用程序特有单个平台。 因此， **OAuth 客户端 ID**应为将使用 Google 登录每个平台创建。

执行这些步骤后，Xamarin.Auth 可以用于启动与 Google 的 OAuth2 身份验证流。

### <a name="creating-and-configuring-an-authenticator"></a>创建和配置身份验证器

Xamarin.Auth 的`OAuth2Authenticator`类负责处理的 OAuth 身份验证流。 下面的代码示例演示的实例化`OAuth2Authenticator`类执行身份验证使用设备的 web 浏览器时：

```csharp
var authenticator = new OAuth2Authenticator(
    clientId,
    null,
    Constants.Scope,
    new Uri(Constants.AuthorizeUrl),
    new Uri(redirectUri),
    new Uri(Constants.AccessTokenUrl),
    null,
    true);
```

`OAuth2Authenticator`类需要大量的参数，如下所示：

- **客户端 ID** – 这标识正在发出请求，并且可以检索从项目中的客户端[Google API 控制台](http://console.developers.google.com)。
- **客户端机密**– 这应是`null`或`string.Empty`。
- **作用域**– 它可标识该应用程序，正在请求的 API 访问权限和值通知向用户显示同意屏幕。 有关作用域的详细信息，请参阅[授权 API 请求](https://developers.google.com/+/web/api/rest/oauth)Google 网站上。
- **授权 URL** – 此标识通过授权代码的 URL。
- **重定向 URL** – 这标识响应将发送到的位置的 URL。 此参数的值必须与一种将出现在值**凭据**中项目的选项卡[Google 开发人员控制台](https://console.developers.google.com/)。
- **AccessToken Url** – 这标识用于获取授权代码后请求访问令牌的 URL。
- **GetUserNameAsync Func** – 一个可选`Func`将用于后它已成功通过身份验证以异步方式检索帐户的用户名。
- **使用本机 UI** –`boolean`值，该值指示是否使用设备的 web 浏览器来执行身份验证请求。

### <a name="setup-authentication-event-handlers"></a>设置身份验证事件处理程序

提供用户界面的事件处理程序之前`OAuth2Authenticator.Completed`必须注册事件，如下面的代码示例中所示：

```csharp
authenticator.Completed += OnAuthCompleted;
```

在用户成功进行身份验证或取消在登录时，将激发此事件。

（可选） 的事件处理程序`OAuth2Authenticator.Error`还注册的事件。

### <a name="presenting-the-sign-in-user-interface"></a>提供在登录用户界面

通过使用必须在每个平台项目中初始化 Xamarin.Auth 登录演示器，可以向用户显示在登录用户界面。 下面的代码示例演示如何初始化中的登录演示器`AppDelegate`的 iOS 项目中的类：

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

下面的代码示例演示如何初始化中的登录演示器`MainActivity`Android 项目中的类：

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

可移植类库 (PCL) 项目然后，可以调用登录演示程序，如下所示：

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

请注意，自变量`Xamarin.Auth.Presenters.OAuthLoginPresenter.Login`方法是`OAuth2Authenticator`实例。 当`Login`调用方法，在登录用户界面向选项卡中的用户显示从设备的 web 浏览器中，以下屏幕截图中所示：

![](oauth-images/login.png "Google 登录")

### <a name="processing-the-redirect-url"></a>处理重定向 URL

用户完成身份验证过程后，控件将从 web 浏览器选项卡，返回到应用程序。这被通过从身份验证过程中，然后检测并处理自定义的 URL，它发送后返回的重定向 URL 注册自定义的 URL 方案。

在选择用于将与应用程序相关联的自定义 URL 架构时，应用程序必须使用基于其控制下的名称的方案。 这可以通过在 iOS 和 Android 上的包名称中使用的捆绑标识符名称，然后反转它们以方便 URL 方案来实现。 但是，某些标识提供程序，如 Google、 分配基于域的名称，然后再反转并用作 URL 方案的客户端标识符。 例如，如果 Google 创建的客户端 id `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`，URL 方案将`com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`。 请注意，只有单个`/`后方案组件可以显示。 因此，重定向 URL 利用自定义的 URL 方案的完整示例是`com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`。

当 web 浏览器收到响应时从标识提供程序，它包含自定义的 URL 方案时，它将尝试加载的 URL，将会失败。 相反，自定义的 URL 方案向操作系统报告的引发事件。 对于已注册的方案，然后检查操作系统和操作系统如果找到一个对象，将启动的应用程序注册方案，并将其发送重定向 URL。

向操作系统注册自定义的 URL 方案和处理方案的机制是特定于每个平台。

#### <a name="ios"></a>iOS

在 iOS 中，自定义的 URL 方案注册中**Info.plist**，如以下屏幕截图中所示：

![](oauth-images/info-plist.png "URL 方案注册")

**标识符**值可以是任何内容，与**角色**值必须设置为**查看器**。 **Url 方案**值，该值开头`com.googleusercontent.apps`，可以在从项目的 iOS 客户端 id 获得[Google API 控制台](http://console.developers.google.com)。

标识提供程序完成授权请求，它将重定向到应用程序的重定向 URL。 由于 URL 使用它会导致启动应用程序的 iOS 自定义方案，为启动参数，它由处理传递 URL 中`OpenUrl`在应用程序的重写`AppDelegate`类，该类中下面的代码示例所示：

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    // Convert NSUrl to Uri
    var uri = new Uri(url.AbsoluteString);

    // Load redirectUrl page
    AuthenticationState.Authenticator.OnPageLoading(uri);

    return true;
}
```

`OpenUrl`方法将从接收的 URL 为`NSUrl`到.NET `Uri`，在处理与重定向 URL 之前`OnPageLoading`的公共方法`OAuth2Authenticator`对象。 这将导致 Xamarin.Auth 以关闭 web 浏览器选项卡，并将收到的 OAuth 数据分析。

#### <a name="android"></a>Android

在 Android 上，自定义的 URL 方案注册通过指定[ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)属性`Activity`将处理方案。 标识提供程序完成授权请求，它将重定向到应用程序的重定向 URL。 随着 URL 使用它会导致启动应用程序的 Android 自定义方案，为启动参数，它由处理传递 URL 中`OnCreate`方法`Activity`注册用于处理自定义的 URL 方案。 下面的代码示例演示的示例应用程序处理的自定义的 URL 方案中的类：

```csharp
[Activity(Label = "CustomUrlSchemeInterceptorActivity", NoHistory = true, LaunchMode = LaunchMode.SingleTop )]
[IntentFilter(
    new[] { Intent.ActionView },
    Categories = new [] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataSchemes = new [] { "<insert custom URL here>" },
    DataPath = "/oauth2redirect")]
public class CustomUrlSchemeInterceptorActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        // Convert Android.Net.Url to Uri
        var uri = new Uri(Intent.Data.ToString());

        // Load redirectUrl page
        AuthenticationState.Authenticator.OnPageLoading(uri);

        Finish();
    }
}
```

`DataSchemes`属性[ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)必须设置为反向客户端标识符上时从项目的 Android 客户端 id 获取[Google API 控制台](http://console.developers.google.com)。

`OnCreate`方法将从接收的 URL 为`Android.Net.Url`到.NET `Uri`，在处理与重定向 URL 之前`OnPageLoading`的公共方法`OAuth2Authenticator`对象。 这将导致 Xamarin.Auth 关闭 web 浏览器选项卡上，并将收到的 OAuth 数据分析。

> [!IMPORTANT]
> 在 Android 上，使用 Xamarin.Auth `CustomTabs` API 与 web 浏览器和操作系统进行通信。 但是，它不能保证，`CustomTabs`兼容的浏览器将安装在用户的设备。

### <a name="examining-the-oauth-response"></a>检查 OAuth 响应

在分析过程中收到的 OAuth 数据之后, Xamarin.Auth 将引发`OAuth2Authenticator.Completed`事件。 此事件的事件处理中`AuthenticatorCompletedEventArgs.IsAuthenticated`属性可以用于确定是否将身份验证成功，如下面的代码示例中所示：

```csharp
async void OnAuthCompleted(object sender, AuthenticatorCompletedEventArgs e)
{
  ...
  if (e.IsAuthenticated)
  {
    ...
  }
}
```

收集成功的身份验证的数据位于`AuthenticatorCompletedEventArgs.Account`属性。 这包括访问令牌，用于对请求的数据传输到标识提供程序提供的 API 进行签名。

### <a name="making-requests-for-data"></a>发出请求的数据

应用程序获取访问令牌后，它用于发出请求到`https://www.googleapis.com/oauth2/v2/userinfo`API，对从标识提供程序的请求基本用户数据。 此请求与 Xamarin.Auth 的`OAuth2Request`类，该类表示使用从检索到的帐户进行身份验证的请求`OAuth2Authenticator`实例，如下面的代码示例中所示：

```csharp
// UserInfoUrl = https://www.googleapis.com/oauth2/v2/userinfo
var request = new OAuth2Request ("GET", new Uri (Constants.UserInfoUrl), null, e.Account);
var response = await request.GetResponseAsync ();
if (response != null)
{
  string userJson = response.GetResponseText ();
  var user = JsonConvert.DeserializeObject<User> (userJson);
}
```

HTTP 方法和 API URL，以及`OAuth2Request`实例还指定`Account`实例，其中包含对指定的 URL 的请求进行签名的访问令牌`Constants.UserInfoUrl`属性。 然后，标识提供程序将基本的用户数据返回为 JSON 响应，包括用户的名称和电子邮件地址，前提它识别为有效的访问令牌。 JSON 响应然后读取和反序列化为`user`变量。

有关详细信息，请参阅[调用 Google API](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) Google 开发人员门户上。

### <a name="storing-and-retrieving-account-information-on-devices"></a>存储和检索设备上的帐户信息

安全地存储 Xamarin.Auth`Account`帐户中的对象存储，以便应用程序并不总是有重新进行身份验证用户。 `AccountStore`类负责存储帐户信息，并由 Keychain 服务在 iOS 中，与`KeyStore`Android 中的类。

下面的代码示例演示如何`Account`安全地保存对象：

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

已保存的帐户唯一标识使用该帐户的所组成的密钥`Username`属性和服务 ID，即属性提取帐户时可以从使用的帐户存储的字符串。 如果`Account`以前保存，调用`Save`方法再次将覆盖它。

`Account` 对象的服务可通过调用检索`FindAccountsForService`方法，如下面的代码示例中所示：

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

`FindAccountsForService`方法返回`IEnumerable`集合`Account`对象，其中的集合设置为匹配的帐户中的第一项。

## <a name="summary"></a>摘要

本文介绍如何使用 Xamarin.Auth 管理 Xamarin.Forms 应用程序中的身份验证过程。 Xamarin.Auth 提供`OAuth2Authenticator`和`OAuth2Request`Xamarin.Forms 应用程序用于使用标识提供程序如 Google、 Microsoft、 Facebook 和 Twitter 的类。


## <a name="related-links"></a>相关链接

- [OAuthNativeFlow （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/OAuthNativeFlow/)
- [对于本机应用程序的 OAuth 2.0](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [使用 OAuth2.0 访问 Google Api](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin.Auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin.Auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
