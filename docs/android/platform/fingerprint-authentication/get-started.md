---
title: 指纹身份验证入门
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 70d27ef3d7518619a246c25aac128b2fd1ed70c5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764393"
---
# <a name="getting-started-with-fingerprint-authentication"></a>指纹身份验证入门

若要开始，让我们首先介绍如何配置 Xamarin.Android 项目，以便能够使用指纹身份验证的应用程序：

1. 更新**AndroidManifest.xml**声明指纹 Api 都需要的权限。
2. 获取对引用`FingerprintManager`。
3. 请检查设备能够指纹扫描。

## <a name="requesting-permissions-in-the-application-manifest"></a>应用程序中的请求权限清单

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Android 应用程序必须请求`USE_FINGERPRINT`清单中的权限。 以下屏幕截图显示如何将此权限添加到 Visual Studio 2015 中的应用程序：

[![启用使用\_Android 清单屏幕中的指纹](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Android 应用程序必须请求`USE_FINGERPRINT`清单中的权限。 以下屏幕截图显示如何将此权限添加到 Visual Studio 中的应用程序，适用于 Mac:

[![在 Android 应用程序屏幕中启用 UseFingerprint](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>获取 FingerprintManager 的实例

接下来，应用程序必须获取其实例`FingerprintManager`或`FingerprintManagerCompat`类。 若要使用较旧版本的 Android 兼容，Android 应用程序应使用的兼容性 API 的 Android 支持 v4 NuGet 包中找到。 下面的代码段演示如何从操作系统获取适当的对象： 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

在以前的段中，`context`是任何 Android `Android.Content.Context`。 通常，这是执行身份验证的活动。

## <a name="checking-for-eligibility"></a>检查有资格

应用程序必须执行多个检查以确保它是可以使用指纹身份验证。 总共有五个应用程序使用检查符合条件的条件：  
 

**API 级别 23** &ndash;指纹 Api 需要 API 级别 23 或更高版本。 `FingerprintManagerCompat`类将包装对你的 API 级别检查。 因此它是建议使用使用**Android 支持库 v4**和`FingerprintManagerCompat`; 这将为这些检查在其中一个帐户。

**硬件**&ndash;当应用程序首次启动时，应检查是否存在指纹扫描程序：

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```
    
**保护设备**&ndash;用户必须持有配备屏幕锁定该设备。 如果用户具有不安全的屏幕锁定设备和安全性很重要的应用程序，然后用户应收到通知，必须配置屏幕锁定。 下面的代码段演示如何检查此预 requiste:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**注册指纹**&ndash;的用户必须具有至少一个指纹向操作系统注册。 此权限检查应发生在每次身份验证尝试之前：

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**权限**&ndash;应用程序必须使用应用程序之前从用户中请求权限。 有关 Android 5.0 和更低，用户授予权限，以安装应用程序的状态。 Android 6.0 引入了一个新的权限模型，检查在运行时的权限。 此代码片段演示了如何检查 Android 6.0 上的权限：

```csharp
// The context is typically a reference to the current activity.
Android.Content.PM.Permission permissionResult = ContextCompat.CheckSelfPermission(context, Manifest.Permission.UseFingerprint);
if (permissionResult == Android.Content.PM.Permission.Granted)
{
    // Permission granted - go ahead and start the fingerprint scanner.
}
else
{
    // No permission. Go and ask for permissions and don't start the scanner. See
    // http://developer.android.com/training/permissions/requesting.html
}
```

应用程序应在每次它想要使用指纹身份验证时检查条件 3、 4 和 5。 可以应用程序在第一次运行的设备，并将结果保存在检查前两个条件 （在共享首选项例如）。

有关如何在 Android 6.0 中的请求权限的详细信息，请参阅 Android 指南[在运行时请求权限](http://developer.android.com/training/permissions/requesting.html)。



## <a name="related-links"></a>相关链接

- [Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [ContextCompat](https://developer.xamarin.com/api/type/Android.Support.V4.Content.ContextCompat/)
- [KeyguardManager](https://developer.xamarin.com/api/type/Android.App.KeyguardManager/)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [在运行时的请求权限](http://developer.android.com/training/permissions/requesting.html)
