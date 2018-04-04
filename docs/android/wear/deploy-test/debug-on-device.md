---
title: 在磨损设备上进行调试
description: 本文介绍如何调试磨损设备上的 Xamarin.Android 磨损应用程序。
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 3f3143dcda4017bbabfbd34a58a40665beea6f75
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="debug-on-a-wear-device"></a>在磨损设备上进行调试

_本文介绍如何调试磨损设备上的 Xamarin.Android 磨损应用程序。_


## <a name="overview"></a>概述

如果你没有如 Android 磨损 Smartwatch Android 磨损设备，你可以在运行应用而不是使用仿真程序在设备上。 (如果你尚不熟悉此过程的部署和运行 Android 磨损应用程序，请参阅[Hello，带](~/android/wear/get-started/hello-wear.md)。)

## <a name="prepare-the-wear-device"></a>准备磨损设备：

使用以下步骤启用 Android 磨损设备上调试：

1.  打开**设置**Android 磨损设备上的菜单。

2.  滚动到底部的菜单和 tap**有关**。

3.  7 次点击的生成号。

4.  上**设置**菜单上，点击**开发人员选项**。

5.  确认**ADB 调试**已启用。


## <a name="debugging-over-usb"></a>通过 USB 调试

如果你的磨损设备有 USB 端口，你可以将磨损设备连接到你的计算机、 部署到它，并运行/调试应用程序将使用 Android 手机 (有关详细信息，请参阅[在设备上进行调试](~/android/deploy-test/debugging/debug-on-device.md))。


## <a name="debugging-over-bluetooth"></a>通过蓝牙调试

如果你磨损设备没有 USB 端口，你可以将应用部署到磨损设备通过蓝牙通过路由到的 Android 手机，连接到你的计算机的应用程序的调试输出。 

### <a name="prepare-your-phone"></a>准备您的电话

使用以下步骤来使到磨损设备的蓝牙连接准备你的手机： 

1.  如果尚未这样做，将设置为你手机上以进行 Xamarin.Android 开发中所述[设置向上用于开发的设备](~/android/get-started/installation/set-up-device-for-development.md)。

2.  下载并安装免费[Android 磨损](https://play.google.com/store/apps/details?id=com.google.android.wearable.app)来自 Google Play 商店的应用。

### <a name="connect-the-device"></a>将设备连接

使用以下步骤将磨损设备连接到你的手机：

1.  在手机上，将充当蓝牙中间 （以上配置），启动 Android 磨损应用程序。 

2.  点击**设置**图标。

3.  启用**调试通过蓝牙**。 你应看到 Android 磨损应用屏幕上显示的以下状态：

        Host: disconnected
        Target: connected

4.  通过 USB 连接到你的计算机的电话。 你在计算机上，输入以下命令：

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    如果端口 4444 不可用，你可以使用有权访问的任何其他可用的端口。 

    **请注意**： 如果你重新启动 Visual Studio 或 Visual Studio for Mac，你必须运行以下命令以设置到磨损设备的连接。

5.  磨损设备提示你时，确认你允许**ADB 调试**。 在 Android 磨损应用中，你应看到状态更改为：

        Host: connected
        Target: connected

6.  完成上述步骤后，运行`adb devices`显示 phone 和 Android 磨损设备的状态：

        List of devices attached
        127.0.0.1:4444    device
        019ad61df0a69399  device

此时，你可以将你的应用程序部署到磨损设备。

<a name="screenshots" />

### <a name="taking-screenshots"></a>拍摄屏幕快照

您可以通过输入以下命令来执行磨损设备的屏幕快照： 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

将屏幕快照复制到你的计算机中，通过输入以下命令：

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

通过输入以下命令来删除设备上的屏幕截图：

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```


### <a name="uninstalling-an-app"></a>卸载的应用

你可以从磨损设备卸载应用程序，通过输入以下命令：

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

例如，若要删除应用程序与包名称`com.xamarin.weartest`，输入以下命令：

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

有关调试 Android 磨损设备通过蓝牙的详细信息，请参阅[调试通过蓝牙](https://developer.android.com/training/wearables/apps/bt-debugging.html)。


## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>调试包含助理 phone 应用程序的磨损应用程序

Android 磨损应用打包使用 Google Play 上的分发的助理 Android 手机应用 (有关详细信息，请参阅[使用打包](~/android/wear/deploy-test/packaging.md))。 但是，你仍开发磨损应用程序和其助理应用单独。 当你释放通过 Google Play 应用商店应用程序时，则将与辅助应用程序一起打包磨损应用，并将其自动安装尽可能。

若要调试磨损应用程序与辅助应用程序： 

1.  生成并部署到电话的辅助应用程序。

2.  右键单击磨损项目并将其设置为默认启动项目。

3.  将磨损项目部署到 wearable 设备。

4.  运行和调试磨损应用在设备上。

 
## <a name="summary"></a>总结

本文介绍如何将从 Visual Studio 中通过蓝牙、 磨损调试 Android 磨损设备配置以及如何调试磨损应用包含助理 phone 应用。 它还提供用于调试通过蓝牙的磨损应用了常见的调试提示。
