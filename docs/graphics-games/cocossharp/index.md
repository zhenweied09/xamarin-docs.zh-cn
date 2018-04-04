---
title: CocosSharp
description: 此文档链接到有关使用 CocosSharp 游戏开发的各种文章。
ms.prod: xamarin
ms.assetid: 5E72869D-3541-408B-AB64-D34C777AFB79
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/29/2018
ms.openlocfilehash: 2772af61dfc60b7ecd0fd5f7ecdfe5701d2504f0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="cocossharp"></a>CocosSharp

_CocosSharp 是用于生成使用 C# 和 F # 的二维游戏的库。它是常用的 Cocos2D 引擎的.NET 端口。_

## <a name="introduction-to-cocossharp"></a>CocosSharp 简介

CocosSharp 二维游戏引擎可为进行跨平台游戏提供技术。 有关受支持的平台的完整列表请参阅[CocosSharp GitHub 上的 wiki](https://github.com/mono/CocosSharp/wiki)。
这些指南用于 C# 代码示例，尽管 CocosSharp 也使用 F # 完全正常运行。

由提供的核心 CocosSharp [MonoGame framework](http://www.monogame.net/)，它本身就是跨平台，用于导入资产提供图形、 音频、 游戏状态管理、 输入和内容的管道的 API 的硬件加速。
CocosSharp 是非常适合于二维游戏的高效的抽象层。
此外，较大游戏可以根据游戏的复杂性有所增加执行其核心库之外自己优化。 换而言之，CocosSharp 提供多种易用性和性能，使开发人员能够快速启动而限制游戏的大小和复杂性。

此动手视频演示如何创建简单的跨平台 CocosSharp 游戏：

> [!Video https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Developing-Cross-platform-2D-Games-in-C-and-CocosSharp/player]

## <a name="bouncinggamegraphics-gamescocossharpbouncing-gamemd"></a>[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)

![BouncingGame](images/bouncing-game.png "BouncingGame")

本指南介绍 BouncingGame，包括如何使用游戏内容，用来生成一个游戏、 添加游戏逻辑和的详细信息的各种可视元素。

## <a name="fruity-falls-gamegraphics-gamescocossharpfruity-fallsmd"></a>[Fruity 回退游戏](~/graphics-games/cocossharp/fruity-falls.md)

![Fruity 回退游戏屏幕截图](images/fruity-falls.png "Fruity 回退游戏屏幕快照")

本指南介绍 Fruity 回退游戏中，其中介绍了常见 CocosSharp 和游戏开发概念，如物理、 内容管理、 游戏状态和游戏的设计。  

## <a name="coin-time-gamegraphics-gamescocossharpcointimemd"></a>[抛硬币时间游戏](~/graphics-games/cocossharp/cointime.md)

![抛硬币时间游戏屏幕截图](images/cointime.png "抛硬币时间游戏屏幕快照")

抛硬币时间是用于 iOS 和 Android 游戏完整 platformer。 游戏的目的是收集所有级别中硬币并且然后同时避免敌人和障碍物到达退出门。

## <a name="drawing-geometry-with-ccdrawnodegraphics-gamescocossharpccdrawnodemd"></a>[与 CCDrawNode 绘制几何图形](~/graphics-games/cocossharp/ccdrawnode.md)

![使用 CCDrawNode 绘制的形状](images/ccdrawnode.png "使用 CCDrawNode 绘制的形状")

CCDrawNode 提供用于绘制的基元对象，例如线条、 圆形和三角形方法。

## <a name="animating-with-ccactiongraphics-gamescocossharpccactionmd"></a>[使用 CCAction 进行动画处理](~/graphics-games/cocossharp/ccaction.md)

![CCAction 动画](images/ccaction.png "CCAction 动画")

`CCAction` 是可用于动态显示 CocosSharp 对象的基类。 本指南涵盖内置`CCAction`实现常见任务，例如定位、 缩放和旋转。 它还讨论如何创建自定义实现通过继承`CCAction`。

## <a name="using-tiled-with-cocossharpgraphics-gamescocossharptiledmd"></a>[将平铺与 CocosSharp 结合使用](~/graphics-games/cocossharp/tiled.md)

![游戏中的级别](images/tiled.png "游戏中的级别")

平铺是功能强大，灵活，以及用于创建正交和等轴磁贴的成熟应用程序的映射的游戏。 CocosSharp 提供平铺的本机文件格式的内置集成。

## <a name="entities-in-cocossharpgraphics-gamescocossharpentitiesmd"></a>[CocosSharp 中的实体](~/graphics-games/cocossharp/entities.md)

![从游戏太空船](images/entities.png "禁止玩游戏太空船")

实体模式是一种强大的方法来组织游戏代码。 它可以改善可读性，使代码更易于维护，并可利用内置的父/子功能。

## <a name="handling-multiple-resolutions-in-cocossharpgraphics-gamescocossharpresolutionsmd"></a>[处理 CocosSharp 中的多个解决方案](~/graphics-games/cocossharp/resolutions.md)

![表示屏幕分辨率的网格](images/resolutions.png "表示屏幕分辨率的网格")

本指南演示如何使用 CocosSharp 开发在不同的解决方案中的设备正确显示的游戏。

## <a name="cocossharp-content-pipelinegraphics-gamescocossharpcontent-pipelineindexmd"></a>[CocosSharp 内容管道](~/graphics-games/cocossharp/content-pipeline/index.md)

![XNB](images/content-pipeline.png "XNB")

内容的管道通常用于在游戏开发优化内容，并设置其格式以便它可以在加载某些硬件上或与某些游戏开发框架。

## <a name="improving-frame-rate-with-ccspritesheetgraphics-gamescocossharpccspritesheetmd"></a>[提高与 CCSpriteSheet 的帧速率](~/graphics-games/cocossharp/ccspritesheet.md)

![从 CCSpriteSheet 树](images/ccspritesheet.png "从 CCSpriteSheet 树")

`CCSpriteSheet` 提供功能组合，并使用一个纹理中的许多图像文件。 减少纹理数可以提高游戏的加载时间和帧速率。

## <a name="texture-caching-using-cctexturecachegraphics-gamescocossharptexture-cachemd"></a>[纹理缓存使用 CCTextureCache](~/graphics-games/cocossharp/texture-cache.md)

![表示形式如何 CocosSharp 缓存映像](images/texture-cache.png "的表示形式如何 CocosSharp 缓存图像")

CocosSharp 的`CCTextureCache`类提供了一种标准的方式来组织、 缓存，和卸载内容。 

## <a name="2d-math-with-cocossharpgraphics-gamescocossharpmathmd"></a>[与 CocosSharp 二维数学](~/graphics-games/cocossharp/math.md)

![要轮替的映像](images/math.png "要轮替的映像")

本指南介绍用于游戏开发的二维数学。 它使用 CocosSharp 来演示如何执行常见的游戏开发任务，并解释了这些任务背后的数学函数。

## <a name="performance-and-visual-effects-with-ccrendertexturegraphics-gamescocossharpccrendertexturemd"></a>[性能和与 CCRenderTexture 的视觉效果](~/graphics-games/cocossharp/ccrendertexture.md)

![从游戏子画面](images/ccrendertexture.png "子画面禁止玩游戏")

`CCRenderTexture`类提供了用于呈现为单个纹理的多个 CocosSharp 对象功能。 一旦创建，`CCRenderTexture`可使用实例，若要高效呈现图形和实现视觉效果。
