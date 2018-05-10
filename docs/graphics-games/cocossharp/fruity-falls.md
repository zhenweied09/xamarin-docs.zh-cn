---
title: Fruity 回退游戏的详细信息
description: 本指南介绍 Fruity 回退游戏中，其中介绍了常见 CocosSharp 和游戏开发概念，如物理、 内容管理、 游戏状态和游戏的设计。
ms.prod: xamarin
ms.assetid: A5664930-F9F0-4A08-965D-26EF266FED24
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 19b57d41fdf8aa4f8a749cf4979755161a0e7e51
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="fruity-falls-game-details"></a>Fruity 回退游戏的详细信息

_本指南介绍 Fruity 回退游戏中，其中介绍了常见 CocosSharp 和游戏开发概念，如物理、 内容管理、 游戏状态和游戏的设计。_

Fruity 回退是简单的、 基于物理的游戏的玩家为彩色不同存储桶，若要获得积分排序红色和黄色水果的生长。 游戏旨在尽可能获得尽可能多的点，而不让水果的生长放到错误中，结束游戏。

![](fruity-falls-images/image1.png "游戏的目标是尽可能获得尽可能多的点，而不让水果的生长放到错误中，结束游戏")

Fruity 回退扩展中介绍的概念[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)通过添加以下：

 - 内容中的 Png 窗体
 - 高级的物理
 - 游戏状态 （场景之间的转换）
 - 能够优化通过单个类中包含的变量的游戏系数
 - 内置 visual 调试支持
 - 代码组织使用游戏实体
 - 游戏设计侧重于有趣和重播值

虽然[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)侧重于介绍了核心 CocosSharp 概念，Fruity 回退演示如何以使其在一起完成的游戏产品。 由于本指南引用 BouncingGame，因此读取器应首先熟悉[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)之前阅读此指南。

此指南介绍了如何实现和 Fruity 回退的设计，以提供见解，从而帮助你做出你自己的游戏。 它涵盖以下主题：


- [GameController 类](#gamecontroller-class)
- [游戏实体](#game-entities)
- [水果的生长图形](#fruit-graphics)
- [物理](#physics)
- [游戏内容](#game-content)
- [GameCoefficients](#gamecoefficients)


## <a name="gamecontroller-class"></a>GameController 类

Fruity 位于 PCL 项目中包括`GameController`类负责实例化游戏和的场景之间移动。 此类的 iOS 和 Android 的项目使用，以消除重复代码。

中包含的代码`Initialize`方法类似于中的代码是`Initialize`方法中的不变的 CocosSharp 模板，但它包含大量的修改。

默认情况下，`GameView.ContentManager.SearchPaths`取决于设备的分辨率。 Fruity 回退如下所述在更多详细信息，使用相同的内容，而不考虑设备解析，因此`Initialize`方法将添加`Images`路径 （与不备份子文件夹） `SearchPaths`:


```csharp
contentSearchPaths.Add ("Images");
```

新 CocosSharp 模板包括类从继承`CCLayer`，并暗示游戏的视觉对象和逻辑应添加到此类。 相反，Fruity 回退使用多个`CCLayer`实例到控件绘制顺序。 这些`CCLayer`实例都包含在`GameView`类，该类继承自`CCScene`。 Fruity 回退还包括多个后台，第一个是`TitleScene`。 因此，`Initialize`方法实例化`TitleScene`实例传递给的`RunWithScene`:


```csharp
var scene = new TitleScene (GameView);
GameView.Director.RunWithScene (scene);
```

Fruity 回退的内容创建为低分标率像素艺术作品出于美观原因。 `GameView.DesignResolution`以便游戏仅 384 个单位宽、 512 安装设置：


```csharp
// We use a lower-resolution display to get a pixellated appearance
int width = 384;
int height = 512;
GameView.DesignResolution = new CCSizeI (width, height); 
```

最后，`GameController`类提供一个静态方法，它可以由任何调用`CCGameScene`转换为另一种`CCScene`。 此方法用于之间移动`TitleScene`和`GameScene`。


## <a name="game-entities"></a>游戏实体

Fruity 回退使用实体模式大部分游戏的对象。 在找不到此模式的详细的说明[CocosSharp 中的实体指导](~/graphics-games/cocossharp/entities.md)。

可以在实体文件夹中找到的实体实施的游戏对象**FruityFalls.Common**项目：

![](fruity-falls-images/image2.png "FruityFalls.Common 项目中的实体文件夹")

实体是继承的对象`CCNode`，并且可能具有视觉对象、 冲突，以及每个帧行为。

`Fruit`对象表示一个典型的 CocosSharp 实体： 它包含视觉对象、 冲突和每个帧逻辑。 其构造函数负责初始化水果的生长：


```csharp
public Fruit ()
{
    CreateFruitGraphic ();
    if (GameCoefficients.ShowCollisionAreas)
    {
        CreateDebugGraphic ();
    }
    CreateCollision ();
    CreateExtraPointsLabel ();
    Acceleration.Y = GameCoefficients.FruitGravity;
}
```


### <a name="fruit-graphics"></a>水果的生长图形

`CreateFruitGraphic`方法创建`CCSprite`实例，并将其添加到`Fruit`。 `IsAntialiased`属性设置为 false，以使具有像素化外观的游戏。 此值设置为 false 上所有`CCSprite`和`CCLabel`整个游戏的实例：


```csharp
private void CreateFruitGraphic()
{
    graphic = new CCSprite ("cherry.png");
    graphic.IsAntialiased = false;
    this.AddChild (graphic);
}
```

每当播放器接触`Fruit`实例与`Paddle`，该水果的生长的点值增加 1。 这对有经验的扮演者能够同时处理的额外点水果的生长提供额外的质询。 使用显示水果的生长的点值`extraPointsLabel`实例。

`CreateExtraPointsLabel`方法创建`CCLabel`实例，并将其添加到`Fruit`:


```csharp
private void CreateExtraPointsLabel()
{
    extraPointsLabel = new CCLabel("", "Arial", 12, CCLabelFormat.SystemFont);
    extraPointsLabel.IsAntialiased = false;
    extraPointsLabel.Color = CCColor3B.Black;
    this.AddChild(extraPointsLabel);
}
```

Fruity 回退包括两种不同类型的水果的生长 – 樱桃和柠檬。 `CreateFruitGraphic`分配默认视觉对象，但水果的生长视觉对象，可以通过更改`FruitColor`属性，又会调用`UpdateGraphics`:


```csharp
private void UpdateGraphics()
{
if (GameCoefficients.ShowCollisionAreas)
    {
        debugGrahic.Clear ();
        const float borderWidth = 4;
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius,
            CCColor4B.Black);
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius - borderWidth,
            fruitColor.ToCCColor ());
    }
    if (this.FruitColor == FruitColor.Yellow)
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("lemon.png");
        extraPointsLabel.Color = CCColor3B.Black;
        extraPointsLabel.PositionY = 0;
    }
    else
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("cherry.png");
        extraPointsLabel.Color = CCColor3B.White;
        extraPointsLabel.PositionY = -8;
    }
}
```

中的第一个 if 语句`UpdateGraphics`更新用于可视化冲突区域的调试图形。 最终发布版本游戏的进行，但可以在用于调试物理开发过程中保留之前，这些视觉对象已关闭。 第二个部件发生更改`graphic.Texture`属性通过调用`CCTextureCache.SharedTextureCache.AddImage`。 此方法通过文件名称提供为纹理的访问权限。 此方法的详细信息可在[纹理缓存指南](~/graphics-games/cocossharp/texture-cache.md)。

`extraPointsLabel`颜色调整以保证对比度的水果的生长图像，并将其`PositionY`值调整到中心`CCLabel`上水果的生长的`CCSprite`:

![](fruity-falls-images/image3.png "ExtraPointsLabel 颜色调整以保证对比度的水果的生长图像，且其 PositionY 值调整到中心上水果 CCSprite CCLabel")

![](fruity-falls-images/image4.png "ExtraPointsLabel 颜色调整以保证对比度的水果的生长图像，且其 PositionY 值调整到中心上水果 CCSprite CCLabel")


### <a name="collision"></a>冲突

Fruity 回退实现几何图形文件夹中使用对象的自定义冲突解决方案：

![](fruity-falls-images/image5.png "Fruity 回退实现几何图形文件夹中使用对象的自定义冲突解决方案")

使用实现 Fruity 回退中的冲突`Circle`或`Polygon`对象，通常与正在添加的实体子为这两种类型之一。 例如，`Fruit`对象具有`Circle`调用`Collision`这将在其初始化其`CreateCollision`方法：


```csharp
private void CreateCollision()
{
    Collision = new Circle ();
    Collision.Radius = GameCoefficients.FruitRadius;
    this.AddChild (Collision);
}
```

请注意，`Circle`类继承自`CCNode`类，因此它可以作为子级添加，到我们的游戏中的实体：


```csharp
public class Circle : CCNode
{
    ...
}
```

`Polygon` 创建是类似于`Circle`创建中, 所示`Paddle`类：


```csharp
private void CreateCollision()
{
    Polygon = Polygon.CreateRectangle(width, height);
    this.AddChild (Polygon);
}
```

涵盖冲突逻辑[本指南后面](#collision)。


## <a name="physics"></a>物理

在 Fruity 回退物理可以分为两个类别： 移动和冲突。 


### <a name="movement-using-velocity-and-acceleration"></a>移动使用速度和加速

Fruity 回退使用`Velocity`和`Acceleration`值来控制其实体，类似于移动[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)。 实体在一个名为方法中实现其移动逻辑`Activity`，其每帧一次调用。 例如，我们可以看到的实现中的移动`Fruit`类的`Activity`方法：

```csharp
public void Activity(float frameTimeInSeconds)
{
    timeUntilExtraPointsCanBeAdded -= frameTimeInSeconds;
    
    // linear approximation:
    this.Velocity += Acceleration * frameTimeInSeconds;

    // This is a linear approximation to drag. It's used to
    // keep the object from falling too fast, and eventually
    // to slow its horizontal movement. This makes the game easier
    this.Velocity -= Velocity * GameCoefficients.FruitDrag * frameTimeInSeconds;
    
    this.Position += Velocity * frameTimeInSeconds;
}
```
`Fruit`对象都具有独特的拖动其实现 – 这将减缓的速度如何快速水果的生长与值是移动。 此实现的拖动提供*终端速度*，即水果的生长实例可能处于的最大速度。 拖动还降低水果，从而使游戏略有易于播放的水平移动。

`Paddle`对象也适用`Velocity`中其`Activity`方法，但其`Velocity`使用计算`desiredLocation`值：


```csharp
public void Activity(float frameTimeInSeconds)
{
    // This code will cause the cursor to lag behind the touch point
    // Increasing this value reduces how far the paddle lags
    // behind the player’s finger. 
    const float velocityCoefficient = 4;
    // Get the velocity from current location and touch location
    Velocity = (desiredLocation - this.Position) * velocityCoefficient;
    this.Position += Velocity * frameTimeInSeconds;
    ...
}
```

通常情况下，使用该对话框的游戏`Velocity`若要不直接控制其对象执行的位置设置实体位置至少不在初始化之后。 而不是直接设置`Paddle`位置，`Paddle.HandleInput`方法分配`desiredLocation`值：

```csharp
public void HandleInput(CCPoint touchPoint)
{
    desiredLocation = touchPoint;
}
```

### <a name="collision"></a>冲突

Fruity 回退实现水果的生长和其他 collidable 对象之间的不完全现实冲突如`Paddle`和`GameScene.Splitter`。 为了帮助调试冲突，Fruity 回退冲突区域可以让用户看到通过更改`GameCoefficients.ShowDebugInfo`中`GameCoefficients.cs`文件：


```csharp
public static class GameCoefficients
{
    ... 
    public const bool ShowCollisionAreas = true;
    ...
}
```

此值设置为`true`可冲突区域呈现：  

![](fruity-falls-images/image6.png "将此值设置为 true 可冲突区域呈现")

在开始冲突逻辑`GameScene.Activity`方法：


```csharp
private void Activity(float frameTimeInSeconds)
{
    if (hasGameEnded == false)
    {
        paddle.Activity(frameTimeInSeconds);
        foreach (var fruit in fruitList)
        {
            fruit.Activity(frameTimeInSeconds);
        }
        spawner.Activity(frameTimeInSeconds);
        DebugActivity();
        PerformCollision();
    }
}
```

`PerformCollision` 处理冲突所有`Fruit`与其他对象的实例： 


```csharp
private void PerformCollision()
{
    // reverse for loop since fruit may be destroyed:
    for(int i = fruitList.Count - 1; i > -1; i--)
    {
        var fruit = fruitList[i];
        FruitVsPaddle(fruit);
        FruitPolygonCollision(fruit, splitter.Polygon, CCPoint.Zero);
        FruitVsBorders(fruit);
        FruitVsBins(fruit);
    }
}
```

#### <a name="fruitvsborders"></a>FruitVsBorders

`FruitVsBorders` 冲突的冲突，而不是依赖于另一个类中包含的逻辑执行自己的逻辑。 存在这种差异是因为水果的生长和屏幕的边框之间冲突不是完全实心 – 可能水果的生长小心刀片移动推送关闭屏幕边缘。 显示与刀片，命中条件在屏幕将反弹水果的生长，但如果玩家渐变推送水果的生长会移动超过的边缘和关闭屏幕：


```csharp
private void FruitVsBorders(Fruit fruit)
{
    if(fruit.PositionX - fruit.Radius < 0 && fruit.Velocity.X < 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionX + fruit.Radius > gameplayLayer.ContentSize.Width && fruit.Velocity.X > 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionY + fruit.Radius > gameplayLayer.ContentSize.Height && fruit.Velocity.Y > 0)
    {
        fruit.Velocity.Y *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
}
```

#### <a name="fruitvsbins"></a>FruitVsBins

`FruitVsBins`方法负责检查是否任何水果的生长已降到两个箱之一。如果是这样，然后玩家获得点 （如果水果的生长/bin 颜色匹配） 或游戏就会结束 （如果颜色不匹配）：


```csharp
private void FruitVsBins(Fruit fruit)
{
    foreach(var bin in fruitBins)
    {
        if(bin.Polygon.CollideAgainst(fruit.Collision))
        {
            if(bin.FruitColor == fruit.FruitColor)
            {
                // award points:
                score += 1 + fruit.ExtraPointValue;
                scoreText.Score = score;
                Destroy(fruit);
            }
            else
            {
                EndGame();
            }
            break;
        }
    }
}
```

#### <a name="fruitvspaddle-and-fruitpolygoncollision"></a>FruitVsPaddle 和 FruitPolygonCollision

与刀片和与拆分 （分隔两个箱的区域） 水果的生长水果的生长冲突都依赖`FruitPolygonCollision`方法。 此方法具有三个部分：

1. 测试是否对象发生冲突
1. 移动的对象 （只需在 Fruity 回退水果），使它们不再重叠
1. 调整对象 （只需在 Fruity 回退水果） 的速度来模拟的弹跳下面的代码演示上面所做的点：


```csharp
private static bool FruitPolygonCollision(Fruit fruit, Polygon polygon, CCPoint polygonVelocity)
{
    // Test whether the fruit collides
    bool didCollide = polygon.CollideAgainst(fruit.Collision);
    if (didCollide)
    {
        var circle = fruit.Collision;
        // Get the separation vector to reposition the fruit so it doesn't overlap the polygon
        var separation = CollisionResponse.GetSeparationVector(circle, polygon);
        fruit.Position += separation;
        // Adjust the fruit's Velocity to make it bounce:
        var normal = separation;
        normal.Normalize();
        fruit.Velocity = CollisionResponse.ApplyBounce(
            fruit.Velocity, 
            polygonVelocity, 
            normal, 
            GameCoefficients.FruitCollisionElasticity);
    }
    return didCollide;
}
```

Fruity 回退冲突响应是单侧 – 调整仅水果的生长的速度和位置。 值得注意的其他游戏，可能有冲突，这会影响所涉及的例如将博尔德或发生故障的两个汽车推送到每个其他字符这两个对象。

中包含的冲突逻辑背后的数学函数`Polygon`和`CollisionResponse`类已超出本指南的作用域，但作为编写，这些方法可以用于许多类型的游戏。 多边形和`CollisionResponse`类甚至支持非矩形的凸多边形，因此，此代码可以用于许多类型的游戏。

 


## <a name="game-content"></a>游戏内容

在 Fruity 回退画立即将游戏与 BouncingGame 区分开来。 虽然游戏设计类似，播放器将立即看到这两个游戏的显示方式存在差异。 游戏玩家通常决定是否由其视觉对象玩游戏。 因此，它是非常重要的开发人员投入资源进行具有视觉冲击力游戏。

使用以下目标创建 Fruity 回退的技巧：

 - 一致的主题
 - 主要侧重于只有一个字符 – Xamarin monkey
 - 亮色创建放宽愉快体验
 - 对比之间前景色和背景，以便在玩游戏对象可轻松直观地跟踪
 - 能够创建无资源密集型动画的简单视觉效果


### <a name="content-location"></a>内容位置

Fruity 回退包括其所有的内容在 Android 项目中的映像文件夹中：

![](fruity-falls-images/image7.png "Fruity 回退包括其所有的内容中的 Android 项目中的映像文件夹")

这些相同的文件会链接中的 iOS 项目，以避免重复，并且因此文件更改影响这两个项目：

![](fruity-falls-images/image8.png "这些相同的文件会链接中的 iOS 项目，以避免重复，并且因此文件更改影响这两个项目")

值得注意的内容未包含在**Ld**或**Hd**文件夹，它是默认 CocosSharp 模板的一部分。 **Ld**和**Hd**文件夹旨在用于提供两个集的内容 — — 一个用于较低分辨率设备，如手机和一个用于更高分辨率设备，例如平板电脑的游戏。 Fruity 回退画有意创建具有像素化美观，因此它不需要针对不同屏幕大小提供内容。 因此， **Ld**和**Hd**文件夹已完全删除从项目。


### <a name="gamescene-layering"></a>GameScene 分层

本指南中，在前面部分中所述`GameScene`负责所有游戏对象实例化、 定位和间对象逻辑 （例如冲突）。 所有对象都添加到其中一个四个`CCLayer`实例：


```csharp
CCLayer backgroundLayer;
CCLayer gameplayLayer;
CCLayer foregroundLayer;
CCLayer hudLayer;
```

在中创建以下四层`CreateLayers`方法。 请注意，将它们添加到`GameScene`后到前的顺序：


```csharp
private void CreateLayers()
{
    backgroundLayer = new CCLayer();
    this.AddLayer(backgroundLayer);
    gameplayLayer = new CCLayer();
    this.AddLayer(gameplayLayer);
    foregroundLayer = new CCLayer();
    this.AddLayer(foregroundLayer);
    hudLayer = new CCLayer();
    this.AddLayer(hudLayer);
}
```

层创建后，所有视觉对象添加到使用的层`AddChild`方法，如中所示`CreateBackground`方法：


```csharp
private void CreateBackground()
{
    var background = new CCSprite("background.png");
    background.AnchorPoint = new CCPoint(0, 0);
    background.IsAntialiased = false;
    backgroundLayer.AddChild(background);
}
```


### <a name="vine-entity"></a>藤蔓式实体

`Vine`实体唯一用于内容-它具有不会影响上玩游戏。 它构成二十`CCSprite`实例，以确保藤蔓式始终到达屏幕顶部的试用版和错误由所选数字：


```csharp
public Vine ()
{
    const int numberOfVinesToAdd = 20;
    for (int i = 0; i < numberOfVinesToAdd; i++)
    {
        var graphic = new CCSprite ("vine.png");
        graphic.AnchorPoint = new CCPoint(.5f, 0);
        graphic.PositionY = i * graphic.ContentSize.Height;
        this.AddChild (graphic);
    }
}
```

第一个`CCSprite`位于使其下边缘匹配藤蔓式的位置。 这通过将 AnchorPoint 设置为实现`new CCPoint(.5f, 0)`。 `AnchorPoint`属性使用*规范化坐标*介于 0 和 1 而不考虑的大小之间的范围`CCSprite`:

![](fruity-falls-images/image9.png "AnchorPoint 属性使用规范化的坐标介于 0 和 1 而不考虑 CCSprite 的大小之间的范围")

后续藤蔓式动画层中的放置方式使用`graphic.ContentSize.Height`值，以像素为单位返回图像的高度。 下图显示藤蔓式图形的平铺方式向上：

![](fruity-falls-images/image10.png "此图显示了如何藤蔓式图磁贴向上")

自的源的`Vine`实体位于底部藤蔓式，然后定位它非常简单，如中所示`Paddle.CreateVines`方法：


```csharp
private void CreateVines()
{
    // Increasing this value moves the vines closer to the 
    // center of the paddle.
    const int vineDistanceFromEdge = 4;
    leftVine = new Vine ();
    var leftEdge = -width / 2.0f;
    leftVine.PositionX = leftEdge + vineDistanceFromEdge;
    this.AddChild (leftVine);
    rightVine = new Vine ();
    var rightEdge = width / 2.0f;
    rightVine.PositionX = rightEdge - vineDistanceFromEdge;
    this.AddChild (rightVine);
}
```

在中实例藤蔓式`Paddle`实体还具有有趣旋转行为。 由于`Paddle`作为一个整体的实体旋转播放器输入 （它本指南涵盖下面更详细地），根据`Vine`实例通过此旋转也会受到影响。 但是，轮换`Vine`实例以及`Paddle`产生不良的视觉效果，如下面的动画中所示：

![](fruity-falls-images/image11.gif "下面的动画中所示，但是，就轮换以及刀片藤蔓式实例产生不良的视觉效果")

抵消`Paddle`旋转`leftVine`和`rightVine`实例是否旋转刀片中, 所示的相反方向`Paddle.Activity`方法：


```csharp
public void Activity(float frameTimeInSeconds)
{
    ...
    // This value adds a slight amount of rotation
    // to the vine. Having a small amount of tilt looks nice.
    float vineAngle = this.Velocity.X / 100.0f;
    leftVine.Rotation = -this.Rotation + vineAngle;
    rightVine.Rotation = -this.Rotation + vineAngle;
}
```

请注意，返回到通过藤蔓式添加少量的旋转`vineAngle`系数。 可以更改此值以调整 vines 旋转的量。


## <a name="gamecoefficients"></a>GameCoefficients

每个很好的游戏是迭代的产品，因此 Fruity 回退包括一个名为类`GameCoefficients`它控制如何播放游戏。 此类包含在整个控件物理布局，生成，或评分到游戏表现力变量。

例如，水果的生长物理受以下变量：


```csharp
public static class GameCoefficients
{
    ...
    
    // The strength of the gravity. Making this a 
    // smaller (bigger negative) number will make the
    // fruit fall faster. A larger (smaller negative) number
    // will make the fruit more floaty.
    public const float FruitGravity = -60;
    // The impact of "air drag" on the fruit, which gives
    // the fruit terminal velocity (max falling speed) and slows
    // the fruit's horizontal movement (makes the game easier)
    public const float FruitDrag = .1f;
    // Controls fruit collision bouncyness. A value of 1
    // means no momentum is lost. A value of 0 means all
    // momentum is lost. Values greater than 1 create a spring-like effect
    public const float FruitCollisionElasticity = .5f;
    
    ...
}
```

顾名思义，这些变量可以用于调整回退，如何水平的速度如何水果的生长移动通过时间和刀片反弹降低。

存储在代码或数据文件 （例如 XML) 的游戏系数可以是对游戏的设计器也不是程序员使用的团队尤其有用。

`GameCoefficients`类还包括打开例如信息或冲突的区域中生成调试信息的值：


```csharp
public static class GameCoefficients
{
    ...
    // This controls whether debug information is displayed on screen.
    public const bool ShowDebugInfo = false;
    public const bool ShowCollisionAreas = false;
    ...
}
```


## <a name="conclusion"></a>结束语

本指南介绍了 Fruity 回退游戏。 它介绍概念包括内容、 物理和游戏状态管理。

## <a name="related-links"></a>相关的链接

- [CocosSharp API 文档](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [已完成的项目 （示例）](https://developer.xamarin.com/samples/mobile/FruityFalls/)
