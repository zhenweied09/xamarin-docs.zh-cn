---
title: 连接 Windows 和 macOS 上的 Visual Studio 2019 预览版
description: 本指南说明如何使用 Visual Studio 2019 预览版在 Windows 上生成 iOS 应用，同时使用 Visual Studio 2019 for Mac 预览版在 macOS 上托管内部版本。
ms.prod: xamarin
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/04/2018
ms.openlocfilehash: 1eeb79737bd712da64ce34a6b4fe83ce2823e3eb
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899360"
---
# <a name="visual-studio-2019-preview-pairing"></a>Visual Studio 2019 预览版配对

![预览](~/media/shared/preview.png)

[Visual Studio for Mac 2019 预览版](https://docs.microsoft.com/visualstudio/mac/install-preview) 是 macOS 完全支持的首个 Visual Studio 并行安装。 这就使生成 iOS 应用程序的 Windows 开发者面临一种新的情况：如果其 Mac 生成主机有 Visual Studio 2017 for Mac（7.x 版）和 Visual Studio 2019 for Mac 预览版（8.0 版）两个版本，用作 Windows 安装的生成主机的是哪一个？

无论你在 Windows 上使用的是 Visual Studio 2017 还是 Visual Studio 2019 预览版，默认情况下，Windows 上的 Visual Studio 都会使用 Visual Studio 2017 for Mac（版本 7.7）这一个稳定版本。

在 Windows 和 macOS 上适当地测试 Visual Studio 2019 预览版：

1. 在 Windows 计算机上安装并使用 Visual Studio 2019（16.0 版）预览版。
2. 在 Mac 上安装 Visual Studio 2019 for Mac（8.0 版）预览版。
3. 在 Visual Studio 2019 for Mac 中：

    a. 选择“Visual Studio”>“检查更新”菜单项。

    b. 在“Visual Studio 更新”窗口内，从更新通道中选择“Xamarin 预览版”。 随即显示以下警告：

    > [!WARNING]
    > 请尝试使用带有最新 Mono 运行时和 Xamarin SDK 的 Visual Studio 2019 for Mac 最新预览版。 从此通道安装版本将中断 Visual Studio 2017 for Mac 版本。 请仅在使用 Visual Studio 2019 预览版在 Windows 操作系统上生成 Xamarin 应用时，使用此通道。

    c. 按“切换通道”按钮启用预览版生成主机。

4. 按照[通过“与 Mac 配对”进行 Xamarin.iOS 开发](index.md)说明以及[疑难解答提示](troubleshooting.md)（如果有）操作。