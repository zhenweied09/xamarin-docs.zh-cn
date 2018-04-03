---
title: 性能和与 CCRenderTexture 的视觉效果
description: CCRenderTexture 使开发人员能够通过减少绘图调用，提高的 CocosSharp 游戏，性能和可用于创建视觉效果。 本指南附带 CCRenderTexture 示例提供如何有效地使用此类的实际操作示例。
ms.topic: article
ms.prod: xamarin
ms.assetid: F02147C2-754B-4FB4-8BE0-8261F1C5F574
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 36661344fc0f4b9e132e3f721c50f82f3a8db057
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="performance-and-visual-effects-with-ccrendertexture"></a>性能和与 CCRenderTexture 的视觉效果

_CCRenderTexture 使开发人员能够通过减少绘图调用，提高的 CocosSharp 游戏，性能和可用于创建视觉效果。本指南附带 CCRenderTexture 示例提供如何有效地使用此类的实际操作示例。_

`CCRenderTexture`类提供了用于呈现为单个纹理的多个 CocosSharp 对象功能。 一旦创建，`CCRenderTexture`可使用实例，若要高效呈现图形和实现视觉效果。 `CCRenderTexture` 允许多个对象要呈现为单个纹理一次。 然后，该纹理可以重复使用每个帧，减少绘图调用的总数。

本指南检查如何使用`CCRenderTexture`对象来提高可收集 cardgame (CCG) 中的呈现卡的性能。 它还演示了如何`CCRenderTexture`可用来使整个实体显示为透明。 本指南引用`CCRenderTexture`[示例项目](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)。

![](ccrendertexture-images/image1.png "本指南引用 CCRenderTexture 示例项目")


## <a name="card--a-typical-entity"></a>卡 – 典型实体

之前着眼于如何使用`CCRenderTexture`对象，我们将首先熟悉自己与`Card`实体，我们将使用在此项目整个来浏览`CCRenderTexture`类。 `Card`类是一个典型的实体，遵循模式中所述在实体[实体指南](~/graphics-games/cocossharp/entities.md)。 卡类具有的所有可视化组件 (的实例`CCSprite`和`CCLabel`) 作为字段列出：


```csharp
class Card : CCNode
{
    bool usesRenderTexture;
    List<CCNode> visualComponents = new List<CCNode>();
    CCSprite background;
    CCSprite colorIcon;
    CCSprite monsterSprite;
    CCLabel monsterNameDisplay;
    CCLabel hpDisplay;
    CCLabel descriptionDisplay;
```

可以通过使用呈现卡实例`CCRenderTexture`，或通过单独绘制每个可视化组件。 尽管每个组件是一个独立的对象，`CCNode`用作父级实体中使用系统使`Card`作为单个对象 – 至少在大多数情况下的行为。 例如，如果`Card`实体进行重新定位、 调整大小或旋转，则所有包含的视觉对象都受到影响，以使显示为单个对象的卡。 若要查看的卡作为单个对象的行为，我们可以修改`GameLayer.AddedToScene`方法以设置`useRenderTextures`变量`false`:

    
```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    ...
}
```

`GameLayer`代码不会移动每个 visual 元素独立，尚未在每个 visual 元素`Card`正确定位实体：

![](ccrendertexture-images/image1.png "GameLayer 代码不会移动每个 visual 元素独立，但每个卡实体内的可视元素位于正确")

示例编码为公开每个 visual 组件呈现自身时可能发生的两个问题：

- 由于多个绘图调用的性能可能会受到影响
- 某些视觉效果，如透明度，不能准确地说，实现，我们将更高版本浏览


### <a name="card-draw-calls"></a>卡绘图调用

我们的代码是完全可能找到的内容的简化*可收集纸牌游戏*(CCG) 如"幻:: 收集"或"Hearthstone"。 我们的游戏中仅一次显示三个卡，并将一小部分可能单位 （蓝色、 绿色和橙色）。 与此相反，完整游戏可能必须超过 20 卡屏幕上在给定时间，并且播放器可能有数百个卡可供选择时创建其的卡片组。 即使我们游戏不当前会受到性能问题，可能会与类似实现完整游戏。

CocosSharp 提供了通过公开绘图调用的呈现性能某些了解执行每个帧。 我们`GameLayer.AddedToScene`方法设置`GameView.Stats.Enabled`到`true`，这会导致在屏幕左下角显示的性能信息：

![](ccrendertexture-images/image2.png "GameLayer.AddedToScene 方法将 GameView.Stats.Enabled 设置为 true，从而导致屏幕的左下角显示的性能信息")

请注意，尽管在屏幕上具有三个卡，我们有十九日绘图调用 （每个卡结果在六个绘图调用，更多个显示性能信息帐户的文本）。 绘图调用对游戏的性能具有显著的影响，因此 CocosSharp 提供多种方式可减少它们。 一种方法中所述[CCSpriteSheet 指南](~/graphics-games/cocossharp/ccspritesheet.md)。 另一种方法是使用`CCRenderTexture`以减小下一次调用，每个实体，因为我们将查看本指南中。


### <a name="card-transparency"></a>卡透明度

我们`Card`实体包含`Opacity`属性控制透明度，如下面的代码段中所示：


```csharp
public override byte Opacity
{
    get
    {
        return base.Opacity;
    }
    set
    {
        base.Opacity = value;
        if (usesRenderTexture)
        {
            this.renderTexture.Sprite.Opacity = value;
        }
        else
        {
            foreach (var component in visualComponents)
            {
                component.Opacity = value;
            }
        }
    }
}
```

请注意 setter 支持使用呈现纹理或单独呈现每个组件。 若要查看其效果，请更改`opacity`值赋给`127`（大约一半的不透明度） 中`GameLayer.AddedToScene`这将导致每个组件都有`Opacity`值`127`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    const byte opacity = 127;
    ...
}
```

游戏现在将呈现的某些透明度，从而导致它们看上去更暗，由于背景是黑色的卡：

![](ccrendertexture-images/image3.png "游戏现在将呈现的某些透明度，从而导致它们看上去更暗，由于背景是黑色的卡")

初看上去它可能看起来像我们卡正确进行了透明。 但是，屏幕快照显示大量的界面上的问题。

由于我们背景是黑色，我们希望我们卡的每个部分将变得更深由于透明度。 也就是说，越透明卡作为，它作为颜色越深。 在不透明度 0，`Card`将完全透明 （完全黑色）。 但是，我们卡的某些部分未变暗当成为不透明度已更改为`127`。 更糟的是，我们卡的某些部分实际变得更亮时它们变得更加透明。 让我们看一下我们卡的部分是黑色*之前*它们是透明 – 大图黑色边框和专门的详细信息文本。 如果我们将这些并排显示，我们可以看到应用透明度的影响：

![](ccrendertexture-images/image4.png "如果并排放置，可以看到应用透明度的影响")

如上所述，卡的所有部分应都变为深时变得更加透明的, 但在许多方面不这种情况：

- 机器人的大纲变得更轻 （从变为黑色灰色）
- 说明文本将变为较浅 （从变为黑色灰色）
- 绿色部分机器人变得不太饱和，但不会变得更深

若要帮助可视化为何发生这种情况，我们需要记住每个 visual 组件是独立绘制，每个部分透明。 绘制的第一个可视化组件为卡的背景。 后续的透明元素将绘制在卡之上，并且将会受到卡背景。 如果我们从我们卡上删除一些文本，向下移动机器人图形，我们可以看到如何卡背景影响机器人。 请注意在 robot 上, 可以看到从顶部的框中的橙色行和机器人其中重叠的蓝色条带化在卡的中心的区域，会绘制深：

![](ccrendertexture-images/image5.png "请注意在 robot 上, 可以看到从顶部的框中的橙色行和机器人其中重叠的蓝色条带化在卡的中心的区域，会绘制深")

使用`CCRenderTexture`允许我们使整个卡透明而不会影响呈现的卡内的单独组件正如我们将看到在本指南后面。


## <a name="using-ccrendertexture"></a>使用 CCRenderTexture

现在，我们已标识单独呈现每个组件的问题，我们将打开到呈现`CCRenderTexture`和比较行为。

若要启用呈现到`CCRenderTexture`，更改`userRenderTextures`变量`true`中`GameLayer.AddedToScene`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = true;
```


### <a name="card-draw-calls"></a>卡绘图调用

如果我们现在运行游戏时，我们将看到从十九至 4 小时减少绘图调用 （每个卡减少从 6 到一个）：

![](ccrendertexture-images/image6.png "如果现在运行游戏，则绘图调用减少从十九至 4 每个卡减少从六个到其中一个")

如前所述，这种类型的缩减可以对在屏幕上的多个 visual 实体的游戏有显著的影响。


### <a name="card-transparency"></a>卡透明度

一次`useRenderTextures`设置为`true`，透明卡将以不同的方式呈现：

![](ccrendertexture-images/image7.png "设置为透明，则为 true 的卡 useRenderTextures 后将以不同的方式呈现")

让我们将使用 （左侧） 与不 （权限） 的呈现器纹理的透明机器人卡进行比较：

![](ccrendertexture-images/image8.png "比较使用呈现器而无需 （右） 的纹理 （左侧） vs 透明机器人卡")

最明显的差异是在机器人画面 （而不是 light 深色和饱和） 和的详细信息文本 （而不是浅灰色的黑色）。


## <a name="ccrendertexture-details"></a>CCRenderTexture 详细信息

现在，我们已了解使用的好处`CCRenderTexture`，让我们看看如何在中使用`Card`实体。

`CCRenderTexture`是可呈现目标的画布。 它具有两个主要的区别相比游戏屏幕时：

1. `CCRenderTexture`仍然存在之间的帧。 这意味着，`CCRenderTexture`需要发生更改时仅呈现。 在本例中为`Card`实体永远不会发生更改，所以它仅呈现一次。 如果任何`Card`组件更改，然后卡需要重绘自己到其`CCRenderTexture`。 例如，如果攻击时，将更改 HP 值 （运行状况磅为单位），然后卡需要来呈现自身以反映新的 HP 值。
1. `CCRenderTexture`像素维度未绑定到屏幕。 A`CCRenderTexture`可以是大于或小于设备的分辨率。 `Card`代码将创建`CCRenderTexture`使用其背景动画层的大小。 智能卡包含的引用`CCRenderTexture`调用`renderTexture`:


```csharp
CCRenderTexture renderTexture;
```

`renderTexture`实例保持`null`直到`UseRenderTexture`属性分配为 true，哪些调用`SwitchToRenderTexture`:


```csharp
private void SwitchToRenderTexture()
{
    // The card needs to be moved to the origin (0,0) so it's rendered on the render target. 
    // After it's rendered to the CCRenderTexture, it will be moved back to its old position
    var oldPosition = this.Position;
    // Make sure visuals are part of the card so they get rendered
    bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
    if (!areVisualComponentsAlreadyAdded)
    {
        // Temporarily add them so we can render the object:
        foreach (var component in visualComponents)
        {
            this.AddChild(component);
        }
    }
    // Create the render texture if it hasn't yet been made:
    if (renderTexture == null)
    {
        // Even though the game is zoomed in to create a pixellated look, we are using
        // high-resolution textures. Therefore, we want to have our canvas be 2x as big as 
        // the background so fonts don't appear pixellated
        var pixelResolution = background.ContentSize * 2;
        var unitResolution = background.ContentSize;
        renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
        //renderTexture.Sprite.Scale = .5f;
    }
    // We don't want the render target to be a child of this when we update it:
    if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
    {
        this.Children.Remove(renderTexture.Sprite);
    }
    // Move this instance back to the origin so it is rendered inside the render texture:
    this.Position = CCPoint.Zero;
    // Clears the CCRenderTexture
    renderTexture.BeginWithClear(CCColor4B.Transparent);
    // Visit renders this object and all of its children
    this.Visit();
    // Ends the rendering, which means the CCRenderTexture's Sprite can be used
    renderTexture.End();
    // We no longer want the individual components to be drawn, so remove them:
    foreach (var component in visualComponents)
    {
        this.RemoveChild(component);
    }
    // Move this back to its original position:
    this.Position = oldPosition;
    // add the render texture sprite to this:
    renderTexture.Sprite.AnchorPoint = CCPoint.Zero;
    this.AddChild(renderTexture.Sprite);
}
```

`SwitchToRenderTexture`当纹理需要刷新时，可以调用方法。 则可以调用它是否已在使用卡其`CCRenderTexture`或切换到`CCRenderTexture`第一次。

下列各节浏览`SwitchToRenderTexture`方法。 


### <a name="ccrendertexture-size"></a>CCRenderTexture 大小

CCRenderTexture 构造函数需要两个集的维度。 第一个控件的大小`CCRenderTexture`时，对其绘制和第二个指定的像素宽度和高度其内容。 `Card`实体实例化其`CCRenderTexture`使用背景[ContentSize](https://developer.xamarin.com/api/property/CocosSharp.CCSprite.ContentSize/)。 我们的游戏中具有`DesignResolution`512 通过 384 中所示的`ViewController.LoadGame`在 iOS 上和`MainActivity.LoadGame`在 Android 上：


```csharp
int width = 512;
int height = 384;
// Set world dimensions
gameView.DesignResolution = new CCSizeI(width, height);
```

`CCRenderTexture`构造函数调用与`background.ContentSize`作为第一个参数，指示`CCRenderTexture`应后台一样大`CCSprite`。 由于卡背景`CCSprite`、 高 200 像素，卡将占用大约一半的屏幕的垂直高度。

第二个参数传递给`CCRenderTexture`构造函数指定的像素的分辨率`CCRenderTexture`。 中所述[CocosSharp 解析指南](~/graphics-games/cocossharp/resolutions.md)的宽度和高度的可视区域以游戏单位通常不是相同的屏幕像素的分辨率。 同样，CCRenderTexture 可能使用较大的分辨率比其大小，因此视觉对象出现更高分辨率的设备上。

像素的分辨率是两次 CCRenderTexture 以防止从查找像素化文本的大小：


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize * 2;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

若要比较，我们可以将更改要匹配的 pixelResolution 值`background.ContentSize`（不带正在增加了一倍） 和比较结果： 


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

![](ccrendertexture-images/image9.png "若要比较，可以更改要与背景匹配的 pixelResolution 值。ContentSize 而无需正在加倍并比较结果")


### <a name="rendering-to-a-ccrendertexture"></a>呈现为 CCRenderTexture

通常情况下，视觉对象中 CocosSharp 不显式会呈现。 相反，将视觉对象添加到`CCLayer`的一部分`CCScene`。 CocosSharp 自动呈现`CCScene`和无需呈现代码调用每个帧中的其可视层次结构。 

与此相反，`CCRenderTexture`必须显式绘制到。 此呈现可以分解成三个步骤：

1. `CCRenderTexture.BeginWithClear` 称为，该值指示所有后续呈现将针对调用`CCRenderTexture`。
1. 对象继承自`CCNode`(如`Card`实体) 均会呈现给`CCRenderTexture`通过调用`Visit`。
1. `CCRenderTexture.End` 称为，该值指示该呈现到`CCRenderTexture`已完成。

可以将任意数量的对象呈现给`CCRenderTexture`之间其`Begin`和`End`调用。 在呈现之前，作为子级添加所有必要的可见对象：


```csharp
bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
if (!areVisualComponentsAlreadyAdded)
{
    // Temporarily add them so we can render the object:
    foreach (var component in visualComponents)
    {
        this.AddChild(component);
    }
}
```

`renderTexture`不应为智能卡的一部分时呈现，因此它将删除：


```csharp
// We don't want the render texture to be a child of the card 
// when we call Visit
if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
{
    this.RemoveChild(renderTexture.Sprite);
}
```

现在`Card`实例可以呈现自身到`CCRenderTexture`实例：


```csharp
// Clears the CCRenderTexture
renderTexture.BeginWithClear(CCColor4B.Transparent);
// Visit renders this object and all of its children
this.Visit();
// Ends the rendering, which means the CCRenderTexture's Sprite can be used
renderTexture.End();
```

呈现完成后，删除了单独的组件和`CCRenderTexture`重新添加。


```csharp
// We no longer want the individual components to be drawn, so remove them:
foreach (var component in visualComponents)
{
    this.RemoveChild(component);
}
// add the render target sprite to this:
this.AddChild(renderTexture.Sprite);
```

## <a name="summary"></a>总结

本指南涵盖`CCRenderTexture`类通过`Card`这被用在可回收纸牌游戏的实体。 它说明了如何使用`CCRenderTexture`类来提高帧速率和正确实现实体级透明度。

尽管本指南使用`CCRenderTexture`包含在实体内，此类可以是用于呈现多个实体，或甚至是整个`CCLayer`屏幕整个产生影响和性能改进的实例。

## <a name="related-links"></a>相关链接

- [CCRenderTexture API 参考](https://developer.xamarin.com/api/type/CocosSharp.CCRenderTexture/)
- [完整的项目 （示例）](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)
