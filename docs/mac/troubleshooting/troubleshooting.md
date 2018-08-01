---
title: Xamarin.Mac 故障排除提示
description: 本文档介绍用于解决开发 Xamarin.Mac 应用程序时遇到的问题的方法。 它还介绍了如何获取支持。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 5e6cd5b338034cfa9956244015d4585b4f005793
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792962"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Xamarin.Mac 故障排除提示

## <a name="overview"></a>概述

有时，所有我们就会在处理项目，无法获取按我们希望的方式使用 API 或尝试解决 bug 时中断。 若要成功地编写你的移动和桌面应用程序，我们在 Xamarin 的目标是和我们提供了一些可帮助资源。

与任意这些资源，有一些准备工作可以采取用来帮助他们快速解决你遇到的问题的步骤：

- 确定最佳尽可能接近报表崩溃问题的根本原因：
 
     - "我的应用程序崩溃"难以进行诊断。 "我的应用程序崩溃时我返回到此调用的空数组"可以更轻松地在修复上工作。

     - "无法获得 NSTable 工作"适用于下列不太不是"针对我 NSTableDelegate 的方法都不在此情况下调用。"

- 如果可能提供显示问题的小型示例程序。 通过查找问题的源代码页的深入采用数量级的更多时间和精力。

- 了解什么你对所做的更改你的应用程序导致问题以显示可以快速缩小问题的源。 注意是否你最近已升级的 Xamarin.Mac，修剪掉的节的应用程序，以查找导致出现此问题，部件的版本或测试上一个版本，使其查找哪些更改引入问题会非常有帮助。


### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>你的应用程序崩溃且无输出时应该如何操作

在大多数情况下，适用于 Mac 的 Visual Studio 中的调试器将捕获异常并在你的应用程序发生崩溃，帮助你查明根本原因。 但是在某些情况下，你的应用程序会反弹停靠窗口，然后退出与很少或没有输出。 这些错误可能包括：

- 代码签名问题。
- 某些单声道运行时崩溃。
- 某些 Objective c 异常和崩溃。
- 某些崩溃非常早的进程生存期。
- 某些堆栈溢出。
- 中列出的 macOS 版本你**Info.plist**较新，不是你当前安装的 macOS 版本或它无效。

调试这些程序会令人沮丧，作为查找所需的信息可能很困难。 这里有几个可能帮助的方法：

- 确保中列出的 macOS 版本**Info.plist**为的同一 macOS 当前计算机上安装的版本。
- Visual Studio 查找 Mac 应用程序输出 (**视图** -> **填充** -> **应用程序输出**) 的堆栈跟踪，或以红色从输出可以描述输出的 Cocoa。
- 从命令行中运行你的应用程序并查看输出 (在**终端**应用) 使用： 

     `MyApp.app/Contents/MacOS/MyApp` (其中`MyApp`是你的应用程序的名称)
- 可以通过将"MONO_LOG_LEVEL"添加到你的命令在命令行中，例如提高输出： 

     `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- 你可以将附加本机调试器 (`lldb`) 到过程以查看是否有提供 （这需要付费的许可证） 的任何详细信息。 例如，执行以下操作：

    1. 输入`lldb MyApp.app/Contents/MacOS/MyApp`在终端。
    2. 输入`run`在终端。
    3. 输入`c`在终端。
    4. 在完成调试时退出。
- 作为最后一招，然后才能调用`NSApplication.Init`中你`Main`方法 （或在其他位置中根据需要），无法将文本写入的文件中的已知位置来跟踪在哪些步骤启动是否遇到了问题。

## <a name="known-issues"></a>已知问题

以下各节涵盖已知的问题和其解决方案。

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>无法连接到沙盒应用程序中的调试器

调试器连接到 Xamarin.Mac 应用的整个 TCP，这意味着，默认情况下启用后沙盒处理，它将无法连接到应用程序，因此如果你尝试在不适当的权限启用的情况下运行应用程序，你将收到一个错误 *"无法连接到调试器"*。 

[![编辑权利](troubleshooting-images/debug01.png "编辑权利")](troubleshooting-images/debug01-large.png#lightbox)

**允许传出网络连接 （客户端）** 权限是所需的调试器，启用此操作将允许通常调试。 你无法调试没有它，因为我们已更新`CompileEntitlements`目标`msbuild`自动将该权限添加到权利，为仅是沙盒处理以用于调试任何应用版本。 发布版本应使用在权利文件中，不做任何修改指定的权利。

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>没有数据是可用于编码 437 System.NotSupportedException:
 
时包括 Xamarin.Mac 应用程序中的第三方库，你可能会在窗体中的错误"System.NotSupportedException： 不会提供编码 437 数据"时尝试编译并运行应用。 例如，库，如`Ionic.Zip.ZipFile`，可能会引发此异常在操作过程。

这可以通过打开 Xamarin.Mac 项目中，转到的选项来解决**Mac 生成** > **国际化**和检查**西部**国际化：

[![编辑生成选项](troubleshooting-images/issue01.png "编辑生成选项")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>编译 (mm5103) 失败

此错误通常被由于在 Xcode 的新版本是版本且已安装新版本，而不是尚未运行它。 然后再尝试使用 Xcode 的新版本进行编译，你需要首先至少一次运行该版本。

首次运行新版本的 Xcode，它会安装所需的 Xamarin.Mac 的多个命令行工具。 此外，你应更新 Xcode 或你 Xamarin.Mac 版本后执行一个干净的生成。

如果你不能解决此问题，请[提交 bug](#filing-a-bug)。

### <a name="missing-entitlementsplist"></a>缺少 entitlements.plist

适用于 Mac 的 Visual Studio 的最新版本已删除从授权部分**Info.plist**编辑器并放入单独**Entitlements.plist**编辑器 （用于更好地跨平台支持使用 Xamarin.iOS)。

使用新的 Visual Studio 对已安装的 Mac，当你创建一个新的 Xamarin.Mac 应用程序项目， **Entitlements.plist**文件将自动添加到项目树：

![选择权利](troubleshooting-images/entitlements01.png "选择权利")

如果双击**Entitlements.plist**文件，权利编辑器将显示：

[![编辑权利](troubleshooting-images/entitlements02.png "编辑权利")](troubleshooting-images/entitlements02-large.png#lightbox)

对于现有 Xamarin.Mac 项目，你将需要手动创建**Entitlements.plist**通过右键单击中的项目文件**解决方案 Pad**并选择**添加**  > **新文件...**.接下来，选择**Xamarin.Mac** > **空属性列表**:

![添加新的属性列表](troubleshooting-images/entitlements03.png "添加新的属性列表")

输入`Entitlements`的名称，然后单击**新建**按钮。 如果你的项目以前包含授权文件，你将会提示您将其添加到项目而不是创建新的文件：

[![验证文件的覆盖](troubleshooting-images/entitlements04.png "验证文件的覆盖")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="contacting-support-business-or-enterprise-licenses"></a>联系支持 （企业或企业许可证）

如果你有一个业务或企业版许可证，你都可直接从 Xamarin 工程师通过支持票证请求帮助。 请参阅[xamarin.com/support](http://xamarin.com/support)有关详细信息。

## <a name="community-support-on-the-forums"></a>在论坛上的社区支持

使用 Xamarin 产品的开发人员社区是令人惊叹，许多请访问我们[Xamarin.Mac 论坛](http://forums.xamarin.com/categories/mac)地分享体验和他们的专业技能。 此外，Xamarin 工程师定期访问帮助的论坛。

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>填写 bug

你的反馈对我们很重要。 如果你发现 Xamarin.Mac 中的任何问题：

- 搜索[问题存储库](https://github.com/xamarin/xamarin-macios/issues) 
- 切换到 GitHub 问题之前，会在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) 中跟踪 Xamarin 问题。 请在其中搜索匹配的问题。
- 如果找不到匹配的问题，请在 [GitHub 问题存储库](https://github.com/xamarin/xamarin-macios/issues/new)中提交一个新问题。

GitHub 的问题是完全公开的。 不能隐藏注释或附件。 

请尽可能多地包含以下内容：                                                                                                                                          

- 一个重现此问题的简单示例。 在可能的情况下，这非常有用。 
- 故障的完整堆栈跟踪。
- 故障周围的 C# 代码。 
