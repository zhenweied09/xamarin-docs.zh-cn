---
title: C 入门
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 04/19/2018
ms.openlocfilehash: 1313d7156a1fd75fd40e2aff65404aef5ab023bb
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="getting-started-with-c"></a>C 入门

## <a name="requirements"></a>要求

若要使用.NET 嵌入 C，你将需要 Mac 或 Windows 的计算机运行：

### <a name="macos"></a>macOS

* macOS 10.12 (Sierra) 或更高版本
* Xcode 8.3.2 或更高版本
* [Mono](http://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7、 8、 10 或更高版本
* Visual Studio 2015 或更高版本

## <a name="installing-net-embedding-from-nuget"></a>安装 NuGet 从嵌入.NET

请按照以下[说明](~/tools/dotnet-embedding/get-started/install/install.md)若要为安装和配置.NET 嵌入你的项目。

你应配置的命令调用将如下所示 （可能使用不同的版本号和路径）：

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

```shell
mono {SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe --gen=c --output=managed_c --platform=macos --compile managed.dll
```

### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
$(SolutionDir)\packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe --gen=c --output=managed_c --platform=windows --compile managed.dll
```

## <a name="generation"></a>代

### <a name="output-files"></a>输出文件

如果一切运行正常，你将看到与下面的输出：

```shell
Parsing assemblies...
    Parsed 'managed.dll'
Processing assemblies...
Generating binding code...
    Generated: managed.h
    Generated: managed.c
    Generated: mscorlib.h
    Generated: mscorlib.c
    Generated: embeddinator.h
    Generated: glib.c
    Generated: glib.h
    Generated: mono-support.h
    Generated: mono_embeddinator.c
    Generated: mono_embeddinator.h
```

由于`--compile`标志已传递给该工具，.NET 嵌入应也已编译的输出文件到是共享库，你可以生成的文件旁边找到， **libmanaged.dylib**上 macOS 和文件**managed.dll** Windows 上。

若要使用共享的库，您可以包括**managed.h** C 标头文件，它提供对应于各自的 C 声明托管库 Api 并使用前面所述的链接编译共享的库。

## <a name="further-reading"></a>其他阅读材料

* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [致力于开放源代码项目](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [错误代码和描述](~/tools/dotnet-embedding/errors.md)
