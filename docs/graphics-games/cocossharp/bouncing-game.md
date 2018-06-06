---
title: BouncingGame 详细信息
description: 本文档提供的 BouncingGame，用 CocosSharp 生成一个简单的弹跳球游戏创建演练。
ms.prod: xamarin
ms.assetid: AC9FD56F-6E4A-40DA-8168-45A761D869FD
author: charlespetzold
ms.author: chape
ms.date: 03/29/2018
ms.openlocfilehash: 1a2423fcbeec0c3414b766a6c9e6f64ab53f4bf9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783101"
---
# <a name="bouncinggame-details"></a>BouncingGame 详细信息

> [!TIP]
> 在找不到的代码 BouncingGame [Xamarin 示例](https://developer.xamarin.com/samples/mobile/BouncingGame/)。 若要最遵循本指南，请下载并浏览代码，如指南引用它。

本演练演示如何实现使用 CocosSharp 简单弹跳球游戏。 

 - 解压缩游戏内容
 - 常见 CocosSharp 可视元素
 - 添加到的可视元素 `GameLayer`
 - 实现每个帧逻辑

我们完成的游戏将如下所示：

![BouncingGame，完成](bouncing-game-images/image1.png "BouncingGame，完成")

## <a name="unzipping-game-content"></a>解压缩游戏内容

游戏开发人员通常使用术语*内容*来引用非代码文件，这通常由 visual 艺术家、 游戏设计器中或音频的设计器创建。 常见类型的内容包含用于显示视觉对象、 播放声音，或控制人工智能 (AI) 行为的文件。 从游戏开发团队的角度来看，通常会由非程序员创建内容。 我们的游戏中包括两种类型的内容：

 - 要定义的球和刀片的显示方式的 png 文件。
 - 要定义由分数显示 （当我们将添加下面的评分显示更详细的讨论） 使用的字体的单个 xnb 文件

此处使用此内容可在[内容 zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)。 我们将需要下载并解压缩到的位置，我们将稍后在本演练中访问这些文件。

## <a name="common-cocossharp-visual-elements"></a>常见 CocosSharp 可视元素

CocosSharp 提供了多个用来显示视觉对象的类。 某些元素是直接可见的而其他人用于组织。 游戏中，我们将使用以下：

 - `CCNode` – 中的所有视觉对象 CocosSharp 基本类。 `CCNode`类包含`AddChild`函数用于构造父/子层次结构，也称为*可视化树*或*场景关系图*。 下面所述的所有类都继承自`CCNode`。
 - `CCScene` – 所有 CocosSharp 游戏的可视化树的根。 所有的可视元素必须是与可视化树的一部分`CCScene`根位置，或它们将不可见。
 - `CCLayer` – 视觉对象的容器对象，如`CCSprite`。 顾名思义，`CCLayer`类用于控制如何将视觉元素层，每个其他。
 - `CCSprite` – 显示图像的一部分。 `CCSprite` 实例可以定位，调整大小，并提供大量的视觉效果。
 - `CCLabel` – 在屏幕上显示的字符串。 供使用的字体`CCLabel`xnb 文件中定义。 我们将讨论下面更详细地 xnbs。

若要了解如何使用不同类型我们将单个`CCSprite`。 可视元素必须添加到`CCLayer`，并且必须具有的可视化树`CCScene`其根部。 因此，单个的父/子关系`CCSprite`将`CCScene`  >  `CCLayer`  >  `CCSprite`。

## <a name="adding-visual-elements-to-gamelayer"></a>将可视元素添加到 GameLayer

### <a name="adding-the-paddle-sprite"></a>添加刀片动画层

我们将在最初设置游戏的背景以黑色并添加单个`CCSprite`在屏幕上呈现。 修改`GameLayer`类添加`CCSprite`，如下所示：

```csharp
using System;
using System.Collections.Generic;
using CocosSharp;
namespace BouncingGame
{
    public class GameLayer : CCLayer
    {
        CCSprite paddleSprite;
        public GameLayer () : base(CCColor4B.Black)
        {
            // "paddle" refers to the paddle.png image
            paddleSprite = new CCSprite ("paddle");
            paddleSprite.PositionX = 100;
            paddleSprite.PositionY = 100;
            AddChild (paddleSprite);
        }
        protected override void AddedToScene ()
        {
            base.AddedToScene ();
            // Use the bounds to layout the positioning of the drawable assets
            CCRect bounds = VisibleBoundsWorldspace;
            // Register for touch events
            var touchListener = new CCEventListenerTouchAllAtOnce ();
            touchListener.OnTouchesEnded = OnTouchesEnded;
            AddEventListener (touchListener, this);
        }
        void OnTouchesEnded (List<CCTouch> touches, CCEvent touchEvent)
        {
            if (touches.Count > 0)
            {
                // Perform touch handling here
            }
        }
    }
}
```

上面的代码中创建单个`CCSprite`并将其作为子级添加`GameLayer`。 `CCSprite`构造函数，我们可以定义要用作字符串的图像文件。 我们的代码告知 CocosSharp 查找名为的文件`paddle`（扩展省略了，这在本指南的后面，我们将讨论）。 任何文件名将查找 CocosSharp`paddle`的根内容文件夹中 (即**内容**) 添加到任何文件夹以及`gameView.ContentManager.SearchPaths`（在上一节中讨论）。

我们将这些文件将直接添加到根**内容**用于 iOS 和 Android 的文件夹。 为此，右键单击或按住 control 键单击上**内容**文件夹中的 iOS 项目并选择**添加** > **添加文件...** 导航到其中我们前面解压缩内容，然后选择**paddle.png**。 如果系统询问有关如何将文件添加到文件夹，我们应选择**复制**选项：

![将文件添加到文件夹对话框](bouncing-game-images/image2.png "将文件添加到文件夹对话框")

接下来，我们将添加到 Android 项目的文件。 右键单击或按住 control 键单击上的内容文件夹 (即在**资产**Android 项目的文件夹)，然后选择选择**添加** > **添加文件...**.此时，导航到 iOS 项目**内容**文件夹。 当系统询问有关如何添加文件，选择**添加链接**选项：

![将文件添加到文件夹对话框](bouncing-game-images/addalink.png "将文件添加到文件夹对话框")

我们将讨论为什么文件会有要添加到以下这两个项目。 每个项目**内容**文件夹现在包含**paddle.png**文件：

![内容的文件夹的内容](bouncing-game-images/image3.png "内容文件夹的内容")

如果我们运行游戏我们将看到`CCSprite`所绘制：

![刀片，屏幕上绘制](bouncing-game-images/image4.png "刀片，屏幕上绘制")

### <a name="file-details"></a>文件的详细信息

到目前为止我们已添加到项目中，单个文件，该过程相当简单。 我们只需添加**paddle.png**文件为**内容**文件夹和代码中引用它。 让我们看一段时间来处理 CocosSharp 中的文件时查看一些注意事项。

#### <a name="capitalization"></a>大写

请注意，文件名称和我们代码中用来访问该文件的字符串都是小写。 这是因为某些平台 （如 Windows 桌面和 iOS 模拟器） 不区分大小写的而其他平台 （如 Android 和 iOS 设备） 是区分大小写。 我们将本教程的其余部分使用小写的所有文件，以便为跨平台的文件和代码保持尽可能。

#### <a name="extensions"></a>扩展

引用刀片文件时，用于创建画面构造函数不包括".png"扩展。 处理相同的资产类型的文件扩展名为 CocosSharp 项目可能平台之间有所不同时，通常省略的文件扩展名。 例如，音频文件可能根据平台的.aiff、.mp3 或.wma 文件格式。 离开关闭扩展允许同一代码适用于所有平台，而不考虑文件扩展名。

#### <a name="content-in-platform-specific-projects"></a>特定于平台的项目中的内容

与大多数代码文件可在不同的 PCL，内容必须将文件添加到每个特定于平台的项目。 CocosSharp 需要这两个原因：

1. 每个平台都有不同**生成操作**。 IOS 项目，添加的内容应使用**BundleResource**生成操作。 添加到 Android 项目的内容应使用**AndroidAsset**生成操作。
2. 某些平台需要特定于平台的文件格式。 例如，字体.xnb 文件 iOS 和 Android 之间有所不同，如我们所见稍后在本演练。

如果文件格式并无不同平台 （如.png) 之间，每个平台可以使用同一个文件，其中一个或多个平台可以链接位于同一位置的文件。

## <a name="orientation"></a>方向

就像任何其他应用程序，CocosSharp 应用时，可以运行纵向或横向朝方向。 我们将调整在仅纵向模式下运行该游戏。 首先，我们将更改以处理纵向纵横比游戏中的解决方法代码。 若要执行此操作，修改`width`和`height`中值`LoadGame`中的方法`ViewController`在 iOS 上的类和`MainActivity`在 Android 上的类：

```csharp
void LoadGame (object sender, EventArgs e)
{
    CCGameView gameView = sender as CCGameView;

    if (gameView != null)
    {
        var contentSearchPaths = new List<string> () { "Fonts", "Sounds" };
        CCSizeI viewSize = gameView.ViewSize;

        int width = 768;
        int height = 1027;
    // ...
```

接下来我们将需要修改每个特定于平台的项目，在纵向模式下运行。

### <a name="ios-orientation"></a>iOS 方向

若要修改 iOS 项目的方向，选择**Info.plist**文件中**BouncingGame.iOS**项目，并更改**iPhone/iPod 部署信息**和**iPad 部署信息**以仅包括纵向方向：

![方向选项](bouncing-game-images/image5.png "方向选项")

### <a name="android-orientation"></a>Android 方向

若要修改 Android 项目的方向，请在 BouncingGame.Android 项目中打开 MainActivity.cs 文件。 修改活动属性定义，以便它指定仅纵向，如下所示：

```csharp
[Activity (
    Label = "BouncingGame.Android",
    AlwaysRetainTaskState = true,
    Icon = "@drawable/icon",
    Theme = "@android:style/Theme.NoTitleBar",
    ScreenOrientation = ScreenOrientation.Portrait,
    LaunchMode = LaunchMode.SingleInstance,
    MainLauncher = true,
    ConfigurationChanges = ConfigChanges.Orientation | ConfigChanges.ScreenSize | ConfigChanges.Keyboard | ConfigChanges.KeyboardHidden)
]
public class MainActivity : Activity
{ 
...
```

## <a name="default-coordinate-system"></a>默认坐标系统

我们的代码，实例化`CCSprite`，设置`PositionX`和`PositionY`为 100 的值。 默认情况下，这意味着， `CCSprite` center 将位于 100 个像素，启动并从屏幕的左下角的右侧。 坐标系统可以修改通过附加`CCCamera`到`CCLayer`。 我们不会使用`CCCamera`此项目，但的详细信息中`CCCamera`可以位于[CocosSharp API 文档](https://developer.xamarin.com/api/type/CocosSharp.CCLayer/)。

在硬件上上述"游戏"而不是像素为单位的像素为单位 100 个像素。 这意味着在不同的解决方法 （如 iPhone 和 iPad) 的设备上运行相同的游戏将导致对象被定位并正确设置相对于的物理屏幕大小。 具体来说，游戏的可见区域将始终为 1024年像素、 高和 768 像素宽，则此解决方案我们已指定的方式中较早`LoadGame`方法。

## <a name="adding-the-ball-sprite"></a>添加球动画层

现在，我们已经熟悉使用的基础知识`CCSprite`，我们将添加第二个`CCSprite`– 一个球。 我们将以下步骤它非常类似于我们如何创建刀片`CCSprite`。 

首先，我们将添加**ball.png**解压缩到 iOS 项目文件夹中的映像**内容**文件夹。 选择**复制**到文件**内容**目录。 请按照与上面添加一个链接到相同的步骤**ball.png** Android 项目中的文件。

接下来创建`CCSprite`对于通过将成员添加此球调用`ballSprite`到`GameScene`类，以及实例化代码`ballSprite`。 完成后`GameLayer`类将如下所示：

```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
    }
```

## <a name="adding-the-score-label"></a>添加评分标签

我们将添加对游戏的最后一个可视元素是`CCLabel`要显示的次数用户已成功退回球。 `CCLabel`使用构造函数中指定的字体在屏幕上显示字符串。

添加以下代码以创建`CCLabel`实例中`GameLayer`。 一次完成的代码应如下所示：

```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    CCLabel scoreLabel;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "Arial", 20, CCLabelFormat.SystemFont);
        scoreLabel.PositionX = 50;
        scoreLabel.PositionY = 1000;
        scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
        AddChild (scoreLabel);
    }
    // ...
```

请注意，使用 scoreLabel`AnchorPoint`的`CCPoint.AnchorUpperLeft`，这意味着，`PositionX`和`PositionY`值定义其左上角位置。 这可以让我们位置`scoreLabel`相对于而无需考虑标签的尺寸，屏幕顶部左侧。

## <a name="implementing-every-frame-logic"></a>实现每个帧逻辑

到目前为止，游戏显示静态场景。 我们将添加逻辑，以通过将代码用于更新高频率的对象的位置添加控制的场景中的对象移动。 每秒-也称为六十六十时间的代码将运行在这种情况下，*帧*每秒 （除非硬件无法处理此频繁地更新）。 具体而言，我们将添加逻辑以便进行划分和针对刀片，将根据输入，刀片并更新玩家的分数，每次球袭击刀片反弹球。

`Schedule`方法，提供的`CCNode`类中，可以让我们将每个帧逻辑添加到游戏。 我们将添加后面的代码`// New code`GameLayer 构造函数：

```csharp
public GameLayer () : base (CCColor4B.Black)
{
    // "paddle" refers to the paddle.png image
    paddleSprite = new CCSprite ("paddle");
    paddleSprite.PositionX = 100;
    paddleSprite.PositionY = 100;
    AddChild (paddleSprite);
    ballSprite = new CCSprite ("ball");
    ballSprite.PositionX = 320;
    ballSprite.PositionY = 600;
    AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "arial", 22, CCLabelFormat.SpriteFont);
    scoreLabel.PositionX = 50;
    scoreLabel.PositionY = 1000;
    scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
    AddChild (scoreLabel);
    // New code:
    Schedule (RunGameLogic);
}
```

现在创建`RunGameLogic`中的方法`GameLayer`类，该类将容纳所有的每个帧逻辑：

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
}
```

浮点型参数定义多长时间的框架都是以秒为单位。 我们将使用此值时实现球的移动。

### <a name="making-the-ball-fall"></a>使位于球

我们可以在任一手动实现重力代码或通过使用内置的 Box2D 功能在 CocosSharp 球。 Box2D 物理模拟引擎可供 CocosSharp 游戏。 这是功能强大且高效，但需要编写安装程序代码。 由于物理模拟是相当简单，此处它将实现手动。

若要实现重力我们将到存储的当前 X 和 Y 速度球的需要。 我们将添加到两个成员`GameLayer`类：

```csharp
float ballXVelocity;
float ballYVelocity;
// How much to modify the ball's y velocity per second:
const float gravity = 140;
```

接下来，我们可以实现中的下降逻辑`RunGameLogic`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
}
```

### <a name="moving-the-paddle-with-touch-input"></a>移动使用触摸屏输入刀片

现在，下降球，我们将通过将添加水平移动到刀片`CCEventListenerTouchAllAtOnce`对象。 此对象提供输入相关的事件的数。 在这种情况下，我们想要如果任何触摸点移动，以便我们可以调整刀片的位置会收到通知。 `GameLayer`已实例化`CCEventListenerTouchAllAtOnce`，因此我们只需将分配`OnTouchesMoved`委托。 为此，请按如下方式修改 AddedToScene 方法：

```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();
    // Use the bounds to layout the positioning of the drawable assets
    CCRect bounds = VisibleBoundsWorldspace;
    // Register for touch events
    var touchListener = new CCEventListenerTouchAllAtOnce ();
    touchListener.OnTouchesEnded = OnTouchesEnded;
    // new code:
    touchListener.OnTouchesMoved = HandleTouchesMoved;
    AddEventListener (touchListener, this);
}
```

接下来，我们将实施`HandleTouchesMoved`:

```csharp
void HandleTouchesMoved (System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    // we only care about the first touch:
    var locationOnScreen = touches [0].Location;
    paddleSprite.PositionX = locationOnScreen.X;
}
```

### <a name="implementing-ball-collision"></a>实现球冲突

如果我们运行游戏，现在我们注意到球落通过刀片。 我们将实施*冲突*（逻辑来应对重叠游戏对象） 中的每个帧代码。 由于对象更改的移动定位每个帧，冲突检测通常是还执行每个帧。 我们将还添加速度 X 轴上时球命中刀片将某些质询添加到该游戏中，但这意味着我们需要防止球移动过去的屏幕的边缘。 我们需要执行此操作`RunGameLogic`应用到的速度后的代码`ballSprite`后`// New Code`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
    // New Code:
    // Check if the two CCSprites overlap...
    bool doesBallOverlapPaddle = ballSprite.BoundingBoxTransformedToParent.IntersectsRect(
        paddleSprite.BoundingBoxTransformedToParent);
    // ... and if the ball is moving downward.
    bool isMovingDownward = ballYVelocity < 0;
    if (doesBallOverlapPaddle && isMovingDownward)
    {
        // First let's invert the velocity:
        ballYVelocity *= -1;
        // Then let's assign a random value to the ball's x velocity:
        const float minXVelocity = -300;
        const float maxXVelocity = 300;
        ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    }
    // First let’s get the ball position:   
    float ballRight = ballSprite.BoundingBoxTransformedToParent.MaxX;
    float ballLeft = ballSprite.BoundingBoxTransformedToParent.MinX;
    // Then let’s get the screen edges
    float screenRight = VisibleBoundsWorldspace.MaxX;
    float screenLeft = VisibleBoundsWorldspace.MinX;
    // Check if the ball is either too far to the right or left:    
    bool shouldReflectXVelocity = 
        (ballRight > screenRight && ballXVelocity > 0) ||
        (ballLeft < screenLeft && ballXVelocity < 0);
    if (shouldReflectXVelocity)
    {
        ballXVelocity *= -1;
    }
}
```

## <a name="adding-scoring"></a>添加评分

现在，可播放游戏，最后一步是添加计分逻辑。 首先，我们将向名为的 GameLayer 类添加分数成员`score`:

```csharp
int score;
```

我们将使用此变量，来跟踪玩家的分数，并显示它使用`scoreLabel`。 若要执行此添加以下代码中的 if 语句`RunGameLogic`时的球和刀片重叠：

```csharp
// ...
if (doesBallOverlapPaddle && isMovingDownward)
{
    // First let's invert the velocity:
    ballYVelocity *= -1;
    // Then let's assign a random to the ball's x velocity:
    const float minXVelocity = -300;
    const float maxXVelocity = 300;
    ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    // New code:
    score++;
    scoreLabel.Text = "Score: " + score;
}
// ...
```

现在我们可以运行游戏，并请参阅游戏显示这增大如球退回从刀片中移出的分数：

![已完成的游戏，它带有递增分数](bouncing-game-images/image1.png "已完成的游戏，它带有递增分数")

## <a name="summary"></a>总结

本演练提供图形、 物理，与使用 CocosSharp 的输入创建跨平台游戏。 它是 CocosSharp 游戏开发入门的第一步。 我们介绍了一些 CocosSharp、 如何构造可视化树，从而正确地呈现对象以及如何实现每个帧游戏逻辑中的最常见类。

本演练涵盖仅 CocosSharp 游戏引擎提供一小的部分。 有关信息和其他 CocosSharp 主题上的演练，请参阅[CocosSharp 指南的其余部分](~/graphics-games/cocossharp/index.md)。

## <a name="related-links"></a>相关链接

- [已完成的游戏 （示例）](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [游戏内容 （示例）](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
