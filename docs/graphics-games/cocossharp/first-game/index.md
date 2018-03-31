---
title: 使用 CocosSharp 游戏开发简介
description: 此多个部分的演练演示如何创建使用 CocosSharp 简单二维游戏。 它介绍常见游戏的编程概念，如图形、 输入和物理。
ms.topic: article
ms.prod: xamarin
ms.assetid: BCA99A61-A48D-4207-9A35-4C62F10C9AA5
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 5ab6f68aed791dd21516d663367ac5435e92d6cc
ms.sourcegitcommit: 7b88081a979381094c771421253d8a388b2afc16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="introduction-to-game-development-with-cocossharp"></a>使用 CocosSharp 游戏开发简介

_此多个部分的演练演示如何创建使用 CocosSharp 简单二维游戏。它介绍常见游戏的编程概念，如图形、 输入和物理。_

CocosSharp 二维游戏引擎可为进行跨平台游戏提供技术。 有关受支持的平台的完整列表请参阅[CocosSharp GitHub 上的 wiki](https://github.com/mono/CocosSharp/wiki)。 本教程将使用 C# 代码示例，虽然 CocosSharp 也是完全正常运行 F #。

由提供的核心 CocosSharp [MonoGame framework](http://www.monogame.net/)，它本身就是跨平台，用于导入资产提供图形、 音频、 游戏状态管理、 输入和内容的管道的 API 的硬件加速。 CocosSharp 是非常适合于二维游戏的高效的抽象层。 此外，较大游戏可以根据游戏的复杂性有所增加执行其核心库之外自己优化。 换而言之，CocosSharp 提供多种易用性和性能，使开发人员能够快速启动而限制游戏的大小和复杂性。

此演练专注于设置一个空的项目的第一节。  第二部分介绍如何编写所有我们游戏逻辑。 

本演练结束我们将创建一个简单的游戏玩家的目标是滑动刀片水平拖动以尝试保留一个球侵扰显示在屏幕位置。 每个弹跳将通过一个点增加玩家的分数。

![](images/image1.png "每个弹跳在玩家的分数将增加一个点")

# <a name="walkthrough-parts"></a>演练部分

* [第 1 – 创建 CocosSharp 项目部分](~/graphics-games/cocossharp/first-game/part1.md)
* [第 2 – 实现 BouncingGame 部分](~/graphics-games/cocossharp/first-game/part2.md)

## <a name="related-links"></a>相关链接

- [游戏内容 （示例）](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
- [已完成的项目 （示例）](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [在 NuGet 上 CocosSharp PCL](http://www.nuget.org/packages/CocosSharp.PCL.Shared/)
- [CocosSharp API 文档](https://developer.xamarin.com/api/namespace/CocosSharp/)
