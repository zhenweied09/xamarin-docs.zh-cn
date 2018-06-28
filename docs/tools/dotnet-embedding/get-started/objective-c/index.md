---
title: Objective C 入门
description: 本文档介绍如何开始使用.NET 嵌入目标 c。 它讨论了要求、 安装从 NuGet 和支持的平台的.NET 嵌入。
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 02d79103825d150b6e6f5bec7ed3ee1788078312
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066622"
---
# <a name="getting-started-with-objective-c"></a>Objective C 入门

这是于 OBJECTIVE-C 的涵盖所有支持的平台的基础知识的入门页。

## <a name="requirements"></a>要求

若要使用.NET 嵌入 Objective C，你需要运行一个 Mac:

* macOS 10.12 (Sierra) 或更高版本
* Xcode 8.3.2 或更高版本
* [Mono 5.0](http://www.mono-project.com/download/)

你可以安装[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)来编辑和编译 C# 代码。

> [!NOTE]
> * 早期版本的 macOS、 Xcode 和 Mono_可能_工作，而是未经测试，不受支持
> * 代码生成可以在 Windows 上，但它才可以在其中安装 Xcode 的 Mac 计算机上对其进行编译

## <a name="installing-net-embedding-from-nuget"></a>安装 NuGet 从嵌入.NET

请按照以下[说明](~/tools/dotnet-embedding/get-started/install/install.md)若要为安装和配置.NET 嵌入你的项目。

中列出的示例命令调用[macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md)和[iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md)入门指南。

## <a name="platforms"></a>平台

Objective C 是一种语言，最常用于编写应用程序 macOS、 iOS、 tvOS 和 watchOS;.NET 嵌入支持所有这些平台。 每个平台使用暗示着[的主要差异并将这些此处所述](~/tools/dotnet-embedding/objective-c/platforms.md)。

### <a name="macos"></a>macOS

[创建 macOS 应用程序](~/tools/dotnet-embedding/get-started/objective-c/macos.md)是最简单的因为它不涉及任意数量的其他步骤，好比设置标识、 provisining 配置文件、 模拟器和设备。 建议为与 macOS 文档先一个用于 iOS 的。

### <a name="ios--tvos"></a>iOS / tvOS

请确保你已设置为之前尝试创建一个开发 iOS 应用程序使用.NET 嵌入。 [按照说明进行操作](~/tools/dotnet-embedding/get-started/objective-c/ios.md)假定你已成功生成并部署 iOS 应用程序从你的计算机。

对 tvOS 的支持是类似于 iOS 的工作原理，只需在 Ide （Visual Studio 和 Xcode） 而不是 iOS 项目使用 tvOS 项目。

> [!NOTE]
> 对 watchOS 将在未来版本中可用，并将非常类似于 iOS/tvOS 的支持。

## <a name="further-reading"></a>其他阅读材料

* [.NET 嵌入特定于 OBJECTIVE-C 的功能](~/tools/dotnet-embedding/objective-c/index.md)
* [Objective c 的最佳做法](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [致力于开放源代码项目](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [错误代码和描述](~/tools/dotnet-embedding/errors.md)
* [目标平台](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>相关链接

- [天气示例 （iOS 和 macOS）](https://github.com/jamesmontemagno/embeddinator-weather)
