---
title: 如何更新 Java 开发工具包 (JDK) 版本？
description: 这篇文章演示了如何更新 Windows 和 mac。 上的 Java 开发工具包 (JDK) 版本
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: dcfc5e406e60ac72fb1ca1e9cfb0395d17074b2c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>如何更新 Java 开发工具包 (JDK) 版本？

_这篇文章演示了如何更新 Windows 和 mac。 上的 Java 开发工具包 (JDK) 版本_

## <a name="overview"></a>概述

Xamarin.Android 使用 Java 开发工具包 (JDK) 将与用于构建 Android 应用和运行 Android 的设计器 Android SDK 集成。 最新版本的 Android sdk (API 24 和更高版本) 需要 JDK 8 (1.8)。 如果尚未更新为 JDK 8，请执行以下步骤来安装和启用它：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  从下载 JDK 8 (1.8) [Oracle 网站](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![屏幕快照的 jdk 下载 Oracle 网站上页面](update-jdk-images/image1.png)

2.  选取要允许的呈现的 64 位版本[自定义控件](https://developer.xamarin.com/releases/vs/xamarin.vs_4/xamarin.vs_4.2/#androiddesignercustomcontrols)Xamarin Android 设计器中：

    ![选择要从 JDK 下载页面中下载的 Windows x64 JDK 包](update-jdk-images/image2.png)

3.  运行.exe 和安装**开发工具**:

    ![安装在 JDK 安装程序中的开发工具](update-jdk-images/image3.png)

4.  打开 Visual Studio 并更新**Java 开发工具包位置**以指向新 JDK 下**工具 > 选项 > Xamarin > Android 设置 > Java 开发工具包的位置 > 更改**:

    ![在 IDE 选项 Android 设置页中的 JDK 的路径设置](update-jdk-images/image4.png)

请确保在更新位置后重新启动 Visual Studio。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  从下载 JDK 8 (1.8) [Oracle 网站](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![屏幕快照的 jdk 下载 Oracle 网站上页面](update-jdk-images/image1.png)

2.  打开.dmg 文件并运行.pkg 安装程序：

    ![在 macOS 上运行 JDK 安装程序](update-jdk-images/image5.png)

Mac OS 时会自动将新的 JDK 版本设置为默认值通过更新**/System/Library/Frameworks/JavaVM.framework/Versions/Current**。 您然后可以仔细检查， **Java SDK (JDK)**位置设置为预期的默认**/usr**下**Visual Studio for Mac > 首选项 > 项目 > SDK 位置 >Android > Java SDK (JDK) > 位置**:

![在 Android SDK 位置页中设置的 JDK 位置](update-jdk-images/image6.png)

-----

