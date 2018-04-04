---
title: 注册指纹
description: 如何设置了锁屏和注册 Android 设备或仿真程序上的使用指纹。
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 4faee5decb102d17d9a270b96cef4a12fc9dbef4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="enrolling-a-fingerprint"></a>注册指纹

## <a name="enrolling-a-fingerprint-overview"></a>注册的指纹概述

才可能发生 Android 应用程序以利用指纹身份验证，如果已使用指纹身份验证配置该设备。 本指南将讨论如何注册 Android 设备或仿真程序上的使用指纹。 仿真程序没有实际的硬件执行指纹扫描速度，但是可以模拟指纹扫描 Android 调试桥接 （如下所述） 的帮助。  本指南将讨论如何启用 Android 设备上的屏幕锁定并注册用于身份验证的指纹。

## <a name="requirements"></a>要求

若要注册指纹，必须具有一个 Android 设备或仿真程序运行 API 级别 23 (Android 6.0)。

使用的 Android 调试桥接 (ADB) 需要熟悉命令提示符下，与**adb**可执行文件必须位于你 Bash，PowerShell，路径或命令提示环境。

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>配置屏幕锁定和注册指纹 

若要设置屏幕锁定，执行以下步骤：

1. 转到**设置 > 安全性**，然后选择**屏幕锁定**:

    ![在安全屏幕上的屏幕锁定选定内容的位置](enrolling-fingerprint-images/testing-01.png)

2. 出现的下一个屏幕将允许你选择和配置的屏幕锁定安全方法之一： 

    ![选择轻扫、 模式、 PIN 或密码](enrolling-fingerprint-images/testing-02.png)

   选择并填写可用屏幕锁定方法之一。

3. Screenlock 配置后，返回到**设置 > 安全性**页，选择**指纹**:

    ![在安全屏幕上的指纹选定范围的位置](enrolling-fingerprint-images/testing-03.png)

4. 在这里，请执行用于将指纹添加到设备的序列：

    [![将指纹添加到设备的屏幕截图的序列](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. 在最后一个屏幕会提示你将手指放置在指纹扫描仪上： 

    ![可以手指置于传感器提示的屏幕](enrolling-fingerprint-images/testing-05.png)

    如果使用 Android 设备，完成该过程通过触摸到扫描程序手指。 
    
    
### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>模拟的仿真程序上的指纹扫描

上一个 Android 仿真程序，则可以通过使用 Android 调试桥模拟指纹扫描。 在 OS X 启动终端会话中的 Windows 上启动命令提示符或 Powershell 会话并运行`adb`:

```shell
$ adb -e emu finger touch 1
```

值**1**是_手指\_id_的上方的手指已"扫描"。 它是为每个虚拟指纹分配一个唯一的整数。 将来当你运行该应用可以运行此相同 ADB 每次仿真程序的命令将提示你为指纹，你可以运行`adb`命令并将其传递_手指\_id_以模拟指纹扫描.

指纹扫描完成后，Android 将通知你，已添加指纹：  

![屏幕显示指纹添加 ！](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>总结 

本指南介绍如何设置了锁屏和注册 Android 设备上或在 Android 仿真程序中的指纹。 

