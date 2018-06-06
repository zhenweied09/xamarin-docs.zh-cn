---
title: watchOS Xamarin 中的通知
description: 本文档介绍如何使用 Xamarin 中 watchOS 通知。 它讨论创建通知控制器，生成通知，以及测试通知。
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 42b0354f19a9e0c31b7a859d598526fddad726cd
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791905"
---
# <a name="watchos-notifications-in-xamarin"></a>watchOS Xamarin 中的通知

监视应用可以接收通知，如果包含 iOS 应用程序支持它们。 没有内置的通知处理，以便不希望这样做*需要*若要添加的其他通知支持下面所述，但是如果你想要自定义通知行为，然后读取上显示的外观。

请参阅[iOS 通知](~/ios/platform/user-notifications/deprecated/index.md)将通知支持添加到解决方案中的 iOS 应用的详细信息的文档。

## <a name="creating-notification-controllers"></a>创建通知控制器

在情节提要通知控制器有一种特殊类型的 segue 触发它们。 当你将一个新**通知接口控制器**到情节提要它将自动拥有附加 segue:

![](notifications-images/notification-storyboard1.png "包含附加 segue 的新通知接口控制器")

当通知 segue 选择可以编辑其属性：

![](notifications-images/notification-storyboard2.png "通知 segue 选")

自定义控制器后其可能类似此示例摘自 WatchKitCatalog:

![](notifications-images/notifications-segue.png "通知属性")


有两种类型的通知：

- **短外观**-由系统定义的非可滚动静态视图。

- **Long 类型的值查找**-可滚动的由你定义的可自定义视图 ！ 可以指定更简单、 静态版本和更复杂的动态版本。

### <a name="short-look-notification-controller"></a>短查看通知控制器

短外观 UI 由只是应用程序图标、 应用程序名称和通知标题字符串组成。

如果用户不会忽略通知，系统将自动切换到提供的详细信息的长时间查看通知。


### <a name="long-look-notification-controller"></a>Long 类型的值查找通知控制器

OS 决定是否以显示静态或动态视图基于许多因素影响。 你必须提供一个静态的接口，并可以有选择性地还包括了动态接口以通知。

#### <a name="static"></a>Static

静态视图应是简单和快速显示。

![](notifications-images/notification-static.png "静态视图")

#### <a name="dynamic"></a>动态

动态视图可以显示更多的数据，并提供更多交互性。

![](notifications-images/notification-dynamic.png "动态视图")


## <a name="generating-notifications"></a>生成通知

通知可以来自远程服务器 ([Apple 推送通知服务](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)，或 APNS) 或将在 iOS 应用程序中本地生成。

请参阅[iOS 通知演练](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md)有关如何生成本地通知的示例和[WatchNotifications 示例](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)有关工作示例。

必须具有本地通知`AlertTitle`设置要显示在 Apple Watch 中-上`AlertTitle`短外观界面中显示字符串。 同时`AlertTitle`和`AlertBody`都显示在通知列表中; 与`AlertBody`long 类型的值查找界面中显示。

此屏幕快照显示`AlertTitle`显示在通知列表中，与`AlertBody`long 类型的值查找界面中显示 (使用[示例代码](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)):

![](notifications-images/watch-notificationslist-sml.png "此屏幕快照显示在通知列表中显示 AlertTitle") ![ ] (notifications-images/watch-notificationcontroller-sml.png "AlertBody long 类型的值查找界面中显示")

## <a name="testing-notifications"></a>测试通知

通知 （本地和远程） 可以仅正确测试在设备上，但是可以使用模拟它们 **.json** iOS 模拟器中的文件。

### <a name="testing-on-apple-watch"></a>在 Apple Watch 上进行测试

在测试时在 Apple Watch 上的通知，请记住， [Apple 的文档](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)状态以下：

> 当用户的 iPhone 上的一个应用程序的本地或远程通知到达时，iOS 将决定是否显示该通知，在 iPhone 上或 Apple Watch 上。

这 alluding 事实该 iOS 决定是否通知将显示在 iPhone 上或在手表上。 如果收到通知时，成对的 iPhone 处于活动状态，很可能在 iPhone 上显示通知和*不*路由到手表。

若要确保在手表上显示通知，请关闭 iPhone 屏幕 （一次按电源按钮），或将其进入睡眠状态。 如果配对的监视处于范围内，有能力，已在你静电腕带正在坏，通知将存在路由，并且出现在监视文件 （伴随细微）。

### <a name="testing-on-the-ios-simulator"></a>在 iOS 模拟器上进行测试

你*必须*提供测试 JSON 负载，在 iOS 模拟器中测试通知模式时。 在中设置路径**自定义执行参数**窗口在 Visual Studio for mac。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

适用于 Mac 的 visual Studio 将显示其他选项，监视扩展器设置为时**启动项目**。
右键单击监视扩展项目并选择**运行与 > 自定义参数...**:
    
[![](notifications-images/runwith-customparams-sml.png "使用自定义属性运行")](notifications-images/runwith-customparams.png#lightbox)
    
这将打开**执行参数**窗口，其中包含**WatchKit**选项卡。选择**通知**并提供 JSON 负载，然后按**执行**在模拟器中启动监视应用程序：
    
[![](notifications-images/runwith-execargs-sml.png "选择通知负载默认")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要编辑的监视扩展中右键单击 Visual Studio 设置测试通知负载**项目属性**。 转到**调试**部分，并从列表中 （它自动列出项目中包含的所有 JSON 文件） 选择通知 JSON 文件。
    
[![](notifications-images/runwith-execargs-sml-vs.png "选择通知 JSON 文件")](notifications-images/runwith-execargs-vs.png#lightbox)

监视扩展时**启动项目**，Visual Studio 将显示其他选项，如下所示。 选择一个**通知**选项用于启动中监视应用**通知**（使用属性窗口中选定的 JSON 文件） 的模式：
    
![](notifications-images/runwith-vs.png "设备菜单")

-----

在模拟器上测试使用默认负载 JSON 文件时，默认通知控制器如下所示：

![](notifications-images/notification-debug-sml.png "示例通知")

还有可能要使用[命令行](~/ios/watchos/troubleshooting.md#command_line)启动 iOS 模拟器。

### <a name="example-notification-payload"></a>示例通知负载

在[监视工具包目录](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)存在示例是一个示例负载 JSON 文件**NotificationPayload.json** （下面列出）。

```csharp
{
    "aps": {
        "alert": "Test message content",
        "title": "Optional title",
        "category": "myCategory"
        },

        "WatchKit Simulator Actions": [
        {
            "title": "First Button",
            "identifier": "firstButtonAction"
        }
        ],

        "customKey": "Use this file to define a testing payload for your notifications. The aps dictionary specifies the category, alert text and title. The WatchKit Simulator Actions array can provide info for one or more action buttons in addition to the standard Dismiss button. Any other top level keys are custom payload. If you have multiple such JSON files in your project, you'll be able to choose between them in when selecting to debug the notification interface of your Watch App."
    }
```



## <a name="related-links"></a>相关链接

- [WatchNotifications （本地通知） （示例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)
- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Apple 的监视工具包通知文档](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
