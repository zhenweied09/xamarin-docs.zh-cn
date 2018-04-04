---
title: WatchOS 简介
description: WatchOS 解决方案结构和限制概述
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 0a0adbab54fc134eaf2e69cc8088713e54b15d3b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-watchos"></a>WatchOS 简介

> [!NOTE]
> 签出[简介 watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)有关最新功能的概述。

## <a name="about-watchos"></a>有关 watchOS

WatchOS 应用程序解决方案具有 3 个项目：

- **观看扩展**– 包含 watch 应用的代码的项目。
- **观看应用**– 包含的用户界面情节提要和资源。
- **iOS 父应用**– 此应用是正常的 iPhone 应用。 监视应用程序，并扩展打包为传递到用户的监视的 iphone 版应用中。

在 watchOS 1 应用中，在扩展代码在 iPhone 上运行 – Apple Watch 实际上是外部显示。 watchOS 2 和 3 应用完全在 Apple Watch 上运行。 这种差异下图所示：

[ ![](intro-to-watchos-images/arch-sml.png "此图显示了 watchOS 1 和 watchOS 2 （和更高版本） 之间的差异")](intro-to-watchos-images/arch.png#lightbox)

无论 watchOS 哪个版本为目标，在 Visual Studio 中的 Mac 的解决方案板的完整解决方案将如下所示：

[![](intro-to-watchos-images/projectstructure-sml.png "解决方案填充")](intro-to-watchos-images/projectstructure.png#lightbox)

*父应用*在 watchOS 解决方案是正则 iOS 应用程序。 这是可见的解决方案中的唯一项目**手机上**。 此应用程序的使用情况下会包括教程、 管理屏幕和中间层筛选，cacheing，等等。但是，它是用户可以安装和运行而不监视应用程序/扩展**曾经**打开父应用程序，因此如果你需要对父应用针对一次性初始化或管理，运行需要进行编程你监视要告知用户的应用程序/扩展的。

尽管父应用程序提供监视应用程序和扩展，它们将在不同的沙箱中运行。

上 watchOS 1 中，它们可以共享数据通过共享的应用程序组或静态函数`WKInterfaceController.OpenParentApplication`，这将触发`UIApplicationDelegate.HandleWatchKitExtensionRequest`父应用程序中的方法`AppDelegate`(请参阅[使用父应用](~/ios/watchos/app-fundamentals/parent-app.md))。

监视连接框架将用于父应用程序中，与通信的 watchOS 2 或更高版本上使用`WCSession`类。

## <a name="application-lifecycle"></a>应用程序生命周期

在监视扩展中的一个子类`WKInterfaceController`类创建为每个情节提要场景。

这些`WKInterfaceController`类是类似于`UIViewController`iOS 编程中的对象，但不是能对视图的访问的同一级别。
例如，你无法动态将控件添加到或重构你的 UI。
可以但是，隐藏和显示控件并，对于某些控件，更改其大小、 透明和外观选项。

生命周期`WKInterfaceController`对象涉及以下调用：

- [唤醒](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.Awake/)： 您应在此方法中执行大部分你初始化。
- [WillActivate](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.WillActivate/) ： 调用前不久 Watch 应用向用户显示。 使用此方法可执行最后时刻初始化、 开始动画，等等。
- 此时，显示监视应用程序和扩展开始响应用户输入和更新每个应用程序逻辑的监视应用程序的显示。
- [DidDeactivate](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.DidDeactivate/)后 Watch 应用已由用户已关闭，则调用此方法。 此方法返回后，用户界面控件不能修改下一次之前`WillActivate`调用。 如果为 iPhone 连接已断开，则还将调用此方法。
- 扩展已被停用后，它都无法访问你的程序。 挂起的异步函数**不将**调用。 监视工具包扩展不能使用后台处理模式。 如果程序由用户重新激活，但未由操作系统终止应用程序，调用的第一个方法将`WillActivate`。

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "应用程序生命周期概述")

## <a name="types-of-user-interface"></a>类型的用户界面

有三种类型的用户可以具有与你监视的应用程序的交互。
所有进行编程，使用的自定义子类`WKInterfaceController`，因此前面讨论过的生命周期序列普遍适用 (通知进行编程中使用的子类`WKUserNotificationController`，后者本身是的一个子类`WKInterfaceController`):

### <a name="normal-interaction"></a>正常的交互

监视应用/扩展交互的大多数将使用的子类`WKInterfaceController`用于对应于后台监视应用程序中编写**Interface.storyboard**。 详细地介绍[安装](~/ios/watchos/get-started/installation.md)和[入门](~/ios/watchos/get-started/index.md)文章。
下图显示了一部分[监视工具包目录](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)示例的情节提要。 此处介绍了每个场景，没有相应的自定义`WKInterfaceController`(`LabelDetailController`， `ButtonDetailController`，`SwitchDetailController`等) 在扩展项目。

![](intro-to-watchos-images/scenes.png "正常交互示例")

### <a name="notifications"></a>通知

[通知](~/ios/watchos/platform/notifications.md)适用于 Apple Watch 的主要用例。 支持本地和远程通知。 与通知交互发生在两个阶段，调用短和 long 类型的值查找。

短如下所示的简要显示，并且显示监视应用程序图标，其名称和标题 (与指定`WKInterfaceController.SetTitle`)。

系统提供长查找将合并**框格**区域和解除与你自定义的情节提要基于内容的按钮。

`WKUserNotificationInterfaceController` 扩展`WKInterfaceController`使用方法`DidReceiveLocalNotification`和`DidReceiveRemoteNotification`。
重写这些方法来对通知事件做出响应。

有关对通知 UI 设计的详细信息，请参阅[Apple Watch 人工界面指南](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "示例通知")

## <a name="screen-sizes"></a>屏幕大小

Apple Watch 具有两种字体大小： 38 mm 和 42 mm，同时使用 5:4 显示比率和 Retina 显示。 其可用的大小为：

- 38 mm: 136 x 170 逻辑像素为单位 （272 x 340 物理像素为单位）
- 42 mm: 156 x 195 逻辑像素 （312 x 390 物理像素为单位）。

使用`WKInterfaceDevice.ScreenBounds`确定上显示的你监视的应用程序是否正在运行。

通常情况下，很容易地开发具有多约束的 38 mm 显示文本和布局设计，然后向上扩展。
如果启动具有较大的环境时，向下缩放可能会导致繁琐的重叠或文本截断。

阅读更多有关[处理屏幕尺寸](~/ios/watchos/app-fundamentals/screen-sizes.md)。


## <a name="limitations-of-watchos"></a>WatchOS 的限制

有 watchOS 需要注意的开发 watchOS 应用时的一些限制：

- Apple Watch 设备有限存储-下载较大的文件 （如之前应注意的可用空间 电影或音频文件）。

- 许多 watchOS[控件](~/ios/watchos/user-interface/index.md)UIKit，在具有类似物但是不同的类 (`WKInterfaceButton`而非`UIButton`，`WKInterfaceSwitch`为`UISwitch`等)，并且具有一组有限的方法相比其 UIKit等效项。 此外，watchOS 具有一些控件，如`WKInterfaceDate`（对于显示的日期和时间） 不具有该 UIKit。

  - 无法将路由到手表仅，通知或 iPhone 仅 （哪种控件用户已通过路由不已经宣布 apple）。

一些其他已知的限制 / 常见问题：

- Apple 将不允许第三方自定义监视表面。

- 允许监视来控制连接的手机上的 iTunes 的 Api 是私有类型。


## <a name="further-reading"></a>其他阅读材料

请查看 Apple 的文档：

* [为监视工具包开发](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

* [观看工具包编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

* [Apple Watch 人机接口指南](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)


## <a name="related-links"></a>相关链接

- [watchOS 3 目录 （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [watchOS 1 目录 （示例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [安装程序并安装](~/ios/watchos/get-started/installation.md)
- [第一个 Watch 应用视频](http://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 的开发的监视包指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Apple 的 WatchKit 提示](https://developer.apple.com/watchkit/tips/)
- [watchOS 3 简介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
