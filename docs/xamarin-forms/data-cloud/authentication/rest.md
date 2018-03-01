---
title: "对 RESTful Web 服务进行身份验证"
description: "HTTP 支持使用多个身份验证机制来控制对资源的访问。 基本身份验证提供对资源的访问权限仅这些客户端具有正确的凭据。 本文演示如何使用基本身份验证来保护对 RESTful web 服务资源的访问。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 7aea74f95e8738cc415eaac3a5ac4f86b069d0f7
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="authenticating-a-restful-web-service"></a>对 RESTful Web 服务进行身份验证

_HTTP 支持使用多个身份验证机制来控制对资源的访问。基本身份验证提供对资源的访问权限仅这些客户端具有正确的凭据。本文演示如何使用基本身份验证来保护对 RESTful web 服务资源的访问。_

随附的 Xamarin.Forms 示例应用程序需要使用提供对 web 服务的只读访问权限的 Xamarin 托管 REST 服务。 因此，创建、 更新和删除数据的操作不会更改应用程序中使用的数据。 但是，REST 服务的可承载版本位于*TodoRESTService*可以那里找到文件夹中的示例应用程序和服务设置的说明。 此可承载版本的 REST 服务提供完整创建、 更新、 读取和删除访问的数据。

> [!NOTE]
> 在 iOS 9 及更高版本中，应用程序传输安全 (ATS) 强制实施安全连接之间 internet 资源 （如应用程序的后端服务器） 和应用程序，从而防止意外泄露的敏感信息。 由于默认情况下，生成的 ios 9 应用中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，则会失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议和安全的 internet 资源的通信。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="authenticating-users-over-http"></a>用户通过 HTTP 进行身份验证

基本身份验证是 HTTP，支持的最简单的身份验证机制，并且涉及在客户端发送的用户名和密码以未加密的 base64 编码文本。 其工作原理，如下所示：

- 如果 web 服务收到为受保护资源请求时，它将拒绝该请求，HTTP 状态代码为 401 （拒绝访问），并设置的 WWW 身份验证响应标头，如下面的关系图中所示：

![](rest-images/basic-authentication-fail.png "基本身份验证失败")

- 如果 web 服务收到受保护资源的请求有`Authorization`正确设置标头，web 服务进行响应使用 HTTP 状态代码 200，这表示请求成功和请求的信息包含在响应中。 这种情况下下图所示：

![](rest-images/basic-authentication-success.png "基本身份验证成功")

> [!NOTE]
> 基本身份验证应仅用于通过 HTTPS 连接。 当通过 HTTP 连接，使用<code>Authorization</code>可以轻松地解码标头，如果攻击者捕获的 HTTP 流量。

## <a name="specifying-basic-authentication-in-a-web-request"></a>在 Web 请求中指定基本身份验证

使用基本身份验证，如下所示指定：

1. 添加到"基本"的字符串`Authorization`的请求标头。
1. 用户名和密码将合并为一个字符串，"用户名： 密码"，然后使用 base64 编码，并且添加到它的格式`Authorization`的请求标头。

因此，利用 XamarinUser 用户名和的 XamarinPassword 密码中，标头将成为：

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

`HttpClient`该类可以设置`Authorization`上的标头值`HttpClient.DefaultRequestHeaders.Authorization`属性。 因为`HttpClient`实例存在跨多个请求，`Authorization`标头仅需要一次，而不是时设置使每个请求，如下面的代码示例中所示：

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    var authData = string.Format ("{0}:{1}", Constants.Username, Constants.Password);
    var authHeaderValue = Convert.ToBase64String (Encoding.UTF8.GetBytes (authData));

    client = new HttpClient ();
    ...
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue ("Basic", authHeaderValue);
  }
  ...
}
```

然后当请求时，web 服务操作使用签名请求`Authorization`标头，，该值指示用户是否有权调用该操作。

> [!NOTE]
> 虽然示例 REST 服务将凭据存储为常量，它们应不会在已发布的应用程序中不安全格式存储。 [Xamarith.Auth](https://www.nuget.org/packages/Xamarin.Auth/) NuGet 提供用于安全地存储凭据的功能。 有关详细信息请参阅[存储和检索设备上的帐户信息](~/xamarin-forms/data-cloud/authentication/oauth.md)。


## <a name="processing-the-authorization-header-server-side"></a>处理授权标头服务器端

随附的示例 REST 服务修饰与每个操作`[BasicAuthentication]`属性。 此属性由`BasicAuthenticationAttribute`类解决方案中、 并用于分析`Authorization`标头，并确定 base64 编码凭据是否通过将它们与存储的值进行比较有效*Web.config*.虽然这种方法适合于此示例服务，但它需要的面向公众的 web 服务扩展。

在基本身份验证模块中使用的 IIS，用户进行身份验证对其 Windows 凭据。 因此，用户必须具有在服务器的域帐户。 但是，可以配置基本身份验证模型，以允许自定义身份验证，其中用户帐户进行身份验证对外部源，如数据库。 有关详细信息请参阅[ASP.NET Web API 中的基本身份验证](http://www.asp.net/web-api/overview/security/basic-authentication)ASP.NET 网站上。

> [!NOTE]
> 基本身份验证不适合管理登出。因此，注销的标准的基本身份验证方法是以结束会话。

## <a name="summary"></a>摘要

这篇文章演示了如何将基本身份验证添加到发出的 Xamarin.Forms 应用程序使用的 web 请求`HttpClient`类。 基本身份验证提供对资源的访问权限仅这些客户端具有正确的凭据。 有关如何使用信息[Xamarin.Auth](https://www.nuget.org/packages/Xamarin.Auth/)来管理在 Xamarin.Forms 应用程序的身份验证过程，以便用户可以共享一个后端，同时仅有权访问其数据，请参阅[进行身份验证的用户使用标识提供程序](~/xamarin-forms/data-cloud/authentication/oauth.md)。


## <a name="related-links"></a>相关链接

- [TodoREST （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [使用 rest 样式 web 服务](~/xamarin-forms/data-cloud/consuming/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
