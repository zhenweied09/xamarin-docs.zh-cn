---
title: "前景服务"
ms.topic: article
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/09/2018
ms.openlocfilehash: 96e8d1a3658a515b6b1d37cf0fdd93157954c01d
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="foreground-services"></a>前景服务

某些服务正在执行的用户是主动注意的一些任务，这些服务被称为_前景服务_。 前景服务的一个示例是会向用户提供说明驾车或步行时的应用。 即使应用是在后台，它仍然很重要服务具有足够资源以正常工作并且用户具有访问该应用程序的快速而方便方法。 对于 Android 应用程序，这意味着前景服务应接收优先级高于"常规"服务和前景服务必须提供`Notification`Android 将显示，只要该服务正在运行。
 
前景服务是创建并启动就像任何其他服务。 当服务正在启动时，它将注册自身与 Android 作为前台服务。
 
本指南将讨论必须执行以便注册前景服务，从而完成后停止服务的额外步骤。

## <a name="registering-as-a-foreground-service"></a>作为前台服务进行注册

前景服务是一种特殊类型的绑定的服务或启动的服务。 该服务，它启动后，调用[ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)方法能够将自身注册 Android 作为前台服务。   

`StartForeground` 采用两个参数，这两种是必需的：
 
* 在应用程序标识服务中是唯一一个整数值。
* A `Notification` Android 将显示在状态栏中为，只要该服务正在运行的对象。

Android 将显示在状态栏的通知，只要该服务正在运行。 通知，至少情况下，将向该服务正在运行的用户提供一个视觉提示。 理想情况下，通知应为用户提供应用程序或可能某些操作按钮控制应用程序的快捷方式。 此示例是音乐播放器&ndash;显示通知可能会按钮来暂停/播放音乐，可使退回到上一首歌曲，或跳到下一首歌曲。 

此代码段是作为前台服务注册服务的示例：   

```csharp
// This is any integer value unique to the application.
public const int SERVICE_RUNNING_NOTIFICATION_ID = 10000;

public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
{
    // Code not directly related to publishing the notification has been omitted for clarity.
    // Normally, this method would hold the code to be run when the service is started.
    
    var notification = new Notification.Builder(this)
        .SetContentTitle(Resources.GetString(Resource.String.app_name))
        .SetContentText(Resources.GetString(Resource.String.notification_text))
        .SetSmallIcon(Resource.Drawable.ic_stat_name)
        .SetContentIntent(BuildIntentToShowMainActivity())
        .SetOngoing(true)
        .AddAction(BuildRestartTimerAction())
        .AddAction(BuildStopServiceAction())
        .Build();

    // Enlist this instance of the service as a foreground service
    StartForeground(SERVICE_RUNNING_NOTIFICATION_ID, notification);
}
```

前一次的通知将显示，这是类似于以下状态栏通知：

![在状态栏中显示通知的图像](foreground-services-images/foreground-services-01.png "状态栏中显示通知的图像")

此屏幕截图显示了在两个操作允许用户对服务进行控制的通知托盘中的扩展的通知：

![显示扩展的通知的图像](foreground-services-images/foreground-services-02.png "显示扩展的通知的图像。")

中提供了有关通知的详细信息[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)部分[Android 通知](~/android/app-fundamentals/notifications/index.md)指南。

## <a name="unregistering-as-a-foreground-service"></a>注销作为前台服务

服务可以取消列出本身作为前台服务通过调用方法`StopForeground`。 `StopForeground` 不会停止该服务，但它将删除的通知图标和信号如有必要，可以向下关闭此服务的 Android。

状态栏通知显示也可能会删除通过传递`true`到方法： 

```csharp
StopForeground(true);
```

如果服务暂停通过调用`StopSelf`或`StopService`，然后将同样删除状态栏通知。


## <a name="related-links"></a>相关链接

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForegrond](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [本地通知](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
