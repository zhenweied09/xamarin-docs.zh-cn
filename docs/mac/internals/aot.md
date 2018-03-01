---
title: "在实时编译之前 Xamarin.Mac"
description: "继续操作的时间 (AOT) 编译的折衷方案和注意事项"
ms.topic: article
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: f9ace41380987472b6957c94719e6ae9b6f7995d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>在实时编译之前 Xamarin.Mac

## <a name="overview"></a>概述

继续操作 (AOT) 编译是时间的一种用于提高启动性能的功能强大的优化技术。 但是，它还会影响你的生成时间、 应用程序大小和程序执行深远的方式。 若要了解会施加的权衡，我们将稍微深入的编译和执行的应用程序。

在托管语言，如 C# 和 F # 中编写代码编译为调用 IL 的中间表示。 此 IL，存储在你库和程序的程序集，是相对紧凑且可移植处理器体系结构之间。 IL，但是，是仅设置的说明和在某一时刻 IL 需要转换为特定于处理器的机器代码的中间。

有两个点可以在其中执行此处理：

- **实时 (JIT)** – 在启动和 IL 编译为机器代码的内存中的应用程序执行期间。
- **预的时间 (AOT)** – 在生成 IL 编译和写出到本机库和应用程序捆绑包中存储过程。

每个选项具有大量的优点和缺点：

- **JIT**
  - **启动时间**– 在启动时必须进行 JIT 编译。 对于大多数应用程序这是时间大约为 100 毫秒，但对于大型应用程序这一次可以是多得多。
  - **执行**– 对于正在使用的特定处理器可以优化为 JIT 代码，可以生成略有更好的代码。 在大多数应用程序为更快的几个百分比点最多。
- **AOT**
  - **启动时间**– 加载预编译的 dylibs 的速度大大快于 JIT 程序集。
  - **磁盘空间**– 这些 dylibs 可以但是需要大量的磁盘空间。 具体取决于哪些程序集是 AOTed，它可以双击或更多应用程序的代码部分的大小。
  - **生成时间**– AOT 编译的速度都要慢得多该 JIT 和会减慢生成使用它。 这种减速可以介于秒最多一分钟或更多，具体取决于的大小和编译的程序集的数量。
  - **模糊处理**– 作为 IL，是要实施反向工程比机器代码简单得多，不一定需要可以去除有助于对敏感的代码进行模糊处理。 这要求"混合"选项下面所述。

## <a name="enabling-aot"></a>启用 AOT

AOT 选项将添加到未来的更新中的 Mac 生成窗格中。 到那时，启用 AOT 需要命令行自变量传递通过在 Mac Build 中的"其他 mmp 自变量"字段。 提供了以下选项：


      --aot[=VALUE]          Specify assemblies that should be AOT compiled
                               - none - No AOT (default)
                               - all - Every assembly in MonoBundle
                               - core - Xamarin.Mac, System, mscorlib
                               - sdk - Xamarin.Mac.dll and BCL assemblies
                               - |hybrid after option enables hybrid AOT which
                               allows IL stripping but is slower (only valid
                               for 'all')
                                - Individual files can be included for AOT via +
                               FileName.dll and excluded via -FileName.dll

                               Examples:
                                 --aot:all,-MyAssembly.dll
                                 --aot:core,+MyOtherAssembly.dll,-mscorlib.dll



## <a name="hybrid-aot"></a>混合 AOT

在 macOS 应用程序运行时的执行期间默认情况下的使用机器代码从 AOT 编译生成的本机库加载。 有，但是，某些区域的代码 trampolines，例如其中 JIT 编译可以产生显著更优化的结果。 这要求托管程序集 IL 可用。 在 iOS 上应用程序限制从任何使用的 JIT 编译;这些部分中的代码有 AOT 也编译。

混合选项中但也可以认为 IL 将不可用在运行时，指示编译器对这两个编译这些部分 （如 iOS)。 可以然后剥离此 IL 发布生成。 如上所述，将强制运行时在某些位置使用不太优化的例程。

## <a name="further-considerations"></a>更多注意事项

导致的负数结果 AOT 缩放性的大小和处理的程序集数量。 完整[目标框架](~/mac/platform/target-framework.md)示例包含明显更大基类库 (BCL) 比现代，并因此 AOT 将花费很长并生成更大的捆绑包。 这，再加上由与链接的完整目标框架的不兼容，这将消除未使用的代码。 请考虑将移到要获得最佳结果的现代并启用链接你的应用程序。

一个额外的 AOT 优势附带与本机调试和分析 toolchains 改进交互。 由于将提前编译绝大多数基本代码，它将具有函数名称和更易于阅读内部本机崩溃报告，分析和调试的符号。 生成的 JIT 函数不具有这些名称，并通常显示为未命名非常难以解决的十六进制偏移量。
