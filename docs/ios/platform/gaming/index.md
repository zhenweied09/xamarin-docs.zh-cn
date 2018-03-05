---
title: "iOS 游戏 Api"
description: "本文介绍如何通过 iOS 9，可用来改善你的 Xamarin.iOS 游戏图形和音频功能提供的新游戏增强功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 6c735c68ee61032d8dfe3a74e6858bc058c6fa47
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="ios-gaming-apis"></a>iOS 游戏 Api

_本文介绍如何通过 iOS 9，可用来改善你的 Xamarin.iOS 游戏图形和音频功能提供的新游戏增强功能。_

Apple 已在 iOS 9 中的 Api 游戏，它可以更轻松地在 Xamarin.iOS 应用程序中实现游戏图形和音频的多项技术改进。
其中包括轻松地通过高级框架和借助的 iOS 设备的 GPU 提高的速度和图形功能强大的开发。

[ ![](images/flocking01.png "下面举例说明运行群的应用程序")](images/flocking01.png)

这包括 GameplayKit、 ReplayKit、 模型 I/O、 MetalKit 和金属性能着色器以及金属、 SceneKit 和 SpriteKit 的新的增强功能。

本文将介绍所有提高你的 iOS 9 的新游戏增强功能的 Xamarin.iOS 游戏的方法：

## <a name="introducing-gameplaykit"></a>引入 GameplayKit

Apple 的新 GameplayKit framework 提供一组技术，可以轻松地通过减少所需的实现的重复、 公共代码中创建适用于 iOS 设备的游戏。 GameplayKit 提供工具，用于快速开发到游戏机制可以然后轻松地与结合使用 （如 SceneKit 或 SpriteKit） 的图形引擎提供已完成的游戏。

GameplayKit 包括几个，常见、 游戏播放算法，如：

- 基于的行为，代理模拟，它允许你定义动作数和 AI 将自动使用的目标。
- 基于轮次的玩游戏 minmax 人工智能。
- 数据驱动具有模糊的原因，以提供紧急行为的游戏逻辑规则系统。

此外，GameplayKit 采用构建基块的方法，到游戏开发通过提供以下功能的模块化体系结构：

- 用于处理复杂的过程性代码的状态机基于玩游戏中的系统。
- 提供的工具而不会导致调试问题随机玩游戏和不可预测性。
- 可重用的组件化实体基于体系结构。

若要了解有关 GameplayKit 的详细信息，请参阅 Apple 的[Gameplaykit 编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172)和[GameplayKit Framework 参考](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199)。

## <a name="gameplaykit-examples"></a>GameplayKit 示例

让我们快速了解一下在使用玩游戏工具包的 Xamarin.iOS 应用程序中实现一些简单玩游戏机制。

### <a name="pathfinding"></a>Pathfinding

Pathfinding 是游戏的查找其方面游戏板的 AI 元素的能力。
例如，查找迷宫通过其方式或通过第一个人射击 world 地形 3D 字符二维防范对象。

下面的代码图，请考虑：

[ ![](images/gkpathfindpath.png "Pathfinding 映射示例")](images/gkpathfindpath.png)

使用 pathfinding 此 C# 代码可以确定通过映射的一种方法：

```csharp
var a = GKGraphNode2D.FromPoint (new Vector2 (0, 5));
var b = GKGraphNode2D.FromPoint (new Vector2 (3, 0));
var c = GKGraphNode2D.FromPoint (new Vector2 (2, 6));
var d = GKGraphNode2D.FromPoint (new Vector2 (4, 6));
var e = GKGraphNode2D.FromPoint (new Vector2 (6, 5));
var f = GKGraphNode2D.FromPoint (new Vector2 (6, 0));

a.AddConnections (new [] { b, c }, false);
b.AddConnections (new [] { e, f }, false);
c.AddConnections (new [] { d }, false);
d.AddConnections (new [] { e, f }, false);

var graph = GKGraph.FromNodes(new [] { a, b, c, d, e, f });

var a2e = graph.FindPath (a, e); // [ a, c, d, e ]
var a2f = graph.FindPath (a, f); // [ a, b, f ]

Console.WriteLine(String.Join ("->", (object[]) a2e));
Console.WriteLine(String.Join ("->", (object[]) a2f));
```

### <a name="classical-expert-system"></a>古典专家系统

下面的 C# 代码段演示如何使用 GameplayKit 来实现古典专家系统：

```csharp
string output = "";
bool reset = false;
int input = 15;

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    /*
    If reset is true, clear the output and set reset to false
    */
    var clearRule = GKRule.FromPredicate ((rules) => reset, rules => {
        output = "";
        reset = false;
    });
    clearRule.Salience = 1;

    var fizzRule = GKRule.FromPredicate (mod (3), rules => {
        output += "fizz";
    });
    fizzRule.Salience = 2;

    var buzzRule = GKRule.FromPredicate (mod (5), rules => {
        output += "buzz";
    });
    buzzRule.Salience = 2;

    /*
    This *always* evaluates to true, but is higher Salience, so evaluates after lower-salience items
    (which is counter-intuitive). Print the output, and reset (thus triggering "ResetRule" next time)
    */
    var outputRule = GKRule.FromPredicate (rules => true, rules => {
        System.Console.WriteLine(output == "" ? input.ToString() : output);
        reset = true;
    });
    outputRule.Salience = 3;

    var rs = new GKRuleSystem ();
    rs.AddRules (new [] {
        clearRule,
        fizzRule,
        buzzRule,
        outputRule
    });

    for (input = 1; input < 16; input++) {
        rs.Evaluate ();
        rs.Reset ();
    }
}

protected Func<GKRuleSystem, bool> mod(int m)
{
    Func<GKRuleSystem,bool> partiallyApplied = (rs) => input % m == 0;
    return partiallyApplied;
}
```

基于给定的一组规则 (`GKRule`) 和已知的一组输入，专家系统 (`GKRuleSystem`) 将创建可预测输出 (`fizzbuzz`对于我们上面的示例)。

### <a name="flocking"></a>群

群允许 AI 一组控制游戏实体表现为 flock，其中组响应的动作和操作的潜在顾客实体处于飞行状态的鸟瞰 flock 或鱼徜徉学校等。

下面的 C# 代码段实现使用图形显示 GameplayKit 和 SpriteKit 群行为：

```csharp
using System;
using SpriteKit;
using CoreGraphics;
using UIKit;
using GameplayKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
using OpenTK;

namespace FieldBehaviorExplorer
{
    public static class FlockRandom
    {
        private static GKARC4RandomSource rand = new GKARC4RandomSource ();

        static FlockRandom ()
        {
            rand.DropValues (769);
        }

        public static float NextUniform ()
        {
            return rand.GetNextUniform ();
        }
    }

    public class FlockingScene : SKScene
    {
        List<Boid> boids = new List<Boid> ();
        GKComponentSystem componentSystem;
        GKAgent2D trackingAgent; //Tracks finger on screen
        double lastUpdateTime = Double.NaN;
        //Hold on to behavior so it doesn't get GC'ed
        static GKBehavior flockingBehavior;
        static GKGoal seekGoal;


        public FlockingScene (CGSize size) : base (size)
        {
            AddRandomBoids (20);

            var scale = 0.4f;
            //Flocking system
            componentSystem = new GKComponentSystem (typeof(GKAgent2D));
            var behavior = DefineFlockingBehavior (boids.Select (boid => boid.Agent).ToArray<GKAgent2D>(), scale);
            boids.ForEach (boid => {
                boid.Agent.Behavior = behavior;
                componentSystem.AddComponent(boid.Agent);
            });

            trackingAgent = new GKAgent2D ();
            trackingAgent.Position = new Vector2 ((float) size.Width / 2.0f, (float) size.Height / 2.0f);
            seekGoal = GKGoal.GetGoalToSeekAgent (trackingAgent);
        }

        public override void TouchesBegan (NSSet touches, UIEvent evt)
        {
            boids.ForEach(boid => boid.Agent.Behavior.SetWeight(1.0f, seekGoal));
        }

        public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            boids.ForEach (boid => boid.Agent.Behavior.SetWeight (0.0f, seekGoal));
        }

        public override void TouchesMoved (NSSet touches, UIEvent evt)
        {
            var touch = (UITouch) touches.First();
            var loc = touch.LocationInNode (this);
            trackingAgent.Position = new Vector2((float) loc.X, (float) loc.Y);
        }


        private void AddRandomBoids (int count)
        {
            var scale = 0.4f;
            for (var i = 0; i < count; i++) {
                var b = new Boid (UIColor.Red, this.Size, scale);
                boids.Add (b);
                this.AddChild (b);
            }
        }

        internal static GKBehavior DefineFlockingBehavior(GKAgent2D[] boidBrains, float scale)
        {
            if (flockingBehavior == null) {
                var flockingGoals = new GKGoal[3];
                flockingGoals [0] = GKGoal.GetGoalToSeparate (boidBrains, 100.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [1] = GKGoal.GetGoalToAlign (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [2] = GKGoal.GetGoalToCohere (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);

                flockingBehavior = new GKBehavior ();
                flockingBehavior.SetWeight (25.0f, flockingGoals [0]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [1]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [2]);
            }
            return flockingBehavior;
        }

        public override void Update (double currentTime)
        {
            base.Update (currentTime);
            if (Double.IsNaN(lastUpdateTime)) {
                lastUpdateTime = currentTime;
            }
            var delta = currentTime - lastUpdateTime;
            componentSystem.Update (delta);
        }
    }

    public class Boid : SKNode, IGKAgentDelegate
    {
        public GKAgent2D Agent { get { return brains; } }
        public SKShapeNode Sprite { get { return sprite; } }

        class BoidSprite : SKShapeNode
        {
            public BoidSprite (UIColor color, float scale)
            {
                var rot = CGAffineTransform.MakeRotation((float) (Math.PI / 2.0f));
                var path = new CGPath ();
                path.MoveToPoint (rot, new CGPoint (10.0, 0.0));
                path.AddLineToPoint (rot, new CGPoint (0.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 20.0));
                path.AddLineToPoint (rot, new CGPoint (20.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 0.0));
                path.CloseSubpath ();

                this.SetScale (scale);
                this.Path = path;
                this.FillColor = color;
                this.StrokeColor = UIColor.White;

            }
        }

        private GKAgent2D brains;
        private BoidSprite sprite;
        private static int boidId = 0;

        public Boid (UIColor color, CGSize size, float scale)
        {
            brains = BoidBrains (size, scale);
            sprite = new BoidSprite (color, scale);
            sprite.Position = new CGPoint(brains.Position.X, brains.Position.Y);
            sprite.ZRotation = brains.Rotation;
            sprite.Name = boidId++.ToString ();

            brains.Delegate = this;

            this.AddChild (sprite);
        }

        private GKAgent2D BoidBrains(CGSize size, float scale)
        {
            var brains = new GKAgent2D ();
            var x = (float) (FlockRandom.NextUniform () * size.Width);
            var y = (float) (FlockRandom.NextUniform () * size.Height);
            brains.Position = new Vector2 (x, y);

            brains.Rotation = (float)(FlockRandom.NextUniform () * Math.PI * 2.0);
            brains.Radius = 30.0f * scale;
            brains.MaxSpeed = 0.5f;
            return brains;
        }

        [Export ("agentDidUpdate:")]
        public void AgentDidUpdate (GameplayKit.GKAgent agent)
        {
        }

        [Export ("agentWillUpdate:")]
        public void AgentWillUpdate (GameplayKit.GKAgent agent)
        {
            var brainsIn = (GKAgent2D) agent;
            sprite.Position = new CGPoint(brainsIn.Position.X, brainsIn.Position.Y);
            sprite.ZRotation = brainsIn.Rotation;
            Console.WriteLine ($"{sprite.Name} -> [{sprite.Position}], {sprite.ZRotation}");
        }
    }
}
```

接下来，在视图控制器中实现此场景：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.
        this.View = new SKView {
        ShowsFPS = true,
        ShowsNodeCount = true,
        ShowsDrawCount = true
    };
}

public override void ViewWillLayoutSubviews ()
{
    base.ViewWillLayoutSubviews ();

    var v = (SKView)View;
    if (v.Scene == null) {
        var scene = new FlockingScene (View.Bounds.Size);
        scene.ScaleMode = SKSceneScaleMode.AspectFill;
        v.PresentScene (scene);
    }
}
```

运行时，有些动画_"Boids"_将 flock 围绕我们手指 tap:

[ ![](images/flocking01.png "稍有动画的 Boids 将 flock 围绕手指分流")](images/flocking01.png)

### <a name="other-apple-examples"></a>其他 Apple 示例

除了上面给出的示例，Apple 提供了以下示例应用可将转换为 C# 和 Xamarin.iOS:

- [FourInARow： 用于对手 AI GameplayKit Minmax 规划师](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog： 使用代理系统中 GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots： 生成 SpriteKit 和 GameplayKit 的跨平台游戏](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

在 iOS 9，Apple 几个更改和添加对进行裸机以提供对 GPU 开销较低的访问。 使用配置文件可以最大化的图形和计算可能您 iOS 应用程序。

裸机 framework 包括以下新功能：

- 新私有和深度模具纹理适用于 OS X。
- 使用深度夹紧和单独前端和后模具值改进了卷影质量。
- 裸机的明暗度语言和金属标准库改进。
- 计算着色器支持广泛的像素格式。

### <a name="the-metalkit-framework"></a>MetalKit Framework

MetalKit framework 提供了一套实用工具类和功能来减少的 iOS 应用中使用配置文件所需的工作。 MetalKit 提供三个主要区域的支持：

1. 从各种源包括常见的格式，如 PNG、 JPEG、 KTX 和 PVR 加载异步纹理。
2. 轻松访问的模型 I/O 基于裸机特定模型处理的资产。 这些功能已高度优化，可提供高效的数据模型 I/O 网格和裸机缓冲区之间的传输。
3. 预定义的裸机视图和视图管理来大幅减少所显示的 iOS 应用程序中的图形呈现所需的代码。

若要了解有关 MetalKit 的详细信息，请参阅 Apple 的[MetalKit Framework 参考](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356)，[金属编程指南](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)，[金属 Framework 参考](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161)和[裸机明暗度语言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

### <a name="metal-performance-shaders-framework"></a>裸机性能着色器 Framework

裸机性能着色器 framework 提供了一条高度优化组图形和计算基于在你配置文件中使用的着色器基于的 iOS 应用。 每个已专门优化 framework 在裸机上提供高性能金属性能着色器中的着色器支持 iOS Gpu。

通过使用裸机性能着色器类，可以实现可能在每个特定 iOS GPU 上的最高性能，而无需为目标，维护单个代码基。 裸机性能着色器可以用于任何裸机的资源，例如纹理和缓冲区。

裸机性能着色器 framework 提供了一套常见的着色器，如：

- **高斯模糊**(`MPSImageGaussianBlur`)
- **Sobel 边缘检测**(`MPSImageSobel`)
- **图像直方图**(`MPSImageHistogram`)

有关详细信息，请参阅 Apple 的[金属明暗度语言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

## <a name="introducing-model-io"></a>引入模型 I/O

Apple 的模型 I/O framework 提供了深入了解的三维资产 （如模型和其相关的资源）。 模型 I/O 提供与基于物理材料，模型和可以用于 GameplayKit、 金属和 SceneKit 的照明 iOS 游戏。

与模型 I/O 可以支持以下类型的任务：

- 导入照明材料，网格数据、 照相机设置和中的各种流行的软件和游戏引擎格式的其他基于场景的信息。
- 处理或生成基于场景的信息，如： 分步创建纹理 sky 圆顶或烘烤糕点照明到网格。
- MetalKit、 SceneKit 和 GLKit 有效地加载到 GPU 呈现的缓冲区的游戏资产与一起使用。
- 将基于场景的信息导出为各种流行的软件和游戏引擎格式。

若要了解有关模型 I/O 的详细信息，请参阅 Apple 的[模型 I/O Framework 参考](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>引入 ReplayKit

Apple 的新 ReplayKit 框架使您可以轻松地将玩游戏的记录添加到你的 iOS 游戏，并允许用户快速轻松地编辑和共享在应用内的从该视频。

有关详细信息，请参阅 Apple 的[ReplayKit 和游戏中心视频转社交](https://developer.apple.com/videos/wwdc/2015/?id=605)及其[DemoBots： 构建跨平台游戏 SpriteKit 与 GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)示例应用程序。

## <a name="scenekit"></a>SceneKit

场景工具包是一个三维场景图形 API，用于简化 3D 图形。 它在 OS X 10.8，首次引入，并具有现在进入 iOS 8。 使用场景工具包创建沉浸式 3D 可视化效果和休闲 3D 游戏不需要 OpenGL 的专业知识。 常见场景 graph 概念上构建，场景工具包简化 OpenGL 和 OpenGL ES，使其很容易添加三维内容对的应用程序。 但是，如果你是 OpenGL 方面的专家，场景工具包已针对直接与以及 OpenGL 的全力支持。 它还包括许多功能，补充 3D 图形，如物理，并与多个其他 Apple 框架，例如核心动画、 Core 映像和画面工具包很好地集成。

有关详细信息，请参阅我们[SceneKit](~/ios/platform/gaming/scenekit.md)文档。

### <a name="scenekit-changes"></a>SceneKit 更改

Apple 已添加了以下新功能到 SceneKit ios 9:

- Xcode 现在提供了一种场景编辑器，可以快速生成游戏和三维的交互式应用程序，通过编辑直接从在 Xcode 内的场景。
- `SCNView`和`SCNSceneRenderer`类可以用于启用 （在受支持的 iOS 设备） 的裸机呈现。
- `SCNAudioPlayer`和`SCNNode`类可以用于添加自动跟踪对 iOS 应用程序的播放器位置的空间音频效果。

有关详细信息，请参阅我们[SceneKit 文档](~/ios/platform/introduction-to-ios8.md#scenekit)和 Apple 的[SceneKit Framework 参考](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283)和[Fox： 生成 SceneKit 游戏使用 Xcode 场景编辑器](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154)示例项目。

## <a name="spritekit"></a>SpriteKit

动画层工具包中，从 Apple 中的 2D 游戏 framework 具有 iOS 8 和 OS X Yosemite 中的一些有趣的新功能。 其中包括与场景工具包、 着色器支持、 照明、 阴影、 约束、 正常映射生成和物理增强功能的集成。 具体而言，新的物理功能使其很容易添加游戏逼真的效果。

有关详细信息，请参阅我们[SpriteKit](~/ios/platform/gaming/spritekit.md)文档。

### <a name="spritekit-changes"></a>SpriteKit 更改

Apple 已添加了以下新功能到 SpriteKit ios 9:

- 自动跟踪玩家的位置使用的空间音频效果`SKAudioNode`类。
- Xcode 现在具有场景编辑器和轻松的二维游戏和应用程序创建的操作编辑器功能。
- 轻松滚动游戏支持新的相机节点 (`SKCameraNode`) 对象。
- 在支持金属的 iOS 设备上, SpriteKit 将自动使用它以进行呈现，即使你已在使用自定义的 OpenGL ES 着色器。

有关详细信息，请参阅我们[SpriteKit 文档](~/ios/platform/introduction-to-ios8.md#spritekit)Apple 的[SpriteKit Framework 参考](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041)及其[DemoBots： 构建跨平台游戏与 SpriteKit 和GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)示例应用程序。

## <a name="summary"></a>摘要

本文已覆盖新游戏功能该 iOS 9 提供有关 Xamarin.iOS 应用程序。
它引入 GameplayKit 和模型 I/O;裸机; 主要增强功能和 SceneKit 和 SpriteKit 的新功能。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
