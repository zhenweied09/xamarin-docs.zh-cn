---
title: 命令行仿真器
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: b1ca1c2b441a9c9ca26de5668f318312bea156a3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762066"
---
# <a name="command-line-emulator"></a>命令行仿真器


## <a name="running-the-android-emulator-from-the-command-line"></a>从命令行运行 Android 仿真器

要从命令行运行 Android 仿真器，可以使用 Android SDK 提供的“仿真器”工具。 此工具可用于从 OS X 上的终端或 Windows 计算机上的命令提示符运行仿真器。

要启动特定的 Android 仿真器，请从 Android SDK 位置的 tools 目录（例如 C:\android-sdk-windows\tools）运行以下命令：

在 Windows 上

```cmd
emulator.exe -avd NameOfYourEmulator -partition-size 512
```

在 macOS 上

```bash
./emulator -avd NameOfYourEmulator -partition-size 512
```

需要分区大小的原因是为了让仿真器具有足够的空间，以在仿真器上安装 Xamarin.Android 平台，因为默认情况下仿真器的大小很小。

有关额外参数的详细信息，可访问此处的 Android 站点 - [http://developer.android.com/guide/developing/tools/emulator.html](http://developer.android.com/guide/developing/tools/emulator.html)
