---
title: 调试在 Windows 上的 Android 需要哪些 USB 驱动程序？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2018
ms.openlocfilehash: 7b3a4c2f807839897a099959fe3a6ea9ec25df78
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732744"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>调试在 Windows 上的 Android 需要哪些 USB 驱动程序？

## <a name="finding-usb-drivers"></a>查找 USB 驱动程序

若要在 Windows 中; 开发时在 Android 设备上进行调试你需要安装兼容的 USB 驱动程序。 Android SDK 管理器包括"Google USB 驱动程序"默认情况下，添加如下所述的 Nexus 设备的支持： [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

其他设备需要专门发布的设备制造商的 USB 驱动程序。 本指南中包括的最常见的制造商某些链接： [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>替代项

具体取决于 manfacturer，它可能很难跟踪所需的确切 USB 驱动程序。 在包括使用 Android 仿真程序或使用外部测试服务的 Windows 中开发用于测试 Android 应用一些替代项。 其中包括：

- [应用中心测试](https://docs.microsoft.com/appcenter/test-cloud/)-测试在数百个真正 Android 设备上运行的服务的云。

- [适用于 Android 的 Visual Studio 模拟器](https://www.visualstudio.com/en-us/features/msft-android-emulator-vs.aspx)

- [使用 Google Android 仿真程序调试](~/android/deploy-test/debugging/android-sdk-emulator/index.md)

