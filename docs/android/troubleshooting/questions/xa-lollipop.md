---
title: 哪个版本的 Xamarin.Android 添加棒糖形支持？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 065c68a373f67bb352b59dc88ef89daec8b51ef8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>哪个版本的 Xamarin.Android 添加棒糖形支持？

**注意：**针对 Android L 预览的最初编写本指南。

-   [Xamarin.Android 4.17](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.17/)添加 Android L 预览支持。
-   [Xamarin.Android 4.20](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.20/)添加 Android 棒糖形支持。

Xamarin 才会主动支持 Xamarin 工具的最新稳定版本。 提供以下信息"作为-是"为较旧版本的工具。 在 Xamarin 版本上的最新信息，请检查[此处](http://releases.xamarin.com/)。

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Android.jar API 级别 21"中缺少 Android L 预览

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

以下错误消息 （或类似） 可能会出现：

```cmd
Error 1 Could not find android.jar for API Level 21.
```

此消息表示未安装 Android SDK 平台，用于 API 级别 21。 请将其安装在 Android SDK 管理器 (工具 > 打开 Android SDK 管理器...)，或更改你的 Xamarin.Android 项目以面向已安装的 API 版本。

有几个针对此问题的解决方法：

1. 更改你的项目，以便该提供程序针对 API 19 或较低。

2. 文件夹重命名你 android 21 从 android 21 为 android l。 （在最好的情况，这应仅使用作为临时解决问题，并且它可能无法很好地根本。）

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. 临时降级回 Android API 级别 21"L"预览 [1]:

    1.  删除**%LOCALAPPDATA%\\Android\\android sdk\\平台\\android 21** 
    2.  提取 [1] 到**c:\\用户\\<username>\\AppData\\本地\\Android\\android sdk\\平台**创建**android-L**文件夹。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

以下错误消息 （或类似） 可能会出现：

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

这意味着未安装 Android SDK 平台，用于 API 级别 21。请将其安装在 Android SDK 管理器 (工具 > SDK 管理器...)，或更改你的 Xamarin.Android 项目以面向已安装的 API 版本。

有几个针对此问题的解决方法：

1. 更改你的项目，以便该提供程序针对 API 19 或较低。

2. 文件夹重命名你 android 21 从 android 21 为 android l。 （在最好的情况，这应仅使用作为临时解决问题，并且它可能无法很好地根本。）

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. 临时降级回 Android API 级别 21"L"预览 [1]:

    1.  Delete **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2.  提取 [1] 到**/Users/username/Library/Developer/Xamarin/android-sdk-macosx**创建**android-L**文件夹。

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
