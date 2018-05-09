---
title: 增强型的用户通知
description: 本文涵盖所有用户通知已得到增强的 iOS 10 和如何在 Xamarin.iOS 应用程序中使用它们的方式。
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2017
ms.openlocfilehash: b27d415240f3b8cd25c4bc54f6d176c50e42a250
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="enhanced-user-notifications"></a>增强型的用户通知

_本文涵盖所有用户通知已得到增强的 iOS 10 和如何在 Xamarin.iOS 应用程序中使用它们的方式。_

新 iOS 10，框架允许在传递和本地和远程通知的处理其用户通知。 使用此框架，应用程序或应用扩展可以计划传递本地通知通过指定一组条件，例如位置或当天的时间。

## <a name="about-user-notifications"></a>有关用户通知

如上面所述，新的用户通知框架允许传递和本地和远程通知的处理。 使用此框架，应用程序或应用扩展可以计划传递本地通知通过指定一组条件，例如位置或当天的时间。

此外，应用程序或扩展可以接收 （和可能修改） 本地和远程通知在传递到用户的 iOS 设备。

该新用户通知 UI 框架允许的应用程序或应用扩展，它们会呈现给用户时，自定义的本地和远程通知的外观。

此框架提供程序应用程序可以将通知传递到用户的以下方面：

- **Visual 警报**-其中通知将向下滚动从为横幅屏幕的顶部。
- **声音和振动**-可以与通知相关联。
- **应用程序图标徽章**-显示新内容是否可用，如未读的邮件数的徽章显示应用程序的图标。

此外，具体取决于用户的当前上下文中，有不同的方式，将显示一条通知：

- 如果设备已解锁，通知将向下滚动从屏幕顶部为横幅。
- 如果设备处于锁定状态，将用户的锁定屏幕上显示通知。
- 如果用户已丢失通知，他们可以打开通知中心，并查看任何可用，等待通知。

Xamarin.iOS 应用程序具有两种类型的用户能够发送的通知：

- **本地通知**-这些发送的用户设备上本地安装的应用。
- **远程通知**-发送从远程服务器，并向用户显示或它们触发后台更新的应用程序的内容。

### <a name="about-local-notifications"></a>有关本地通知

IOS 应用可以发送本地通知具有以下功能和属性：

- 就会发送的是本地用户的设备上的应用。 
- 它们是可以配置为使用时间或位置基于触发器。 
- 应用程序与用户的设备计划通知和触发条件得以满足时显示它。
- 在用户交互并发出通知时，应用程序将收到回调。

本地通知的一些示例包括：

- 日历警报
- 提醒警报
- 位置感知触发器

有关详细信息，请参阅 Apple 的[本地和远程通知编程指南](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)文档。

### <a name="about-remote-notifications"></a>有关远程通知

IOS 应用可以发送远程通知具有以下功能和属性：

- 应用程序具有它与之通信的服务器端组件。
- Apple 推送通知服务 (APNs) 用于从开发人员的基于云的服务器传输到用户的设备的远程通知的最大努力交货。
- 当应用程序收到远程通知时，它将向用户显示。
- 在用户交互与通知时，应用程序将收到回调。

远程通知的一些示例包括：

- 新闻警报
- 体育方面的更新
- 即时消息传递消息

有可用的 iOS 应用程序的两种类型的远程通知：

- **面向用户**-这些设备上的用户向显示。
- **无提示更新**-这些提供一种机制，以更新 iOS 应用程序在后台的内容。 当收到无提示更新时，应用程序可以访问删除服务器下拉列表的最新内容。

有关详细信息，请参阅 Apple 的[本地和远程通知编程指南](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)文档。

### <a name="about-the-existing-notifications-api"></a>有关现有通知 API

在 iOS 10 之前的 iOS 应用程序将使用`UIApplication`来向系统注册通知并安排日程 （无论是时间或地点） 如何应触发该通知。

有几个使用现有的通知 API 时，开发人员可能会遇到的问题：

- 没有所需的本地或远程通知这可能导致重复代码的不同回调。
- 应用程序只有有限的通知的控件之后它已使用系统已计划了。
- 在所有 Apple 的现有平台都没有不同的支持级别。

### <a name="about-the-new-user-notification-framework"></a>有关新的用户通知框架

IOS 10，与 Apple 引入了新的用户通知框架，它将取代现有`UIApplication`方法如上所示。

用户通知 framework 提供以下功能：

- 一种熟悉 API，包括与现有框架使其轻松对端口代码的前面所用方法的功能奇偶校验。
- 包括一组扩展的内容的选项，允许更丰富的通知发送到用户。
- 由相同的代码和回调，可以处理本地和远程通知。
- 可以简化处理发送到应用程序在用户交互并发出通知时的回调的过程。
- 挂起并传送通知包括删除或更新通知的能力的增强的管理。
- 添加了执行操作的通知的应用内演示文稿的功能。
- 添加了计划和处理从应用程序扩展中的通知的功能。
- 添加新的通知本身的扩展点。 

新的用户通知 framework 提供了统一的通知 API 平台的多个 Apple 支持包括： 

- **iOS** -完全支持，以管理和计划通知。
- **tvOS** -将功能添加到本地和远程通知的徽章应用图标。
- **watchOS** -添加了转发到其 Apple Watch 来自用户的成对的 iOS 设备的通知的功能并使监视应用能够执行直接在手表上的本地通知。

有关详细信息，请参阅 Apple 的[UserNotifications Framework 参考](https://developer.apple.com/reference/usernotifications)和[UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)文档。

## <a name="preparing-for-notification-delivery"></a>为通知传递做好准备

之前提供的 iOS 应用程序可以将通知发送到应用程序必须使用系统注册的用户和应用程序由于通知是用户中断，必须显式请求之前将它们发送权限。

有三个不同级别的用户可以批准的应用的通知请求：

- 将显示横幅。
- 声音警报。
- 徽章的应用程序图标。

此外，必须请求这些批准级别并将其设置为本地和远程通知。

只要该应用将启动通过添加下面的代码不应请求通知权限`FinishedLaunching`方法`AppDelegate`和设置所需的通知类型 (`UNAuthorizationOptions`):

```csharp
using UserNotifications;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    return true;
}
```

此外，用户始终可以更改应用程序在任何时间使用的通知特权**设置**设备上的应用。 应用程序应提供通知使用下面的代码之前检查用户的请求的通知权限：

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
}); 
``` 

### <a name="configuring-the-remote-notifications-environment"></a>配置远程通知环境

新到 iOS 10，开发人员必须告知的操作系统以开发或生产运行哪些环境推送通知。 如果不能提供此信息可能会导致应用程序提交到与通知类似于以下 iTune 应用商店时被拒绝：

> 缺少推送通知授权-您的应用程序包括一个 API 用于 Apple 的推送通知服务，但`aps-environment`授权是缺少从应用程序的签名。

若要提供的所需的授权，请执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 双击`Entitlements.plist`文件中**解决方案 Pad**以将其打开以进行编辑。
2. 切换到**源**视图： 

    [![](enhanced-user-notifications-images/setup01.png "源视图")](enhanced-user-notifications-images/setup01.png#lightbox)
3. 单击**+** 按钮以添加新键。
4. 输入`aps-environment`为**属性**，保留**类型**作为`String`并输入任一`development`或`production`为**值**: 

    [![](enhanced-user-notifications-images/setup02.png "Ap 环境属性")](enhanced-user-notifications-images/setup02.png#lightbox)
5. 保存对文件所做的更改。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 双击`Entitlements.plist`文件中**解决方案资源管理器**以将其打开以进行编辑。
3. 单击**+** 按钮以添加新键。
4. 输入`aps-environment`为**属性**，保留**类型**作为`String`并输入任一`development`或`production`为**值**: 

    [![](enhanced-user-notifications-images/setup02w.png "Ap 环境属性")](enhanced-user-notifications-images/setup02.png#lightbox)
5. 保存对文件所做的更改。

-----

### <a name="registering-for-remote-notifications"></a>注册远程通知

如果将发送和接收远程通知应用程序，它仍需要执行_令牌注册_使用现有`UIApplication`API。 此注册要求设备能够进行实时网络连接访问 APNs，将生成将发送到应用的必要令牌。 应用程序需要然后转发给开发人员的服务器端应用程序以注册远程通知此令牌：

[![](enhanced-user-notifications-images/token01.png "令牌注册概述")](enhanced-user-notifications-images/token01.png#lightbox)

下面的代码用于初始化所需要的注册：

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

获取发送到开发人员的服务器端应用程序的令牌将需要的通知负载该 get 的一部分发送从服务器到 APNs 发送远程通知时要包含：

[![](enhanced-user-notifications-images/token02.png "包含通知负载的令牌")](enhanced-user-notifications-images/token02.png#lightbox)

令牌充当通知和用于打开或响应通知应用程序将联系在一起的密钥。

有关详细信息，请参阅 Apple 的[本地和远程通知编程指南](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)文档。

## <a name="notification-delivery"></a>通知传递

向应用程序完全注册和从请求所需的权限授予用户，通过应用现已准备好发送和接收通知。 

### <a name="providing-notification-content"></a>提供通知内容

新到 iOS 10，所有通知同时都包含**标题**和**副标题**，将始终显示与**正文**的通知内容。 新增功能还包括，能够添加**媒体附件**通知内容。

若要创建的本地通知内容，请使用下面的代码：

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

远程通知进行类似的过程：

```csharp
{
    "aps":{
        "alert":{
            "title":"Notification Title",
            "subtitle":"Notification Subtitle",
            "body":"This is the message body of the notification."
        },
        "badge":1
    }
}
```

### <a name="scheduling-when-a-notification-is-sent"></a>计划时通知发送

使用创建通知的内容，应用程序需要计划时将通过设置向用户显示通知*触发器*。 iOS 10 提供了四种不同的触发器类型：

- **推送通知**-以独占方式用于远程通知，当 APNs 通知发送到设备上运行的应用打包时触发。
- **时间间隔**-允许本地通知得以按计划从时间间隔开始的现在和结束将来某个时间点。 例如，`var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`
- **日历日期**-允许本地通知用于安排特定日期和时间。
- **位置基于**-允许本地通知，以进入或离开某个特定地理位置或受到任何蓝牙信号的攻击给定接近 iOS 设备时计划。

准备本地通知时，应用程序需要调用`Add`方法`UNUserNotificationCenter`对象来计划其显示给用户。 针对远程通知服务器端应用程序将发送通知负载给 APNs，后者随后发送到用户的设备数据包。

将所有资源片段集合在一起，本地通知的示例可能如下所示：

```csharp
using UserNotifications;
...

var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);

var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

## <a name="handling-foreground-app-notifications"></a>处理前景应用程序通知

新到 iOS 10，应用程序可以通知以不同方式处理在前台，并且在触发通知。 通过提供`UNUserNotificationCenterDelegate`和实现`UserNotificationCenter`方法，该应用程序可以接管负责显示通知。 例如：

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        #region Constructors
        public UserNotificationCenterDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void WillPresentNotification (UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
        {
            // Do something with the notification
            Console.WriteLine ("Active Notification: {0}", notification);

            // Tell system to display the notification anyway or use
            // `None` to say we have handled the display locally.
            completionHandler (UNNotificationPresentationOptions.Alert);
        }
        #endregion
    }
}
```

此代码只需编写出的内容`UNNotification`到应用程序输出和询问系统以显示标准警报通知。 

如果应用想要在前台时, 显示通知本身并不使用系统默认值，则传递`None`到完成处理程序。 示例:

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

此代码中的位置，打开`AppDelegate.cs`以进行编辑文件并将更改`FinishedLaunching`方法看起来类似于以下：

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    // Watch for notifications while the app is active
    UNUserNotificationCenter.Current.Delegate = new UserNotificationCenterDelegate ();

    return true;
}
```

此代码附加自定义`UNUserNotificationCenterDelegate`从上面为当前`UNUserNotificationCenter`使处于活动状态时，应用程序可以处理通知和在前台。

## <a name="notification-management"></a>通知管理

新到 iOS 10，通知管理挂起并传送通知提供访问权限，并添加了要删除、 更新或升级这些通知的功能。

通知管理的一个重要部分是_请求标识符_时它已创建并将计划与系统分配给通知。 为远程通知，此分配通过新`apps-collapse-id`HTTP 请求标头字段。

请求标识符用于选择希望对执行通知管理应用程序的通知。

### <a name="removing-notifications"></a>删除通知

若要从系统中删除挂起的通知，请使用下面的代码：

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

若要删除的已发送的通知，请使用下面的代码：

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>更新现有通知

若要更新现有通知，只需创建和修改 （如新的触发器时间） 所需参数的新通知，并将其添加到具有相同的请求标识符作为需要修改通知系统。 示例:


```csharp
using UserNotifications;
...

// Rebuild notification
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

// New trigger time
var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger (10, false);

// ID of Notification to be updated
var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

// Add to system to modify existing Notification
UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

已传递通知，将获取更新并提升为列表上的家庭和锁定屏幕和通知中心的顶部，如果已读取用户现有的通知。

## <a name="working-with-notification-actions"></a>使用通知操作

在 iOS 10 中，交付给用户的通知不是静态的并提供了用户进行交互的多种它们 （从内置到自定义操作）。

有三种类型的 iOS 应用程序可以响应的操作：

- **默认操作**-这是当用户点击通知来打开应用程序并显示给定通知的详细信息。
- **自定义操作**-这些被添加在 iOS 8 并提供让用户能够直接从通知执行自定义任务，而无需启动应用程序的快速方法。 它们可以显示为带可自定义标题按钮的列表或一个文本输入的字段可以运行在 （其中应用程序指定少量的时间才能完成请求） 的背景或前景中 （其中启动应用程序是在到 fu 前台lfill 请求)。 自定义操作是适用于 iOS 和 watchOS。
- **关闭操作**-当用户关闭给定的通知时，此操作发送到应用。

### <a name="creating-custom-actions"></a>创建自定义操作

创建并向系统注册自定义操作，请使用下面的代码：

```csharp
// Create action
var actionID = "reply";
var title = "Reply";
var action = UNNotificationAction.FromIdentifier (actionID, title, UNNotificationActionOptions.None);

// Create category
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);
    
// Register category
var categories = new UNNotificationCategory [] { category };
UNUserNotificationCenter.Current.SetNotificationCategories (new NSSet<UNNotificationCategory>(categories)); 
```

在创建新`UNNotificationAction`，分配唯一 ID 和将显示在按钮的标题。 默认情况下，将创建操作作为后台操作，但可以提供选项，以便调整此动作的行为 （例如将其设置为前台执行操作）。

每个创建的操作需要与类别关联。 在创建新`UNNotificationCategory`，它分配的唯一 ID，它可以执行的操作的列表，以提供有关意图的类别中的操作的详细信息和某些选项来控制类别的行为的意向 Id 的列表。

最后，系统使用注册的所有类别`SetNotificationCategories`方法。

### <a name="presenting-custom-actions"></a>提供自定义操作

创建并使用系统注册后的一组自定义操作和类别，可以从本地或远程通知向他们显示。

对于远程通知设置`category`上面创建的类别之一匹配的远程通知负载中。 例如：

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

对于本地通知设置`CategoryIdentifier`属性`UNMutableNotificationContent`对象。 例如：

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

同样，此 ID 必须与上面已创建类别之一匹配。

### <a name="handling-dismiss-actions"></a>处理取消操作

如上面所述，关闭操作在用户关闭通知可以发送到应用。 由于这不是标准的操作，将需要时将创建类别设置选项。 例如：

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>处理操作响应

当用户交互时使用的自定义操作和上面创建的类别时，应用程序需要以完成请求的任务。 这可通过提供`UNUserNotificationCenterDelegate`和实现`UserNotificationCenter`方法。 例如：

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        ...

        #region Override Methods
        public override void DidReceiveNotificationResponse (UNUserNotificationCenter center, UNNotificationResponse response, Action completionHandler)
        {
            // Take action based on Action ID
            switch (response.ActionIdentifier) {
            case "reply":
                // Do something
                break;
            default:
                // Take action based on identifier
                if (response.IsDefaultAction) {
                    // Handle default action...
                } else if (response.IsDismissAction) {
                    // Handle dismiss action
                }
                break;
            }

            // Inform caller it has been handled
            completionHandler();
        }
        #endregion
    }
}
```

传入中`UNNotificationResponse`类具有`ActionIdentifier`属性可以是默认的操作或取消操作。 使用`response.Notification.Request.Identifier`若要测试的任何自定义操作。

`UserText`属性保留的任何用户的文本输入的值。 `Notification`属性中存储了包括该触发器的请求的原始通知和通知内容。 如果它是本地或远程通知基于触发器的类型，则可以决定应用程序。

## <a name="working-with-service-extensions"></a>使用服务扩展

使用远程通知时_服务扩展_提供一种方法启用端到端加密内通知负载。 服务扩展是通知的用户界面运行的扩展 （适用于 iOS 10） 的补充或取代可见内容之前它显示给用户的主要目的与后台中。 

[![](enhanced-user-notifications-images/extension01.png "服务扩展概述")](enhanced-user-notifications-images/extension01.png#lightbox)

服务扩展应快速运行，并仅可以由系统执行的时间长。 事件中服务扩展无法在分配的时间内完成其任务，将调用的回退方法。 如果回退失败，原始通知内容将显示给用户。

服务扩展的一些潜在用途包括：

- 提供远程通知内容的端到端的加密。
- 将附件添加到远程通知来丰富它们。

### <a name="implementing-a-service-extension"></a>实现一个服务扩展

若要在 Xamarin.iOS 应用程序中实现一个服务扩展，请执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 为 mac。 在 Visual Studio 中打开应用程序的解决方案
2. 右键单击解决方案名称中**解决方案 Pad**和选择**添加** > **添加新项目**。
3. 选择**iOS** > **扩展** > **通知服务扩展**单击**下一步**按钮： 

    [![](enhanced-user-notifications-images/extension02.png "选择通知服务扩展")](enhanced-user-notifications-images/extension02.png#lightbox)
4. 输入**名称**扩展和单击**下一步**按钮： 

    [![](enhanced-user-notifications-images/extension03.png "输入扩展的名称")](enhanced-user-notifications-images/extension03.png#lightbox)
5. 调整**项目名称**和/或**解决方案名称**如果必需的且单击**创建**按钮： 

    [![](enhanced-user-notifications-images/extension04.png "调整项目名称和/或解决方案名称")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 Visual Studio 中打开应用程序的解决方案。
2. 右键单击解决方案名称中**解决方案资源管理器**和选择**添加 > 新建项目...**.
3. 选择**Visual C# > iOS 扩展 > 通知服务扩展**:

    [![](enhanced-user-notifications-images/extension01.w157-sml.png "选择通知服务扩展")](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. 输入**名称**扩展和单击**确定**按钮。

-----

> [!IMPORTANT]
> 服务扩展的捆绑标识符应与使用主应用程序的捆绑标识符匹配`.appnameserviceextension`追加到末尾。 例如，如果主应用程序具有的捆绑标识符`com.xamarin.monkeynotify`，服务扩展应具有的捆绑标识符`com.xamarin.monkeynotify.monkeynotifyserviceextension`。 这应会自动设置时将扩展添加到解决方案。 

在需要进行修改，以提供所需的功能的通知服务扩展中没有一个主类。 例如：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyChatServiceExtension
{
    [Register ("NotificationService")]
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Computed Properties
        public Action<UNNotificationContent> ContentHandler { get; set; }
        public UNMutableNotificationContent BestAttemptContent { get; set; }
        #endregion

        #region Constructors
        protected NotificationService (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            ContentHandler = contentHandler;
            BestAttemptContent = (UNMutableNotificationContent)request.Content.MutableCopy ();

            // Modify the notification content here...
            BestAttemptContent.Title = $"{BestAttemptContent.Title}[modified]";

            ContentHandler (BestAttemptContent);
        }

        public override void TimeWillExpire ()
        {
            // Called just before the extension will be terminated by the system.
            // Use this as an opportunity to deliver your "best attempt" at modified content, otherwise the original push payload will be used.

            ContentHandler (BestAttemptContent);
        }
        #endregion
    }
}
```

第一种方法， `DidReceiveNotificationRequest`，将通知标识符以及通知内容通过传递`request`对象。 传入中`contentHandler`需要调用以向用户显示通知。

第二种方法， `TimeWillExpire`，即将用完服务扩展，用于处理该请求的时间之前将调用。 如果服务扩展无法调用`contentHandler`在分配时间内，将向用户显示的原始内容。

### <a name="triggering-a-service-extension"></a>触发一个服务扩展

扩展名为服务创建和传递与该应用程序，可以通过修改远程发送到设备的通知负载触发它。 例如：

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

新`mutable-content`密钥指定服务扩展，将需要启动更新远程通知内容。 `encrypted-content`密钥保存服务扩展可以显示给用户之前解密的加密的数据。

看一看下面的示例服务扩展：

```csharp
using UserNotification;

namespace myApp {
    public class NotificationService : UNNotificationServiceExtension {
    
        public override void DidReceiveNotificationRequest(UNNotificationRequest request, contentHandler) {
            // Decrypt payload
            var decryptedBody = Decrypt(Request.Content.UserInfo["encrypted-content"]);
            
            // Modify Notification body
            var newContent = new UNMutableNotificationContent();
            newContent.Body = decryptedBody;
            
            // Present to user
            contentHandler(newContent);
        }
        
        public override void TimeWillExpire() {
            // Handle out-of-time fallback event
            ...
        }
        
    }
}
```

此代码解密从加密的内容`encrypted-content`密钥，创建一个新`UNMutableNotificationContent`，设置`Body`属性解密的内容并使用`contentHandler`以向用户显示通知。

## <a name="summary"></a>总结

本文已覆盖所有用户通知已通过 iOS 10 得到了增强的方式。 它提供新的用户通知框架以及如何在 Xamarin.iOS 应用程序或应用扩展使用它。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [UserNotifications Framework 参考](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本地和远程通知编程指南](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
