---
title: 快速交互技术 watchOS 3
description: 本文介绍如何快速交互技术 Apple 都增加 watchOS 3 以及如何实现它们在 Xamarin.iOS 转换为 Apple Watch 中。
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: d3c7c6d80a6f23cdadda04d787e28e13b054a9e6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="quick-interaction-techniques-for-watchos-3"></a>快速交互技术 watchOS 3

_本文介绍如何快速交互技术 Apple 都增加 watchOS 3 以及如何实现它们在 Xamarin.iOS 转换为 Apple Watch 中。_

提供快速的用户交互至关重要创建引人注目的 Apple Watch 应用和复杂性。 新 watchOS 3，到 Apple 添加了对笔势识别器访问的数字王冠和新的用户通知和导航技术支持。 此操作，请添加支持 SceneKit 和 SpriteKit，以及允许开发人员可以轻松创建丰富，方便查看是快速、 响应快的接口。

## <a name="what-are-quick-interactions"></a>快速交互有哪些？

有关用于创建面向 iOS 或 macOS （其中的用户花费与应用程序交互的时间度量在几分钟或小时数） 的应用程序开发人员，程序的 Apple Watch 设计成功进行应用程序可能是一个难题和需要为其他方法。

在 watchOS，用户通常想要引发其静电腕带，快速与应用交互 （通常为简要几秒为单位），然后删除其静电腕带并继续它是他们所做的任何内容。

在 Apple Watch 上的典型快速交互的几个示例如下：

- 正在启动一个计时器。
- 正在检查天气。
- 将标记 todo 列表项。

若要实现这些目标，必须是 Apple Watch 上的应用程序：

- **方便查看**-这意味着，以快速速览用户应该能够获取所需的信息。 
- **可操作**-这意味着用户应该能够使快速、 明智的决策。
- **响应式**-这意味着用户应等待的时间不以接收所需的信息或实现他们所需的操作。

### <a name="quick-interactions-length"></a>快速交互长度

由于 Apple Watch 应用的方便查看性质，Apple 提供的建议的快速交互理想长度应为两秒或更少。 由于此两个的第二个限制，开发人员将需要花费相当长的时间同时设计和实现 Apple Watch 应用。 

## <a name="new-watchos-3-features-and-apis"></a>新 watchOS 3 功能和 Api

Apple 已添加到 WatchKit 以帮助开发人员将快速交互添加到其 Apple Watch 应用多项新功能和 Api:

- watchOS 3 提供对新类型，如输入的用户的访问权限：
    - 笔势识别器
    - 数字王冠旋转 
- watchOS 3 提供了新方法的显示和更新信息，如：
    - 增强的表导航
    - 新用户通知 framework 的支持
    - SpriteKit 和 SceneKit 集成

通过实现这些新功能，开发人员可确保其 watchOS 3 应用程序的 Glanceable、 可操作和应答性。

### <a name="gesture-recognizer-support"></a>笔势识别器支持

如果开发人员已在 iOS 中实现笔势识别器，它们应非常熟悉笔势识别器 watchOS 3 中的工作原理。 若要刷新，笔势识别器是将低级别的触控事件分析为可识别的、 预定义笔势的对象。

watchOS 3 将支持四个以下笔势识别器：

- 离散手势类型：
    - 滑动手势 (`WKSwipeGestureRecognizer`)。
    - 点击动作 (`WKTapGestureRecognizer`)。
- 连续笔势类型：
    - 平移笔势 (`WKPanGestureRecognizer`)。
    - Long 类型的值按笔势 (`WKLongPressGestureRecognizer`)。

若要实现新笔势识别器之一，只是 Mac 将其拖到 iOS Visual Studio 中的设计器中的设计图面，并配置其属性。

在代码中，响应识别器来处理正在由用户触发的笔势的操作。 同样，这是通过完成相同的方式将在 iOS 中对其进行处理。

#### <a name="discrete-gesture-states"></a>离散笔势状态

对于离散笔势，操作称为时识别出的笔势和状态 (`WKGestureRecognizerState`) 被指定为：

[![](quick-interaction-techniques-images/quick01.png "离散笔势状态")](quick-interaction-techniques-images/quick01.png#lightbox)

按的所有离散手势启动`Possible`状态和转换到任一`Failed`或`Recognized`状态。 当使用离散手势，开发人员通常不会处理直接与状态。 相反，它们依赖仅识别出的笔势后调用的操作。

#### <a name="continuous-gesture-states"></a>连续笔势状态

连续手势是与离散笔势，操作调用的那多次如识别出的笔势略有不同：

[![](quick-interaction-techniques-images/quick02.png "连续笔势状态")](quick-interaction-techniques-images/quick02.png#lightbox)

同样，连续手势启动时处于`Possible`随多个更新进度的状态，但它们。 此处的开发人员需要考虑识别器状态并更新期间的应用程序的 UI`Changed`阶段最后笔势之前`Recognized`或`Canceled`。

#### <a name="gesture-recognizer-usage-tips"></a>笔势识别器使用提示

具有笔势识别器在 watchOS 3 中工作时，Apple 提供以下建议：

- 将笔势识别器添加到组元素，而不是单独的控件。 由于 Apple Watch 具有较小的物理屏幕大小，组元素往往能够更大和更轻松的用户按的目标。 此外，笔势识别器可能会发生冲突与生成中已在本机 UI 控件的手势。
- 设置监视应用程序的情节提要中的依赖关系。
- 某些笔势优先于其他笔势类型，如：
    - 滚动
    - 也强制改动
 
### <a name="digital-crown-rotation"></a>数字王冠旋转

通过在其 watchOS 3 应用中实现数字王冠支持，开发人员可以提供增加的导航速度和精度为其用户的交互。

自 watchOS 2，Apple Watch 应用可以使用`WKInterfacePicker`对象来访问数字王冠通过提供一份`WKPickerItems`和选取器样式 （列表、 堆叠方式或映像序列）。 watchOS 则允许用户使用数字王冠以从列表中选择项。

使用时`WKInterfacePicker`，WatchKit 正在处理的大部分的工作：

- 绘制列表和单个界面元素上。
- 处理数字王冠事件。
- 选择项目时，请调用操作。

新 watchOS 3，开发人员现在能够直接访问数字王冠旋转事件这使他们可以创建自己的响应旋转值的 UI 元素。

由以下元素不会提供数字王冠访问：

- `WKCrownSequencer` -提供对每秒的旋转访问。
- `WKCrownDelegate` -提供对旋转增量事件的访问。

#### <a name="rotations-per-second"></a>每秒的旋转

当使用物理基于动画，则从数字王冠访问旋转 Per Second 非常有用。 若要访问旋转每秒，使用`CrownSequencer`属性`WKInterfaceController`的监视扩展。 例如：

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>旋转增量

使用从数字王冠旋转增量的旋转计数。 使用`CrownDidRotate`重写的方法`WKCrownDelegate`访问旋转增量。 例如：

```csharp
using System;
using WatchKit;
using Foundation;

namespace MonkeyWatch.MonkeySeeExtension
{
    public class CrownDelegate : WKCrownDelegate
    {
        #region Computed Properties
        public double AccumulatedRotations { get; set;}
        #endregion

        #region Constructors
        public CrownDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void CrownDidRotate (WKCrownSequencer crownSequencer, double rotationalDelta)
        {
            base.CrownDidRotate (crownSequencer, rotationalDelta);

            // Accumulate rotations
            AccumulatedRotations += rotationalDelta;
        }
        #endregion
    }
}
```

此处应用维护累加器 (`AccumulatedRotations`) 若要确定旋转数。 一个数字王冠完全轮换等于的累积增量`1.0`，并且会半旋转`0.5`。

Apple 使其处于由开发人员确定旋转计数如何对应于正在更新的用户界面元素上的更改的敏感度。

符号 (`+/-`) 旋转增量的指示用户是否在转数字王冠的方向：

[![](quick-interaction-techniques-images/quick03.png "旋转增量的符号指示用户在转数字王冠的方向")](quick-interaction-techniques-images/quick03.png#lightbox)


如果用户向上滚动，WatchKit 将返回正数的增量可以保持和如果向下滚动，然后负增量将返回，无论何种方向用户穿中的监视。

#### <a name="digital-crown-focus"></a>数字王冠焦点

就像任何其他界面元素，数字王冠具有焦点的概念。 此焦点可以移动离开数字王冠中，到根据如何在用户交互与监视其他界面元素中。 

例如，以下控件的任何无法窃取数字王冠的重点：

- 选取器
- Slider
- 滚动控制器

它是由开发人员确定其自定义界面元素需要数字王冠焦点时。 Apple 提供的建议使用新的笔势识别器获取自定义的 UI 元素中的焦点。

### <a name="vertical-paging"></a>垂直分页

某个用户导航 watchOS 应用中的表视图的标准方法是向滚动到所需的数据块的点击特定的行，以显示详细的视图上，点击后退按钮完成查看详细信息，然后重复该过程的任何其他信息，y 感兴趣从表中：

[![](quick-interaction-techniques-images/quick04.png "表和详细信息视图之间移动")](quick-interaction-techniques-images/quick04.png#lightbox)

新 watchOS 3，开发人员可以启用垂直分页其表视图控件上。 启用了此功能，用户可以向上滚动以查找表视图行并点击要查看其详细的信息之前的行。 但是，它们可以现在往下轻扫向上选择下一步的行在表或列表选择上一行 （或使用数字王冠），所有而无需返回到表视图首先：

[![](quick-interaction-techniques-images/quick05.png "表和详细信息视图之间移动和轻扫向上和向下移动其他行之间")](quick-interaction-techniques-images/quick05.png#lightbox)

若要启用此模式下，在 Xcode 中打开 watchOS 应用的情节提要，以便进行编辑，选择表视图，并检查**垂直详细信息分页**复选框：

[![](quick-interaction-techniques-images/quick06.png "检查垂直详细信息分页复选框")](quick-interaction-techniques-images/quick06.png#lightbox)

请确保表使用 Segues 显示详细的视图，情节提要保存所做的更改并返回到 Visual Studio for Mac 同步。

开发人员可以以编程方式进行垂直分页到使用下面的代码对表视图的特定行：

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

在使用垂直分页时，开发人员需要注意 WatchKit 将自动处理的预加载的控制器和 UI 才会显示实际结果是，可以调用某些控制器生命周期方法。

### <a name="notification-enhancements"></a>通知增强功能

通知用户遇到的情况通常 watchOS 的快速交互的主要形式，并且由于第一个 Apple Watch 和 watchOS 1 已可用。

典型的通知快速交互作用是，如下所示：

1. 收到新的通知时，用户感觉通知 Haptic。
2. 它们会引发其静电腕带查看通知短查找接口。
3. 如果它们继续保持其静电腕带引发，watchOS 将自动转换到长查找通知接口。

有多种用户可能响应与通知：

- 对于定义并呈现通知有特别，用户将不执行任何操作，并且只需取消通知。
- 它们还可能点击通知以启动 watchOS 应用程序。
- 支持自定义操作的通知，用户可能选择其中一个自定义操作。 这些可以是：
    - **前景操作**-这些启动应用程序来执行的操作。
    - **后台操作**-已始终路由至在 watchOS 2 iPhone，但可以路由到在 watchOS 3 watchApp。

WatchOS 3： 的新增功能

* 通知使用跨所有平台 （iOS、 watchOS、 tvOS 和 macOS） 类似的 API。
* 可以在 Apple Watch 上计划本地通知。
* 后台通知将路由到应用的扩展，如果它们已计划在 Apple Watch 上。

#### <a name="notification-scheduling-and-delivery"></a>通知计划和传递

从用户的 iPhone 的通知将向前发送到 Apple Watch 时将发生以下情况：

* 这部 iphone 手机的屏幕处于关闭状态。
* Apple Watch 正在磨损和已解锁。

WatchOS 3，在本地通知可以在 Apple Watch 上计划，并且仅用于监视进行传递。 由开发人员计划相应 iPhone 通知，如果它必需的应用程序。

通过包括相同的通知标识符上的 Apple Watch 和 iPhone 版本的通知，它可防止在手表上显示重复的通知。 通知的 Apple Watch 版本将优先于 iPhone 版本。

由于 watchOS 3 使用相同`UINotification`API 框架，因为 iOS 10，请参阅我们的 iOS 10[用户通知 Framework](~/ios/platform/user-notifications/index.md)更多详细信息的文档。

### <a name="using-spritekit-and-scenekit"></a>使用 SpriteKit 和 SceneKit

新到 watchOS 3，开发人员可以现在使用 SpritKit 和 SceneKit 对象在其应用程序的用户界面设计呈现 2D 和 3D 图形。

添加了两个新的接口类，以支持此功能：

- `WKInterfaceSKScene` -对于使用 SpriteKit 二维图形。
- `WKInterfaceSCNScene` -对于使用 SceneKit 3D 图形。

若要使用这些对象，只需将它们拖到设计图面内在 Xcode 的接口生成器中的监视应用程序的情节提要，并使用**属性检查器**进行配置。

从该点，请使用 SpriteKit 或 SceneKit 后台工作方式的方式与其在 iOS 应用程序内。 监视的应用程序将提供`WKInterfaceSKScene`通过调用之一`Present`方法。 对于 SceneKit，只需设置`Scene`属性`WKInterfaceSCNScene`对象。

## <a name="actionable-complications"></a>可操作的并发数据

在 watchOS 2，Apple 引入了复杂性的第三方应用程序。 在 watchOS 3，Apple 已扩展开发人员可以在一个 WatchKit 问题中包括的功能。 

此外，多个内置中监视表面现在可以包含复杂性和现有监视面向，已支持的复杂性可以现在包含更多的复杂性。

此外新是指用户快速轻扫左或向右通过监视立方体表面其 Apple Watch 安装的所有转换。 在 Apple Watch 助理 iPhone 应用上使用新库，用户可以添加和自定义新的监视表面和任何它们可以包括的复杂因素。

由于这些新功能，Apple 建议 Apple Watch 上的每个应用程序还应包括在至少一个复杂性并在这种情况下，所有本机 Apple Watch 应用现在具有复杂性。

复杂性提供到应用程序的以下功能：

- 它们是高度方便查看，因为它们始终是手表表盘上存在。
- WatchOS 经常更新复杂性。 任何应用程序包括在用户的当前显示的手表表盘上一个问题被更新至少两次一小时。
- 任何应用程序与用户的当前显示的手表表盘上一个问题是保留在内存这使快速启动该应用并提高应用程序的响应速度。
- 复杂性方便用户启动 watchOS 应用中的特定功能。

## <a name="glanceable-notification"></a>方便查看通知

在 Apple Watch 上的通知提供极好可自定义方法，以快速通知的事件或新的信息，如传入的消息或达到锻炼应用中的目标的用户。

通过使用一条通知，有价值的信息可以快速显示给用户。 在许多情况下，设计良好的通知可以删除用户实际启动该应用必要手段。

新手 watchOS 3，所有通知现在支持：

- SpriteKit
- SceneKit
- 内联视频

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>使用 SpriteKit 和 SceneKit 增强的 UI

通常情况下，开发人员可能认为的游戏 UI 时 SpriteKit 和 SceneKit 所述。 但是，可用于创建非游戏包括自定义的布局，内容的 Ui 和动画，否则不能在单独的 WatchKit SpriteKit 和 SceneKit。

例如，来自照片共享应用程序的用户通知可用于 SpriteKit 通过包括发布以及为实际图像和其他丰富用户体验的自定义的信息图的用户提供丰富的用户体验。

此外，SpriteKit 和 SceneKit 可以混合使用与应用程序的用户界面设计中的标准 WatchKit UI 元素。

## <a name="simple-navigation"></a>简单导航

watchOS 3 显示开发人员可以简化在其 watchOS 应用程序，例如新中的导航的多种[垂直分页](#Vertical-Paging)，[笔势识别器支持](#Gesture-Recognizer-Support)和[数字王冠旋转](#Digital-Crown-Rotation)上面介绍的功能。

数字王冠是 Apple Watch 到唯一的可在许多不同的方法来简化导航。 例如，计时器应用程序可以使用数字王冠若要通过可用计时器长度清理。

自定义特定动作可以提供与监视应用程序进行交互的用户的新的和独特方式，并还可以使用来简化应用程序导航。

Apple 建议寻找组合的所有新的快速交互功能在 watchOS 3 提供丰富、 方便地快速使用 watchOS 应用接口中添加几种方法。

## <a name="finishing-the-quick-interaction"></a>完成快速交互

设计良好的快速交互体验将授予用户的信心，若要删除其静电腕带 （和取消与该应用程序） 时它们完成当前的交互。

具体而言，这会在执行任何类型的网络连接或与其辅助 iPhone 应用程序共享信息 watch 应用时是一个问题。 这常常会导致等待指示器事务开始执行时，快速交互期间不需要。 请参见以下示例：

[![](quick-interaction-techniques-images/quick07.png "监视应用程序执行此操作的网络连接并与其辅助 iPhone 应用程序共享信息的关系图")](quick-interaction-techniques-images/quick07.png#lightbox)

1. 用户选择要在手表上购买的项。
2. 用户点击购买按钮。
3. 该应用将启动网络事务，并显示正在加载指示器。
4. 一段时间后，完成事务，并应用程序显示是购买确认。
5. 用户将删除其静电腕带并与应用程序中松开。

从用户点击购买按钮，直到事务完成的时，它们都有查看正在加载指示器引发其静电腕带。 若要解决这种情况下，Apple 提供的建议开发人员应呈现给用户而不是显示正在加载指示器的即时反馈。 

使用 Apple 的建议的模型，看看相同快速交互试：

[![](quick-interaction-techniques-images/quick08.png "同类建议的模型关系图")](quick-interaction-techniques-images/quick08.png#lightbox)

1. 用户选择要在手表上购买的项。
2. 用户点击购买按钮。
3. 该应用将启动网络事务，并显示消息，告知购买已成功启动。
4. 用户将删除其静电腕带并与应用程序中松开。
5. 事务成功完成时一段时间后，应用将显示本地通知通知成功购买后的用户。

此时，只要用户点击这些更新会显示一条消息，在购买已启动，以便它们可以放心地删除其静电腕带和结束快速交互此时购买按钮。 更高版本向他们通知用户通知中的事务成功与否。 这种方式，用户仅与交互应用程序进程的"活动"阶段。

对于正在网络的应用程序，他们可以使用背景`NSURLSession`以处理与下载任务的网络通信。 这将允许应用程序以在后台处理的下载的信息唤醒。 对于需要后台处理的应用，使用后台任务断言来处理所需的处理。

## <a name="quick-interaction-design-tips"></a>快速交互设计提示

由于快速交互所需的长度为两秒或更少，开发人员应专注于从一开始设计过程的应用程序的交互的设计。 查找的区域，以便这些交互可以 （使用上面介绍的技术），来简化，并使用 watchOS 3 的新功能来快速且高度可响应使应用程序。

Apple 提供的以下建议：

- 通过将应用的最常用的功能，从而转发专注于快速交互。
- 使用复杂性和用户通知展示常见特性和功能。
- 使用 SceneKit 和 SpriteKit 创建丰富，方便查看用户界面。
- 只要有可能，简化应用程序中的导航。
- 切勿使用户等待，允许它们进行删除其静电腕带并尽可能快地取消与该应用程序。

## <a name="summary"></a>总结

本文介绍了快速交互技术 Apple 都增加 watchOS 3 以及如何实现它们在 Xamarin.iOS 转换为 Apple Watch 中。

## <a name="related-links"></a>相关链接

- [watchOS 示例](https://developer.xamarin.com/samples/watchos/all/)
