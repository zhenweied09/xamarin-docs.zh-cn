---
title: "Siri 远程和蓝牙控制器"
description: "本文介绍如何在你 Xamarin.tvOS 应用中支持新的 Siri 远程和蓝牙游戏控制器。"
ms.topic: article
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: cef717a727b3b018b9eec3e8a402ae4f927f7cb8
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="siri-remote-and-bluetooth-controllers"></a>Siri 远程和蓝牙控制器

_本文介绍如何在你 Xamarin.tvOS 应用中支持新的 Siri 远程和蓝牙游戏控制器。_


Xamarin.tvOS 应用程序的用户将不与它的接口直接作为 ios 其中他们点击设备的屏幕上的映像但间接从跨聊天室使用交互[Siri 远程](#The-Siri-Remote)。

如果你的应用程序是一个游戏，你可以根据需要生成的第三方，所做的支持 iOS (MFI)[蓝牙游戏控制器](#Bluetooth-Game-Controllers)中你的应用程序。

[![](remote-bluetooth-images/intro01.png "蓝牙远程和游戏控制器")](remote-bluetooth-images/intro01.png#lightbox)

本指南介绍了[Siri 远程](#The-Siri-Remote)， [Touch 面手势](#Touch-Surface-Gestures)和[Siri 远程按钮](#Siri-Remote-Buttons)并演示如何使用这些通过[手势和情节提要](#Gestures-and-Storyboards)，[笔势和代码](#Gestures-and-Code)和[低级别事件处理](#Low-Level-Event-Handling)。 最后，还会讨论[使用游戏控制器](#Working-with-Game-Controllers)Xamarin.tvOS 应用中。

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>Siri 远程

将与 Apple TV 和你 Xamarin.tvOS 的应用，交互用户的主要方式是通过包含 Siri 远程。 Apple 设计远程来桥接坐在新床和在同一房间电视屏幕上显示的 Apple 电视用户界面上的用户之间的距离。

您作为 tvOS 应用程序开发人员的挑战是创建一个快速、 易于使用和极具视觉表现力的用户界面，利用 Siri 远程触摸面、 加速计、 陀螺和按钮。

[![](remote-bluetooth-images/remote01.png "Siri 远程")](remote-bluetooth-images/remote01.png#lightbox)

Siri 远程具有以下功能和 tvOS 应用中的预期的用法：

<table width="100%" border="1px">
<tr>
    <td><b>功能</b></td>
    <td><b>常规应用使用情况</b></td>
    <td><b>游戏的应用使用情况</b></td>
</tr>
<tr>
    <td valign="top"><b>触摸面</b><br/>轻扫，若要导航，请按以选择并按住对于上下文菜单。</td>
    <td valign="top"><b>点击/轻扫：</b><br/>UI 可获得焦点的项之间导航。<br/><br/><b>单击所示：</b><br/>激活所选 （焦点） 项。</td>
    <td valign="top"><b>点击/轻扫：</b><br/>依赖于游戏设计和可用作通过边缘上的点击 D 填充。<br/><br/><b>单击所示：</b><br/>执行主按钮功能。</td>
</tr>
<tr>
    <td valign="top"><b>菜单</b><br/>按下以返回到之前的屏幕或菜单。</td>
    <td valign="top">返回到上一屏幕并从主应用程序屏幕退出到 Apple 电视主页屏幕。</td>
    <td valign="top">暂停和继续玩游戏，将返回到上一屏幕并退出到 Apple 电视主页屏幕从主应用程序屏幕。</td>
</tr>
<tr>
    <td valign="top"><b>Siri/Search</b><br/>在使用 Siri 国家/地区，请按住语音控件，在所有其他国家/地区，显示搜索屏幕。</td>
    <td valign="top">n/a</td>
    <td valign="top">n/a</td>
</tr>
<tr>
    <td valign="top"><b>播放/暂停</b><br/>播放和暂停媒体或提供应用程序中的辅助函数。</td>
    <td valign="top">启动媒体播放和暂停/继续播放。</td>
    <td valign="top">执行辅助按钮的功能或跳过简介视频 (如果存在)。</td>
</tr>
<tr>
    <td valign="top"><b>主文件夹</b><br/>按以返回到主页屏幕中，双击要显示运行中的应用，请按住进入睡眠状态的设备。</td>
    <td valign="top">n/a</td>
    <td valign="top">n/a</td>
</tr>
<tr>
    <td valign="top"><b>卷</b><br/>控件附加音频/视频设备卷。</td>
    <td valign="top">n/a</td>
    <td valign="top">n/a</td>
</tr>
</table>

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>触摸图面手势

Siri 远程数据库的 Touch 面是能够检测到各种可响应 Xamarin.tvOS 应用程序中的单指手势：

<table width="100%">
<tr>
    <td valign="top" width="30%"><img src="remote-bluetooth-images/Gesture01.png"></td>
    <td valign="top" width="30%"><img src="remote-bluetooth-images/Gesture02.png"></td>
    <td valign="top" width="30%"><img src="remote-bluetooth-images/Gesture03.png"></td>
</tr>
<tr>
    <td valign="top"><b>轻扫：</b><br/>在屏幕上的 UI 元素之间移动选择 （焦点） （向上、 下左、 右）。 轻扫可用来快速使用惯性的内容的大型列表中滚动。</td>
    <td valign="top"><b>单击所示：</b><br/>激活所选的 （焦点） 项或像游戏中的主按钮。 单击并按住可以激活上下文菜单或辅助函数。</td>
    <td valign="top"><b>点击：</b><br/>轻轻点击 Touch 曲面上边缘的作用类似方向按钮在 D-板、 将焦点移向上、 下、 左或右根据点击的区域。 具体取决于应用程序中，可以使用以显示隐藏的控件。</td>
</tr>
</table>

Apple 提供用于使用 Touch 面手势以下建议：

* **区分点击量和分流**-单击是由用户有意操作，而且非常适用于所选内容、 激活和主按钮的游戏。 点击是更难以捉摸，应尽量少使用，因为用户通常在其现有持有 Siri 远程，并可以轻松地意外激活点击事件。
* **不重新定义标准手势**-用户具有特定手势将执行特定操作，假定条件下不应重新的含义和这些手势函数定义应用程序中。 一个例外是在 active 玩游戏的游戏的应用程序。
* **尽量少新手势定义**-再次，用户具有特定手势将执行特定操作的假定条件。 应避免定义自定义特定动作执行标准操作。 并再次，游戏最常用的异常可在其中自定义笔势添加有趣、 引人入胜 play 对游戏。
* **适当时，响应 D Pad 点击**-轻轻点击的 Touch 面边缘将做出反应如游戏控制器将焦点移或方向上, 一个 D 簿下，左或向右角。 适当时，您应响应你的应用或游戏中的这些手势。

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Siri 远程按钮

Touch 图面上的笔势，除了你的应用程序可以响应用户单击 Touch 面或按播放/暂停按钮。 如果你正在访问 Siri 远程使用游戏控制器框架，你还可以检测已按下的菜单按钮。 

此外，菜单按钮按下可以使用来检测笔势识别器标准`UIKit`元素。 如果截获按下的菜单按钮，你将负责关闭当前视图和视图控制器，并返回到前一个。

> [!IMPORTANT]
> **注意：**应该**始终**为播放/暂停按钮在远程数据库上指定函数。 具有非功能性按钮可使查找中断向最终用户应用。 如果你没有此按钮的有效函数，将分配与主按钮 （Touch 面单击） 相同的功能。




<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>笔势和情节提要

使用 Siri 远程 Xamarin.tvOS 应用程序中的最简单方法是将笔势识别器添加到您在接口设计器中的视图。

若要添加手势识别器，请执行以下操作：

1. 在**解决方案资源管理器**，双击`Main.storyboard`文件，并打开以进行编辑接口设计器。
2. 拖动**点击手势识别器**从**库**并将其放在视图上： 

    [![](remote-bluetooth-images/storyboard01.png "点击手势识别器")](remote-bluetooth-images/storyboard01.png#lightbox)
3. 检查**选择**中**按钮**部分**属性检查器**: 

    [![](remote-bluetooth-images/storyboard02.png "检查选择")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **选择**意味着手势将响应用户单击**Touch 面**上使用 Siri 远程。 你还可以选择响应**菜单**，**播放/暂停**，**向上**，**下**，**左**和**右**按钮。
5. 接下来，连接**操作**从**点击手势识别器**和调用它`TouchSurfaceClicked`: 

    [![](remote-bluetooth-images/storyboard03.png "从 Tap 笔势识别器的操作")](remote-bluetooth-images/storyboard03.png#lightbox)
6. 保存所做的更改并返回到 Visual Studio for mac。

编辑视图控制器 (示例`FirstViewController.cs`) 文件，并添加以下代码以处理正在触发的笔势：

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class FirstViewController : UIViewController
    {
        ...

        #region Custom Actions
        partial void TouchSurfaceClicked (Foundation.NSObject sender) {
            // Handle click here
            ...
        }
        #endregion
    }
}
```

有关使用情节提要的详细信息，请参阅我们[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 具体而言[创建用户界面](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface)和[编写使用插座和操作代码](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)部分。

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>笔势和代码

或者，可以在 C# 代码中直接创建手势，并在用户界面中将它们添加到视图。 例如，若要添加轻扫笔势识别器的一系列，编辑视图控制器，并添加以下代码：

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class SecondViewController : UIViewController
    {
        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();    

            // Wire-up gestures
            var upGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Up";
                ButtonLabel.Text = "Swiped Up";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Up
            };
            this.View.AddGestureRecognizer (upGesture);

            var downGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Down";
                ButtonLabel.Text = "Swiped Down";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Down
            };
            this.View.AddGestureRecognizer (downGesture);

            var leftGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Left";
                ButtonLabel.Text = "Swiped Left";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Left
            };
            this.View.AddGestureRecognizer (leftGesture);

            var rightGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Right";
                ButtonLabel.Text = "Swiped Right";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Right
            };
            this.View.AddGestureRecognizer (rightGesture);
        }
        #endregion
    }
}
```

<a name="Low-Level-Event-Handling" />

## <a name="low-level-event-handling"></a>低级别事件处理

如果要创建自定义类型基于`UIKit`Xamarin.tvOS 应用程序中 (例如`UIView`)，你还能够提供低级别的处理操作的按钮按下通过`UIPress`事件。 

A`UIPress`到 tvOS 的事件是什么`UITouch`事件是于 iOS 的除`UIPress`返回有关按钮的信息按上使用 Siri 远程或其他附加蓝牙设备 （如游戏控制器）。 `UIPress` 事件描述按下按钮和其状态 （Began、 已取消、 已更改或已结束）。 

对于如蓝牙游戏控制器，设备上的模拟按钮`UIPress`也会返回应用于按钮的强制的量。 `Type`属性`UIPress`事件定义的物理按钮有已更改的状态，而余下的属性描述发生的更改。

下面的代码演示一种处理低级别`UIPress`事件`UIView`:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvRemote
{
    public partial class EventView : UIView
    {
        #region Computed Properties
        public override bool CanBecomeFocused {
            get {
                return true;
            }
        }
        #endregion

        #region 
        public EventView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void PressesBegan (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesBegan (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Red;
                }
            }
        }

        public override void PressesCancelled (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesCancelled (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }

        public override void PressesChanged (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesChanged (presses, evt);
        }

        public override void PressesEnded (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesEnded (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }
        #endregion
    }
}
```

与`UITouch`事件，如果你需要实现的任何`UIPress`事件重写，则应实现所有四个。

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>蓝牙游戏控制器

除了标准 Siri 远程 Apple TV、 第三方、 做为 iOS 附带可以与 Apple TV 配对 (MFI) 蓝牙游戏控制器，并且用于控制 Xamarin.tvOS 应用程序中。

[![](remote-bluetooth-images/game01.png "蓝牙游戏控制器")](remote-bluetooth-images/game01.png#lightbox)

游戏控制器可以用于提高玩游戏并提供了在游戏中的高质量的意义。 它们还可以用于控制标准 Apple TV 接口，以便使用无需远程和控制器之间切换。

> [!IMPORTANT]
> **注意：**蓝牙游戏控制器最终用户可能会使可选购买，您的应用程序不能强制用户在购买一个。 如果你的应用程序支持游戏控制器，它还必须支持 Siri 远程，以便游戏已由 Apple TV 的所有用户使用。


游戏控制器具有以下功能和 tvOS 应用中的预期的用法：
<table width="100%" border="1px">
<tr>
    <td><b>功能</b></td>
    <td><b>常规应用使用情况</b></td>
    <td><b>游戏的应用使用情况</b></td>
</tr>
<tr>
    <td valign="top"><b>D-Pad</b></td>
    <td valign="top">将导航到 UI 元素 （更改焦点）。</td>
    <td valign="top">依赖于游戏。</td>
</tr>
<tr>
    <td valign="top"><b>A</b></td>
    <td valign="top">激活所选的 （焦点） 项。</td>
    <td valign="top">执行主按钮的功能，并确认对话框操作。</td>
</tr>
<tr>
    <td valign="top"><b>B</b></td>
    <td valign="top">返回到之前的屏幕或退出到主页屏幕中，如果应用程序的主屏幕上。</td>
    <td valign="top">执行辅助按钮的功能或返回到之前的屏幕。</td>
</tr>
<tr>
    <td valign="top"><b>X</b></td>
    <td valign="top">启动媒体播放或暂停/继续播放。</td>
    <td valign="top">依赖于游戏。</td>
</tr>
<tr>
    <td valign="top"><b>Y</b></td>
    <td valign="top">n/a</td>
    <td valign="top">依赖于游戏。</td>
</tr>
<tr>
    <td valign="top"><b>菜单</b></td>
    <td valign="top">返回到之前的屏幕或退出到主页屏幕中，如果应用程序的主屏幕上。</td>
    <td valign="top">暂停/恢复玩游戏，将返回到之前的屏幕或主页屏幕的退出如果应用程序的主屏幕。</td>
</tr>
<tr>
    <td valign="top"><b>左即时权限提升按钮</b></td>
    <td valign="top">导航左侧。</td>
    <td valign="top">依赖于游戏。</td>
</tr>
<tr>
    <td valign="top"><b>左的触发器</b></td>
    <td valign="top">导航左侧。</td>
    <td valign="top">依赖于游戏。</td>
</tr>
<tr>
    <td valign="top"><b>右即时权限提升按钮</b></td>
    <td valign="top">导航右。</td>
    <td valign="top">依赖于游戏。</td>
</tr>
<tr>
    <td valign="top"><b>正确的触发器</b></td>
    <td valign="top">导航右</td>
    <td valign="top">依赖于游戏。</td>
</tr>
<tr>
    <td valign="top"><b>左的摇杆</b></td>
    <td valign="top">将导航到 UI 元素 （更改焦点）。</td>
    <td valign="top">依赖于游戏。</td>
</tr>
<tr>
    <td valign="top"><b>右摇杆</b></td>
    <td valign="top">n/a</td>
    <td valign="top">依赖于游戏。</td>
</tr>
</table>

Apple 提供了用于游戏控制器以下建议：

- **确认游戏控制器连接**-tvOS 应用可以启动和停止在任何时候，最终用户。 你应始终检查存在在启动应用程序或处于唤醒状态的时间的游戏控制器，并根据需要采取措施。
- **确保你的应用程序适用于使用 Siri 远程和游戏控制器**-不需要用户 Siri 远程和游戏控制器，以使用你的应用之间进行切换。 测试应用通常使用这两种类型的控制器确保所有内容轻松地导航，并按预期方式工作。
- **方法返回提供**-按菜单按钮应始终返回到之前的屏幕。 如果用户是在主应用程序屏幕中，菜单按钮应返回它们到 Apple 电视主页屏幕。 玩游戏，期间菜单按钮应显示警报向用户提供暂停/继续玩游戏或返回到主菜单的功能。

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>使用游戏控制器

如上面所述，除了标准 Siri 带的远程访问附带 Apple TV，用户可以选择性地附加中的第三方，所做为 iOS (MFI) 蓝牙游戏控制器，并且使用它来控制 Xamarin.tvOS 应用程序。

如果你的应用程序需要低级别的控制器输入，你可以使用 Apple 的[游戏控制器框架](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276)其中包含了对 tvOS 以下修改之处：

- Micro 游戏控制器配置文件 (`GCMicroGamepad`) 已添加以面向 Siri 远程。
- 新`GCEventViewController`类可以用于将路由应用程序的游戏控制器事件。 请参阅[确定游戏控制器输入](#Determining-Game-Controller-Input)下面部分以了解更多详细信息。

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>游戏控制器支持要求

Apple 有几个如果 Xamarin.tvOS 应用支持游戏控制器，必须满足的特定要求：

- **你必须支持 Siri 远程**-必须始终支持 Siri 远程。 你的游戏不能要求第三方可以播放游戏控制器。
- **你必须支持扩展控件布局**-所有 tvOS 游戏控制器都是非 formfitting 扩展控制器。
- **游戏必须具有独立控制器 Playable** -如果你的应用程序支持扩展游戏控制器，它必须是可使用该游戏控制器单独播放。
- **你必须支持播放/暂停按钮**-玩游戏，期间如果用户按下播放/暂停按钮中，你应显示警报向用户提供到暂停/继续玩游戏的功能或返回到主菜单。

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>启用游戏控制器支持

若要启用游戏控制器支持 Xamarin.tvOS 应用程序中的，双击`Info.plist`文件中**解决方案资源管理器**以将其打开以进行编辑：

[![](remote-bluetooth-images/game02.png "Info.plist 编辑器")](remote-bluetooth-images/game02.png#lightbox)

下**游戏控制器**部分中，通过选中**启用游戏控制器**，然后检查所有应用程序将支持的游戏控制器类型。

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>为游戏控制器使用 Siri 远程

附带 Apple TV Siri 远程可以用作有限的游戏控制器。 如其他游戏控制器，它会出现在游戏控制器框架中`GCController`对象，并支持这两个`GCMotion`和`GCMicroGamepad`配置文件。

用作游戏控制器时，使用 Siri 远程具有以下特征：

- Touch 面可以用作提供模拟的输入的数据 D 填充。
- 可以在纵向或横向朝方向中使用远程和你的应用程序决定该配置文件对象是否应自动翻转输入的数据。
- 单击 Touch 面作用类似于按按钮**A**游戏控制器上。
- 播放/暂停按钮的作用类似按钮**X**游戏控制器上。
- 菜单按钮应显示警报向用户提供暂停/继续玩游戏或返回到主菜单的功能。

<a name="Summary" />

### <a name="determining-game-controller-input"></a>确定游戏控制器输入

与 iOS 游戏控制器事件可以采用与触控事件并行不同 tvOS 处理所有低级别的事件，以提供高级`UIKit`事件。 因此，如果你需要对较低级别的游戏控制器事件的访问，你将需要关闭`UIKit`的默认行为。

上 tvOS，当你想要直接你需要使用处理游戏控制器输入`GCEventViewController`（或子类） 来显示游戏的用户界面。 每当`GCEventViewController`是*第一个响应方*，将捕获游戏控制器输入，并将其传送到你的应用通过游戏控制器框架。

你可以使用`UserInteractionEnabled`属性`GCEventViewController`类来切换处理和处理事件的方式。

有关实现游戏控制器支持的信息，请参阅 Apple 的[使用游戏控制器](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html)主题中[应用对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html)和[游戏控制器编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html)。

<a name="Summary" />

## <a name="summary"></a>摘要

本文已覆盖新 Siri 远程附带 Apple TV、 Touch 面手势和 Siri 远程按钮。 接下来，则覆盖使用手势和情节提要、 手势和代码和低级别事件。 最后，如果讨论使用游戏控制器。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
