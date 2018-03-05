---
title: "访问 Graph API"
description: "使用 Active Directory 查询 Graph API 使用 Xamarin"
ms.topic: article
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 2b0e4a9466ab59ec27b957bf284a7d3895f6a4fc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="accessing-the-graph-api"></a>访问 Graph API

_使用 Active Directory 查询 Graph API 使用 Xamarin_

按照这些步骤，使用 Graph API 在 Xamarin 应用程序中：

1. [注册到 Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md)上*windowsazure.com*门户，然后
2. [配置服务](~/cross-platform/data-cloud/active-directory/get-started/configure.md)。

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>步骤 3。 将 Active Directory 身份验证添加到应用程序

在你的应用程序添加到的引用**Azure Active Directory 身份验证库 (Azure ADAL)** Visual Studio 或 Visual Studio 中的 NuGet 包管理器用于 mac。
请确保选择**显示预发行包**以包括此包，因为它仍处于预览阶段。

> [!IMPORTANT]
> 注意： Azure ADAL 3.0 目前预览和之前发布的最终版本可能是重大更改。 


![](graph-images/06.-adal-nuget-package.jpg "添加对 Azure Active Directory 身份验证库 (Azure ADAL) 的引用")

你的应用程序，现在需要添加以下类级变量所需的身份验证流。

```csharp
//Client ID
public static string clientId = "25927d3c-.....-63f2304b90de";
public static string commonAuthority = "https://login.windows.net/common"
//Redirect URI
public static Uri returnUri = new Uri("http://xam-demo-redirect");
//Graph URI if you've given permission to Azure Active Directory
const string graphResourceUri = "https://graph.windows.net";
public static string graphApiVersion = "2013-11-08";
//AuthenticationResult will hold the result after authentication completes
AuthenticationResult authResult = null;
```

一个需要注意的事项还有`commonAuthority`。 身份验证终结点时`common`，应用程序变得**多租户**，这意味着任何用户可以使用登录名使用其 Active Directory 凭据。 身份验证后，该用户将在他们自己的 Active Directory 的上下文上工作-即，他们将看到其 Active Directory 与相关的详细信息。

### <a name="write-method-to-acquire-access-token"></a>编写方法以获取访问令牌

（对于 Android) 下面的代码将启动身份验证并完成后将分配中的结果`authResult`。 IOS 和 Windows Phone 实现略有不同： 第二个参数 (`Activity`) 在 iOS 上有不同，且不在 Windows Phone 上。

```csharp
public static async Task<AuthenticationResult> GetAccessToken
            (string serviceResourceId, Activity activity)
{
    authContext = new AuthenticationContext(Authority);
    if (authContext.TokenCache.ReadItems().Count() > 0)
        authContext = new AuthenticationContext(authContext.TokenCache.ReadItems().First().Authority);
    var authResult = await authContext.AcquireTokenAsync(serviceResourceId, clientId, returnUri, new AuthorizationParameters(activity));
    return authResult;
}  
```

在上面的代码中，`AuthenticationContext`负责使用 commonAuthority 进行身份验证。 它具有`AcquireTokenAsync`方法，将参数作为需要访问，在这种情况下的资源`graphResourceUri`， `clientId`，和`returnUri`。 应用程序将返回到`returnUri`身份验证完成时。 此代码将保持不变，则对于所有平台，但是，最后一个参数， `AuthorizationParameters`，将会在不同平台上有所不同，并且负责管理身份验证流。

对于 Android 或 iOS，我们传递`this`参数`AuthorizationParameters(this)`以共享上下文，而在 Windows 中它将不带任何参数作为传递新`AuthorizationParameters()`。

### <a name="handle-continuation-for-android"></a>适用于 Android 的句柄延续

身份验证完成后，流应返回到该应用。 对于 Android 下面的代码处理此，应将其添加到**MainActivity.cs**:


```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);

    
}
```

### <a name="handle-continuation-for-windows-phone"></a>Windows Phone 的句柄延续

对于 Windows Phone 修改`OnActivated`中的方法**App.xaml.cs**文件与下面的代码：

```csharp
protected override void OnActivated(IActivatedEventArgs args)
{
#if WINDOWS_PHONE_APP
  if (args is IWebAuthenticationBrokerContinuationEventArgs)
  {
     WebAuthenticationBrokerContinuationHelper.SetWebAuthenticationBrokerContinuationEventArgs(args as IWebAuthenticationBrokerContinuationEventArgs);
  }
#endif
  base.OnActivated(args);
}
```

现在如果你运行应用程序，你将看到身份验证对话框。
成功进行身份验证，它将要求您用于访问 （在我们的示例 Graph API） 的资源的权限：

![](graph-images/08.-authentication-flow.jpg "成功进行身份验证，它将要求你访问我们的示例 Graph API 中的资源的权限")

如果身份验证会成功，并且您已授权应用程序访问资源，你应获得`AccessToken`和`RefreshToken`中的组合`authResult`。 这些令牌是进一步 API 调用和与 Azure Active Directory 在幕后的授权需要。

![](graph-images/07.-access-token-for-authentication.jpg "这些标记所需进一步 API 调用和使用 Azure Active Directory 在后台进行授权")

例如，下面的代码，可从 Active Directory 中获取用户列表。 你可以替换你的 Web API 由 Azure AD 保护 Web API URL。

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```

