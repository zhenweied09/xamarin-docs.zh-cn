---
title: 扫描指纹
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/23/2016
ms.openlocfilehash: 2b20402fd2cd6782247fb2c62513d7aff43a95a6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="scanning-for-fingerprints"></a>扫描指纹

现在，我们已了解如何准备 Xamarin.Android 应用程序以使用指纹身份验证，让我们返回到`FingerprintManager.Authenticate`方法，并讨论其 Android 6.0 指纹身份验证中的位置。 此列表中描述了指纹身份验证的工作流的快速概述：

1. 调用`FingerprintManager.Authenticate`，并传递`CryptoObject`和`FingerprintManager.AuthenticationCallback`实例。 `CryptoObject`用于确保指纹身份验证结果不被篡改。 
2. 子类[FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)类。 此类的实例将提供给`FingerprintManager`时指纹身份验证启动。 指纹扫描程序完成后，它将调用此类上的回调方法之一。
3. 编写代码以更新 UI 以让用户知道该设备已开始指纹扫描仪，正在等待用户交互。 
4. Android 指纹扫描程序完成操作后，将通过在调用方法向应用程序返回结果`FingerprintManager.AuthenticationCallback`上一步中所提供的实例。
5. 应用程序将通知指纹身份验证结果的用户，并根据结果对做出响应。 

下面的代码段演示了将启动扫描指纹的活动中的方法：

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerPrintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

让我们讨论中的这些参数的每个`Authenticate`中更详细地方法：

* 第一个参数是_加密_对象指纹扫描仪将用来帮助进行身份验证指纹扫描的结果。 此对象可能`null`，在这种情况下应用程序中，将不会盲目信任已篡改指纹结果。 建议`CryptoObject`实例化并提供给`FingerprintManager`而不是 null。 [创建 CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md)将详细说明了如何实例化`CryptoObject`基于`Cipher`。
* 第二个参数始终为零。 Android 文档将此标识为组标志，并最有可能保留供将来使用。 
* 第三个参数，`cancellationSignal`是用于关闭指纹扫描仪和取消当前请求的对象。 这是[Android CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html)，而不从.NET framework 类型。
* 第四个参数是必需的它是该子类的类`AuthenticationCallback`抽象类。 此类上的方法将调用以向客户端发出信号时`FingerprintManager`已完成以及结果是什么。 若要了解有关实现很多原样`AuthenticationCallback`，将在介绍[很自己部分](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)。
* 第五个参数是可选的`Handler`实例。 如果`Handler`提供对象之后，`FingerprintManager`将使用`Looper`从处理来自指纹硬件的消息时该对象。 通常情况下，一个不需要提供`Handler`，将使用 FingerprintManager`Looper`从应用程序。

## <a name="cancelling-a-fingerprint-scan"></a>取消指纹扫描

它可能是用户 （或应用程序） 后已启动取消指纹扫描所必需的。 在此情况下，调用[ `IsCancelled` ](http://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled())方法[ `CancellationSignal` ](http://developer.android.com/reference/android/os/CancellationSignal.html)提供给`FingerprintManager.Authenticate`时调用它以启动的指纹扫描。

现在，我们看到`Authenticate`方法，让我们检查一些更多详细信息中的更重要的参数。 首先，我们将考察[响应身份验证回调](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)，这将讨论如何子类[FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)，启用 Android 应用程序以响应提供的指纹扫描程序的结果。




## <a name="related-links"></a>相关链接

- [CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
