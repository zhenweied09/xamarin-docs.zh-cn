---
title: 指纹身份验证
description: 本指南介绍如何添加指纹身份验证，在 Android 6.0 中，引入到 Xamarin.Android 应用程序。
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1b28b16dfd92ef3a31201ef2e86681a425a58ab8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="fingerprint-authentication"></a>指纹身份验证

_本指南介绍如何添加指纹身份验证，在 Android 6.0 中，引入到 Xamarin.Android 应用程序。_


## <a name="fingerprint-authentication-overview"></a>指纹身份验证概述

在 Android 设备上的指纹扫描仪到达提供与传统的用户名/密码方法的替代的用户身份验证的应用程序。 使用指纹对用户进行身份验证使应用程序合并少受侵入比用户名和密码的安全。

FingerprintManager Api 使用指纹扫描程序的目标设备，以及是否正在运行的 API 级别 23 (Android 6.0) 或更高版本。 Api 位于`Android.Hardware.Fingerprints`命名空间。 Android 支持库 v4 提供指纹针对较旧版本的 Android Api 的版本。 兼容性 Api 位于`Android.Support.v4.Hardware.Fingerprint`命名空间，通过分发[Xamarin.Android.Support.v4 NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。

[FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (及其支持库对应的整型， [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) 是使用扫描硬件的指纹的主类。 此类是围绕管理与硬件本身的交互的系统级别服务的 Android SDK 包装。 它负责启动指纹扫描仪并响应来自扫描程序的反馈。 此类具有只有三个成员具有的非常简单接口：

* **`Authenticate`** &ndash; 此方法将初始化硬件扫描程序，并在后台，等待用户扫描其指纹即可启动该服务。
* **`EnrolledFingerprints`** &ndash; 此属性将返回`true`如果用户已注册的设备的一个或多个指纹。
* **`HardwareDetected`** &ndash; 此属性用于确定设备是否支持指纹扫描。

`FingerprintManager.Authenticate` Android 应用程序使用方法来启动指纹扫描程序。 下面的代码段演示了如何使用支持库兼容性 Api 调用：

```csharp
// context is any Android.Content.Context instance, typically the Activity 
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
fingerprintManager.Authenticate(FingerprintManager.CryptoObject crypto,
                                int flags,
                                CancellationSignal cancel,
                                FingerprintManagerCompat.AuthenticationCallback callback,
                                Handler handler
                               );
```

本指南将讨论如何使用`FingerprintManager`Api 来增强指纹身份验证的 Android 应用程序。 它将介绍如何实例化并创建`CryptoObject`来帮助保护指纹扫描程序的结果。 我们将查看如何应用程序应子类`FingerprintManager.AuthenticationCallback`并响应来自指纹扫描程序的反馈。 最后，我们将了解如何注册 Android 设备或仿真程序上的使用指纹以及如何使用**adb**以模拟指纹扫描。

## <a name="requirements"></a>要求

指纹身份验证需要 Android 6.0 （API 级别 23） 或更高版本和使用指纹扫描程序的设备。 

指纹必须已注册该设备的每个用户都是进行身份验证。 这涉及到设置了锁屏使用密码、 PIN、 轻扫模式或面部识别。 就可以模拟某些 Android 仿真程序中的指纹身份验证功能。  有关这两个主题的详细信息，请参阅[注册指纹](enrolling-fingerprint.md)部分。 






## <a name="related-links"></a>相关链接

- [指纹指南示例应用程序](https://developer.xamarin.com/samples/monodroid/FingerprintGuide/)
- [指纹对话框示例](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [在运行时的请求权限](http://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](http://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [指纹和付款 API （视频）](https://youtu.be/VOn7VrTRlA4)
