---
title: 工具和命令
description: 包括目标 Sharpie 和命令行参数以使用它们的工具的概述。
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 8a307739134fe3b76692fbef5c1dc028af01017d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="tools--commands"></a>工具和命令

_包括目标 Sharpie 和命令行参数以使用它们的工具的概述。_

<style type="text/css"> .terminal 蓝色 {颜色： rgb(10,96,254);}.terminal 绿色 {颜色： rgb(12,156,26);}.terminal 洋红色 {颜色： rgb(152,12,103);} </style>


已成功目标 Sharpie 后[安装](~/cross-platform/macios/binding/objective-sharpie/get-started.md)，打开终端并熟悉<em>命令</em>目标 Sharpie 必须提供：

<pre>$ <b>sharpie -help</b>
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, -help                Show detailed help
  -v, -version             Show version information

Telemetry Options:
  -tlm-about               Show a detailed overview of what usage and binding
                             information will be submitted to Xamarin by
                             default when using Objective Sharpie.
  -tlm-do-not-submit       Do not submit any usage or binding information to
                             Xamarin. Run 'sharpie -tml-about' for more
                             information.
  -tlm-do-not-identify     Do not submit Xamarin account information when
                             submitting usage or binding information to Xamarin
                             for analysis. Binding attempts and usage data will
                             be submitted anonymously if this option is
                             specified.

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation</pre>

目标 Sharpie 提供了以下工具：

|工具|描述|
|--- |--- |
|**xcode**|提供有关当前的 Xcode 安装和版本的 iOS 和 Mac Sdk 所提供的信息。 我们将使用此信息更高版本时我们生成我们的绑定。|
|**pod**|搜索、 配置、 安装 （在本地目录），并将绑定 Objective C [CocoaPod](https://cocoapods.org/) master 规范存储库中可用的库。 此工具会评估已安装的 CocoaPod 自动推导出正确的输入要传递给`bind`下面的工具。 3.0 中新增功能 ！|
|**bind**|分析标头文件 (`*.h`) 到初始 Objective C 库中[ApiDefinition.cs 和 StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)文件。|
|**update**|检查目标 Sharpie 的较新版本和下载并启动安装程序，如果有的话。|
|**verify-docs**|显示有关详细的信息`[Verify]`属性。|
|**docs**|导航到在默认 web 浏览器中的此文档。|

若要获得关于特定目标 Sharpie 工具帮助，请输入工具的名称和`-help`选项。 例如，`sharpie xcode -help`将返回以下输出：

<pre>$ <b>sharpie xcode -help</b>
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.</pre>

我们可以开始绑定过程之前，我们需要通过终端中输入以下命令获取有关我们当前已安装的 Sdk 信息`sharpie xcode -sdks`。 你的输出可能与不同，具体取决于你已安装 Xcode 的哪个版本。 在任何安装的 Sdk 目标 Sharpie 查找`Xcode*.app`下`/Applications`目录：

<pre>$ <b>sharpie xcode -sdks</b>
<span class="terminal-blue">sdk:</span> appletvos9.0    <span class="terminal-green">arch:</span> arm64
<span class="terminal-blue">sdk:</span> iphoneos9.1     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos9.0     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos8.4     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> macosx10.11     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> macosx10.10     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> watchos2.0      <span class="terminal-green">arch:</span> armv7</pre>

综上所述，我们可以看到我们具有`iphoneos9.1`我们计算机上安装 SDK，并且具有`arm64`体系结构支持。 我们将在本部分中的所有示例使用此值。 使用就地此信息，我们已准备好将 Objective C 库标头文件解析到初始`ApiDefinition.cs`和`StructsAndEnums.cs`绑定项目。

