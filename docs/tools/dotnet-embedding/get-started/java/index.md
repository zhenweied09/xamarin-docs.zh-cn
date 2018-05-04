---
title: Java 入门
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/28/2018
ms.openlocfilehash: e61e610de9186978e2924c0e69e7517a39a54f04
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="getting-started-with-java"></a>Java 入门

这是适用于 Java，涵盖所有支持的平台的基础知识的入门页。

## <a name="requirements"></a>要求

若要使用.NET 嵌入的 Java 中，你将需要：

* Java 1.8 或更高版本
* [Mono 5.0](http://www.mono-project.com/download/)

适用于 Mac:

* Xcode 8.3.2 或更高版本

对于 Windows:

* 使用 c + + 支持 visual Studio 2017
* Windows 10 SDK

对于 Android:

* [Xamarin.Android 7.5](https://www.visualstudio.com/xamarin/)或更高版本
* [Android Studio 3.x](https://developer.android.com/studio/index.html) with Java 1.8

你可以使用[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)来编辑和编译 C# 代码。

> [!NOTE]
> 早期版本的 Xcode、 Visual Studio、 Xamarin.Android、 Android Studio 中，和 Mono_可能_工作，而是未经测试，不受支持。

## <a name="installation"></a>安装

.NET 嵌入是一种当前位于[NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

这会将**Embeddinator 4000.exe**到**包/Embeddinator-4000/工具**目录。

此外，你可以生成源从嵌入.NET，请参阅我们[git 存储库](https://github.com/mono/Embeddinator-4000/)和[参与](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)说明的文档。

## <a name="platforms"></a>平台

Java 目前处于预览状态 macOS、 Windows 和 Android。

通过选择平台`--platform=<platform>`.NET 嵌入工具命令行自变量。 当前`macOS`， `Windows`，和`Android`支持。

### <a name="macos-and-windows"></a>macOS 和 Windows

对于开发，你应能够使用支持 Java 1.8 任何 Java IDE。 您甚至可以使用 Android Studio 此如果需要，[这里](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects)。 也可以是任何标准的 Java jar 文件，你可以使用输出的 JAR 文件。

### <a name="android"></a>Android

请确保你已设置为之前尝试创建一个开发 Android 应用程序使用.NET 嵌入。 [按照说明进行操作](~/tools/dotnet-embedding/get-started/java/android.md)假定你已成功生成并部署 Android 应用程序从你的计算机。

Android Studio 建议开发，但其他 Ide 搭配使用，只要没有针对支持[AAR 文件格式](https://developer.android.com/studio/projects/android-library.html)。

## <a name="further-reading"></a>其他阅读材料

* [在 Android 上入门](~/tools/dotnet-embedding/get-started/java/android.md)
* [在 Android 上的回调](~/tools/dotnet-embedding/android/callbacks.md)
* [初步 Android 研究](~/tools/dotnet-embedding/android/index.md)
* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [致力于开放源代码项目](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [错误代码和描述](~/tools/dotnet-embedding/errors.md)
