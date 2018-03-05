---
title: "Google 仿真器管理器"
description: "如何使用 Google 仿真器管理器创建和管理 Android 虚拟设备"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0C0BBEC0-C84A-4558-B905-4EF81FCD62F9
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 12/22/2017
ms.openlocfilehash: f275ff6c7d3e6eeec5eb3878cc39633d70238f66
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="google-emulator-manager"></a>Google 仿真器管理器

验证硬件加速已启用后（如 [Android 仿真器硬件加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md)中所述），下一步是创建用于测试和调试应用的虚拟设备。 可以使用旧版 Google 仿真器管理器（也称为 Android 虚拟设备 (AVD) 管理器）创建虚拟设备以供 Android SDK 仿真器使用。

> [!NOTE]
> **注意：**如果你面向的是 Android 8.0 Oreo，则必须使用 [Xamarin Android 设备管理器](~/android/get-started/installation/android-emulator/xamarin-device-manager.md)来创建和配置虚拟设备。

<a name="sysimg" />

## <a name="installing-system-images"></a>安装系统映像

必须下载并安装 Android SDK 仿真器使用的 API 级别专属系统映像，具体视目标 Android API 级别而定。 对于每个 Android API 级别，都需要先下载并安装一组 x86 系统映像，然后才能创建虚拟设备。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要安装必需系统映像，请启动“Android SDK 管理器”（依次转到“工具”>“Android”>“Android SDK 管理器”）再滚动到要支持的 API 级别。 对于每个 API 级别，选中以下系统映像旁边的复选标记：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要安装必需的系统映像，请启动“Android SDK 管理器”（“工具”>“SDK 管理器”），然后滚动到要支持的 API 级别。 对于每个 API 级别，选中以下系统映像旁边的复选标记：

-----

-   **Intel x86 Atom 系统映像**
-   **Google API Intel x86 Atom 系统映像**

后一个系统映像会将 Google API（例如，Google 地图 API）添加到虚拟设备中。 

在下面的屏幕截图中，将安装 Intel x86 Atom 映像，以便可以创建运行 Android 6.0 的虚拟设备：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![为 Android 仿真器选择 Android 6.0 x86 系统映像](google-emulator-manager-images/win/03-select-x86-images-sml.png)](google-emulator-manager-images/win/03-select-x86-images.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![为 Android 仿真器选择 Android 6.0 x86 系统映像](google-emulator-manager-images/mac/02-select-x86-images-sml.png)](google-emulator-manager-images/mac/02-select-x86-images.png)

-----

若要开发 64 位应用，请改为安装以下系统映像：

-   **Intel x86 Atom_64 系统映像**
-   **Google API Intel x86 Atom_64 系统映像**

可以使用这些 64 位系统映像运行 32 位应用；不过，32 位 Intel x86 Atom 系统映像在 Android SDK 仿真器中的运行速度略快一些。

若要开发 Android Wear 应用，请安装以下系统映像：

-   **Android Wear Intel x86 Atom 系统映像**
-   **Google API Intel x86 Atom 系统映像**

安装这些系统映像后，可以在配置虚拟设备（下一部分将介绍具体操作方法）过程中，选择适当的 API 级别和 CPU/ABI 选项，创建基于 x86 的 Android 虚拟设备。


<a name="virtualdevice" />

## <a name="configuring-virtual-devices"></a>配置虚拟设备

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

虚拟设备是通过 Android 仿真器管理器（亦称为“Android 虚拟设备管理器” 或“AVD 管理器”）进行配置。 若要通过 Visual Studio 启动 Android 仿真器管理器，请单击工具栏中的“Android 仿真器管理器”图标：

[![AVD 图标位置](google-emulator-manager-images/win/04-avd-icon-sml.png)](google-emulator-manager-images/win/04-avd-icon.png)

也可以依次选择“工具”>“Android”>“Android 仿真器管理器”，通过菜单栏启动 Android 仿真器管理器：

[![“Android 仿真器管理器”菜单项位置](google-emulator-manager-images/win/05-avd-manager-menu-item-sml.png)](google-emulator-manager-images/win/05-avd-manager-menu-item.png)

“Android 虚拟设备(AVD)管理器”显示现有 Android 虚拟设备的列表：

[![Android 虚拟设备管理器](google-emulator-manager-images/win/06-virtual-device-manager-sml.png)](google-emulator-manager-images/win/06-virtual-device-manager.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

虚拟设备是通过 Android 仿真器管理器（亦称为“Android 虚拟设备管理器” 或“AVD 管理器”）进行配置。 

可以依次选择“工具”>“Google 仿真器管理器”，从菜单栏启动 Android 仿真器管理器：

[![“Android 仿真器管理器”菜单项位置](google-emulator-manager-images/mac/03-avd-manager-menu-item-sml.png)](google-emulator-manager-images/mac/03-avd-manager-menu-item.png)

“Android 虚拟设备(AVD)管理器”显示现有 Android 虚拟设备的列表：

[![Android 虚拟设备管理器](google-emulator-manager-images/mac/05-virtual-device-manager-sml.png)](google-emulator-manager-images/mac/05-virtual-device-manager.png)

-----

可以新建具有不同设备特征和 API 级别的虚拟设备映像，下一部分将介绍如何创建自定义设备定义和虚拟设备。

<a name="custom-def" />

### <a name="creating-a-custom-device-definition"></a>创建自定义设备定义

若要创建自定义设备定义，请单击“Android 虚拟设备(AVD)管理器”中的“创建...”。 这将打开“新建 Android 虚拟设备(AVD)”对话框：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![基于 Nexus 6 的自定义设备定义](google-emulator-manager-images/win/07-custom-device-sml.png)](google-emulator-manager-images/win/07-custom-device.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![基于 Nexus 6 的自定义设备定义](google-emulator-manager-images/mac/06-custom-device-sml.png)](google-emulator-manager-images/mac/06-custom-device.png)

-----

在此对话框中，配置以下选项：

-   **AVD 名称** &ndash; 设备定义的唯一名称。 在上面的示例屏幕截图中，此名称设为“MyNexus”。 请注意，AVD 名称不能包含空格。若试图在 AVD 名称中使用空格，“确定”按钮将处于禁用状态。

-   **设备** &ndash; 选择要仿真的硬件配置文件（例如，“Nexus 5”或“Nexus 6”）。

-   **目标** &ndash; 选择虚拟设备的 Android API 级别。 此设置应大于或等于应用的最低 Android 版本。

-   **CPU/ABI** &ndash; 选择“Google API Intel Atom (x86)”，以便可以在设备定义中使用 Google API。

-   **皮肤** &ndash; 选择虚拟设备的外观。 在上面的示例屏幕截图中，选择的是“HVGA”皮肤（本文结尾处的仿真器示例屏幕截图使用的就是“HVGA”皮肤）。

-   **内存选项** &ndash; 默认 RAM 设置通常过高，导致 Windows 上生成以下警告：“可能无法仿真大于 768M 的 RAM”。 对于大多数用户，建议将 RAM 设置为 768MB（如上面的屏幕截图所示）。 RAM 值较高可能会减缓仿真器的运行速度。

-   **使用主机 GPU** &ndash; 选中此选项后，仿真器会使用主机的图形处理单元 (GPU) 执行图形操作。 建议选中此选项，以进一步提升仿真器的性能。 有关“仿真选项”部分的详细信息，请参阅[什么是快照以及“使用主机 GPU”仿真选项有何用途？](https://android.stackexchange.com/questions/51739/what-is-snapshot-and-use-host-gpu-emulation-options-for)


对于剩余选项，可以保留默认设置。 一切就绪之后，单击“确定”，新建虚拟设备。 下一个对话框将详细介绍新虚拟设备的配置结果：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![新建 AVD 后的“结果”对话框](google-emulator-manager-images/win/08-create-results.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![新建 AVD 后的“结果”对话框](google-emulator-manager-images/mac/07-create-results.png)

-----

有关此对话框中列出的配置属性的详细说明，请参阅[硬件配置文件属性](https://developer.android.com/studio/run/managing-avds.html#hpproperties)。
单击“确定”后，新设备配置将显示在现有 Android 虚拟设备列表中。 在下面的屏幕截图中，列表中新增了“MyNexus”：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![MyNexus 已被添加到设备列表](google-emulator-manager-images/win/09-added-to-list-sml.png)](google-emulator-manager-images/win/09-added-to-list.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![MyNexus 已被添加到设备列表](google-emulator-manager-images/mac/08-added-to-list-sml.png)](google-emulator-manager-images/mac/08-added-to-list.png)

-----

设备下拉菜单中也添加了新的自定义虚拟设备：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![MyNexus 已被添加到“设备”下拉菜单](google-emulator-manager-images/win/10-available-custom-device-sml.png)](google-emulator-manager-images/win/10-available-custom-device.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![MyNexus 已被添加到“设备”下拉菜单](google-emulator-manager-images/mac/09-available-custom-device-sml.png)](google-emulator-manager-images/mac/09-available-custom-device.png)

-----


<a name="cloning" />

### <a name="cloning-a-device-definition"></a>克隆设备定义

可以通过选择并克隆现有设备定义，新建自定义设备定义。 如果只需对现有设备定义进行一些小的更改即可满足需求，那么最好采用这样的策略。 “Android 虚拟设备(AVD)管理器”中的“设备定义”选项卡列出了所有可用的设备定义：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![可用设备定义列表](google-emulator-manager-images/win/11-device-definitions-sml.png)](google-emulator-manager-images/win/11-device-definitions.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![可用设备定义列表](google-emulator-manager-images/mac/10-device-definitions-sml.png)](google-emulator-manager-images/mac/10-device-definitions.png)

-----

不能修改此列表中的预配置设备，只能编辑用户创建的虚拟设备。 可以选择预配置设备定义，再单击“克隆”，从而通过此设备定义派生新的设备定义。 例如，选择“Nexus 5”定义，再单击“克隆”，将会看到以下对话框：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![“克隆设备”对话框](google-emulator-manager-images/win/12-clone-device-sml.png)](google-emulator-manager-images/win/12-clone-device.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![“克隆设备”对话框](google-emulator-manager-images/mac/11-clone-device-sml.png)](google-emulator-manager-images/mac/11-clone-device.png)

-----

在下一张屏幕截图中，名称改为 **Nexus 5 Custom** 且已修改设备参数，以创建新的自定义设备定义：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![自定义 Nexus 5 AVD](google-emulator-manager-images/win/13-custom-nexus.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![自定义 Nexus 5 AVD](google-emulator-manager-images/mac/12-custom-nexus-sml.png)](google-emulator-manager-images/mac/12-custom-nexus.png)

-----

单击“克隆设备”可创建新的设备定义，该定义现显示在**设备定义**列表中：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![“Nexus 5 Custom”显示为新用户设备定义](google-emulator-manager-images/win/14-new-definition-sml.png)](google-emulator-manager-images/win/14-new-definition.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![“Nexus 5 Custom”显示为新用户设备定义](google-emulator-manager-images/mac/13-new-definition-sml.png)](google-emulator-manager-images/mac/13-new-definition.png)

-----

请注意，用户创建的每个设备定义都将显示一个绿色图标，如上所示。 可以选择此新设备定义，再单击“新建 AVD”，从而使用此定义新建 AVD。 这将显示“新建 Android 虚拟设备(AVD)”对话框。 在下面的示例中，自动为新虚拟设备生成了名称“AVD\_for\_Nexus\_5\_Custom”：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![通过“Nexus 5 Custom”用户设备定义创建 AVD](google-emulator-manager-images/win/15-create-avd-sml.png)](google-emulator-manager-images/win/15-create-avd.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![通过“Nexus 5 Custom”用户设备定义创建 AVD](google-emulator-manager-images/mac/14-create-avd-sml.png)](google-emulator-manager-images/mac/14-create-avd.png)

-----

单击“确定”后，现有 Android 虚拟设备列表中将会显示此自定义设备配置。 此外，它还被添加到“设备”下拉菜单中：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![新的自定义 AVD 已被添加到“设备”下拉菜单](google-emulator-manager-images/win/16-new-avd-sml.png)](google-emulator-manager-images/win/16-new-avd.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![新的自定义 AVD 已被添加到“设备”下拉菜单](google-emulator-manager-images/mac/15-new-avd-sml.png)](google-emulator-manager-images/mac/15-new-avd.png)

-----

