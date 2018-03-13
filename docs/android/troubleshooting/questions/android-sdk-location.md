---
title: "其中设置我的 Android SDK 位置？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 11/16/2017
ms.openlocfilehash: 0113cc15bf1de5e0e668b05c2b0288a6ead141b5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>其中设置我的 Android SDK 位置？

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Visual Studio 中，导航到**工具 > 选项 > Xamarin > Android 设置**可以查看和设置为 Android SDK 的位置：

[![在首选项中的示例位置选项卡](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

每个路径的默认位置是，如下所示：

- Java 开发工具包的位置： 

    **C:\\程序文件\\Java\\jdk1.8.0_131**

- Android SDK 的位置： 

    C:\\Program Files (x86)\\Android\\android-sdk

- Android NDK 位置： 

    **C:\\ProgramData\\Microsoft\\AndroidNDK64\\android-ndk-r13b**

请注意，NDK 的版本号可能不同。 而不是，如**android ndk r13b**，它可能是早期版本，如**android ndk r10e**。

若要设置为 Android SDK 的位置，输入到 Android SDK 目录的完整路径**Android SDK 的位置**框。 你可以导航到文件资源管理器中的 Android SDK 位置、 将路径从地址栏中，复制和粘贴到此路径**Android SDK 的位置**框。
例如，如果你的 Android SDK 位置位于**c:\\用户\\用户名\\AppData\\本地\\Android\\Sdk**，清除中的旧路径**Android SDK 的位置**框中，粘贴在此路径中，并单击**确定**。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在 Visual Studio for Mac 中，导航到**首选项 > 项目 > SDK 位置 > Android**。 在**Android**页上，单击**位置**选项卡以查看和设置为 SDK 的位置：

[![在首选项中的示例位置选项卡](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

每个路径的默认位置是，如下所示：

- Android SDK 的位置： 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Android NDK 位置： 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Java SDK (JDK) 的位置： 

    **/usr**

请注意，NDK 的版本号可能不同。 而不是，如**android ndk r14b**，它可能是早期版本，如**android ndk r10e**。

若要设置为 Android SDK 的位置，输入到 Android SDK 目录的完整路径**Android SDK 的位置**框。 您可以选择的 Android SDK 文件夹中查找工具，按**CTRL +&#8984;+ I**要查看文件夹的信息，请单击并拖动右侧的路径**其中：**，复制，然后将其粘贴到**Android SDK位置**框中**位置**选项卡。例如，如果你的 Android SDK 位置位于**~/Library/Developer/Android/Sdk**，清除中的旧路径**Android SDK 的位置**框中，粘贴在此路径中，并单击**确定**.

-----
