---
title: "指纹身份验证指南"
ms.topic: article
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 20defea58ec0c09becd5a3cec1961806cb0acc1d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="fingerprint-authentication-guidance"></a>指纹身份验证指南

## <a name="fingerprint-authentication-guidance"></a>指纹身份验证指南

既然我们已了解概念和 Api 周围 Android 6.0 指纹身份验证，让我们讨论指纹 Api 用于一些常规建议。

1. **使用 Android 支持库 v4 兼容性 Api** &ndash;这可通过从代码中删除 API 检查简化应用程序代码，并允许应用程序以面向最设备可能。
2. **提供替代项为指纹身份验证**&ndash;指纹身份验证是一种极佳、 快速的方法，应用程序用户进行身份验证，但是，它不能假定它将始终工作或可用。 很可能，指纹扫描程序可能会失败，小视窗可能变脏、 是用户可能未配置设备使用指纹身份验证或指纹由于进入缺失。 还有可能用户可能不希望你的应用程序中使用指纹身份验证。 出于这些原因，Android 应用程序应提供用户名和密码等一个备用身份验证过程。
3. **使用 Google 的指纹图标**&ndash;所有应用程序应使用 Google 提供相同的指纹图标。 使用标准图标轻松 Android 用户识别应用程序中使用指纹身份验证的位置： 
    
    ![Android 指纹图标](summary-images/ic-fp-40px.png)
    
4. **通知用户**&ndash;应用程序应显示某种类型的通知向用户指纹扫描程序处于活动状态和等待触摸或轻扫。 

## <a name="summary"></a>摘要

指纹身份验证是一种很好的方法来允许一个 Xamarin.Android 应用程序来快速验证用户，使用户更轻松地与敏感的功能进行交互，如应用内购买。 本指南讨论的概念和合并 Xamarin.Android 应用程序中的 API 的 Android 6.0 指纹所需的代码。

首先，我们讨论了 API 的本身，指纹`FingerprintManager`(和`FingerprintManagerCompat`)。 我们探讨了如何`FingerprintManager.AuthenticationCallbacks`必须由应用程序扩展抽象类，并将其用作指纹硬件和应用程序本身的中介。 然后，我们探讨了如何验证使用 Java 的指纹扫描程序结果的完整性`Cipher`对象。 最后，我们接触到了有点通过描述如何注册设备上的使用指纹，并使用来测试**adb**来模拟在模拟器上的指纹轻扫。 

如果你尚未这样做，你应查看[示例应用程序](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)附带本指南。 [指纹对话框示例](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)到 Xamarin.Android 移植通过 Java 和提供有关如何将指纹身份验证添加到 Android 应用程序的另一个示例。



## <a name="related-links"></a>相关链接

- [指纹指南示例应用程序](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [指纹对话框示例](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [指纹图标](https://developer.android.comhttps://developer.xamarin.com/samples/FingerprintDialog/res/drawable-hdpi/ic_fp_40px.html)
