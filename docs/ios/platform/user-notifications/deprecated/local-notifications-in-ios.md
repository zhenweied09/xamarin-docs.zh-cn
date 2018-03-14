---
title: "在 Xamarin.iOS 的通知"
description: "本部分演示如何在 Xamarin.iOS 中实现本地通知。 它将解释 iOS 通知的各种 UI 元素，并讨论 API 的涉及创建和显示一条通知。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 2a8ae55f9cc3e2dd4818dec96a35017c76cc9623
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="notifications-in-xamarinios"></a>在 Xamarin.iOS 的通知

_本部分演示如何在 Xamarin.iOS 中实现本地通知。它将解释 iOS 通知的各种 UI 元素，并讨论 API 的涉及创建和显示一条通知。_

> [!IMPORTANT]
> **注意：**此部分中的信息与 iOS 9 和之前，它仍处于此处以支持较旧的 iOS 版本。 对于 iOS 10 及更高版本，请参阅[用户通知 Framework 指南](~/ios/platform/user-notifications/index.md)支持的 iOS 设备上的本地和远程通知。

iOS 具有三种方法可以指示用户已收到通知：

-  **声音或振动**-iOS 可以播放声音通知用户。 如果禁用声音，则该设备可以配置能振动。
-  **警报**-可以使用通知有关的信息在屏幕上显示一个对话框。
-  **徽章**-在发布通知后，可以显示一个数字 （带有徽章） 的应用程序图标。


iOS 还提供了*通知中心*将向用户显示所有通知，本地和远程。 用户可以访问此向下轻扫从屏幕顶部：

 ![](local-notifications-in-ios-images/image13.png "通知中心")

## <a name="creating-local-notifications-in-ios"></a>在 iOS 中创建本地通知

iOS 使相当简单，若要创建和处理本地通知。
首先，iOS 8 要求应用程序要求的显示通知用户的权限。 在尝试发送本地通知之前将下面的代码添加到你的应用程序-附加的示例将其放入**AppDelegate**的**FinishedLaunching**方法。

```csharp
var settings = UIUserNotificationSettings.GetSettingsForTypes(
  UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound
  , null);
UIApplication.SharedApplication.RegisterUserNotificationSettings (settings);
```

  [![](local-notifications-in-ios-images/image0-sml.png "确认能够发送本地通知")](local-notifications-in-ios-images/image0.png#lightbox)

若要计划本地通知你创建`UILocalNotification`对象，设置`FireDate`，并将其通过安排`ScheduleLocalNotification`方法`UIApplication.SharedApplication`对象。 下面的代码段将显示将激发一分钟在将来，并显示了一条消息的警报的通知：

```csharp
UILocalNotification notification = new UILocalNotification();
NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

下面的屏幕截图显示此警报如下所示：

  [![](local-notifications-in-ios-images/image2-sml.png "示例警报")](local-notifications-in-ios-images/image2.png#lightbox)

请注意，如果用户选择到*不允许*将显示通知，然后执行任何操作。

如果你想要应用到应用程序图标具有大量的徽章，你可以设置它如以下代码行中所示：

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

在顺序播放声音带有图标，将 SoundName 属性设置的通知中下面的代码段所示：

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

每 Apple 人机接口指南中，如果通知播放声音，它都应该还附带锁屏提醒和警报来帮助用户确定应用程序发出警报。 此外，如果声音的长度超过 30 秒，iOS 将播放默认声音相反。

> [!IMPORTANT]
> **请注意**： 是一个 bug，将激发委托通知两次的 iOS 模拟器中。 在设备上运行应用程序时，不应出现此问题。

## <a name="handling-notifications"></a>处理通知

iOS 应用程序处理方式几乎完全相同的远程和本地通知。 当运行应用程序时，`ReceivedLocalNotification`方法或`ReceivedRemoteNotification`将调用 AppDelegate 类上的方法，并且将作为参数传递的通知信息。

应用程序可以在不同的方式中处理一条通知。 例如，应用程序可能只显示警报以提醒用户的一些事件。 或通知可能用于向过程已完成，如正在同步到服务器的文件的用户显示警报。

下面的代码演示如何处理本地通知和显示警报，并将徽章数量重置为零：

```csharp
 public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
        {
            // show an alert
            UIAlertController okayAlertController = UIAlertController.Create (notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
            okayAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));
            viewController.PresentViewController (okayAlertController, true, null);

            // reset our badge
            UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
        }
```

如果应用程序未运行，iOS 将播放声音和/或更新图标徽章 （如果适用）。 当用户启动与警报关联的应用程序时，应用程序将启动将调用应用程序委托的 FinishedLaunching 方法和通知信息将通过选项参数中传递。 如果选项字典包含键`UIApplication.LaunchOptionsLocalNotificationKey`，AppDelegate 知道应用程序已启动从本地通知。 下面的代码段演示了此过程：

```csharp
// check for a local notification
if (options.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
 {
     var localNotification = options[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
     if (localNotification != null)
     {
        UIAlertController okayAlertController = UIAlertController.Create (localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));
        viewController.PresentViewController (okayAlertController, true, null);

         // reset our badge
         UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
     }
 }
```

如果它是选项字典将改为包含一个远程通知`LaunchOptionsRemoteNotificationKey`，并将该注册表项的生成值`NSDictionary`具有远程通知负载对象。 你可以提取通过警报、 锁屏提醒和声音密钥通知负载。 下面的代码段演示如何获取远程通知：

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>摘要

本部分介绍了如何创建和发布在 Xamarin.iOS 的通知。 它显示如何应用程序可能响应通知通过重写`ReceivedLocalNotification`方法或`ReceivedRemoteNotification`中的方法`AppDelegate`。


## <a name="related-links"></a>相关链接

- [本地通知 （示例）](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [本地和开发人员的推送通知](https://developer.apple.com/notifications/)
- [本地和推送通知编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
