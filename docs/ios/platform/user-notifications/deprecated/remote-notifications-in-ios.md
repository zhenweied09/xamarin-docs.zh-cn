---
title: 在 iOS 中的推送通知
description: 本部分将介绍在 iOS 中的推送通知。 它引入了 Apple 推送通知网关服务和它到 iOS 应用程序的发布通知中所扮演的角色。 它将说明如何创建启用推送通知和讨论所需的安全证书。 最后本部分将讨论某些应用程序服务器必须执行能够跟踪客户端移动设备的管理任务。
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 3a86ce5e61576faec41b5fcddf899d731d2cc57a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="push-notifications-in-ios"></a>在 iOS 中的推送通知

_本部分将介绍在 iOS 中的推送通知。它引入了 Apple 推送通知网关服务和它到 iOS 应用程序的发布通知中所扮演的角色。它将说明如何创建启用推送通知和讨论所需的安全证书。最后本部分将讨论某些应用程序服务器必须执行能够跟踪客户端移动设备的管理任务。_

> [!IMPORTANT]
> 此部分中的信息与 iOS 9 和之前，它仍处于此处以支持较旧的 iOS 版本。 对于 iOS 10 及更高版本，请参阅[用户通知 Framework 指南](~/ios/platform/user-notifications/index.md)支持的 iOS 设备上的本地和远程通知。

推送通知应保持简短，并且只包含足够的数据用于通知移动应用程序，它应更新，请联系服务器应用程序。 例如，当新的电子邮件到达时，服务器应用程序将仅通知的移动应用程序收到了新的电子邮件。 通知将不会包含新的电子邮件本身。 移动应用程序便可检索新的电子邮件从服务器时适合

在中心的推送通知在 iOS 中的是*Apple 推送通知网关服务 (APNS)*。 这是由负责将通知从应用程序服务器路由到 iOS 设备的 Apple 提供的服务。
下图阐释了适用于 iOS 的推送通知拓扑： ![ ](remote-notifications-in-ios-images/image4.png "此图阐释了适用于 iOS 的推送通知拓扑")

远程通知本身是遵循格式的字符串的 JSON 格式和协议中指定[的通知负载](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1)部分[本地和推送通知编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)中[iOS 开发人员文档](https://developer.apple.com/devcenter/ios/index.action)。

Apple 维护的 APNS 的两种环境：*沙盒*和*生产*环境。 沙盒环境主要用于测试的开发阶段和可以在找到`gateway.sandbox.push.apple.com`TCP 端口 2195年。 生产环境并表示用于应用程序已部署并且可以位于`gateway.push.apple.com`TCP 端口 2195年。

## <a name="requirements"></a>要求

推送通知必须遵守规定的 APNS 的体系结构的以下规则：

-  **256 字节消息限制**-通知的整个消息大小不得超过 256 个字节。
-  **没有回执确认**-APNS 不提供任何通知，一条消息供它的目标接收方发件人。 如果设备不可用，发送多个连续的通知，除最新的所有通知将都会丢失。 仅最新的通知将发送到设备。
-  **每个应用程序需要的安全证书**-通过 SSL 必须进行与 APNS 通信。


## <a name="creating-and-using-certificates"></a>创建和使用证书

上一节中所述的环境的每个需要他们自己的证书。 本部分将介绍如何创建证书、 将其与预配配置文件中，关联和随后获取个人信息交换证书以用于与 PushSharp 一起使用。

1.  若要创建证书请转到的 iOS 设置门户 Apple 网站上如以下屏幕截图 （注意在左侧的应用 Id 菜单项） 中所示：

    [![](remote-notifications-in-ios-images/image5new.png "IOS 设置门户同类网站上")](remote-notifications-in-ios-images/image5new.png#lightbox)

2.  接下来，导航到应用 ID 部分，并创建新的应用程序 ID，如下面的屏幕截图中所示：

    [![](remote-notifications-in-ios-images/image6new.png "导航到的应用 Id 部分并创建新的应用程序 ID")](remote-notifications-in-ios-images/image6new.png#lightbox)

3.  当你单击**+**按钮，你将能够输入的描述和捆绑标识符，在应用程序 id 中的下一步的屏幕截图所示：

    [![](remote-notifications-in-ios-images/image7new.png "输入应用程序 ID 说明和捆绑标识符")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. 请务必选择**显式应用 ID**并且不会在结束捆绑标识符`*`。 这将创建适合用于多个应用程序的标识符和推送通知证书必须是单个应用程序。

1. 在应用程序服务下选择**推送通知**:

    [![](remote-notifications-in-ios-images/image8new.png "选择推送通知")](remote-notifications-in-ios-images/image8new.png#lightbox)

2. 按**提交**以确认新的应用程序 ID 的注册：

    [![](remote-notifications-in-ios-images/image9new.png "确认注册新的应用程序 ID")](remote-notifications-in-ios-images/image9new.png#lightbox)

3.  接下来，您必须为应用程序 id。 创建证书 在左侧导航窗格中，浏览到**证书 > 所有**和选择`+`按钮，如下面的屏幕截图中所示：

    [![](remote-notifications-in-ios-images/image10new.png "为应用程序 ID 创建证书")](remote-notifications-in-ios-images/image8.png#lightbox)

4.  选择是否想要使用的开发或生产证书：

    [![](remote-notifications-in-ios-images/image11new.png "选择开发或生产证书")](remote-notifications-in-ios-images/image11new.png#lightbox)

5. 然后选择刚创建的新应用程序 ID:

    [![](remote-notifications-in-ios-images/image12new.png "选择刚创建的新应用程序 ID")](remote-notifications-in-ios-images/image12new.png#lightbox)

6.  这将显示说明将指导您完成创建的过程*证书签名请求*使用**Keychain Access**在 mac 上应用程序

7.  既然已创建了证书，它必须用作生成过程的一部分对应用程序签名，以便它可以向 APNs 注册。 这需要创建和安装使用证书预配配置文件。

8.  若要创建预配配置文件的开发，导航到**预配配置文件**部分并按相关步骤来创建它，使用我们刚创建的应用程序 Id。

9.  一旦你已创建预配配置文件，打开**Xcode 组织程序**并刷新它。 如果你创建预配配置文件不显示它可能需要从 iOS 设置门户下载配置文件，并手动将其导入。 下面的屏幕快照显示组织者的示例与设置配置文件添加：

    [![](remote-notifications-in-ios-images/image13new.png "此屏幕截图与设置配置文件添加显示组织者的示例")](remote-notifications-in-ios-images/image13new.png#lightbox)

10.  此时，我们需要用于配置 Xamarin.iOS 项目以使用此新创建的预配配置文件。 这通过**项目选项**对话框下**iOS 捆绑签名**选项卡，如以下屏幕截图中显示：

    [![](remote-notifications-in-ios-images/image11.png "配置 Xamarin.iOS 项目以使用此新创建的预配配置文件")](remote-notifications-in-ios-images/image11.png#lightbox)



此时，应用程序已配置为使用推送通知。 但是，仍有几个所需的证书的详细步骤。 此证书是 DER PushSharp，需要个人信息交换 (PKCS12) 证书与不兼容的格式。 若要将证书转换，以便它可供 PushSharp，执行这些最终步骤：

1.  **下载的证书文件**-登录到 iOS 设置门户，选择证书选项卡中，选择正确的预配配置文件并选择与关联的证书**下载**。
1.  **打开 Keychain Access** -这是应用程序是在 OS X 中的密码管理系统的 GUI 接口。
1.  **导入证书**-如果尚未安装证书，**文件...导入项目**从密钥链访问菜单。 导航到更高版本，导出的证书，然后选择它。
1.  **导出证书**-展开证书，因此相关联的私钥是可见，此项上右键单击并选择导出。 系统将提示您输入文件名和导出的文件的密码。

此时我们完成证书。 我们已创建将使用 iOS 应用程序进行签名的证书，并转换该证书为服务器应用程序中可与 PushSharp 中使用的格式。 下一步让我们看一下 iOS 应用程序如何使用 APNS 进行交互。

## <a name="registering-with-apns"></a>向 APNS 注册

在 iOS 之前应用程序可以接收远程通知它必须向 APNS 注册。 将生成唯一的设备令牌 APNS 并将其返回到 iOS 应用程序。 IOS 应用程序将时，将设备标记，然后向应用程序服务器注册自身。 一旦所有此情况下，注册已完成，并且应用程序服务器可能会将通知推送到移动设备。

从理论上讲，但是实际上这不会发生，通常，设备令牌可能会更改 iOS 应用程序将将自身注册到 APNS，每次。 作为一种优化应用程序可以缓存的最新的设备标记，并仅在它会更改时更新应用程序服务器。 下图说明了注册和获取设备令牌的过程：

 ![](remote-notifications-in-ios-images/image12.png "此图描述了注册和获取设备令牌的过程")

在处理时使用 APNS 注册`FinishedLaunching`通过调用应用程序委托类的方法`RegisterForRemoteNotificationTypes`对当前`UIApplication`对象。 当向 APNS 注册的 iOS 应用程序时，它还必须指定它希望接收哪些类型的远程通知。 枚举中声明这些远程通知类型`UIRemoteNotificationType`。 下面的代码段演示了如何 iOS 应用程序注册后可以接收远程的警报和徽章通知：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

    UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications ();
} else {
    UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
    UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
}
```

在后台-发生 APNS 注册请求时收到响应时，iOS 将调用此方法`RegisteredForRemoteNotifications`中`AppDelegate`类并将已注册的设备令牌传递。 该令牌将包含在`NSData`对象。 下面的代码段演示如何检索设备标记提供该 APNS:

```csharp
public override void RegisteredForRemoteNotifications (
UIApplication application, NSData deviceToken)
{
    // Get current device token
    var DeviceToken = deviceToken.Description;
    if (!string.IsNullOrWhiteSpace(DeviceToken)) {
        DeviceToken = DeviceToken.Trim('<').Trim('>');
    }

    // Get previous device token
    var oldDeviceToken = NSUserDefaults.StandardUserDefaults.StringForKey("PushDeviceToken");

    // Has the token changed?
    if (string.IsNullOrEmpty(oldDeviceToken) || !oldDeviceToken.Equals(DeviceToken))
    {
        //TODO: Put your own logic here to notify your server that the device token has changed/been created!
    }

    // Save new device token
    NSUserDefaults.StandardUserDefaults.SetString(DeviceToken, "PushDeviceToken");
}
```

如果出于某种原因 （如设备未连接到 Internet），则注册将失败，将调用 iOS`FailedToRegisterForRemoteNotifications`委托应用程序的类。 下面的代码段演示如何向用户告知他们注册失败，显示警报：

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>设备令牌内务处理

设备令牌将过期或随时间而变化。 因为这一事实，服务器应用程序将需要进行一些内部清理，然后清除这些已过期或已更改的令牌。 当应用程序将作为已过期的令牌的设备的推送通知发送时，APNS 将记录并保存该过期的令牌。 然后，服务器可能查询 APNS 找出哪些令牌已过期。

APNS 用于提供*反馈服务*-通过创建以发送推送通知，并将备份数据有关哪些令牌已过期的证书进行身份验证的 HTTPS 终结点。 这已否决 Apple 和删除。

相反，是个用例的反馈服务先前已报告新的 HTTP 状态代码：

> 410-设备令牌不再处于活动状态的主题。

此外，新`timestamp`响应正文中将 JSON 数据密钥：

> 如果中的值： 状态标头是 410，此键的值是 APNs 确认设备令牌已不再有效的主题的最后一个时间。
>
> 停止将推送通知，直到设备与更高版本的时间戳与你的提供程序注册令牌。

## <a name="summary"></a>总结

本部分介绍针对在 iOS 中的推送通知的关键概念。 它所述的 Apple 推送通知网关服务 (APNS) 的角色。 它然后涵盖创建和使用对 APNS 至关重要的安全证书。 本文档讨论如何使用应用程序服务器已完成的最后*反馈服务*停止跟踪过期的设备令牌。


## <a name="related-links"></a>相关链接

- [通知-演示本地和远程通知 （示例）](https://developer.xamarin.com/samples/monotouch/Notifications/)
- [本地和开发人员的推送通知](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
