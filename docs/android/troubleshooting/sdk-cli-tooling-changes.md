---
title: 对 Android SDK 工具的更改
description: 对 Android SDK 如何管理已安装的 API 级别和 AVDs 的更改。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2018
ms.openlocfilehash: 4e808736fd92fa40ecbf0c24938c0fedd7afcff9
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935446"
---
# <a name="changes-to-the-android-sdk-tooling"></a>对 Android SDK 工具的更改

_对 Android SDK 如何管理已安装的 API 级别和 AVDs 的更改。_

## <a name="changes-to-android-sdk-tooling"></a>对 Android SDK 工具的更改

中的 Android SDK 工具的最新版本，Google 已删除现有 AVD 和 SDK 管理器支持新的 CLI （命令行界面） 工具。 **Android**程序已被删除以及适用于 Mac 和较旧版本的 Visual Studio Tools for Xamarin 的 Visual Studio 中的 Google GUI （图形用户界面） 管理器将不再起作用过去版本 25.2.5 的 Android SDK 工具。 例如，尝试使用**android**通过命令行程序将导致错误消息如下所示：

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

以下各节说明如何管理的 Android SDK 和 Android 虚拟设备使用 Android SDK 25.3.0 及更高版本。

### <a name="ui-tools"></a>用户界面工具

Visual Studio 和适用于 Mac 的 Visual Studio 现在提供已停止使用基于 Google GUI 的管理器的 Xamarin 替换：

-   若要下载 Android SDK 工具、 平台和开发 Xamarin.Android 应用程序需要其他组件，使用[Xamarin Android SDK 管理器](~/android/get-started/installation/android-sdk.md)而不是旧 Google SDK 管理器。

-   若要创建和配置 Android 虚拟设备，使用[Android 设备管理器](~/android/get-started/installation/android-emulator/device-manager.md)而不是旧 Google 模拟器管理器。

这些工具在功能上等效于基于 Google GUI 的管理器，它们可替代。

### <a name="cli-tools"></a>CLI 工具

或者，可以使用 CLI 工具来管理和更新你的仿真程序和 Android SDK。 Android SDK 工具的命令行接口现在进行以下程序：

#### <a name="sdkmanager"></a>sdkmanager

**中添加：** Android SDK 工具 25.2.3 (2016 年 11 月) 和更高版本。

没有一个名为的新程序**sdkmanager**中**工具/bin** Android sdk 的文件夹。 此工具用于维护命令行处的 Android SDK。 有关使用此工具的详细信息，请参阅[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)。

#### <a name="avdmanager"></a>avdmanager

**中添加：** Android SDK 工具 25.3.0 (2017 年 3 月) 和更高版本。

没有一个名为的新程序**avdmanager**中**工具/bin** Android sdk 的文件夹。 此工具用于 Android 的仿真程序的维护 AVDs。 有关使用此工具的详细信息，请参阅[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)。

### <a name="downgrading"></a>降级

可以将降级你**Android SDK 工具**通过安装以前版本的 Android sdk 版本[Android 开发人员网站](https://developer.android.com/studio/index.html)。

### <a name="using-the-old-gui"></a>使用旧的 GUI

你仍可以通过运行使用原始 GUI **android**程序内你**工具**文件夹，只要您位于**Android SDK 工具**版本**25.2.5**或更低。


## <a name="related-links"></a>相关链接

- [Android SDK 安装](~/android/get-started/installation/android-sdk.md)
- [Android 设备管理器](~/android/get-started/installation/android-emulator/device-manager.md)
- [了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)
- [SDK Tools 发行说明 (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
