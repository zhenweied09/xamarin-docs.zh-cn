---
title: 服务通知
description: 本指南讨论如何 Android 服务可能使用本地通知调度到用户的信息。
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 65ccb94bd4fac1f3818b4f08eb34ecf73d6c52e4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="service-notifications"></a>服务通知

_本指南讨论如何 Android 服务可能使用本地通知调度到用户的信息。_


## <a name="service-notifications-overview"></a>服务通知概述

即使 Android 应用程序不在前台服务通知功能允许应用程序以向用户显示信息。 它有可能的通知，以便提供用户，例如显示从应用程序的活动的操作。 下面的代码示例演示服务如何可能调度到用户的通知：

```csharp
[Service]
public class MyService: Service 
{
    // A notification requires an id that is unique to the application.
    const int NOTIFICATION_ID = 9000;
    
    public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
    {
        // Code omitted for clarity - here is where the service would do something.
    
        // Work has finished, now dispatch anotification to let the user know.
        Notification.Builder notificationBuilder = new Notification.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_notification_small_icon)
            .SetContentTitle(Resources.GetString(Resource.String.notification_content_title))
            .SetContentText(Resources.GetString(Resource.String.notification_content_text));
        
        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.Notify(NOTIFICATION_ID, notificationBuilder.Build());
    }
}
```

此屏幕截图是显示通知的示例：

[![在状态栏中显示的通知图标](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

当用户幻灯片向下的顶部的通知屏幕时，将显示完整的通知：

![Notication 通知栏中显示](service-notifications-images/02-fullnotification.png)


## <a name="updating-a-notification"></a>更新通知

若要更新通知，该服务将重新发布通知使用相同的通知 id。 Android 将显示或更新根据需要在状态栏中的通知。

```csharp 
void UpdateNotification(string content)
{
   var notification = GetNotification(content, pendingIntent);

   NotificationManager notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
   notificationManager.Notify(NOTIFICATION_ID, notification);
}

Notification GetNotification(string content, PendingIntent intent)
{
   return new Notification.Builder(this)
           .SetContentTitle(tag)
           .SetContentText(content)
           .SetSmallIcon(Resource.Drawable.NotifyLg)
           .SetLargeIcon(BitmapFactory.DecodeResource(Resources, Resource.Drawable.Icon))
           .SetContentIntent(intent).Build();
}
```

中提供了有关通知的详细信息[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)部分[Android 通知](~/android/app-fundamentals/notifications/index.md)指南。


## <a name="related-links"></a>相关链接

- [在 Android 中的本地通知](~/android/app-fundamentals/notifications/local-notifications.md)
