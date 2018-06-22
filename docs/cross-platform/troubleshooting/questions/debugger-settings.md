---
title: 所需项目的设置所需的调试器？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: asb3993
ms.author: amburns
ms.openlocfilehash: 67c4b51a518f5c7dba6ae372dbd9206dd3ef8e9f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919984"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>所需项目的设置所需的调试器？

为了使调试器以达到预期效果 （命中的断点、 显示调试日志等），开发人员检测和调试信息显示必须同时启用。

请按照以下步骤来检查你的环境设置：

## <a name="visual-studio"></a>Visual Studio
1. 打开项目选项
2. 转到**生成 > 高级...** 设置为调试信息**完整**
3. 每个平台的设置：
   - 转到**Android 选项 > 调试选项**。 刻度**启用开发人员检测**框。
   - 转到**iOS 生成 > 调试选项**。 刻度**启用调试**框。

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac
1. 打开项目选项
2. 转到**生成 > 编译器 > 常规选项**。 设置为调试信息**完整**
3. 每个平台的设置：
  - 转到**生成 > Android 生成 > 调试选项**。 刻度**启用开发人员检测**框。
  - 转到**生成 > iOS 调试**。 刻度**启用调试**框。

