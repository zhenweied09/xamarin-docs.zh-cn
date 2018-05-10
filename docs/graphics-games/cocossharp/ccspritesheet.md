---
title: 提高与 CCSpriteSheet 的帧速率
description: CCSpriteSheet 提供用于组合并使用一个纹理中的许多图像文件的功能。 减少纹理数可以提高游戏的加载时间和帧速率。
ms.prod: xamarin
ms.assetid: A1334030-750C-4C60-8B84-1A8A54B0D00E
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 9b0f58554b26b1a5334970b8c1288234acbf8db7
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="improving-frame-rate-with-ccspritesheet"></a>提高与 CCSpriteSheet 的帧速率

_CCSpriteSheet 提供用于组合并使用一个纹理中的许多图像文件的功能。减少纹理数可以提高游戏的加载时间和帧速率。_

很多游戏需要优化努力顺利进行与移动的硬件上的加载速度快。 `CCSpriteSheet`类可帮助您解决 CocosSharp 游戏所遇到的许多常见性能问题。 本指南介绍常见性能问题和如何解决它们使用`CCSpriteSheet`类。


## <a name="what-is-a-sprite-sheet"></a>什么是画面表？

A*画面表*，这可以也被称为*纹理地貌和城市等*，是用于将多个映像合并到一个文件的映像。 这可以提高运行时性能，以及内容的加载时间。

例如下, 图是由三个单独的映像创建一个简单的画面表。 各映像可以是任意大小，并且生成的画面表不是需要完全填充：

![](ccspritesheet-images/image1.png "各映像可以是任意大小，并且生成的画面表不需要完全填充")


### <a name="render-states"></a>呈现状态

视觉 CocosSharp 对象 (如`CCSprite`) 需要顶点缓冲区创建传统的图形 API 呈现代码 MonoGame 或 OpenGL，例如通过简化呈现代码 (中所述[绘制与 3D 图形中 MonoGame 顶点](~/graphics-games/monogame/3d/part2.md)指南)。 尽管其简易性，而 CocosSharp 并不能排除设置的成本*呈现状态*，它们的纹理或其他呈现相关的状态，必须切换呈现代码的次数。

CocosSharp 的内部代码均将呈现每个 visual 元素按顺序，通过遍历当前开头的可视化树`CCScene`。 例如，考虑以下场景：

![](ccspritesheet-images/image2.png "请考虑此场景")

CocosSharp 将序列中呈现的四个星号：

![](ccspritesheet-images/image3.png "CocosSharp 将序列中呈现的四个星号")

由于每个`CCSprite`使用相同的纹理，CocosSharp 可以将所有四个星组合在一起。 此代码需要每个框架的一个呈现器状态分配 （分配的星型纹理）。 这种情况下是非常高效。

当然，很少游戏使用只有一个映像。 以下场景引入世界各地图：

![](ccspritesheet-images/image4.png "此场景引入了世界各地图")

理想情况下 CocosSharp 应绘制使用一个映像第一次 （如星），然后使用其他图像 （在世界各地） 子画面的其余部分的所有子画面：

![](ccspritesheet-images/image5.png "理想情况下 CocosSharp 应绘制第一次使用一个映像，如的星号，然后使用其他图像在世界各地动画层的其余部分的所有子画面")

排序更高版本需要两个呈现状态： 一个在第星型，一个在第一个世界各地。

如果所有`CCSprite`实例都是相同的子`CCNode`，然后 CocosSharp 将优化以减少呈现器状态更改的绘制顺序。 另一方面，如果`CCSprite`实例进行组织，以致无法优化呈现 CocosSharp (例如，如果它们是不同的实体的一部分`CCNode`实例)，则顺序可能不是最佳。 下面显示的最差的可能的绘图顺序，从而导致呈现的五个状态：

![](ccspritesheet-images/image6.png "这将显示的最差的可能绘图顺序，从而导致五个呈现器状态")

呈现状态可能很难进行优化因为绘图顺序必须遵循的可视化树`CCNode`实例。 此树通常是结构是易于使用的 （如实体包含其可见的子级的），或由艺术家定义组织由于所需的可视布局。

当然，理想情况下是具有单个呈现状态，尽管具有多个映像。 CocosSharp 游戏可以完成此操作通过将所有图像都合并为单个文件，然后加载该文件 (以及其伴随 **.plist**文件) 到`CCSpriteSheet`。 使用`CCSpriteSheet`类变得更重要的游戏其中有大量的映像，或具有非常复杂布局。 

### <a name="load-times"></a>加载时间

将多个映像合并为一个文件还提高了多种原因的游戏的加载时间：

 - 将多个映像合并为单个文件可以减少通过有效地打包所用的像素的总数
 - 加载少选一些文件意味着开销更少每个文件，如分析.png 标头
 - 加载较少的文件需要小于查找时，这将基于磁盘的媒体如 Dvd 和传统的计算机的硬盘驱动器的重要

## <a name="using-ccspritesheet-in-code"></a>在代码中使用 CCSpriteSheet

若要创建`CCSpriteSheet`实例，该代码必须提供的映像和文件用于定义要用于每个帧的图像的区域。 可以为加载映像 **.png**或 **.xnb**文件 (如果使用[内容的管道](~/graphics-games/cocossharp/content-pipeline/index.md))。 定义帧的文件是 **.plist**可以手动创建的文件或*TexturePacker* （其下面，我们将讨论）。

示例应用程序，其中[可以在此处下载](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)，创建`CCSpriteSheet`从 **.png**和 **.plist**文件使用以下代码：

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

一次加载，`CCSpriteSheet`包含`List`的`CCSpriteFrame`实例 – 对应于用于创建整个工作表的源映像的一个每个实例。 情况下**SpriteSheetDemo**项目，`CCSpriteSheet`包含三个映像。 **.Plist** Visual Studio 中用于 Mac 或任何文本编辑器，若要查看可用的映像中，可以检查文件。 如果我们查看 **.plist**我们可以看到的三个帧 （节省略以强调键名称） 的文本编辑器中的文件：


```csharp
...
<dict>
    <key>frames</key>
    <dict>
        <key>farBackground.png</key>
        ...
        <key>foreground.png</key>
        ...
<key>forestBackground.png</key>
...
```

我们可以使用`Find`方法，如下所示按名称查找帧 (代码省略以强调`CCSpriteFrame`使用情况):


```csharp
CCSpriteFrame frame;
...
frame = sheet.Frames.Find(item=>item.TextureFilename == "farBackground.png"); 
CCSprite sprite = new CCSprite (frame); 
...
```

由于`CCSprite`构造函数可以采用`CCSpriteFrame`，代码永远不会有参数调查的详细信息`CCSpriteFrame`，如哪些纹理，它使用，或主画面表中的图像的区域。


## <a name="creating-a-sprite-sheet-plist"></a>创建画面表.plist

.Plist 文件是一个基于 xml 的文件，其中可创建和进行手动编辑。 同样，编辑程序的映像可以用于将多个文件合并到一个更大的文件。 创建和维护画面表可以非常耗时，因为我们将了解在 TexturePacker 程序可以导出 CocosSharp 格式文件。 TexturePacker 提供免费和的"Pro"的版本，并且可用于 Windows 和 Mac OS。 通过使用免费版本，可以遵循本指南的其余部分。 

可以是 TexturePacker[从 TexturePacker 网站下载](https://www.codeandweb.com/texturepacker)。 打开时，TexturePacker 没有加载的项目。 开始屏幕，可添加子画面，打开最近的项目 （如果已创建其他项目），并选择要用于画面表的格式。 CocosSharp 使用 Cocos2D 数据格式：

![](ccspritesheet-images/image7.png "CocosSharp 使用 Cocos2D 数据格式")

图像文件 (如 **.png**) 可以通过拖放方式它们从 Windows 资源管理器在 Windows 或 mac 上的 Finder 上添加到 TexturePacker 每当添加文件时，TexturePacker 自动更新画面表预览：

![](ccspritesheet-images/image8.png "TexturePacker 自动更新画面表预览，无论何时添加文件")

若要导出的画面表，请单击**发布画面表**按钮，然后选择画面表的位置。 TexturePacker 将保存.plist 文件和图像文件。

若要使用生成的文件，请向 CocosSharp 项目添加的.png 和.plist。 有关将文件添加到 CocosSharp 项目的信息，请参阅[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)。 一旦将文件添加，它们可以是加载到`CCSpriteSheet`如上面的代码中前面所示：

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

### <a name="considerations-for-maintaining-a-texturepacker-sprite-sheet"></a>维护 TexturePacker 画面表注意事项

随着开发游戏，艺术家可能添加、 删除或修改艺术作品。 任何更改都需要更新的画面表。 以下注意事项可以轻松地画面表维护：

 - 将原始文件 （用于创建画面表的文件） 存储在你项目的文件夹中，并确保它们添加到版本控制。 这些文件将需要重新创建画面表，只要进行了更改。
 - 不要将原始文件添加到 Visual Studio 中，for Mac/Visual Studio，或如果添加了它们，将设置**生成操作**到**无**。 如果文件被添加，并且具有特定于平台的**生成操作**，则它们将不必要地增加生成应用程序的文件大小。
 - 请考虑使用*智能文件夹*TexturePacker 中。 智能文件夹会自动添加到动画层工作表的包含的任何映像。 此功能可以具有大量映像保存大量开发游戏的时间。 

    ![](ccspritesheet-images/image9.png "此功能可以具有大量映像保存大量时间开发游戏")
 - 关注画面表纹理大小。 某些较旧的 phone 硬件不支持大于 2048 x 2048 纹理大小。 此外，2048 x 2048 的 32 位映像使用几乎 17 mb 的 RAM – 占用大量的内存。
 - TexturePacker 不包括文件夹画面名称中默认情况下，因此可能会出现名称冲突。 最好决定是否要包括的文件夹名称或未开发的开头。 更大的游戏应考虑使用文件夹名称以防止冲突。 若要包括文件夹路径，请单击**显示高级**中**数据**部分，并检查**前置文件夹名称**。 

    ![](ccspritesheet-images/image10.png "若要包括文件夹路径，单击 Data 节中显示高级，请检查前置文件夹名称")

## <a name="summary"></a>总结

本指南介绍如何创建和使用`CCSpriteSheet`类。 它还介绍如何构造文件可以加载到`CCSpriteSheet`实例使用 TexturePacker 程序。

## <a name="related-links"></a>相关的链接

- [CCSpriteSheet](https://developer.xamarin.com/api/type/CocosSharp.CCSpriteSheet/)
- [完整演示 （示例）](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)
