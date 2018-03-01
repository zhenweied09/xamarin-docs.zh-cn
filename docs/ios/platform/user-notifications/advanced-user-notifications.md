---
title: "高级的用户通知"
description: "本文将新的用户通知框架深入了解一下以及如何充分利用它在 Xamarin.iOS 应用程序中。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4e1ff652-28f0-4566-b383-9d12664401a4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 6408f3b45f93413fa814e410f07e7b71179b7338
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="advanced-user-notifications"></a>高级的用户通知

_本文将新的用户通知框架深入了解一下以及如何充分利用它在 Xamarin.iOS 应用程序中。_

新 iOS 10，框架允许在传递和本地和远程通知的处理其用户通知。 使用此框架，应用程序或应用扩展可以计划传递本地通知通过指定一组条件，例如位置或当天的时间。

## <a name="about-user-notifications"></a>有关用户通知

新的用户通知框架允许传递和本地和远程通知的处理。 使用此框架，应用程序或应用扩展可以计划传递本地通知通过指定一组条件，例如位置或当天的时间。

此外，应用程序或扩展可以接收 （和可能修改） 本地和远程通知在传递到用户的 iOS 设备。

该新用户通知 UI 框架允许的应用程序或应用扩展，它们会呈现给用户时，自定义的本地和远程通知的外观。

此框架提供程序应用程序可以将通知传递到用户的以下方面：

- **Visual 警报**-其中通知将向下滚动从为横幅屏幕的顶部。
- **声音和振动**-可以与通知相关联。
- **应用程序图标徽章**-其中显示应用程序的图标的旁边显示新内容是否可用的标记。 如未读电子邮件消息数。

此外，具体取决于用户的当前上下文中，有不同的方式，将显示一条通知：

- 如果设备已解锁，通知将向下滚动从屏幕顶部为横幅。
- 如果设备处于锁定状态，将用户的锁定屏幕上显示通知。
- 如果用户已丢失通知，他们可以打开通知中心，并查看任何可用，等待通知。

Xamarin.iOS 应用程序具有两种类型的用户能够发送的通知：

- **本地通知**-这些发送的用户设备上本地安装的应用。
- **远程通知**-从远程服务器，并向用户显示发送或触发后台更新的应用程序的内容。

有关详细信息，请参阅我们[增强用户通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)文档。

## <a name="the-new-user-notification-interface"></a>新的用户通知接口

在 iOS 10 中的用户通知将出现新的用户界面设计为在顶部的设备或通知中心横幅在锁定屏幕上，提供更多的内容，如标题、 副标题和可以显示的可选媒体附件。

无论其中用户通知将显示在 iOS 10，它显示相同的外观和感觉与使用相同的特性和功能。

在 iOS 8，Apple 引入了可操作的通知开发人员无法将自定义操作附加到一条通知，允许用户在通知上执行操作，而无需启动应用程序。 在 iOS 9，Apple 增强可操作的通知，使用快速回复这样用户就可以响应包含文本输入的通知。

由于用户通知 iOS 10 中的用户体验的更组成部分，Apple 已进一步扩展可操作的通知，以支持 3D Touch，其中用户按下在通知上，自定义用户界面是显示效果以提供丰富的交互与通知。

自定义用户通知 UI 显示时，如果用户与连接到通知任何操作交互时，自定义 UI 可以立即更新，以提供有关已更改内容的反馈。

新用户通知 UI API 到 iOS 10，允许 Xamarin.iOS 应用程序可以轻松地使利用这些新的用户通知 UI 功能。

## <a name="adding-media-attachments"></a>添加媒体附件

更常见获取的用户之间共享的项目之一是 photos，因此 iOS 10 添加可以附加媒体项 （例如照片） 直接向一个通知，它将在其中提交并可随时提供的其余部分的通知的碳精笔用户和nt。

但是，由于参与发送大小甚至的小图像，将其连接到远程的通知负载会变得不切实际。 若要处理这种情况下，开发人员可以使用新服务中的扩展 iOS 10 从另一个源 （如 CloudKit 数据存储库） 下载映像并将其连接到通知的内容之前向用户显示。

为要由服务扩展修改远程通知，及其负载必须标记可变。 例如:

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

看一看以下概述的过程：

[ ![](advanced-user-notifications-images/extension02.png "添加媒体附件进程")](advanced-user-notifications-images/extension02.png)

一旦远程通知将发送到设备 （仅通过 APNs) 中，服务扩展可以然后下载所需的映像，通过所需的任何方式 (如`NSURLSession`) 和它收到映像后，它可以修改的通知和显示的内容它给用户。

下面是在代码中对此过程可能处理的示例：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyNotification
{
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Constructors
        public NotificationService ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            // Get file URL
            var attachementPath = request.Content.UserInfo.ObjectForKey (new NSString ("my-attachment"));
            var url = new NSUrl (attachementPath.ToString ());

            // Download the file
            var localURL = new NSUrl ("PathToLocalCopy");

            // Create attachment
            var attachmentID = "image";
            var options = new UNNotificationAttachmentOptions ();
            NSError err;
            var attachment = UNNotificationAttachment.FromIdentifier (attachmentID, localURL, options , out err);

            // Modify contents
            var content = request.Content.MutableCopy() as UNMutableNotificationContent;
            content.Attachments = new UNNotificationAttachment [] { attachment };

            // Display notification
            contentHandler (content);
        }

        public override void TimeWillExpire ()
        {
            // Handle service timing out
        }
        #endregion
    }
}
```

从 APNs 收到通知时，映像的自定义地址读取内容，并从服务器下载文件。 则`UNNotificationAttachement`创建具有唯一的 ID 和映像的本地位置 (作为`NSUrl`)。 创建的通知内容可变副本并添加媒体附件。 通过调用向用户显示通知最后， `contentHandler`。

一旦附件已添加到通知，系统将接管移动和管理的文件。

除了上面介绍远程通知，媒体附件也支持从本地通知，其中`UNNotificationAttachement`创建并附加到其内容以及通知。

在 iOS 10 中的通知支持的映像的媒体附件 (静态和 Gif)，音频或视频和系统将自动显示正确的自定义 UI 为每个这些类型的附件时向用户显示通知。

> [!NOTE]
> **注意：**应格外小心地优化这两个的媒体大小和运行应用程序的服务时，它将采用可从远程服务器下载媒体 （或者本地通知装配媒体），并作为系统的时间有一定严格限制到这两扩展。 例如，考虑发送按比例缩小版本的映像或视频的通知中显示的小剪辑。

## <a name="creating-custom-user-interfaces"></a>创建自定义用户界面

若要为其用户通知创建自定义用户界面，开发人员需要将通知内容扩展 （新到 iOS 10） 添加到应用程序的解决方案。

通知内容扩展允许开发人员可以将其自己的视图添加到通知 UI 绘制出他们希望的任何内容。 但是，交互式 UI 小组件 （如按钮或文本字段） 是**不**由通知 UI 支持并应永远不会添加到自定义的 UI 设计。

若要支持与用户通知的用户交互，自定义操作应会创建、 使用系统注册和之前使用系统计划附加到通知。 将调用通知内容扩展，以处理这些操作的处理。 请参阅[使用通知操作](~/ios/platform/user-notifications/enhanced-user-notifications.md)部分[增强用户通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)有关自定义操作的详细信息的文档。

当包含自定义用户界面的用户通知呈现给用户时，它将具有以下元素：

[ ![](advanced-user-notifications-images/customui01.png "用户通知与自定义 UI 元素")](advanced-user-notifications-images/customui01.png)

如果用户交互 （如通知所示） 了自定义操作时，可以更新用户界面，以便为它们调用某个给定的操作时，发生了什么情况的用户反馈。

### <a name="adding-a-notification-content-extension"></a>添加通知内容扩展

若要在 Xamarin.iOS 应用程序中实现自定义用户通知用户界面，执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 为 mac。 在 Visual Studio 中打开应用程序的解决方案
2. 右键单击解决方案名称中**解决方案 Pad**和选择**添加** > **添加新项目**。
3. 选择**iOS** > **扩展** > **通知内容扩展**单击**下一步**按钮： 

    [ ![](advanced-user-notifications-images/notify01.png "选择通知内容扩展")](advanced-user-notifications-images/notify01.png)
4. 输入**名称**扩展和单击**下一步**按钮： 

    [ ![](advanced-user-notifications-images/notify02.png "输入扩展的名称")](advanced-user-notifications-images/notify02.png)
5. 调整**项目名称**和/或**解决方案名称**如果必需的且单击**创建**按钮： 

    [ ![](advanced-user-notifications-images/notify03.png "调整项目名称和/或解决方案名称")](advanced-user-notifications-images/notify03.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 为 mac。 在 Visual Studio 中打开应用程序的解决方案
2. 右键单击解决方案名称中**解决方案资源管理器**和选择**添加** > **添加新项目**。
3. 选择**iOS** > **扩展** > **通知内容扩展**: 

    [ ![](advanced-user-notifications-images/notify01w.png "选择通知内容扩展")](advanced-user-notifications-images/notify01w.png)
4. 输入**名称**扩展和单击**确定**按钮。

-----

在通知内容扩展添加到解决方案，将在扩展的项目中创建三个文件：

1. `NotificationViewController.cs` -这是通知内容扩展的主视图控制器。
2. `MainInterface.storyboard` -其中开发人员以可见的 UI 布局 iOS 设计器中的通知内容扩展。
3. `Info.plist` -控制通知内容扩展的配置。

默认值`NotificationViewController.cs`文件如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

        }
        #endregion
    }
}
```

`DidReceiveNotification`通知，以便通知内容扩展可以使用自定义 UI 的内容来填充用户扩展时调用方法`UNNotification`。 对于上述示例中，标签已添加到视图中，向具有名称的代码公开`label`，用于显示通知的正文。

### <a name="setting-the-notification-content-extensions-categories"></a>设置通知内容扩展的类别

系统需要以了解有关如何查找应用程序的通知内容扩展基于响应的特定类别。 请执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 双击扩展的`Info.plist`文件中**解决方案 Pad**以将其打开以进行编辑。
2. 切换到**源**视图。
3. 展开`NSExtension`密钥。
4. 添加`UNNotificationExtensionCategory`作为类型键**字符串**与扩展所属类别的值 (在此示例中事件邀请): 

    [ ![](advanced-user-notifications-images/customui02.png "添加 UNNotificationExtensionCategory 密钥")](advanced-user-notifications-images/customui02.png)
5. 保存更改。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 双击扩展的`Info.plist`文件中**解决方案资源管理器**以将其打开以进行编辑。
3. 展开`NSExtension`密钥。
4. 添加`UNNotificationExtensionCategory`作为类型键**字符串**与扩展所属类别的值 (在此示例中事件邀请): 

    [ ![](advanced-user-notifications-images/customui02w.png "添加 UNNotificationExtensionCategory 密钥")](advanced-user-notifications-images/customui02w.png)
5. 保存更改。

-----

通知内容扩展类别 (`UNNotificationExtensionCategory`) 使用使用用于注册通知操作的类别值相同。 在其中应用将使用同一 UI 的多个类别的情况下，切换`UNNotificationExtensionCategory`为类型**数组**并提供所有所需的类别。 例如:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![](advanced-user-notifications-images/customui03.png "通知内容扩展类别")](advanced-user-notifications-images/customui03.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![](advanced-user-notifications-images/customui03w.png "通知内容扩展类别")](advanced-user-notifications-images/customui03w.png)

-----

### <a name="hiding-the-default-notification-content"></a>隐藏默认通知内容

在其中自定义通知用户界面将会显示相同的内容为默认值 （标题、 副标题和底部的通知 UI，会自动显示的正文） 的通知的情况下，可以通过添加隐藏此默认信息`UNNotificationExtensionDefaultContentHidden`键，以`NSExtensionAttributes`作为类型键**布尔**值为`YES`中扩展的`Info.plist`文件：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![](advanced-user-notifications-images/customui04.png "查找默认信息")](advanced-user-notifications-images/customui04.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![](advanced-user-notifications-images/customui04w.png "查找默认信息")](advanced-user-notifications-images/customui04w.png)

-----

### <a name="designing-the-custom-ui"></a>设计自定义用户界面

若要设计通知内容扩展的自定义用户界面，双击`MainInterface.storyboard`文件以打开进行编辑设计器中，iOS 中拖动中生成所需的接口所需的元素 (如`UILabels`和`UIImageViews`)。

> [!NOTE]
> **注意：**通知 UI 却_不_通知内容扩展中支持交互式控件如文本字段或按钮。 尽管它们都可以添加到情节提要，用户将不能与它们进行交互。 若要添加到自定义通知 UI 的用户交互，请改为使用自定义操作。

一旦 UI 布局并向 C# 代码公开所需的控件，打开`NotificationViewController.cs`以进行编辑和修改`DidReceiveNotification`方法来填充 UI，当用户展开通知。 例如:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

        }
        #endregion
    }
}
```

### <a name="setting-the-content-area-size"></a>设置内容区域的大小

若要调整向用户显示的内容区域的大小，请设置下面的代码`PreferredContentSize`中的属性`ViewDidLoad`到所需大小的方法。 此大小也进行调整，通过将约束应用于 iOS 设计器中的视图，它从左到开发人员可以选取最适合的方法。

因为在调用内容扩展，通知内容区域之前已运行系统通知将启动完整大小，而进行动画处理到时向用户显示请求的大小。

若要消除此效果，编辑`Info.plist`扩展并设置文件`UNNotificationExtensionInitialContentSizeRatio`键`NSExtensionAttributes`键以键入**数**与表示所需的比率的值。 例如:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![](advanced-user-notifications-images/customui05.png "UNNotificationExtensionInitialContentSizeRatio 密钥")](advanced-user-notifications-images/customui05.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![](advanced-user-notifications-images/customui05w.png "UNNotificationExtensionInitialContentSizeRatio 密钥")](advanced-user-notifications-images/customui05w.png)

-----

### <a name="using-media-attachments-in-custom-ui"></a>在自定义 UI 中使用媒体附件

因为媒体附件 (中所示[添加媒体附件](#Adding-Media-Attachments)上面部分) 是通知负载的一部分时，它们可以访问，就像它们是在默认显示在通知内容扩展通知 UI。

例如，如果上面的自定义用户界面包含`UIImageView`，已公开对 C# 代码，则无法使用下面的代码使用媒体附件从其进行填充：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }
        #endregion
    }
}
```

由于媒体附件由系统，它是外部应用程序的沙盒。 需要通知系统它通过调用想对文件的访问扩展`StartAccessingSecurityScopedResource`方法。 当扩展完成与文件后时，它需要调用`StopAccessingSecurityScopedResource`释放其连接。

### <a name="adding-custom-actions-to-a-custom-ui"></a>将自定义操作添加到自定义用户界面

如上面所述，通知 UI 将不支持用户交互，因此 UI 设计中不支持控件如文本字段或按钮。

相反，标准的自定义操作机制用于向自定义通知 UI 添加交互性。 请参阅[使用通知操作](~/ios/platform/user-notifications/enhanced-user-notifications.md)部分[增强用户通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)有关自定义操作的详细信息的文档。

自定义操作，除了通知内容扩展可以响应下列内置操作以及：

- **默认操作**-这是当用户点击通知来打开应用程序并显示给定通知的详细信息。
- **关闭操作**-当用户关闭给定的通知时，此操作发送到应用。

通知内容扩展还可以在用户调用了一个自定义操作时更新它们的 UI，例如显示为已接受时的日期用户点击**接受**自定义操作按钮。 此外，通知内容扩展可告诉系统延迟的通知 UI 开除，以便关闭通知之前，用户可以看到这些文件的操作的效果。

这可通过实现的第二个版本`DidReceiveNotification`包括完成处理程序的方法。 例如:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;
using CoreGraphics;

namespace myApp {
    public class NotificationViewController : UIViewController, UNNotificationContentExtension {

        public override void ViewDidLoad() {
            base.ViewDidLoad();

            // Adjust the size of the content area
            var size = View.Bounds.Size
            PreferredContentSize = new CGSize(size.Width, size.Width/2);
        }

        public void DidReceiveNotification(UNNotification notification) {

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = Content.UserInfo["location"] as string;
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments[0];
                if (attachment.Url.StartAccessingSecurityScopedResource()) {
                    EventImage.Image = UIImage.FromFile(attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch(response.ActionIdentifier){
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
    }
}
```

通过添加`Server.PostEventResponse`处理程序`DidReceiveNotification`通知内容扩展，该扩展方法*必须*处理所有自定义操作。 扩展还可以通过更改转发到包含应用程序的自定义操作`UNNotificationContentExtensionResponseOption`。 例如:

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>使用自定义 UI 中的文本输入操作

根据应用程序和通知的设计，可能要求用户输入 （如答复消息） 通知文本的次数。 通知内容扩展有权访问的内置文本输入操作，只需像标准通知一样。

例如:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Computed Properties
        // Allow to take input
        public override bool CanBecomeFirstResponder {
            get { return true; }
        }

        // Return the custom created text input view with the
        // required buttons and return here
        public override UIView InputAccessoryView {
            get { return InputView; }
        }
        #endregion

        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Private Methods
        private UNNotificationCategory MakeExtensionCategory ()
        {

            // Create Accept Action
            ...

            // Create decline Action
            ...

            // Create Text Input Action
            var commentID = "comment";
            var commentTitle = "Comment";
            var textInputButtonTitle = "Send";
            var textInputPlaceholder = "Enter comment here...";
            var commentAction = UNTextInputNotificationAction.FromIdentifier (commentID, commentTitle, UNNotificationActionOptions.None, textInputButtonTitle, textInputPlaceholder);

            // Create category
            var categoryID = "event-invite";
            var actions = new UNNotificationAction [] { acceptAction, declineAction, commentAction };
            var intentIDs = new string [] { };
            var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);

            // Return new category
            return category;

        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Is text input?
            if (response is UNTextInputNotificationResponse) {
                var textResponse = response as UNTextInputNotificationResponse;
                Server.Send (textResponse.UserText, () => {
                    // Close Notification
                    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
                });
            }

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch (response.ActionIdentifier) {
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
        #endregion
    }
}
```

此代码将创建新的文本输入的操作并将其添加到扩展的类别 (在`MakeExtensionCategory`) 方法。 在`DidReceive`重写方法，它将处理用户输入文本替换为以下代码：

```csharp
// Is text input?
if (response is UNTextInputNotificationResponse) {
    var textResponse = response as UNTextInputNotificationResponse;
    Server.Send (textResponse.UserText, () => {
        // Close Notification
        completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
    });
}
```

如果设计调用文本输入字段中添加自定义按钮，添加以下代码以将它们包括：

```csharp
// Allow to take input
public override bool CanBecomeFirstResponder {
    get {return true;}
}

// Return the custom created text input view with the
// required buttons and return here
public override UIView InputAccessoryView {
    get {return InputView;}
}
```

当用户触发注释操作时，视图控制器和自定义文本输入的字段需要激活：

```csharp
// Update UI when the user interacts with the
// Notification
Server.PostEventResponse += (response) {
    // Take action based on the response
    switch(response.ActionIdentifier){
    ...
    case "comment":
        BecomeFirstResponder();
        TextField.BecomeFirstResponder();
        break;
    }

    // Close Notification
    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);

};
```

<a name="Summary" />

## <a name="summary"></a>摘要

本文已在 Xamarin.iOS 应用程序中使用新的用户通知框架高级的看。 它涵盖媒体将附件添加到本地和远程通知，则覆盖使用新的用户通知 UI 创建自定义通知 Ui。


## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [UserNotifications Framework 参考](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本地和远程通知编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
