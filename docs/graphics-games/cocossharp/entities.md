---
title: "CocosSharp 中的实体"
description: "实体模式是一种强大的方法来组织游戏代码。 它可以改善可读性，使代码更易于维护，并可利用内置的父/子功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1D3261CE-AC96-4296-8A53-A76A42B927A8
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: fe722ce75f0322ab60bb6fd967ff2c498b2e7b20
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="entities-in-cocossharp"></a>CocosSharp 中的实体

_实体模式是一种强大的方法来组织游戏代码。它可以改善可读性，使代码更易于维护，并可利用内置的父/子功能。_

实体模式可以提高开发人员的工作与 CocosSharp 通过改进的代码的组织。 本演练将显示如何创建两个实体 – 发货实体和项目符号实体的动手示例。 这些实体将是自包含的对象，这意味着，一次实例化它们将自动呈现，并将执行移动逻辑根据它们的类型。 

本指南涵盖以下主题：

 - 介绍游戏实体
 - 与特定实体类型的常规
 - 项目设置
 - 创建实体类
 - 添加到的实体实例 `GameLayer`
 - 对实体中的逻辑的作出反应 `GameLayer`

完成的游戏将如下所示：

![](entities-images/image1.png "完成的游戏将如下所示")


# <a name="introduction-to-game-entities"></a>介绍游戏实体

游戏实体是用于定义需要呈现、 冲突、 物理或人工智能逻辑的对象类。 幸运的是，游戏的基本代码中存在的实体通常与匹配游戏中的概念性对象。 在这种情况，识别需要游戏中的实体，可以更轻松地实现。 

例如，主题空格[投篮机会控制在全身向上游戏](http://en.wikipedia.org/wiki/Shoot_%27em_up)可能包括以下实体：

 - `PlayerShip`
 - `EnemyShip`
 - `PlayerBullet`
 - `EnemyBullet`
 - `CollectableItem`
 - `HUD`
 - `Environment`

这些实体将它们自己的类在游戏中，并且每个实例需要很少或没有安装超出了实例化。


# <a name="general-vs-specific-entity-types"></a>常规 vs。特定实体类型

游戏开发人员使用实体系统所面临的第一个问题之一是配合使用来通用化其实体多少。 即使它们在几个特征上存在差异，最特定的实现将定义用于每种类型的实体，类。 更多常规系统将将的实体组组合到一个类，并允许进行自定义的实例。

例如，我们可以假设一个空间游戏，它定义了以下类：

 - `PlayerDogfighter`
 - `PlayerBomber`
 - `EnemyMissileShip`
 - `EnemyLaserShip`

一个更通用的方法为创建播放器附带的单个类和对手附带，无法配置为支持随附了不同类型的单个类。 自定义项可能包括要加载，哪种类型的项目符号实体解决移动系数时创建的映像，以及防范对象的 AI 逻辑附带。 在这种情况下的实体列表可能会降低到：

 - `PlayerShip`
 - `EnemyShip`

当然，可以通过允许每个实例自定义用于控制移动进一步通用化这些实体类型。 对手发货实例可能执行 AI 逻辑时，将从输入读取播放器发货实例。 这意味着无法通用化实体进一步成一个类：

 - `Ship`

游戏可能使用了一个基类的所有实体，泛化可以进一步 – 继续。 此单个类，可以将其称为`GameEntity`，将使用整个游戏中每个实体实例的类，包括实体不附带项目符号和可回收项 (电源 ups) 等。

此系统的大多数常规通常称为*组件系统*。 在此类系统中，游戏实体可以具有单独的组件，如物理，AI，或呈现组件添加自定义行为和外观。 纯的基于组件的系统启用最大的灵活性，并可以避免出现问题而导致的如复杂的继承链继承。 与其他泛化，有效组件系统可能很难设置，并且可以降低代码的表现力。

使用的泛化程度取决于许多注意事项，包括：

 - 游戏大小 – 较小的游戏可以承受更大的游戏可能很难管理大量的类时创建特定的类。
 - 数据驱动的开发 – 依赖于数据 （映像、 3D 模型和数据文件，例如 JSON 或 XML） 的游戏可能受益于具有通用化实体类型和配置基于数据的详细信息。 这是特别针对的预计会在开发期间或之后已释放游戏中添加新内容的游戏的导入。
 - 游戏引擎模式 – 某些游戏引擎强烈建议组件系统使用的情况而另一些则允许开发人员可以决定如何组织实体。 CocosSharp 不需要使用组件的系统，因此，开发人员可以自由地实现任何类型的实体。 

为简单起见，我们将使用特定的基于类的方法与一个装运和项目符号实体出于本教程。


# <a name="project-setup"></a>项目设置

我们开始实现我们实体之前，我们需要创建一个项目。 我们将使用 CocosSharp 项目模板来简化项目创建。 [检查此文章](http://forums.xamarin.com/discussion/26822/cocossharp-project-templates-for-xamarin-studio)有关从 Visual Studio for Mac 模板创建 CocosSharp 项目信息。 本指南的其余部分将使用的项目名称**EntityProject**。

创建项目后我们将设置在 480 x 320 运行我们的游戏中的分辨率。 若要执行此操作，调用`CCScene.SetDefaultDesignResolution`中`GameAppDelegate.ApplicationDidFinishLaunching`方法，如下所示：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...

    // New code for resolution setting:
    CCScene.SetDefaultDesignResolution(480, 320, CCSceneResolutionPolicy.ShowAll);
    
    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);
    mainWindow.RunWithScene (scene);
} 
```

有关处理 CocosSharp 解决方法的详细信息，请参阅我们[CocosSharp 中处理多个解决方案指南](~/graphics-games/cocossharp/resolutions.md)。


# <a name="adding-content-to-the-project"></a>将内容添加到项目

一旦创建我们的项目后，我们将添加中包含的文件[此内容的 zip 文件](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)。 若要执行此操作，下载的 zip 文件，并将其解压缩。 请同时添加**ship.png**和**bullet.png**到**内容**文件夹。 **内容**文件夹将为内部**资产**文件夹在 Android 上，并且将在 iOS 上项目的根目录。 添加后，我们应看到这两个文件中的**内容**文件夹：

![](entities-images/image2.png "内容文件夹中添加后，应为这两个文件")


# <a name="creating-the-ship-entity"></a>创建发货实体

`Ship`类将为我们的游戏中的第一个实体。 若要添加`Ship`类中，首先创建一个名为文件夹**实体**项目根级别。 添加新类中的**实体**名为的文件夹`Ship`:

![](entities-images/image3.png "在调用发货实体文件夹中添加一个新类")

我们将会对进行的第一个更改我们`Ship`类是让它继承自`CCNode`类。 `CCNode` 用作通用 CocosSharp 类的基类喜欢`CCSprite`和`CCLayer`，并为我们提供以下功能：

 - `Position` 移动随附了在屏幕的属性。
 - `Children` 添加的属性 `CCSprite.`
 - `Parent` 属性，可用于附加`Ship`实例对其他`CCNodes`。 我们不会在本教程; 使用此功能更大的游戏通常要利用将实体附加到其他`CCNodes`。 
 - `AddEventListener` 响应的移动船上输入的方法。
 - `Schedule` 有关解决项目符号的方法。

我们还将添加`CCSprite`实例，以便可以在屏幕上看到我们发货：


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Ship : CCNode
    {
        CCSprite sprite;

        public Ship () : base()
        {
            sprite = new CCSprite ("ship.png");
            // Center the Sprite in this entity to simplify
            // centering the Ship when it is instantiated
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);
        }
    }
}
```

接下来，我们将添加到发货我们`GameLayer`才能看到它显示在我们的游戏中：


```csharp
public class GameLayer : CCLayer
{
    Ship ship;

    public GameLayer ()
    {
        ship = new Ship ();
        ship.PositionX = 240;
        ship.PositionY = 50;
        this.AddChild (ship);
    } 
    ...
```

如果我们运行我们的游戏中我们现在将看到我们发货实体：

![](entities-images/image4.png "当运行游戏时，将显示发货实体")


## <a name="why-inherit-from-ccnode-instead-of-ccsprite"></a>为什么继承而不是 CCSprite CCNode？

此时我们`Ship`类是简单包装器`CCSprite`实例。 由于`CCSprite`也继承自`CCNode`，我们无法直接从已继承`CCSprite`，这将减少中的代码`Ship.cs`。 此外，直接从继承`CCSprite`可以减少内存中对象数目，可以通过使依赖关系树更小提高性能。

这些好处，尽管我们继承自`CCNode`以隐藏某些`CCSprite`每个实例中的属性。 例如，`Texture`属性不应修改外部`Ship`类，并从继承`CCNode`使我们可以隐藏此属性。 我们实体的公共成员变得尤为重要，随着游戏就会增加大以及其他开发人员添加到组中。


# <a name="adding-input-to-the-ship"></a>添加到发货的输入

现在，我们随附了已在屏幕上可见，我们会添加输入。 我们方法将类似于而采取的做法[CocosSharp 指南简介](~/graphics-games/cocossharp/first-game/part2.md)，只不过我们将放置的代码中的移动`Ship`类而不是中包含的`CCLayer`或`CCScene`.

将代码添加到`Ship`以支持将其移动到任何位置用户触摸屏幕：


```csharp
public class Ship : CCNode
{
    CCSprite sprite;

    CCEventListenerTouchAllAtOnce touchListener;

    public Ship () : base()
    {
        sprite = new CCSprite ("ship.png");
        // Center the Sprite in this entity to simplify
        // centering the Ship on screen
        sprite.AnchorPoint = CCPoint.AnchorMiddle;
        this.AddChild(sprite);

        touchListener = new CCEventListenerTouchAllAtOnce();
        touchListener.OnTouchesMoved = HandleInput;
        AddEventListener(touchListener, this);

    }

    private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
    {
        if(touches.Count > 0)
        {
            CCTouch firstTouch = touches[0];

            this.PositionX = firstTouch.Location.X;
            this.PositionY = firstTouch.Location.Y;
        } 
    }
} 
```

许多投篮机会控制在全身向上游戏实现最大速度，与传统基于控制器的移动。 也就是说，我们将只需实现即时移动使我们更短的代码。


# <a name="creating-the-bullet-entity"></a>创建项目符号实体

我们简单游戏中的第二个实体是用于显示项目符号的实体。 就像`Ship`实体，`Bullet`实体将包含`CCSprite`以使其显示在屏幕上。 移动的逻辑不同之处在于它不依赖于移动; 的用户输入相反，`Bullet`实例将使用速度属性的直线中移动。

首先，我们将添加的新类文件，以便我们**实体**文件夹和调用它**项目符号**:

![](entities-images/image5.png "将新的类文件添加到实体文件夹和调用它项目符号")

添加我们将修改后`Bullet.cs`代码，如下所示：


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Bullet : CCNode
    {
        CCSprite sprite;

        public float VelocityX
        {
            get;
            set;
        }

        public float VelocityY
        {
            get;
            set;
        }

        public Bullet () : base()
        {
            sprite = new CCSprite ("bullet.png");
            // Making the Sprite be centered makes
            // positioning easier.
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);

            this.Schedule (ApplyVelocity);
        }

        void ApplyVelocity(float time)
        {
            PositionX += VelocityX * time;
            PositionY += VelocityY * time;
        }
    }
} 
```

除了更改用于文件`CCSprite`到`bullet.png`中的代码`ApplyVelocity`包括移动逻辑基于两个系数：`VelocityX`和`VelocityY`。

`Schedule`方法允许添加委托，调用每个帧。 在这种情况下我们正在将添加`ApplyVelocity`方法，以便我们项目符号将根据其速度值移动。 `Schedule`方法采用`Action<float>`，其中 float 参数指定的时间 （以秒为单位） 自上一帧，我们使用来实现基于时间的移动。 以来值以秒为单位，然后我们速度值表示中的移动*每秒像素*。


# <a name="adding-bullets-to-gamelayer"></a>将项目符号添加到 GameLayer

我们添加任何之前`Bullet`到我们的游戏中的实例，我们将生成一个容器，专门`List<Bullet>`。 修改`GameLayer`使其包含的项目符号列表：


```csharp
    public class GameLayer : CCLayer
    {
        Ship ship;
        List<Bullet> bullets;

        public GameLayer ()
        {
            ship = new Ship ();
            ship.PositionX = 240;
            ship.PositionY = 50;
            this.AddChild (ship);

            bullets = new List<Bullet> ();
        }
        ... 
```

接下来我们需要填充`Bullet`列表。 何时创建的逻辑`Bullet`应包含在`Ship`实体，但`GameLayer`负责存储的项目符号列表。 我们将使用工厂模式以允许`Ship`实体创建`Bullet`实例。 此工厂将公开事件的`GameLayer`可以处理。 

若要这样做首先，我们将将文件夹添加到我们的项目称为**工厂**，然后添加新的类称为`BulletFactory`:

![](entities-images/image6.png "将文件夹添加到名为工厂，该项目，然后添加名 BulletFactory 的新类")

接下来，我们将实施`BulletFactory`singleton 类：


```csharp
using System;

namespace EntityProject
{
    public class BulletFactory
    {
        static Lazy<BulletFactory> self = 
            new Lazy<BulletFactory>(()=>new BulletFactory());

        // simple singleton implementation
        public static BulletFactory Self
        {
            get
            {
                return self.Value;
            }
        }

        public event Action<Bullet> BulletCreated;

        private BulletFactory()
        {

        }

        public Bullet CreateNew()
        {
            Bullet newBullet = new Bullet ();

            if (BulletCreated != null)
            {
                BulletCreated (newBullet);
            }

            return newBullet;
        }
    }
} 
```

`Ship`实体将处理创建`Bullet`实例-具体而言，它将处理频率`Bullet`应 （即频率触发项目符号） 可创建实例，其位置和其速度。

修改`Ship`实体的构造函数来添加新`Schedule`拨打电话，然后实现此方法，如下所示：


```csharp
...
public Ship () : base()
{
    sprite = new CCSprite ("ship.png");
    // Center the Sprite in this entity to simplify
    // centering the Ship on screen
    sprite.AnchorPoint = CCPoint.AnchorMiddle;
    this.AddChild(sprite);

    touchListener = new CCEventListenerTouchAllAtOnce();
    touchListener.OnTouchesMoved = HandleInput;
    AddEventListener(touchListener, this);

    Schedule (FireBullet, interval: 0.5f);

}

void FireBullet(float unusedValue)
{
    Bullet newBullet = BulletFactory.Self.CreateNew ();
    newBullet.Position = this.Position;
    newBullet.VelocityY = 100;
} 
...
```

最后一步是新处理创建`Bullet`实例`GameLayer`代码。 添加到事件处理程序`BulletCreated`添加新创建的事件`Bullet`到适当的列表：


```csharp
...
public GameLayer ()
{
    ship = new Ship ();
    ship.PositionX = 240;
    ship.PositionY = 50;
    this.AddChild (ship);

    bullets = new List<Bullet> ();
    BulletFactory.Self.BulletCreated += HandleBulletCreated;
}

void HandleBulletCreated(Bullet newBullet)
{
    AddChild (newBullet);
    bullets.Add (newBullet);
}
... 
```

现在，我们可以运行游戏，请参阅`Ship`解决`Bullet`实例：

![](entities-images/image1.png "运行游戏和解决在装运中的项目符号实例")


# <a name="why-gamelayer-has-ship-and-bullets-members"></a>为什么 GameLayer 包含发货和项目符号成员

我们`GameLayer`类定义两个字段来保存到我们实体实例的引用 (`ship`和`bullets`)，但不与它们执行任何操作。 此外，实体负责如移动和解决其自己的行为。 因此为什么未，我们添加`ship`和`bullets`字段设置为`GameLayer`？

我们添加这些成员的原因是因为完整游戏实现需要中的逻辑`GameLayer`用于不同的实体之间的交互。 例如，可能会进一步建立此游戏以包括敌人玩家被破坏。 这些敌人将包含在`List`中`GameLayer`，和测试的逻辑是否`Bullet`实例与冲突将在中执行敌人`GameLayer`以及。 换而言之，`GameLayer`是根*所有者*所有实体的实例，也是如此负责实体实例之间的交互。


# <a name="bullet-destruction-considerations"></a>项目符号析构注意事项

我们的游戏中当前缺少代码销毁`Bullet`实例。 每个`Bullet`实例具有逻辑移动在屏幕上，但我们尚未添加任何代码即可销毁任何屏幕`Bullet`实例。

此外，析构`Bullet`实例可能不属于`GameLayer`。 例如，而被销毁时屏幕之外，不是`Bullet`实体可能具有逻辑以在一段时间后销毁本身。 在这种情况下，`Bullet`需要一种方式进行通信应销毁到`GameLayer`、 与相似`Ship`实体传递给`GameLayer`，新`Bullet`通过创建`BulletFactory`。

最简单的解决方案是展开的工厂类，以支持析构的责任。 可以在工厂可以通知被销毁，而可以由其他对象，如处理的实体实例的`GameLayer`从其列表中删除实体实例。 


# <a name="summary"></a>摘要

本指南演示如何通过继承创建 CocosSharp 实体`CCNode`类。 这些实体是自包含的对象，处理创建他们自己的视觉对象和自定义逻辑。 此指南从属于根实体容器 （冲突和其他实体交互逻辑） 的代码指定所属实体 （移动和创建的其他实体） 内的代码。

## <a name="related-links"></a>相关链接

- [CocosSharp API 文档](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [内容 zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)
