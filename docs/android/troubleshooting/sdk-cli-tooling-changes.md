---
title: 对 Android SDK 工具的更改
description: 对 Android SDK 如何管理已安装的 API 级别和 AVDs 的更改。
ms.topic: article
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: a16aa3704d9e0a63cfabde4b620452e7e2a5bf57
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="changes-to-the-android-sdk-tooling"></a>对 Android SDK 工具的更改

_对 Android SDK 如何管理已安装的 API 级别和 AVDs 的更改。_

## <a name="changes-to--android-sdk-tooling"></a>对 Android SDK 工具的更改

在现代版本的 Android SDK 工具，Google 已删除现有 AVD 和 SDK 管理器采用新 CLI （命令行界面） 工具。 前者**android**程序已被删除以及适用于 Mac 和 Xamarin for Visual Studio 的较旧版本的 Visual Studio 中的 GUI （图形用户界面） 管理器将不再起作用过去版本 25.2.5 的 Android SDK 工具。


![Visual Studio 中的 android IDE 菜单](sdk-cli-tooling-changes-images/android-ide-menu.png)

尝试使用**android**通过命令行程序将导致错误消息如下所示：

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

因此，你将需要使用 CLI 工具来管理和更新你的仿真程序和 Android SDK。

### <a name="cli-tools"></a>CLI 工具

Android SDK 工具的命令行接口现在进行以下程序：

#### <a name="sdkmanager"></a>sdkmanager

**中添加：** Android SDK 工具 25.2.3 (2016 年 11 月) 和更高版本。

没有一个名为的新程序**sdkmanager**中**工具/bin** Android sdk 的文件夹。 此工具用于维护命令行处的 Android SDK。 有关使用此工具的详细信息，请参阅[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)。

#### <a name="avdmanager"></a>avdmanager

**中添加：** Android SDK 工具 25.3.0 (2017 年 3 月) 和更高版本。

没有一个名为的新程序**avdmanager**中**工具/bin** Android sdk 的文件夹。 此工具用于维护 AVDs 的 Google Android 仿真程序。 有关使用此工具的详细信息，请参阅[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)。

### <a name="downgrading"></a>降级

可以将降级你**Android SDK 工具**通过安装以前版本的 Android sdk 版本[Android 开发人员网站](https://developer.android.com/studio/index.html)。

### <a name="using-the-old-gui"></a>使用旧的 GUI

你仍可以通过运行使用原始 GUI **android**程序内你**工具**文件夹，只要您位于**Android SDK 工具**版本**25.2.5**或更低。


## <a name="related-links"></a>相关链接

- [Android SDK 安装](~/android/get-started/installation/android-sdk.md)
- [了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)
- [SDK Tools 发行说明 (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
