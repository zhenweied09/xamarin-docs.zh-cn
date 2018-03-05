---
title: "使用 UrhoSharp"
description: "UrhoSharp 引擎概述"
ms.topic: article
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: cd30f17d631216a3ea119a262aca6d294394a77f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="using-urhosharp"></a>使用 UrhoSharp

_UrhoSharp 引擎概述_

在编写你的第一个游戏之前，你想要获取掌握基础知识： 如何设置场景、 如何加载的资源 （包含图片） 以及如何创建简单的交互，为你的游戏。

<a name="scenenodescomponentsandcameras"/>

# <a name="scenes-nodes-components-and-cameras"></a>场景、 节点、 组件和摄像头

可以基于组件的场景图形描述场景模型。 场景包含节点的层次结构场景，从根节点，它还表示整个场景开始。 每个[ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/)具有三维转换 （位置、 旋转和缩放）、 名称、 ID，以及任意数量的组件。  组件将融入生活的节点，这些用户可添加可视表示形式 ([`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel))，它们可以发出声音 ([`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource))，它们可以提供冲突边界，依此类推。

你可以创建场景和安装程序节点上使用[Urho 编辑器](#UrhoEditor)，或你可以在 C# 代码中执行操作。  本文档中我们将探讨使用代码中，设置操作，如它们说明了获取要在你的屏幕上显示的事项所必需的元素

除了设置场景，你需要设置[ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/)，这是哪些因素决定了什么将获取向用户显示。

## <a name="setting-up-your-scene"></a>设置场景

您通常将创建此窗体 Start 方法：

```csharp
var scene = new Scene ();
// Create the Octree component to the scene. This is required before
// adding any drawable components, or else nothing will show up. The
// default octree volume will be from -1000, -1000, -1000) to
//(1000, 1000, 1000) in world coordinates; it is also legal to place
// objects outside the volume but their visibility can then not be
// checked in a hierarchically optimizing manner
scene.CreateComponent<Octree> ();
// Create a child scene node (at world origin) and a StaticModel
// component into it. Set the StaticModel to show a simple plane mesh
// with a "stone" material. Note that naming the scene nodes is
// optional. Scale the scene node larger (100 x 100 world units)
var planeNode = scene.CreateChild("Plane");
planeNode.Scale = new Vector3 (100, 1, 100);
var planeObject = planeNode.CreateComponent<StaticModel> ();
planeObject.Model = ResourceCache.GetModel ("Models/Plane.mdl");
planeObject.SetMaterial(ResourceCache.GetMaterial("Materials/StoneTiled.xml"));
```

## <a name="components"></a>组件数

呈现三维对象、 声音播放、 物理和脚本化的逻辑更新全部启用通过为节点创建不同的组件，通过调用[ `CreateComponent<T>()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateComponent%3CT%3E/p/Urho.CreateMode/System.UInt32/)。  例如，设置你的节点和浅色组件如下：

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

我们已创建具有该名称节点的上方"`DirectionalLight`"并将方向设置为，但没有其他任何内容。  现在，我们可以将上面的节点转换 light 发出节点中，通过将附加[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/)组件与它，与`CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

创建到组件`Scene`本身具有特殊的角色： 若要实现场景级功能。 它们应在所有其他组件之前创建，并且包括以下各项：

* [`Octree`](https://developer.xamarin.com/api/type/Urho.Octree/)： 实现空间分区并加速可见性查询。 而无需此三维不可以呈现对象。
* [`PhysicsWorld`](https://developer.xamarin.com/api/type/Urho.Physics.PhysicsWorld/)： 实现物理模拟。 如物理学组件[ `RigidBody` ](https://developer.xamarin.com/api/type/Urho.Physics.RigidBody/)或[ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/)可以不正常未对此进行的工作。
* [`DebugRenderer`](https://developer.xamarin.com/api/type/Urho.DebugRenderer/)： 实现调试几何图形呈现。

普通的组件，如[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light)， [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera)或[ `StaticModel` ](https://developer.xamarin.com/api/type/Urho.StaticModel)不应直接创建[ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene)，但而是为子节点。

库附带的组件可以附加到你的节点，以使它们变得生动各种： 用户可见的元素 （模型）、 声音、 刚性正文、 冲突形状、 摄像头、 光源、 粒子发射器和更多内容。

## <a name="shapes"></a>形状

为方便起见，各种形状均显示为 Urho.Shapes 命名空间中的简单节点。  其中包括框、 球体、 圆锥、 柱面和平面。

## <a name="camera-and-viewport"></a>摄像头和视区

就像光一样摄像头是组件，因此你将需要将该组件附加到节点，这可以如下所示：

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

与此，你已创建相机、 和中 3D world 停放照相机下, 一步是通知`Application`，这你想要使用的照相机，这是替换为以下代码：

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

并且现在你应该能够看到你创建的结果。

## <a name="identification-and-scene-hierarchy"></a>标识和场景层次结构

与节点，不同组件没有名称;在同一节点内的组件的唯一标识是通过其类型，并在该节点的组件列表，按创建顺序填充的索引，例如，可以检索[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light)外组件`lightNode`对象更高版本如下所示：

```csharp
var myLight = lightNode.GetComponent<Light>();
```

你也可以通过检索可以获得的所有组件列表[ `Components` ](https://developer.xamarin.com/api/property/Urho.Node.Components/)属性返回`IList<Component>`，你可以使用。

在创建时，节点和组件将获取场景全局整数 Id。 它们可以从场景查询，通过使用函数[ `GetNode(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetNode/p/System.UInt32/)和[ `GetComponent(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetComponent/p/System.UInt32/)。 这是比例如执行递归基于名称的场景节点的查询快得多。

没有内置的实体或游戏对象; 概念相反，它是到需要程序员来决定节点层次结构，并将任何已编写脚本的逻辑的节点中。 通常情况下，作为根节点的子级创建的移动 3D 生活中的对象。 可以使用或不使用的名称，可以创建节点[ `CreateChild()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateChild/p/System.String/Urho.CreateMode/System.UInt32/)。 不强制使用节点名称的唯一性。

只要某些层次结构的组合，它是建议 （和事实上必需的因为组件不具有其自己三维转换） 来创建一个子节点。

例如如果字符已在其现有持有一个对象，该对象应具有其自己的节点，将成为其父级的字符的现有骨骼 (还[ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/))。  例外情况是物理[ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape)，它可以是 offsetted 和旋转单独相对于节点。

请注意， [ `Scene`](https://developer.xamarin.com/api/type/Urho.Node/)的拥有时计算的子节点的派生的世界转换，转换将特意忽略作为一种优化，因此无法更改它有影响，并且它应保持原样 （来源，无旋转位置无缩放。)

[`Scene`](https://developer.xamarin.com/api/type/Urho.Node/) 可以自由地父级节点。 与此相反组件始终属于到它们附加到，并且不可以节点之间移动的节点。 节点和组件提供[ `Remove()` ](https://developer.xamarin.com/api/member/Urho.Node.Remove()/)函数来实现此目的而无需经历父级。 节点删除之后，不将会在调用该函数后安全问题的组件在节点上的任何操作。

还有可能创建`Node`，不属于场景。 这很有用例如带相机的场景可能加载或保存，在移动，因为然后相机以及实际的场景中，将不保存并加载场景时不会被破坏。 但请注意，创建几何图形、 物理或脚本组件未附加的节点，然后将其移到场景更高版本将导致这些组件不正常工作。

## <a name="scene-updates"></a>场景更新

启用其更新的场景 （默认值） 将在每个主要的循环迭代上自动更新。  应用程序的[ `SceneUpdate` ](https://developer.xamarin.com/api/event/Urho.Scene.SceneUpdate/)对其调用事件处理程序。

节点和组件可以通过禁用这些从场景更新中排除，请参阅[ `Enabled` ](https://developer.xamarin.com/api/member/Urho.Node.Enabled)。  行为取决于特定组件，但例如禁用可绘制组件还会使它不可见的而禁用声音源组件使它静音。 如果禁用了节点，所有其组件将被视为为禁用，而不考虑自己启用/禁用状态。

# <a name="adding-behavior-to-your-components"></a>将行为添加到你的组件

构建你的游戏的最佳方法是使您自己的组件，用于封装各种 actor 或在你的游戏的元素。  这使得该功能自包含从用于显示，其行为中的资产。

将行为添加到组件的最简单方法是使用操作，它们是你可以进行排队并结合起来使用 C# 异步编程中的说明。  这允许让组件能够非常高的级别的行为，并使其更易于了解发生了什么情况。

或者，你可以控制完全会发生什么情况组件通过更新你的组件属性 （在基于框架的行为部分中讨论） 每个帧上。

## <a name="actions"></a>操作

你可以将行为添加到节点非常轻松地使用操作。  操作可更改各种节点属性和执行它们的时间，一段时间内或重复这些步骤与给定的动画曲线次数。

例如，考虑场景上的"云"节点，你可以淡入淡出它如下：

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

操作是不可变对象，你可以重复使用用于驱动不同的对象的操作。

一个常用方法是创建执行反向操作的操作：

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

下面的示例将淡入淡出对象为你的三秒一段时间内。  你还可以运行一个操作后另一个字符串，例如，你可以首先移动云，并再隐藏它：

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

如果你想要在同一时间发生这两个操作，你可以使用的并行操作，并且提供所有的操作所需并行完成：

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

在上面的示例云移动和在同一时间淡出。

你将注意到，将使用这些 C# 等待，这允许你以线性方式考虑你想要实现的行为。

## <a name="basic-actions"></a>基本操作

这些是在 UrhoSharp 中受支持的操作：

* 移动节点： [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo)， [ `MoveBy` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveBy)， [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place)， [ `BezierTo` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierTo)， [ `BezierBy` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierBy), [`JumpTo`](https://developer.xamarin.com/api/type/Urho.Actions.JumpTo), [`JumpBy`](https://developer.xamarin.com/api/type/Urho.Actions.JumpBy)
* 轮换节点： [ `RotateTo` ](https://developer.xamarin.com/api/type/Urho.Actions.RotateTo)， [`RotateBy`](https://developer.xamarin.com/api/type/Urho.Actions.RotateBy)
* 缩放节点： [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo)， [`ScaleBy`](https://developer.xamarin.com/api/type/Urho.Actions.ScaleBy)
* 淡出节点： [ `FadeIn` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeIn)， [ `FadeTo` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeTo)， [ `FadeOut` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeOut)， [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide)， [`Blink`](https://developer.xamarin.com/api/type/Urho.Actions.Blink)
* 色调： [ `TintTo` ](https://developer.xamarin.com/api/type/Urho.Actions.TintTo)， [`TintBy`](https://developer.xamarin.com/api/type/Urho.Actions.TintBy)
* 时刻时刻： [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide)， [ `Show` ](https://developer.xamarin.com/api/type/Urho.Actions.Show)， [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place)， [ `RemoveSelf` ](https://developer.xamarin.com/api/type/Urho.Actions.RemoveSelf)， [`ToggleVisibility`](https://developer.xamarin.com/api/type/Urho.Actions.ToggleVisibility)
* 循环： [ `Repeat` ](https://developer.xamarin.com/api/type/Urho.Actions.Repeat)， [ `RepeatForever` ](https://developer.xamarin.com/api/type/Urho.Actions.RepeatForever)， [`ReverseTime`](https://developer.xamarin.com/api/type/Urho.Actions.ReverseTime)

其他高级的功能包括的组合[ `Spawn` ](https://developer.xamarin.com/api/type/Urho.Actions.Spawn)和[ `Sequence` ](https://developer.xamarin.com/api/type/Urho.Actions.Sequence)操作。

## <a name="easing---controlling-the-speed-of-your-actions"></a>缓动-控制你的操作的速度

缓动是指示动画将展开，和它可以使你动画变得更加愉快的方式的方法。  默认情况下你的操作会有线性的行为，例如[ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo)操作将具有非常自动移动。  你可以包装你的操作转换为一个缓动操作更改的行为，例如，另一个用于将缓慢启动移动，加快和渐变到达结尾 ([`EasyInOut`](https://developer.xamarin.com/api/type/Urho.Actions.EasyInOut))。

通过包装现有操作转换为一个缓动操作，例如来执行此操作：

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

有许多缓动模式，以下图表显示各种缓动类型以及它们的行为，它们控制的时间段内从开始到完成的对象的值：

![缓动模式](using-images/easing.png "此图显示的各种缓动类型以及它们的行为上的时间段内控制对象的值")

## <a name="using-actions-and-async-code"></a>使用操作和异步代码

在你[ `Component` ](https://developer.xamarin.com/api/type/Urho.Component/)子类，你应该在引入的异步方法，准备你的组件行为和驱动器有关它的功能。
然后将调用此方法使用 C#`await`关键字的程序，另一部分中任一你`Application.Start`方法或响应中你的应用程序的用户或情景点。

例如:

```csharp
class Robot : Component {
    public bool IsAlive;
    async void Launch ()
    {
        // Dress up our robot
        var cache = Application.ResourceCache;
        var model = node.CreateComponent<StaticModel>();
        model.Model = cache.GetModel ("robot.mdl"));
        model.SetMaterial (cache.GetMaterial ("robot.xml"));
        Node.SetScale (1);

        // Bring the robot into our scene
        await Node.RunActionsAsync(
            new MoveBy(duration: 0.6f, position: new Vector3(0, -2, 0)));
        // Make him move around to avoid the user fire
        MoveRandomly(minX: 1, maxX: 2, minY: -3, maxY: 3, duration: 1.5f);
        // And simultaneously have him shoot at the user
        StartShooting();
    }

    protected async void MoveRandomly (float minX, float maxX,
                                       float minY, float maxY,
                       float duration)
    {
        while (IsAlive){
            var moveAction = new MoveBy(duration,
                new Vector3(RandomHelper.NextRandom(minX, maxX),
                            RandomHelper.NextRandom(minY, maxY), 0));
            await Node.RunActionsAsync(moveAction, moveAction.Reverse());
        }
    }
    protected async void StartShooting()
    {
        while (IsAlive && Node.Components.Count > 0){
            foreach (var weapon in Node.Components.OfType<Weapon>()){
                await weapon.FireAsync(false);
                if (!IsAlive)
                    return;
            }
            await Node.RunActionsAsync(new DelayTime(0.1f));
        }
    }
}
```

在`Launch`启动上述三个操作方法： 自动机进入场景、 0.6 秒的一段时间内，此操作会更改节点的位置。  由于这是一个异步选项，这将会发生同时作为这是调用下一条指令到`MoveRandomly`。  此方法将改变机器人以并行方式对任意位置的位置。  这通过执行两个复合的操作，移动到新位置，并返回到原始位置并重复此操作，只要机器人保持活动状态。  以使操作更加有趣，机器人将保留解决同时。  解决才开始每隔 0.1 秒。

## <a name="frame-based-behavior-programming"></a>基于框架的行为编程

如果你想要控制而不是使用操作请逐个框架基于组件的行为，就像是重写[ `OnUpdate` ](https://developer.xamarin.com/api/member/Urho.Component.OnUpdate)方法你[ `Component` ](https://developer.xamarin.com/api/type/Urho.Component)子类。  此方法调用，一次每帧，且仅当 ReceiveSceneUpdates 属性设置为 true，则调用。

下面显示了如何创建`Rotator`组件，然后附加到一个节点，这会导致要旋转的节点：

```csharp
class Rotator : Component {
    public Rotator()
    {
        ReceiveSceneUpdates = true;
    }
    public Vector3 RotationSpeed { get; set; }
    protected override void OnUpdate(float timeStep)
    {
        Node.Rotate(new Quaternion(
            RotationSpeed.X * timeStep,
            RotationSpeed.Y * timeStep,
            RotationSpeed.Z * timeStep),
            TransformSpace.Local);
    }
}
```

这是如何，你会将此组件附加到的节点：

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

## <a name="combining-styles"></a>组合样式

可用于异步/操作基于模型的行为，这非常有用的编程中，即发即弃样式大部分编程，但你可以还精细调整您的组件的行为，还在每个帧中运行某些更新代码。

例如，在 SamplyGame 演示这用于`Enemy`类将编码的基本行为使用操作，但它还可确保组件通过设置的节点的方向点朝着用户`Node.LookAt`:

```csharp
    protected override void OnUpdate(SceneUpdateEventArgs args)
    {
        Node.LookAt(
            new Vector3(0, -3, 0),
            new Vector3(0, 1, -1),
            TransformSpace.World);
        base.OnUpdate(args);
    }
```

# <a name="loading-and-saving-scenes"></a>加载和保存场景

可以加载和保存为 XML 格式; 场景请参阅函数[ `LoadXml` ](https://developer.xamarin.com/api/member/Urho.Scene.LoadXml)和[ `SaveXML()` ](https://developer.xamarin.com/api/member/Urho.Scene.SaveXml)。 场景加载时，首先删除所有现有内容 （子节点和组件）。 节点和标记临时使用的组件`Temporary`属性将不会保存。 序列化程序处理所有内置组件和属性，但它不是足够智能，可处理自定义属性和组件子类中定义的字段。 但是，它为此提供两个虚方法：

* [`OnSerialize`](https://developer.xamarin.com/api/member/Urho.Component.OnSerialize) 可以在其中你注册你的序列化的自定义状态

* [`OnDeserialized`](https://developer.xamarin.com/api/member/Urho.Component.OnDeserialize) 从何处可以获取你已保存的自定义状态。

通常情况下，自定义组件将如下所示：

```csharp
class MyComponent : Component {
    // Constructor needed for deserialization
    public MyComponent(IntPtr handle) : base(handle) { }
    public MyComponent() { }
    // user defined properties (managed state):
    public Quaternion MyRotation { get; set; }
    public string MyName { get; set; }

    public override void OnSerialize(IComponentSerializer ser)
    {
        // register our properties with their names as keys using nameof()
        ser.Serialize(nameof(MyRotation), MyRotation);
        ser.Serialize(nameof(MyName), MyName);
    }

    public override void OnDeserialize(IComponentDeserializer des)
    {
        MyRotation = des.Deserialize<Quaternion>(nameof(MyRotation));
        MyName = des.Deserialize<string>(nameof(MyName));
    }
    // called when the component is attached to some node
    public override void OnAttachedToNode()
    {
        var node = this.Node;
    }
}
```

## <a name="object-prefabs"></a>对象 Prefabs

刚加载或保存整个场景不足够灵活，针对的游戏需要动态创建新对象。 另一方面，创建复杂的对象和在代码中设置其属性也将需要很长时间。 为此，还有可能保存其子节点、 组件和属性将包括一个场景节点。 作为一个组可以更高版本方便地加载这些功能。  此类已保存的对象通常称为 prefab。 有三种方法可以实现此操作：

- 在代码中通过调用[ `Node.SaveXml` ](https://developer.xamarin.com/api/member/Urho.Node.SaveXml)节点上
- 在编辑器中，通过在层次结构窗口中选择节点，然后选择"保存节点作为"从"文件"菜单。
- 使用中的"节点"命令`AssetImporter`、 哪些将存储场景节点层次结构和所有模型中的输入资产 （如都包含 Collada 文件）

若要实例化到场景的已保存的节点，请调用[ `InstantiateXml()` ](https://developer.xamarin.com/api/member/Urho.Scene.InstantiateXml)。 该节点将创建为子级的场景，但可以自由地父级之后。 位置和旋转放置节点需要指定。 下面的代码演示如何实例化 prefab`Ninja.xm`到与所需的位置和旋转场景：

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

# <a name="events"></a>事件

UrhoObjects 引发的事件数，这些将在生成它们的各种类显示为 C# 事件。  除了 C#-基于的事件模型中，还有可能要使用`SubscribeToXXX`方法，您可以订阅并将其保存订阅标记可更高版本用于取消订阅。  区别是，前者将允许订阅的多个调用方，而第二个仅允许一个，但允许不用去涉猎 lambda 样式着手，才能使用和棒的是，允许进行的订阅的轻松删除。  它们是互相排斥。

当你订阅事件时，你必须提供采用相应的事件自变量的参数的方法。

例如，这是你订阅了鼠标按钮按下事件的方式：

```csharp
public void override Start ()
{
    UI.MouseButtonDown += HandleMouseButtonDown;
}

void HandleMouseButtonDown(MouseButtonDownEventArgs args)
{
    Console.WriteLine ("button pressed");
}
```

使用 lambda 样式：

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

有时你将想要停止接收通知的事件，在这些情况下，保存返回值与调用`SubscribeTo`方法，并调用其上的取消订阅方法：

```csharp
Subscription mouseSub;

public void override Start ()
{
    mouseSub = UI.SubscribeToMouseButtonDown (args => {
    Console.WriteLine ("button pressed");
      mouseSub.Unsubscribe ();
    };
}
```

由事件处理程序接收的参数是一个强类型的事件自变量类是特定于每个事件，包括事件负载。

# <a name="responding-to-user-input"></a>响应用户输入

您可通过订阅事件，并且响应传递的输入来订阅下的各种事件，如键击：

```csharp
Start ()
{
    UI.KeyDown += HandleKeyDown;
}

void HandleKeyDown (KeyDownEventArgs arg)
{
     switch (arg.Key){
     case Key.Esc: Engine.Exit (); return;
}
```

但在许多情况下，你希望场景更新处理程序，当用户正在更新，并相应地更新你的代码时检查的键的当前状态。  例如，以下可以用于更新基于键盘输入的相机位置：

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX * moveSpeed * timeStep, TransformSpace.Local);
}
```

# <a name="resources-assets"></a>资源 （资产）

资源包括 UrhoSharp 过程初始化或运行时从大容量存储中加载的大多数操作：

- [`Animation`](https://developer.xamarin.com/api/type/Urho.Animation/) -用于主干动画
- [`Image`](https://developer.xamarin.com/api/type/Urho.Resources.Image) -表示存储在不同的图形格式的图像
- [`Model`](https://developer.xamarin.com/api/type/Urho.Model/) -三维模型
- [`Material`](https://developer.xamarin.com/api/type/Urho.Material) -用于呈现模型的材料。
- [`ParticleEffect`](https://developer.xamarin.com/api/type/Urho.ParticleEffect)- [描述](http://urho3d.github.io/documentation/1.4/_particles.html)粒子发射器的工作原理，请参阅"[粒子](#particles)"下面。
- [`Shader`](https://developer.xamarin.com/api/type/Urho.Shader) -自定义的着色器
- [`Sound`](https://developer.xamarin.com/api/type/Urho.Audio.Sound) -声音播放，请参阅"[声音](#sound)"下面。
- [`Technique`](https://developer.xamarin.com/api/type/Urho.Technique/) -材料呈现技术
- [`Texture2D`](https://developer.xamarin.com/api/type/Urho.Urho2D.Texture2D/) -二维纹理
- [`Texture3D`](https://developer.xamarin.com/api/type/Urho.Texture3D/) -三维纹理
- [`TextureCube`](https://developer.xamarin.com/api/type/Urho.TextureCube/) 的多维数据集纹理
- `XmlFile`

托管和加载的[ `ResourceCache` ](https://developer.xamarin.com/api/type/Urho.Resources.ResourceCache/)子系统 (可用作[ `Application.ResourceCache` ](https://developer.xamarin.com/api/property/Urho.Application.ResourceCache/))。

由它们相对于已注册的资源目录或包文件的文件路径标识资源本身。 默认情况下，该引擎注册的资源目录`Data`和`CoreData`，或包`Data.pak`和`CoreData.pak`如果它们存在。

如果加载资源失败，将记录错误并返回空引用。

下面的示例演示从资源缓存提取资源的典型方式。  在这种情况下，这使用的用户界面元素纹理，`ResourceCache`属性从`Application`类。

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

资源也可以手动创建和存储到资源缓存，如果它们过从磁盘中加载。

内存预算可以设置的每个资源类型： 如果资源占用较多的内存超过了允许，最旧的资源将删除从缓存中并且在使用不再。 默认情况下内存预算设置为不受限制。

## <a name="bringing-3d-models-and-images"></a>将三维模型和映像

Urho3D 将尝试使用现有的文件格式，只要有可能，并定义自定义文件格式如模型的绝对必要时仅 (*.mdl) 和用于动画 (*.ani)。 对于这些类型的资产，Urho 提供的转换器- [AssetImporter](http://urho3d.github.io/documentation/1.4/_tools.html)这可以使用多种常用的 3D 格式，如 fbx、 dae、 3ds，和 obj，等等。

此外还有方便外接程序 Blender [https://github.com/reattiva/Urho3D-Blender](https://github.com/reattiva/Urho3D-Blender) ，可以将导出格式，适用于 Urho3D Blender 资产。

## <a name="background-loading-of-resources"></a>资源的后台加载

通常情况下，请求使用其中一个的资源时`ResourceCache`的`Get`方法，它们是在主线程，可能会花几秒钟时间进行所有必需的步骤中立即加载 （从磁盘加载文件，分析数据，如有必要将上载到 GPU)，因此可能导致帧速率下降。

如果你知道提前你需要的哪些资源，你可以请求才能通过调用在后台线程中加载这些`BackgroundLoadResource()`。 你可以通过使用订阅资源后台加载事件`SubscribeToResourceBackgroundLoaded`方法。 它将告诉加载实际是否成功或失败。 根据资源，仅加载过程的一部分可能被移动到后台线程，例如完成 GPU 上载步骤始终需要在主线程中发生。 请注意，是否你调用的资源加载将排队，等待后台加载的资源的方法之一，主线程将挂起直到其加载已完成。

异步加载功能的场景`LoadAsync()`和`LoadAsyncXML()`具有第一次之前继续加载场景内容的资源的后台加载选项。 它还可以使用仅加载资源，而无需修改场景中，通过指定`LoadMode.ResourcesOnly`。 这样就可以准备快速实例化的场景或对象的 prefab 文件。

最后上花费的最大时间 （以毫秒为单位） 每个帧完成的后台加载的资源可以通过设置配置`FinishBackgroundResourcesMs`属性`ResourceCache`。

<a name="sound"/>

# <a name="sound"></a>声音

声音是玩游戏的重要组成部分，UrhoSharp framework 提供了一种方法在你的游戏播放声音。  通过附加播放声音[ `SoundSource` ](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource/)组件[ `Node` ](https://developer.xamarin.com/api/type/Urho.Node) ，然后播放你的资源从一个命名的文件。

这是它的工作原理：

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

# <a name="particles"></a>粒子

粒子提供一种简单的方法来向你的应用程序中添加一些简单和成本较低的影响。  你可以使用粒子 PEX 格式存储在使用像这样的工具[http://onebyonedesign.com/flash/particleeditor/](http://onebyonedesign.com/flash/particleeditor/)。

粒子是可以添加到节点的组件。  你需要调用该节点的`CreateComponent<ParticleEmitter2D>`方法来创建粒子，然后通过将影响属性设置为二维效果中配置粒子加载从资源缓存。

例如，你可以在组件以显示一些抵达时将呈现为爆炸式增长的粒子调用此方法：

```csharp
public async void Explode (Component target)
{
    // show a small explosion when the missile reaches an aircraft.
    var cache = Application.ResourceCache;
    var explosionNode = Scene.CreateChild();
    explosionNode.Position = target.Node.WorldPosition;
    explosionNode.SetScale(1f);
    var particle = explosionNode.CreateComponent<ParticleEmitter2D>();
    particle.Effect = cache.GetParticleEffect2D("explosion.pex");
    var scaleAction = new ScaleTo(0.5f, 0f);
    await explosionNode.RunActionsAsync(
        scaleAction, new DelayTime(0.5f));
    explosionNode.Remove();
}
```

上面的代码将创建附加到当前组件爆炸节点，此爆炸节点内，我们创建二维粒子发射器并通过将影响属性设置对其进行配置。  我们运行两个操作，一个缩放较小，节点，另一个 0.5 秒将其保留在该大小。  然后我们删除爆炸式增长，同时从屏幕中删除的粒子效果。

使用球纹理时，上述粒子呈现如下：

![用球纹理的粒子](using-images/image-1.png "使用球纹理时，上述粒子呈现如下")

这是如果你使用块状纹理它显示的内容：

![用框纹理的粒子](using-images/image-2.png "，这是如果使用块状纹理它显示的内容")

# <a name="multithreading-support"></a>多线程处理支持

UrhoSharp 是单个线程的库。  这意味着你不应尝试调用方法中 UrhoSharp 从后台线程，或风险会损坏应用程序状态，并可能导致你的应用程序的崩溃。

如果你想要在后台运行某些代码，然后更新上的主 UI Urho 组件，则可以使用[ `Application.InvokeOnMain(Action)` ](https://developer.xamarin.com/api/member/Urho.Application.InvokeOnMain)方法。  此外，你可以的使用 C# await 和.NET 任务 Api，以确保在合适的线程上执行的代码。


# <a name="urhoeditor"></a>UrhoEditor

你可以从平台下载 Urho 编辑器[Urho 网站](http://urho3d.github.io/)，转到下载并选择最新版本。

# <a name="copyrights"></a>版权

本文档包含原始内容从 Xamarin Inc，但从 Urho3D 项目的开源文档进行了广泛绘制，包含从 Cocos2D 项目的屏幕快照。



## <a name="related-links"></a>相关链接

- [世界各地地球工作簿](https://developer.xamarin.com/workbooks/graphics/urhosharp/planetearth/planetearth.workbook)
- [浏览坐标工作簿](https://developer.xamarin.com/workbooks/graphics/urhosharp/coordinates/ExploringUrhoCoordinates.workbook)
