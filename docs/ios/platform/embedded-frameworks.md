---
title: 在 Xamarin.iOS 中的嵌入式的框架
description: 本文档介绍如何使用 Xamarin.iOS 应用程序中的嵌入式框架共享代码。 这可以使用 mtouch 工具或本机引用。
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/05/2018
ms.openlocfilehash: cce5356fd1d3d9a5cf16370a4843c3541b00a7c0
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351429"
---
# <a name="embedded-frameworks-in-xamarinios"></a>在 Xamarin.iOS 中的嵌入式的框架

_本文档介绍了如何应用程序开发人员可以在其应用中嵌入用户框架。_

与 iOS 8.0 Apple 成为可能创建以应用扩展和 Xcode 中的主应用程序之间共享代码的嵌入式的框架。

Xamarin.iOS 9.0 Xamarin.iOS 应用中添加使用嵌入式的框架 （使用 Xcode 创建） 的支持。 *它将**不**能够从任何类型的 Xamarin.iOS 项目中创建嵌入的框架，只需使用现有的本机 (Objective C) 框架。*

有两种方法来使用 Xamarin.iOS 中的框架：

- 通过将以下内容添加到项目中的其他 mtouch 参数传递到 mtouch 工具的框架**iOS 生成**选项：

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  这必须为每个项目配置设置。

- 从上下文菜单添加本机引用

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

右键单击项目，然后浏览以添加本机引用

![](embedded-frameworks-images/xam-native-refs.png "为 Mac 在 Visual Studio 中选择添加本机引用")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

右键单击项目，然后浏览以添加本机引用

![](embedded-frameworks-images/vs-native-refs.png "在 Visual Studio 中选择添加本机引用")

-----

  这适用于所有配置。

在 Visual Studio for Mac 和适用于 Visual Studio 的 Xamarin 工具的未来版本将可以使用框架从 IDE 内的 （而无需手动编辑项目文件）。

可以上找到几个示例项目[github](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>限制

- 仅支持嵌入式的框架[统一](~/cross-platform/macios/unified/index.md)项目。
- 嵌入式的框架仅支持在与部署目标的项目中至少 iOS 8.0。
- 如果扩展需要一个嵌入的框架，则容器应用程序还必须具有对 framework，否则为该框架将不包括在应用程序捆绑包的引用。

## <a name="the-mono-runtime"></a>Mono 运行时

在内部 Xamarin.iOS 会利用此功能将其与 Mono 运行时作为一种框架，而不是将 Mono 运行时以静态方式链接到每个扩展插件和容器应用的链接。

如果容器应用程序是一个统一应用，它包含扩展插件和目标部署为 iOS 8.0 或更高版本，这会自动完成。

不带扩展的应用程序将仍然与 Mono 运行时静态链接，因为没有使用一种框架，如果只有一个应用程序对其进行引用的对大小产生负面影响。

可以通过将以下内容添加为项目的 iOS 生成选项中的其他 mtouch 参数，由应用开发中，重写此行为：

- `--mono:static`： 与 Mono 运行时以静态方式链接。
- `--mono:framework`： 与 Mono 运行时作为一种框架的链接。

即使对于不带扩展的应用程序的一个框架与 Mono 运行时链接的一个方案是减少可执行文件大小来克服 Apple 对可执行文件强制实施任何大小限制。 有关参考，Mono 运行时增加约 1.7 MB 每个体系结构 （如的 Xamarin.iOS 8.12，但是他变化之间的版本中，以及甚至应用程序之间）。 Mono 框架增加约 2.3 MB 每个体系结构，这意味着对于单体系结构应用而无需任何扩展，使应用程序链接与 Mono 运行时框架将收缩 ~1.7MB，由可执行文件，但添加 ~2.3MB 框架，从而在 ~0.6MB 更大应用程序一起。

