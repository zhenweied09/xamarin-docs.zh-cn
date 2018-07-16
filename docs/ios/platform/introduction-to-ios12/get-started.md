---
title: 开始使用 iOS 12、 12、 tvOS 和 watchOS 5
description: 本文档介绍如何获取设置为生成 iOS 12 和 tvOS 12 应用使用 Xamarin。 它讨论了如何下载 Xcode 10 和适用于 Mac 和 Visual Studio 2017 更新 Visual Studio。
ms.prod: xamarin
ms.assetid: 6C0F0133-1A5F-408B-8BCA-BDCA313A55C2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 70f67f934c2503e6f6fa0d3bae1f37bcc1f6f0a4
ms.sourcegitcommit: cfb72be633e335147d156af3ef9527151b9e31d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030647"
---
# <a name="getting-started-with-ios-12-tvos-12-and-watchos-5"></a>开始使用 iOS 12、 12、 tvOS 和 watchOS 5

![预览](~/media/shared/preview.png)

> [!WARNING]
> Xamarin 的 iOS 12，tvOS 12，和 watchOS 5 支持目前处于预览状态，它可能包含 bug，这意味着不是功能完备，并可能会更改。 将它用于仅试验。

> [!NOTE]
> 有关详细信息，请阅读 Xamarin 预览版[发布博客文章](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)。

本文档介绍如何获取设置为生成 iOS 12、 12、 tvOS 和 watchOS 5 应用使用 Xamarin。 它讨论了如何下载 Xcode 10 和适用于 Mac 和 Visual Studio 2017 更新 Visual Studio。

## <a name="download-and-install"></a>下载并安装

1. **安装最新的 Xcode 10 beta** – 已注册的 Apple 开发人员可以下载并安装最新版本从 Xcode 10 [Apple 开发人员门户](https://developer.apple.com/download/)。

   > [!NOTE]
   > IOS 12、 12、 tvOS 和 watchOS 5 Sdk 随 Xcode 10。

2. **运行 Xcode 10** – 更新和运行 Visual Studio for Mac 或 Visual Studio 2017; 之前运行 Xcode 10 安装 Xamarin 需要一些工具。

3. **适用于 Mac 和 Visual Studio 2017 更新 Visual Studio** – 按照上的说明[发布博客](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)安装 Xamarin 预览版。

4. _（可选）_ **IOS 设备上安装最新的 iOS beta** – 表示设备测试的应用，使用新引入了 iOS 12、 12、 tvOS 或 watchOS 5 Api，已注册的 Apple 开发人员可以[下载](https://developer.apple.com/download)和在其设备上安装最新 iOS 12、 12、 tvOS 或 watchOS 5 开发人员测试版。

   > [!TIP]
   > 即使您的应用程序不使用任何新 iOS 12、 12、 tvOS 或 watchOS 5 Api，请务必使用 iOS 12、 12、 tvOS 或 watchOS 生成 5 SDK （使用 Xcode 10 安装） 和测试，以确保一切正常作为预期。 如果应用不会调用任何新的 Api，你可以重新编译它使用 iOS 12、 12、 tvOS 或 watchOS 5 SDK 并对其测试尚未升级到新操作系统的设备上。

   > [!IMPORTANT]
   > 在你的设备升级到 iOS 12、 12、 tvOS 或 watchOS 5 测试调用新的 iOS 12、 12、 tvOS 或 watchOS 5 的 Xamarin 应用程序之前的 Api:
   > - 读取[Apple 的发行说明](https://developer.apple.com/download/)操作系统更新。
   > - 读取 Xamarin 预览版[发布博客文章](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)。

## <a name="related-links"></a>相关链接

- [下载 Xcode 10](https://developer.apple.com/download/)
- Xamarin 预览版[发布博客文章](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)
