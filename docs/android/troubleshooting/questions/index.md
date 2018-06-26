---
title: 常见问题
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2018
ms.openlocfilehash: bbb1e92596fe69246a608eb0758e789f5b99e954
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935459"
---
# <a name="frequently-asked-questions"></a>常见问题

## <a name="installation--setup"></a>安装和设置

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[应安装哪些 Android SDK 包？](install-android-sdk-packages.md)

安装 Android SDK 不会自动包括用于开发的所有最小所需的包。 各开发人员需要不同，本指南讨论通常需要使用 Xamarin.Android 进行开发的包。

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[可以在哪里设置 Android SDK 位置？](android-sdk-location.md)

本指南介绍的 Android sdk，也适用于大多数设置; 默认设置以及如何根据需要更改用于 Mac 或 Visual Studio 的 Visual Studio 中的这些默认值。

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[如何更新 Java Development Kit (JDK) 版本？](update-jdk.md)

这篇文章演示了如何更新 Windows 和 mac。 上的 Java 开发工具包 (JDK) 版本

### <a name="can-i-use-java-development-kit-jdk-version-9jdk9-errorsmd"></a>[可否使用 Java Development Kit (JDK) 版本 9？](jdk9-errors.md)

Xamarin.Android 需要 JDK 8，而不是 JDK 9。 本文列出了一些常见的错误消息，你可能会看到已安装 JDK 9，以及有关签入的 JDK 版本说明。


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[如何手动安装 Xamarin.Android.Support 包所需的 Android 支持库？](install-android-support-library.md)

本指南提供有关安装的示例步骤`Xamarin.Android.Support.v4`支持库在 Windows 和 mac。

### <a name="how-do-i-install-google-play-services-in-an-emulatorinstall-gpsmd"></a>[如何在仿真器中安装 Google Play Services？](install-gps.md)

此指南链接到 Visual Studio Android 仿真程序中安装 Google Play 服务的信息。

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[在 Windows 上调试 Android 需要哪些 USB 驱动程序？](android-drivers-debug-windows.md)

若要在 Windows 中; 开发时在 Android 设备上进行调试你需要安装兼容的 USB 驱动程序。 Android SDK 管理器默认情况下，增加了对 Nexus 设备支持包括"Google USB 驱动程序"。
其他设备需要专门发布的设备制造商的 USB 驱动程序。 本指南提供有关查找这些驱动程序以及为替代的测试方法的信息。

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[是否可以从 Windows VM 连接到在 Mac 上运行的 Android 仿真器？](connect-android-emulator-mac-windows.md)

使用 Android 仿真程序时，本指南将介绍的方法。

## <a name="general-questions"></a>一般问题

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[如何自动化 Android NUnit 测试项目？](automate-android-nunit-test.md)

此指南介绍了如何设置 Android NUnit 测试项目中，步骤_不_Xamarin.UITest 项目。 找不到 Xamarin.UITest 指南[此处](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)。

### <a name="how-do-i-enable-intellisense-in-android-axml-filesenable-axml-intellisensemd"></a>[如何在 Android.axml 文件中启用 Intellisense？](enable-axml-intellisense.md)

本指南介绍如何激活 Android.axml 文件的 Visual Studio Intellisense。

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[为何我的 Android 发布版本无法连接到 Internet？](android-internet.md)

此问题的最常见原因是， **INTERNET**权限将自动包含在调试版本，但必须手动设置为发布版本。 本指南介绍如何启用发布版本的权限。

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[更智能的 Xamarin Android 支持 v4 / v13 NuGet 包](android-support-v4v13-libraries.md)

`Support-v4` 和`Support-v13`不能使用一起在相同的应用中，即，它们是互相排斥。 这是因为`Support-v13`实际包含的所有类型和实现`Support-v4`。 如果您尝试并同时在同一项目引用将会遇到重复的类型错误。

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[如何解决 PathTooLongException 错误？](path-too-long-exception.md)

此文章介绍了如何解决**PathTooLongException**生成 Xamarin.Android 项目时可能发生的错误。



## <a name="deprecated"></a>不推荐使用

> [!NOTE]
> 以下文章适用于 Xamarin 的最新版本中已解决的问题。 但是，如果最新版本的软件会出现此问题，请文件[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与你的完整版本控制信息和完整生成日志输出。

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[哪个版本的 Xamarin.Android 添加了 Lollipop 支持？](xa-lollipop.md)

Android L 预览版最初编写本指南。Xamarin.Android 4.17 添加 Android L 预览版支持和 Xamarin.Android 4.20 添加 Android 棒糖形支持。

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat - 未找到与给定名称匹配的资源：属性“android:actionModeShareDrawable”](missing-action-mode-share-drawable.md)

如果缺少某些所需的 Android SDK pacakages，Xamarin 的较旧版本中可能出现此错误。

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[调整 Android Designer 的 Java 内存参数](android-designer-java-memory.md)

在启动时使用的默认内存参数`java`处理 Android 设计器可能与某些系统配置不兼容。 使用 Xamarin Studio 5.7.2.7 和启动 Xamarin for Visual Studio 3.9.344 这些设置可以在每个项目的基础上自定义。

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Android Resource.designer.cs 文件不更新](resource-designer-wont-update.md)

在 Xamarin.Studio 5.1 bug 以前通过部分或完全删除.csproj 文件中的 xml 代码损坏.csproj 文件。 这将导致重要组成部分 Android 生成系统 （如更新 Android Resource.designer.cs） 失败。 从稳定 5.1.4 开始发布在年 7 月 15 日，则此 bug 已修复;但在许多情况下的项目文件已在本指南中所述手动修复。



