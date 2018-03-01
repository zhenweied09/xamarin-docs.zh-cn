---
title: "C 入门"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: ea8335348416dc00074d83e09b74521da7abcb66
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="getting-started-with-c"></a>C 入门


## <a name="requirements"></a>惠?

若要使用.NET 嵌入 C 将需要 Mac 或 Windows 的计算机运行：

* macOS 10.12 (Sierra) 或更高版本
* Xcode 8.3.2 或更高版本

* Windows 7、 8、 10 或更高版本
* Visual Studio 2013 或更高版本

* [Mono](http://www.mono-project.com/download/)


## <a name="installation"></a>安装

下一步是下载并在您的计算机上安装的.NET 嵌入的工具。

为 C 和 Java 生成器的二进制版本仍不可用，但即将推出。

作为替代方法可以生成从我们的 git 存储库，请参阅[参与](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)说明的文档。


## <a name="generation"></a>代

若要生成的 C 代码，调用传递要面向的 C 语言的右标志.NET 嵌入工具：

### <a name="windows"></a>Windows：

```csharp
$ build/lib/Debug/Embeddinator-4000.exe --gen=c --output=managed_c --platform=windows --compile managed.dll
```

请确保调用从 Visual Studio 命令外壳程序特定的 Visual Studio 版本你是针对。

### <a name="macos"></a>macOS

```csharp
$ mono build/lib/Debug/Embeddinator-4000.exe --gen=c --output=managed_c --platform=macos --compile managed.dll
```

### <a name="output-files"></a>输出文件

如果一切运行正常，你将看到与下面的输出：

```csharp
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

由于`--compile`标志已传递给该工具，.NET 嵌入应也已编译的输出文件到是共享库，你可以生成的文件旁边找到，`libmanaged.dylib`上 macOS，文件和`managed.dll`Windows 上。

若要使用共享的库，可以包括`managed.h`C 标头文件，它提供对应于各自的 C 声明托管库 Api 并使用前面所述的链接编译共享的库。

## <a name="further-reading"></a>其他阅读材料

* [Embeddinator 限制](~/tools/dotnet-embedding/limitations.md)
* [致力于开放源代码项目](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [错误代码和描述](~/tools/dotnet-embedding/errors.md)
