---
title: 设置用于 Xamarin.Android 的 Android SDK
description: Visual Studio 包含一个取代 Google 的独立 SDK 管理器的 Android SDK 管理器。 本指南说明如何使用 SDK 管理器下载 Android SDK 工具、平台以及开发 Xamarin.Android 应用所需的其他组件。
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/02/2018
ms.openlocfilehash: 0af0ef56630103498041ad61f7c5ce900358b055
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732848"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>设置用于 Xamarin.Android 的 Android SDK

_Visual Studio 包含一个取代 Google 的独立 SDK 管理器的 Android SDK 管理器。本指南说明如何使用 SDK 管理器下载 Android SDK 工具、平台以及开发 Xamarin.Android 应用所需的其他组件。_


## <a name="overview"></a>概述

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

本指南说明如何在 Windows 上（或[针对 Mac](?tabs=vsmac)）安装和使用用于 Visual Studio 的 Xamarin Android SDK 管理器。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

本指南说明如何安装和使用用于 Visual Studio for Mac（或[适用于 Windows](?tabs=vswin)）的 Xamarin Android SDK 管理器。

> [!NOTE]
> 本指南仅适用于 Visual Studio 2017 和 Visual Studio for Mac。  

-----

Xamarin Android SDK 管理器可帮助你下载开发 Xamarin.Android 应用所需的最新 Android 组件。
它取代了已被弃用的 Google 的独立 SDK 管理器。

为什么要使用 Xamarin Android SDK 管理器，而不使用 Android SDK 中包含的 SDK 管理器？ 在 Android SDK Tools 包的 25.2.3 版本中，Google 引入了一个新的工具来维护 Android SDK。 此新工具 **[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)** 是一个命令行实用工具，取代了用于 Android SDK 的独立 UI 管理器。 因此，如果更新到 SDK Tools 版本 26.0.1（Android 8.0 所需）或更高版本，并且想继续通过 UI 接口管理 Android SDK，则必须使用 Xamarin Android SDK 管理器。

## <a name="requirements"></a>惠?

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要使用 Xamarin Android SDK 管理器，需要以下工具：

- Visual Studio 2017（Community、Professional 或 Enterprise 版本）。 需要 Visual Studio 2017 版本 15.5 或更高版本。

- Visual Studio Tools for Xamarin 版本 4.5.0 或更高版本。 

Xamarin Android SDK 管理器与 Visual Studio 不兼容
2015. Visual Studio 2015 的用户应使用 Android SDK 中由 Google 提供的 SDK 管理器工具。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

-   Visual Studio for Mac 7.0.0.3146（或更高版本）。

-----

Xamarin Android SDK 管理器还需要 Java 开发工具包（此工具包自动安装在 Xamarin.Android 中）。
Xamarin.Android 使用[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)，这是在为 API 级别 24 或更高级别进行开发时所必需的（JDK 8 还支持低于 24 的 API 级别）。 如果专门为 API 级别 23 或更低级别进行开发，可以继续使用 [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)。

> [!IMPORTANT]
> Xamarin.Android 不支持 JDK 9。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="installation"></a>安装

可以在安装时将 Xamarin SDK 管理器添加到 Visual Studio 2017 中。 在安装 Visual Studio 时，单击“单个组件”并向下滚动到“开发活动”部分。
启用“Xamarin SDK 管理器”（如果尚未选中）：

![从“单个组件”中启用“Xamarin SDK 管理器”](android-sdk-images/win/01-sdk-manager-install.png)

如果已安装 Visual Studio 2017，请参阅[修改 Visual Studio 2017](https://docs.microsoft.com/en-us/visualstudio/install/modify-visual-studio) 以查看如何修改 Visual Studio 的说明，然后按照以上过程启用 Xamarin SDK 管理器。 如果看到一条更新 SDK 管理器的提示，可以使用此相同的过程安装 Xamarin SDK 管理器。

单击“工具”>“Android”>“Android SDK 管理器”（如下所述）后，将启动 Xamarin Android SDK 管理器，而不是 Google Android SDK 管理器。 如果使用支持 Google 的独立 Android SDK 管理器的 Android SDK 早期版本，安装 Xamarin Android SDK 管理器将不会创建冲突 &ndash; 你仍然可以从 Visual Studio 外部启动独立的 Google SDK 管理器来管理 Android SDK。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
 
-----

 
## <a name="sdk-manager"></a>SDK 管理器 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要在 Visual Studio 中启动 SDK 管理器，请单击“工具”>“Android”>“Android SDK 管理器”：

[![“Android SDK 管理器”菜单项的位置](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

“Xamarin Android SDK 管理器”会在“Android SDK 和工具”屏幕中打开。 此屏幕上有两个选项卡 &ndash;“平台”和“工具”：

[![“平台”选项卡中打开的 Android SDK 管理器的屏幕截图](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

下面的部分将对“Android SDK 和工具”屏幕进行更详细的介绍。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要在 Visual Studio for Mac 中启动 SDK 管理器，请单击“工具”>“SDK 管理器”：
 
![“Android SDK 管理器”菜单项的位置](android-sdk-images/mac/sdkmanager-01.png )

“Android SDK 管理器”会在“首选项窗口”中打开，此窗口中包含“平台”、“工具”和“位置”这三个选项卡：

![“平台”选项卡中打开的 Android SDK 管理器的屏幕截图](android-sdk-images/mac/sdkmanager-02.png)

下面的部分将对 Xamarin Android SDK 管理器的各个选项卡进行介绍。

-----



# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="android-sdk-location"></a>Android SDK 位置

Android SDK 位置是在“Android SDK 和工具”屏幕的顶部进行配置的，如上图中所示。 必须正确配置此位置，这样“平台”和“工具”选项卡才能正常工作。 出于下面一个或多个原因，可能需要设置 Android SDK 的位置：

1. Xamarin SDK 管理器无法定位 Android SDK。 

2. 你已在备用（非默认）位置安装了 Android SDK。 

若要设置 Android SDK 的位置，请单击“Android SDK 位置”最右侧的“&hellip;”按钮。 这将打开“浏览文件夹”对话框，用于导航到 Android SDK 的位置。 在下面的屏幕截图中，已选中“Program Files (x86)\\Android”下面的 Android SDK：

![定位 android sdk 的 Windows“浏览文件夹”的屏幕截图](android-sdk-images/win/05-browse-for-folder.png)

单击“确定”后，Xamarin Android SDK 管理器将管理安装在所选位置的 Android SDK。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

### <a name="locations-tab"></a>“位置”选项卡

“位置”选项卡具有用于配置 Android SDK、Android NDK 和 Java SDK (JDK) 位置的三个设置。 必须正确配置这些位置，这样“平台”和“工具”选项卡才能正常工作。

SDK 管理器启动后，它会自动确定每个已安装包的路径，并通过在此路径旁放置一个绿色的复选标记图标以指示“已找到”路径：

![“位置”选项卡的屏幕截图](android-sdk-images/mac/sdkmanager-03.png)

单击“重置为默认值”按钮，使 SDK 管理器在 SDK、NDK 和 JDK 的默认位置查找它们。 

通常情况下，使用“位置”选项卡来修改 Android SDK 和/或 Java JDK 的位置。 不需要安装 NDK 来开发 Xamarin.Android 应用 &ndash; 只有当需要使用 C 和 C++ 等本机代码的语言开发应用的一部分时才使用 NDK。

-----


### <a name="tools-tab"></a>“工具”选项卡

“工具”选项卡显示“工具”和“附加程序”的列表。 使用此选项卡以安装 Android SDK 工具、平台工具和生成工具。
此外，还可以安装 Android Emulator、低级别调试器 (LLDB)、NDK、HAXM 加速和 Google Play 库。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

例如，若要下载 Google Android Emulator 包，请单击“Android Emulator”旁的复选标记，然后单击“应用更改”按钮：

[![从“工具”选项卡中安装 Android Emulator](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

例如，若要下载 Google Android Emulator 包，请单击“Android Emulator”旁的复选标记，然后单击“安装更新”按钮：

![从“工具”选项卡中安装 Android Emulator](android-sdk-images/mac/sdkmanager-08.png)

-----


可能会出现带有“一些组件可进行更新。是否要立即更新它们?”消息的对话框。 单击 **“是”**。 接着会出现“许可证接受”对话框：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![“许可证接受”屏幕](android-sdk-images/win/07-license-acceptance.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![“许可证接受”屏幕](android-sdk-images/mac/sdkmanager-09.png)

-----

如果接受这些条款和条件，请单击“接受”。 在窗口底部，有一个进度栏会指示下载和安装进度。 安装完成后，“工具”选项卡将显示已安装所选的工具和附加程序。



### <a name="platforms-tab"></a>“平台”选项卡

“平台”选项卡显示平台 SDK 版本以及适用于每个平台的其他资源（例如系统映像）的列表。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![“平台”窗格的屏幕截图](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![“平台”窗格的屏幕截图](android-sdk-images/mac/sdkmanager-11.png)

-----

此屏幕列出了 Android 版本（例如“Android 7.0”）、代码名称（“Nougat”）、API 级别（例如“24”）以及状态（如果已安装平台，则为“已安装”）。 使用“平台”选项卡为想要面向的 Android API 级别安装组件（有关 Android 版本和 API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)）。

如果某个平台的所有组件已安装，该平台名称旁边将显示一个复选标记。 如果某个平台的组件并非已全部安装，该平台的此框则会被填充。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

可以通过单击某个平台左侧的“+”框展开此平台以查看其组件（以及已安装的组件）。
单击“-”取消展开某个平台的组件列表。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

可以通过单击某个平台左侧的“箭头”展开此平台以查看其组件（以及已安装的组件）。
单击“向下箭头”以取消展开某个平台的组件列表。

-----

若要将另一个平台添加到 SDK，请单击此平台旁边的框 - 直到显示复选标记 - 以安装其所有组件，然后单击“应用更改”：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![将 Android 7.1 Nougat 组件添加到 Android SDK 的示例](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![将 Android 4.4 组件添加到 Android SDK 的示例](android-sdk-images/mac/sdkmanager-12.png)

-----

若要仅安装 SDK，请单击一次此平台旁边的框。 然后可以选择所需的任何单个组件：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![添加某些 Android 7.1 组件的示例](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![添加某些 Android 4.4 组件的示例](android-sdk-images/mac/sdkmanager-13.png)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

请注意，要安装的组件数量显示在“应用更改”按钮旁边。 在上面的示例中，有 6 个组件准备安装。 单击“应用更改”按钮后，将看到“许可证接受”屏幕：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

请注意，要安装的组件数量显示在“应用更改”按钮旁边。 单击“应用更改”按钮后，将看到“许可证接受”屏幕：

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![“平台”选项卡的“许可证接受”对话框](android-sdk-images/win/11-license-screen.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![“平台”选项卡的“许可证接受”对话框](android-sdk-images/mac/sdkmanager-14.png)

-----

如果接受这些条款和条件，请单击“接受”。 如果有多个组件要安装，则可能会多次看到此对话框。 在窗口底部，有一个进度栏会指示下载和安装进度。 下载和安装过程完成后（可能需要好几分钟的时间，具体取决于需要下载的组件数量），已添加的组件会被复选标记标记出来并被列为“已安装”。

现在可以针对最新、最高的 Android API 级别开发你的应用了！


 
## <a name="summary"></a>总结

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

本指南说明了如何在 Visual Studio 中安装和使用 Xamarin Android SDK 管理器工具。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

本指南说明了如何在 Visual Studio for Mac 中使用 Xamarin Android SDK 管理器工具。

-----


## <a name="related-links"></a>相关链接

- [对 Android SDK 工具的更改](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)
- [SDK Tools 发行说明 (Google)](https://developer.android.com/studiohttps://developer.xamarin.com/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
