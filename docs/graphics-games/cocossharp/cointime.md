---
title: 抛硬币时游戏的详细信息
description: 本指南讨论在抛硬币时间游戏中，包括使用磁贴图、 创建实体，进行动画处理子画面，和实现高效的冲突的实现详细信息。
ms.topic: article
ms.prod: xamarin
ms.assetid: 5D285684-0417-4E16-BD14-2D1F6DEFBB8B
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 8c33b74af80a14df1626ab39ba8c055a81259194
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="coin-time-game-details"></a>抛硬币时游戏的详细信息

_本指南讨论在抛硬币时间游戏中，包括使用磁贴图、 创建实体，进行动画处理子画面，和实现高效的冲突的实现详细信息。_

抛硬币时间是用于 iOS 和 Android 游戏完整 platformer。 游戏的目的是收集所有级别中硬币并且然后同时避免敌人和障碍物到达退出门。

![](cointime-images/image1.png "游戏的目的是收集所有级别中硬币并随后将同时避免敌人和障碍物到达退出门")

本指南讨论抛硬币时间，涵盖以下主题中的实现详细信息：

- [使用 tmx 文件](#working-with-tmx-files)
- [级别加载](#level-loading)
- [添加新实体](#adding-new-entities)
- [动画的实体](#animated-entities)


## <a name="content-in-coin-time"></a>抛硬币时间中的内容

抛硬币时间是表示完整的 CocosSharp 项目可能整理方式的示例项目。 抛硬币时间的结构旨在简化的添加和维护的内容。 它使用**.tmx**创建的文件[平铺](http://www.mapeditor.org)级别和 XML 文件来定义动画。 修改或添加新内容，可以通过最小的工作量。 

尽管此方法使得抛硬币时间的有效于学习和试验项目，它还反映如何专业游戏进行。 本指南介绍了某些所需简化添加和修改内容的方法。


## <a name="working-with-tmx-files"></a>使用 tmx 文件

使用.tmx 文件格式，此输出通过定义抛硬币的时间级别[平铺](http://www.mapeditor.org)磁贴映射表编辑器。 使用平铺的详细讨论，请参阅[使用平铺科科斯尖锐指南](~/graphics-games/cocossharp/tiled.md)。 

在中包含其自己.tmx 文件中定义每个级别**CoinTime/资产/内容/级别**文件夹。 所有抛硬币时间级别共享一个 tileset 文件，在中定义**mastersheet.tsx**文件。 此文件定义为每个图块，如该磁贴是否具有实体冲突或是否磁贴应替换为实体实例的自定义属性。 Mastersheet.tsx 文件允许属性来定义一次并在所有级别上使用。 


### <a name="editing-a-tile-map"></a>编辑磁贴映射

若要编辑的磁贴映射，请通过双击.tmx 文件或通过打开通过平铺中的文件菜单中平铺打开.tmx 文件。 抛硬币级别磁贴图包含三个层的时间： 

- **实体**– 此层包含磁贴将替换为在运行时的实体的实例。 示例包括播放器、 硬币、 敌人和结束的级别门。
- **地形**– 这一层包含其通常具有实体冲突的磁贴。 实心冲突允许播放器遍历这些磁贴上，而从未下降。 带有实线冲突磁贴还可以充当墙壁和上限。
- **后台**– 背景层包含用作静态背景的磁贴。 此层不滚动时相机移动整个级别，创建通过视差深度的外观。

我们将更高版本浏览，级别加载代码需要在所有抛硬币时间级别这些三个层。

#### <a name="editing-terrain"></a>编辑地形

可以通过单击在放置磁贴**mastersheet** tileset，然后单击该磁贴映射。 例如，若要绘制新地形级别中：

1. 选择地形层
1. 单击该磁贴以绘制
1. 单击或推送和拖动要绘制该磁贴的映射

    ![](cointime-images/image2.png "单击该磁贴以绘制 1")

左上方的 tileset 包含所有抛硬币时间地形。 地形，为纯色，包括**SolidCollision**属性，在屏幕左侧的磁贴属性中所示：

![](cointime-images/image3.png "地形，为纯色，包括 SolidCollision 属性中，在屏幕左侧的磁贴属性中所示")

#### <a name="editing-entities"></a>编辑实体

可以添加或删除从级别 – 一样地形实体。 **Mastersheet** tileset 有放置有关便中途水平的所有实体，因此它们可能不是可见而无需向右滚动：

![](cointime-images/image4.png "Mastersheet tileset 有放置有关便中途水平的所有实体，因此它们可能不是可见而无需向右滚动")

新的实体应置于**实体**层。

![](cointime-images/image5.png "新的实体应将其置于实体层")

CoinTime 代码查找**EntityType**时加载级别以确定实体应替换为磁贴： 

![](cointime-images/image6.png "当加载级别以确定实体应替换为磁贴时，为 EntityType 查找 CoinTime 代码")

修改并保存该文件后，所做的更改将自动显示项目生成和运行：

![](cointime-images/image7.png "修改并保存该文件后，所做的更改将自动显示是否生成和运行项目")


### <a name="adding-new-levels"></a>添加新的级别

将级别添加到抛硬币时间的过程需要更改任何代码和仅少量小对项目的更改。 若要添加一个新级别：

1. 打开级别文件夹位于 <**CoinTime 根 > \CoinTime\Assets\Content\levels**
1. 复制并粘贴的一个级别，如**level0.tmx**
1. 命名此新.tmx 文件，因此它将继续使用现有级别的级别编号规则如**level8.tmx**
1. 在 Visual Studio 或适用于 Mac 的 Visual Studio 中，将新的.tmx 文件添加到 Android 级别文件夹。 验证该文件使用**AndroidAsset**生成操作。

    ![](cointime-images/image8.png "验证该文件使用 AndroidAsset 生成操作")

1. 将新的.tmx 文件添加到 iOS 级别文件夹。 请务必将该文件从其原始位置链接并验证它使用**BundleResource**生成操作。

    ![](cointime-images/image9.png "请务必将该文件从其原始位置链接并验证它使用 BundleResource 生成操作")

新级别应在级别选择屏幕中显示为级别 9 (级别文件名启动保持为 0，但级别按钮以数字 1 开头):

![](cointime-images/image10.png "新级别应在级别选择屏幕中显示为级别 9 级别的文件名称开始保持为 0，但级别按钮以数字 1 开头")


## <a name="level-loading"></a>级别加载

如前面所示，更高级别的需要在代码中的任何更改 – 游戏，如果它们都正确命名并添加到自动检测是否级别**级别**文件夹具有正确的生成操作 (**BundleResource**或**AndroidAsset**)。

用于确定的级别数的逻辑包含在`LevelManager`类。 实例时`LevelManager`（使用单独模式），构造`DetermineAvailbleLevels`调用方法：


```csharp
private void DetermineAvailableLevels()
{
    // This game relies on levels being named "levelx.tmx" where x is an integer beginning with
    // 1. We have to rely on MonoGame's TitleContainer which doesn't give us a GetFiles method - we simply
    // have to check if a file exists, and if we get an exception on the call then we know the file doesn't
    // exist. 
    NumberOfLevels = 0;
    while (true)
    {
        bool fileExists = false;
        try
        {
            using(var stream = TitleContainer.OpenStream("Content/levels/level" + NumberOfLevels + ".tmx"))
            {
            }
            // if we got here then the file exists!
            fileExists = true;
        }
        catch
        {
            // do nothing, fileExists will remain false
        }
        if (!fileExists)
        {
            break;
        }
        else
        {
            NumberOfLevels++;
        }
    }
}
```

CocosSharp 不提供跨平台方法检测文件是否存在，因此我们需要依赖于`TitleContainer`类尝试打开的流。 如果打开流的代码引发异常，则该文件不存在且 while 循环中断。 在循环完成后，`NumberOfLevels`属性报告多少有效级别为项目的一部分。

`LevelSelectScene`类使用`LevelManager.NumberOfLevels`来确定要在中创建的按钮个数`CreateLevelButtons`方法：


```csharp
private void CreateLevelButtons()
{
    const int buttonsPerPage = 6;
    int levelIndex0Based = buttonsPerPage * pageNumber;
    int maxLevelExclusive = System.Math.Min (levelIndex0Based + 6, LevelManager.Self.NumberOfLevels);
    int buttonIndex = 0;
    float centerX = this.ContentSize.Center.X;
    const float topRowOffsetFromCenter = 16;
    float topRowY = this.ContentSize.Center.Y + topRowOffsetFromCenter;
    for (int i = levelIndex0Based; i < maxLevelExclusive; i++)
    {
        ...
    }
}
```

`NumberOflevels`属性用于确定应创建哪些按钮。 此代码会将用户当前正在查看并只创建每页的六个按钮的最多的页。 按钮单击时，实例调用`HandleButtonClicked`方法：


```csharp
private void HandleButtonClicked(object sender, EventArgs args)
{
    // levelNumber is 1-based, so subtract 1:
    var levelIndex = (sender as Button).LevelNumber - 1;
    LevelManager.Self.CurrentLevel = levelIndex;
    CoinTime.GameAppDelegate.GoToGameScene ();
}
```

此方法将分配`CurrentLevel`属性由使用`GameScene`加载级别时。 设置后`CurrentLevel`、`GoToGameScene`引发方法时，切换从场景`LevelSelectScene`到`GameScene`。

`GameScene`构造函数调用`GoToLevel`，这将执行级别加载逻辑：


```csharp
private void GoToLevel(int levelNumber)
{
    LoadLevel (levelNumber);
    CreateCollision();
    ProcessTileProperties ();
    touchScreen = new TouchScreenInput(gameplayLayer);
    secondsLeft = secondsPerLevel;
}
```

接下来我们将看一看中调用方法`GoToLevel`。


### <a name="loadlevel"></a>LoadLevel

`LoadLevel`方法负责加载.tmx 文件并将其添加到`GameScene`。 此方法不会创建任何交互的对象，例如冲突或实体 – 它只需创建的视觉对象的级别，也称为*环境*。


```csharp
private void LoadLevel(int levelNumber)
{
    currentLevel = new CCTileMap ("level" + levelNumber + ".tmx");
    currentLevel.Antialiased = false;
    backgroundLayer = currentLevel.LayerNamed ("Background");
    // CCTileMap is a CCLayer, so we'll just add it under all entities
    this.AddChild (currentLevel);
    // put the game layer after
    this.RemoveChild(gameplayLayer);
    this.AddChild(gameplayLayer);
    this.RemoveChild (hudLayer);
    this.AddChild (hudLayer);
}
```

`CCTileMap`构造函数采用一个文件名，它创建使用传递给级别号`LoadLevel`。 有关详细信息创建和使用`CCTileMap`实例，请参阅[使用平铺 CocosSharp 指南](~/graphics-games/cocossharp/tiled.md)。

目前，CocosSharp 不允许重新排序的层不删除并重新将它们添加到其父`CCScene`(即`GameScene`在这种情况下)，因此该方法的最后几行所需重新排序图层。


### <a name="createcollision"></a>CreateCollision

`CreateCollision`方法构造`LevelCollision`实例用于执行*实体冲突*之间播放器和环境。


```csharp
private void CreateCollision()
{
    levelCollision = new LevelCollision();
    levelCollision.PopulateFrom(currentLevel);
}
```

此冲突，播放器将贯穿到级别而无需游戏将无法播放。 实心冲突允许播放机引导到地上和阻止从遍历通过墙壁或通过上限向上跳转播放器。

可以使用任何其他代码 – 仅修改平铺文件添加抛硬币时间中的冲突。 


### <a name="processtileproperties"></a>ProcessTileProperties

一旦加载一个级别并创建此冲突，`ProcessTileProperties`调用以执行基于磁贴属性的逻辑。 抛硬币时间包括`PropertyLocation`用于定义属性和这些属性与磁贴的坐标的结构：


```csharp
public struct PropertyLocation
{
    public CCTileMapLayer Layer;
    public CCTileMapCoordinates TileCoordinates;
    public float WorldX;
    public float WorldY;
    public Dictionary<string, string> Properties;
}
```

此结构用于构造创建实体实例并删除不必要的磁贴中`ProcessTileProperties`方法：


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```

Foreach 循环计算每个磁贴属性，如果密钥是检查`EntityType`或`RemoveMe`。 `EntityType` 指示应创建实体实例。 `RemoveMe` 指示该磁贴应在运行时完全删除。

如果具有的属性`EntityType`找到密钥，则`TryCreateEntity`调用时，尝试创建实体使用属性匹配`EntityType`密钥：


```csharp
private bool TryCreateEntity(string entityType, float worldX, float worldY)
{
    CCNode entityAsNode = null;
    switch (entityType)
    {
    case "Player":
        player = new Player ();
        entityAsNode = player;
        break;
    case "Coin":
        Coin coin = new Coin ();
        entityAsNode = coin;
        coins.Add (coin);
        break;
    case "Door":
        door = new Door ();
        entityAsNode = door;
        break;
    case "Spikes":
        var spikes = new Spikes ();
        this.damageDealers.Add (spikes);
        entityAsNode = spikes;
        break;
    case "Enemy":
        var enemy = new Enemy ();
        this.damageDealers.Add (enemy);
        this.enemies.Add (enemy);
        entityAsNode = enemy;
        break;
    }
    if(entityAsNode != null)
    {
        entityAsNode.PositionX = worldX;
        entityAsNode.PositionY = worldY;
        gameplayLayer.AddChild (entityAsNode);
    }
    return entityAsNode != null;
}
```


## <a name="adding-new-entities"></a>添加新实体

抛硬币时间将用于其游戏对象的实体模式 (这一点在[CocosSharp 中的实体指导](~/graphics-games/cocossharp/entities.md))。 所有实体都继承自`CCNode`，这意味着它们都可添加作为的子级`gameplayLayer`。

每个实体类型也引用直接通过列表或单个实例。 例如，`Player`引用`player`字段和所有`Coin`中引用实例`coins`列表。 保留的实体的直接引用 (而不是引用通过`gameLayer.Children`列表) 使其访问更易读这些实体，并消除产生高昂的代价类型强制转换的代码。

现有的代码提供了多种实体类型作为如何创建新的实体的示例。 以下步骤可以用于创建新实体：


### <a name="1---define-a-new-class-using-the-entity-pattern"></a>1-定义一个新的类使用的实体模式

创建实体的唯一要求是创建一个类继承自`CCNode`。 大多数实体都有一些视觉对象，如`CCSprite`，其应被添加为其构造函数中的实体的子级。

CoinTime 提供`AnimatedSpriteEntity`简化的动画实体创建的类。 将在将更详细地介绍动画[进行动画处理实体部分](#animated-entities)。


### <a name="2--add-a-new-entry-to-the-trycreateentity-switch-statement"></a>2-添加新条目 TryCreateEntity switch 语句

新的实体的实例应在实例化`TryCreateEntity`。 如果实体要求每个帧逻辑，如冲突、 AI 或读取输入，则`GameScene`需要保持对对象的引用。 如果需要多个实例 (如`Coin`或`Enemy`实例)，然后新`List`应添加到`GameScene`类。


### <a name="3--modify-tile-properties-for-the-new-entity"></a>3 – 修改新的实体的磁贴属性

一旦代码支持创建新的实体，新的实体将需要添加到 tileset。 可以通过打开任何级别编辑 tileset`.tmx`文件。 

与在.tmx 分开存储 tileset **mastersheet.tsx**文件，因此它在可以对其进行编辑之前必须导入：

![](cointime-images/image11.png "Tileset 存储分开.tsx 文件中，因此它在可以对其进行编辑之前必须导入")

一旦导入，所选的磁贴上的属性是可编辑，并且可以添加 EntityType:

![](cointime-images/image12.png "一旦导入，所选的磁贴上的属性是可编辑，并可以添加 EntityType")

创建属性后，可以设置其值以匹配新`case`中`TryCreateEntity`:

![](cointime-images/image13.png "创建属性后，可以设置其值以匹配 TryCreateEntity 中的新用例")

在更改 tileset 后，必须导出 – 这使所做的更改可用于所有其他级别：

![](cointime-images/image14.png "在更改 tileset 后，必须导出")

Tileset 应覆盖现有**mastersheet.tsx** tileset:

![](cointime-images/image15.png "他 tileset 应覆盖现有的 mastersheet.tsx tileset")


## <a name="entity-tile-removal"></a>实体磁贴删除

当磁贴映射加载到一个游戏时，单个的磁贴将是静态对象。 由于实体需要自定义行为，如移动，抛硬币时代码中创建实体时删除磁贴。

`ProcessTileProperties` 包括逻辑，用于删除磁贴创建实体使用`RemoveTile`方法：


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            ...
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        ...
    }
}
```

磁贴此自动删除已足够的实体的在 tileset，如硬币和敌人中占用只有一个磁贴。 更大的实体要求额外的逻辑和属性。

门需要两个磁贴完全绘制：

![](cointime-images/image16.png "门需要两个磁贴完全绘制")

门中的底部磁贴包含用于创建实体的属性 (**EntityType**设置为**门**):

![](cointime-images/image17.png "门中的底部磁贴包含用于创建 EntityType 设置为门实体的属性")

因为门中底部磁贴的只删除创建门实例时，才需要其他逻辑删除在运行时的顶部磁贴。 顶部的磁贴有**RemoveMe**属性设置为**true**:

![](cointime-images/image18.png "顶部的磁贴有的 RemoveMe 属性设置为 true")



此属性用于删除中的磁贴`ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        ...
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```


## <a name="entity-offsets"></a>实体偏移量

从磁贴创建实体位于通过协调使用磁贴的中心的实体的中心。 更大的实体，如`Door`，使用其他属性和逻辑来正确放置。 

底部门磁贴，定义`Door`实体放置指定**正文**值为 4。 如果没有此属性，`Door`实例置于该磁贴的中心：

![](cointime-images/image19.png "如果没有此属性，在该磁贴的中心放置门实例")

 

此问题得以更正通过应用**正文**中的值`ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            ...
        }
...
    }
}
```


## <a name="animated-entities"></a>动画的实体

抛硬币时间包括多个动画的实体。 `Player`和`Enemy`实体播放审核动画和`Door`实体所收集所有硬币后充当打开动画。


### <a name="achx-files"></a>.achx 文件

.Achx 文件中定义了抛硬币时间动画。 每个动画定义之间`AnimationChain`标记，如以下中定义的动画中所示**propanimations.achx**:


```xml
<AnimationChain>
  <Name>Spikes</Name>
  <ColorKey>0</ColorKey>
  <Frame>
    <FlipHorizontal>false</FlipHorizontal>
    <FlipVertical>false</FlipVertical>
    <TextureName>..\images\mastersheet.png</TextureName>
    <FrameLength>0.1</FrameLength>
    <LeftCoordinate>1152</LeftCoordinate>
    <RightCoordinate>1168</RightCoordinate>
    <TopCoordinate>128</TopCoordinate>
    <BottomCoordinate>144</BottomCoordinate>
    <RelativeX>0</RelativeX>
    <RelativeY>0</RelativeY>
  </Frame>
</AnimationChain> 
```

此动画仅包含单个框架，从而导致显示静态图像的峰值实体。 实体可以使用.achx 文件，不管它们显示单个或多帧的动画。 其他框架可以添加到.achx 文件中，无需在代码中的任何更改。 

帧定义中显示的图像`TextureName`参数，而在中显示的坐标`LeftCoordinate`， `RightCoordinate`， `TopCoordinate`，和`BottomCoordinate`标记。 这些表示正在使用 – 的图像中的动画的帧的像素坐标**mastersheet.png**在这种情况下。

![](cointime-images/image20.png "这些表示图像中的动画帧的像素坐标")

`FrameLength`属性定义的应显示动画中的帧的秒数。 单帧动画忽略此值。

按抛硬币时间忽略的.achx 文件中的所有其他 AnimationChain 属性。


### <a name="animatedspriteentity"></a>AnimatedSpriteEntity

动画逻辑包含在`AnimatedSpriteEntity`类，该类用作基类的类，有关在中使用大多数实体`GameScene`。 它提供以下功能：

 - 加载的`.achx`文件
 - 加载动画的动画缓存
 - 用于显示动画 CCSprite 实例
 - 更改当前帧的逻辑

峰值构造函数提供了如何加载和使用动画的简单示例：


```csharp
public Spikes ()
{
    LoadAnimations ("Content/animations/propanimations.achx");
    CurrentAnimation = animations [0];
}
```

**PropAnimations.achx**仅包含一个动画，因此构造函数的索引来访问此动画。 如果.achx 文件包含多个动画，则中所示，可以按名称，引用动画`Enemy`构造函数：


```csharp
walkLeftAnimation = animations.Find (item => item.Name == "WalkLeft");
walkRightAnimation = animations.Find (item => item.Name == "WalkRight");
```


## <a name="summary"></a>总结

本指南介绍抛硬币时间的实现详细信息。 抛硬币时间创建为完整的游戏，但也可以轻松地修改和扩展的项目。 添加新的级别，以及创建新的实体来进一步了解如何实现抛硬币时间鼓励花费时间修改为级别，将读取器。

## <a name="related-links"></a>相关的链接

- [游戏项目 （示例）](https://developer.xamarin.com/samples/mobile/CoinTime/)
