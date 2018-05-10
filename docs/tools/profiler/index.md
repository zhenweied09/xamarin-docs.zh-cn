---
title: Xamarin Profiler
description: 本指南介绍了 Xamarin 探查器的主要功能。 它查找在探查器、 分析和时应使用它们，和的标准工作流分析 Xamarin 应用程序。
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: topgenorth
ms.author: toopge
ms.date: 10/27/2017
ms.openlocfilehash: 7e28e2513b74faa884fb7bc3d7194f1b02844734
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="xamarin-profiler"></a>Xamarin Profiler

_本指南介绍了 Xamarin 探查器的主要功能。它查找在探查器、 分析和时应使用它们，和的标准工作流分析 Xamarin 应用程序。_

应用程序的成功取决于最终用户体验。 作为开发人员可能已实现某些真正出色的功能在你的应用，但如果应用程序是缓慢或完整的故障，用户将可能将其删除。

从历史上看，Mono 一番功能强大的命令行探查器收集有关在单声道运行时中运行的程序调用的[单声道日志探查器](http://www.mono-project.com/docs/debug+profile/profile/profiler/)。 Xamarin 探查器单声道日志探查器和分析 Android、 iOS、 tvOS，和上 Mac 和 Android、 iOS、 Mac 应用程序和 Windows 上的 tvOS 应用程序的支持的图形界面。

Xamarin 探查器具有大量检测可用于分析-分配、 周期和时间探查器。 本指南介绍这些票据测量和它们如何分析你的应用程序，并阐明了每个屏幕上呈现的数据的含义。

本指南检查常见分析方案，并作为一种工具来帮助分析并优化 iOS 和 Android 应用程序引入探查器。

## <a name="contents"></a>内容

- [下载并安装](#Download_and_Install)
- [探查器和分析](#Profilers_and_Profiling)
- [Xamarin Profiler](#Xamarin_Profiler)
- [探查器支持](#Profiler_Support)
- [探查器基础知识](#Profiler_Basics)
    - [允许在你的应用程序中进行分析](#Allowing_Profiling_in_your_App)
    - [启动探查器](#Launching_the_Profiler)
        - [从 Visual Studio 启动 mac](#Launching_from_Xamarin_Studio)
        - [从 Visual Studio 启动](#Launching_from_Visual_Studio)
        - [保存和加载探查器会话](#Saving_and_Loading_Profiler_Sessions)
        - [探查器功能和检测](#Profiler_Features)
    - [分配](#Allocations)
    - [时间探查器](#Time_Profiler)
    - [周期](#Cycles)
- [分析应用程序](#Profiling_Applications)
- [摘要](#Summary)

## <a name="download-and-install"></a>下载并安装

> [!NOTE]
> **注意：**你将需要[Visual Studio Enterprise](https://www.visualstudio.com/vs/compare/)订阅服务器才可解锁此功能在 Windows 上的任一 Visual Studio Enterprise 或 Visual Studio for mac 上的 Mac

Xamarin 探查器是独立的应用程序，并且已集成使用适用于 Mac 的 Visual Studio 和 Visual Studio，若要启用分析从 IDE 内。

### <a name="download"></a>下载

下载你的平台的安装程序包：

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

下载完成后，启动安装程序将 Xamarin 探查器添加到你的系统。


## <a name="profilers-and-profiling"></a>探查器和分析

分析是一个重要且经常会被忽略的步骤，应用程序开发中。 分析是一种形式**动态程序分析**-正在运行且正在使用时，它会分析该程序。 探查器是一种数据挖掘工具，收集有关时间复杂性、 特定方法的使用情况和分配的内存的信息。 探查器使您可以深深入并分析这些度量值，以找出代码中的问题区域。

当设计和开发的应用程序，务必不优化提前;也就是说，花费时间开发将很少访问的区域中的代码。 这是电源的分析。 探查器提供深入了解最常用的基代码部分并可帮助找到请花一些时间进行改进的区域。 开发人员应注意若要了解在你的应用程序，其中花费大部分时间以及你的应用程序如何使用内存。

分析很有帮助中所有类型的开发，但它是极其重要移动开发中。 未优化的代码是更明显比台式计算机上的移动平台上，你的应用的成功取决于美观和优化代码，它可有效地运行。

## <a name="xamarin-profiler"></a>Xamarin Profiler

Xamarin 探查器向开发人员提供了一种方法从配置文件应用程序在 Visual Studio for Mac 或 Visual Studio。 探查器收集并显示有关应用程序，随后可由开发人员来分析应用程序的行为的信息。 有多种不同的方式分析使用 Xamarin 探查器时，应用程序即内存分析和统计采样。 这些不会执行的分配和时间探查器通过分别检测。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

当前，可以使用 Xamarin 探查器在 Mac 上 (通过 Visual Studio for Mac) 的 Xamarin.iOS 和 Xamarin.Android，Xamarin.Mac 应用程序进行测试。 探查器是从 IDE，单独的进程，因此，除了从 Visual Studio 启动 Mac 的它可以用作独立的应用程序检查.exe 和`.mlpd`从生成的文件[单声道日志探查器](http://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

目前，Xamarin 探查器可以用于测试 （通过 Visual Studio 和 Visual Studio for Mac） 的 Windows 上的 Xamarin.Android 应用程序。 探查器是从 IDE，单独的进程，因此，除了从 Visual Studio 启动，它可以用作独立的应用程序检查.exe 和`.mlpd`从生成的文件[单声道日志探查器](http://www.mono-project.com/docs/debug+profile/profile/profiler/).

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>探查器支持

对 Xamarin 探查器的支持是在以下平台上可用：

 - 适用于 Mac (macOS，企业许可证) 的 visual Studio
    - Android
        - 设备和仿真程序
    - iOS
        - 设备和模拟器
    - tvOS （不支持时间检测）
        - 设备和模拟器
    - Mac

 - Visual Studio (仅**企业**版本)
    - Android
        - 设备和仿真程序
    - iOS [实验]
        - 设备和模拟器
    - tvOS
        - 设备和模拟器

请注意，你可以**仅**配置文件**调试**配置。

## <a name="profiler-basics"></a>探查器基础知识

本部分介绍的 Xamarin 探查器部分，并 tours 其功能。

### <a name="allow-profiling-in-your-app"></a>允许在你的应用程序中进行分析

应用程序可以成功进行分析之前，你需要允许在应用程序的项目选项中的事件探查。

 - iOS:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

  **生成 > iOS 调试 > 启用分析**

  ![](images/ios-options-mac.png "iOS 适用于 Mac 的 Visual Studio 中的选项对话框")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  **属性 > iOS 生成 > 启用分析**

  ![](images/ios-project-options-vs.png "iOS Visual Studio 中的选项对话框")

-----

 - Android:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

  **生成 > Android 调试 > 启用开发人员检测**

  ![Visual Studio 中适用于 Mac 的 android 选项对话框](images/android-project-options.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  **生成 > Android 调试 > 启用开发人员检测**

  ![Visual Studio 中适用于 Mac 的 android 选项对话框](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>启动探查器

从 IDE iOS 或 Android 应用程序进行分析时或作为独立应用程序，则可以启动 Xamarin 探查器。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

#### <a name="launching-from-visual-studio-for-mac"></a>从 Visual Studio 启动 mac

1. 首先，请确保你具有你的 Mac 上，在 Visual Studio 中加载的应用程序并选择 （默认值） 调试配置。
2. 浏览到**运行 > 启动分析**适用于 Mac，Visual Studio 中或**分析 > Xamarin 探查器**在 Visual Studio 中，若要打开探查器，如下面的关系图中所示：

  ![](images/start-profiling-xs.png "为 Mac 启动从 Visual Studio 探查器")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

#### <a name="launching-from-visual-studio"></a>从 Visual Studio 启动

1.  首先，请确保您有你在 Visual Studio 中，加载的应用程序，选择 （默认值） 调试配置中，如同上面所指定。
2.  浏览到**分析 > Xamarin 探查器**在 Visual Studio 中，若要打开探查器，如下面的关系图中所示：

![启动从 Visual Studio 探查器](images/start-profiling-vs.png)

-----

如果未出现的菜单项，请参阅[故障排除指南](~/tools/profiler/troubleshooting.md)。

这将启动探查器，并分析应用程序将自动启动。

可以使用探查器来测量内存和性能。 它来实现此目标通过分配和时间探查器检测，我们将在下一节中详细探讨。

#### <a name="saving-and-loading-profiler-sessions"></a>保存和加载探查器会话

若要保存在任何时间分析会话，请选择**文件 > 另存为...** 从探查器菜单栏中。 这将保存在文件_mlpd_格式、 分析数据的特殊的高度压缩的格式。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在安装了之后 Xamarin 探查器可以找到在你的应用程序文件夹中，如下面的屏幕截图中所示：

![](images/applications.png "从 Mac 打开独立探查器")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在安装了之后可以在你的应用程序目录中找到的 Xamarin 探查器应用程序：

![](images/applications-vs.png "从 Windows 打开独立探查器 ")

-----

你可以加载 *.mlpd*到，打开独立应用程序，探查器的文件选择**选择目标**和加载该文件。

有关详细信息，请参阅[生成.mlpd 文件](~/tools/profiler/troubleshooting.md#gen_mlpd)。


## <a name="profiler-features"></a>探查器功能

Xamarin 探查器的定义如下所示的五个部分组成：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](images/profiler-mac-sml.png "适用于 Mac 的 Visual Studio 中的探查器部分")](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/profiler-vs.png "Visual Studio 中的探查器部分")](images/profiler-vs.png#lightbox)

-----

- **工具栏**– 位于顶部的探查器，这提供选项，若要启动/停止分析，选择目标进程，查看应用程序中的运行时间和选择撰写探查器应用程序的拆分视图。
- **对列表进行检测**– 这将列出已加载的分析会话的所有设备。
- **绘制图**– 这些图表水平与检测列表中的相关票据。 滑块 （时间探查器下方所示） 可用来更改其小数位数。
- **检测详细信息区域**-包含要显示由当前检测所选视图数据。 我们将查看这些视图在下面部分中的更多详细信息。
- **检查器视图**– 这包含分段控件可以选择的部分。 部分依赖于选择，检测，包括： 配置设置、 统计信息，堆栈跟踪信息、 和到根的路径。

### <a name="allocations"></a>分配

分配检测提供有关应用程序中的对象的详细的信息，因为它们正在创建并且垃圾回收。

在探查器的顶部是内存的分配图，后者将显示在分析过程中定期分配量。 当前分配关系图是总数的分配和堆的大小不在该点的时间。 在某种意义上，它将永远不会停机，它将永远只有增加。 这包括在堆栈上分配的对象。 具体取决于使用的运行时版本，图表可以查看不同 – 即使对于相同的应用。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](images/allocations1.png "分配检测")](images/allocations1.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/allocations1-vs.png "分配检测")](images/allocations1-vs.png#lightbox)

-----

有不同的数据视图中分配检测，允许开发人员能够分析其应用程序如何使用和释放内存。 这些视图如下所述：

 -   **分配**– 这显示所有分配的列表，并按类名对其进行分组。 这提供类和正在使用的方法、 使用的频率和使用的类的集合大小的极佳的概述。 双击类将显示分配的内存： 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

  [![](images/allocations3.png "分配选项卡")](images/allocations3.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations2-vs.png "分配选项卡")](images/allocations2-vs.png#lightbox)

-----

分配的检查器视图堆栈跟踪和路径提供筛选和分组对象、 内存分配，提供统计信息和选项顶部分配，以及视图到根。

 -   **调用关系树**– 此应用程序中显示所有线程的整个调用树，并包含有关每个节点上分配的内存信息。 当在列表中选择某个元素时，将出现所有同级节点灰色。 你可以展开树，或双击要向下钻取到其中的元素。在显示此数据视图时，显示设置检查器视图可以用于更改它显示的方式。 目前，有两个选项：
    1.  **反转调用关系树**– 这会将从上到下的堆栈跟踪。 这是一个方便视图选项因为它表明最深方法其中 CPU 具有已花费的时间。
    2.  **单独线程**– 此选项将按线程组织调用关系树。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

  [![](images/allocations2.png "调用树选项卡")](images/allocations2.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations3-vs.png "调用树选项卡")](images/allocations3-vs.png#lightbox)

-----

 -   **快照**– 此窗格显示有关内存快照的信息。 若要生成这些分析实时应用程序时，请单击_相机_中你想要查看哪些内存是保留和释放每个点上的工具栏按钮。 然后，你可以单击每个快照，以浏览实质上发生了什么情况。 请注意，快照仅时可以执行实时分析应用程序。 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

  [![](images/allocations4.png "快照选项卡")](images/allocations4.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations4-vs.png "快照选项卡")](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>时间探查器

时间探查器检测测量应用程序的每种方法中完全花费多少时间。 应用程序将暂停按固定的间隔，并在每个活动线程上运行的堆栈跟踪。 检测详细信息区域中的每一行显示已执行的执行路径。

须图中，如下面的屏幕截图中所示显示运行该应用程序收到的样本数：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![时间探查器检测](images/time1.png)](images/time1.png#lightbox) 

[![时间探查器检测 – 示例列表](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![时间探查器检测](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![时间探查器检测 – 示例列表](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----


- **调用关系树**– 显示所用的时间每种方法中：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

  [![](images/time2.png "时间探查器检测 – 调用关系树")](images/time2.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/time2-vs.png "时间探查器检测 – 调用关系树")](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>循环

通过使用 C# 和 F # 托管代码，它可以是很常见，和遗憾的是很容易创建对将永远无法释放的对象的引用。 此 intrument 可以找出这些对象，并显示在你的应用程序中引用的周期。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![周期检测](images/cycles-vs.png)](images/time1-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>分析应用程序

目前，默认的调试配置可以进行分析。

如果你配置文件包含的任何其他配置的应用，你将看到与以下的消息对话框：


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![分析错误对话框](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/image1vs.png "分析错误对话框")](images/image1vs.png#lightbox) 

-----


选择**更新**以继续。

<!---
## Profiling Android Applications


Due to the recent inclusion of the profiling libraries into any new Android project template, you will find that when profiling any legacy applications you are greeted with the message dialog above.

You will need to enable this to make sure that the profiling libraries are included in your Android application, for debug builds. This should not be checked for release builds as it creates overhead.


## Profiling iOS Applications

### Profiling tvOS

## Profiling Mac Applications
-->

### <a name="sgen-garbage-collector-and-profiling"></a>SGen 垃圾回收器和分析

[SGen](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/)垃圾回收器用于所有 Xamarin 平台。

SGen 它分配的应用程序分成三个堆的对象是代 GC-Nursery、 主要堆和大型对象空间。 这使得加速执行垃圾回收。 SGen 目前 Xamarin.Android，默认 GC 和统一的 Xamarin.iOS 应用程序。

使用经典 API 的 Xamarin.iOS 应用程序使用 Boehm GC – 保守、 非代的垃圾回收器。 按原样保守，就很难释放可用内存，因此就使用探查器时，可能导致不准确的结果。 因此，分配检测不能用于 Boehm 垃圾回收器。

时你将提示一个消息对话框，如果你的应用程序使用 Boehm GC，Xamarin 建议不要切换到 SGen Boehm 使用未经仔细研究和全面测试的现有 iOS 应用程序。 Xamarin 还不建议切换到 SGen 来配置文件和切换回，因为这些结果将不会提供的内存使用量的准确基准。

有关内存管理的详细信息，请参阅[内存和性能最佳实践](~/cross-platform/deploy-test/memory-perf-best-practices.md)指南。

## <a name="summary"></a>总结

在本指南中我们看上去哪些分析及如何都很有好处向开发人员。 然后，我们引入了 Xamarin 探查器，提供的某些历史记录和到其工作原理的信息。 最后，我们在观看的 Xamarin 探查器，功能和解决分配以及时间探查器检测。


## <a name="related-links"></a>相关链接

- [内存和性能的最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [发行说明](https://developer.xamarin.com/releases/profiler/preview/)
