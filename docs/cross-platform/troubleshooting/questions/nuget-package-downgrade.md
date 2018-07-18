---
title: 如何将降级 NuGet 包
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: asb3993
ms.author: amburns
ms.openlocfilehash: 50a96340f8dada802303d6de140812801fdc836d
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33947516"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>如何将降级 NuGet 包

适用于 Mac 的 visual Studio 和 Visual Studio 这两个具有用于选择较旧版本的包以及自动; 安装它们的功能类似于如何更新包工作原理。 这些步骤如下所述。

## <a name="visual-studio"></a>Visual Studio
1. 转到**工具 > NuGet 包管理器 > 程序包管理器控制台**
2. 将在项目设置**默认项目**
3. 使用以下语法：

    > 安装包 [PackageName]-版本 [版本菜单的选项卡]

你还可以复制/粘贴从程序包的 NuGet 页的确切命令。 Xamarin.Forms 的示例： [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac
1. 在项目中，右键单击包文件夹和选择**添加包**
2. 在 searchbar，你可以使用以下语法搜索所需的包：

    `[PackageName] version:*`

### <a name="examples"></a>示例 
- 列出所有 Xamarin.Forms 包： 

    `Xamarin.Forms version:`
- 列出所有 Xamarin.Forms 1.4.x 包： 

    `Xamarin.Forms version:1.4`

*注意： 如果添加之间留一个空格`version:`和版本号，搜索将表现得像不指定的任何版本。*

