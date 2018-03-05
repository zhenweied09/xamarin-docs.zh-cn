---
title: "Xamarin Android 设备管理器"
description: "Xamarin Android 设备管理器（当前提供预览版）取代了 Google 的旧版设备管理器。 本指南说明如何使用 Xamarin Android 设备管理器创建和配置对 Android 设备进行仿真的 Android 虚拟设备 (AVD)。 可以使用这些虚拟设备运行和测试应用，而不需要依赖物理设备。"
ms.topic: article
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/25/2018
ms.openlocfilehash: 20c7c5a9aaaf13cd9f4050254c7234ada78d926d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="xamarin-android-device-manager"></a>Xamarin Android 设备管理器

_Xamarin Android 设备管理器当前提供了预览版，取代了 Google 的传统设备管理器。本指南说明如何使用 Xamarin Android 设备管理器创建和配置对 Android 设备进行仿真的 Android 虚拟设备 (AVD)。你可以使用这些虚拟设备运行和测试应用，而不需要依赖物理设备。_

![当前提供预览版](~/media/shared/preview.png)

<a name="overview" />
 
## <a name="overview"></a>概述

验证硬件加速已启用后（如[硬件加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md)中所述），下一步是创建用于测试和调试应用的虚拟设备。 可以使用 Xamarin Android 设备管理器创建虚拟设备以供 Android SDK 仿真器使用。

为什么要使用 Xamarin Android 设备管理器，而不使用 [Google 设备管理器](~/android/get-started/installation/android-emulator/google-emulator-manager.md)？
自 Android SDK Tools 版本 26.0.1 起，Google 已移除了对基于 UI 的 AVD 和 SDK 管理器的支持，以支持他们新的 CLI（命令行接口）工具。 出于此更改，在更新到 Android SDK Tools 26.0.1 和更高版本时（开发 Android 8.0 Oreo 所需），必须使用 [Xamarin SDK 管理器](~/android/get-started/installation/android-sdk.md)和 Xamarin Android 设备管理器。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

本指南说明如何在 Windows 上（或[针对 Mac](?tabs=vsmac)）安装和使用用于 Visual Studio 的 Xamarin Android 设备管理器：

[![“设备”选项卡中 Xamarin Android 设备管理器的屏幕截图](xamarin-device-manager-images/win/01-devices-dialog-sml.png)](xamarin-device-manager-images/win/01-devices-dialog.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

本指南说明如何安装和使用用于 Visual Studio for Mac（或[适用于 Windows](?tabs=vswin)）的 Xamarin Android 设备管理器。

[![“设备”选项卡中 Xamarin Android 设备管理器的屏幕截图](xamarin-device-manager-images/mac/01-devices-dialog-sml.png)](xamarin-device-manager-images/mac/01-devices-dialog.png)

> [!NOTE]
> **注**意：本指南仅适用于 Visual Studio for Mac。
Xamarin Studio 与 Xamarin Android 设备管理器不兼容。

-----

使用 Xamarin Android 设备管理器创建和配置在 [Android SDK 仿真器](~/android/deploy-test/debugging/android-sdk-emulator/index.md)中运行的 *Android 虚拟设备* (AVD)。
每台 AVD 是模拟物理 Android 设备的仿真器配置。 这样可以在模拟不同物理 Android 设备的多种配置中运行和测试应用。 Xamarin Android 设备管理器取代了 Google 的独立 AVD 管理器（已弃用）。

本指南介绍如何安装和开始使用 Android 设备管理器。 你将了解如何创建、复制、自定义和启动设备管理器。 本指南还介绍了如何配置每台虚拟设备的属性（例如 API 级别、CPU、内存和分辨率）以启用/禁用模拟传感器（例如加速计、GPS、方向和光传感器）和配置该虚拟设备使用的硬件加速的类型。


<a name="requirements" />

## <a name="requirements"></a>惠?

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要使用 Xamarin Android 设备管理器，需要以下工具：

-   需要 Visual Studio 2017 版本 15.5 或更高版本。 支持 Visual Studio Community 版和更高版本。

-   适用于 Visual Studio 的 Xamarin 版本 4.8 或更高版本。 有关更新 Xamarin 的信息，请参阅[更改更新通道](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/)。

-   适用于 Windows 的 [Xamarin 设备管理器安装程序](https://go.microsoft.com/fwlink/?linkid=865528)的最新版本。

-   **Android SDK** &ndash; 必须安装 Android SDK（请参阅 [Android SDK 安装](~/android/get-started/installation/android-sdk.md)），并且必须安装 SDK 工具版本 26.0，如下一部分中所述。 请务必在以下位置安装 Android SDK（如果尚未安装）：C:\\Program Files (x86)\\Android\\android-sdk。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

-   Visual Studio for Mac 7.4 或更高版本。

-   适用于 macOS 的 [Xamarin 设备管理器安装程序](https://go.microsoft.com/fwlink/?linkid=865527)的最新版本。

-   **Android SDK** &ndash; 必须通过 SDK 管理器安装 Android SDK 8.0 (API 26) 或更高版本。

-----

 
## <a name="installing-the-device-manager"></a>安装设备管理器

使用以下步骤安装 Xamarin Android 设备管理器：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 下载适用于 Windows 的 [Xamarin 设备管理器安装程序](https://go.microsoft.com/fwlink/?linkid=865528)。

2. 双击“Xamarin.DeviceManager.msi”，然后按照安装说明进行操作： 

    ![Xamarin Android 设备管理器安装向导](xamarin-device-manager-images/win/30-installer.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 下载适用于 macOS 的 [Xamarin 设备管理器安装程序](https://go.microsoft.com/fwlink/?linkid=865527)。

2. 双击“AndroidDevices.pkg”，然后按照安装说明进行操作： 

    [![Xamarin Android 设备管理器安装向导](xamarin-device-manager-images/mac/30-installer-sml.png)](xamarin-device-manager-images/mac/30-installer.png)

-----

<a name="dev-manager" /> 
 
## <a name="launching-the-device-manager"></a>启动设备管理器

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Visual Studio 15.6 Preview 3 和更高版本中，可以从“工具”菜单启动 Xamarin Android 设备管理器。 如果使用 Visual Studio 15.6 Preview 3 或更高版本，则通过单击“工具”>“Android 仿真器管理器”启动设备管理器：

[![从“工具”菜单启动](xamarin-device-manager-images/win/04-tools-menu-sml.png)](xamarin-device-manager-images/win/04-tools-menu.png)

如果使用早期版本的 Visual Studio，则必须从 Windows“开始”菜单启动 Xamarin Android 设备管理器。

![“开始”菜单中的 Xamarin Android 设备管理器](xamarin-device-manager-images/win/31-start-menu.png)

右键单击“Xamarin Android 设备管理器”，然后选择“更多”>“以管理员身份运行”。 如果在启动时看到以下错误对话框，请参阅[故障排除](#troubleshooting)部分以查找解决方法：

![Android SDK 实例错误](xamarin-device-manager-images/win/32-sdk-error.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在 Visual Studio for Mac 7.6 Preview 3（目前处于 alpha 通道中）或更高版本中，可以通过选择“工具”>“仿真程序管理器”启动 Xamarin Android 设备管理器：

[![从“工具”菜单启动](xamarin-device-manager-images/mac/16-tools-menu-sml.png)](xamarin-device-manager-images/mac/16-tools-menu.png)

如果使用早期版本的 Visual Studio for Mac，则必须独立启动 Xamarin Android 设备管理器。 在“Applications”文件夹中找到“Android Devices”，然后双击它以启动：

[![Finder 中 Xamarin Android 设备管理器的位置](xamarin-device-manager-images/mac/31-location-in-finder-sml.png)](xamarin-device-manager-images/mac/31-location-in-finder.png)


-----

在使用 Android 设备管理器之前，必须安装 Android SDK 工具版本 26.0.0 或更高版本。 如果未安装 Android SDK 工具 26.0.0 或更高版本，在启动时则会看到此错误对话框：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![“Android SDK 实例错误”对话框](xamarin-device-manager-images/win/02-sdk-instance-error.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![“Android SDK 实例错误”对话框](xamarin-device-manager-images/mac/02-sdk-instance-error.png)

-----

如果看到此错误对话框，请单击“确定”以打开 Android SDK 管理器。 在 Android SDK 管理器中，单击“工具”选项卡并安装“Android SDK Tools 26.0.2”或更高版本、“Android SDK 平台工具 26.0.0”或更高版本以及“Android SDK 生成工具 26.0.0”（或更高版本）：


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![安装 Android SDK Tools 26.0](xamarin-device-manager-images/win/03-sdk-tools-sml.png)](xamarin-device-manager-images/win/03-sdk-tools.png)

安装这些包后，可以关闭 SDK 管理器并重新启动 Android 设备管理器。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![安装 Android SDK Tools 26.0](xamarin-device-manager-images/mac/03-sdk-tools-sml.png)](xamarin-device-manager-images/mac/03-sdk-tools.png)

-----

<a name="devices" />
 
## <a name="main-screen"></a>主屏幕

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

首次启动 Android 设备管理器时，它会展现一个显示所有当前已配置的虚拟设备的屏幕。 对于每台设备，将显示“名称”、“操作系统”（Android API 级别）、CPU、“内存”大小以及屏幕分辨率：

[![已安装设备及其参数的列表](xamarin-device-manager-images/win/05-installed-list-sml.png)](xamarin-device-manager-images/win/05-installed-list.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

首次启动 Android 设备管理器时，它会展现一个显示所有当前已配置的虚拟设备的屏幕。 对于每台设备，将显示“名称”、“系统映像”（Android API 级别）、CPU、“内存”大小以及屏幕分辨率：

[![已安装设备及其参数的列表](xamarin-device-manager-images/mac/05-devices-list-sml.png)](xamarin-device-manager-images/mac/05-devices-list.png)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

单击列表中的设备时，“启动”按钮将出现在右侧。 可以单击“启动”按钮以通过此虚拟设备启动仿真器：

[![设备的“启动”按钮图像](xamarin-device-manager-images/win/06-start-button-sml.png)](xamarin-device-manager-images/win/06-start-button.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

单击“播放”按钮以通过所选的虚拟设备启动仿真器：
 
[![设备的“启动”按钮图像](xamarin-device-manager-images/mac/06-start-button-sml.png)](xamarin-device-manager-images/mac/06-start-button.png)
 
-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

通过所选虚拟设备启动仿真器后，“启动”按钮将更改为可用于终止运行仿真器的“停止”按钮：

[![正在运行的设备的“停止”按钮](xamarin-device-manager-images/win/07-stop-button-sml.png)](xamarin-device-manager-images/win/07-stop-button.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

通过所选虚拟设备启动仿真器后，“播放”按钮将更改为可用于终止运行仿真器的“停止”按钮：
 
[![正在运行的设备的“停止”按钮](xamarin-device-manager-images/mac/07-stop-button-sml.png)](xamarin-device-manager-images/mac/07-stop-button.png)
 
-----

<a name="device-new" />
 
### <a name="new-device"></a>新设备

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要创建新设备，请单击“新建”按钮（位于屏幕的右上方区域）：

[![用于创建新设备的“新建”按钮](xamarin-device-manager-images/win/08-new-button-sml.png)](xamarin-device-manager-images/win/08-new-button.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要创建新设备，请单击“新建设备”按钮（位于屏幕的右上方区域）：
 
[![用于创建新设备的“新建”按钮](xamarin-device-manager-images/mac/08-new-button-sml.png)](xamarin-device-manager-images/mac/08-new-button.png)
 
-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

单击“新建”以启动“新建设备”屏幕：

[![设备管理器的“新建设备”屏幕](xamarin-device-manager-images/win/09-new-device-editor-sml.png)](xamarin-device-manager-images/win/09-new-device-editor.png)

若要在“新建设备”中配置新设备，请使用以下步骤：

1. 通过单击“设备”下拉菜单以选择要仿真的物理设备：

    [![“设备”下拉菜单](xamarin-device-manager-images/win/10-device-menu-sml.png)](xamarin-device-manager-images/win/10-device-menu.png)

2. 通过单击“系统映像”下拉菜单以选择要与此虚拟设备一起使用的系统映像。 此菜单在“已安装”下列出已安装的系统映像。 “下载”部分列出当前开发计算机上不可用但可以进行自动安装的系统映像：

    [![“系统映像”下拉菜单](xamarin-device-manager-images/win/11-system-image-menu-sml.png)](xamarin-device-manager-images/win/11-system-image-menu.png)

3. 为设备提供新名称。 在下面的示例中，新设备名为“Nexus 5 API 25”：

    [![为新设备命名](xamarin-device-manager-images/win/12-device-name-sml.png)](xamarin-device-manager-images/win/12-device-name.png)

4. 编辑需要修改的任何属性。 若要对属性进行更改，请参阅本指南后面的[配置文件属性](#properties)部分。

5. 添加需要显式设置的任何其他属性。 尽管“新建设备”屏幕只列出了最常修改的属性，但你可以单击“添加属性”下拉菜单（位于左下角）以添加其他的属性。 在下面的示例中，正在添加 `hw.lcd.backlight` 属性：

    [![“添加属性”下拉菜单](xamarin-device-manager-images/win/13-add-property-menu-sml.png)](xamarin-device-manager-images/win/13-add-property-menu.png)

6. 单击“创建”按钮（位于右下角）以创建新设备：

    ![“创建”按钮](xamarin-device-manager-images/win/14-create-button.png)

7. 可能会出现“许可证接受”屏幕。 如果同意许可条款，请单击“接受”：

    ![“许可证接受”屏幕](xamarin-device-manager-images/win/15-license-acceptance.png)

8. 在创建设备时，Android 设备管理器会将新设备添加到已安装虚拟设备的列表，并会显示“正在创建”的进度指示器：

    [![创建进度指示器](xamarin-device-manager-images/win/16-creating-the-device-sml.png)](xamarin-device-manager-images/win/16-creating-the-device.png)

9. 创建过程完成后，新设备会显示在已安装虚拟设备的列表中，并且会显示可以启动的“启动”按钮：

   [![可以启动的新创建的设备](xamarin-device-manager-images/win/17-created-device-sml.png)](xamarin-device-manager-images/win/17-created-device.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

单击“新建设备”以启动“新建设备”屏幕：

[![设备管理器的“新建设备”屏幕](xamarin-device-manager-images/mac/09-new-device-editor-sml.png)](xamarin-device-manager-images/mac/09-new-device-editor.png)

使用以下步骤在“新建设备”屏幕中配置新设备：

1. 通过单击“设备”下拉菜单以选择要仿真的物理设备：

    [![“设备”下拉菜单](xamarin-device-manager-images/mac/10-device-menu-sml.png)](xamarin-device-manager-images/mac/10-device-menu.png)

2. 通过单击“系统映像”下拉菜单以选择要与此虚拟设备一起使用的系统映像。 此菜单在“已安装”下列出已安装的系统映像。 “下载”部分（如有显示）列出当前开发计算机上不可用但可以进行自动安装的系统映像：

    [![“系统映像”下拉菜单](xamarin-device-manager-images/mac/11-system-image-menu-sml.png)](xamarin-device-manager-images/mac/11-system-image-menu.png)

3. 为设备提供新名称。 在下面的示例中，新设备名为“Nexus 5X API 25”：

    [![为新设备命名](xamarin-device-manager-images/mac/12-device-name-sml.png)](xamarin-device-manager-images/mac/12-device-name.png)

4. 编辑需要修改的任何属性。 若要对属性进行更改，请参阅本指南后面的[配置文件属性](#properties)部分。

5. 添加需要显式设置的任何其他属性。 尽管“新建设备”屏幕只列出了最常修改的属性，但你可以单击“添加属性”下拉菜单（位于左下角）以添加其他的属性：

    [![“添加属性”下拉菜单](xamarin-device-manager-images/mac/13-add-property-menu-sml.png)](xamarin-device-manager-images/mac/13-add-property-menu.png)

6. 也可以单击“自定义”为设备定义新属性：

    ![“创建”按钮](xamarin-device-manager-images/mac/14-custom-button.png)

7. 单击“创建”按钮（位于右下角）以创建新设备：

    ![“创建”按钮](xamarin-device-manager-images/mac/15-create-button.png)

8. 可能会出现“许可证接受”屏幕。 如果同意许可条款，请单击“接受”。

9. 在创建设备时，Android 设备管理器会将新设备添加到设备列表，并会显示“正在创建”的进度指示器：

    [![创建进度指示器](xamarin-device-manager-images/mac/17-creating-the-device-sml.png)](xamarin-device-manager-images/mac/17-creating-the-device.png)

10. 创建过程完成后，新设备会显示在设备列表中，并且会显示可以启动的“播放”按钮：

   [![可以启动的新创建的设备](xamarin-device-manager-images/mac/18-created-device-sml.png)](xamarin-device-manager-images/mac/18-created-device.png)

-----


<a name="device-edit" />
 
### <a name="edit-device"></a>编辑设备

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要编辑现有的虚拟设备，请选择设备并单击“编辑”按钮（位于屏幕的右上方）：

[![用于修改新设备的“编辑”按钮](xamarin-device-manager-images/win/19-edit-button-sml.png)](xamarin-device-manager-images/win/19-edit-button.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要编辑现有的虚拟设备，请选择“其他选项”下拉菜单（齿轮图标），然后选择“编辑”：
 
[![用于修改新设备的“编辑”菜单选项](xamarin-device-manager-images/mac/19-edit-button-sml.png)](xamarin-device-manager-images/mac/19-edit-button.png)
 
-----

单击“编辑”为所选的虚拟设备启动“设备编辑器”：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![“设备编辑器”屏幕](xamarin-device-manager-images/win/20-device-editor-sml.png)](xamarin-device-manager-images/win/20-device-editor.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
 
[![“设备编辑器”屏幕](xamarin-device-manager-images/mac/20-device-editor-sml.png)](xamarin-device-manager-images/mac/20-device-editor.png)
 
-----

“设备编辑器”屏幕在第一列中列出了虚拟设备的属性，第二列中为每个属性相应的值。 当选择某个属性时，有关该属性的详细描述会显示在右侧。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

例如，在下面的屏幕截图中，`hw.lcd.density` 属性正从“420”更改为“240”：

[![设备编辑示例](xamarin-device-manager-images/win/21-device-editing-sml.png)](xamarin-device-manager-images/win/21-device-editing.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

例如，在下面的屏幕截图中，`hw.lcd.density` 属性正从“320”更改为“240”并且 `hw.ramSize` 已更改为“768”：
 
[![设备编辑示例](xamarin-device-manager-images/mac/21-device-editing-sml.png)](xamarin-device-manager-images/mac/21-device-editing.png)
 
-----

在进行必要的配置更改后，单击“保存”按钮。
有关更改虚拟设备属性的详细信息，请参阅本指南后面的[配置文件属性](#properties)部分。


<a name="addopt" />
 
### <a name="additional-options"></a>附加选项

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

右上方的“&hellip;”菜单中提供适用于设备的附加选项：

[![附加选项菜单的位置](xamarin-device-manager-images/win/22-overflow-menu-sml.png)](xamarin-device-manager-images/win/22-overflow-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

“播放”按钮左侧的下拉菜单中提供适用于设备的附加选项：

[![附加选项菜单的位置](xamarin-device-manager-images/mac/22-overflow-menu-sml.png)](xamarin-device-manager-images/mac/22-overflow-menu.png)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

附加选项菜单中包含以下项：

-   **复制和编辑** &ndash; 复制当前所选的设备，并会在“新建设备”屏幕中使用不同的唯一名称打开。 例如，选择“VisualStudio_android-23_x86_phone”，然后单击“复制和编辑”将计数器追加到名称中：

    [![“复制和编辑”屏幕](xamarin-device-manager-images/win/23-dupe-and-edit-sml.png)](xamarin-device-manager-images/win/23-dupe-and-edit.png)

-   **在资源管理器中展现** &ndash; 在包含用于虚拟设备的文件的文件夹中打开“Windows 资源管理器”窗口。 例如，选择“Nexus 5X API 25”并单击“在资源管理器中展现”以打开一个窗口，如下所示：

    [![单击“在资源管理器中展现”的结果](xamarin-device-manager-images/win/24-reveal-in-explorer-sml.png)](xamarin-device-manager-images/win/24-reveal-in-explorer.png)

-   **恢复出厂设置**  &ndash; 将所选设备重置为其默认设置，擦除任何用户在此设备运行时对其内部状态进行的更改。 此更改不会影响在创建和编辑期间对虚拟设备做出的修改。 将出现提醒此重置无法被撤消的一个对话框。 单击“擦除用户数据”以确认重置。

-   **删除** &ndash; 永久删除所选的虚拟设备。
    将出现提醒删除设备无法被撤消的一个对话框。 如果确定要删除设备，请单击“删除”。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

附加选项菜单中包含以下项：

-   **编辑** &ndash; 在设备编辑器中打开当前所选设备，如之前[编辑设备](#device-edit)中所述。

-   **复制和编辑** &ndash; 复制当前所选的设备，并会在“新建设备”屏幕中使用不同的唯一名称打开。
    例如，选择“Nexus 5X API 25”，然后单击“复制和编辑”将计数器追加到名称中：

    [![“复制和编辑”屏幕](xamarin-device-manager-images/mac/23-dupe-and-edit-sml.png)](xamarin-device-manager-images/mac/23-dupe-and-edit.png)

-   **在查找器中展现** &ndash; 在包含用于虚拟设备的文件的文件夹中打开 macOS Finder 窗口。 例如，选择“Nexus 5X API 25”并单击“在查找器中展现”以打开一个窗口，如下所示：

    [![单击“在资源管理器中展现”的结果](xamarin-device-manager-images/mac/24-reveal-in-finder-sml.png)](xamarin-device-manager-images/mac/24-reveal-in-finder.png)

-   **恢复出厂设置**  &ndash; 将所选设备重置为其默认设置，擦除任何用户在此设备运行时对其内部状态进行的更改。 此更改不会影响在创建和编辑期间对虚拟设备做出的修改。 将出现提醒此重置无法被撤消的一个对话框。 单击“擦除用户数据”以确认重置。

-   **删除** &ndash; 永久删除所选的虚拟设备。
    将出现提醒删除设备无法被撤消的一个对话框。 如果确定要删除设备，请单击“删除”。

-----

<a name="properties" />
 
## <a name="profile-properties"></a>配置文件属性

“新建设备”和“设备编辑”屏幕在第一列中列出了虚拟设备的属性，第二列中为每个属性相应的值。 当选择某个属性时，有关该属性的详细描述会显示在右侧。 可以修改其“硬件配置文件属性”和“AVD 属性”。
硬件配置文件属性（如`hw.ramSize` 和 `hw.accelerometer`）描述仿真设备的物理特征。 这些特征包括屏幕大小、可用的 RAM 量以及是否具有加速计。 AVD 属性指定 AVD 在运行时的操作。 例如，可以配置 AVD 属性以指定 AVD 如何使用开发计算机的图形卡进行呈现。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

可以使用以下准则来更改属性：

-   若要更改布尔属性，请单击布尔属性右侧的复选标记：

    ![更改布尔属性](xamarin-device-manager-images/win/25-boolean-value.png)

-   若要更改枚举 (enumerated) 属性，请单击属性右侧的下拉箭头并选择一个新值。

    ![更改枚举属性](xamarin-device-manager-images/win/27-enum-value.png)

-   若要更改字符串或整数属性，请双击值列中当前的字符串或整数设置并输入一个新值。

    ![更改整数属性](xamarin-device-manager-images/win/26-integer-value.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

可以使用以下准则来更改属性：

-   若要更改布尔属性，请单击布尔属性右侧的复选标记：

    ![更改布尔属性](xamarin-device-manager-images/mac/25-boolean-value.png)

-   若要更改枚举 (enumerated) 属性，请单击属性右侧的下拉菜单并选择一个新值。

    ![更改枚举属性](xamarin-device-manager-images/mac/27-enum-value.png)

-   若要更改字符串或整数属性，请双击值列中当前的字符串或整数设置并输入一个新值。

    ![更改整数属性](xamarin-device-manager-images/mac/26-integer-value.png)


-----

下表详细说明了“新建设备”和“设备编辑器”屏幕中列出的属性：

[!include[](~/android/includes/table.html)]

有关这些属性的详细信息，请参阅[硬件配置文件属性](https://developer.android.com/studio/run/managing-avds.html#hpproperties)。


<a name="troubleshooting" />

## <a name="troubleshooting"></a>疑难解答

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

以下介绍了常见的 Xamarin Android 设备管理器问题和解决方法：

### <a name="android-sdk-in-non-standard-location"></a>Android SDK 位于非标准位置

通常情况下，Android SDK 安装在以下位置：

C:\\Program Files (x86)\\Android\\android-sdk

如果 SDK 未安装在此位置，在启动时则可能会出现此错误：

![Android SDK 实例错误](xamarin-device-manager-images/win/32-sdk-error.png)

若要解决此问题，请执行下列操作：

1. 从 Windows 桌面依次转到 C:\\Users\\username\\AppData\\Roaming\\XamarinDeviceManager：

    ![Xamarin 设备管理器日志文件位置](xamarin-device-manager-images/win/33-log-files.png)

2. 双击以打开某个日志文件，并找到“配置文件路径”。 例如:

    [![日志文件中的“配置文件路径”](xamarin-device-manager-images/win/34-config-file-path-sml.png)](xamarin-device-manager-images/win/34-config-file-path.png)

3. 导航到此位置，然后双击“user.config”以将其打开。 

4. 在“user.config”中，找到“&lt;UserSettings&gt;”元素，并向它添加一个“AndroidSdkPath”特性。 将此特性设置为 Android SDK 在开发计算机上的安装位置路径，然后保存文件。 例如，如果 Android SDK 安装在 C:\\Programs\\Android\\SDK，“&lt;UserSettings&gt;”将如下所示：
        
    ```xml
    <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:\Programs\Android\SDK" />
    ```

对“user.config”进行更改后，应该能够启动 Xamarin Android 设备管理器。

### <a name="generating-a-bug-report"></a>生成 Bug 报表

如果发现 Xamarin Android 设备管理器出现问题，但无法使用上述故障排除提示解决此问题，请右键单击标题栏并选择“生成 Bug 报告”，将 bug 报告归档：

![归档 bug 报告的菜单项的位置](xamarin-device-manager-images/win/35-bug-report.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Visual Studio for Mac 上的 Xamarin Android 设备管理器暂无已知问题/解决办法。 

### <a name="generating-a-bug-report"></a>生成 Bug 报表

如果发现问题，请单击“帮助”>“生成 Bug 报告”，将 bug 报告归档：

![归档 bug 报告的菜单项的位置](xamarin-device-manager-images/mac/35-bug-report.png)

-----

 
<a name="summary" />
 
## <a name="summary"></a>摘要

本指南介绍 Visual Studio for Mac 和适用于 Visual Studio 的 Xamarin 中提供的 Xamarin Android 设备管理器。 其中介绍了启动和停止 Android 仿真器、选择要运行的 Android 虚拟设备 (AVD)、创建新的虚拟设备以及如何编辑虚拟设备等基本功能。 还说明了如何编辑配置文件硬件属性以进一步自定义。


## <a name="related-links"></a>相关链接

- [对 Android SDK 工具的更改](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [使用 Android SDK 仿真器进行调试](~/android/deploy-test/debugging/android-sdk-emulator/index.md)
- [SDK Tools 发行说明 (Google)](https://developer.android.com/studiohttps://developer.xamarin.com/releases/sdk-tools.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
