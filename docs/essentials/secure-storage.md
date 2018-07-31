---
title: Xamarin.Essentials： 安全存储
description: 本文档介绍中 Xamarin.Essentials，可帮助安全地存储简单键/值对的 SecureStorage 类。 它讨论了如何使用类、 平台实现的细节和限制。
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: redth
ms.author: jodick
ms.date: 05/04/2018
ms.openlocfilehash: 2dfdb7051b269e73c68290a557849b9ae606c165
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353290"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials： 安全存储

![预发行版 NuGet](~/media/shared/pre-release.png)

**SecureStorage**类可帮助安全地存储简单键/值对。

## <a name="getting-started"></a>入门

访问**SecureStorage**功能，以下特定于平台的安装程序是必需的：

# <a name="androidtabandroid"></a>[Android](#tab/android)

无需其他设置。

# <a name="iostabios"></a>[iOS](#tab/ios)

当在 iOS 模拟器上进行开发，启用**密钥链**权利，并添加应用程序的捆绑包标识符的密钥链访问组。

打开**Entitlements.plist**中的 iOS 项目和查找**Keychain**权利，并启用它。 这会自动将作为一个组添加应用程序的标识符。

在项目属性下**iOS 捆绑签名**设置**自定义权利**到**Entitlements.plist**。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无需其他设置。

-----

## <a name="using-secure-storage"></a>使用安全存储

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要保存的值给定_密钥_安全存储中：

```csharp
try
{
  await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

若要从安全存储中检索值：

```csharp
try
{
  var oauthToken = await SecureStorage.GetAsync("oauth_token");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

> [!NOTE]
> 如果没有与请求的键相关联的值`GetAsync`将返回`null`。

若要删除特定密钥，请调用：

```csharp
SecureStorage.Remove("oauth_token");
```

若要删除所有密钥，请调用：

```csharp
SecureStorage.RemoveAll();
```


## <a name="platform-implementation-specifics"></a>平台实现的细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

[Android KeyStore](https://developer.android.com/training/articles/keystore.html)用来存储用于加密值，之后再将它保存到的密码密钥[共享首选项](https://developer.android.com/training/data-storage/shared-preferences.html)的文件名 **[您的应用包的 ID].xamarinessentials**.  在共享首选项文件中使用的关键在于_MD5 哈希_传递到的密钥的`SecureStorage`Api。

## <a name="api-level-23-and-higher"></a>API 级别 23 和更高版本

在较新的 API 级别**AES**密钥是从 Android 密钥存储中获取并用于**AES/GCM/NoPadding**密码以加密值，然后将存储在共享首选项文件中。

## <a name="api-level-22-and-lower"></a>API 级别 22 和更低

在较旧的 API 级别上 Android 密钥存储仅支持存储**RSA**密钥，通过使用该**RSA/ECB/PKCS1Padding**进行加密的密码**AES** （随机密钥在运行时生成） 和存储在共享首选项文件中的项下_SecureStorageKey_，如果其中一个已尚未生成。

**SecureStorage**使用[首选项](preferences.md)API，如下所示相同的数据持久性中所述[首选项](preferences.md#persistence)文档。 如果设备从 API 级别 22 或更低级别升级到 API 级别 23 和更高版本，此类型的加密将继续使用，除非卸载该应用程序或**RemoveAll**调用。

# <a name="iostabios"></a>[iOS](#tab/ios)

[密钥链](https://developer.xamarin.com/api/type/Security.SecKeyChain/)用于在 iOS 设备上安全地存储值。  `SecRecord`用来存储值具有`Service`值设置为 **[YOUR-应用的捆绑包的 ID].xamarinessentials**。

在某些情况下密钥链数据与 iCloud、 同步和卸载应用程序可能从 iCloud 和用户的其他设备中删除安全值。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider)是用于 UWP 设备上安全地加密值。

加密的值存储在`ApplicationData.Current.LocalSettings`，在一个名为容器内 **[YOUR APP ID].xamarinessentials**。

**SecureStorage**使用[首选项](preferences.md)API，如下所示相同的数据持久性中所述[首选项](preferences.md#persistence)文档。

-----

## <a name="limitations"></a>限制

此 API 用于存储少量文本。  性能可能会很慢，如果你尝试使用它来存储大量的文本。

## <a name="api"></a>API

- [SecureStorage 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/SecureStorage)
- [SecureStorage API 文档](xref:Xamarin.Essentials.SecureStorage)
