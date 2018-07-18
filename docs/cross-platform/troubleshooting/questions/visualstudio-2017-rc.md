---
redirect_url: /xamarin/cross-platform/troubleshooting/questions/
title: 可以通过 Xamarin 使用 Visual Studio 自 2017 年 1 候选发布版本？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8E752F36-F73A-4EFC-9F82-4E18FDE1C9E2
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4c93653793c7c8cce0d2257107097c89f73fc726
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33946715"
---
# <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarin"></a>可以通过 Xamarin 使用 Visual Studio 自 2017 年 1 候选发布版本？

## <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarin"></a>可以通过 Xamarin 使用 Visual Studio 自 2017 年 1 候选发布版本？

可以。 现在能够利用 Xamarin 通过 Visual Studio 自 2017 年 1 候选发布版本。 但是，请注意，当前，到 Visual Studio 自 2017 年 1 RC 安装 Xamarin 将卸载任何早期版本的安装到 Visual Studio 2015/2013 Xamarin。 Xamarin 的 Visual Studio 2017 不能安装在同一时间作为 Xamarin 对于 Visual Studio 2015/2013 由于 Visual Studio 2017 远离朝着利用 Visual Studio 安装程序系统的 MSI 包。

虽然团队当前研究如何绕过此预期的行为，建议用户选择其基于其需求的开发环境。 

> [!IMPORTANT]
> 如果你正在构建 Xamarin.iOS 项目，请确保 Visual Studio for Mac 配对 Mac 系统上为相同版本的 Xamarin.iOS 通道。

## <a name="how-do-i-install-xamarin-to-visual-studio-2017-release-candidate"></a>如何到 Visual Studio 自 2017 年 1 候选发布版本安装 Xamarin

### <a name="installing-during-the-visual-studio-2017-rc-installer"></a>Visual Studio 自 2017 年 1 RC 安装程序的过程中安装

* 选择**Xamarin**作为新的一部分的组件**Visual Studio 安装程序**

  [![](visualstudio-2017-rc-images/install1-sml.png "Visual Studio 自 2017 年 1 RC 安装程序屏幕")](visualstudio-2017-rc-images/install1-orig.png#lightbox)

这将安装 Xamarin.iOS 和 Xamarin.Android 开发的 Visual Studio 扩展。

### <a name="installing-or-reinstalling-xamarin-in-an-existing-installation-of-visual-studio-2017-rc"></a>在现有安装中的 Visual Studio 自 2017 年 1 RC 的安装或重新安装 Xamarin

#### <a name="using-the-visual-studio-installer"></a>使用 Visual Studio 安装程序：

1. 搜索 Visual Studio 安装程序应用程序

  [![](visualstudio-2017-rc-images/reinstall1-sml.png "Visual Studio 安装程序应用程序的搜索结果")](visualstudio-2017-rc-images/reinstall1-orig.png#lightbox)

2. 选择：。 **使用.NET （预览版） 移动开发**在工作负荷选项卡中，或

  [![](visualstudio-2017-rc-images/reinstall2-sml.png "VS 安装程序工作负荷选项卡")](visualstudio-2017-rc-images/reinstall2-orig.png#lightbox) b。 **Xamarin**中**各个组件**选项卡

  [![](visualstudio-2017-rc-images/reinstall3-sml.png "VS 安装程序组件选项卡")](visualstudio-2017-rc-images/reinstall3-orig.png#lightbox)

#### <a name="using-the-visual-studio-installer-within-visual-studio"></a>使用 Visual Studio 中的 Visual Studio 安装程序：
1. 导航到 Visual Studio 自 2017 年 1 起始页
2. 单击**多项目模板**下**新项目**部分

    [![](visualstudio-2017-rc-images/reinstall4-sml.png "Visual Studio 起始页")](visualstudio-2017-rc-images/reinstall4-orig.png#lightbox)
3. 单击`Open Visual Studio Installer`的左窗格中

    [![](visualstudio-2017-rc-images/reinstall5-sml.png "新建项目屏幕")](visualstudio-2017-rc-images/reinstall5-orig.png#lightbox)
4. 选择：
    
    a. **使用.NET （预览版） 移动开发**在工作负荷选项卡中，或

    [![](visualstudio-2017-rc-images/reinstall2-sml.png "VS 安装程序工作负荷选项卡")](visualstudio-2017-rc-images/reinstall2-orig.png#lightbox) b。 **Xamarin**中**各个组件**选项卡

    [![](visualstudio-2017-rc-images/reinstall3-sml.png "VS 安装程序组件选项卡")](visualstudio-2017-rc-images/reinstall3-orig.png#lightbox)
