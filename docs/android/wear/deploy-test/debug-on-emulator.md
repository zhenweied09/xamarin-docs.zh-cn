---
title: 调试的仿真程序上的 Android 磨损
description: 这些文章介绍如何调试的仿真程序上的 Xamarin.Android 磨损应用程序。
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2018
ms.openlocfilehash: baa8df87caf2c05d7b6202d5160c930e51656e10
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36934973"
---
# <a name="debug-android-wear-on-an-emulator"></a>调试的仿真程序上的 Android 磨损

_这些文章介绍如何调试的仿真程序上的 Xamarin.Android 磨损应用程序。_

## <a name="debug-wear-on-emulator-overview"></a>调试磨损仿真程序概述

开发 Android 磨损应用程序需要运行该应用程序，请在物理硬件上或使用仿真程序或模拟器。 使用硬件是最好的方法，但并不总是最实用的方法。 在许多情况下，它可以更简单且更具成本效益以模拟/模拟 Android 磨损硬件使用仿真程序，如下所述。 如果你尚不熟悉此过程的部署和运行 Android 磨损应用程序，请参阅[Hello，带](~/android/wear/get-started/hello-wear.md)。

## <a name="configure-the-android-emulator"></a>配置 Android 仿真程序

若要在模拟器上运行你磨损的应用程序，必须安装 Android SDK Android 仿真程序，并将其配置为 Android 损耗。 有关整体的 Android SDK 仿真程序安装和配置信息，请参阅[Android 仿真程序安装程序](~/android/get-started/installation/android-emulator/index.md)。

当创建磨损虚拟设备时，选择 Android 磨损设备配置文件 (如**Android 磨损正方形**)。 为提高性能，使用磨损**x86** CPU/ABI 在此示例中所示：

[![示例磨损虚拟设备的配置](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)


## <a name="launch-the-wear-virtual-device"></a>启动磨损虚拟设备 

创建 Android 磨损虚拟设备后，你可以选择它从在 IDE 中的设备下拉列表菜单开始调试之前。 如果你的虚拟设备不可用的设备下拉列表中，验证你的项目是否 Android*磨损*应用程序项目 （不 Android 应用程序项目） 和，其目标 API 级别设置为相同的 API 级别与虚拟设备。 例如：

[![在 Visual Studio 设备菜单中选择磨损 AVD](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Android 仿真程序启动后，则 Xamarin.Android 将磨损应用部署到仿真程序。 仿真器会使用配置的虚拟设备映像运行此应用。

不要感到惊讶如果看到此内容 （或另一个间隙屏幕） 在第一个。 监视仿真程序可能需要一段时间才能启动： 

![观看仿真程序将显示只需一分钟...](debug-on-emulator-images/please-wait.png)

可以一直运行仿真器；无需关闭仿真器并在每次运行应用时重启。

 
## <a name="summary"></a>总结
 
本指南介绍如何配置于磨损开发的 Android 仿真程序和启动调试的磨损虚拟设备。
