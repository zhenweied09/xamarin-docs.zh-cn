---
title: 安装.NET 嵌入
description: 本文档介绍如何安装.NET 嵌入。 它讨论如何手动，运行此工具如何生成绑定自动，如何使用自定义 MSBuild 目标和必要的生成后步骤。
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 4/18/2018
ms.openlocfilehash: 057a1f3f662b2dbe2f8aee277505e1d6e8798084
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793790"
---
# <a name="installing-net-embedding"></a>安装.NET 嵌入

## <a name="installing-net-embedding-from-nuget"></a>安装 NuGet 从嵌入.NET

选择**添加 > 添加 NuGet 包...** 并安装**Embeddinator 4000**从 NuGet 包管理器：

![NuGet 程序包管理器](images/visualstudionuget.png)

这将安装**Embeddinator 4000.exe**和**objcgen**到**包/Embeddinator-4000/工具**目录。

一般情况下，应下载选择 Embeddinator 4000 的最新版本。 Objective C 的支持，需要 0.4 或更高版本。

## <a name="running-manually"></a>手动运行

既然已安装 NuGet，你可以手动运行此工具。

- 打开一个终端 (macOS) 或命令提示符 (Windows)
- 将目录更改为你的解决方案根目录
- 此工具安装在中：
    - **。 / 包/Embeddinator-4000。[VERSION] / 工具/objcgen** (Objective C)
    - **。 / 包/Embeddinator-4000。[VERSION]/tools/Embeddinator-4000.exe** (Java/C) 
- 在 macOS 上, **objcgen**可以直接运行。 
- 在 Windows 上， **Embeddinator 4000.exe**可以直接运行。
- 在 macOS 上, **Embeddinator 4000.exe**需要使用运行**单声道**: 
    - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

每个命令调用将需要大量的特定于平台的文档中列出的参数。

## <a name="automatic-binding-generation"></a>自动绑定生成

有两种方法为自动运行一次.NET 嵌入的生成过程的一部分。

- 自定义 MSBuild 目标
- 生成后步骤

虽然本文档将介绍同时，使用自定义 MSBuild 目标是首选方法。 从命令行生成时，将不一定运行后生成步骤。

### <a name="custom-msbuild-targets"></a>自定义 MSBuild 目标

若要自定义你的生成包含 MSbuild 目标，请先创建**Embeddinator 4000.targets**旁边类似于以下你 csproj 文件：

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

在这里，应使用一个特定于平台的文档中列出的.NET 嵌入调用填充命令的文本。

若要使用此目标：

- 关闭你的项目在 Visual Studio 2017 或 Visual Studio for Mac
- 在文本编辑器中打开库 csproj
- 在底部，最终上面添加以下行`</Project>`行：

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- 重新打开你的项目

### <a name="post-build-steps"></a>生成后步骤

添加生成后步骤以运行.NET 嵌入的步骤会因 IDE 的不同而异：

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

在适用于 Mac 的 Visual Studio，请转到**项目选项 > 生成 > 自定义命令**并添加**后生成**步骤。

设置特定于平台的文档中列出的命令。

> [!NOTE]
> 请确保使用从 NuGet 安装的版本号

如果想要执行的操作正在进行开发 C# 项目，则还可能添加自定义的命令，以清理**输出**目录在运行.NET 嵌入之前：

```shell
rm -Rf '${SolutionDir}/output/'
```

![自定义生成操作](images/visualstudiocustombuild.png)

> [!NOTE]
> 所生成的绑定将放置在指示的目录`--outdir`或`--out`参数。 因为某些平台上的包名称限制，生成的绑定名称可能会发生变化。

#### <a name="visual-studio-2017"></a>Visual Studio 2017

我们将实质上是设置相同，但在 Visual Studio 2017 菜单会稍有不同。 Shell 命令也是略有不同的。

转到**项目选项 > 生成事件**并输入到特定于平台的文档中列出的命令**后期生成事件命令行**框。 例如：

![在 Windows 上嵌入的.NET](images/visualstudiowindows.png)
