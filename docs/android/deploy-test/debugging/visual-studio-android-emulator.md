---
title: Visual Studio Android 仿真器
description: 本指南介绍如何配置和使用 Visual Studio Android 仿真器在 Visual Studio 2015 中开发 Xamarin.Android 应用。
ms.prod: xamarin
ms.assetid: CD128CB9-499F-4558-B49F-77248824EFDF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/30/2018
ms.openlocfilehash: bcc88ba4c46aa9abe9038d6ea65bc9c17f337da6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="visual-studio-android-emulator"></a>Visual Studio Android 仿真器

_本指南介绍如何配置和使用 Visual Studio Android 仿真器在 Visual Studio 2015 中开发 Xamarin.Android 应用。_

## <a name="visual-studio-android-emulator-overview"></a>Visual Studio Android 仿真器概述

Microsoft Visual Studio 2015 包括一个 Android 模拟器，该模拟器可用作调试 Xamarin.Android 应用 (Visual Studio Emulator for Android) 的目标。 此模拟器使用开发计算机的 Hyper-V 功能，相较于 Android SDK 附带的默认模拟器，能够更快地启动执行。 开发 Xamarin.Android 应用程序时，Visual Studio Emulator for Android 可用于替代默认的 Android SDK 模拟器。

> [!NOTE]
> Visual Studio Android Emulator 仅与 Visual Studio 2015 兼容 &ndash; 它不适用于 Visual Studio 2017。

本指南说明如何从 Visual Studio 启动 Microsoft Android 模拟器来测试应用，并描述了模拟器的各种可用功能。 你将学习如何选择设备配置文件（类似于默认 Android SDK 模拟器中的设备定义）来模拟不同类型的 Android 设备。 最后，疑难解答部分说明了常见缺陷和解决方法。

## <a name="requirements"></a>惠?

要运行模拟器，计算机必须满足 Hyper-V 运行要求。 Hyper-V 要求 64 位专业版的 Windows 8、Windows 8.1、Windows 10 或更高版本。 有关要求的详细信息，请参阅 [Visual Studio Emulator for Android 系统要求](https://msdn.microsoft.com/en-us/library/mt228280.aspx)。

> [!NOTE]
> 启用 Hyper-V 时，无法使用 HAXM（用于 Android SDK 仿真器）。 若要详细了解 HAXM 存在的限制和潜在问题，请参阅 [HAXM 虚拟化冲突](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#virt-conflicts)。


## <a name="running-the-emulator"></a>运行模拟器

在“调试目标”下拉菜单中（如以下屏幕快照所示），Visual Studio 提供多个预配置的目标设备配置文件。 Microsoft Android 模拟器目标以“VS 模拟器”开头：

[![预配置的目标设备配置文件](visual-studio-android-emulator-images/01-vs-emulator-defaults-vs-sml.png)](visual-studio-android-emulator-images/01-vs-emulator-defaults-vs.png#lightbox)

当 Visual Studio 启动 Xamarin.Android 应用程序时，会启动具有选定设备目标的模拟器，且该应用会部署到模拟器。 Visual Studio 左下角会显示一条消息，指示模拟器正在启动：

[![启动 VS 仿真器](visual-studio-android-emulator-images/02-emulator-starting-vs-sml.png)](visual-studio-android-emulator-images/02-emulator-starting-vs.png#lightbox)

启动片刻后，会出现模拟器屏幕，如左下图所示。 向上拖动屏幕锁定图标，解锁设备。
然后 Xamarin.Android 应用就会在模拟器中运行，如右图所示：

[![仿真器屏幕快照](visual-studio-android-emulator-images/03-first-screen-vs-sml.png)](visual-studio-android-emulator-images/03-first-screen-vs.png#lightbox)

使用默认 Android SDK 模拟器时，可以设置代码中的断点、检查变量，并查看调用堆栈。 通过模拟器右侧的垂直工具栏，可以获取模拟器功能：

[![垂直工具栏上的按钮](visual-studio-android-emulator-images/04-vertical-toolbar-vs-sml.png)](visual-studio-android-emulator-images/04-vertical-toolbar-vs.png#lightbox)

以下列表总结了垂直工具栏中每个按钮的功能：

-   **关闭** &ndash; 关闭模拟器应用程序。 此按钮不经常使用 &ndash; 通常情况下，第一次启动模拟器后，会让它保持运行状态（以避免模拟器重启延迟），仅当不再需要时才关闭。

-   **最小化** &ndash; 保持运行模拟器，但将其最小化到任务栏。

-   **电源** &ndash; 模拟打开和关闭设备。 （模拟器保持运行。）

-   **多点触控** &ndash; 在设备显示屏上分布了一些点，作为用于收聚和缩放的触摸点。
    拖动一个点会使另一个点在相反方向移动，从而模拟两指移动。

-   **单点鼠标输入** &ndash;（在使用多点触控输入后）让设备变回单点输入。

-   **向左/向右旋转** &ndash; 帮助测试应用如何响应方向更改。 例如，第一次单击向左旋转按钮时，模拟器将切换为横向模式。 单击向右旋转时，模拟器将变回纵向模式。

-   **适应屏幕** &ndash; 缩放模拟器屏幕的大小，使其适应桌面屏幕。

-   **缩放** &ndash; 按 33%、50%、66%、100% 或自定义百分比缩放模拟器屏幕。


其他工具按钮显示一个对话框，打开后显示模拟器的额外功能：

[![“其他工具”对话框](visual-studio-android-emulator-images/05-additional-tools-vs-sml.png)](visual-studio-android-emulator-images/05-additional-tools-vs.png#lightbox)


每项额外功能都可从对话框顶部的选项卡行获取：

-   **加速计** &ndash; 模拟设备在 3D 空间的运动。

-   **位置** &ndash; 显示可用于选择和模拟 GPS 位置的地图。 在此地图中，可以创建地图点，用于模拟位置之间的运动。

-   **电池** &ndash; 提供滑块，用于模拟电池剩余电量。

-   **屏幕快照** &ndash; 在此选项卡中，“捕获”按钮可获取屏幕快照，并显示快速预览。 “保存”按钮用于保存屏幕快照。

-   **照相机** &ndash; 模拟通过固定动画图像、来自文件的图片或来自主计算机上的附加网络摄像头的图片拍照。 可以选择前置或后置相机。

-   **SD 卡** &ndash; 模拟器可以将主机计算机上的文件夹提供给设备作为 SD 卡。
    当应用读取文件并将文件写入模拟的 SD 卡时，可直接从桌面上获取文件，而无需使用 `adb` 命令。

-   **网络** &ndash; 显示模拟器的网络设置摘要（模拟器重新使用主机计算机的网络连接）。

有关如何使用这些功能的详细信息，请参阅 [Introducing Visual Studio's Emulator for Android](https://blogs.msdn.microsoft.com/visualstudioalm/2014/11/12/introducing-visual-studios-emulator-for-android/)（Visual Studio Emulator for Android 简介）。



## <a name="configuring-device-profiles"></a>配置设备配置文件

Microsoft Android 模拟器包括了代表市场上热门 Android 设备的 Android 版本、屏幕大小和硬件属性的一组设备配置文件。 此外，已为 KitKat、Lollipop 和 Marshmallow 等各种 Android 版本配置了这些设备配置文件。

模拟器管理器用于安装、卸载和启动设备配置文件。 从“工具”菜单上，选择“Visual Studio Emulator for Android...”，如该屏幕截图所示：

[![从工具菜单启动仿真器](visual-studio-android-emulator-images/06-launch-emulator-manager-vs-sml.png)](visual-studio-android-emulator-images/06-launch-emulator-manager-vs.png#lightbox)

这会打开“设备配置文件”对话框。 已安装的配置文件突出显示在设备配置文件列表顶部。 未安装（但可安装）的配置文件显示为灰色：

[![“设备配置文件”图标](visual-studio-android-emulator-images/07-device-profiles-vs-sml.png)](visual-studio-android-emulator-images/07-device-profiles-vs.png#lightbox)

若要安装新的配置文件，请单击配置文件安装图标（向下指向箭头，如上述屏幕快照所示）。 例如，单击 **5.7" Marshmallow (6.0.0) XHDPI Phone** 的配置文件安装图标时，模拟器管理器会下载配置文件，如下所示：

[![有关下载配置文件的示例](visual-studio-android-emulator-images/08-downloading-profile-vs-sml.png)](visual-studio-android-emulator-images/08-downloading-profile-vs.png#lightbox)

下载设备配置文件后，会将其突出显示，指示已成功安装该配置文件。 单击“显示详细信息”图标，显示平台类型、CPU 体系结构、屏幕大小/分辨率和设备可用内存：

[![显示设备配置文件详细信息](visual-studio-android-emulator-images/09-show-details-vs-sml.png)](visual-studio-android-emulator-images/09-show-details-vs.png#lightbox)

打开 Visual Studio“调试目标”下拉菜单时，最新安装的设备配置文件现在可以作为目标使用了：

[![目标下拉菜单中的新配置文件](visual-studio-android-emulator-images/10-debug-target-vs-sml.png)](visual-studio-android-emulator-images/10-debug-target-vs.png#lightbox)

通过单击模拟器管理器中的“卸载此配置文件”删除未使用的设备配置文件，可缩短列表。 请注意，目前没有办法在此模拟器中创建自定义设备配置文件。


## <a name="troubleshooting"></a>疑难解答

本部分描述针对 Xamarin.Android 使用 Visual Studio Emulator for Android时的一些常见错误及解决方法。

<a name="cant_connect" />

### <a name="emulator-will-not-start"></a>不能启动模拟器

在某些情况下，如果主机处理器和 Hyper-V 虚拟机之间有不兼容之处，将无法启动模拟器。 若要解决此问题，请配置 Hyper-V 以限制虚拟机可以拥有的处理器功能 &ndash; 这能提高虚拟机与不同主机处理器版本的兼容性。
若要进行此更改，请使用以下步骤：

1.  单击“启动”按钮，键入“MMC”，然后按 **Enter** 键。 单击“Hyper-V 管理器”，如下所示：

    [![Hyper-V 管理器](visual-studio-android-emulator-images/15-launch-hyperv-manager.png)](visual-studio-android-emulator-images/15-launch-hyperv-manager.png#lightbox)

2.  在 Hyper-V 管理器的“虚拟机”窗格中，右键单击要编辑使用的模拟器，并单击“设置…”：

    [![虚拟机设置菜单项](visual-studio-android-emulator-images/16-vm-settings.png)](visual-studio-android-emulator-images/16-vm-settings.png#lightbox)

3.  在设置窗口中，找到“兼容性”部分（在“硬件”>“处理器”下），并启用“迁移到具有不同处理器版本的物理计算机”：

    [![已选中迁移选项](visual-studio-android-emulator-images/17-set-compatibility-vs-sml.png)](visual-studio-android-emulator-images/17-set-compatibility-vs.png#lightbox)

4.  单击“确定”并关闭 Hyper-V 管理器窗口。



### <a name="app-deploys-and-starts-but-fails-immediately"></a>应用可以部署和启动，但会立即失败

这种情况下，模拟器可以启动，应用可以成功部署到模拟器中，并且可以启动。 但是，应用会立即失败。
在许多情况下，这也是由主机处理器和 Hyper-V 虚拟机之间的不兼容引起的。 要解决此错误，请遵循[模拟器不能启动](#cant_connect)（上述部分）中的说明。


### <a name="emulator-stops-with-the-diagnostic-message-libaot-mscorlibdllso-not-found"></a>模拟器停止并显示诊断消息：“找不到 libaot mscorlib.dll.so”

要解决此错误，请使用以下步骤来禁用快速部署：

1.  请遵循[模拟器不能启动](#cant_connect)（上述部分）中的说明。

2.  双击项目“属性”。

3.  单击“Android 选项”并取消选择“使用快速部署（仅限调试模式）”：

    [![未选中“使用快速部署”选项](visual-studio-android-emulator-images/18-fast-deployment-vs-sml.png)](visual-studio-android-emulator-images/18-fast-deployment-vs.png#lightbox)



### <a name="drag-and-drop-does-not-work"></a>无法拖放

如果以管理员身份启动 Visual Studio Emulator for Android（或者如果从 Visual Studio 启动，同时 Visual Studio 使用管理员权限运行），可能无法拖放 .APK 或 .ZIP文件。 要解决此问题，请不要使用提升的权限（即，不以管理员身份）运行 Visual Studio Emulator for Android。


### <a name="other-errors"></a>其他错误

以上疑难解答提示介绍了针对 Xamarin.Android 使用 Visual Studio Android 模拟器时最常见的问题。 若要获取更完整的 Visual Studio Android 模拟器的疑难解答指南，请参阅[适用于 Android 的 Visual Studio 仿真程序疑难解答](https://msdn.microsoft.com/en-us/library/mt228282.aspx)。



## <a name="summary"></a>总结

本文介绍了 Visual Studio Emulator for Android说明了如何使用模拟器在 Visual Studio 中调试 Xamarin.Android 应用，描述了垂直工具栏上各个按钮的功能，并概述了“其他工具”对话框中提供的各项功能。 它阐释了如何使用模拟器管理器来安装、卸载和启动设备配置文件。 “疑难解答”部分介绍了使用模拟器时的常见问题和解决方法。


## <a name="related-links"></a>相关链接

- [适用于 Android 的 Visual Studio 模拟器](https://www.visualstudio.com/en-us/explore/msft-android-emulator-vs.aspx)
- [介绍适用于 Android 的 Visual Studio 仿真器](https://blogs.msdn.microsoft.com/visualstudioalm/2014/11/12/introducing-visual-studios-emulator-for-android/)
