---
title: 嵌入的框架
description: 本文档介绍如何，应用程序开发人员可以在其应用中嵌入用户框架。
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: f223d8ef6e89cc44822b8a831dbba3cf71d727c9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="embedded-frameworks"></a>嵌入的框架

_本文档介绍如何，应用程序开发人员可以在其应用中嵌入用户框架。_

使用 iOS 8.0 Apple 进行可以创建应用程序扩展和在 Xcode 中的主应用程序之间共享代码的嵌入式的框架。

Xamarin.iOS 9.0 在 Xamarin.iOS 应用程序中添加使用这些嵌入式的框架 （使用 Xcode 创建） 的支持。 *它将**不**能从任何类型的 Xamarin.iOS 项目创建嵌入的框架，只需使用现有的本机 (OBJECTIVE-C) 框架。*

有两种方法来使用在 Xamarin.iOS 的框架：

- 通过将以下内容添加到项目中的其他 mtouch 参数将框架传递给 mtouch 工具， **iOS 生成**选项：

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  这必须为每个项目配置设置。

- 从上下文菜单添加本机引用

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

右键单击项目和浏览以添加本机引用

![](embedded-frameworks-images/xam-native-refs.png "在 Visual Studio 中的添加本机引用选择适用于 Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

右键单击项目和浏览以添加本机引用

![](embedded-frameworks-images/vs-native-refs.png "在 Visual Studio 中选择添加本机引用")

-----

  这将适用于所有配置。

适用于 Mac 的 Visual Studio 和 Xamarin Tools for Visual Studio 的未来版本中将可以使用从 IDE 中的框架 （无需手动编辑项目文件）。

上找不到几个示例项目[github](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>限制

- 只有支持嵌入的框架[统一](~/cross-platform/macios/unified/index.md)项目。
- 嵌入的框架仅支持与部署目标的项目中至少 iOS 8.0。
- 如果扩展需要的嵌入式的框架，然后容器应用程序还必须对 framework，否则为框架将不包括在应用捆绑包的引用。

## <a name="the-mono-runtime"></a>单声道运行时

内部 Xamarin.iOS 充分利用此功能将其与单声道运行时作为框架，而不是将单声道运行时以静态方式链接到每个扩展和容器应用程序的链接。

这将自动完成的如果容器应用程序是一个统一应用，它包含扩展的目标部署为 iOS 8.0 或更高版本。

不带扩展的应用程序将仍与单声道运行时静态方式链接，因为没有为引用它的一个应用是否使用框架对大小产生负面影响。

通过将以下内容添加作为项目的 iOS 生成选项中的其他 mtouch 参数，应用程序开发人员，可以覆盖此行为：

- `--mono:static`： 使用单声道运行时静态链接。
- `--mono:framework`： 与单声道运行时作为框架的链接。

将与单声道运行时作为甚至适用于不带扩展的应用框架的链接的一个方案是减少可执行文件大小，从而克服 Apple 可执行文件强制执行任何大小限制。 对于引用，单声道运行时添加大约 1.7 MB，每个体系结构 （随着的 Xamarin.iOS 8.12，但是其变化版本，之间和应用之间甚至）。 Mono 框架增加约 2.3 MB 每个体系结构，这意味着不带任何扩展体系结构的单个应用程序，使应用链接与单声道运行时作为一个框架，将收缩 ~1.7MB，由可执行文件，但添加 ~2.3MB 框架，生成在 ~0.6MB 更大的应用程序一起。

