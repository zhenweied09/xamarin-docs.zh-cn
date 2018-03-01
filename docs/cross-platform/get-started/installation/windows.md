---
title: "在 Visual Studio on Windows 中安装 Xamarin"
ms.topic: article
ms.prod: xamarin
ms.assetid: E20D4463-368E-4B60-A059-F50DB8C5552D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 09/29/2017
ms.openlocfilehash: b68e03251b83192bdc5836af6ea54446ddaad24a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="installing-xamarin-in-visual-studio-on-windows"></a>在 Visual Studio on Windows 中安装 Xamarin

现在，所有版本的 Visual Studio 中都免费附带 Xamarin，并且不需要单独的许可证，可使用 Visual Studio 安装程序下载和安装 Xamarin 工具。

-   [要求](#requirements)
-   [安装](#installation)
-   [将 Xamarin 添加到 Visual Studio 2017](#vs2017)
-   [将 Xamarin 添加到 Visual Studio 2015](#vs2015)
-   [验证安装](#verifying)
-   [后续步骤](#nextsteps)


<a name="requirements" />

# <a name="requirements"></a>惠?

安装 Visual Studio tools for Xamarin 需要以下项：

1. Windows 7 或更高版本。

2. Visual Studio 2015 或 2017（Community、Professional 或 Enterprise）。

3. Xamarin for Visual Studio。

请注意，由于缺少插件支持，Xamarin 不能与 Visual Studio Express 版配合使用。

有关安装和使用 Xamarin 的先决条件的详细信息，请参阅[系统要求](~/cross-platform/get-started/requirements.md)。


<a name="installation" />

# <a name="installation"></a>安装

Xamarin 可安装为新 Visual Studio 的一部分。
为此，请执行下列步骤：

1. 从 [Visual Studio](https://www.visualstudio.com/vs/) 页面下载 Visual Studio Community、Visual Studio Professional 或 Visual Studio Enterprise（底部有下载链接）。

2. 双击下载的程序包以开始安装。

3. 从安装屏幕中选择“使用 .NET 的移动开发”工作负载： 

    [![工作负载屏幕上具有 .NET 选择的移动开发](windows-images/01-mobile-dev-workload-sml.png)](windows-images/01-mobile-dev-workload.png)

4. 选择“使用 .NET 的移动开发”时，查看右侧的“摘要”面板。 可在此处取消选择不希望安装的移动开发选项。 默认情况下，会安装以下屏幕截图中的所有选项（Xamarin 工作簿、Xamarin Profiler、Xamarin Remoted Simulator、Android NDK、Android SDK、Java SE 开发工具包、Google Android 仿真器、F# 支持和 Intel HAXM）：

    ![列出要安装的 Xamarin 选项的摘要面板](windows-images/02-summary.png)

5. Visual Studio 安装准备就绪后，单击右下角的“安装”按钮：

    ![安装按钮的位置](windows-images/03-click-install.png)

   安装过程可能需要很长时间才能完成，具体取决于要安装的 Visual Studio 版本。 可使用进度栏监视安装过程：

    ![安装过程中进度栏的屏幕截图示例](windows-images/04-progress-bars.png)

6. Visual Studio 安装完成后，单击“启动”按钮以启动 Visual Studio：

    ![启动按钮的位置](windows-images/05-launch.png)


<a name="vs2017" />

## <a name="adding-xamarin-to-visual-studio-2017"></a>将 Xamarin 添加到 Visual Studio 2017

如果已安装 Visual Studio 2017，可通过重新运行 Visual Studio 安装程序修改工作负载来添加 Xamarin（有关详细信息，请参阅[修改 Visual Studio ](https://docs.microsoft.com/visualstudio/install/modify-visual-studio)）。 接下来，按照上面列出的步骤安装 Xamarin。

有关下载和安装 Visual Studio 2017 的详细信息，请参阅[安装 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。


<a name="vs2015" />

## <a name="adding-xamarin-to-visual-studio-2015"></a>将 Xamarin 添加到 Visual Studio 2015

若要将 Xamarin.Android 添加到 Visual Studio 2015 的现有安装中，请执行以下步骤：

1. 右键单击 Windows“开始”按钮并选择“程序和功能”。

2. 右键单击“Microsoft Visual Studio”，然后单击“更改”。

3. 出现 Visual Studio 安装程序对话框时，请单击“修改”按钮。

4. 在“功能”选项卡中，向下滚动到“跨平台移动开发”。 单击“C#/.NET (Xamarin)”旁边的复选框：

    ![将 C#/.NET Xamarin 添加到 Visual Studio 2015](windows-images/06-add-xamarin.png)

5. 单击“更新”按钮将 Xamarin 添加到 Visual Studio。


<a name="verifying" />

## <a name="verifying-installation"></a>验证安装

在 Visual Studio 2017 中，可通过单击“帮助”菜单来验证是否安装了 Xamarin。 如果安装了 Xamarin，应该看到 Xamarin 菜单项，如此屏幕截图所示：

![“帮助”菜单上显示的 Xamarin 菜单项](windows-images/12-xamarin-menu-item.png)

如果使用的是早期版本的 Visual Studio，可单击“帮助”>“关于 Microsoft Visual Studio”，滚动浏览已安装的产品列表，查看是否已安装 Xamarin：

![显示 Visual Studio 已安装产品的屏幕](windows-images/13-xamarin-is-installed.png)

有关查找版本信息的详细信息，请参阅 [Where can I find my version information and logs?](~/cross-platform/troubleshooting/questions/version-logs.md)（在何处可找到我的版本信息和日志？）

<a name="nextsteps" />

# <a name="next-steps"></a>后续步骤

通过安装 Visual Studio Tools for Xamarin，可开始编写应用代码，但若要构建应用并将其部署到模拟器、仿真器和设备，则需要其他设置。 请访问以下指南，完成安装并开始跨平台应用构建。

## <a name="ios"></a>iOS

有关详细信息，请参阅[在 Windows 上安装 Xamarin.iOS](~/ios/get-started/installation/windows/index.md) 指南。 

1. [在 Mac 上安装 Xamarin.iOS 工具](~/ios/get-started/installation/windows/index.md#installation)
2. [配置 Mac](~/ios/get-started/installation/windows/index.md#configuration)
3. [iOS 开发人员设置](~/ios/get-started/installation/windows/index.md#developersetup)（在设备上运行应用程序）。
4. [将 Visual Studio 连接到 Mac 生成主机](~/ios/get-started/installation/windows/index.md#connectingtomac)
5. [远程 iOS 模拟器](~/tools/ios-simulator.md)
6. [Xamarin.iOS for Visual Studio 简介](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)

## <a name="android"></a>Android

有关详细信息，请参阅[在 Windows 上安装 Xamarin.Android](~/android/get-started/installation/windows.md) 指南。

1. [Xamarin.Android 配置](~/android/get-started/installation/windows.md#configuration)
2. [使用 Xamarin Android SDK 管理器](~/android/get-started/installation/android-sdk.md?ide=vs)
3. [Android SDK 仿真器](~/android/get-started/installation/android-emulator/index.md)
4. [设置设备进行开发](~/android/get-started/installation/set-up-device-for-development.md)
