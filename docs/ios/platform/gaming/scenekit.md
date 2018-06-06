---
title: 在 Xamarin.iOS SceneKit
description: 本文档介绍 SceneKit，三维场景 graph API，用于简化 3D 图形的抽象化 OpenGL 的复杂性。
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: fb72e194e14f903061e1bd2dc6d04ef88ab429d4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786780"
---
# <a name="scenekit-in-xamarinios"></a>在 Xamarin.iOS SceneKit

SceneKit 是一个三维场景图形 API，用于简化 3D 图形。 它在 OS X 10.8，首次引入，并具有现在进入 iOS 8。 与 SceneKit 创建沉浸式 3D 可视化效果和休闲 3D 游戏不需要 OpenGL 的专业知识。 常见场景 graph 概念上构建，SceneKit 简化 OpenGL 和 OpenGL ES，使其很容易添加三维内容对的应用程序。 但是，如果你是 OpenGL 方面的专家，SceneKit 具有针对直接与以及 OpenGL 的全力支持。 它还包括许多功能，补充 3D 图形，如物理，并与多个其他 Apple 框架，例如核心动画、 Core 映像和画面工具包很好地集成。

SceneKit 是非常容易地使用。 它是负责呈现的声明性 API。 你只需设置场景，将属性添加到它，以及 SceneKit 句柄的呈现的场景。

若要使用 SceneKit 你创建场景图使用`SCNScene`类。 场景包含节点，由的实例表示的层次结构`SCNNode`，定义在三维空间中的位置。 每个节点具有 geometry、 照明等材料影响其外观的属性，如下图所示：

![](scenekit-images/image7.png "SceneKit 层次结构") 

## <a name="create-a-scene"></a>创建场景

若要使在屏幕上显示的场景，你将其添加到`SCNView`将它分配给该视图的场景属性。 此外，如果对场景，进行任何更改`SCNView`将更新自己以显示所做的更改。

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

可以填充场景，从通过建模工具，三维导出的文件或以编程方式从几何基元。 例如，这是如何创建移动球体和将其添加到场景：

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>添加 Light

此时球体不会显示任何内容因为场景中没有任何 light。 附加`SCNLight`到节点的实例在 SceneKit 中创建灯。 有几种类型的范围从各种形式的方向照明到环境的照明的灯。 例如，下面的代码创建一侧球体无定向 light:

```csharp
// omnidirectional light
var light = SCNLight.Create ();
var lightNode = SCNNode.Create ();
light.LightType = SCNLightType.Omni;
light.Color = UIColor.Blue;
lightNode.Light = light;
lightNode.Position = new SCNVector3 (-40, 40, 60);
scene.RootNode.AddChildNode (lightNode);
```

无定向照明生成类似于用手电筒有点导致甚至照明漫射反射。 创建环境光线非常相似，尽管它具有没有方向，因为它达到同样所有方向。 其想像语气:) 照明

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

与就地灯，球体现场景中可见。

![](scenekit-images/image8.png "球体是场景时亮起中可见")
 
## <a name="adding-a-camera"></a>添加相机

将照相机 (SCNCamera) 添加到场景更改的角度。 要添加相机的模式是类似的。 创建相机、 将其附加到节点并将节点添加到场景。

```csharp
// camera
camera = new SCNCamera {
    XFov = 80,
    YFov = 80
};
cameraNode = new SCNNode {
    Camera = camera,
    Position = new SCNVector3 (0, 0, 40)
};
scene.RootNode.AddChildNode (cameraNode);
```

正如您可以看到从上面的代码，可以使用构造函数创建对象的 SceneKit 或创建的工厂方法。 前者允许使用 C# 初始值设定项语法，但要使用哪一种很大程度的首选项。

与就地照相机，整个球体是对用户可见：

![](scenekit-images/image9.png "整个球体是对用户可见")
 
你可以向场景以及添加其他灯。 下面是如下所示使用少量的多个无定向灯：

![](scenekit-images/image10.png "有几个更多的全向光源球")
 
此外，通过设置`sceneView.AllowsCameraControl = true`，用户可以更改角度来看，与触摸手势。

### <a name="materials"></a>材料

使用 SCNMaterial 类创建材料。 若要添加到球的图面上的映像的示例，将图像设置针对相应材料*扩散*内容。

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

此层到节点上的映像，如下所示：

![](scenekit-images/image11.png "分层到球体上的映像")
 
可以设置材料响应其他类型的照明过。 例如，可以将对象变为亮和其反射内容设置为用来显示反射反射，导致亮点表面上看，如下所示：

![](scenekit-images/image12.png "进行亮使用反射反射，从而导致亮点图面上的对象")
 
材料是非常灵活，允许您将能够实现大量使用极少量的代码。 例如，而不是设置到漫射内容，将其设置为反射状内容相反。

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

现在 monkey 似乎内球体，独立的角度来看的直观地位于以下位置。

### <a name="animation"></a>动画

SceneKit 旨在很好地配合动画。 你可以创建隐式或显式动画，而甚至会使核心动画层树中的场景。 在创建时隐式动画，SceneKit 提供其自己的转换类， `SCNTransaction`。

下面是将旋转球体示例：

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

可以通过动画轮换比得多。 许多属性 SceneKit 是可进行动画处理。 例如，下面的代码进行动画处理的材料`Shininess`以提高反射的反射。

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

SceneKit 是非常简单，使用。 它提供了丰富的附加功能，包括约束、 物理、 声明性操作，三维文字、 字段支持、 画面工具包集成和 Core 映像集成来命名只需少量的深度。