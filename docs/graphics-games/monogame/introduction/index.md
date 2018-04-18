---
title: 使用 MonoGame 游戏开发简介
description: 此多个部分的演练演示如何创建使用 MonoGame 的简单二维应用程序。  它涵盖了常见的游戏编程概念，如图形中，输入，游戏实体和物理。
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: c94ed2e06ee57b67745b6a02692df2360aeb9754
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="introduction-to-game-development-with-monogame"></a>使用 MonoGame 游戏开发简介

_此多个部分的演练演示如何创建使用 MonoGame 的简单二维应用程序。它涵盖了常见的游戏编程概念，如图形中，输入，游戏实体和物理。_

本指南介绍了 MonoGame API 技术进行跨平台游戏。 有关平台的完整列表，请参阅[MonoGame 网站](http://www.monogame.net/)。 本教程将使用 C# 代码示例，虽然 MonoGame 也是完全正常运行 F #。

MonoGame 是一个跨平台，用于导入资产提供图形、 音频、 游戏状态管理、 输入和内容的管道的 API 的硬件加速。 与大多数的游戏引擎不同 MonoGame 并未提供或在施加任何模式或项目的结构。  这意味着开发人员可用于根据组织其代码，它还意味着，在首次开始一个新项目，则需要的安装程序代码。

本演练的第一个部分侧重于设置一个空的项目。 最后一节介绍如何编写的所有我们游戏逻辑和内容 — — 最其中将跨平台。

本演练结束时，我们将创建一个简单的游戏玩家可控的动画的字符使用触摸屏输入。  虽然这不是从技术上讲完整游戏 （因为它未包含任何 win 或丢失的条件），它演示大量游戏开发概念，并可以用作许多类型的游戏奠定了基础。 

下面显示此演练的结果：

![示例游戏字符跟随鼠标的动画](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame 和 XNA

MonoGame 库用于模拟在语法和功能的 Microsoft XNA 库。  所有 MonoGame 对象都存在 Microsoft.Xna 命名空间 – 允许无需修改在 MonoGame 中使用的大多数 XNA 代码下。 

开发人员熟悉 XNA 将已经熟悉 MonoGame 的语法，并寻找 MonoGame 所使用的其他信息的开发人员将能够引用现有的联机 XNA 演练、 API 文档和讨论。


## <a name="walkthrough-parts"></a>演练部分

- [第 1 – 创建跨平台 MonoGame 项目部分](~/graphics-games/monogame/introduction/part1.md)
- [第 2 – 实现 WalkingGame 部分](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>相关链接

- [WalkingGame MonoGame 项目 （示例）](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
- [XNB 字体 iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB 字体 Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [在 NuGet 上 MonoGame Android](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [在 NuGet 上 MonoGame iOS](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 文档](http://www.monogame.net/documentation/?page=main)
