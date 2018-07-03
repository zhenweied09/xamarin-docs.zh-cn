---
title: 身份验证和授权
description: 本章介绍 eShopOnContainers 移动应用程序如何执行身份验证和授权针对容器化微服务。
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: 9e6cfa566ab455841b3f11e4a857dcf678083417
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242422"
---
# <a name="authentication-and-authorization"></a>身份验证和授权

身份验证是从用户获取标识的凭据，如名称和密码，并根据证书颁发机构验证这些凭据的过程。 如果凭据有效，提交凭据实体被视为已验证的身份。 一旦标识已经过身份验证，授权过程将确定该标识是否有权访问给定的资源。

有许多种集成到 Xamarin.Forms 应用 ASP.NET MVC web 应用程序，包括使用 ASP.NET Core 标识时，例如 Microsoft、 Google、 外部身份验证提供程序与之通信的身份验证和授权Facebook 或 Twitter 和身份验证中间件。 EShopOnContainers 移动应用程序执行身份验证和授权与使用 IdentityServer 4 容器化的标识微服务。 用于对用户进行身份验证或访问资源，移动应用程序从 IdentityServer，请求安全令牌。 为代表用户颁发令牌的 IdentityServer，用户必须登录到 IdentityServer。 但是，IdentityServer 不提供用户界面或数据库进行身份验证。 因此，在 eShopOnContainers 引用应用程序中，ASP.NET Core 标识用于此目的。

## <a name="authentication"></a>身份验证

应用程序需要了解的当前用户身份时，身份验证是必需的。 ASP.NET Core 用于标识用户的主要机制是 ASP.NET Core 标识成员身份系统，它在由开发人员配置了数据存储中存储用户的信息。 尽管可以使用自定义存储或第三方包将标识信息存储在 Azure 存储、 Azure Cosmos DB 或其他位置，此数据存储通常情况下，将一个 EntityFramework 存储区。

有关身份验证方案，请使用本地用户数据存储区，并且保留之间通过 cookie （就是典型 ASP.NET MVC web 应用程序中） 发出的请求的标识信息，则 ASP.NET Core 标识为合适的解决方案。 但是，cookie 并不总是保持和传输数据的自然方式。 例如，ASP.NET Core web 应用程序公开从移动应用程序访问的 RESTful 终结点通常需要使用持有者令牌身份验证，因为无法在此方案中使用 cookie。 但是，持有者令牌可以轻松地检索和包括在从移动应用程序进行 web 请求的 authorization 标头。

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>颁发的持有者令牌使用 IdentityServer 4

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4)有关 ASP.NET Core，可用于许多的身份验证和授权方案，包括颁发本地 ASP.NET Core 标识用户的安全令牌是一种开放源代码 OpenID Connect 和 OAuth 2.0 框架。

> [!NOTE]
> OpenID Connect 和 OAuth 2.0 则是非常相似，而同时将不同职责。

OpenID Connect 是 OAuth 2.0 协议层，身份验证。 OAuth 2 是计算的协议，使应用程序可以从安全令牌服务请求访问令牌并使用它们与通信 Api。 由于可以集中式身份验证和授权，这种委托可以减少客户端应用程序和 Api 的复杂性。

OpenID Connect 和 OAuth 2.0 的组合组合的身份验证和 API 访问的两个基本的安全问题并 IdentityServer 4 是这些协议的实现。

使用直接微服务构成客户端通信，例如 eShopOnContainers 引用应用程序，应用程序中充当作为安全令牌服务 (STS) 专用身份验证 microservice 可用来验证用户身份，图中所示9-1。 有关直接微服务构成客户端通信的详细信息，请参阅[之间客户端通信和微服务](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices)。

![](authentication-and-authorization-images/authentication.png "通过专用身份验证微服务进行身份验证")

**图 9-1:** 通过专用身份验证微服务进行身份验证

EShopOnContainers 移动应用与标识微，它使用 IdentityServer 4 执行身份验证，并为 Api 的访问控制服务进行通信。 因此，移动应用程序请求令牌从 IdentityServer，用于对用户进行身份验证或访问资源：

-   IdentityServer 与用户进行身份验证通过移动应用程序请求*标识*令牌中，它表示的身份验证过程的结果。 至少裸机，它包含的用户和有关如何以及何时对用户身份验证信息的标识符。 它还可以包含附加的标识数据。
-   访问具有 IdentityServer 的资源通过移动应用程序请求*访问*令牌中，它允许对 API 资源的访问。 客户端请求访问令牌，并将它们转发给 API。 访问令牌包含有关客户端和用户信息 （如果存在）。 Api 然后使用该信息来授予对其数据的访问权限。

> [!NOTE]
> 客户端必须先向 IdentityServer 注册，然后它可以请求令牌。

### <a name="adding-identityserver-to-a-web-application"></a>将 IdentityServer 添加到 Web 应用程序

为了使 ASP.NET Core web 应用程序使用 IdentityServer 4，必须将添加到 web 应用程序的 Visual Studio 解决方案。 有关详细信息，请参阅[安装程序和概述](https://identityserver4.readthedocs.io/en/release/quickstarts/0_overview.html)IdentityServer 文档中。

一旦 IdentityServer 将包含在 web 应用程序的 Visual Studio 解决方案，它必须添加到 web 应用程序的 HTTP 请求处理管道，这是这样它可以提供到 OpenID Connect 和 OAuth 2.0 终结点的请求。 这实现`Configure`中 web 应用程序的方法`Startup`类，如下面的代码示例中所示：

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

在 web 应用程序的 HTTP 请求处理管道，顺序非常重要。 因此，IdentityServer 必须添加到 UI 框架实现登录屏幕之前管道。

### <a name="configuring-identityserver"></a>配置 IdentityServer

应在配置 IdentityServer`ConfigureServices`中 web 应用程序的方法`Startup`类通过调用`services.AddIdentityServer`方法，如下面的代码示例从 eShopOnContainers 引用应用程序中所示：

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

在调用`services.AddIdentityServer`方法，其他 fluent Api 调用来配置以下各项：

-   用于签名的凭据。
-   用户可能会请求的 API 和标识资源访问权限。
-   将连接到请求令牌的客户端。
-   ASP.NET Core 标识。

>💡 **提示**： 动态加载 IdentityServer 4 配置。 IdentityServer 4 Api 均允许配置 IdentityServer 从内存中列表的配置对象。 在 eShopOnContainers 引用应用程序中，这些内存中集合是硬编码到应用程序。 但是，在生产方案中可以加载它们动态从配置文件或数据库。

有关配置 IdentityServer 使用 ASP.NET Core 标识的信息，请参阅[使用 ASP.NET Core 标识](https://identityserver4.readthedocs.io/en/release/quickstarts/6_aspnet_identity.html)IdentityServer 文档中。

#### <a name="configuring-api-resources"></a>配置 API 资源

配置 API 资源时`AddInMemoryApiResources`方法需要`IEnumerable<ApiResource>`集合。 下面的代码示例演示`GetApis`提供 eShopOnContainers 在此集合的方法引用应用程序：

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

此方法指定 IdentityServer 应该保护的订单和篮 Api。 因此，IdentityServer 托管访问在对这些 Api 的调用时，将需要令牌。 有关详细信息`ApiResource`类型，请参阅[API 资源](https://identityserver4.readthedocs.io/en/release/reference/api_resource.html#refapiresource)IdentityServer 4 文档中。

#### <a name="configuring-identity-resources"></a>配置标识资源

配置标识资源时`AddInMemoryIdentityResources`方法需要`IEnumerable<IdentityResource>`集合。 标识资源是数据，例如用户 ID、 名称或电子邮件地址。 每个标识资源的唯一名称，并且初始屏幕任意声明类型可以分配给它，它然后将包括在用户的标识令牌。 下面的代码示例演示`GetResources`提供 eShopOnContainers 在此集合的方法引用应用程序：

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

OpenID Connect 规范指定一些[标准标识资源](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims)。 最低要求是提供支持是为了发出用户的唯一 ID。 这通过公开`IdentityResources.OpenId`标识资源。

> [!NOTE]
> `IdentityResources`类支持所有 OpenID Connect 规范 （openid、 电子邮件、 配置文件、 电话和地址） 中定义的作用域。

IdentityServer 还支持定义自定义标识资源。 有关详细信息，请参阅[定义自定义标识资源](https://identityserver4.readthedocs.io/en/release/topics/resources.html#defining-custom-identity-resources)IdentityServer 文档中。 有关详细信息`IdentityResource`类型，请参阅[标识资源](https://identityserver4.readthedocs.io/en/release/reference/identity_resource.html)IdentityServer 4 文档中。

#### <a name="configuring-clients"></a>配置客户端

客户端是可以从 IdentityServer 请求令牌的应用程序。 通常情况下，必须与最小值为每个客户端定义的以下设置：

-   是唯一的客户端 id。
-   （称为授予类型） 的令牌服务进行的允许的交互。
-   其中将标识和访问令牌发送到的位置 （称为重定向 URI）。
-   客户端是否允许访问的资源的列表 （称为作用域）。

配置客户端时`AddInMemoryClients`方法需要`IEnumerable<Client>`集合。 下面的代码示例显示 eShopOnContainers 移动应用中的配置`GetClients`提供 eShopOnContainers 在此集合的方法引用应用程序：

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

此配置可指定以下属性的数据：

-   `ClientId`： 客户端唯一 ID。
-   `ClientName`： 用于日志记录和同意屏幕客户端显示名称。
-   `AllowedGrantTypes`： 指定客户端想要与 IdentityServer 交互的方式。 有关详细信息请参阅[配置身份验证流](#configuring_the_authentication_flow)。
-   `ClientSecrets`： 指定从令牌的终结点请求令牌时使用的客户端机密凭据。
-   `RedirectUris`： 指定允许的 Uri，对其返回令牌或授权代码。
-   `RequireConsent`： 指定是否需要同意屏幕。
-   `RequirePkce`： 指定是否使用授权代码的客户端必须发送校验密钥。
-   `PostLogoutRedirectUris`： 指定要注销后重定向到允许的 Uri。
-   `AllowedCorsOrigins`： 指定的源的客户端，以便 IdentityServer 可以允许从原点的跨域调用。
-   `AllowedScopes`： 指定客户端有权访问的资源。 默认情况下，客户端具有不能访问任何资源。
-   `AllowOfflineAccess`： 指定客户端是否可以请求刷新令牌。

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>配置的身份验证流

身份验证流客户端和 IdentityServer 之间可以配置通过指定中的授予类型`Client.AllowedGrantTypes`属性。 OpenID Connect 和 OAuth 2.0 规范定义了大量的身份验证流，包括：

-   隐式。 此流针对基于浏览器的应用程序进行了优化，应仅身份验证的用户或身份验证和访问令牌请求。 所有令牌通过浏览器中，传输，并因此高级功能，例如不允许使用刷新令牌。
-   授权代码。 此流提供的功能，同时还支持客户端身份验证检索反向通道，而不是浏览器前通道上的令牌。
-   混合。 此流是隐式的组合，授权代码授予类型。 标识令牌通过浏览器通道传输，并包含其他项目，比如授权代码以及签名的协议响应。 验证成功后的响应，应使用反向通道检索的访问权限和刷新令牌。

> [!TIP]
> 使用混合身份验证流。 混合身份验证流可以缓解产生大量的攻击适用于浏览器通道，并是想要检索访问令牌 （和可能刷新令牌） 的本机应用程序建议的流程。

有关身份验证流的详细信息，请参阅[授予类型](https://identityserver4.readthedocs.io/en/release/topics/grant_types.html)IdentityServer 4 文档中。

### <a name="performing-authentication"></a>执行身份验证

为代表用户颁发令牌的 IdentityServer，用户必须登录到 IdentityServer。 但是，IdentityServer 不提供用户界面或数据库进行身份验证。 因此，在 eShopOnContainers 引用应用程序中，ASP.NET Core 标识用于此目的。

EShopOnContainers 移动应用程序使用进行身份验证与 IdentityServer 一点图 9-2 中的混合身份验证流。

![](authentication-and-authorization-images/sign-in.png "在登录过程的高级概述")

**图 9-2:** 登录过程的高级概述

向发出登录请求`<base endpoint>:5105/connect/authorize`。 以下身份验证成功，IdentityServer 返回包含授权代码和一个标识令牌身份验证响应。 然后，授权代码将发送到`<base endpoint>:5105/connect/token`，这将使用访问、 标识和刷新令牌进行响应。

EShopOnContainers 移动应用程序签名的扩展 IdentityServer 通过发送到请求`<base endpoint>:5105/connect/endsession`，使用其他参数。 注销之后，IdentityServer 就会将 post 注销重定向 URI 发送回移动应用程序。 图 9-3 说明了此过程。

![](authentication-and-authorization-images/sign-out.png "注销过程的高级概述")

**图 9-3:** 注销过程的高级概述

由在 eShopOnContainers 移动应用中，执行与 IdentityServer 通信`IdentityService`类，该类实现`IIdentityService`接口。 此接口指定实现的类必须提供`CreateAuthorizationRequest`， `CreateLogoutRequest`，和`GetTokenAsync`方法。

#### <a name="signing-in"></a>登录

当用户点击**登录**按钮上`LoginView`、`SignInCommand`中`LoginViewModel`执行类，其中又执行`SignInAsync`方法。 下面的代码示例演示此方法：

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

此方法调用`CreateAuthorizationRequest`中的方法`IdentityService`类，该类中下面的代码示例所示：

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

此方法为 IdentityServer 的创建 URI[授权终结点](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html)，所需的参数。 授权终结点位于`/connect/authorize`在端口 5105 公开作为用户设置的基本终结点。 有关用户设置的详细信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> EShopOnContainers 移动应用程序的受攻击面减少实现 OAuth 代码 Exchange (PKCE) 扩展证明密钥。 PKCE 可防止使用如果截获授权代码。 这通过客户端生成机密的验证程序，授权请求中传递哈希，并已提供未经哈希时兑换授权代码。 有关 PKCE 的详细信息，请参阅[OAuth 公共客户端的代码交换的证明键](https://tools.ietf.org/html/rfc7636)Internet 工程任务组 web 站点上。

返回的 URI 存储在`LoginUrl`属性`LoginViewModel`类。 当`IsLogin`属性变为`true`、 [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)中`LoginView`变得可见。 `WebView`数据绑定其[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/)属性`LoginUrl`属性`LoginViewModel`类，并因此向 IdentityServer 发出登录请求时`LoginUrl`属性设置为IdentityServer 的授权终结点。 当收到此请求时 IdentityServer 和用户进行身份验证，`WebView`将重定向到配置的登录页上，而会显示在图 9-4 中。

![](authentication-and-authorization-images/login.png "WebView 所显示的登录页")

**图 9-4:** WebView 所显示的登录页

完成登录后， [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)将重定向到返回的 URI。 这`WebView`导航将导致`NavigateAsync`中的方法`LoginViewModel`类要执行下面的代码示例中所示：

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

此方法分析身份验证响应中返回的 URI，包含并假设存在一个有效的授权代码，则它发出一个请求到 IdentityServer 的[令牌终结点](https://identityserver4.readthedocs.io/en/release/endpoints/token.html)，传递的授权代码，PKCE 机密验证程序，以及其他必需的参数。 令牌的终结点位于`/connect/token`在端口 5105 公开作为用户设置的基本终结点。 有关用户设置的详细信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

>💡 **提示**： 验证返回 Uri。 尽管 eShopOnContainers 移动应用程序不验证返回的 URI，最佳做法是验证返回的 URI，引用到已知的位置，以防止打开重定向攻击。

如果令牌终结点收到有效的授权代码和 PKCE 机密验证程序，因此它的响应使用访问令牌、 标识令牌和刷新令牌。 为应用程序设置，然后存储的访问令牌 （其中允许对 API 资源的访问） 和标识令牌并执行页导航。 因此，eShopOnContainers 移动应用程序中的总体影响是这： 提供的用户都能成功进行 IdentityServer 身份验证，将用户导航到`MainView`页上，这是[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)显示`CatalogView`作为其所选的选项卡。

有关页导航的信息，请参阅[导航](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 有关如何信息[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)导航导致视图模型方法执行，请参阅[使用行为调用导航](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors)。 有关应用程序设置的信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> EShopOnContainers 时，还可以模拟登录在应用程序配置为使用中的模拟服务`SettingsView`。 在此模式下，应用程序不会与其 IdentityServer，而允许用户使用任何身份验证登录。

#### <a name="signing-out"></a>签名扩展

当用户点击**注销**按钮`ProfileView`、`LogoutCommand`中`ProfileViewModel`执行类，其中又执行`LogoutAsync`方法。 此方法执行到页面导航`LoginView`页上，将传递`LogoutParameter`实例设置为`true`作为参数。 在页面导航期间传递参数的详细信息，请参阅[将参数传递期间导航](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation)。

创建了一个视图并导航到，`InitializeAsync`执行方法的此视图关联的视图模型，其中然后执行`Logout`方法`LoginViewModel`类，该类中下面的代码示例所示：

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

此方法调用`CreateLogoutRequest`中的方法`IdentityService`类，并传入标识令牌检索应用程序设置作为参数。 有关应用程序设置的详细信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。 下面的代码示例演示`CreateLogoutRequest`方法：

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

此方法创建的 URI IdentityServer 的[结束会话终结点](https://identityserver4.readthedocs.io/en/release/endpoints/endsession.html#refendsession)，所需的参数。 结束会话终结点位于`/connect/endsession`在端口 5105 公开作为用户设置的基本终结点。 有关用户设置的详细信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

返回的 URI 存储在`LoginUrl`属性`LoginViewModel`类。 虽然`IsLogin`属性是`true`、 [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)中`LoginView`可见。 `WebView`数据绑定其[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/)属性`LoginUrl`属性`LoginViewModel`类，并因此对 IdentityServer 注销请求时`LoginUrl`属性设置为IdentityServer 的结束会话终结点。 当 IdentityServer 收到此请求时，提供程序的用户在签名时，注销时发生。 身份验证会跟踪与由从 ASP.NET Core cookie 身份验证中间件的 cookie。 因此，从 IdentityServer 注销删除身份验证 cookie 并发送 post 注销重定向 URI 回客户端。

在移动应用中， [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)将重定向到 post 注销重定向 URI。 这`WebView`导航将导致`NavigateAsync`中的方法`LoginViewModel`类要执行下面的代码示例中所示：

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

此方法清除标识令牌和应用程序设置中的访问令牌，并设置`IsLogin`属性`false`，这将导致[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)上`LoginView`页后，可以变得不可见. 最后，`LoginUrl`属性设置为 URI 的 IdentityServer 的[授权终结点](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html)，所需的参数，在下一次的准备过程中用户启动一次登录中。

有关页导航的信息，请参阅[导航](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 有关如何信息[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)导航导致视图模型方法执行，请参阅[使用行为调用导航](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors)。 有关应用程序设置的信息，请参阅[配置管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> EShopOnContainers 时，还可以 mock 注销应用程序配置为使用 SettingsView 中模拟的服务。 在此模式下，应用程序不与 IdentityServer，通信，并改为清除应用程序设置的任何存储的令牌。

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

如果未经授权的用户尝试访问的控制器或操作，标记有`Authorize`属性，则 MVC 框架将返回 401 （未经授权） 的 HTTP 状态代码。

> [!NOTE]
> 可以在上指定参数`Authorize`特性限制对特定用户的 API。 有关详细信息，请参阅[授权](/aspnet/core/security/authorization/introduction/)。

IdentityServer 可以集成到了授权工作流中，以便它提供控件授权的访问令牌。 此方法将显示在图 9-5 中。

![](authentication-and-authorization-images/authorization.png "通过访问令牌的授权")

**图 9-5:** 通过访问令牌的授权

EShopOnContainers 移动应用程序与标识微服务通信，并请求访问令牌身份验证过程的一部分。 访问令牌然后转发到排序和 basket 微服务作为访问请求的一部分公开的 Api。 访问令牌包含有关客户端和用户信息。 Api 然后使用该信息来授予对其数据的访问权限。 有关如何配置 IdentityServer 来保护 Api 的信息，请参阅[配置 API 资源](#configuring-api-resources)。

### <a name="configuring-identityserver-to-perform-authorization"></a>配置 IdentityServer 执行授权

若要执行使用 IdentityServer 授权，必须将其授权中间件添加到 web 应用程序的 HTTP 请求管道。 在中添加该中间件`ConfigureAuth`中 web 应用程序的方法`Startup`类，该类从调用`Configure`方法，并在 eShopOnContainers 引用应用程序的以下代码示例演示：

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

此方法可确保使用有效的访问令牌可以只访问 API。 该中间件来验证传入标记，以确保发送从受信任的颁发者，并验证该令牌的有效用于接收它的 API。 因此，浏览到排序或购物篮控制器将返回 401 （未经授权） HTTP 状态代码，，该值指示需要访问令牌。

> [!NOTE]
> IdentityServer 的授权中间件之前，必须添加到 web 应用程序的 HTTP 请求管道添加与 MVC`app.UseMvc()`或`app.UseMvcWithDefaultRoute()`。

### <a name="making-access-requests-to-apis"></a>对 Api 进行访问请求

下面的代码示例中所示，必须在请求中，包括发出请求时到排序和 basket 微服务，访问令牌，在身份验证过程中，从 IdentityServer 获取：

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

访问令牌存储为应用程序设置，和是从特定于平台的存储中检索，包含在调用`GetOrderAsync`中的方法`OrderService`类。

同样，访问令牌时必须包含将数据发送到 IdentityServer 受保护的 API，如下面的代码示例中所示：

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

从特定于平台的存储检索访问令牌并将其包含在调用`UpdateBasketAsync`中的方法`BasketService`类。

`RequestProvider`类，在 eShopOnContainers 移动应用中，使用`HttpClient`类以向 eShopOnContainers 引用应用程序公开的 RESTful Api 发出请求。 当进行请求到排序和 basket Api，它需要授权，必须包含在请求的有效访问令牌。 这通过将访问令牌添加到的标头实现`HttpClient`实例，如下面的代码示例中所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

`DefaultRequestHeaders`属性`HttpClient`类公开与每个请求一起发送的标头和访问令牌添加到`Authorization`使用字符串作为前缀的标头`Bearer`。 当请求发送到 RESTful API 的值`Authorization`提取和验证以确保已从受信任的颁发者，发送和用于确定用户是否有权调用 API 接收它标头。

有关如何 eShopOnContainers 移动应用程序发出 web 请求的详细信息，请参阅[访问远程数据](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。

## <a name="summary"></a>总结

有许多方法将身份验证和授权集成到 Xamarin.Forms 应用 ASP.NET MVC web 应用程序与之通信。 EShopOnContainers 移动应用程序执行身份验证和授权与使用 IdentityServer 4 容器化的标识微服务。 IdentityServer 是用于与 ASP.NET Core标识来执行持有者令牌身份验证集成的 ASP.NET Core 的开放源代码 OpenID Connect 和 OAuth 2.0 框架。

用于对用户进行身份验证或访问资源，移动应用程序从 IdentityServer，请求安全令牌。 资源时，必须对需要授权的 Api 请求中包含访问令牌。 IdentityServer 的中间件验证传入的访问令牌，以确保它们发送从受信任的颁发者，并且它们可以有效地与接收它们的 API 一起使用。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
