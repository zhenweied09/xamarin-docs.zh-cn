---
title: 在 iOS 中的 Backgrounding 简介
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 3a82a34a37e53e0c6922ef47717a4100576c2277
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-backgrounding-in-ios"></a>在 iOS 中的 Backgrounding 简介

iOS 控制后台处理非常紧密，并提供了三种方法来实现：

-  **注册后台任务**-如果应用程序需要完成一项重要任务，它可以要求 iOS 无法中断时应用程序会移动到后台任务。 例如，应用程序可能需要完成日志记录在用户或完成下载较大的文件。
-  **作为背景需要应用程序注册**-应用程序可以将注册为特定类型的应用程序具有已知特定 backgrounding 要求，如*音频*， *VoIP* ， *外部附件*， *Newsstand* ，和*位置*。 这些应用程序允许连续的后台处理权限，只要它们将执行的已注册应用程序类型的参数中的任务。
-  **启用后台更新**-应用程序可以触发后台更新*区域监视*或通过侦听*位置的重大更改*。 截至 iOS 7，应用程序还可以注册以更新内容在后台中使用*背景提取*或*远程通知*。


## <a name="application-states-and-application-delegate-methods"></a>应用程序状态和应用程序委托方法

我们深入探讨后台处理 iOS 中的代码之前，我们需要了解如何 backgrounding 影响 iOS 应用程序的生命周期。

IOS 应用程序生命周期是应用程序状态和方法将移动它们之间的集合。 应用程序可以基于用户的行为和应用程序的 backgrounding 要求的状态之间转换。 下图所示移动：

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "应用程序状态和应用程序委托方法的关系图")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

-  **未运行**-应用程序尚未尚未启动在设备上。
-  **运行/主动**-应用程序在屏幕上，并且在前台执行代码。
-  **非活动**-传入的电话呼叫、 文本或其他中断中断应用程序。
-  **Backgrounded** -应用程序会移动到后台并继续执行后台代码。
-  **已挂起**-如果应用程序没有要在后台运行任何代码或应用程序如果已完成的所有代码，将会*Suspended*操作系统。 挂起应用程序的进程保持活动状态，但应用程序无法在此状态下执行的任何代码。
-  **返回到不运行/终止 (Rare)** -有时，应用程序的进程将被销毁，并在应用程序返回到*未运行*状态。 在内存不足的情况下，将发生这种情况或如果用户手动终止应用程序。


自从引入了多任务支持，iOS 极少数情况下终止空闲的应用程序，并改为将保留它们的进程*Suspended*在内存中。 保持应用程序的进程处于活动状态，以确保应用程序快速启动的下次用户打开它。 它还意味着应用程序可以从自由移动*Suspended*回状态*Backgrounded*状态不会在系统资源上绘制。 iOS 7 利用此功能包含使应用程序能够在设备进入睡眠状态，而无需用户交互，以及更多背景从直接的更新内容时暂停后台任务的新 Api。 我们将介绍中的新 Api [iOS Backgrounding 技术](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)。

## <a name="application-lifecycle-methods"></a>应用程序生命周期方法

IOS 应用程序状态更改，通知应用程序通过事件方法`AppDelegate`类：

-  `OnActivated` -这首次启动应用程序，调用后，每当应用程序再次置于前台。 这是将需要运行每次打开应用程序的代码放入的位置。
-  `OnResignActivation` -如果用户将收到中断例如短信或电话呼叫，调用此方法获取并应用暂时停用。 用户应接受电话呼叫，应用程序将发送到背景。
-  `DidEnterBackground` -当应用程序进入 backgrounded 的状态时调用，此方法，应用程序大约 5 秒钟内做好可能终止。 使用此时间来保存用户数据和任务，并从屏幕中删除敏感信息。
-  `WillEnterForeground` -当用户返回到 backgrounded 或挂起应用程序，并将它启动置于前台，`WillEnterForeground`会被调用。 这是准备应用以使前台解冻期间保存任何状态的时间`DidEnterBackground`。  `OnActivated` 将此方法完成后立即调用。
-  `WillTerminate` -应用程序关闭的情况下，并销毁其进程。 如果多任务未在设备或操作系统版本中，可用，如果内存较低，或在用户手动终止 backgrounded 应用程序仅获取调用此方法。 请注意，将不会调用挂起的应用程序获取终止`WillTerminate`。


下图说明了如何应用程序状态和生命周期方法组合在一起：

 [![](introduction-to-backgrounding-in-ios-images/image2.png "此图说明了如何应用程序状态和生命周期方法组合在一起")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>在 iOS 中 Backgrounding 的用户控件

iOS 7 引入了几个功能，为用户提供更好地控制应用程序的 backgrounded 状态。 应用程序切换器和后台应用程序刷新设置会影响应用程序生命周期。

### <a name="app-switcher"></a>应用程序切换器

应用程序切换器是一项重要的控制功能在 iOS 7 中引入的。 它由双点击启动**主页**按钮，并显示其进程都处于活动状态的应用程序：

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "使用应用程序切换器应用之间移动")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

使用应用程序切换器，用户可以滚动浏览所有 backgrounded 和挂起的应用程序的快照。 点击应用程序将启动置于前台。 向上轻扫从后台，正在终止其进程中删除应用程序。 我们将采用详述中应用切换器[iOS 应用程序生命周期演示](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)中下一节。

> [!IMPORTANT]
> 应用程序切换器不显示 backgrounded 和挂起应用程序之间的差异。



### <a name="background-app-refresh-settings"></a>后台应用程序刷新设置

iOS 7 增加用户对应用程序生命周期的控制通过允许用户选择退出应用程序 backgrounding[为后台处理注册](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)。 *这不会阻止应用程序在运行后台任务*。

用户可以更改此设置，通过导航到<span class="uiitem">设置 > 常规 > 后台应用程序刷新</span>和编辑所选应用程序的 backgrounding 特权。 如果后台应用程序刷新设置为 off，则将立即挂起进入后台应用程序，并将其禁止，否则任何后台处理：

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "后台应用程序刷新设置")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

开发人员可以检查使用的后台刷新应用程序状态`BackgroundRefreshStatus`API。 有关示例，请参阅[检查后台刷新设置配方](https://developer.xamarin.com/recipes/ios/multitasking/check_background_refresh_setting/)。

我们已介绍 iOS 应用程序生命周期，以及用于控制应用程序生命周期的功能的基础知识。 接下来，我们来看看在操作中的 iOS 应用程序生命周期。

