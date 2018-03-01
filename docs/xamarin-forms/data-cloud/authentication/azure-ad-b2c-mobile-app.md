---
title: "将与 Azure 移动应用程序集成 Azure Active Directory B2C"
description: "Azure Active Directory B2C 是面向使用者的 web 和移动应用程序的云标识管理解决方案。 本文演示如何使用 Azure Active Directory B2C Xamarin.Forms 中提供身份验证和授权与 Azure Mobile Apps 实例。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 3a7d89d9b0f383d365b18364e5d902ee0642f395
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>将与 Azure 移动应用程序集成 Azure Active Directory B2C

_Azure Active Directory B2C 是面向使用者的 web 和移动应用程序的云标识管理解决方案。本文演示如何使用 Azure Active Directory B2C Xamarin.Forms 中提供身份验证和授权与 Azure Mobile Apps 实例。_

![](~/media/shared/preview.png "此 API 已当前预发行版")

> [!NOTE]
> **请注意**: [Microsoft 身份验证库](https://www.nuget.org/packages/Microsoft.Identity.Client)仍为预览版，但适合在生产环境中使用。 但是，那里可能重大更改对 API、 内部缓存格式和库中，这可能会影响你的应用程序的其他机制。

## <a name="overview"></a>概述

Azure 移动应用，可以使用可缩放的后端移动身份验证、 脱机同步和推送通知的支持与托管在 Azure App Service 中开发应用程序。 有关 Azure Mobile Apps 的详细信息，请参阅[使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)，和[进行身份验证的用户与 Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md)。

Azure Active Directory B2C 是标识管理服务面向使用者的应用程序，允许使用者能够登录到您的应用程序：

- 使用现有社交帐户 （Microsoft、 Google、 Facebook、 Amazon、 LinkedIn）。
- 创建新的凭据 （电子邮件地址和密码，或用户名和密码）。 这些凭据称为*本地*帐户。

有关 Azure Active Directory B2C 的详细信息，请参阅[进行身份验证的用户与 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

Azure Active Directory B2C 可以用于为 Azure 移动应用管理身份验证工作流。 使用此方法时，标识管理体验在云中，已完全定义，并且可以修改而无需更改你的移动应用程序代码。

有与 Azure Mobile Apps 实例集成的 Azure Active Directory B2C 租户时可以采用的两个身份验证工作流：

- [客户端托管](#client_managed)– 这接近 Xamarin.Forms 移动应用程序启动身份验证处理与 Azure Active Directory B2C 租户中，并将收到的身份验证令牌传递给 Azure Mobile Apps 实例。
- [服务器管理](#server_managed)– 在此方法中 Azure Mobile Apps 实例将使用 Azure Active Directory B2C 租户对来启动基于 web 的工作流通过身份验证过程。

在这两种情况下，由 Azure Active Directory B2C 租户提供的身份验证体验。 在示例应用程序，这会导致登录屏幕中的以下屏幕截图所示：

![](azure-ad-b2c-mobile-app-images/screenshots.png "登录页")

在登录与社交标识提供程序，或使用本地帐户、 允许使用。 尽管 Microsoft、 Google 和 Facebook 用于为在此示例中的社交标识提供程序，还可以使用其他标识提供程序。

## <a name="setup"></a>安装

无论使用身份验证工作流的情况下，将 Azure Active Directory B2C 租户集成与 Azure Mobile Apps 实例的初始过程如下所示：

1. 创建 Azure Mobile Apps 实例。 有关详细信息，请参阅[使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 启用 Azure Mobile Apps 实例和 Xamarin.Forms 应用程序中的身份验证。 有关详细信息，请参阅[进行身份验证的用户与 Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md)。
1. 创建 Azure Active Directory B2C 租户。 有关详细信息，请参阅[进行身份验证的用户与 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

请注意，Microsoft 身份验证库 (MSAL) 需要使用客户端托管身份验证工作流时。 MSAL 使用设备的 web 浏览器来执行身份验证。 这提高了应用程序的可用性，因为用户仅需要在登录后每台设备，提高转换率的登录和授权流应用程序中。 设备浏览器还提供了改进的安全性。 用户完成身份验证过程后，控件将从 web 浏览器选项卡，返回到应用程序。这被通过从身份验证过程中，然后检测并处理自定义的 URL，它发送后返回的重定向 URL 注册自定义的 URL 方案。 有关使用 MSAL 与 Azure Active Directory B2C 租户通信的详细信息，请参阅[进行身份验证的用户与 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

<a name="client_managed" />

## <a name="client-managed-authentication"></a>客户端托管身份验证

在客户端托管身份验证，Xamarin.Forms 移动应用程序将联系 Azure Active Directory B2C 租户启动身份验证流。 后成功登录 Azure Active Directory B2C 租户将返回一个标识令牌，然后提供在登录到 Azure Mobile Apps 实例过程。 这允许 Xamarin.Forms 应用程序需要身份验证的用户权限的 Azure Mobile Apps 实例上执行操作。

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Azure Active Directory B2C 租户配置

为客户端托管身份验证工作流，应按以下方式配置 Azure Active Directory B2C 租户：

- 包括本机客户端。
- 设置为唯一标识移动应用程序后, 跟 URL 方案的自定义重定向 URI `://auth/`。 有关选择自定义的 URL 方案的详细信息，请参阅[选择本机应用程序重定向 URI](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri)。

下面的屏幕截图演示了此配置：

[![](azure-ad-b2c-mobile-app-images/client-flow-config-sml.png "Azure Active Directory B2C Configuration")](azure-ad-b2c-mobile-app-images/client-flow-config.png "Azure Active Directory B2C Configuration")

在 Azure Active Directory B2C 以便答复 URL 设置为相同的自定义 URL 方案，也可配置租户中使用的策略跟`://auth/`。 下面的屏幕截图演示了此配置：

![](azure-ad-b2c-mobile-app-images/client-flow-policies.png "Azure Active Directory B2C 策略")

### <a name="azure-mobile-app-configuration"></a>Azure 移动应用配置

为客户端托管身份验证工作流，应按以下方式配置 Azure Mobile Apps 实例：

- 应启用应用程序服务身份验证。
- 当请求未经过身份验证时要执行的操作应设置为**登录 Azure Active Directory**。

下面的屏幕截图演示了此配置：

![](azure-ad-b2c-mobile-app-images/client-flow-ama-config.png "Azure 移动应用程序配置")

Azure Mobile Apps 实例还应配置为与 Azure Active Directory B2C 租户通信。 这可以通过实现来完成**高级**的 Azure Active Directory 身份验证提供程序的模式与**客户端 ID**正在**应用程序 ID**的 AzureActive Directory B2C 租户，与**颁发者 Url**正在 Azure Active Directory B2C 策略的元数据终结点。 下面的屏幕截图演示了此配置：

![](azure-ad-b2c-mobile-app-images/client-flow-ama-advanced-config.png "高级配置的 azure 移动应用程序")

### <a name="signing-in"></a>登录

下面的代码示例演示如何启动客户端托管身份验证流：

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);

    ...
    var payload = new JObject();
    payload["access_token"] = authenticationResult.IdToken;

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    ...
}
```

Microsoft 身份验证库 (MSAL) 用于与 Azure Active Directory B2C 租户中启动的身份验证工作流。 `AcquireTokenAsync`方法启动设备的 web 浏览器并显示指定策略引用通过 Azure Active Directory B2C 策略中定义的身份验证选项`Constants.Authority`常量。 此策略定义期间注册和登录，使用者将经历的体验和应用程序将接收在成功注册或登录的声明。

结果`AcquireTokenAsync`方法调用是`AuthenticationResult`实例。 如果身份验证成功，`AuthenticationResult`实例将包含一个标识令牌，将本地缓存的。 如果身份验证不成功，`AuthenticationResult`实例将包含数据，该值指示身份验证失败的原因。 有关如何使用 MSAL 与 Azure Active Directory B2C 租户通信的信息，请参阅[进行身份验证的用户与 Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)。

当`MobileServiceClient.LoginAsync`调用方法，Azure Mobile Apps 实例收到包装在标识令牌`JObject`。 有效的令牌意味着，Azure Mobile Apps 实例不需要启动其自己的 OAuth 2.0 身份验证流存在。 相反，`MobileServiceClient.LoginAsync`方法返回`MobileServiceUser`将存储在实例`MobileServiceClient.CurrentUser`属性。 此属性提供`UserId`和`MobileServiceAuthenticationToken`属性。 这些表示经过身份验证的用户和用户，直至其过期可以使用的身份验证令牌。 身份验证令牌将包括在 Azure Mobile Apps 实例，允许 Xamarin.Forms 应用程序需要身份验证的用户权限的 Azure Mobile Apps 实例上执行操作进行的所有请求。

### <a name="signing-out"></a>注销

下面的代码示例演示如何调用客户端托管的注销过程：

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();

    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

`MobileServiceClient.LogoutAsync`方法取消用户进行身份验证与 Azure Mobile Apps 实例，并从本地缓存由 MSAL 时则会清除所有身份验证令牌。

<a name="server_managed" />

## <a name="server-managed-authentication"></a>服务器托管的身份验证

在服务器管理的身份验证，Xamarin.Forms 应用程序将联系 Azure Mobile Apps 实例，使用 Azure Active Directory B2C 租户通过 B2C 策略中定义时，显示在登录页面管理的 OAuth 2.0 身份验证流。 以下成功登录，Azure Mobile Apps 实例返回一个令牌以便 Xamarin.Forms 应用程序需要身份验证的用户权限的 Azure Mobile Apps 实例上执行操作。

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Azure Active Directory B2C 租户配置

服务器托管的身份验证工作流，应按以下方式配置 Azure Active Directory B2C 租户：

- 包括 web 应用程序/web API，并允许隐式流。
- 设置为 Azure 移动应用程序的地址的答复 URL 后, 跟`/.auth/login/aad/callback`。

下面的屏幕截图演示了此配置：

[![](azure-ad-b2c-mobile-app-images/server-flow-config-sml.png "Azure Active Directory B2C Configuration")](azure-ad-b2c-mobile-app-images/server-flow-config.png "Azure Active Directory B2C Configuration")

在 Azure Active Directory B2C 租户也可配置，使答复 URL 设置到的 Azure 移动应用程序中，地址中使用的策略跟`/.auth/login/aad/callback`。 下面的屏幕截图演示了此配置：

![](azure-ad-b2c-mobile-app-images/server-flow-policies.png "Azure Active Directory B2C 策略")

### <a name="azure-mobile-apps-instance-configuration"></a>Azure 移动应用程序实例配置

服务器托管的身份验证工作流，应按以下方式配置 Azure Mobile Apps 实例：

- 应启用应用程序服务身份验证。
- 当请求未经过身份验证时要执行的操作应设置为**登录 Azure Active Directory**。

下面的屏幕截图演示了此配置：

![](azure-ad-b2c-mobile-app-images/server-flow-ama-config.png "Azure 移动应用程序配置")

Azure Mobile Apps 实例还应配置为与 Azure Active Directory B2C 租户通信。 这可以通过实现来完成**高级**的 Azure Active Directory 身份验证提供程序的模式与**客户端 ID**正在**应用程序 ID**的 AzureActive Directory B2C 租户，与**颁发者 Url**正在 Azure Active Directory B2C 策略的元数据终结点。 下面的屏幕截图演示了此配置：

![](azure-ad-b2c-mobile-app-images/server-flow-ama-advanced-config.png "高级配置的 azure 移动应用程序")

### <a name="signing-in"></a>登录

下面的代码示例演示如何启动服务器托管的身份验证流：

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...
    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        UIApplication.SharedApplication.KeyWindow.RootViewController,
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);
    ...
}
```

当`MobileServiceClient.LoginAsync`调用方法，Azure Mobile Apps 实例执行了链接的 Azure Active Directory B2C 策略，它启动的 OAuth 2.0 身份验证流。 请注意，每个`AuthenticateAsync`方法是特定于平台的。 但是，每个`AuthenticateAsync`方法使用`MobileServiceClient.LoginAsync`方法并指定在身份验证过程中，将使用 Azure Active Directory 租户。 有关详细信息，请参阅[中用户的日志记录](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in)。

`MobileServiceClient.LoginAsync`方法返回`MobileServiceUser`将存储在实例`MobileServiceClient.CurrentUser`属性。 此属性提供`UserId`和`MobileServiceAuthenticationToken`属性。 这些表示经过身份验证的用户和用户，直至其过期可以使用的身份验证令牌。 身份验证令牌将包括在 Azure Mobile Apps 实例，允许 Xamarin.Forms 应用程序需要身份验证的用户权限的 Azure Mobile Apps 实例上执行操作进行的所有请求。

### <a name="signing-out"></a>注销

下面的代码示例演示如何调用服务器托管的注销过程：

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
    ...
}
```

`MobileServiceClient.LogoutAsync`方法取消用户进行身份验证与 Azure Mobile Apps 实例。 有关详细信息，请参阅[日志记录用户](~/xamarin-forms/data-cloud/authentication/azure.md#logging-out)。

## <a name="summary"></a>摘要

这篇文章演示了如何使用 Azure Active Directory B2C Xamarin.Forms 中提供身份验证和授权与 Azure Mobile Apps 实例。 Azure Active Directory B2C 是面向使用者的 web 和移动应用程序的云标识管理解决方案。


## <a name="related-links"></a>相关链接

- [TodoAzureAuth ServerFlow （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [TodoAzureAuth ClientFlow （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)
- [使用 Azure 移动应用程序的用户进行身份验证](~/xamarin-forms/data-cloud/authentication/azure.md)
- [使用 Azure Active Directory B2C 的用户进行身份验证](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Microsoft 身份验证库](https://www.nuget.org/packages/Microsoft.Identity.Client)
