---
title: 从 Azure 移动应用程序发送推送通知
description: Azure 通知中心提供可缩放的推送基础结构用于发送移动推送通知从任意后端向任何移动平台，同时无后端不必与不同的平台通知系统通信的复杂性。 此文章介绍了如何使用 Azure 通知中心将推送通知从 Azure Mobile Apps 实例发送到 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: A1EF400F-73F4-43E9-A0C3-1569A0F34A3B
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: cff0b85514d2e5995d09735d6ad99b7909bfacb4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="sending-push-notifications-from-azure-mobile-apps"></a>从 Azure 移动应用程序发送推送通知

_Azure 通知中心提供可缩放的推送基础结构用于发送移动推送通知从任意后端向任何移动平台，同时无后端不必与不同的平台通知系统通信的复杂性。此文章介绍了如何使用 Azure 通知中心将推送通知从 Azure Mobile Apps 实例发送到 Xamarin.Forms 应用程序。_

> [!VIDEO https://youtube.com/embed/le2lDY22xwM]

**Azure 通过推送通知中心与 Xamarin.Forms， [Xamarin 大学](https://university.xamarin.com/)**

推送通知使用以提供信息，例如一条消息，包括从后端系统上的移动设备的应用程序，以提高应用程序关注度和使用率。 可以发送通知在任何时候，即使用户不活跃地使用目标应用程序。

后端系统将推送通知发送到移动设备通过平台通知系统 (PNS) 下, 图中所示：

[![](azure-images/pns.png "平台通知系统")](azure-images/pns-large.png#lightbox "平台通知系统")

若要发送推送通知后, 端系统，请联系特定于平台的 PNS 将通知发送到客户端应用程序实例。 这会大大提高的复杂性的后端时跨平台推送通知是必需的因为后端必须使用每个特定于平台的 PNS API 和协议。

Azure 通知中心消除这种复杂性由提取的详细信息的不同平台通知系统，允许跨平台通知的发送一次的 API 调用，如下面的关系图中所示：

[![](azure-images/notification-hub.png)](azure-images/notification-hub-large.png#lightbox)

若要发送推送通知后, 端系统仅联系人 Azure 通知中心，这反过来使用不同平台通知系统进行通信，从而减小的后端复杂性代码该发送推送通知。

Azure 移动应用都使用通知中心的推送通知的内置支持。 从 Azure Mobile Apps 实例向 Xamarin.Forms 应用程序发送推送通知的过程如下所示：

1. Xamarin.Forms 应用程序注册 PNS，返回的句柄。
1. Azure Mobile Apps 实例将通知发送到 Azure 通知中心，指定将作为目标设备的句柄。
1. Azure 通知中心将通知发送到相应的 PNS 设备。
1. PNS 将通知发送到指定的设备。
1. Xamarin.Forms 应用程序处理通知，并显示它。

示例应用程序演示其数据存储在 Azure Mobile Apps 实例 todo 列表应用程序。 每次新项添加到 Azure Mobile Apps 实例中，一条推送通知发送到 Xamarin.Forms 应用程序。 以下屏幕快照显示的每个平台显示已接收的推送通知：

[![](azure-images/screenshots.png "示例应用程序接收推送通知")](azure-images/screenshots-large.png#lightbox "示例应用程序接收推送通知")

有关 Azure 通知中心的详细信息，请参阅[Azure 通知中心](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)和[向 Xamarin.Forms 应用添加推送通知](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push/)。

## <a name="azure-and-platform-notification-system-setup"></a>Azure 和平台通知系统设置

将 Azure 通知中心集成到 Azure Mobile Apps 实例的过程如下所示：

1. 创建 Azure Mobile Apps 实例。 有关详细信息，请参阅[使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 配置通知中心。 有关详细信息，请参阅[配置通知中心](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#create-hub)。
1. 更新要发送推送通知的 Azure Mobile Apps 实例。 有关详细信息，请参阅[更新服务器项目以发送推送通知](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#update-the-server-project-to-send-push-notifications)。
1. 注册每个 PNS。
1. 配置通知中心与每个 PNS 进行通信。

以下部分提供每个平台的其他设置说明。

### <a name="ios"></a>iOS

若要使用 Apple 推送通知服务 (APNS) 从 Azure 通知中心，必须开展以下附加步骤：

1. 生成证书签名请求与 Keychain Access 工具的推送证书。 有关详细信息，请参阅[生成的推送证书的证书签名请求文件](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#generate-the-certificate-signing-request-file-for-the-push-certificate)Azure 文档中心上。
1. 注册 Apple 开发人员中心上的推送通知支持 Xamarin.Forms 应用程序。 有关详细信息，请参阅[你应用程序注册推送通知](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-app-for-push-notifications)Azure 文档中心上。
1. 在 Apple 开发人员中心上创建的推送通知启用预配配置文件 Xamarin.Forms 应用程序。 有关详细信息，请参阅[创建应用程序的预配配置文件](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#create-a-provisioning-profile-for-the-app)Azure 文档中心上。
1. 配置通知中心与 APNS 通信。 有关详细信息，请参阅[的 APNS 配置通知中心](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-apns)。
1. Xamarin.Forms 应用程序配置为使用新的应用程序 ID 和预配配置文件。 有关详细信息，请参阅[在 Xamarin Studio 中配置的 iOS 项目](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-xamarin-studio)或[在 Visual Studio 中配置的 iOS 项目](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-visual-studio)Azure 文档中心上。

### <a name="android"></a>Android

若要使用 Firebase 云消息传送 (FCM) 从 Azure 通知中心，必须开展以下附加步骤：

1. 注册 FCM。 服务器 API 密钥和客户端 ID 是自动生成并打包`google-services.json`下载的文件。 有关详细信息，请参阅[启用 Firebase 云消息传送 (FCM)](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#enable-firebase-cloud-messaging-fcm)。
1. 配置通知中心与 FCM 进行通信。 有关详细信息，请参阅[配置移动应用回结束以发送推送请求使用 FCM](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm)。

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要使用 Azure 通知中心从 Windows 通知服务 (WNS)，必须开展以下附加步骤：

1. 为 Windows 通知服务 (WNS) 注册。 有关详细信息，请参阅[注册到 WNS 的推送通知你 Windows 应用](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-windows-app-for-push-notifications-with-wns)Azure 文档中心上。
1. 配置通知中心使用 WNS 通信。 有关详细信息，请参阅[的 WNS 配置通知中心](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-wns)Azure 文档中心上。

## <a name="adding-push-notification-support-to-the-xamarinforms-application"></a>向 Xamarin.Forms 应用程序添加推送通知支持

以下各节讨论在每个特定于平台的项目，需要支持推送通知的实现。

### <a name="ios"></a>iOS

在 iOS 应用程序中实现的推送通知支持的过程如下所示：

1. 注册与 Apple 推送通知服务 (APNS) 在`AppDelegate.FinishedLaunching`方法。 有关详细信息，请参阅[向 Apple 推送通知系统注册](#ios_register)。
1. 实现`AppDelegate.RegisteredForRemoteNotifications`方法以处理的注册响应。 有关详细信息，请参阅[处理注册响应](#ios_registration_response)。
1. 实现`AppDelegate.DidReceiveRemoteNotification`方法来处理传入的推送通知。 有关详细信息，请参阅[处理传入的推送通知](#ios_process_incoming)。

<a name="ios_register" />

### <a name="registering-with-the-apple-push-notification-service"></a>注册 Apple Push Notification 服务

IOS 应用程序可收到推送通知之前，它必须注册与 Apple 推送通知服务 (APNS)，这将生成唯一的设备令牌并将其返回到应用程序。 在调用注册`FinishedLaunching`中重写`AppDelegate`类：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    ...
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert, new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ...
}
```

当通过 APNS 注册的 iOS 应用程序时，它必须指定想要接收的推送通知的类型。 `RegisterUserNotificationSettings`方法注册的应用程序可以接收，通知的类型与`RegisterForRemoteNotifications`注册从 APNS 中接收推送通知的方法。

> [!NOTE]
> 未能调用`RegisterUserNotificationSettings`方法将导致应用程序以无提示方式接收的推送通知。

<a name="ios_registration_response" />

### <a name="handling-the-registration-response"></a>处理的注册响应

APNS 注册请求将在后台发生。 当收到响应时，将调用 iOS`RegisteredForRemoteNotifications`中重写`AppDelegate`类：

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyAPNS}
    };

    // Register for push with the Azure mobile app
    Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
    push.RegisterAsync(deviceToken, templates);
}
```

此方法创建简单的通知消息模板为 JSON，并注册设备以接收从通知中心发送模板通知。

> [!NOTE]
> `FailedToRegisterForRemoteNotifications`应实现重写用于处理无网络连接等情况。 这是重要的因为用户可能会启动时应用程序脱机。

<a name="ios_process_incoming" />

### <a name="processing-incoming-push-notifications"></a>处理传入的推送通知

`DidReceiveRemoteNotification`中重写`AppDelegate`类用于处理传入的推送通知，当应用程序正在运行，而收到通知时调用：

```csharp
public override void DidReceiveRemoteNotification(
    UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
    NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

    string alert = string.Empty;
    if (aps.ContainsKey(new NSString("alert")))
        alert = (aps[new NSString("alert")] as NSString).ToString();

    // Show alert
    if (!string.IsNullOrEmpty(alert))
    {
      var notificationAlert = UIAlertController.Create("Notification", alert, UIAlertControllerStyle.Alert);
      notificationAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Cancel, null));
      UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(notificationAlert, true, null);
    }
}
```

`userInfo`字典包含`aps`键，其值是`alert`利用剩余的通知数据的字典。 检索此字典时，与`string`在对话框中显示的通知消息。

> [!NOTE]
> 如果应用程序未运行在一条推送通知到达时，将启动应用程序但`DidReceiveRemoteNotification`方法不会处理通知。 相反，获取通知负载并做出相应响应从`WillFinishLaunching`或`FinishedLaunching`重写。

有关 APNS 的详细信息，请参阅[在 iOS 中的推送通知](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)。

### <a name="android"></a>Android

在 Android 应用程序中实现的推送通知支持的过程如下所示：

1. 添加[Xamarin.Firebase.Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) NuGet 包到的 Android 项目，并设置应用程序的目标版本为 Android 7.0 或更高。
1. 添加`google-services.json`文件，从 Firebase 控制台中，下载到 Android 项目的根目录，并将其生成操作设置为**GoogleServicesJson**。 有关详细信息，请参阅[添加 Google 服务 JSON 文件](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。
1. 通过声明 Android 清单中的接收方注册与 Firebase 云消息传送 (FCM) 文件中，并通过实现`FirebaseRegistrationService.OnTokenRefresh`方法。 有关详细信息，请参阅[Firebase Cloud Messaging 注册](#android_register_fcm)。
1. 注册到 Azure 通知中心`AzureNotificationHubService.RegisterAsync`方法。 有关详细信息，请参阅[向 Azure 通知中心注册](#android_register_azure)。
1. 实现`FirebaseNotificationService.OnMessageReceived`方法来处理传入的推送通知。 有关详细信息，请参阅[显示内容的推送通知](#android_displaying_notification)。

有关 Firebase 云消息传送的详细信息，请参阅[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)和[远程通知 Firebase Cloud Messaging](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md)。

<a name="android_register_fcm" />

#### <a name="registering-with-firebase-cloud-messaging"></a>注册 Firebase 云消息传送

Android 应用程序可收到推送通知之前，它必须注册 FCM，这将生成的注册令牌并将其返回到应用程序。 有关注册令牌的详细信息，请参阅[注册到 FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#registration)。

这是通过实现的：

- 声明 Android 清单中的接收方。 有关详细信息，请参阅[声明 Android 清单中的接收方](#declaring_a_receiver)。
- 实现 Firebase 实例 ID 服务。 有关详细信息，请参阅[实现 Firebase 实例 ID 服务](#implementing-firebase-instance-id-service)。

<a name="declaring_a_receiver" />

##### <a name="declaring-the-receiver-in-the-android-manifest"></a>声明 Android 清单中的接收方

编辑**AndroidManifest.xml**和插入以下`<receiver>`元素到`<application>`元素：

```xml
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
  <intent-filter>
    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
    <category android:name="${applicationId}" />
  </intent-filter>
</receiver>
```

此 XML 将执行以下操作：

- 声明内部`FirebaseInstanceIdInternalReceiver`用于安全地启动服务的实现。
- 声明`FirebaseInstanceIdReceiver`实现，提供有关每个应用程序实例的唯一标识符。 此接收器还进行身份验证和授权操作。

`FirebaseInstanceIdReceiver`接收`FirebaseInstanceId`和`FirebaseMessaging`事件和传递到类派生自这些`FirebasesInstanceIdService`。

<a name="implementing-firebase-instance-id-service" />

##### <a name="implementing-the-firebase-instance-id-service"></a>实现 Firebase 实例 ID 服务

FCM 中注册应用程序通过派生类从`FirebaseInstanceIdService`类。 此类负责生成授权客户端应用程序访问 FCM 的安全令牌。 在示例应用程序`FirebaseRegistrationService`类派生自`FirebaseInstanceIdService`类并在下面的代码示例所示：

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    const string TAG = "FirebaseRegistrationService";

    public override void OnTokenRefresh()
    {
        var refreshedToken = FirebaseInstanceId.Instance.Token;
        Log.Debug(TAG, "Refreshed token: " + refreshedToken);
        SendRegistrationTokenToAzureNotificationHub(refreshedToken);
    }

    void SendRegistrationTokenToAzureNotificationHub(string token)
    {
        // Update notification hub registration
        Task.Run(async () =>
        {
            await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
        });
    }
}
```

`OnTokenRefresh`当应用程序收到来自 FCM 注册令牌时，会调用方法。 此方法检索从令牌`FirebaseInstanceId.Instance.Token`属性，用于通过 FCM 以异步方式更新。 `OnTokenRefresh`很少调用方法，因为安装或卸载，当用户中删除应用程序数据时应用程序清除实例 ID，应用程序时，才会更新令牌时的安全令牌已被或泄露。 此外，FCM 实例 ID 服务将请求，应用程序刷新其令牌定期，通常每 6 个月。

`OnTokenRefresh`方法也会调用`SendRegistrationTokenToAzureNotificationHub`方法，用于将用户的注册令牌与 Azure 通知中心相关联。

<a name="android_register_azure" />

#### <a name="registering-with-the-azure-notification-hub"></a>向 Azure 通知中心注册

`AzureNotificationHubService`类提供`RegisterAsync`方法，将用户的注册令牌与 Azure 通知中心相关联。 下面的代码示例演示`RegisterAsync`方法，通过调用`FirebaseRegistrationService`类用户的注册令牌更改时：

```csharp
public class AzureNotificationHubService
{
    const string TAG = "AzureNotificationHubService";

    public static async Task RegisterAsync(Push push, string token)
    {
        try
        {
            const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBody}
            };

            await push.RegisterAsync(token, templates);
            Log.Info("Push Installation Id: ", push.InstallationId.ToString());
        }
        catch (Exception ex)
        {
            Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
        }
    }
}
```

此方法创建简单的通知消息模板为 JSON 和寄存器接收来自使用 Firebase 注册令牌的通知中心模板通知。 这可确保从 Azure 通知中心发送任何通知都将针对注册令牌所表示的设备。

<a name="android_displaying_notification" />

#### <a name="displaying-the-contents-of-a-push-notification"></a>显示一条推送通知的内容

显示一条推送通知的内容通过派生类从`FirebaseMessagingService`类。 此类包括可重写`OnMessageReceived`方法，调用应用程序接收来自 FCM 的通知时，提供在前台运行该应用程序。 在示例应用程序`FirebaseNotificationService`类派生自`FirebaseMessagingService`类，并在下面的代码示例所示：

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseNotificationService : FirebaseMessagingService
{
    const string TAG = "FirebaseNotificationService";

    public override void OnMessageReceived(RemoteMessage message)
    {
        Log.Debug(TAG, "From: " + message.From);

        // Pull message body out of the template
        var messageBody = message.Data["message"];
        if (string.IsNullOrWhiteSpace(messageBody))
            return;

        Log.Debug(TAG, "Notification message body: " + messageBody);
        SendNotification(messageBody);
    }

    void SendNotification(string messageBody)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
            .SetContentTitle("New Todo Item")
            .SetContentText(messageBody)
            .SetContentIntent(pendingIntent)
            .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
            .SetAutoCancel(true);

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }
}
```

应用程序收到通知后从 FCM，`OnMessageReceived`方法提取消息内容，并调用`SendNotification`方法。 此方法将消息内容转换为在应用程序运行时，与通知显示在通知区域中启动的本地通知。

##### <a name="handling-notification-intents"></a>处理通知意向

当用户点击通知时，相关的通知消息的任何数据都可以在`Intent`其他功能。 此数据可以提取替换为以下代码：

```csharp
if (Intent.Extras != null)
{
  foreach (var key in Intent.Extras.KeySet())
  {
    var value = Intent.Extras.GetString(key);
    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
  }
}
```

应用程序的启动器`Intent`当用户点击其通知消息，因此此代码将记录中的任何随附的数据时触发`Intent`到输出窗口。

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP) 之前应用程序可收到推送通知，它必须注册与 Windows 通知服务 (WNS)，它将返回通知通道。 由调用注册`InitNotificationsAsync`中的方法`App`类：

```csharp
private async Task InitNotificationsAsync()
{
    var channel = await PushNotificationChannelManager
          .CreatePushNotificationChannelForApplicationAsync();

    const string templateBodyWNS =
        "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

    JObject headers = new JObject();
    headers["X-WNS-Type"] = "wns/toast";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyWNS},
        {"headers", headers} // Needed for WNS.
    };

    await TodoItemManager.DefaultManager.CurrentClient.GetPush()
          .RegisterAsync(channel.Uri, templates);
}
```

此方法获取推送通知通道、 创建通知消息模板为 JSON，并注册设备以接收从通知中心发送模板通知。

`InitNotificationsAsync`方法调用从`OnLaunched`中重写`App`类：

```csharp
protected override async void OnLaunched(LaunchActivatedEventArgs e)
{
    ...
    await InitNotificationsAsync();
}
```

这可确保推送通知注册是在创建或刷新每次启动应用程序，因此确保 WNS 推送通道始终处于活动状态。

收到推送通知时它将自动显示为*toast* – 一个无模式窗口，其中包含的消息。

## <a name="summary"></a>总结

这篇文章演示了如何使用 Azure 通知中心将推送通知从 Azure Mobile Apps 实例发送到 Xamarin.Forms 应用程序。 Azure 通知中心提供可缩放的推送基础结构用于发送移动推送通知从任意后端向任何移动平台，同时无后端不必与不同的平台通知系统通信的复杂性。


## <a name="related-links"></a>相关链接

- [使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Azure 通知中心](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)
- [向 Xamarin.Forms 应用添加推送通知](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/)
- [在 iOS 中的推送通知](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)
- [Firebase 云消息传送](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [TodoAzurePush (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)
- [Azure 的移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
