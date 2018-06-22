---
title: 'Android.Support.v7.AppCompat-未找到资源的给定名称相匹配： attr android: actionModeShareDrawable'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 07655587642c3e1aa94d035e76f6f6758340546d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774891"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat-未找到资源的给定名称相匹配： attr android: actionModeShareDrawable

1. 请确保下载最新的其他功能，以及 Android 5.0 (API 21) SDK 通过 Android SDK 管理器。

2. 确保您正在使用设置为 21 compileSdkVersion 编译你的应用程序。 你可以选择性地为 21 以及设置 targetSdkVersion。

3. 如果你需要一个以前的版本，如 API 19，请下载 Nuget 页上找到的各自版本：

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

*请注意*： 如果你手动安装此通过程序包管理器控制台，请确保还安装相同版本的 Xamarin.Android.Support.v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

堆栈溢出参考： [http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>另请参阅

- [应安装哪些 Android SDK 包？](~/android/troubleshooting/questions/install-android-sdk-packages.md)

