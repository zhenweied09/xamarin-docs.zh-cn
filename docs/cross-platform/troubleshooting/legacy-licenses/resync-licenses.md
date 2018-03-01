---
title: "如何手动同步 Xamarin 许可证"
ms.topic: article
ms.prod: xamarin
ms.assetid: D0BD93E9-3A1F-4E5B-8EE8-36ADC33DCFE4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 2413b6b7563a6ed1e17a8db61d2d61ddc85e71ae
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="how-do-i-manually-resynchronize-xamarin-licenses"></a>如何手动同步 Xamarin 许可证

> [!IMPORTANT]
> 本指南不适用于大多数 MSDN 用户由于不需要它们来拥有或登录到 Xamarin 帐户，除非使用[Xamarin 组件存储](https://components.xamarin.com/)或[适用于 Mac (Mac) 的 Visual Studio](~/cross-platform/get-started/requirements.md)。




## <a name="overview"></a>概述

通常您的许可证信息将自动进行重新同步与 Xamarin 许可证服务器启动 IDE 时。 例如，许可证升级、 降级和续订将正常自动后显示重启 IDE。 在 IDE 中显示的许可证信息应匹配上显示的信息你[帐户页](https://store.xamarin.com/account/my/subscription/computers)。 如果仍不匹配的信息，你可以首先请仔细检查你的存储帐户信息正确显示、，然后手动重新许可证同步通过注销、 删除许可证文件在磁盘上，然后重新登录。

### <a name="note-for-ios-developers-on-windows"></a>请注意，在 Windows 上的 iOS 开发人员

在 Windows 上的 iOS 开发人员可能还需要对 Visual Studio 中执行这些步骤，适用于 Mac;即使你直接不在配对 Mac 生成主机上进行开发。

## <a name="quick-manual-refresh-steps"></a>快速手动刷新步骤

此快速的过程会跳过删除它可能在某些情况下足够的磁盘上的许可证文件的步骤。 

1.  注销你通过 IDE 的 Xamarin 帐户：
    -   Visual Studio for Mac
        -   右上角的欢迎屏幕
        -   **Visual Studio for Mac > 帐户**(Mac)
        -   **工具 > 帐户**(Windows)
    -   Visual Studio
        -   **工具 > Xamarin 帐户**
2.  重新登录到你在 IDE 中的 Xamarin 帐户。

## <a name="extended-manual-license-refresh-steps"></a>扩展手动许可证刷新步骤

1.  注销你通过 IDE 的 Xamarin 帐户。 
2.  退出 IDE。
3.  检查存储帐户信息上去正确。 特别是检查重复的计算机名称上[计算机页](https://store.xamarin.com/account/my/subscription/computers)。

4.  如果你看到的成对的重复的计算机名称，使用**停用**要移除的下拉列表菜单项_同时_在对成员：
    
    ![许可证-> 停用上 https://store.xamarin.com/account/my/subscription/computers](resync-licenses-images/deactivate.png "使用停用下拉列表菜单项来删除对这两个成员")

5.  删除任何剩余磁盘上仍然存在的许可证文件的副本。
    -   Windows

        删除所有的以下文件夹：
        -   `%PROGRAMDATA%\Mono for Android`
        -   `%PROGRAMDATA%\MonoTouch`
        -   `%LOCALAPPDATA%\VirtualStore\ProgramData\Mono for Android`
        -   `%LOCALAPPDATA%\VirtualStore\ProgramData\MonoTouch`

        中的 Windows 默认安装：
        -   `%PROGRAMDATA%` 将扩展至 `C:\ProgramData`
        -   `%LOCALAPPDATA%` 将扩展至 `C:\Users\%USERNAME%\AppData\Local`

        `VirtualStore`份许可证在某些方案中由 Windows 自动创建。 如果 VirtualStore 副本存在，则将读取_改为_中的许可证`%PROGRAMDATA%`。

    -   Mac

        删除所有的以下文件夹：

        -   `~/Library/MonoAndroid`
        -   `~/Library/MonoTouch`
        -   `~/Library/Xamarin.Mac`

        你可以访问这些路径中的**Finder**通过选择**转 > 转到文件夹**菜单，然后将它们粘贴到对话框窗口。 Finder 自动替换 ~ 颚化符字符与您的用户文件夹。

6.  打开适用于 Mac 的 Visual Studio 或 Visual Studio 和日志回你的 Xamarin 帐户。

## <a name="supplementary-information-individual-license-file-locations"></a>补充信息： 单个许可证文件位置

### <a name="windows"></a>Windows

在 Windows 上的各个许可证文件存储在以下位置：

-   Xamarin.Android:  
     `%PROGRAMDATA%\Mono for Android\License\monoandroid.licx`
-   Xamarin.iOS:  
     `%PROGRAMDATA%\MonoTouch\License\monotouch.licx`

许可证*试用*订阅的命名方式不同：

-   Xamarin.Android:  
     `%PROGRAMDATA%\Mono for Android\License\monoandroid.trial.licx`
-   Xamarin.iOS:  
     `%PROGRAMDATA%\MonoTouch\License\monotouch.trial.licx`

### <a name="mac"></a>Mac

在 Mac 上的各个许可证文件存储在以下位置：

-   Xamarin.Android:  
     `~/Library/MonoAndroid/License`
-   Xamarin.iOS:  
     `~/Library/MonoTouch/License.v2`
-   Xamarin.Mac:  
     `~/Library/Xamarin.Mac/License`

许可证*试用*订阅的命名方式不同：

-   Xamarin.Android:  
     `~/Library/MonoAndroid/License.trial`
-   Xamarin.iOS:  
     `~/Library/MonoTouch/License.trial`
-   Xamarin.Mac:  
     `~/Library/Xamarin.Mac/License.trial`

## <a name="additional-troubleshooting-steps"></a>其他故障排除步骤

-   检查你是否具有任何非 ASCII 字符在你的用户名、 计算机名，或任何许可证文件的完全展开路径中。

-   [与 Xamarin 开发人员支持部门联系](http://xamarin.com/support)
