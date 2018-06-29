---
title: IOS 12 和 tvOS 12 入门
description: 本文档介绍如何获取多达生成 iOS 12 以及如何使用 Xamarin tvOS 12 应用设置。 它讨论了如何下载 Xcode 10 和适用于 Mac 和 Visual Studio 2017 更新 Visual Studio。
ms.prod: xamarin
ms.assetid: 6C0F0133-1A5F-408B-8BCA-BDCA313A55C2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 887a5cc72b951b2f115cdc6998a6cf6ca7a94df0
ms.sourcegitcommit: 4939748d537cbb1934a3b5b22add39ef3d9aa64d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37091553"
---
# <a name="getting-started-with-ios-12-and-tvos-12"></a>IOS 12 和 tvOS 12 入门

![预览](~/media/shared/preview.png)

> [!WARNING]
> Xamarin 的 iOS 12 和 tvOS 12 的支持是当前在预览版中，它可能包含 bug，这意味着不是功能完成，并可能会更改。 将其用于实验仅。

> [!NOTE]
> 详细信息，请阅读[发行说明](https://releases.xamarin.com/preview-release-xcode-10-beta/)为 Xamarin 预览版。

本文档介绍如何获取多达生成 iOS 12 以及如何使用 Xamarin tvOS 12 应用设置。 它讨论了如何下载 Xcode 10 和适用于 Mac 和 Visual Studio 2017 更新 Visual Studio。

## <a name="download-and-install"></a>下载并安装

1. **安装最新的 Xcode 10 beta** – 注册 Apple 开发人员可以下载并安装最新版本的 Xcode 10 从[Apple 开发人员门户](https://developer.apple.com/download/)。

   > [!NOTE]
   > IOS 12 和 tvOS 12 Sdk 随 Xcode 10。

2. **运行 Xcode 10** – 在更新并运行 Visual Studio for Mac 或 Visual Studio 2017; 之前运行 Xcode 10 安装 Xamarin 需要一些工具。

3. **更新 Visual Studio 针对 Mac 和 Visual Studio 2017** – 按照中的说明[发行说明](https://releases.xamarin.com/preview-release-xcode-10-beta/)安装 Xamarin 预览。

4. _（可选）_ **在 iOS 设备上安装最新的 iOS beta** – 表示设备的应用程序测试，使用新引入 iOS 12 或 tvOS 12 Api，已注册的 Apple 开发人员可以[下载](https://developer.apple.com/download)并安装最新的 iOS 12 或 tvOS 12 开发人员在其 iOS 和 tvOS 的设备上的测试版。

   > [!TIP]
   > 即使你的应用程序不使用任何新的 iOS 12 或 tvOS 12 Api，一定要生成 iOS 12 或 tvOS 12 SDK （随 Xcode 10 一起安装） 和测试它以确保一切正常作为预期。 如果应用程序不调用任何新的 Api，你可以与 iOS 12 或 tvOS 12 重新编译 SDK 并在尚未升级到新操作系统的设备上对其进行测试。

   > [!IMPORTANT]
   > 在升级你的设备到 iOS 12 或 tvOS 12 调用新的 iOS 12 的 Xamarin 应用程序进行测试或 tvOS 12 之前 Api:
   > - 读取[Apple 的发行说明](https://developer.apple.com/download/)操作系统更新。
   > - 读取[发行说明](https://releases.xamarin.com/preview-release-xcode-10-beta/)为 Xamarin 预览版。

## <a name="related-links"></a>相关链接

- [下载 Xcode 10](https://developer.apple.com/download/)
- Xamarin 预览[发行说明](https://releases.xamarin.com/preview-release-xcode-10-beta/)
