---
title: Xamarin 探查器故障排除
description: 本文档提供与 Xamarin 探查器相关的疑难解答信息。 它介绍与日志记录和诊断，IDE 中，以及其他主题相关的问题。
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: topgenorth
ms.author: toopge
ms.date: 10/27/2017
ms.openlocfilehash: 247a18cf7f645ea90d100cb3f4900f30ac7754cc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793845"
---
# <a name="xamarin-profiler-troubleshooting"></a>Xamarin 探查器故障排除

## <a name="logging-and-diagnostics"></a>日志记录和诊断

Xamarin 团队可以帮助跟踪问题，如果你向我们提供信息，包括：

- 问题、 崩溃时，或失败和工作流对它引起段视频。
- 日志输出 （见下文）。
- **.Mlpd**正在生成分析会话 （见下文）。

### <a name="getting-log-outputs"></a>获取日志输出

在 Mac 上日志会保存到`~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`。

在 Windows 上这些将保存到`%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log`请包括最新的日志，每当您提交问题。

我们正在将添加详细日志记录我们看一下，因此此输出应增长，并且随着时间的推移会更有用。

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>生成.mlpd 文件

**.Mlpd**文件是单声道运行时探查器的压缩的输出。 Xamarin 探查器 GUI 读取从数据 **.mlpd**并将其显示为用户。 **.mlpd** ，则可为 Xamarin 调试工具，因为它们可帮助我们诊断的问题探查器可能会遇到与你的数据的工程师文件。

**.Mlpd**为当前会话自动保存在你的 Mac 的`/tmp`目录，并可以确定按时间戳。 如果你启用日志记录，则第一个输出将为的路径 **.mlpd**文件。 **.Mlpd**文件通常保存在目录中启动 ~/var/文件夹...

**.Mlpd**为当前会话也可以通过选择保存**文件 > 另存为...** 从探查器的菜单：

**Visual Studio for Mac**:

![](troubleshooting-images/image17.png "Visual Studio 中适用于 Mac 的保存.mlpd 文件")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "在 Visual Studio 中保存.mlpd 文件")

务必请注意， **.mlpd**包含大量的信息，并且将大文件大小。

## <a name="troubleshooting"></a>疑难解答

下面列出了常见的问题、 解决方法，和提示和技巧使用探查器。

> [!NOTE]
> **请注意**： 你必须是一个 Visual Studio**企业**订阅服务器才可解锁此功能在 Windows 上的任一 Visual Studio Enterprise 或 Visual Studio for mac。

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>我无法看到 iOS 探查器选项，或灰显 [Visual Studio 和适用于 Mac 的 Visual Studio]

检查来解决此问题的以下设置：

- 确保您使用的调试配置
- 确保你正在使用 SGen 垃圾回收器。
- 确保该平台是[支持](~/tools/profiler/index.md#Profiler_Support)。
- 确保你具有正确的许可证。
- 确保你在正确和身份验证。
- [Visual Studio]你必须使用[Visual Studio Enterprise](https://www.visualstudio.com/vs/enterprise/)和具有有效的企业许可证。

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>在尝试启动探查器时遇到错误

如果你运行到此错误框中，在 Visual Studio 中使用探查器时：

![](troubleshooting-images/error.png "错误对话框时使用 Visual Studio 中的探查器")

它通常是由于无法启动到模拟器 / 仿真程序。 重试和正常运行应用程序，请修复它赋予，，然后尝试再次使用探查器的问题。

#### <a name="to-watch-a-specific-thread"></a>若要监视特定线程

如果你有你想要专门观看的线程，理想的做法是以名称在非常从其创建，以便获取线程`ThreadName`而不是`0x0`。 有关作为 UI 中设置线程名称的示例，你可以使用下面的代码：

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>相关链接

- [演练-使用 Xamarin 探查器](~/tools/profiler/index.md)
- [内存和性能的最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [发行说明](https://developer.xamarin.com/releases/profiler/preview/)
