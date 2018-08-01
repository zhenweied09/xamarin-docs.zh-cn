---
title: 动画处理与 CCAction
description: CCAction 类简化了添加动画到 CocosSharp 游戏。 若要实现功能或添加波兰语，则可以使用这些动画。
ms.prod: xamarin
ms.assetid: 74DBD02A-6F10-4104-A61B-08CB49B733FB
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: b6209816f741423f40945a0fe4391fe921cb35de
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921782"
---
# <a name="animating-with-ccaction"></a>动画处理与 CCAction

_CCAction 类简化了添加动画到 CocosSharp 游戏。若要实现功能或添加波兰语，则可以使用这些动画。_

`CCAction` 是基类，用于对 CocosSharp 对象进行动画处理。 本指南涵盖内置`CCAction`实现常见任务，例如定位、 缩放和旋转。 它还讨论如何创建自定义实现通过继承`CCAction`。

本指南使用一个名为项目**ActionProject**其中[可以在此处下载](https://developer.xamarin.com/samples/mobile/CCAction)。 本指南使用`CCDrawNode`类，该类中介绍了[与 CCDrawNode 绘制几何图形](~/graphics-games/cocossharp/ccdrawnode.md)指南。


## <a name="running-the-actionproject"></a>运行 ActionProject

**ActionProject**是可以针对 iOS 和 Android 生成一 CocosSharp 解决方案。 有关如何使用的代码示例既可以它`CCAction`类和为常见的实时演示`CCAction`实现。

运行时，ActionProject 显示三个`CCLabel`左侧的屏幕和绘制由两个视觉对象的实例`CCDrawNode`用于查看各种操作的实例：

![](ccaction-images/image1.png "ActionProject 屏幕和绘制由两个 CCDrawNode 实例用于查看各种操作的视觉对象的左侧显示三个 CCLabel 实例")

在左侧的标签指示哪种类型的`CCAction`后点击屏幕上，将创建。 默认情况下，**位置**选择值，从而导致`CCMove`操作正在创建并应用到的红色圆圈：

![](ccaction-images/image2.gif "选择了位置值，从而导致 CCMove 操作正在创建并应用到的红色圆圈")

单击左侧的标签更改哪种类型的`CCAction`在圆上执行。 例如，单击**位置**标签将循环浏览不同的值，可以更改：

![](ccaction-images/image3.gif "单击位置标签将循环浏览不同的值，可以更改")

## <a name="common-variable-changing-ccaction-classes"></a>常见变量更改 CCAction 类

**ActionProject**使用以下`CCAction`-继承类，该类属于 CocosSharp:

 - `CCMoveTo` – 更改`CCNode`实例的`Position`属性
 - `CCScaleTo` – 更改`CCNode`实例的`Scale`属性
 - `CCRotateTo` – 更改`CCNode`实例的`Rotation`属性

本指南是指作为这些操作*变量更改*，这意味着它们直接影响的变量`CCNode`添加到。 其他类型的操作称为*缓动*操作，如在本指南后面所述。

**ActionProject**演示这些操作旨在随着时间的推移修改变量。 具体而言，这些`CCActions`构造函数采用两个参数： 现在可以和要分配的值的长度。 下面的代码段演示如何创建操作的三种类型：


```csharp
switch (VariableOptions [currentVariableIndex])
{
    case "Position":
        coreAction = new CCMoveTo(timeToTake, touch.Location);

        break;
    case "Scale":
        var distance = CCPoint.Distance (touch.Location, drawNodeRoot.Position);
        var desiredScale = distance / DefaultCircleRadius;
        coreAction = new CCScaleTo(timeToTake, desiredScale);

        break;
    case "Rotation":
        float differenceY = touch.Location.Y - drawNodeRoot.PositionY;
        float differenceX = touch.Location.X - drawNodeRoot.PositionX;

        float angleInDegrees = -1 * CCMathHelper.ToDegrees(
            (float)System.Math.Atan2(differenceY, differenceX));

        coreAction = new CCRotateTo (timeToTake, angleInDegrees);

        break; 
...
}
```

操作创建后，它添加到 CCNode，如下所示：


```csharp
nodeToAddTo.AddAction (coreAction); 
```

`AddAction` 启动`CCAction`实例的行为和它将自动执行其逻辑后逐帧直至完成为止。

每个类型包含的单词的结束上面列出*到*这意味着`CCAction`将修改`CCNode`以便完成操作后，自变量值表示的最终状态。 例如，创建`CCMoveTo`X 的位置 = 100 和 Y = 200 将导致`CCNode`实例的`Position`被设置为 X = 100，Y 指定，而不考虑时间的末尾 = 200`CCNode`实例的起始位置。

每个"To"类还具有"通过"版本，这样会将自变量值的当前值添加上`CCNode`。 例如，创建`CCMoveBy`X 的位置 = 100 和 Y = 200 将导致`CCNode`正在从到正位于发起操作时的位置中移动到的右 100 单元和向上 200 个单位的实例。


## <a name="easing-actions"></a>缓动操作

默认情况下，变量更改操作将执行*线性内插*– 操作将逐渐实现以恒定速度所需的值。 如果插入*位置*而呈线性增长，移动的对象将立即启动和停止开头和末尾相应的操作、 移动和其速度将保持不变操作执行。 

非线性内插不太快，并添加波兰语的元素，因此 CocosSharp 提供了各种各样的缓动操作用于修改变量更改操作。

在**ActionProject**示例中，我们可以切换通过单击第二个标签的缓动操作这些类型 (使用默认**<None>**):

![](ccaction-images/image4.gif "用户可以通过单击第二个标签的缓动操作这些类型之间进行切换")

缓动操作是特别强大，因为它们不会与任何特定的变量设置操作关联。 这意味着，可以使用相同的缓动操作来分派位置、 旋转、 缩放或自定义操作 （如也会显示在本指南后面）。

缓动操作包装变量设置的操作 (处理程序，但前提是继承自变量设置操作`CCFiniteTimeAction`) 通过接受其构造函数中的自变量作为一个变量设定的操作。

例如，如果标签设置为**位置**， **CCEaseElastic**，然后检测到触摸屏输入时，将执行下面的代码 （请注意代码已被省略以突出显示的相关行）：


```csharp
CCFiniteTimeAction coreAction = null; 
...
coreAction = new CCMoveTo(timeToTake, touch.Location); 
...
CCAction easing = null; 
...
easing = new CCEaseSineOut (coreAction); 
...
nodeToAddTo.AddAction (easing); 
```

应用程序所示，确切的相同缓动可应用于其他变量设置操作如`CCRotateTo`:

![](ccaction-images/image5.gif "确切的相同缓动可以应用于其他变量设置操作，例如 CCRotateTo")


## <a name="easing-in-out-and-inout"></a>缓动 In、 Out 和 InOut

所有的缓动操作都有`In`， `Out`，或`InOut`追加到缓动类型。 缓动应用时这些术语是指：`In`意味着缓动将开始时，应用`Out`在结束时，意味着和`InOut`意味着同时开头和结尾处。

`In`缓动操作将影响变量可应用于整个 （不管是在开头和末尾），整个内插的方式，但通常的缓动操作的最可识别特征会进行开头。 同样，`Out`缓动操作的特征是内插的末尾时其行为。 例如，`CCEaseBounceOut`将导致对象会在操作结束传来传去。


### <a name="out"></a>Out

`Out` 通常缓动适用在内插末尾最明显的更改。 例如，`CCEaseExponentialOut`接近目标值，则会减慢的不断变化的变量的变动率：

![](ccaction-images/image6.gif "CCEaseExponentialOut 会减慢更改变量的变动率的接近目标值")


### <a name="in"></a>内

`In` 通常缓动适用开头的内插最值得注意的更改。 例如，`CCEaseExponentialIn`将更慢移动操作的开始处：

![](ccaction-images/image7.gif "CCEaseExponentialIn 将更慢移动操作的开始处")


### <a name="inout"></a>InOut

`InOut` 通常将应用同时在起点和终点最为明显更改。 `InOut` 缓动是通常对称的。 例如，`CCEaseExponentialInOut`将慢慢地移动开头和末尾操作：

![](ccaction-images/image8.gif "CCEaseExponentialInOut 将慢慢地移动开头和末尾操作")


## <a name="implementing-a-custom-ccaction"></a>实现自定义 CCAction

所有我们前面讨论过的类都将纳入 CocosSharp 以提供通用功能。 自定义`CCAction`实现可以提供更大的灵活性。 例如，`CCAction`它可以控制体验栏的填充的比率可以使用，以便每当用户赚取体验体验栏顺利地增长。

`CCAction` 实现通常需要两个类：

 - `CCFiniteTimeAction` 实现 – 有限时间操作类负责启动该操作。 它是实例化的类，或者直接添加到`CCNode`或的缓动操作。 它必须实例化并返回`CCFiniteTimeActionState`，这将执行更新。
 - `CCFiniteTimeActionState` 实现 – 有限时间操作状态类负责更新操作中涉及的变量。 它必须实现一个更新的函数，它分配的时间值根据目标上的值。 此类外部的没有显式引用`CCFiniteTimeAction`这将创建它。 它只需在"后台"运行。

**ActionProject**提供一个自定义`CCFiniteTimeAction`实现调用`LineWidthAction,`用于调整绘制之上的红色圆圈的黄色线条的宽度。 请注意，其唯一作业是实例化并返回`LineWidthState`实例：


```csharp
public class LineWidthAction : CCFiniteTimeAction
{
    float endWidth;

    public LineWidthAction (float duration, float width) : base(duration)
    {
        endWidth = width;
    }

    public override CCFiniteTimeAction Reverse ()
    {
        throw new NotImplementedException ();
    }

    protected override CCActionState StartAction (CCNode target)
    {
        return new LineWidthState (this, target, endWidth);
    }
}
```

如上所述，`LineWidthState`执行的工作的分配的行`Width`属性根据多少`time`已通过：


```csharp
public class LineWidthState : CCFiniteTimeActionState
{
    float deltaWidth;
    float startWidth;

    LineNode castedTarget;

    public LineWidthState(LineWidthAction action, CCNode target, float endWidth) : base(action, target)
    {
        castedTarget = target as LineNode;

        if (castedTarget == null)
        {
            throw new InvalidOperationException ("The argument target must be a LineNode");
        }

        startWidth = castedTarget.Width;
        deltaWidth = endWidth - startWidth;
    }

    public override void Update (float time)
    {
        castedTarget.Width = startWidth + deltaWidth * time;
    }
} 
```

可以与任何缓动操作更改的线条宽度，以各种方式组合 LineWidthAction，如下面的动画中所示：

![](ccaction-images/image9.gif "此动画中所示，可以与任何缓动操作更改的线条宽度，以各种方式组合 LineWidthAction")


### <a name="interpolation-and-the-update-method"></a>内插和更新方法

除了将值存储在上面的类外的唯一逻辑驻留在`LineWidthState.Update`方法。 `startWidth`变量存储目标的宽度`LineNode`操作开始时和`deltaWidth`变量存储量的值将更改操作的过程。

通过用替换来`time`变量替换值为 0，我们可以看到目标`LineNode`将在它的起始位置上：


```csharp
castedTarget.Width = startWidth + deltaWidth * 0; 
```

同样，我们可以看到目标`LineNode`是其目标中的替换值为 1 时变量：


```csharp
castedTarget.Width = startWidth + deltaWidth * 1; 
```

`time`值通常为介于 0 和 1-之间但不是总是-和`Update`实现不应假定这些边界。 某些缓动方法 (如`CCEaseBackIn`和`CCEaseBackOut`) 将提供的 0 到 1 范围之外的时间值。


## <a name="conclusion"></a>结束语

内插和缓动是创建精美的游戏，尤其是在创建用户界面的关键部分。 本指南介绍如何使用`CCActions`进行插值如位置和旋转的标准值以及自定义值。 `LineWidthState`和`LineWidthAction`类演示如何实现自定义操作。

## <a name="related-links"></a>相关的链接

- [CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction)
- [CCMoveTo](https://developer.xamarin.com/api/type/CocosSharp.CCMoveTo)
- [CCScaleTo](https://developer.xamarin.com/api/type/CocosSharp.CCScaleTo)
- [CCRotateTo](https://developer.xamarin.com/api/type/CocosSharp.CCRotateTo)
- [CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode)
- [完整示例](https://developer.xamarin.com/samples/mobile/CCAction/)
