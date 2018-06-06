---
title: 在 Xamarin.iOS SpriteKit
description: 本文档介绍 SpriteKit，Apple 的二维图形 framework 与 SceneKit 集成，包含物理和动画，包括支持照明和明暗度，等等。 SpriteKit 可用来创建二维游戏。
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: b74b5a722aab240b55ed96bea2a33b162d7817eb
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786764"
---
# <a name="spritekit-in-xamarinios"></a>在 Xamarin.iOS SpriteKit

SpriteKit，从 Apple 中的二维图形框架已在 iOS 8 和 OS X Yosemite 一些有趣的新功能。 其中包括与 SceneKit、 着色器支持、 照明、 阴影、 约束、 正常映射生成和物理增强功能的集成。 具体而言，新的物理功能使其很容易添加游戏逼真的效果。

## <a name="physics-bodies"></a>物理正文

SpriteKit 包括二维、 刚性正文物理 API。 每个动画层具有关联的物理主体 (`SKPhysicsBody`)，它在物理世界中定义的物理属性，如大容量和摩擦，以及正文的几何图形。

## <a name="creating-a-physics-body-from-a-texture"></a>从纹理创建物理正文
SpriteKit 现在支持从其纹理派生子画面物理正文。 这便于实现看起来更更自然的冲突。

例如，请注意，在以下冲突几乎在每个图像的图面香蕉和 monkey 的发生冲突：
 
![](spritekit-images/image13.png "几乎在每个图像的图面冲突的香蕉和 monkey")

SpriteKit 可创建此类物理正文，可以使用一行代码。 只需调用`SKPhysicsBody.Create`用纹理和大小： 画面。PhysicsBody = SKPhysicsBody.Create （画面。纹理，画面。大小）;

## <a name="alpha-threshold"></a>Alpha 阈值

除了只需设置`PhysicsBody`直接向派生自纹理的几何图形的属性，应用程序可以设置和 alpha 阈值来控制几何图形中派生的方式。 

Alpha 阈值定义像素必须具有要生成的物理正文中包括的最小 alpha 值。 例如，下面的代码将导致略有不同的物理正文：

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

调整如下的 alpha 阈值的效果 fine-tunes 以前的冲突，以便 monkey 会在时与香蕉冲突：

![](spritekit-images/image14.png "Monkey 会在时与香蕉冲突")
 
## <a name="physics-fields"></a>物理字段

另一个极好补充到 SpriteKit 是新的物理字段支持。 这些选项允许你添加等顶点字段径向重力字段和 spring 字段，只需少量的名称。

创建使用 SKFieldNode 类，该类添加到就像任何其他场景的物理字段`SKNode`。 上有大量的工厂方法`SKFieldNode`创建不同的物理字段。 你可以通过调用创建 spring 字段`SKFieldNode.CreateSpringField()`，通过调用径向重力字段`SKFieldNode.CreateRadialGravityField()`，依次类推。

`SKFieldNode` 此外具有属性来控制字段特性，例如字段强度、 字段区域和字段强制的衰减。

## <a name="spring-field"></a>Spring 字段

例如，以下代码创建 spring 字段，并将其添加到场景：

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

然后可添加动画层，并设置其`PhysicsBody`属性，使物理字段影响子画面，下面的代码一样在用户接触屏幕时：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    var touch = touches.AnyObject as UITouch;
    var pt = touch.LocationInNode (this);
    var node = SKSpriteNode.FromImageNamed ("TinyBanana");
    node.PhysicsBody = SKPhysicsBody.Create (node.Texture, node.Size);
    node.PhysicsBody.AffectedByGravity = false;
    node.PhysicsBody.AllowsRotation = true;
    node.PhysicsBody.Mass = 0.03f;
    node.Position = pt;
    AddChild (node);
}
```

这将导致香蕉振动字段节点周围 spring 类似：

![](spritekit-images/image15.png "香蕉振动如围绕字段节点 spring")
 
## <a name="radial-gravity-field"></a>径向重力字段

添加不同的字段是类似的。 例如，下面的代码创建径向重力字段：

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

这会导致不同的强制字段，其中香蕉径向提取有关的字段：

![](spritekit-images/image16.png "该字段周围径向拉取香蕉")
