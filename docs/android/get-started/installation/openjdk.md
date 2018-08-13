---
title: Microsoft 分发的 OpenJDK 预览版
description: 逐步介绍了如何配置适用于移动开发的 Microsoft 分发的 OpenJDK 预览版。
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: 2022337ebd65997c7b2492137193586278f2dffd
ms.sourcegitcommit: bf51592be39b2ae3d63d029be1d7745ee63b0ce1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39573589"
---
# <a name="microsofts-mobile-openjdk-distribution-preview"></a>Microsoft 分发的 OpenJDK 预览版

_本指南介绍了如何切换到 Microsoft 分发的 OpenJDK 预览版。此分发适用于移动开发。_

![预览版功能](~/media/shared/preview.png)

## <a name="overview"></a>概述

自 Visual Studio 15.9 和 Visual Studio for Mac 7.7 起，Visual Studio Tools for Xamarin 将从 Oracle JDK 迁移到仅用于 Android 开发的 OpenJDK 轻型版本：

![新工作流支持在 VS 15.8 Preview 5 中通过 Web 预览 OpenJDK](openjdk-images/openjdk.png)

此迁移带来以下优势：

- 始终拥有适用于 Android 开发的 OpenJDK 版本。

- 下载 JDK 9 或 10 不会影响开发体验。

- 显著减少了下载大小和占用空间。

- 第三方服务器和安装程序不再出现问题。

若要更快地迁移到改进后体验，可以使用 Microsoft 分发的 Mobile OpenJDK 内部版本在 Windows 和 Mac 上进行测试。 下面介绍了具体设置过程，随时可以还原回 Oracle JDK。

## <a name="download"></a>下载

首先，下载适用于系统的内部版本：

- **Mac** &ndash; https://dl.xamarin.com/OpenJDK/mac/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x86** &ndash; https://dl.xamarin.com/OpenJDK/win32/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x64** &ndash; https://dl.xamarin.com/OpenJDK/win64/microsoft-dist-openjdk-1.8.0.9.zip

## <a name="configure"></a>配置

解压缩到正确位置：

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**
- **Windows** &ndash; **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**

> [!IMPORTANT]
> 此示例使用内部版本 1.8.0.9；但你可以下载更高版本。

将 IDE 定目标到新 JDK：

- **Mac** &ndash; 依次单击“工具 > SDK 管理器 > 位置”，并将“Java SDK (JDK)位置”更改为 OpenJDK 安装的完整路径。 在以下示例中，此路径设置为“$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9”。

![在 Mac 上设置 Microsoft 分发的 Mobile OpenJDK 的 JDK 路径](openjdk-images/vsm.png)

- **Windows** &ndash; 依次单击“工具 > 选项 > Xamarin > Android 设置”，并将“Java 开发工具包位置”设置为 OpenJDK 安装的完整路径。 在以下示例中，此路径设置为“C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9”：

![在 Windows 上设置 Microsoft 分发的 Mobile OpenJDK 的 JDK 路径](openjdk-images/vs.png)

## <a name="revert"></a>还原

若要还原回 Oracle JDK，请将 Java SDK 位置更改为以前使用的 Oracle JDK 路径，并重新生成解决方案。 在 Mac 上，可以通过单击“重置为默认值”来还原 Oracle JDK 路径。

若对 Microsoft 分发的 Mobile OpenJDK 有任何疑问，请使用 IDE 中的反馈工具来报告问题，以便我们能够快速跟踪和修复问题。

## <a name="known-issues--planned-fix-dates"></a>已知问题和计划修复日期

`JAVA_HOME` 环境变量可能无法正确导出到 SDK 和设备管理器。 作为解决方法，可以将此环境变量设置为计算机上的 OpenJDK 位置。 15.9 预览版已修复此问题。

## <a name="summary"></a>总结

本文介绍了如何将 IDE 配置为使用 Microsoft 分发的 Mobile OpenJDK 预览版（计划于 2018 年晚些时候发布稳定版本）。
