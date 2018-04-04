---
title: 使用 Azure Active Directory B2C 的用户进行身份验证
description: Azure Active Directory B2C 是面向使用者的 web 和移动应用程序的云标识管理解决方案。 本文演示如何使用 Microsoft 身份验证库和 Azure Active Directory B2C 将使用者标识管理集成到移动应用程序。
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: f17a6ad012aff81674db943b7d65e65ba77dca52
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="authenticating-users-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 的用户进行身份验证

_Azure Active Directory B2C 是面向使用者的 web 和移动应用程序的云标识管理解决方案。本文演示如何使用 Microsoft 身份验证库和 Azure Active Directory B2C 将使用者标识管理集成到移动应用程序。_

![](~/media/shared/preview.png "此 API 已当前预发行版")

> [!NOTE]
> [Microsoft 身份验证库](https://www.nuget.org/packages/Microsoft.Identity.Client)仍为预览版，但适合在生产环境中使用。 但是，那里可能重大更改对 API、 内部缓存格式和库中，这可能会影响你的应用程序的其他机制。

## <a name="overview"></a>概述

Azure Active Directory B2C 是标识管理服务面向使用者的应用程序，允许使用者能够登录到您的应用程序：

- 使用现有社交帐户 （Microsoft、 Google、 Facebook、 Amazon、 LinkedIn）。
- 创建新的凭据 （电子邮件地址和密码，或用户名和密码）。 这些凭据称为*本地*帐户。

将 Azure Active Directory B2C 标识管理服务集成到移动应用程序的过程如下所示：

1. 创建 Azure Active Directory B2C 租户。 有关详细信息，请参阅[在 Azure 门户中创建一个 Azure Active Directory B2C 租户](/azure/active-directory-b2c/active-directory-b2c-get-started/)。
1. 向 Azure Active Directory B2C 租户中注册你的移动应用程序。 注册过程将分配**应用程序 ID**唯一标识你的应用程序，和一个**重定向 URL**可以用于定向回你的应用程序的响应。 有关详细信息，请参阅[Azure Active Directory B2C： 注册你的应用程序](/azure/active-directory-b2c/active-directory-b2c-app-registration/)。
1. 创建一个注册和登录策略。 此策略将定义期间注册和登录，使用者将经历的体验，并且还指定应用程序将收到的令牌的内容，在成功注册或登录。 有关详细信息，请参阅[Azure Active Directory B2C： 内置策略](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。
1. 使用[Microsoft 身份验证库](https://www.nuget.org/packages/Microsoft.Identity.Client)(MSAL) 移动应用程序与 Azure Active Directory B2C 租户中启动的身份验证工作流中。

> [!NOTE]
> 将 Azure Active Directory B2C 标识管理集成到移动应用程序，以及 MSAL 还可将 Azure Active Directory 标识管理集成到移动应用程序。 这可以通过向 Azure Active Directory 在注册的移动应用程序来实现[应用程序注册门户](https://apps.dev.microsoft.com/)。 注册过程将分配**应用程序 ID**唯一标识你的应用程序，应使用 MSAL 时指定。 有关详细信息，请参阅[如何注册应用程序与 v2.0 终结点](/azure/active-directory/develop/active-directory-v2-app-registration/)，和[进行身份验证你移动应用程序使用 Microsoft 身份验证库](https://blog.xamarin.com/authenticate-mobile-apps-using-microsoft-authentication-library/)Xamarin 博客上。

MSAL 使用设备的 web 浏览器来执行身份验证。 这提高了应用程序的可用性，因为用户仅需要在登录后每台设备，提高转换率的登录和授权流应用程序中。 设备浏览器还提供了改进的安全性。 用户完成身份验证过程后，控件将从 web 浏览器选项卡，返回到应用程序。这被通过从身份验证过程中，然后检测并处理自定义的 URL，它发送后返回的重定向 URL 注册自定义的 URL 方案。 有关选择自定义的 URL 方案的详细信息，请参阅[选择本机应用程序重定向 URI](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri/)。

> [!NOTE]
> 向操作系统注册自定义的 URL 方案和处理方案的机制是特定于每个平台。

每个请求发送到 Azure Active Directory B2C 租户指定*策略*。 策略描述了使用者标识体验，如注册，或在登录。 例如，注册策略可让 Azure Active Directory B2C 租户通过以下设置来配置的行为：

- 使用者可用于登录到应用程序的帐户类型。
- 要在注册过程从使用者收集的数据。
- 多因素身份验证。
- 注册页内容。
- 移动应用程序将在执行策略时收到的令牌声明。

Azure Active Directory 租户可以包含不同类型，然后根据需要应用程序中使用的多个策略。 此外，策略可以在应用程序，允许你定义和修改使用者标识体验，而无需更改你的代码之间进行重用。 有关策略的详细信息，请参阅[Azure Active Directory B2C： 内置策略](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)。

## <a name="setup"></a>安装

Microsoft 身份验证库 (MSAL) NuGet 库必须添加到可移植类库 (PCL) 项目和 Xamarin.Forms 解决方案中的平台项目中。 以下部分提供有关使用 MSAL 与从移动应用程序的 Azure Active Directory B2C 租户通信的附加设置说明。

### <a name="portable-class-library"></a>可移植类库

MSAL 不支持 Windows Phone 8.1，因此使用 MSAL 的 Pcl 将需要删除此目标。 这可以通过重定目标 Pcl 使用 Profile7 来实现。 有关 PCL的详细信息，请参阅[可移植类库简介](~/cross-platform/app-fundamentals/pcl.md)。

### <a name="ios"></a>iOS

在 iOS 中，向 Azure Active Directory B2C 已注册的自定义 URL 方案必须注册中**Info.plist**，如以下屏幕截图中所示：

![](azure-ad-b2c-images/customurl-ios.png "注册在 iOS 上自定义 URL 方案")

Azure Active Directory B2C 完成授权请求，它将重定向到已注册的重定向 URL。 由于 URL 使用它会导致启动移动应用程序的 iOS 自定义方案，为启动参数，它由处理传递 URL 中`OpenUrl`在应用程序的重写`AppDelegate`类，以下代码所示示例：

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        ...
        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
            return true;
        }
    }
}
```

中的代码`OpenURL`方法确保控件返回到 MSAL 后身份验证工作流的交互部分已结束。

### <a name="android"></a>Android

在 Android 上，向 Azure Active Directory B2C 已注册的自定义 URL 方案必须注册中**AndroidManifest.xml**，通过添加`<activity>`内现有元素`<application>`元素。 `<activity>`元素指定`IntentFilter`上`Activity`，处理方案，并在下面的示例所示：

```xml
<application ...>
  <activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
      <action android:name="android.intent.action.VIEW" />
      <category android:name="android.intent.category.DEFAULT" />
      <category android:name="android.intent.category.BROWSABLE" />
      <data android:scheme="INSERT_URL_SCHEME_HERE" android:host="auth" />
    </intent-filter>
  </activity>
</application>
```

Azure Active Directory B2C 完成授权请求，它将重定向到已注册的重定向 URL。 由于 URL 使用它会导致启动移动应用程序的 Android 自定义方案，为启动参数，它由处理传递 URL 中`microsoft.identity.client.BrowserTabActivity`。 请注意，`data android:scheme`属性必须设置为与 Azure Active Directory B2C 应用程序注册的自定义 URL 方案。

此外，`MainActivity`必须修改类，如下面的代码示例中所示：

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.Droid
{
    ...
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            global::Xamarin.Forms.Forms.Init(this, bundle);
            Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();
            LoadApplication(new App());
            App.UiParent = new UIParent(this);
        }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
        }
    }
}

```

`OnCreate`方法修改通过分配`UIParent`到实例`App.UiParent`属性。 这可确保当前活动的上下文中发生的身份验证流。

中的代码`OnActivityResult`方法确保控件返回到 MSAL 后身份验证工作流的交互部分已结束。

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台上，没有其他安装程序需要使用 MSAL。

## <a name="initialization"></a>初始化

Microsoft 身份验证库使用的成员`PublicClientApplication`类以启动身份验证流。 示例应用程序声明并初始化`public`的名为此类型的属性`ADB2CClient`中`AuthenticationProvider`类。 下面的代码示例演示如何初始化此属性：

```csharp
ADB2CClient = new PublicClientApplication(Constants.ClientID, Constants.Authority);
```

当移动应用程序已向 Azure Active Directory B2C 租户注册的时注册过程分配**应用程序 ID**。 必须指定此 ID`PublicClientApplication`构造函数，以及`Authority`包含基 URL，以及 Azure Active Directory B2C 策略要执行的常量。

## <a name="signing-in"></a>登录

以下屏幕截图显示了示例应用程序中的登录屏幕：

![](azure-ad-b2c-images/login.png "登录页")

在登录与社交标识提供程序，或使用本地帐户、 允许使用。 Microsoft、 Google 和 Facebook，如上所示，用作社交标识提供程序时还可以使用其他标识提供程序。

下面的代码示例演示如何调用登录过程：

```csharp
using Microsoft.Identity.Client;

public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);
    ...
}

```

`AcquireTokenAsync`方法启动设备的 web 浏览器并显示指定策略引用通过 Azure Active Directory B2C 策略中定义的身份验证选项`Constants.Authority`常量。 此策略定义期间注册和登录，使用者将经历的体验和应用程序将接收在成功注册或登录的声明。

结果`AcquireTokenAsync`方法调用是`AuthenticationResult`实例。 如果身份验证成功，`AuthenticationResult`实例将包含一个标识令牌，将本地缓存的。 如果身份验证不成功，`AuthenticationResult`实例将包含数据，该值指示身份验证失败的原因。

在示例应用程序，如果身份验证成功，`TodoList`页导航到。

## <a name="silent-re-authentication"></a>无提示重新进行身份验证

当`LoginPage`在示例应用程序出现，尝试检索用户令牌，而不显示任何身份验证的用户界面。 这用来实现`AcquireTokenSilentAsync`方法，如下面的代码示例中所示：

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult;

    if (useSilent)
    {
        authenticationResult = await ADB2CClient.AcquireTokenSilentAsync(
            Constants.Scopes,
            GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
            Constants.Authority,
            false);
    }
    ...
}
```

`AcquireTokenSilentAsync`方法尝试从缓存中，检索用户令牌，而无需用户进行登录。 这将处理方案中的合适的令牌，可能已会出现在缓存中了先前会话中。 如果成功，尝试获取令牌`TodoList`页导航到。 如果尝试获取令牌失败，会执行任何操作，并且用户将可以选择以启动新的身份验证流。

## <a name="signing-out"></a>注销

下面的代码示例演示如何调用注销过程：

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

这将清除从本地缓存的所有身份验证令牌。

## <a name="summary"></a>总结

这篇文章演示了如何使用 Microsoft 身份验证库 (MSAL) 和 Azure Active Directory B2C 来将使用者标识管理集成到移动应用程序。 Azure Active Directory B2C 是面向使用者的 web 和移动应用程序的云标识管理解决方案。


## <a name="related-links"></a>相关链接

- [AzureADB2CAuth （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Microsoft 身份验证库](https://www.nuget.org/packages/Microsoft.Identity.Client)
