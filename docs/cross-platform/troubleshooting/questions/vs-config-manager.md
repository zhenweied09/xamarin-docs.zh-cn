---
title: 为什么 Visual Studio 未将引用的库项目包含在生成中？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: 8a35c8e0cd03d5ff07405647b08275ef9ca3758d
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>为什么 Visual Studio 不在生成中包括我引用的库的项目

Visual Studio 使用 **Configuration Manager** 确定解决方案中自动包含在给定版本或部署配置中的项目。

某些使用引用的库项目生成的模板可能已将引用的库包含在配置中，否则需要手动进行设置。

## <a name="how-to-use-the-configuration-manager"></a>如何使用 Configuration Manager

1. 打开“生成”>“Configuration Manager”
2. 选择要自定义，例如配置**调试 | iPhone**
3. 选择要包括的项目的复选框。

> [!NOTE]
> 灰色框进行自动处理，并且不应需要的任何更改。

这些步骤的视频演示： [http://screencast.com/t/zLoQOpEn](http://screencast.com/t/zLoQOpEn)
