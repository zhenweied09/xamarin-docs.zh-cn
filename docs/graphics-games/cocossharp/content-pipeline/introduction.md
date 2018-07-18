---
title: 内容的管道简介
description: 内容的管道有应用程序或应用程序的组成部分，用于将文件转换为可以由游戏项目加载的格式。 MonoGame 内容管道是将转换为 CocosSharp 和 MonoGame 项目文件的特定内容的管道实现。
ms.prod: xamarin
ms.assetid: 40628B5F-FAF7-4FA7-A929-6C3FEA83F8EC
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: a369c5ba61033eb61c0f188c03b21e08c71784fb
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
ms.locfileid: "33922830"
---
# <a name="introduction-to-content-pipelines"></a>内容的管道简介

_内容的管道有应用程序或应用程序的组成部分，用于将文件转换为可以由游戏项目加载的格式。MonoGame 内容管道是将转换为 CocosSharp 和 MonoGame 项目文件的特定内容的管道实现。_

本文提供了对内容的管道，主要关注概念的理解*MonoGame 内容管道*，这是与 CocosSharp 和 MonoGame 一起使用的内容的管道实现。


## <a name="what-is-a-content-pipeline"></a>什么是内容的管道？

术语*内容的管道*是为将文件从一种格式转换为另一个进程的常用术语。 *输入*的内容的管道是通常由创作工具，如图像文件从 Photoshop 输出文件。 内容的管道创建*输出*可以直接由游戏项目加载的格式的文件。 通常的输出文件进行了优化用于快速加载和减少磁盘大小。

内容的管道可能是命令行可执行文件、 专用基于 GUI 的应用程序或插件嵌入其他应用程序，如 Visual Studio 中。 内容的管道通常运行游戏执行之前。 如果某些应用程序，如 Visual Studio 与关联内容的管道，则它可能执行在编译期间。 如果内容的管道，独立的应用程序，它可能运行时明确告知应用程序为此用户。 应用程序或逻辑负责转换特定的输入的文件 (如 **.png**) 到关联的输出文件称为*生成器*。 

我们可以直观显示从创作，如下所示加载在运行时所需的文件的路径：

![](introduction-images/image1.png "从创作到正在加载在运行时所需的文件的路径是在此关系图中进行可视化")

## <a name="why-use-a-content-pipeline"></a>为何使用内容的管道？

内容的管道引入创作应用程序和游戏，这可以增加的编译时间并且会给开发过程增加复杂性之间的一个额外步骤。 尽管这些注意事项，内容的管道为游戏开发带来了许多优点：


### <a name="converting-to-a-format-understood-by-the-game"></a>将转换为格式理解的游戏

CocosSharp 和 MonoGame 提供用于加载各种类型的内容; 方法但是，必须在加载之前正确格式化的内容。 大多数类型的内容需要某种类型的转换之前正在加载。 例如，声音特效中 **.wav**格式必须转换为 **.xnb**文件是在运行时加载，因为 CocosSharp 和 MonoGame 不支持加载 **.wav**文件格式。


### <a name="converting-to-a-format-native-to-the-hardware"></a>将转换为本机硬件的格式

不同的硬件可能以不同的方式将内容，在运行时产生负面影响。 例如，CocosSharp 游戏可以加载图像文件，在创建时`CCSprite`实例。 虽然可以使用相同的代码加载在 iOS 和 Android 上的文件，但每个平台以不同的方式存储的已加载的文件。 因此，MonoGame 内容管道格式纹理 **.xnb**以不同的方式取决于目标平台的文件。


### <a name="reducing-size-on-disk"></a>减少磁盘上大小 

内容的管道可以用于删除信息，这是在创作时很有用，但在运行时不必要。 原始 （输入） 文件可以存储所有的信息可帮助内容的创建者保留现有内容，但输出文件可以是精简以减少总体游戏的文件。 这一点的下载而不是分布式安装媒体上的移动游戏特别有用。


### <a name="reducing-load-time"></a>减少加载时间

游戏可能需要修改要提高运行时性能，以提高视觉对象，或添加新功能的内容。 例如许多 3D 游戏计算照明一次，然后呈现复杂的情形时使用此计算的结果。 自加载内容可以是成本过高时执行这些计算可以改为执行计算时生成游戏。 生成计算可以包含在启用内容要比其他方式否则可能更快地加载的内容。 


## <a name="xnb-file-extension"></a>xnb 文件扩展名

**.Xnb**文件扩展名是由 Monogame 内容管道输出的所有文件的扩展名。 这符合由 Microsoft XNA 内容管道输出的文件的扩展名。

**.Xnb**扩展使用而不考虑原始文件类型。 换而言之，图像文件 (**.png**)，音频文件 (**.wav**)，和任何自定义的文件类型将所有输出为 **.xnb**文件时通过内容的管道进行传递。 由于该扩展不能用于区分不同的文件然后格式加载 CocosSharp 和 MonoGame 方法 **.xnb**文件加载文件时不希望扩展。

可以使用 Monogame 管道工具对其进行讨论创建 CocosSharp 和 MonoGame.xnb 文件[在本演练中](~/graphics-games/cocossharp/content-pipeline/walkthrough.md)。


## <a name="summary"></a>总结

本文还提供的概述和内容的管道的好处一般情况下，MonoGame 内容管道的简介。

## <a name="related-links"></a>相关的链接

- [MonoGame 管道文档](http://www.monogame.net/documentation/?page=Pipeline)
