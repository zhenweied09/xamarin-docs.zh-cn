---
title: 安装后缺少 Visual Studio 扩展
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/20/2017
ms.openlocfilehash: 72870b9bf6ff6c3068ee037e6405e4ec03546cd6
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="missing-visual-studio-extensions-after-installation"></a>安装后缺少 Visual Studio 扩展

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>错误消息： 此项目是与当前版本的 Visual Studio 不兼容

确保安装 Visual Studio 的兼容版本：

-   Visual Studio 2017 （Community、 Professional 或 Enterprise）
-   Visual Studio 2015 （Community、 Professional 或 Enterprise）

另请参阅[Windows 要求](~/cross-platform/get-started/requirements.md#windows)。

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>可能的解决方法 1： 更改安装后，若要确保安装 Visual Studio 扩展

在某些情况下，Xamarin 安装程序可能会自动取消选中 Visual Studio 扩展的安装选项。 如果这是问题的原因，则使用安装程序的“更改”命令安装缺少的 Visual Studio 扩展。 例如，安装 Visual Studio 2013 的扩展：

1. 打开 Windows 的“程序和功能”控制面板。

2. 右键单击“Xamarin”项，然后选择“更改”。

3. 单击“下一步”并单击“更改”。

4. 请确保**Xamarin for Visual Studio 2013**选项设置为安装：

    ![](missing-vs-extensions-images/installer.png "启用 Xamarin 的 Visual Studio 2013 安装选项")

5. 继续执行安装程序向导的其余部分。

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>可能的修复 2：要求 Visual Studio 重新安装扩展

1. 检查 Xamarin 扩展是否已复制到 Visual Studio 扩展文件夹中：

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    如果 （用于版本 3.1.228） 正确安装的扩展，将有 60 项的文件夹中：


    ![](missing-vs-extensions-images/folder.png "在资源管理器 Xamarin\3.1.228.0 文件夹内容的列表")

2. 确认此文件夹看起来正确之后，让 Visual Studio 尝试重新安装扩展：

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>可能的修复 3：尝试完全重新安装 Xamarin

1.  在 Windows 控制面板中，卸载存在的所有以下项目：

    *   Xamarin

    *   Xamarin for Windows

    *   Xamarin.Android

    *   Xamarin.iOS

    *   面向 Visual Studio 的 Xamarin

2.  在资源管理器中，删除 Xamarin Visual Studio 扩展文件夹中的任何保留文件（所有版本，包括 **Program Files** 和 **Program Files (x86)**）：

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3.  此外，检查 “VirtualStore”目录，查看是否有扩展目录存在任何“覆盖”副本：

    `%LOCALAPPDATA%\VirtualStore`

4.  打开注册表编辑器 (regedit)。

5.  查找此键：

    _HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls_

6.  查找并删除与此模式匹配的所有项：

    _C:\Program Files\*\Microsoft Visual Studio 1\*.0\Common7\IDE\Extensions\Xamarin_

7.  查找此键：

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8.  删除看起来可能与 Xamarin 相关的所有项。 例如，下面的项曾在旧版 Xamarin 中引发问题：

    _Mono.VisualStudio.Shell,1.0_

9.  重新启动。

10.  重新安装 Xamarin 从当前稳定版本[visualstudio.com](https://visualstudio.com/xamarin)。

## <a name="possible-fix-4-repair-visual-studio-installation"></a>可能的修复 4：修复 Visual Studio 安装

1.  打开 Windows 的“程序和功能”控制面板。

2.  右键单击相关的 Microsoft Visual Studio 项，然后选择“更改”

3.  单击打开的 Visual Studio 对话框中的“修复”按钮。
