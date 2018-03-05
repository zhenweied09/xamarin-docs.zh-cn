---
title: "使用与 CocosSharp 平铺"
description: "平铺是功能强大，灵活，以及用于创建正交和等轴磁贴的成熟应用程序的映射的游戏。 CocosSharp 提供平铺的本机文件格式的内置集成。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 804C042C-F62A-4E6C-B10F-06528637F0E2
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 5a469a372a9299712be7aef46c51f3d644946535
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="using-tiled-with-cocossharp"></a>使用与 CocosSharp 平铺

_平铺是功能强大，灵活，以及用于创建正交和等轴磁贴的成熟应用程序的映射的游戏。CocosSharp 提供平铺的本机文件格式的内置集成。_

*平铺*应用程序是标准，用于创建*磁贴地图*在游戏开发中使用。 本指南将指导如何采用现有.tmx 文件 （平铺所创建的文件） 并将其用于 CocosSharp 游戏。 具体而言，本指南将介绍：

 - 磁贴地图的用途
 - 使用.tmx 文件
 - 呈现像素艺术作品的注意事项
 - 在运行时使用磁贴属性

当完成我们将具有以下演示：

![](tiled-images/image1.png "按照本指南中的步骤创建演示应用程序")


# <a name="the-purpose-of-tile-maps"></a>磁贴地图的用途

磁贴映射已存在的游戏开发几十年来，但仍通常用于在二维游戏中其效率和 esthetics。 平铺图是效率的能够实现通过其使用磁贴集 – 使用磁贴映射的源映像极高级别。 磁贴组是合并成一个文件的映像的集合。 虽然磁贴设置引用在磁贴映射中使用的图像，但包含多个较小的图像文件也称为画面表或画面映射游戏开发中。 我们可以可视化向磁贴集中，我们将使用在我们的演示程序中添加一个网格，使用磁贴设置：

![](tiled-images/image2.png "添加到磁贴网格设置使用磁贴集的可视化的视图将用于演示")

磁贴地图排列磁贴集中的各个磁贴。 我们应注意每个磁贴映射不需要存储其自己的磁贴副本 set – 相反，多个磁贴映射可以引用同一磁贴组。 这意味着，除了磁贴组中，磁贴地图需要很少的内存。 这允许创建大量的磁贴地图，即使它们用于创建一个大游戏的区域，如[滚动 platformer](http://en.wikipedia.org/wiki/Platform_game)环境。 下面演示了可能使用相同的磁贴组的布局：

![](tiled-images/image3.png "此图显示了可能的排列方式使用相同的磁贴组")

![](tiled-images/image4.png "此图显示了可能的排列方式使用相同的磁贴组")


# <a name="working-with-tmx-files"></a>使用.tmx 文件

.Tmx 文件格式是由平铺应用程序，可创建一个 XML 文件[平铺网站上免费下载](http://www.mapeditor.org/)。 .Tmx 文件格式存储的信息适用于磁贴映射。 通常游戏将具有一个.tmx 文件为每个级别或单独的区域。

本指南重点介绍如何使用 CocosSharp; 中的现有.tmx 文件但是，其他教程找不到联机状态，包括[此简介平铺映射表编辑器](http://gamedevelopment.tutsplus.com/tutorials/introduction-to-tiled-map-editor--gamedev-2838)。

你将需要解压缩[内容 zip 文件](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)在我们的游戏中使用它。 第一个需要注意的事项是磁贴地图使用两个.tmx 文件 (dungeon.tmx) 以及一个或多个图像文件用于定义该磁贴设置数据 (dungeon_1.png)。 因此的游戏的需求，以包含这两个要加载在运行时，.tmx 这些文件添加到项目的同时**内容**文件夹 (它包含在**资产**Android 项目中的文件夹)。 具体而言，将文件添加到一个名为文件夹**tilemaps**内**内容**文件夹：

![](tiled-images/image5.png "将文件添加到文件夹在内容的文件夹内调用 tilemaps")

**Dungeon.tmx**现在可以在运行时到加载文件`CCTileMap`对象。 接下来，修改`GameLayer`（或等效的根容器对象） 以包含`CCTileMap`实例并将其作为子级添加：


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");
        this.AddChild (tileMap);
    }
} 
```

如果我们运行的游戏我们将要看到的磁贴映射将显示在屏幕的左下角中：

![](tiled-images/image6.png "如果运行游戏时，磁贴映射显示在屏幕的左下角")


# <a name="considerations-for-rendering-pixel-art"></a>呈现像素艺术作品的注意事项

像素画，视频游戏开发的上下文中是指二维 visual 画通常创建通过手形，并且通常低分辨率。 像素艺术作品严格，则可以是时间密集型若要创建，因此像素艺术作品磁贴集通常包括低分标率磁贴，如 16 或 32 个像素宽度和高度。 如果未在运行时比例进行调整，像素图片是通常太小，大多数现代手机和平板电脑。

我们可以调整显示的尺寸在我们的游戏 GameAppDelegate.cs 文件中，我们将在其中添加调用`CCScene.SetDefaultDesignResolution`:


```csharp
 public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");

    CCSize windowSize = mainWindow.WindowSizeInPixels;

    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to be (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDefaultDesignResolution (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    
    // Determine whether to use the high or low def versions of our images
    // Make sure the default texel to content size ratio is set correctly
    // Of course you're free to have a finer set of image resolutions e.g (ld, hd, super-hd)
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }

    // New code:
    CCScene.SetDefaultDesignResolution (380, 240, CCSceneResolutionPolicy.ShowAll);

    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);

    mainWindow.RunWithScene (scene);
} 
```

有关详细信息`CCSceneResolutionPolicy`，请参阅上的我们指南[处理中 CocosSharp 分辨率](~/graphics-games/cocossharp/resolutions.md)。

如果我们现在运行游戏时，我们将看到游戏占用整个屏幕的我们的设备：

![](tiled-images/image7.png "游戏占用整个屏幕的设备")

最后，我们将需要禁用我们磁贴图上的抗锯齿。 `Antialiased`属性呈现在放大对象时应用模糊效果。 抗锯齿功能可用于减少像素化外观的图形对象，但也可能引入其自己的呈现项目。 具体而言，抗锯齿模糊的每个图块的内容。 但是，不还模糊的每个图块的边缘，从而而不是使用相邻的磁贴混合突出显示的单个磁贴。 我们还应注意像素艺术作品游戏通常保留其像素化查看，维护*怀旧式*感觉。

设置`Antialiased`到`false`后构造`tileMap`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();

    tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

    // new code:
    tileMap.Antialiased = false;

    this.AddChild (tileMap);
} 
```

现在我们磁贴映射将不会出现模糊：

![](tiled-images/image8.png "现在磁贴映射将不会出现模糊")


# <a name="using-tile-properties-at-runtime"></a>在运行时使用磁贴属性

到目前为止我们有`CCTileMap`将.tmx 文件加载和显示它，但我们没有任何办法与其进行交互。 具体而言，某些磁贴 （如我们宝藏胸部） 需要具有自定义逻辑。 我们将逐步了解如何检测自定义磁贴属性和各种方法对这些属性在运行时一次标识做出响应。

我们编写任何代码之前，我们将需要将属性添加到通过平铺我们平铺图。 若要执行此操作，请在平铺程序中打开 dungeon.tmx 文件。 一定要打开游戏项目中使用的文件。

后打开，请在设置以查看其属性的磁贴中选择宝藏胸部：

![](tiled-images/image9.png "后打开，在设置以查看其属性的磁贴中选择宝藏胸部")

如果未出现宝藏胸部属性，在宝藏胸部上右键单击并选择**磁贴属性**:

![](tiled-images/image10.png "如果未出现宝藏胸部属性，在宝藏胸部上右键单击并选择磁贴属性")

平铺的属性实现具有一个名称和值。 若要添加某个属性，请单击 **+** 按钮，输入名称**IsTreasure**，单击**确定**，然后输入值**true**: 

![](tiled-images/image11.png "若要添加一个属性，单击按钮，输入名称 IsTreasure，单击确定，然后输入值 true")

不要忘记将.tmx 文件保存后修改属性。

最后，我们将添加代码以查找我们新添加的属性。 我们将循环访问每个`CCTileMapLayer`（我们映射具有 2 层），然后通过每个行和列，查找具有任何磁贴`IsTreasure`属性：


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

        // new code:
        tileMap.Antialiased = false;

        this.AddChild (tileMap);

        HandleCustomTileProperties (tileMap);
    }

    void HandleCustomTileProperties(CCTileMap tileMap)
    {
        // Width and Height are equal so we can use either
        int tileDimension = (int)tileMap.TileTexelSize.Width;

        // Find out how many rows and columns are in our tile map
        int numberOfColumns = (int)tileMap.MapDimensions.Size.Width;
        int numberOfRows = (int)tileMap.MapDimensions.Size.Height;

        // Tile maps can have multiple layers, so let's loop through all of them:
        foreach (CCTileMapLayer layer in tileMap.TileLayersContainer.Children)
        {
            // Loop through the columns and rows to find all tiles
            for (int column = 0; column < numberOfColumns; column++)
            {
                // We're going to add tileDimension / 2 to get the position
                // of the center of the tile - this will help us in 
                // positioning entities, and will eliminate the possibility
                // of floating point error when calculating the nearest tile:
                int worldX = tileDimension * column + tileDimension / 2;
                for (int row = 0; row < numberOfRows; row++)
                {
                    // See above on why we add tileDimension / 2
                    int worldY = tileDimension * row + tileDimension / 2;

                    HandleCustomTilePropertyAt (worldX, worldY, layer);
                }
            }
        }
    }

    void HandleCustomTilePropertyAt(int worldX, int worldY, CCTileMapLayer layer)
    {
        CCTileMapCoordinates tileAtXy = layer.ClosestTileCoordAtNodePosition (new CCPoint (worldX, worldY));

        CCTileGidAndFlags info = layer.TileGIDAndFlags (tileAtXy.Column, tileAtXy.Row);

        if (info != null)
        {
            Dictionary<string, string> properties = null;

            try
            {
                properties = tileMap.TilePropertiesForGID (info.Gid);
            }
            catch
            {
                // CocosSharp 
            }

            if (properties != null && properties.ContainsKey ("IsTreasure") && properties["IsTreasure"] == "true" )
            {
                layer.RemoveTile (tileAtXy);

                // todo: Create a treasure chest entity
            }
        }
    }
} 
```

代码的大部分都具有自我说明性，但我们应该讨论宝藏磁贴的处理。 在这种情况下，我们将删除标识为宝藏 chests 任何磁贴。 这是因为宝藏 chests 可能需要在运行时的影响冲突或奖励宝物打开时的内容的播放器的自定义代码。 此外，可能需要宝物以响应正在打开 （更改其可视外观） 并且可能会逻辑敌人仅出现在屏幕上的所有已失效。

换而言之，宝藏胸部将受益于正在实体，而不是由中的简单磁贴`CCTileMap`。 有关游戏的实体的详细信息，请参阅[CocosSharp 中的实体指导](~/graphics-games/cocossharp/entities.md)。


# <a name="summary"></a>摘要

本演练介绍如何加载到 CocosSharp 应用程序创建的平铺的.tmx 文件。 它演示如何修改应用解决方法要考虑较低分辨率像素画，以及如何查找其属性以执行自定义逻辑，如创建实体实例磁贴。

## <a name="related-links"></a>相关链接

- [平铺的网站](http://www.mapeditor.org/)
- [内容 zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)
