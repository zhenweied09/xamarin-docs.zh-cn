---
title: "所需项目的设置所需的调试器？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 6097dc8dfdff8807137ef68be86a08e4c9e23988
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>所需项目的设置所需的调试器？

为了使调试器以达到预期效果 （命中的断点、 显示调试日志等），开发人员检测和调试信息显示必须同时启用。

请按照以下步骤来检查你的环境设置：

## <a name="visual-studio"></a>Visual Studio
1. 打开项目选项
2. 转到**生成 > 高级...**设置为调试信息**完整**
3. 每个平台的设置：
   - 转到**Android 选项 > 调试选项**。 刻度**启用开发人员检测**框。
   - 转到**iOS 生成 > 调试选项**。 刻度**启用调试**框。

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac
1. 打开项目选项
2. 转到**生成 > 编译器 > 常规选项**。 设置为调试信息**完整**
3. 每个平台的设置：
  - 转到**生成 > Android 生成 > 调试选项**。 刻度**启用开发人员检测**框。
  - 转到**生成 > iOS 调试**。 刻度**启用调试**框。

