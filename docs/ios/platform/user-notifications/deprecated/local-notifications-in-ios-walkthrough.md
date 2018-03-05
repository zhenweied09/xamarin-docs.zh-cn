---
title: "演练-在 Xamarin.iOS 中使用本地通知"
description: "在本部分中我们将演练如何在 Xamarin.iOS 应用程序中使用本地通知。 它将演示创建和发布将弹出警报时接收的应用程序的通知的基础知识。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 72a66fae7a1d4554643c1b52796256cc0b3dbd31
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>演练-在 Xamarin.iOS 中使用本地通知

_在本部分中我们将演练如何在 Xamarin.iOS 应用程序中使用本地通知。它将演示创建和发布将弹出警报时接收的应用程序的通知的基础知识。_

> [!IMPORTANT]
> **注意：**此部分中的信息与 iOS 9 和之前，它仍处于此处以支持较旧的 iOS 版本。 对于 iOS 10 及更高版本，请参阅[用户通知 Framework 指南](~/ios/platform/user-notifications/index.md)支持的 iOS 设备上的本地和远程通知。

## <a name="walkthrough"></a>演练

允许创建的简单应用程序将显示运行中的本地通知。 此应用程序将在其上有这样一个按钮。 当我们单击按钮时，它将创建本地通知。 经过指定时间段后，我们将看到显示的通知。


1. 在适用于 Mac 的 Visual Studio，创建一个新的单一视图 iOS 解决方案，并对其`Notifications`。
1. 打开`Main.storyboard`文件中，并将按钮拖动到该视图。 将该按钮命名**按钮**，并为其提供标题**添加通知**。 你可能还想要设置某些[约束](~/ios/user-interface/designer/designer-auto-layout.md)到按钮在此点： 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "设置在按钮上的一些约束")
1. 编辑`ViewController`类，并将以下事件处理程序添加到 ViewDidLoad 方法：

    ```csharp
    button.TouchUpInside += (sender, e) =>
    {
        // create the notification
        var notification = new UILocalNotification();

        // set the fire date (the date time in which it will fire)
        notification.FireDate = NSDate.FromTimeIntervalSinceNow(60);

        // configure the alert
        notification.AlertAction = "View Alert";
        notification.AlertBody = "Your one minute alert has fired!";

        // modify the badge
        notification.ApplicationIconBadgeNumber = 1;

        // set the sound to be the default sound
        notification.SoundName = UILocalNotification.DefaultSoundName;

        // schedule it
        UIApplication.SharedApplication.ScheduleLocalNotification(notification);
    };
    ```

    此代码将创建使用声音，该图标标记的值设置为 1，并为用户显示警报的通知。

1. 接下来编辑文件`AppDelegate.cs`，首先将添加到下面的代码`FinishedLaunching`方法。 我们已检查，以确定设备正在运行 iOS 8，如果我们正在**必需**寻求接收通知的用户的权限：

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
            );

            application.RegisterUserNotificationSettings (notificationSettings);
        }
    ```

1. 仍在`AppDelegate.cs`，添加以下方法，将收到通知时调用：

    ```csharp
    public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
            {
                // show an alert
                UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
                okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

                Window.RootViewController.PresentViewController(okayAlertController, true, null);

                // reset our badge
                UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
            }

    ```

1. 我们需要处理通知由于本地通知程序启动时所在的情况。 编辑该方法`FinishedLaunching`中`AppDelegate`包括下面的代码段：


    ```csharp
    // check for a notification

    if (launchOptions != null)
    {
        // check for a local notification
        if (launchOptions.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
        {
            var localNotification = launchOptions[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
            if (localNotification != null)
            {
                UIAlertController okayAlertController = UIAlertController.Create(localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
                okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

                Window.RootViewController.PresentViewController(okayAlertController, true, null);

                // reset our badge
                UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
            }
        }
    }

    ```

1. 最后，运行该应用程序。 在 iOS 8 中，将提示您允许通知。 单击**确定**，然后单击**添加通知**按钮。 片刻后你应看到警报对话框中，如以下屏幕截图中所示：

    ![](local-notifications-in-ios-walkthrough-images/image0.png "确认能够发送通知") ![ ](local-notifications-in-ios-walkthrough-images/image1.png "添加通知按钮") ![ ](local-notifications-in-ios-walkthrough-images/image2.png "通知警报对话框")

## <a name="summary"></a>摘要

本演练演示了如何使用各种 API 创建和发布在 iOS 中的通知。 它还演示了如何使用以向用户提供一些应用程序特定反馈的徽章更新应用程序图标。


## <a name="related-links"></a>相关链接

- [本地通知 （示例）](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [本地和推送通知编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
