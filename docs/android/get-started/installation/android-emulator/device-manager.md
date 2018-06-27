---
title: 使用 Android Device Manager 管理虚拟设备
description: 本文介绍如何使用 Android Device Manager 创建和配置对物理 Android 设备进行仿真的 Android 虚拟设备 (AVD)。 可以使用这些虚拟设备运行和测试应用，而不需要依赖物理设备。
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/02/2018
ms.openlocfilehash: 888f126d3e58b0300ba7ce3ad1cb5a8001fc545a
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34733285"
---
# <a name="managing-virtual-devices-with-the-android-device-manager"></a>使用 Android Device Manager 管理虚拟设备

本文介绍如何使用 Android Device Manager 创建和配置对物理 Android 设备进行仿真的 Android 虚拟设备 (AVD)_。你可以使用这些虚拟设备运行和测试应用，而不需要依赖物理设备。_

## <a name="overview"></a>概述

验证硬件加速已启用后（如[通过硬件加速提高仿真器性能](~/android/get-started/installation/android-emulator/hardware-acceleration.md)中所述），下一步是使用 Android Device Manager（也称为 Xamarin Android Device Manager）创建用于测试和调试应用的虚拟设备。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

本文介绍了如何使用 Android Device Manager 创建、复制、自定义和启动 Android 虚拟设备。

[![“设备”选项卡中 Android Device Manager 的屏幕截图](device-manager-images/win/01-devices-dialog-sml.png)](device-manager-images/win/01-devices-dialog.png#lightbox)

使用 Android Device Manager 创建和配置在 [Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md) 中运行的 Android 虚拟设备 (AVD)。
每台 AVD 是模拟物理 Android 设备的仿真器配置。 这样可以在模拟不同物理 Android 设备的多种配置中运行和测试应用。

## <a name="requirements"></a>惠?

若要使用 Android Device Manager，需要具备以下各项：

-   需要 Visual Studio 2017 版本 15.7 或更高版本。 支持 Visual Studio Community、Professional 和 Enterprise 版本。

-   Visual Studio Tools for Xamarin 版本 4.9 或更高版本。

-   必须安装 Android SDK（请参阅[设置用于 Xamarin.Android 的 Android SDK](~/android/get-started/installation/android-sdk.md)），并且必须安装 SDK 工具版本 26.1.1 或更高版本，如下一节中所述。 请务必在以下位置安装 Android SDK（如果尚未安装）：C:\\Program Files (x86)\\Android\\android-sdk。


## <a name="launching-the-device-manager"></a>启动设备管理器

通过单击“工具”>“Android”>“Android Device Manager”，从“工具”菜单启动 Android Device Manager：

[![从“工具”菜单启动](device-manager-images/win/04-tools-menu-sml.png)](device-manager-images/win/04-tools-menu.png#lightbox)

如果在启动时看到以下错误对话框，请参阅[仿真器安装问题疑难解答](~/android/get-started/installation/android-emulator/troubleshooting.md)查找解决方法：

![Android SDK 实例错误](device-manager-images/win/32-sdk-error.png)

在使用 Android Device Manage 之前，必须先安装 Android SDK 工具版本 26.1.1 或更高版本。 如果未安装 Android SDK 工具 26.1.1 或更高版本，在启动时可能会看到以下错误对话框：

![“Android SDK 实例错误”对话框](device-manager-images/win/02-sdk-instance-error.png)

如果看到此错误对话框，请单击“打开 SDK 管理器”以打开 Android SDK 管理器。 在 Android SDK 管理器中，单击“工具”选项卡并安装以下工具：

-   Android SDK Tools 26.1.1 或更高版本 
-   Android SDK 平台工具 27.0.1 或更高版本  
-   Android SDK 生成工具 27.0.3 或更高版本

这些包应显示为“已安装”状态，如下面的屏幕截图所示：

[![安装 Android SDK Tools 27.0](device-manager-images/win/03-sdk-tools-sml.png)](device-manager-images/win/03-sdk-tools.png#lightbox)

安装这些包后，可以关闭 SDK 管理器并重新启动 Android 设备管理器。

## <a name="main-screen"></a>主屏幕

首次启动 Android 设备管理器时，它会展现一个显示所有当前已配置的虚拟设备的屏幕。 对于每台设备，将显示“名称”、“操作系统”（Android API 级别）、CPU、“内存”大小以及屏幕分辨率：

[![已安装设备及其参数的列表](device-manager-images/win/05-installed-list-sml.png)](device-manager-images/win/05-installed-list.png#lightbox)

单击列表中的设备时，“启动”按钮将出现在右侧。 可以单击“启动”按钮以通过此虚拟设备启动仿真器：

[![设备的“启动”按钮图像](device-manager-images/win/06-start-button-sml.png)](device-manager-images/win/06-start-button.png#lightbox)

通过所选虚拟设备启动仿真器后，“启动”按钮将更改为可用于终止运行仿真器的“停止”按钮：

[![正在运行的设备的“停止”按钮](device-manager-images/win/07-stop-button-sml.png)](device-manager-images/win/07-stop-button.png#lightbox)

### <a name="new-device"></a>新设备

若要创建新设备，请单击“新建”按钮（位于屏幕的右上方区域）：

[![用于创建新设备的“新建”按钮](device-manager-images/win/08-new-button-sml.png)](device-manager-images/win/08-new-button.png#lightbox)

单击“新建”以启动“新建设备”屏幕：

[![设备管理器的“新建设备”屏幕](device-manager-images/win/09-new-device-editor-sml.png)](device-manager-images/win/09-new-device-editor.png#lightbox)

若要在“新建设备”中配置新设备，请使用以下步骤：

1. 通过单击“设备”下拉菜单以选择要仿真的物理设备：

    [![“设备”下拉菜单](device-manager-images/win/10-device-menu-sml.png)](device-manager-images/win/10-device-menu.png#lightbox)

2. 通过单击“系统映像”下拉菜单以选择要与此虚拟设备一起使用的系统映像。 此菜单在“已安装”下列出已安装的系统设备管理器映像。 “下载”部分列出当前开发计算机上不可用但可以自动安装的系统设备管理器映像：

    [![“系统映像”下拉菜单](device-manager-images/win/11-system-image-menu-sml.png)](device-manager-images/win/11-system-image-menu.png#lightbox)

3. 为设备提供新名称。 在下面的示例中，新设备名为“Nexus 5 API 25”：

    [![为新设备命名](device-manager-images/win/12-device-name-sml.png)](device-manager-images/win/12-device-name.png#lightbox)

4. 编辑需要修改的任何属性。 若要对属性进行更改，请参阅[编辑 Android 虚拟设备属性](~/android/get-started/installation/android-emulator/device-properties.md)。

5. 添加需要显式设置的任何其他属性。 尽管“新建设备”屏幕只列出了最常修改的属性，但你可以单击“添加属性”下拉菜单（位于左下角）以添加其他的属性。 在下面的示例中，正在添加 `hw.lcd.backlight` 属性：

    [![“添加属性”下拉菜单](device-manager-images/win/13-add-property-menu-sml.png)](device-manager-images/win/13-add-property-menu.png#lightbox)

6. 单击“创建”按钮（位于右下角）以创建新设备：

    ![“创建”按钮](device-manager-images/win/14-create-button.png)

7. 可能会出现“许可证接受”屏幕。 如果同意许可条款，请单击“接受”：

    ![“许可证接受”屏幕](device-manager-images/win/15-license-acceptance.png)

8. 在设备创建期间，Android Device Manager 将新设备添加到已安装虚拟设备列表中，同时显示“正在创建”进度指示器：

    [![创建进度指示器](device-manager-images/win/16-creating-the-device-sml.png)](device-manager-images/win/16-creating-the-device.png#lightbox)

9. 创建过程完成后，新设备会显示在已安装虚拟设备的列表中，并且会显示可以启动的“启动”按钮：

   [![可以启动的新创建的设备](device-manager-images/win/17-created-device-sml.png)](device-manager-images/win/17-created-device.png#lightbox)


### <a name="edit-device"></a>编辑设备

若要编辑现有的虚拟设备，请选择设备并单击“编辑”按钮（位于屏幕的右上方）：

[![用于修改新设备的“编辑”按钮](device-manager-images/win/19-edit-button-sml.png)](device-manager-images/win/19-edit-button.png#lightbox)

单击“编辑”为所选的虚拟设备启动“设备编辑器”：

[![“设备编辑器”屏幕](device-manager-images/win/20-device-editor-sml.png)](device-manager-images/win/20-device-editor.png#lightbox)

“设备编辑器”屏幕在第一列中列出了虚拟设备的属性，第二列中为每个属性相应的值。 当选择某个属性时，有关该属性的详细描述会显示在右侧。

例如，在下面的屏幕截图中，`hw.lcd.density` 属性正从“420”更改为“240”：

[![设备编辑示例](device-manager-images/win/21-device-editing-sml.png)](device-manager-images/win/21-device-editing.png#lightbox)

在进行必要的配置更改后，单击“保存”按钮。
有关更改虚拟设备属性的详细信息，请参阅[编辑 Android 虚拟设备属性](~/android/get-started/installation/android-emulator/device-properties.md)。

 
### <a name="additional-options"></a>附加选项

右上方的“&hellip;”菜单中提供适用于设备的附加选项：

[![附加选项菜单的位置](device-manager-images/win/22-overflow-menu-sml.png)](device-manager-images/win/22-overflow-menu.png#lightbox)

附加选项菜单中包含以下项：

-   **复制和编辑** &ndash; 复制当前所选的设备，并会在“新建设备”屏幕中使用不同的唯一名称打开。 例如，选择“VisualStudio_android-23_x86_phone”，然后单击“复制和编辑”将计数器追加到名称中：

    [![“复制和编辑”屏幕](device-manager-images/win/23-dupe-and-edit-sml.png)](device-manager-images/win/23-dupe-and-edit.png#lightbox)

-   **在资源管理器中展现** &ndash; 在包含用于虚拟设备的文件的文件夹中打开“Windows 资源管理器”窗口。 例如，选择“Nexus 5X API 25”并单击“在资源管理器中展现”以打开一个窗口，如下所示：

    [![单击“在资源管理器中展现”的结果](device-manager-images/win/24-reveal-in-explorer-sml.png)](device-manager-images/win/24-reveal-in-explorer.png#lightbox)

-   **恢复出厂设置**  &ndash; 将所选设备重置为其默认设置，擦除用户在此设备运行时对其内部状态进行的任何更改（这也会擦除当前[快速启动](~/android/deploy-test/debugging/android-sdk-emulator/running-the-emulator.md#quick-boot)快照（如果有））。 此更改不会影响在创建和编辑期间对虚拟设备做出的修改。 将出现提醒此重置无法被撤消的一个对话框。 单击“擦除用户数据”以确认重置。

-   **删除** &ndash; 永久删除所选的虚拟设备。
    将出现提醒删除设备无法被撤消的一个对话框。 如果确定要删除设备，请单击“删除”。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

本文介绍了如何使用 Android Device Manager 创建、复制、自定义和启动 Android 虚拟设备。

[![“设备”选项卡中 Android Device Manager 的屏幕截图](device-manager-images/mac/01-devices-dialog-sml.png)](device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> 本指南仅适用于 Visual Studio for Mac。
Xamarin Studio 与 Android Device Manager 不兼容。

使用 Android Device Manager 创建和配置在 [Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md) 中运行的 Android 虚拟设备 (AVD)。
每台 AVD 是模拟物理 Android 设备的仿真器配置。 这样可以在模拟不同物理 Android 设备的多种配置中运行和测试应用。

## <a name="requirements"></a>惠?

- Visual Studio for Mac 7.5 或更高版本。

- 必须通过 Android SDK 管理器安装 Android SDK 8.0 (API 26) 或更高版本。


## <a name="launching-the-device-manager"></a>启动设备管理器

通过单击“工具”>“Device Manager”启动 Android Device Manager：

[![从“工具”菜单启动](device-manager-images/mac/16-tools-menu-sml.png)](device-manager-images/mac/16-tools-menu.png#lightbox)

在使用 Android Device Manager 之前，必须先安装 Android SDK 工具版本 26.0.2 或更高版本。 如果未安装 Android SDK 工具 26.0.2 或更高版本，在启动时会看到以下错误对话框：

![“Android SDK 实例错误”对话框](device-manager-images/mac/02-sdk-instance-error.png)

如果看到此错误对话框，请单击“确定”以打开 Android SDK 管理器。 在 Android SDK 管理器中，单击“工具”选项卡并安装以下工具：

-   Android SDK Tools 26.0.2 或更高版本 
-   Android SDK 平台工具 26.0.0 或更高版本 
-   Android SDK 生成工具 26.0.0 或更高版本

这些包应显示为“已安装”状态，如下面的屏幕截图所示：

[![安装 Android SDK Tools 26.0](device-manager-images/mac/03-sdk-tools-sml.png)](device-manager-images/mac/03-sdk-tools.png#lightbox)

## <a name="main-screen"></a>主屏幕

首次启动 Android 设备管理器时，它会展现一个显示所有当前已配置的虚拟设备的屏幕。 对于每台设备，将显示“名称”、“系统映像”（Android API 级别）、CPU、“内存”大小以及屏幕分辨率：

[![已安装设备及其参数的列表](device-manager-images/mac/05-devices-list-sml.png)](device-manager-images/mac/05-devices-list.png#lightbox)

单击“播放”按钮以通过所选的虚拟设备启动仿真器：

[![设备的“启动”按钮图像](device-manager-images/mac/06-start-button-sml.png)](device-manager-images/mac/06-start-button.png#lightbox)

通过所选虚拟设备启动仿真器后，“播放”按钮将更改为可用于终止运行仿真器的“停止”按钮：

[![正在运行的设备的“停止”按钮](device-manager-images/mac/07-stop-button-sml.png)](device-manager-images/mac/07-stop-button.png#lightbox)

### <a name="new-device"></a>新设备

若要创建新设备，请单击“新建设备”按钮（位于屏幕的右上方区域）：

[![用于创建新设备的“新建”按钮](device-manager-images/mac/08-new-button-sml.png)](device-manager-images/mac/08-new-button.png#lightbox)

单击“新建设备”以启动“新建设备”屏幕：

[![设备管理器的“新建设备”屏幕](device-manager-images/mac/09-new-device-editor-sml.png)](device-manager-images/mac/09-new-device-editor.png#lightbox)

使用以下步骤在“新建设备”屏幕中配置新设备：

1. 通过单击“设备”下拉菜单以选择要仿真的物理设备：

    [![“设备”下拉菜单](device-manager-images/mac/10-device-menu-sml.png)](device-manager-images/mac/10-device-menu.png#lightbox)

2. 通过单击“系统映像”下拉菜单以选择要与此虚拟设备一起使用的系统映像。 此菜单在“已安装”下列出已安装的系统设备管理器映像。 “下载”部分（如有显示）列出当前开发计算机上不可用但可以自动安装的系统设备管理器映像：

    [![“系统映像”下拉菜单](device-manager-images/mac/11-system-image-menu-sml.png)](device-manager-images/mac/11-system-image-menu.png#lightbox)

3. 为设备提供新名称。 在下面的示例中，新设备名为“Nexus 5X API 25”：

    [![为新设备命名](device-manager-images/mac/12-device-name-sml.png)](device-manager-images/mac/12-device-name.png#lightbox)

4. 编辑需要修改的任何属性。 若要对属性进行更改，请参阅[编辑 Android 虚拟设备属性](~/android/get-started/installation/android-emulator/device-properties.md)。

5. 添加需要显式设置的任何其他属性。 尽管“新建设备”屏幕只列出了最常修改的属性，但你可以单击“添加属性”下拉菜单（位于左下角）以添加其他的属性：

    [![“添加属性”下拉菜单](device-manager-images/mac/13-add-property-menu-sml.png)](device-manager-images/mac/13-add-property-menu.png#lightbox)

6. 也可以单击“自定义”为设备定义新属性：

    ![“创建”按钮](device-manager-images/mac/14-custom-button.png)

7. 单击“创建”按钮（位于右下角）以创建新设备：

    ![“创建”按钮](device-manager-images/mac/15-create-button.png)

8. 可能会出现“许可证接受”屏幕。 如果同意许可条款，请单击“接受”。

9. 在设备创建期间，Android Device Manager 将新设备添加到已安装虚拟设备列表中，同时显示“正在创建”进度指示器：

    [![创建进度指示器](device-manager-images/mac/17-creating-the-device-sml.png)](device-manager-images/mac/17-creating-the-device.png#lightbox)

10. 创建过程完成后，新设备会显示在设备列表中，并且会显示可以启动的“播放”按钮：

   [![可以启动的新创建的设备](device-manager-images/mac/18-created-device-sml.png)](device-manager-images/mac/18-created-device.png#lightbox)


### <a name="edit-device"></a>编辑设备

若要编辑现有的虚拟设备，请选择“其他选项”下拉菜单（齿轮图标），然后选择“编辑”：
 
[![用于修改新设备的“编辑”菜单选项](device-manager-images/mac/19-edit-button-sml.png)](device-manager-images/mac/19-edit-button.png#lightbox)

单击“编辑”为所选的虚拟设备启动“设备编辑器”：
 
[![“设备编辑器”屏幕](device-manager-images/mac/20-device-editor-sml.png)](device-manager-images/mac/20-device-editor.png#lightbox)

“设备编辑器”屏幕在第一列中列出了虚拟设备的属性，第二列中为每个属性相应的值。 当选择某个属性时，有关该属性的详细描述会显示在右侧。

例如，在下面的屏幕截图中，`hw.lcd.density` 属性正从“320”更改为“240”并且 `hw.ramSize` 已更改为“768”：
 
[![设备编辑示例](device-manager-images/mac/21-device-editing-sml.png)](device-manager-images/mac/21-device-editing.png#lightbox)

在进行必要的配置更改后，单击“保存”按钮。
有关更改虚拟设备属性的详细信息，请参阅[编辑 Android 虚拟设备属性](~/android/get-started/installation/android-emulator/device-properties.md)。

 
### <a name="additional-options"></a>附加选项

“播放”按钮左侧的下拉菜单中提供适用于设备的附加选项：

[![附加选项菜单的位置](device-manager-images/mac/22-overflow-menu-sml.png)](device-manager-images/mac/22-overflow-menu.png#lightbox)

附加选项菜单中包含以下项：

-   **编辑** &ndash; 在设备编辑器中打开当前所选设备，如之前所述。

-   **复制和编辑** &ndash; 复制当前所选的设备，并会在“新建设备”屏幕中使用不同的唯一名称打开。
    例如，选择“Nexus 5X API 25”，然后单击“复制和编辑”将计数器追加到名称中：

    [![“复制和编辑”屏幕](device-manager-images/mac/23-dupe-and-edit-sml.png)](device-manager-images/mac/23-dupe-and-edit.png#lightbox)

-   **在查找器中展现** &ndash; 在包含用于虚拟设备的文件的文件夹中打开 macOS Finder 窗口。 例如，选择“Nexus 5X API 25”并单击“在查找器中展现”以打开一个窗口，如下所示：

    [![单击“在资源管理器中展现”的结果](device-manager-images/mac/24-reveal-in-finder-sml.png)](device-manager-images/mac/24-reveal-in-finder.png#lightbox)

-   **恢复出厂设置**  &ndash; 将所选设备重置为其默认设置，擦除任何用户在此设备运行时对其内部状态进行的更改。 此更改不会影响在创建和编辑期间对虚拟设备做出的修改。 将出现提醒此重置无法被撤消的一个对话框。 单击“擦除用户数据”以确认重置。

-   **删除** &ndash; 永久删除所选的虚拟设备。
    将出现提醒删除设备无法被撤消的一个对话框。 如果确定要删除设备，请单击“删除”。

-----

## <a name="summary"></a>总结

本指南介绍 Visual Studio for Mac 和 Visual Studio Tools for Xamarin 中提供的 Android Device Manager。 其中介绍了启动和停止 Android 仿真器、选择要运行的 Android 虚拟设备 (AVD)、创建新的虚拟设备以及如何编辑虚拟设备等基本功能。 还说明了如何编辑配置文件硬件属性以进一步自定义。


## <a name="related-links"></a>相关链接

- [对 Android SDK 工具的更改](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [使用 Android SDK 仿真器进行调试](~/android/deploy-test/debugging/android-sdk-emulator/index.md)
- [SDK Tools 发行说明 (Google)](https://developer.android.com/studio/releases/sdk-tools)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
