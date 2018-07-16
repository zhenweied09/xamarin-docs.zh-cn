---
title: 开始使用 macOS Mojave
description: 本文档介绍如何获取设置，将生成 macOS Mojave Xamarin.Mac 应用。 它讨论了如何下载 Xcode 10 和更新 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/08/2018
ms.openlocfilehash: cf2725eafa18330a07a08db4235bad1a1ecd47b6
ms.sourcegitcommit: cfb72be633e335147d156af3ef9527151b9e31d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030613"
---
# <a name="getting-started-with-macos-mojave"></a>开始使用 macOS Mojave

![预览](~/media/shared/preview.png)

> [!WARNING]
> Xamarin 的 macOS Mojave 支持目前处于预览状态，这意味着它可能包含 bug、 不是功能完成的并且可能会更改。
> 将它用于仅试验。

> [!NOTE]
> 有关详细信息，请阅读 Xamarin 预览版[发布博客文章](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)。

本文档介绍如何获取设置，将生成 macOS Mojave Xamarin.Mac 应用。 它讨论了如何下载 Xcode 10 和更新 Visual Studio for mac。

## <a name="download-and-install"></a>下载并安装

1. **安装最新的 Xcode 10 beta** – 已注册的 Apple 开发人员可以下载并安装最新版本从 Xcode 10 [Apple 开发人员门户](https://developer.apple.com/download/)。

   > [!NOTE]
   > MacOS Mojave SDK 随 Xcode 10。

2. **运行 Xcode 10** – 更新并运行 Visual Studio for Mac; 之前运行 Xcode 10 安装 Xamarin 需要一些工具。

3. **更新 Visual Studio for Mac** – 按照上的说明[发布博客](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)安装 Xamarin 预览版。

4. _（可选）_ **在 Mac 上安装最新的 macOS Mojave beta** – 若要测试 Xamarin.Mac 应用程序使用新引入的 macOS Mojave Api，已注册的 Apple 开发人员可[下载](https://developer.apple.com/download/)并安装最新的 macOS Mojave 开发人员测试版。

   > [!TIP]
   > 即使您的应用程序不使用任何新 macOS Mojave Api，请务必使用 macOS Mojave SDK （安装 Xcode 10） 生成和测试，以确保一切按预期工作。 如果应用不会调用任何新的 Api，可以重新编译它 macos Mojave SDK 并测试它而不用升级你的 Mac 操作系统。

   > [!IMPORTANT]
   > 然后再向 macOS Mojave 用于生成和测试 Xamarin.Mac 应用程序来调用新 macOS Mojave Api 升级你的 Mac:
   > - 读取[Apple 的发行说明](https://developer.apple.com/download/)操作系统更新。
   > - 读取 Xamarin 预览版[发布博客文章](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)。

## <a name="related-links"></a>相关链接

- [下载 Xcode 10](https://developer.apple.com/download/)
- Xamarin 预览版[发布博客文章](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)
