---
title: 入门 macOS Mojave
description: 本文档介绍如何获取设置为生成 macOS 使用 Xamarin.Mac Mojave 应用。 它讨论如何下载 Xcode 10 并更新 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: ee5269bf314401328d0184631e817b37ce091479
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067275"
---
# <a name="getting-started-with-macos-mojave"></a>入门 macOS Mojave

![预览](~/media/shared/preview.png)

> [!WARNING]
> Xamarin 的 macOS Mojave 支持当前处于预览状态，这意味着它可能包含 bug、 功能完成的不是，并且可能会更改。
> 将其用于实验仅。

> [!NOTE]
> 详细信息，请阅读[发行说明](https://releases.xamarin.com/preview-release-xcode-10-beta/)为 Xamarin 预览版。

本文档介绍如何获取设置为生成 macOS 使用 Xamarin.Mac Mojave 应用。 它讨论如何下载 Xcode 10 并更新 Visual Studio for mac。

## <a name="download-and-install"></a>下载并安装

1. **安装最新的 Xcode 10 beta** – 注册 Apple 开发人员可以下载并安装最新版本的 Xcode 10 从[Apple 开发人员门户](https://developer.apple.com/download/)。

   > [!NOTE]
   > MacOS Mojave SDK 被随 Xcode 10。

2. **运行 Xcode 10** – 在更新并运行 Visual Studio for Mac; 之前运行 Xcode 10 安装 Xamarin 需要一些工具。

3. **更新 Visual Studio for Mac** – 按照中的说明[发行说明](https://releases.xamarin.com/preview-release-xcode-10-beta/)安装 Xamarin 预览。

4. _（可选）_ **在 Mac 上安装最新的 macOS Mojave beta** – 若要测试使用新引入 macOS Mojave Api，已注册的 Apple 开发人员可以 Xamarin.Mac 应用[下载](https://developer.apple.com/download/)并安装最新 macOS Mojave 开发人员 beta。

   > [!TIP]
   > 即使你的应用程序不使用任何新 macOS Mojave Api，请务必生成它 macOS Mojave SDK （随 Xcode 10 一起安装） 以及其进行测试以确保一切按预期方式。 如果应用程序不调用任何新的 Api，可以使用 macOS Mojave SDK 重新编译它并对其进行测试且不升级你的 Mac 的操作系统。

   > [!IMPORTANT]
   > 然后再升级到 macOS Mojave 以生成并测试 Xamarin.Mac 应用程序来调用新 macOS Mojave Api 你的 Mac:
   > - 读取[Apple 的发行说明](https://developer.apple.com/download/)操作系统更新。
   > - 读取[发行说明](https://releases.xamarin.com/preview-release-xcode-10-beta/)为 Xamarin 预览版。 请注意，此第一个预览版不包括新 macOS Mojave AppKit Api （例如深色模式） 的绑定。

## <a name="related-links"></a>相关链接

- [下载 Xcode 10](https://developer.apple.com/download/)
- Xamarin 预览[发行说明](https://releases.xamarin.com/preview-release-xcode-10-beta/)
