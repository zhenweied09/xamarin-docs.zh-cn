---
title: "UrhoSharp 简介"
description: "这提供了 UrhoSharp 背后的概念的简要介绍"
ms.topic: article
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: 280ff20a6b45e4bfde984fc9c068e5c95276f9e9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="an-introduction-to-urhosharp"></a>UrhoSharp 简介

_这提供了 UrhoSharp 背后的概念的简要介绍_

![](introduction-images/urhosharp-icon.png "UrhoSharp 是 Xamarin 和.NET 开发人员的功能强大的三维游戏引擎")

UrhoSharp 是 Xamarin 和.NET 开发人员的功能强大的三维游戏引擎。  它在设计理念类似于 Apple 的 SceneKit 和 SpriteKit 并包括物理、 导航、 网络和很多更而仍跨平台。

它是对的.NET 绑定[Urho3D](http://urho3d.github.io/)引擎和使开发人员可以编写跨平台代码可以面向 Android、 iOS、 Windows 和 Mac 具有相同基本代码，并可以呈现为 OpenGL 和 Direct3D 系统。

UrhoSharp 是功能的游戏引擎包含大量现成:

 - 功能强大的三维图形呈现
- [物理学模拟](https://developer.xamarin.com/api/namespace/Urho.Physics/)（使用项目符号库）
- [场景处理](https://developer.xamarin.com/api/type/Urho.Scene/)
- 等待异步支持
- [友好操作 API](https://developer.xamarin.com/api/namespace/Urho.Actions/)
- [二维集成到三维场景](https://developer.xamarin.com/api/namespace/Urho.Urho2D/)
- [与 FreeType 字体呈现](https://developer.xamarin.com/api/type/Urho.Gui.FontFaceFreeType/)
- [客户端和网络功能的服务器](https://developer.xamarin.com/api/namespace/Urho.Network/)
- [导入广泛的资产](https://developer.xamarin.com/api/namespace/Urho.Resources/)（与打开的资产库）
- [导航网格和 pathfinding](https://developer.xamarin.com/api/namespace/Urho.Navigation/) （使用强制转换/Detour）
- [冲突检测的凸包生成](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/)（使用 StanHull）
- [音频播放](https://developer.xamarin.com/api/namespace/Urho.Audio/)(与**libvorbis**)

# <a name="getting-started"></a>入门

作为方便地分发 UrhoSharp [NuGet 包](https://www.nuget.org/)，它可以被添加到你的 C# 或 F # 项目面向 Windows、 Mac、 Android 或 iOS。  NuGet 随附于运行程序，所需的库以及引擎使用的基本资产 (出现 CoreData)。

## <a name="urho-as-a-portable-class-library"></a>Urho 作为可移植类库

可供 Urho 包从一个特定于平台的项目，或从可移植类库项目，从而可以在所有平台之间重用所有代码。  这意味着只需在每个平台上执行操作旨在编写平台的特定入口点，然后将控件传输到您共享的游戏代码。

## <a name="samples"></a>示例

可以通过打开适用于 Mac 的 Visual Studio 或 Visual Studio 中的示例解决方案从获取 Urho 功能偏好：

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

默认解决方案包含项目用于 Android、 iOS、 Windows 和 mac。  我们有结构化该解决方案，以便我们具有小平台的特定启动器，和的所有示例代码和游戏代码居住在可移植类库，说明如何在所有平台之间最大化代码重用。

请查阅[Urho 和你的平台](~/graphics-games/urhosharp/platform/index.md)有关如何创建自己的解决方案的详细信息页。

因为所有示例共享一组通用的用户界面元素，这些示例具有提取此文件中的基本设置：

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

这提供处理某些基本的键击操作的示例基类和 touch 相机的事件，设置，提供基本用户界面元素，这使得每个示例能够专注于所展示的特定功能。

下面的示例演示的引擎是能够这样做：

- [Samply 游戏](https://github.com/xamarin/urho-samples/tree/master/SamplyGame)ShootySkies 的简单克隆。

其他示例演示中的每个示例的各个属性。

# <a name="basic-structure"></a>基本结构

你的游戏应子类[ `Application` ](https://developer.xamarin.com/api/type/Urho.Application/)类，这是将在此安装程序你的游戏 (上[ `Setup` ](https://developer.xamarin.com/api/member/Urho.Application.Setup/)方法) 并启动你的游戏 (在[ `Start` ](https://developer.xamarin.com/api/member/Urho.Application.Start)方法)。  然后，你构造你的主要用户界面。  我们将演练演示如何设置三维场景，一些 UI 元素和将简单行为附加到它的 Api 的小型示例。

```csharp
class MySample : Application {
    protected override void Start ()
    {
        CreateScene ();
        Input.KeyDown += (args) => {
            if (args.Key == Key.Esc) Exit ();
        };
    }

    async void CreateScene()
    {
        // UI text
        var helloText = new Text()
        {
            Value = "Hello World from MySample",
            HorizontalAlignment = HorizontalAlignment.Center,
            VerticalAlignment = VerticalAlignment.Center
        };
        helloText.SetColor(new Color(0f, 1f, 1f));
        helloText.SetFont(
            font: ResourceCache.GetFont("Fonts/Font.ttf"),
            size: 30);
        UI.Root.AddChild(helloText);

        // Create a top-level scene, must add the Octree
    // to visualize any 3D content.
        var scene = new Scene();
        scene.CreateComponent<Octree>();
        // Box
        Node boxNode = scene.CreateChild();
        boxNode.Position = new Vector3(0, 0, 5);
        boxNode.Rotation = new Quaternion(60, 0, 30);
        boxNode.SetScale(0f);
        StaticModel modelObject = boxNode.CreateComponent<StaticModel>();
        modelObject.Model = ResourceCache.GetModel("Models/Box.mdl");
        // Light
        Node lightNode = scene.CreateChild(name: "light");
        lightNode.SetDirection(new Vector3(0.6f, -1.0f, 0.8f));
        lightNode.CreateComponent<Light>();
        // Camera
        Node cameraNode = scene.CreateChild(name: "camera");
        Camera camera = cameraNode.CreateComponent<Camera>();
        // Viewport
        Renderer.SetViewport(0, new Viewport(scene, camera, null));
        // Perform some actions
        await boxNode.RunActionsAsync(
            new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
        await boxNode.RunActionsAsync(
            new RepeatForever(new RotateBy(duration: 1,
                deltaAngleX: 90, deltaAngleY: 0, deltaAngleZ: 0)));
     }
}
```

如果你运行此应用程序，你很快就会发现运行时所抱怨资产不存在。  你需要做什么是开头的特殊目录名称"数据"，你项目中创建层次结构，在此操作，请将置于你在你的程序中引用的资产。  你必须设置在为每个资产"复制到输出目录"到"复制如果较新"项属性中，这将确保你的数据是否存在。

让我们解释什么转到此处。

若要启动应用程序，你调用引擎初始化函数，接着创建应用程序类，此类的新实例：

    new MySample().Run();

运行时将调用`Setup`和`Start`为你的方法。  如果你重写`Setup`你可以配置引擎参数 （不在此示例中所示）。

必须重写`Start`因为这将启动你的游戏。  在此方法将加载你的资产、 连接事件处理程序、 设置场景并启动所需的任何操作。  在我们示例中，我们这两种创建很少的 UI，以向用户，以及设置三维场景。

下面的代码段使用 UI 框架来创建的文本元素并将其添加到你的应用程序：

        // UI text
        var helloText = new Text()
        {
            Value = "Hello World from UrhoSharp",
            HorizontalAlignment = HorizontalAlignment.Center,
            VerticalAlignment = VerticalAlignment.Center
        };
        helloText.SetColor(new Color(0f, 1f, 1f));
        helloText.SetFont(
            font: ResourceCache.GetFont("Fonts/Font.ttf"),
            size: 30);
        UI.Root.AddChild(helloText);

它添加到新节点的工作原理和 UI 框架是否有提供非常简单的中的游戏用户界面， [ `UI.Root` ](https://developer.xamarin.com/api/property/Urho.Gui.UI.Root/)节点。

我们的示例设置的第二部分的主要场景。  这涉及到多个步骤，创建一个的三维场景，在屏幕中，创建一个三维框添加浅色、 相机和视区。  这些部分中的更详细地讨论"[场景、 节点、 组件和摄像头](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras)"

我们的示例中的第三个部分将触发几个操作。  操作是配方描述了特定的效果，并一次创建它们可以通过调用执行按需节点[ `RunActionAsync` ](https://developer.xamarin.com/api/member/Urho.Node.RunActionsAsync)方法`Node`。

第一项操作缩放弹跳效果包装盒和第二个永久旋转框中：

    await boxNode.RunActionsAsync(
        new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));

上图显示如何我们创建的第一个操作是[ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo/)操作，这是仅原因之一，该值指示你想要缩放为 1 的值向第二个节点的扩展属性。  此操作反过来包装在缓动操作， [ `EaseBounceOut` ](https://developer.xamarin.com/api/type/Urho.Actions.EaseBounceInOut/)操作。  缓动操作会误报线性执行操作，并应用效果，在这种情况下，它提供了会传来传去扩展效果。
因此，我们原因之一无法编写为：

    var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));

一旦创建食谱后，我们将执行原因之一：

    await boxNode.RunActionsAsync (recipe)

Await 指示将想要操作完成后恢复执行此行的后面。  操作完成后我们触发第二个动画。

[使用 UrhoSharp](~/graphics-games/urhosharp/using.md)文档探讨更深入的概念 Urho 以及如何构建你的代码以生成游戏。

# <a name="copyrights"></a>版权

本文档包含原始内容从 Xamarin Inc，但从 Urho3D 项目的开源文档进行了广泛绘制，包含从 Cocos2D 项目的屏幕快照。



## <a name="related-links"></a>相关链接

- [世界各地地球工作簿](https://developer.xamarin.com/workbooks/graphics/urhosharp/planetearth/planetearth.workbook)
- [浏览坐标工作簿](https://developer.xamarin.com/workbooks/graphics/urhosharp/coordinates/ExploringUrhoCoordinates.workbook)
