---
title: 身份验证和授权
description: 本章介绍 eShopOnContainers 移动应用程序如何执行身份验证和授权对容器化微服务。
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: beb9e8f351a1cecc6017a08345f7cfc5e207ba35
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996212"
---
# <a name="authentication-and-authorization"></a>身份验证和授权

身份验证是从用户获取标识名称和密码等凭据并将根据证书颁发机构验证这些凭据的过程。 如果凭据有效，提交凭据的实体被视为已验证的身份。 一旦标识已经过身份验证，授权过程确定该标识是否有权访问给定的资源。

有许多种集成到 Xamarin.Forms 应用 ASP.NET MVC web 应用程序，包括使用 ASP.NET Core 标识时，例如 Microsoft、 Google、 外部身份验证提供程序与之通信的身份验证和授权Facebook 或 Twitter 和身份验证中间件。 EShopOnContainers 移动应用执行身份验证和授权与使用 IdentityServer 4 的容器化的标识微服务。 对用户进行身份验证或用于访问资源，移动应用从 IdentityServer，请求安全令牌。 为代表用户颁发令牌的 IdentityServer，用户必须登录到 IdentityServer。 但是，IdentityServer 不提供用户界面或数据库进行身份验证。 因此，在 eShopOnContainers 引用应用程序中，ASP.NET Core 标识用于此目的。

## <a name="authentication"></a>身份验证

应用程序需要知道的当前用户标识时，身份验证是必需的。 ASP.NET Core 用于标识用户的主要机制是 ASP.NET Core 标识成员身份系统，它在由开发人员配置了数据存储中存储用户的信息。 通常情况下，此数据存储区将 EntityFramework 的存储，但自定义存储或第三方包可用于将标识信息存储在 Azure 存储、 Azure Cosmos DB 或其他位置。

有关身份验证方案，请使用本地用户数据存储区，并且保留之间通过 cookie （就是典型 ASP.NET MVC web 应用程序中） 发出的请求的标识信息，则 ASP.NET Core 标识为合适的解决方案。 但是，cookie 不始终是保留和传输数据的自然方式。 例如，ASP.NET Core web 应用程序公开从移动应用程序访问的 RESTful 终结点通常需要使用持有者令牌身份验证，因为无法在此方案中使用 cookie。 但是，持有者令牌可以轻松地检索和发出的移动应用中的 web 请求的 authorization 标头中包含。

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>颁发的持有者令牌使用 IdentityServer 4

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4)有关 ASP.NET Core，可用于许多的身份验证和授权方案，包括颁发本地 ASP.NET Core 标识用户的安全令牌是一种开放源代码 OpenID Connect 和 OAuth 2.0 框架。

> [!NOTE]
> OpenID Connect 和 OAuth 2.0 则是非常相似，具有不同的责任。

OpenID Connect 是在 OAuth 2.0 协议之上的身份验证层。 OAuth 2 是一种协议，允许应用程序从安全令牌服务请求的访问令牌并使用它们来进行通信的 Api。 由于可以集中式身份验证和授权，这种委托可以减少在客户端应用程序和 Api 的复杂性。

OpenID Connect 和 OAuth 2.0 的组合合并的身份验证和 API 访问权限的两个基本的安全问题和 IdentityServer 4 是这些协议的实现。

使用直接的客户端到微服务通信，如 eShopOnContainers 引用应用程序，应用程序中操作作为安全令牌服务 (STS) 的专用身份验证微服务可用于验证用户身份，如图中所示9-1。 有关客户端到微服务直接通信的详细信息，请参阅[客户端之间的通信和微服务](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices)。

![](authentication-and-authorization-images/authentication.png "专用身份验证微服务进行身份验证")

**图 9-1:** 专用身份验证微服务进行身份验证

EShopOnContainers 移动应用与标识微服务，它使用 IdentityServer 4 执行身份验证，并为 Api 的访问控制进行通信。 因此，移动应用程序请求令牌从 IdentityServer，对用户进行身份验证或用于访问资源：

-   IdentityServer 具有的用户进行身份验证通过移动应用程序请求来实现*标识*令牌表示身份验证过程的结果。 至少，它包含的用户和有关如何以及何时对用户身份验证信息的标识符。 它还可以包含附加的标识数据。
-   访问有 IdentityServer 的资源通过移动应用程序请求来实现*访问*令牌中，可对 API 资源访问权限。 客户端请求访问令牌，并将其转发到 API。 访问令牌包含有关客户端和用户信息 （如果存在）。 Api 然后使用该信息来授予对其数据的访问权限。

> [!NOTE]
> 它可以请求令牌之前，必须向 IdentityServer 注册客户端。

### <a name="adding-identityserver-to-a-web-application"></a>IdentityServer 添加到 Web 应用程序

为了使 ASP.NET Core web 应用程序使用 IdentityServer 4，必须将添加到 web 应用程序的 Visual Studio 解决方案。 有关详细信息，请参阅[安装程序和概述](https://identityserver4.readthedocs.io/en/release/quickstarts/0_overview.html)IdentityServer 文档中。

一旦 IdentityServer 包含在 web 应用程序的 Visual Studio 解决方案，它必须添加到 web 应用程序的 HTTP 请求处理管道，这是，以便它可以处理到 OpenID Connect 和 OAuth 2.0 终结点的请求。 这实现`Configure`中 web 应用程序的方法`Startup`类，如以下代码示例所示：

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

Web 应用程序的 HTTP 请求处理管道中，顺序非常重要。 因此，IdentityServer 必须添加到 UI 框架实现登录屏幕之前管道。

### <a name="configuring-identityserver"></a>配置 IdentityServer

应在配置 IdentityServer`ConfigureServices`中 web 应用程序的方法`Startup`类通过调用`services.AddIdentityServer`方法，如在 eShopOnContainers 引用应用程序从下面的代码示例所示：

```csharp
public void ConfigureServices(IServiceCollection services)  
{  
    ...  
    services.AddIdentityServer(x => x.IssuerUri = "null")  
        .AddSigningCredential(Certificate.Get())                 
        .AddAspNetIdentity<ApplicationUser>()  
        .AddConfigurationStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .AddOperationalStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .Services.AddTransient<IProfileService, ProfileService>();  
}
```

在调用`services.AddIdentityServer`方法中，新增的 fluent Api 调用来配置以下各项：

-   用于签名的凭据。
-   API 和标识资源的用户可能会请求访问权限。
-   将连接到请求令牌的客户端。
-   ASP.NET Core 标识。

>💡 **提示**： 动态加载 IdentityServer 4 配置。 IdentityServer 4 Api，可以从内存中列表的配置对象配置 IdentityServer。 在 eShopOnContainers 引用应用程序，这些内存中集合是硬编码到应用程序。 但是，在生产方案中它们可以是加载动态配置文件中或从数据库。

有关配置 IdentityServer 使用 ASP.NET Core 标识的信息，请参阅[使用 ASP.NET Core 标识](https://identityserver4.readthedocs.io/en/release/quickstarts/6_aspnet_identity.html)IdentityServer 文档中。

#### <a name="configuring-api-resources"></a>配置 API 资源

配置 API 资源时`AddInMemoryApiResources`方法需要`IEnumerable<ApiResource>`集合。 下面的代码示例演示`GetApis`方法提供了此集合，在 eShopOnContainers 引用应用程序：

```csharp
public static IEnumerable<ApiResource> GetApis()  
{  
    return new List<ApiResource>  
    {  
        new ApiResource("orders", "Orders Service"),  
        new ApiResource("basket", "Basket Service")  
    };  
}
```

此方法指定 IdentityServer 应保护订单和购物篮 Api。 因此，IdentityServer 托管访问这些 Api 调用时，将需要令牌。 有关详细信息`ApiResource`类型，请参阅[API 资源](https://identityserver4.readthedocs.io/en/release/reference/api_resource.html#refapiresource)IdentityServer 4 文档中。

#### <a name="configuring-identity-resources"></a>配置标识资源

配置标识资源时`AddInMemoryIdentityResources`方法需要`IEnumerable<IdentityResource>`集合。 标识资源是数据，例如用户 ID、 名称或电子邮件地址。 每个标识资源都有唯一的名称，并且任意声明类型可以分配给它，然后将包含在用户的标识令牌中。 下面的代码示例演示`GetResources`方法提供了此集合，在 eShopOnContainers 引用应用程序：

```csharp
public static IEnumerable<IdentityResource> GetResources()  
{  
    return new List<IdentityResource>  
    {  
        new IdentityResources.OpenId(),  
        new IdentityResources.Profile()  
    };  
}
```

OpenID Connect 规范指定一些[标准标识资源](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims)。 最低要求是用于发出的唯一 ID 的用户提供支持。 这通过公开`IdentityResources.OpenId`标识资源。

> [!NOTE]
> `IdentityResources`类支持所有 （openid、 电子邮件、 配置文件、 电话和地址） OpenID Connect 规范中定义的作用域。

IdentityServer 还支持定义自定义标识资源。 有关详细信息，请参阅[定义自定义标识资源](https://identityserver4.readthedocs.io/en/release/topics/resources.html#defining-custom-identity-resources)IdentityServer 文档中。 有关详细信息`IdentityResource`类型，请参阅[标识资源](https://identityserver4.readthedocs.io/en/release/reference/identity_resource.html)IdentityServer 4 文档中。

#### <a name="configuring-clients"></a>配置客户端

客户端是可以从 IdentityServer 请求令牌的应用程序。 通常情况下，必须至少为每个客户端定义的以下设置：

-   一个唯一的客户端 id。
-   允许与令牌服务 （即所谓的授予类型构建） 交互。
-   标识和访问令牌发送到何处的位置 （称为重定向 URI）。
-   客户端允许访问的资源的列表 （称为作用域）。

配置客户端时`AddInMemoryClients`方法需要`IEnumerable<Client>`集合。 以下代码示例演示用于 eShopOnContainers 中的移动应用配置`GetClients`方法提供了此集合，在 eShopOnContainers 引用应用程序：

```csharp
public static IEnumerable<Client> GetClients(Dictionary<string,string> clientsUrl)
{
    return new List<Client>
    {
        ...
        new Client
        {
            ClientId = "xamarin",
            ClientName = "eShop Xamarin OpenId Client",
            AllowedGrantTypes = GrantTypes.Hybrid,
            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },
            RedirectUris = { clientsUrl["Xamarin"] },
            RequireConsent = false,
            RequirePkce = true,
            PostLogoutRedirectUris = { $"{clientsUrl["Xamarin"]}/Account/Redirecting" },
            AllowedCorsOrigins = { "http://eshopxamarin" },
            AllowedScopes = new List<string>
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.OfflineAccess,
                "orders",
                "basket"
            },
            AllowOfflineAccess = true,
            AllowAccessTokensViaBrowser = true
        },
        ...
    };
}
```

此配置指定以下属性的数据：

-   `ClientId`： 客户端一个唯一 ID。
-   `ClientName`： 用于日志记录和许可屏幕客户端显示名称。
-   `AllowedGrantTypes`： 指定客户端要与 IdentityServer 进行交互的方式。 有关详细信息请参阅[配置身份验证流](#configuring_the_authentication_flow)。
-   `ClientSecrets`： 指定从令牌终结点请求令牌时使用的客户端机密凭据。
-   `RedirectUris`： 指定要返回的令牌或授权代码到允许的 Uri。
-   `RequireConsent`： 指定是否为必需的许可屏幕。
-   `RequirePkce`： 指定是否使用授权代码的客户端必须发送校验密钥。
-   `PostLogoutRedirectUris`： 指定要注销后重定向到允许的 Uri。
-   `AllowedCorsOrigins`： 指定客户端的源，这样，IdentityServer 可以允许从原点的跨域调用。
-   `AllowedScopes`： 指定客户端有权访问的资源。 默认情况下，客户端具有不能访问任何资源。
-   `AllowOfflineAccess`： 指定客户端是否可以请求刷新令牌。

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>配置身份验证流

身份验证流的客户端和 IdentityServer 之间可以通过配置指定在授权类型`Client.AllowedGrantTypes`属性。 OpenID Connect 和 OAuth 2.0 规范定义了大量的身份验证流，包括：

-   隐式。 此流适用于基于浏览器的应用程序，因此应仅身份验证的用户或身份验证和访问令牌请求。 所有令牌是通过浏览器中，传输，因此高级功能，例如不允许使用刷新令牌。
-   授权代码。 此流提供的功能来检索令牌返回通道，而不是浏览器前通道，同时还支持客户端身份验证。
-   混合。 此流是隐式的组合，并且授权代码授予类型。 标识令牌通过浏览器通道传输，并包含与其他项目，例如授权代码一起签名的协议响应。 验证成功后的响应，应使用反向通道检索的访问权限和刷新令牌。

> [!TIP]
> 使用混合身份验证流。 混合身份验证流可缓解多种攻击，适用于浏览器通道，并是想要检索访问令牌 （和可能是刷新令牌） 的本机应用程序的推荐的流程。

有关身份验证流的详细信息，请参阅[授权类型](https://identityserver4.readthedocs.io/en/release/topics/grant_types.html)IdentityServer 4 文档中。

### <a name="performing-authentication"></a>执行身份验证

为代表用户颁发令牌的 IdentityServer，用户必须登录到 IdentityServer。 但是，IdentityServer 不提供用户界面或数据库进行身份验证。 因此，在 eShopOnContainers 引用应用程序中，ASP.NET Core 标识用于此目的。

EShopOnContainers 移动应用进行身份验证与 IdentityServer 具有混合身份验证流，它图中图 9-2 所示。

![](authentication-and-authorization-images/sign-in.png "在登录过程的高级概述")

**图 9-2:** 登录过程的高级概述

向发出登录请求`<base endpoint>:5105/connect/authorize`。 以下身份验证成功，IdentityServer 返回包含授权代码和一个标识令牌的身份验证响应。 授权代码然后发送到`<base endpoint>:5105/connect/token`，这会使用访问、 标识和刷新令牌进行响应。

EShopOnContainers 移动应用符号扩展的 IdentityServer 通过发送到请求`<base endpoint>:5105/connect/endsession`，使用其他参数。 注销之后，IdentityServer 响应返回到移动应用发送 post 注销重定向 URI。 图 9-3 说明了此过程。

![](authentication-and-authorization-images/sign-out.png "注销过程的高级概述")

**图 9-3:** 注销过程的高级概述

在 eShopOnContainers 移动应用中，由与 IdentityServer 通信`IdentityService`类，该类实现`IIdentityService`接口。 此接口指定实现的类必须提供`CreateAuthorizationRequest`， `CreateLogoutRequest`，和`GetTokenAsync`方法。

#### <a name="signing-in"></a>登录

当用户点击**登录名**按钮`LoginView`，则`SignInCommand`中`LoginViewModel`执行类时，它将依次执行`SignInAsync`方法。 下面的代码示例显示了此方法：

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

此方法将调用`CreateAuthorizationRequest`中的方法`IdentityService`类，该类在下面的代码示例所示：

```csharp
public string CreateAuthorizationRequest()
{
    // Create URI to authorization endpoint
    var authorizeRequest = new AuthorizeRequest(GlobalSetting.Instance.IdentityEndpoint);

    // Dictionary with values for the authorize request
    var dic = new Dictionary<string, string>();
    dic.Add("client_id", GlobalSetting.Instance.ClientId);
    dic.Add("client_secret", GlobalSetting.Instance.ClientSecret); 
    dic.Add("response_type", "code id_token");
    dic.Add("scope", "openid profile basket orders locations marketing offline_access");
    dic.Add("redirect_uri", GlobalSetting.Instance.IdentityCallback);
    dic.Add("nonce", Guid.NewGuid().ToString("N"));
    dic.Add("code_challenge", CreateCodeChallenge());
    dic.Add("code_challenge_method", "S256");

    // Add CSRF token to protect against cross-site request forgery attacks.
    var currentCSRFToken = Guid.NewGuid().ToString("N");
    dic.Add("state", currentCSRFToken);

    var authorizeUri = authorizeRequest.Create(dic); 
    return authorizeUri;
}

```

此方法为 IdentityServer 的创建 URI[授权终结点](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html)，所需的参数。 授权终结点位于`/connect/authorize`5105 公开为用户设置的基本终结点的端口上。 有关用户设置的详细信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> EShopOnContainers 移动应用的受攻击面减少通过实现 oauth 的 Code Exchange (PKCE) 扩展名的证明密钥。 PKCE 可防止截获如果正在使用授权代码。 这通过客户端生成机密的验证程序，在授权请求中传递的该哈希，并提供未经哈希的兑换授权代码时。 有关 PKCE 的详细信息，请参阅[代码 Exchange OAuth 公共客户端的 Proof Key](https://tools.ietf.org/html/rfc7636) Internet 工程任务组网站上。

返回的 URI 存储在`LoginUrl`属性的`LoginViewModel`类。 当`IsLogin`属性会变得`true`，则[ `WebView` ](xref:Xamarin.Forms.WebView)中`LoginView`将变得可见。 `WebView`数据绑定其[ `Source` ](xref:Xamarin.Forms.WebView.Source)属性设置为`LoginUrl`属性的`LoginViewModel`类，并向 IdentityServer 发出登录请求，因此当`LoginUrl`属性设置为IdentityServer 的授权终结点。 如果 IdentityServer 收到此请求，并且用户未经身份验证，`WebView`将重定向到已配置的登录页上，如图 9-4。

![](authentication-and-authorization-images/login.png "Web 视图所显示的登录页")

**图 9-4:** web 视图所显示的登录页

完成登录后， [ `WebView` ](xref:Xamarin.Forms.WebView)将重定向到返回的 URI。 这`WebView`导航将导致`NavigateAsync`中的方法`LoginViewModel`类要执行下面的代码示例中所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    var authResponse = new AuthorizeResponse(url);  
    if (!string.IsNullOrWhiteSpace(authResponse.Code))  
    {  
        var userToken = await _identityService.GetTokenAsync(authResponse.Code);  
        string accessToken = userToken.AccessToken;  

        if (!string.IsNullOrWhiteSpace(accessToken))  
        {  
            Settings.AuthAccessToken = accessToken;  
            Settings.AuthIdToken = authResponse.IdentityToken;  

            await NavigationService.NavigateToAsync<MainViewModel>();  
            await NavigationService.RemoveLastFromBackStackAsync();  
        }  
    }  
    ...  
}
```

此方法分析包含在返回的 URI，该身份验证响应并到 IdentityServer 的前提是存在一个有效的授权代码，则发出请求[令牌终结点](https://identityserver4.readthedocs.io/en/release/endpoints/token.html)，传递的授权代码，PKCE 机密验证程序，以及其他必需的参数。 令牌终结点位于`/connect/token`5105 公开为用户设置的基本终结点的端口上。 有关用户设置的详细信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

>💡 **提示**： 验证返回的 Uri。 尽管 eShopOnContainers 移动应用不会验证返回的 URI，但最佳做法是验证返回的 URI 引用已知的位置，若要阻止打开重定向攻击。

如果令牌终结点收到有效的授权代码和 PKCE 机密验证工具，它使用访问令牌、 标识令牌和刷新令牌的响应。 作为应用程序设置，然后存储的访问令牌 （它允许 API 资源的访问权限） 和标识令牌并执行页面导航。 因此，在 eShopOnContainers 移动应用中的总体效果是： 提供的用户都能成功进行身份验证与 IdentityServer，将用户导航到`MainView`页上，即[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)它显示`CatalogView`作为其所选的选项卡。

有关页面导航的信息，请参阅[导航](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 了解如何[ `WebView` ](xref:Xamarin.Forms.WebView)导航会导致视图模型方法执行，请参阅[调用导航使用行为](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors)。 有关应用程序设置的信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> EShopOnContainers 还允许模拟单一登录应用程序配置为使用中的模拟服务时`SettingsView`。 在此模式下，该应用不会与 IdentityServer，而允许用户使用任何身份验证登录进行通信。

#### <a name="signing-out"></a>签名扩展

当用户点击**注销**按钮`ProfileView`，则`LogoutCommand`中`ProfileViewModel`执行类时，它将依次执行`LogoutAsync`方法。 此方法执行到的页面导航`LoginView`页上，传递`LogoutParameter`设置为实例`true`作为参数。 在页面导航期间传递参数的详细信息，请参阅[将参数传递期间导航](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation)。

创建了一个视图并导航到，`InitializeAsync`执行的视图关联的视图模型方法时，然后执行`Logout`方法的`LoginViewModel`类，该类在下面的代码示例所示：

```csharp
private void Logout()  
{  
    var authIdToken = Settings.AuthIdToken;  
    var logoutRequest = _identityService.CreateLogoutRequest(authIdToken);  

    if (!string.IsNullOrEmpty(logoutRequest))  
    {  
        // Logout  
        LoginUrl = logoutRequest;  
    }  
    ...  
}
```

此方法将调用`CreateLogoutRequest`中的方法`IdentityService`类，并传入标识令牌检索应用程序设置作为参数。 有关应用程序设置的详细信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。 下面的代码示例演示`CreateLogoutRequest`方法：

```csharp
public string CreateLogoutRequest(string token)  
{  
    ...  
    return string.Format("{0}?id_token_hint={1}&post_logout_redirect_uri={2}",   
        GlobalSetting.Instance.LogoutEndpoint,  
        token,  
        GlobalSetting.Instance.LogoutCallback);  
}
```

此方法创建到 IdentityServer 的 URI[结束会话终结点](https://identityserver4.readthedocs.io/en/release/endpoints/endsession.html#refendsession)，所需的参数。 结束会话终结点位于`/connect/endsession`5105 公开为用户设置的基本终结点的端口上。 有关用户设置的详细信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

返回的 URI 存储在`LoginUrl`属性的`LoginViewModel`类。 虽然`IsLogin`属性是`true`，则[ `WebView` ](xref:Xamarin.Forms.WebView)中`LoginView`可见。 `WebView`数据绑定其[ `Source` ](xref:Xamarin.Forms.WebView.Source)属性设置为`LoginUrl`属性的`LoginViewModel`类，并因此对 IdentityServer 注销请求时`LoginUrl`属性设置为IdentityServer 的结束会话终结点。 IdentityServer 接收此请求时，前提是用户登录的是，注销时发生。 身份验证会跟踪与由从 ASP.NET Core cookie 身份验证中间件的 cookie。 因此，从 IdentityServer 注销删除身份验证 cookie 并将发送注销后重定向 URI 返回给客户。

在移动应用中， [ `WebView` ](xref:Xamarin.Forms.WebView)将重定向到后注销重定向 URI。 这`WebView`导航将导致`NavigateAsync`中的方法`LoginViewModel`类要执行下面的代码示例中所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    Settings.AuthAccessToken = string.Empty;  
    Settings.AuthIdToken = string.Empty;  
    IsLogin = false;  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    ...  
}
```

此方法将清除标识令牌和应用程序设置中的访问令牌，并设置`IsLogin`属性设置为`false`，这将导致[ `WebView` ](xref:Xamarin.Forms.WebView)上`LoginView`页面变得不可见. 最后，`LoginUrl`属性设置为 URI 的 IdentityServer 的[授权终结点](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html)，带所需的参数，以准备在下次用户启动的登录。

有关页面导航的信息，请参阅[导航](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 了解如何[ `WebView` ](xref:Xamarin.Forms.WebView)导航会导致视图模型方法执行，请参阅[调用导航使用行为](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors)。 有关应用程序设置的信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> EShopOnContainers 还允许模拟注销应用程序配置为在 SettingsView 中使用的模拟服务时。 在此模式下，应用程序与 IdentityServer，不会进行通信，并改为清除应用程序设置的任何存储的令牌。

<a name="authorization" />

## <a name="authorization"></a>授权

身份验证后，ASP.NET Core web Api 通常需要授予访问权限，这样，服务以使 Api 可供某些经过身份验证的用户，而不是为全部。

将访问限制为 ASP.NET Core MVC 路由可以通过将 Authorize 属性应用于控制器或操作，这可以限制访问控制器或操作到身份验证的用户，如下面的代码示例中所示：

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

如果未经授权的用户尝试访问控制器或操作标有`Authorize`属性，则 MVC 框架将返回 401 （未经授权） 的 HTTP 状态代码。

> [!NOTE]
> 参数可以指定在`Authorize`特性限制对特定用户的 API。 有关详细信息，请参阅[授权](/aspnet/core/security/authorization/introduction/)。

IdentityServer 可以集成到授权工作流，它提供控制授权的访问令牌。 这种方法是显示在图 9-5。

![](authentication-and-authorization-images/authorization.png "通过访问令牌的授权")

**图 9-5:** 授权的访问令牌

EShopOnContainers 移动应用与标识微服务进行通信和身份验证过程的一部分来请求访问令牌。 访问令牌然后转发给排序，然后将购物篮微服务作为访问请求的一部分公开的 Api。 访问令牌包含有关客户端和用户信息。 Api 然后使用该信息来授予对其数据的访问权限。 有关如何配置 IdentityServer 来保护 Api 的信息，请参阅[配置 API 资源](#configuring-api-resources)。

### <a name="configuring-identityserver-to-perform-authorization"></a>配置 IdentityServer 执行授权

若要执行使用 IdentityServer 进行授权，必须将其授权中间件添加到 web 应用程序的 HTTP 请求管道。 中添加中间件`ConfigureAuth`中 web 应用程序的方法`Startup`类，该类从调用`Configure`方法，并从 eShopOnContainers 引用应用程序下面的代码示例所示：

```csharp
protected virtual void ConfigureAuth(IApplicationBuilder app)  
{  
    var identityUrl = Configuration.GetValue<string>("IdentityUrl");  
    app.UseIdentityServerAuthentication(new IdentityServerAuthenticationOptions  
    {  
        Authority = identityUrl.ToString(),  
        ScopeName = "basket",  
        RequireHttpsMetadata = false  
    });  
} 
```

此方法可确保使用有效的访问令牌可以只访问 API。 中间件验证传入令牌，确保将发送从受信任的颁发者，并验证该令牌有效用于接收它的 API。 因此，浏览到排序或购物篮控制器将返回 401 （未经授权） HTTP 状态代码，指示需要访问令牌。

> [!NOTE]
> 添加 MVC 和之前，必须将 IdentityServer 的授权中间件添加到 web 应用程序的 HTTP 请求管道`app.UseMvc()`或`app.UseMvcWithDefaultRoute()`。

### <a name="making-access-requests-to-apis"></a>对 Api 进行访问请求

下面的代码示例中所示，必须在请求中，包括向发出请求时进行排序和购物篮微服务，访问令牌，身份验证过程中，从 IdentityServer 获取：

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

访问令牌存储为应用程序设置，和是从特定于平台的存储中检索到，包含在调用`GetOrderAsync`中的方法`OrderService`类。

同样，访问令牌时必须包括将数据发送到 IdentityServer 受保护的 API，如下面的代码示例中所示：

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

从特定于平台的存储中检索和对调用中包含访问令牌`UpdateBasketAsync`中的方法`BasketService`类。

`RequestProvider`类，在 eShopOnContainers 移动应用中，使用`HttpClient`类向 eShopOnContainers 引用应用程序公开的 RESTful Api 发出请求。 向排序，然后将购物篮 Api，这需要授权，使请求时有效的访问令牌必须包含在请求。 这通过将访问令牌添加到的标头来实现`HttpClient`实例，如下面的代码示例中所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

`DefaultRequestHeaders`的属性`HttpClient`类公开了与每个请求一起发送的标头和访问令牌添加到`Authorization`标头的字符串作为前缀`Bearer`。 当请求发送到 RESTful API 的值`Authorization`提取和验证，以确保它已从受信任的颁发者，发送和用来确定用户是否有权调用 API，它接收它的标头。

有关如何在 eShopOnContainers 的移动应用使 web 请求的详细信息，请参阅[访问远程数据](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。

## <a name="summary"></a>总结

有许多方法与 ASP.NET MVC web 应用程序进行通信的 Xamarin.Forms 应用中集成身份验证和授权。 EShopOnContainers 移动应用执行身份验证和授权与使用 IdentityServer 4 的容器化的标识微服务。 IdentityServer 是用于与 ASP.NET Core 标识来执行持有者令牌身份验证集成的 ASP.NET Core 的开放源代码 OpenID Connect 和 OAuth 2.0 框架。

对用户进行身份验证或用于访问资源，移动应用从 IdentityServer，请求安全令牌。 时访问资源，必须对需要授权的 Api 的请求中包含访问令牌。 IdentityServer 的中间件验证传入访问令牌，以确保它们会从受信任的颁发者，并且它们可以有效地与接收它们的 API 一起使用。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
