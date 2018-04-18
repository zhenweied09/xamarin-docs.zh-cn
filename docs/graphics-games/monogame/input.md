---
title: MonoGame 游戏板引用
description: 游戏板是用于访问 MonoGame 中的输入的设备的标准的、 跨平台的类。
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: e7ce8320441f6b248b183a8698a4f41c78bf2d64
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="monogame-gamepad-reference"></a>MonoGame 游戏板引用

_游戏板是用于访问 MonoGame 中的输入的设备的标准的、 跨平台的类。_

`GamePad` 可以用于读取输入从多个 MonoGame 平台上的输入设备。 本指南演示如何使用游戏板类。 由于每个输入的设备不同的布局和它所提供的按钮的数目，本指南包括的关系图中显示的各种设备映射。

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>游戏板 Xbox360GamePad 替代

提供的原始 XNA API`Xbox360GamePad`从 Xbox 360 或 PC 上的游戏控制器读取输入的类。 MonoGame 已取代这些都可以通过`GamePad`类由于 Xbox 360 控制器不能在大多数 MonoGame 平台 （如 iOS 或 Xbox One） 上。 尽管此名称更改，请使用`GamePad`类是类似于`Xbox360GamePad`类。

## <a name="reading-input-from-gamepad"></a>从游戏板读取输入

`GameController`类提供一种标准化的方法读取输入的任何 MonoGame 平台上。 它是通过两种方法提供的信息：

- `GetState` – 返回的控制器的按钮、 模拟记忆棒和控制键的当前状态。
- `GetCapabilities` – 返回的功能信息的硬件，如控制器是否具有某些按钮或支持振动。

### <a name="example-moving-a-character"></a>示例： 移动一个字符

下面的代码演示如何使用左的 thumb 棍棒将该字符移动通过设置其`XVelocity`和`YVelocity`属性。 此代码假定`characterInstance`是某对象包含的一个实例`XVelocity`和`YVelocity`属性：

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>示例： 检测推送

`GamePadState` 提供的控制器，例如是否按下某个按钮的当前状态有关的信息。 某些操作，例如让人物跳转，如果，则需要检查已按了按钮 （未关闭最后一个帧，但已关闭此帧） 或释放 （已关闭最后一个帧，但不是关闭此帧）。 

若要执行此类型的逻辑，存储将上一个帧的局部变量`GamePadState`和当前帧的`GamePadState`必须创建。 下面的示例演示如何存储和使用上一个帧`GamePadState`实现跳转：

```csharp
// At class scope:
// Store the last frame's and this frame's GamePadStates.
// "new" them so that code doesn't have to perform null checks:
GamePadState lastFrameGamePadState = new GamePadState();
GamePadState currentGamePadState = new GamePadState();
protected override void Update(GameTime gameTime)
{
    // store off the last state before reading the new one:
    lastFrameGamePadState = currentGamePadState;
    currentGamePadState = GamePad.GetState(PlayerIndex.One);
    bool wasAButtonPushed = 
currentGamePadState.Buttons.A == ButtonState.Pressed
        && lastFrameGamePadState.Buttons.A == ButtonState.Released;
    if(wasAButtonPushed)
    {
        MakeCharacterJump();
    }
...
}
```

### <a name="example-checking-for-buttons"></a>示例： 查找按钮

`GetCapabilities` 可用来检查控制器是否具有特定硬件，如特定按钮或模拟盘。 下面的代码演示如何检查在游戏这需要两个按钮的状态中的控制器上的 B 和 Y 按钮：

```csharp
var capabilities = GamePad.GetCapabilities(PlayerIndex.One);
bool hasBButton = capabilities.HasBButton;
bool hasXButton = capabilities.HasXButton;
if(!hasBButton || !hasXButton)
{
    NotifyUserOfMissingButtons();
}
```

## <a name="ios"></a>iOS

iOS 应用支持无线游戏控制器输入。

> [!IMPORTANT]
> MonoGame 3.5 NuGet 包中未包含无线游戏控制器的支持。 在 iOS 上使用游戏板类需要生成从源 MonoGame 3.5 或使用 MonoGame 3.6 NuGet 二进制文件。 

### <a name="ios-game-controller"></a>iOS 游戏控制器

`GamePad`类返回从无线控制器中读取的属性。 中的属性`GamePad`好的覆盖率为提供标准 iOS 控制器硬件，如下面的关系图中所示：

![](input-images/image1.png "游戏板中的属性提供好的覆盖率标准 ios 控制器硬件，此图中所示")

## <a name="apple-tv"></a>Apple TV

Apple TV 游戏可使用 Siri 远程或无线游戏控制器用于输入。

### <a name="siri-remote"></a>Siri 远程

*Siri 远程*Apple tv 是本机的输入的设备。 虽然可以通过事件读取 Siri 远程中的值 (如中所示[Siri 远程和蓝牙控制器指南](~/ios/tvos/platform/remote-bluetooth.md))，则`GamePad`类可以从使用 Siri 远程返回值。

请注意，`GamePad`只能读取输入从开始按钮和 touch 面： 

![](input-images/image2.png "请注意，游戏板只能读取输入从开始按钮和 touch 面")

自触摸图面移动读取`DPad`属性，使用报告值的移动`ButtonState`类。 换而言之，值都仅可用作`ButtonState.Pressed`或`ButtonState.Released`，而不是数字值或手势。

### <a name="apple-tv-game-controller"></a>Apple 电视游戏控制器

有关 Apple TV 的游戏控制器到 iOS 应用的游戏控制器具有相同行为。 有关详细信息，请参阅[iOS 游戏控制器部分](#iOS_Game_Controller)。 

## <a name="xbox-one"></a>Xbox One

Xbox One 控制台支持从 Xbox One 的游戏控制器读取输入。

### <a name="xbox-one-game-controller"></a>Xbox 一个游戏控制器

Xbox One 游戏控制器是为 Xbox One 最常见的输入的设备。 `GamePad`类提供了从游戏控制器硬件的输入的值。

![](input-images/image3.png "游戏板类提供了从游戏控制器硬件的输入的值")

## <a name="summary"></a>总结

本指南概述了 MonoGame 的`GamePad`类如何实现输入读取逻辑和关系图的常见`GamePad`实现。

## <a name="related-links"></a>相关链接

- [MonoGame 游戏板](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
