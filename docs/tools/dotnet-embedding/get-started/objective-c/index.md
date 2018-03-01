---
title: "Objective C 入门"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 832ad2e6d462b39df7fa4a45739e97f7a7d6e5b5
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="getting-started-with-objective-c"></a>Objective C 入门

这是于 OBJECTIVE-C 的涵盖所有支持的平台的基础知识的入门页。


## <a name="requirements"></a>惠?

若要使用.NET 嵌入 Objective C 将需要运行 Mac:

* macOS 10.12 (Sierra) 或更高版本
* Xcode 8.3.2 或更高版本
* [Mono 5.0](http://www.mono-project.com/download/)

你可以安装[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)来编辑和编译 C# 代码。


注意：

* 早期版本的 macOS、 Xcode 和 Mono_可能_工作，而是未经测试，不受支持;
* 代码生成可以在 Windows 上，但它才可以在 Xcode 的安装位置; Mac 计算机上对其进行编译


## <a name="installation"></a>安装

下一步是下载并在 mac 上安装.NET 嵌入

* [包](https://dl.xamarin.com/embeddinator/Xamarin.Embeddinator-4000-0.2.0.79.pkg)
* [发行说明](https://github.com/mono/Embeddinator-4000/tree/master/docs/releases)

它是一种替代方法可以生成从我们[git 存储库](https://github.com/mono/Embeddinator-4000/tree/objc)，请参阅[参与](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)说明的文档。

安装程序是基于标准 pkg 的安装程序：

![安装程序简介](images/install1.png)
![安装程序安装类型](images/install2.png)
![安装摘要](images/install3.png)

安装后通过安装程序中，在你开始新的终端会话之后，你可以使用`objcgen`命令。
否则始终通过其绝对路径运行该工具： `/Library/Frameworks/Xamarin.Embeddinator-4000.framework/Commands/objcgen`。

## <a name="platforms"></a>平台

Objective C 是一种语言，最常用于编写应用程序 macOS、 iOS、 tvOS 和 watchOS;和 embeddinator 支持所有这些平台。 每个平台使用意味着的主要区别，并解释了这些[此处](~/tools/dotnet-embedding/objective-c/platforms.md)。

### <a name="macos"></a>macOS

[创建 macOS 应用程序](~/tools/dotnet-embedding/get-started/objective-c/macos.md)是最简单的因为它不涉及任意数量的其他步骤，好比设置标识、 provisining 配置文件、 模拟器和设备。 建议为与 macOS 文档先一个用于 iOS 的。

### <a name="ios--tvos"></a>iOS / tvOS

请确保你已设置为在尝试创建一个使用 embeddinator 之前开发 iOS 应用程序。 [按照说明进行操作](~/tools/dotnet-embedding/get-started/objective-c/ios.md)假定你已成功生成并部署 iOS 应用程序从你的计算机。

对 tvOS 的支持是类似于 iOS 的工作原理，只需在 Ide （Visual Studio 和 Xcode） 而不是 iOS 项目使用 tvOS 项目。

> [!NOTE]
> 注意： watchOS 支持将在未来版本中可用，并将非常类似于 iOS/tvOS。


## <a name="further-reading"></a>其他阅读材料

* [.NET 嵌入特定于 OBJECTIVE-C 的功能](~/tools/dotnet-embedding/objective-c/index.md)
* [Objective c 的最佳做法](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [致力于开放源代码项目](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [错误代码和描述](~/tools/dotnet-embedding/errors.md)
* [目标平台](~/tools/dotnet-embedding/objective-c/platforms.md)


## <a name="related-links"></a>相关链接

- [天气示例 （iOS 和 macOS）](https://github.com/jamesmontemagno/embeddinator-weather)
