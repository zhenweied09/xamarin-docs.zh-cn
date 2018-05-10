---
title: Xamarin.Essentials 安全存储
description: SecureStorage 类可帮助安全地存储简单的键/值对。
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: redth
ms.author: jodick
ms.date: 05/04/2018
ms.openlocfilehash: 24d1e29ba0203aaafc3e21533478f6c505cc09b3
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials 安全存储

![预发行 NuGet](~/media/shared/pre-release.png)

**SecureStorage**类可帮助安全地存储简单的键/值对。

## <a name="using-secure-storage"></a>使用安全存储

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要保存的值给定_密钥_安全存储中：

```csharp
await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
```

若要从安全存储中检索一个值：

```csharp
var oauthToken = await SecureStorage.GetAsync("oauth_token");
```

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

[Android KeyStore](https://developer.android.com/training/articles/keystore.html)用于存储加密密钥用于加密值，然后再将它保存到[共享首选项](https://developer.android.com/training/data-storage/shared-preferences.html)的文件名 **[您的应用程序的包的 ID].xamarinessentials**.  用于在共享首选项文件中的密钥_MD5 哈希_密钥传入的`SecureStorage`API 的。

## <a name="api-level-23-and-higher"></a>API 级别 23 和更高版本

在较新的 API 级别**AES**从 Android KeyStore 获取密钥并将其用于**AES/GCM/NoPadding**密码以加密值，然后再将其存储在共享首选项文件。

## <a name="api-level-22-and-lower"></a>API 级别 22 和更低

在较旧的 API 级别上 Android KeyStore 仅支持存储**RSA**密钥，用于与**RSA/ECB/PKCS1Padding**密码来加密**AES** （随机密钥在运行时生成） 和存储在共享首选项文件项下_SecureStorageKey_，如果其中一个不已生成。

从设备中卸载应用程序时，将删除所有加密的值。

# <a name="iostabios"></a>[iOS](#tab/ios)

[KeyChain](https://developer.xamarin.com/api/type/Android.Security.KeyChain/)用于 iOS 的设备上安全地存储值。  `SecRecord`用于存储值具有`Service`值设置为 **[您的应用程序的捆绑包的 ID].xamarinessentials**。

KeyChain 数据与 iCloud，在某些情况下的同步和卸载应用程序可能从 iCloud 和用户的其他设备中删除的安全值。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/en-us/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider)用于安全地在 UWP 设备上的 encryped 值。

Encryped 值存储在`ApplicationData.Current.LocalSettings`，在名为容器内 **[您的应用程序的 ID].xamarinessentials**。

卸载应用程序将导致_LocalSettings_，以及要同时删除所有加密的值。

-----

## <a name="limitations"></a>限制

此 API 用于存储少量的文本。  性能可能会比较慢，如果你尝试使用它来存储大量的文本。

## <a name="api"></a>API

- [SecureStorage 源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/SecureStorage)
- [SecureStorage API 文档](xref:Xamarin.Essentials.SecureStorage)
