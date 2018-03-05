---
title: "本地通知"
description: "本部分演示如何在 Xamarin.Android 中实现本地通知。 本文档说明 Android 通知的各种 UI 元素，并介绍 API 的涉及创建和显示一条通知。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 7566ebac0f487ef321c512c988c79f34e50777ac
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="local-notifications"></a>本地通知

_本部分演示如何在 Xamarin.Android 中实现本地通知。本文档说明 Android 通知的各种 UI 元素，并介绍 API 的涉及创建和显示一条通知。_

## <a name="local-notifications-overview"></a>本地通知概述

本主题说明如何在 Xamarin.Android 应用程序中实现本地通知。 它讨论 Android 通知的各个部分，它还说明了应用程序开发人员可用的不同通知样式和它介绍了一些用于创建和发布通知的 Api。

Android 可提供用于向用户显示通知图标和通知信息的两个系统控制的区域。 在首次发布通知，图标将其显示在*通知区域*，下面的屏幕快照中所示：

![示例在设备上的通知区域](local-notifications-images/01-notification-shade.png)

若要获取有关通知的详细信息，用户可以打开的通知抽屉 （用于扩展每个通知图标可显示通知内容） 和执行与通知关联的任何操作。 下面的屏幕快照显示*通知抽屉*对应到上面显示的通知区域：

[![示例显示三个通知的通知抽屉](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png)

Android 通知使用两种类型的布局：

-   ***基本布局*** &ndash; compact、 固定的显示格式。

-   ***展开的布局***&ndash;可以扩展到更大的大小以显示详细信息的显示格式。

下列各节介绍了上述每种布局类型 （以及如何创建它们）。

<a name="base-layout" />

### <a name="base-layout"></a>基本布局

所有 Android 通知都基于基本布局格式，其中至少包括以下元素：

1.  A*通知图标*，如果应用程序支持不同类型的通知表示原始应用程序或通知类型。

2.  通知*标题*，或如果通知是一条个人消息的发件人的名称。

3.  通知消息。

4.  A*时间戳*。

这些元素将显示下图中所示：

[![通知元素的位置](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png)

基本布局仅限于 64 密度无关的像素 (dp) 高度。 默认情况下，android 创建此基本通知样式。

（可选） 通知可以显示大图标表示应用程序或发件人的照片。 当通知 Android 5.0 及更高版本中使用大图标时，小的通知图标通过大图标显示为徽章：

![简单通知照片](local-notifications-images/04-simple-notification-photo.png)

从 Android 5.0 开始，通知也可以出现在锁屏上：

[![示例锁屏通知](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png)

用户可以双击锁屏通知对设备解锁和跳转到的应用程序产生该通知，或轻扫以取消通知。 应用程序可以设置的通知，以便控制上锁屏，显示的内容的可见性级别和用户可以选择是否允许在锁屏通知中显示的敏感内容。

Android 5.0 引入了称为高优先级通知演示文稿格式*提醒*。 提醒通知几秒钟滑从屏幕顶部，然后备份到通知区域的撤回：

[![示例 heads-up 通知](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png)

提醒通知使系统 UI，以将呈现给用户的重要信息放而不会中断当前正在运行的活动的状态。

Android 包括对通知元数据支持，以便可以进行排序和智能地显示通知。 通知元数据还控制在锁屏上和提醒格式呈现方式通知。 应用程序可以设置以下类型的通知元数据：

-   **优先级**&ndash;的优先级别确定如何以及何时显示通知。 例如，在 Android 5.0 中，优先级较高的通知将显示为提醒通知。

-   **可见性**&ndash;指定多少通知内容是在锁屏上显示通知时要显示。

-   **类别**&ndash;通知系统如何处理在各种情况下，例如设备时通知*不打扰*模式。

**注意：** **可见性**和**类别**Android 5.0 和早期版本的 Android 中不提供有中引入。 从 Android 8.0 开始[通知通道](#notif-chan)用于控制如何将通知呈现给用户。

<a name="expanded-layouts" />

### <a name="expanded-layouts"></a>展开的布局

从 Android 4.1 开始，可以使用允许用户以展开要查看更多的内容的通知的高度扩展的布局样式配置通知。 例如，下面的示例阐释了在合同模式下的展开的布局通知：

![签约的通知](local-notifications-images/07-contracted-notification.png)

展开此通知时，它将显示整个消息：

![展开的通知](local-notifications-images/08-expanded-notification.png)

Android 支持单事件通知的三个扩展的布局样式：

-   ***大文本***&ndash;在合同模式下，将显示两个句点后, 跟的消息的第一行的摘录。 在展开模式下，显示整个消息 （如在上面的示例所示）。

-   ***收件箱***&ndash;在合同模式下，将显示新的消息数。 在展开模式中显示的第一个电子邮件消息或收件箱中的消息列表。

-   ***映像***&ndash;在合同模式下，仅显示消息文本。 在展开模式中显示文本和图像。

[基本通知超出](#beyond-the-basic-notification)（在本文后面介绍） 的说明如何创建*大文本*，*收件箱*，和*映像*通知。

<a name="notification-creation" />

## <a name="notification-creation"></a>通知创建

若要在 Android 中创建一条通知，你可以使用[Notification.Builder](https://developer.xamarin.com/api/type/Android.App.Notification+Builder/)类。 `Notification.Builder` 若要简化通知对象的创建的 Android 3.0 中引入了。 若要创建与较旧版本的 Android 兼容的通知，你可以使用[NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)类而不是`Notification.Builder`(请参阅[兼容性](#compatibility)本主题中更高版本有关使用详细信息`NotificationCompat.Builder`)。
`Notification.Builder` 提供了一个通知，如设置各种选项的方法：

-   内容，包括标题、 邮件正文和通知图标。

-   样式的通知，如*大文本*，*收件箱*，或*映像*样式。

-   通知的优先级： 最小值、 低，默认情况下，高，或最大。

-   在锁屏通知的可见性： 公共、 私有、 或机密。

-   类别的元数据，可帮助 Android 进行分类和筛选通知。

-   为可选打算，该值指示用于启动时点击通知的活动。

在生成器中设置这些选项后，你将生成通知对象包含的设置。 若要发布通知，则此通知将对象传递到*通知管理器*。 Android 提供[NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)类，该类负责发布通知并向用户显示它们。 可以从任何上下文中，如活动或服务获取与此类的引用。

<a name="how-to-generate" />

### <a name="how-to-generate-a-notification"></a>如何生成通知

若要在 Android 中生成一条通知，请按照下列步骤：

1.  实例化`Notification.Builder`对象。

2.  调用上的各种方法`Notification.Builder`要设置通知选项对象。

3.  调用[生成](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/)方法`Notification.Builder`对象实例化通知对象。

4.  调用[通知](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification))要发布通知的通知管理器的方法。

你必须至少提供每个通知的以下信息：

-   小图标 (24 x 24 dp 大小)

-   短标题

-   通知的文本

下面的代码示例演示如何使用`Notification.Builder`以生成基本的通知。 请注意，`Notification.Builder`方法支持[方法链接](http://en.wikipedia.org/wiki/Method_chaining); 也就是说，每个方法返回生成器对象，因此你可以使用的最后一次的方法调用的结果来调用下一步的方法调用：

```csharp
// Instantiate the builder and set notification elements:
Notification.Builder builder = new Notification.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

在此示例中，新`Notification.Builder`调用对象`builder`是实例化的标题和文本通知的设置，并从加载的通知图标**Resources/drawable/ic_notification.png**。 对通知生成器调用`Build`方法使用这些设置创建通知对象。 下一步是调用`Notify`通知管理器的方法。 若要找到的通知管理器，请调用`GetSystemService`，如上所示。

`Notify`方法接受两个参数： 通知标识符和通知对象。 通知标识符是一个唯一的整数，它标识你的应用程序通知。 在此示例中，通知标识符设置为零 (0);但是，在生产应用程序，你将想要为每个通知指定一个唯一标识符。 重复使用以前的标识符值对的调用中`Notify`将使最后一个通知被覆盖。

当在 Android 5.0 设备上运行此代码时，它将生成一个通知，如以下示例所示：

![有关示例代码通知结果](local-notifications-images/09-hello-world.png)

通知图标会显示在通知的左侧&ndash;此映像的带圆圈&ldquo;我&rdquo;了 alpha 通道，因此，Android 可以绘制背后循环灰色背景。 你也可以提供一个图标，而无需 alpha 通道。 若要将照片图像显示为图标，请参阅[大图标格式](#large-icon-format)本主题中更高版本。

时间戳设置自动，但您可以通过调用来覆盖此设置[集](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/)通知生成器方法。 例如，下面的代码示例将时间戳设置为当前时间：

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```
<a name="sound-and-vibr" />

### <a name="enabling-sound-and-vibration"></a>启用声音和振动

如果希望你通知还播放声音，则可以调用通知生成器[SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/)方法并传入`NotificationDefaults.Sound`标志：

```csharp
// Instantiate the notification builder and enable sound:
Notification.Builder builder = new Notification.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

此调用`SetDefaults`将使该设备发布通知时播放声音。 如果你想要振动，而不是播放声音的设备，你可以传递`NotificationDefaults.Vibrate`到`SetDefaults.`如果你想要播放声音和振动设备的设备，你可以传递到这两个标志`SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

如果启用声音而不指定播放的声音，Android 将使用默认系统通知声音。 但是，可以更改都将通过调用通知生成器播放的声音[SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/)方法。 例如，若要播放警报声音与你的通知 （而不是默认的通知声音），你可以获取的 URI 警报中的声音[RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/)和将其传递到`SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

或者，你可以使用系统默认铃声声音实现你的通知：

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

创建通知对象后，就可以在通知对象上设置通知属性 (而不是将它们配置为通过提前`Notification.Builder`方法)。 例如，而不是调用`SetDefaults`方法，以使振动上一个通知，你可以直接修改通知的位标志[默认](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/)属性：

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

此示例会导致设备能振动发布通知时。

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>更新通知

如果你想要更新的通知内容，已发布后，可以重复使用现有`Notification.Builder`对象创建新的通知对象和发布与标识符的最后一个通知此通知。 例如:

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

在此示例中，现有`Notification.Builder`对象用于创建新的通知对象具有不同的标题和消息。
新的通知对象已发布使用的标识符的前一次的通知，并且此操作将更新以前发布的通知的内容：

![更新的通知](local-notifications-images/12-updated-notification.png)

重复使用以前的通知的正文&ndash;仅标题和通知更改时通知显示通知抽屉中的文本。 标题文本从"示例通知"更改为"更新通知"，并从"Hello World 更改消息文本 ！ 这是我的第一个通知"！ 为"已更改为此邮件"。

通知保持可见，直到发生下列三项操作之一：

-   在用户关闭通知 (或点击*全部清除*)。

-   应用程序将调用`NotificationManager.Cancel`，并传入通知已发布时分配的唯一通知 ID。

-   应用程序调用`NotificationManager.CancelAll`。

有关详细信息更新 Android 通知，请参阅[修改通知](http://developer.android.com/training/notify-user/managing.html#Updating)。

<a name="starting-an-activity" />

### <a name="starting-an-activity-from-a-notification"></a>从通知开始活动

在 Android 中，很常见的通知，以便与关联的*操作*&ndash;当用户点击通知时将启动的活动。 此活动可以位于另一个应用程序中或甚至另一个任务中。 若要将操作添加到通知，你可以创建[PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)对象，并将关联`PendingIntent`与通知。 A`PendingIntent`是允许收件人应用程序使用的发送应用程序的权限运行预定义的一段代码的意图的特殊类型。 当用户点击通知时，Android 启动指定的活动`PendingIntent`。

下面的代码段演示如何创建一个通知，通知`PendingIntent`这将启动的原始应用程序中，活动`MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
Notification.Builder builder = new Notification.Builder(this)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

此代码将非常类似于上一节，只不过中的通知代码`PendingIntent`已添加到通知对象。 在此示例中，`PendingIntent`是与原始应用程序的活动相关联，传递到通知生成器前[SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/)方法。 `PendingIntentFlags.OneShot`标志会传递给`PendingIntent.GetActivity`方法，以便`PendingIntent`使用仅一次。 此代码运行时，将显示以下通知：

![第一次操作通知](local-notifications-images/10-first-action-notification.png)

点击此通知将用户定向回到原始活动。

在生产应用中，你的应用程序必须处理*后退堆栈*当用户按**回**通知活动中的按钮 （如果尚不熟悉 Android 任务和后退堆栈，请参阅[任务和后退堆栈](http://developer.android.com/guide/components/tasks-and-back-stack.html))。
在大多数情况下，向后导航出通知活动应返回用户从应用程序并返回到主屏幕。 若要管理后的堆栈中，你的应用程序使用[TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/)类，以创建`PendingIntent`具有后的堆栈。

另一个实际注意事项是原始活动可能需要将数据发送到通知活动。 例如，通知可能指示已到达文本消息，并通知活动 （查看屏幕任何消息），需要以向用户显示消息的消息的 ID。 创建活动`PendingIntent`可以使用[Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/)方法将数据 （例如，字符串） 添加到其目的，以便此数据传递到通知活动。

下面的代码示例演示如何使用`TaskStackBuilder`来管理后的堆栈中，并且它包括举例说明如何将单个消息字符串发送到名为通知活动`SecondActivity`:

```csharp
// Setup an intent for SecondActivity:
Intent secondIntent = new Intent (this, typeof(SecondActivity));

// Pass some information to SecondActivity:
secondIntent.PutExtra ("message", "Greetings from MainActivity!");

// Create a task stack builder to manage the back stack:
TaskStackBuilder stackBuilder = TaskStackBuilder.Create(this);

// Add all parents of SecondActivity to the stack:
stackBuilder.AddParentStack (Java.Lang.Class.FromType (typeof (SecondActivity)));

// Push the intent that starts SecondActivity onto the stack:
stackBuilder.AddNextIntent (secondIntent);

// Obtain the PendingIntent for launching the task constructed by
// stackbuilder. The pending intent can be used only once (one shot):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    stackBuilder.GetPendingIntent (pendingIntentId, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including
// the pending intent:
Notification.Builder builder = new Notification.Builder (this)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my second action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

在此代码示例中，应用程序包含两个活动： `MainActivity` （其中包含上述通知代码），和`SecondActivity`，则用户将看到点击通知后的屏幕。 当运行此代码时，显示简单通知 （类似于前面的示例）。 用户会看到在通知上的点击`SecondActivity`屏幕：

![第二个活动屏幕快照](local-notifications-images/11-second-activity.png)

字符串消息 (传入的意图`PutExtra`方法) 中检索`SecondActivity`通过此代码行：

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

中会显示此检索到的消息，"Greetings 从 MainActivity ！，"`SecondActivity`屏幕，上面的屏幕截图中所示。 当用户按**回**时中的按钮`SecondActivity`，从应用程序并返回到前面的应用程序启动屏幕将引导导航。

有关创建挂起意向的详细信息，请参阅[PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)。


<a name="notif-chan" />

## <a name="notification-channels"></a>通知通道

从 Android 8.0 (Oreo) 开始，你可以使用*通知通道*功能来创建用于每种类型的你想要显示的通知的用户可自定义通道。 通知通道使你组通知，以便所有通知都发布到通道展相同的行为。 例如，你可能必须适用于需要立即关注的通知的通知通道和单独的"更安静"通道用于信息性消息。

**YouTube**随 Android Oreo 一起安装的应用列出了两个通知类别：**下载通知**和**常规通知**:

[![在 Android Oreo YouTube 的通知屏幕](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png)

每个类别对应于通知通道。 YouTube 应用实现**下载通知**通道和**常规通知**通道。 用户可以点击**下载通知**，应用程序的下载通知通道，它会显示设置屏幕：

[![下载 YouTube 应用的通知屏幕](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png)

在此屏幕中，用户可以修改的行为**下载**通知通道通过执行以下操作：

-   设置的重要性级别**紧急**，**高**，**中等**，或**低**，该配置声音和 visual 中断的级别。

-   打开或关闭通知点。

-   打开或关闭指示灯闪烁。

-   显示或隐藏在锁定屏幕上的通知。

-   重写**不打扰**设置。

**常规通知**通道具有相似的设置：

[![YouTube 应用的常规通知屏幕](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png)

请注意没有通知通道如何与用户交互的绝对控制&ndash;用户可以修改任何设备上的通知通道的设置，在上面的屏幕截图所示。 但是，您可以配置默认值 （如下所述将是）。 如这些示例所示，新的通知通道功能使你能够向用户授予对不同类型的通知细化的控制。

你应将对通知通道的支持添加到你的应用程序？ 如果你面向 Android 8.0，你的应用程序*必须*实现通知通道。
针对尝试向用户发送本地通知，而不使用通知通道的 Oreo 应用将无法在 Oreo 设备上显示通知。 如果你不面向 Android 8.0，则你的应用程序如它也会出现运行 Android 7.1 或更低版本时仍将运行 Android 8.0 上但具有相同的通知行为。

<a name="notif-chan-create" />

### <a name="creating-a-notification-channel"></a>创建一个通知通道

若要创建通知通道，请执行以下操作：

1. 构造[NotificationChannel](https://developer.android.com/reference/android/app/NotificationChannel.html)替换为以下对象：

    - 在你的包中是唯一的 ID 字符串。 在下面的示例中，字符串`com.xamarin.myapp.urgent`使用。

    - 通道 （小于 40 个字符） 的用户可见名称。 在下面的示例中，名称**紧急**使用。

    - 控制如何中断的通知通道的重要性发布到`NotificationChannel`。 重要性可以是`Default`， `High`， `Low`， `Max`， `Min`， `None`，或`Unspecified`。

    向其传递这些值[构造函数](https://developer.android.com/reference/android/app/NotificationChannel.html#NotificationChannel%28java.lang.String,%20java.lang.CharSequence,%20int%29)(在此示例中，`Resource.String.noti_chan_urgent`设置为**紧急**):

    ```csharp
    public const string URGENT_CHANNEL = "com.xamarin.myapp.urgent";
    . . .
    string chanName = GetString (Resource.String.noti_chan_urgent);
    var importance = NotificationImportance.High;
    NotificationChannel chan =
       new NotificationChannel (URGENT_CHANNEL, chanName, importance);
    ```

2.  配置`NotificationChannel`使用初始设置的对象。
    例如，下面的代码配置`NotificationChannel`对象，以便通知发布到此通道将振动设备和默认显示在锁定屏幕上：

    ```csharp
    chan.EnableVibration (true);
    chan.LockscreenVisibility = NotificationVisibility.Public;
    ```

3.  提交到通知管理器的通知通道对象：

    ```csharp
    NotificationManager notificationManager =
        (NotificationManager) GetSystemService (NotificationService);
    notificationManager.CreateNotificationChannel (chan);
    ```

<a name="notif-chan-post" />

### <a name="posting-to-a-notifications-channel"></a>发布到通知通道

要发布到通知通道的通知，执行以下操作：

1.  配置通知使用`Notification.Builder`，并将通道 ID 与传入`SetChannelId`方法。 例如:

    ```csharp
    Notification.Builder builder = new Notification.Builder (this)
        .SetContentTitle ("Attention!")
        .SetContentText ("This is an urgent notification message!")
        .SetChannelId (URGENT_CHANNEL);
    ```

2.  生成并发出通知使用通知管理器的[通知](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/p/System.Int32/Android.App.Notification/)方法：

    ```csharp
    const int notificationId = 0;
    notificationManager.Notify (notificationId, builder.Build());
    ```

你可以重复上述步骤以创建另一种通知渠道信息性消息。 此第二个通道无法，默认情况下，禁用震动，设置为默认锁定屏幕可见性`Private`，并将通知重要性设置为`Default`。

Android Oreo 通知通道在操作中的完整代码示例，请参阅[NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels)示例; 此示例应用程序管理两个通道，并设置其他通知选项。



<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>超出基本的通知

通知默认为、 没有任何装饰基布局格式在 Android 中，但可通过使用更多，提高此基本格式`Notification.Builder`方法调用。 在本部分中，你将了解如何将一个大型的照片图标添加到你的通知，并将看到如何创建扩展的布局通知的示例。

<a name="large-icon-format" />

### <a name="large-icon-format"></a>大图标格式

Android 通知通常显示原始应用程序的图标 （通知左侧）。 但是，可以显示通知，图像或照片 (*大图标*) 而不是标准的小图标。 例如，消息传送应用程序可以显示照片的发件人，而不是应用程序图标。

下面是一个示例基本的 Android 5.0 通知&ndash;它显示仅的小型应用程序图标：

![示例一般的通知](local-notifications-images/13-sample-notification.png)

这是通知后对其进行修改，以显示大图标的屏幕截图&ndash;它使用基于 Xamarin 代码 monkey 的映像创建一个图标：

![示例大图标通知](local-notifications-images/14-large-icon-sample.png)

请注意，如果以大图标格式显示一条通知的小型应用程序图标显示为旁边大图标的右下角的标记。

若要将图像用作大图标在通知中，你可以调用通知生成器[SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/)方法并传入的图像的位图。 与不同`SetSmallIcon`，`SetLargeIcon`只接受位图。 要转换成位图图像文件，则使用[BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/)类。 例如:

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

此代码示例打开在该图像文件**Resources/drawable/monkey_icon.png**、 将其转换为位图，并将传递到生成的位图`Notification.Builder`。 通常情况下，源图像分辨率大于的小图标&ndash;但不是大得多。 太大的映像可能会导致不必要的大小调整操作可能会延迟通知的发布。
有关详细信息中 Android 的通知图标大小，请参阅[通知图标](http://developer.android.com/design/style/iconography.html#notification)。

<a name="big-text-style" />

### <a name="big-text-style"></a>大文本样式

*大文本*样式是用于在通知中显示长消息扩展的布局模板。 如所有扩展的布局通知，compact 演示文稿格式是最初显示大文本通知：

![示例大文本通知](local-notifications-images/15-big-text-notification.png)

在这种格式，仅消息的摘录显示，由两个句点终止。 当用户向下拖动的通知时，它会展开以显示整个通知消息：

![展开大文本通知](local-notifications-images/16-big-text-expanded.png)

此扩展的布局格式还包括底部的通知的摘要文本。 最大高度*大文本*通知为 256 dp。

若要创建*大文本*通知，您实例化`Notification.Builder`对象，和前面一样，然后实例化并添加[BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/)对象传递给`Notification.Builder`对象。 例如:

```csharp
// Instantiate the Big Text style:
Notification.BigTextStyle textStyle = new Notification.BigTextStyle();

// Fill it with text:
string longTextMessage = "I went up one pair of stairs.";
longTextMessage += " / Just like me. ";
//...
textStyle.BigText (longTextMessage);

// Set the summary text:
textStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (textStyle);

// Create the notification and publish it ...
```

在此示例中，消息文本和摘要文本存储在`BigTextStyle`对象 (`textStyle`) 传递给之前 `Notification.Builder.`

<a name="image-style" />

### <a name="image-style"></a>图像样式

*映像*样式 (也称为*大局*样式) 是一种扩展的通知格式，可用于通知的正文中显示图像。 例如，屏幕快照应用或照片应用可以使用*映像*通知样式，为用户提供的最后一个通知映像的捕获。 请注意的最大高度*映像*通知为 256 dp &ndash; Android 将调整大小以适应此最大高度限制，可用内存限制范围内的映像。

就像所有展开布局通知*映像*通知首先显示在一种紧凑显示随附的消息文本的摘录：

![Compact 映像通知将显示任何图像](local-notifications-images/17-image-compact.png)

当用户向下拖动*映像*通知，它就会展开以显示图像。 例如，下面是通知的前一次的扩展的版本：

![展开的图像通知显示图像](local-notifications-images/18-image-expanded.png)

请注意，当以紧凑显示通知时，它将显示通知文本 (将传递给通知生成器的文本`SetContentText`方法，如前面所示)。 但是，通知将展开以显示图像，它会显示在图像上方的摘要文本。

若要创建*映像*通知，您实例化`Notification.Builder`对象和前面一样，然后创建并插入[BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/)对象插入`Notification.Builder`对象。 例如:

```csharp
// Instantiate the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Convert the image to a bitmap before passing it into the style:
picStyle.BigPicture (BitmapFactory.DecodeResource (Resources, Resource.Drawable.x_bldg));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create the notification and publish it ...
```

如`SetLargeIcon`方法`Notification.Builder`、[覆盖](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/)方法`BigPictureStyle`需要你想要通知的正文中显示的图像的位图。 在此示例中， [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32))方法`BitmapFactory`读取图像文件位于**Resources/drawable/x_bldg.png**并将其转换成位图。

此外可以作为资源显示不一起打包的映像。 例如，下面的代码示例从本地的 SD 卡加载图像并将其显示在*映像*通知：

```csharp
// Using the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Read an image from the SD card, subsample to half size:
BitmapFactory.Options options = new BitmapFactory.Options();
options.InSampleSize = 2;
string imagePath = "/sdcard/Pictures/my-tshirt.jpg";
picStyle.BigPicture (BitmapFactory.DecodeFile (imagePath, options));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("Check out my new T-Shirt!");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create notification and publish it ...
```

在此示例中，图像文件位于**/sdcard/Pictures/my-tshirt.jpg**进行加载，将大小调整回其原始大小的一半，然后转换为在通知中使用的位图：

![通知中的示例 t 恤图像](local-notifications-images/19-tshirt-notification.png)

如果不事先知道图像文件的大小，它是一个好办法包装对调用[BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/)异常处理程序中&ndash;`OutOfMemoryError`如果图像太大，可能会引发异常若要调整大小的 android。

加载和大型位图图像解码的详细信息，请参阅[负载大型位图高效地](https://developer.xamarin.com/recipes/android/resources/general/load_large_bitmaps_efficiently)。

<a name="inbox-style" />

### <a name="inbox-style"></a>收件箱样式

*收件箱*格式是用于通知的正文中显示的文本 （例如电子邮件收件箱摘要） 的单独的行的扩展的布局模板。 *收件箱*格式通知第一次显示一种紧凑中：

![示例 compact 收件箱通知](local-notifications-images/20-inbox-compact.png)

当用户向下拖动的通知时，它会展开以显示下面的屏幕截图中所示的电子邮件摘要：

![展开示例收件箱通知](local-notifications-images/21-inbox-expanded.png)

若要创建*收件箱*通知，您实例化`Notification.Builder`对象，和前面一样，并将添加[InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/)对象传递给`Notification.Builder`。 例如:

```csharp
// Instantiate the Inbox style:
Notification.InboxStyle inboxStyle = new Notification.InboxStyle();

// Set the title and text of the notification:
builder.SetContentTitle ("5 new messages");
builder.SetContentText ("chimchim@xamarin.com");

// Generate a message summary for the body of the notification:
inboxStyle.AddLine ("Cheeta: Bananas on sale");
inboxStyle.AddLine ("George: Curious about your blog post");
inboxStyle.AddLine ("Nikko: Need a ride to Evolve?");
inboxStyle.SetSummaryText ("+2 more");

// Plug this style into the builder:
builder.SetStyle (inboxStyle);
```

若要将新的文本行添加到通知正文，调用[Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/)方法`InboxStyle`对象 (的最大高度*收件箱*通知为 256 dp)。 请注意，与不同*大文本*样式，*收件箱*样式通知正文中支持独立的文本行。

你还可以使用*收件箱*任何通知，需要在扩展格式中显示的文本的各个行的样式。 例如，*收件箱*可以使用通知样式来合并多个挂起通知到可以更新单个*收件箱*样式具有新的通知行通知内容 (请参阅[更新通知](#updating-a-notification)上面)，而不是生成一个连续的新的、 主要类似通知流。 有关这种方法的详细信息，请参阅[汇总通知](http://developer.android.com/design/patterns/notifications.html#summarize_your_notifications)。

<a name="configuring-metadata" />

## <a name="configuring-metadata"></a>配置元数据

`Notification.Builder` 包含可以调用以设置你的通知，如优先级、 可见性和类别的元数据的方法。 Android 使用此信息&mdash;以及用户首选项设置&mdash;来确定如何以及何时显示通知。

<a name="priority-settings" />

### <a name="priority-settings"></a>优先级设置

发布通知时，通知的优先级设置确定的两个结果：

-   通知出现的位置相对于其他通知。
    例如，高优先级通知上面较低的优先级通知在通知抽屉中，而不考虑时提供每个通知已发布。

-   是否 Heads-up 通知格式 (Android 5.0 及更高版本) 会显示通知。 仅*高*和*最大*优先级通知显示为提醒通知。

Xamarin.Android 定义以下枚举来设置通知优先级：

-   `NotificationPriority.Max` &ndash; 提醒用户的紧急或关键条件 （例如，的传入呼叫、 打开通过打开的说明或紧急警报）。 在 Android 5.0 和更高版本设备上，以提醒格式显示最高优先级别通知。

-   `NotificationPriority.High` &ndash; 通知用户 （如重要的电子邮件或实时聊天消息的到达） 的重要事件。 在 Android 5.0 和更高版本设备上，高优先级通知提醒格式显示。

-   `NotificationPriority.Default` &ndash; 将通知用户具有中等级别的重要性的条件。

-   `NotificationPriority.Low` &ndash; 有关非紧急用户需要会将通知 （例如，软件更新提醒或社交网络更新） 的信息。

-   `NotificationPriority.Min` &ndash; 用户通知时，才的背景信息查看通知 （例如，位置或天气信息）。

若要设置通知的优先级，请调用[SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/)方法`Notification.Builder`对象传递中的优先级别。 例如:

```csharp
builder.SetPriority (NotificationPriority.High);
```

在下面的示例、 高优先级通知、"重要邮件 ！" 将显示在通知抽屉的顶部：

![示例-优先级较高的通知](local-notifications-images/22-hi-priority-drawer.png)

由于这是优先级较高的通知，它还会显示为 Android 5.0 中用户的当前活动屏幕上显示的警告通知：

![示例提醒通知](local-notifications-images/23-heads-up-example.png)

在下一步的示例中，低优先级"一天认为"通知显示在优先级较高的电池电量级别通知：

![示例低优先级通知](local-notifications-images/24-lo-priority-drawer.png)

因为"中的一天的思想"通知是一个低优先级通知，Android 将不显示它在 Heads-up 格式。

<a name="visibility-settings" />

### <a name="visibility-settings"></a>可见性设置

从 Android 5.0 开始*可见性*设置才可用于控制安全锁屏上显示多少通知内容。
Xamarin.Android 定义以下枚举来设置通知可见性：

-   `NotificationVisibility.Public` &ndash; 通知的全部内容显示在安全锁屏。

-   `NotificationVisibility.Private` &ndash; 仅基本信息显示在安全锁屏 （如通知图标和把它发布应用的名称），但通知的详细信息的其余部分隐藏。 所有通知都默认为`NotificationVisibility.Private`。

-   `NotificationVisibility.Secret` &ndash; 安全锁屏，甚至不通知图标上显示任何内容。 只有在用户解锁设备后，通知内容可用。

若要设置的可见性通知，应用调用`SetVisibility`方法`Notification.Builder`对象传递中的可见性设置。 例如，此调用`SetVisibility`使通知`Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

当`Private`发布通知，安全锁屏上显示的名称和应用程序的图标。 而不是通知消息，则用户将看到"解锁您的设备以查看此通知":

![解锁设备通知消息](local-notifications-images/25-lockscreen-private.png)

在此示例中， **NotificationsLab**是源应用程序的名称。 通知此密文的版本时才出现锁屏安全 （即，安全 PIN、 模式或密码通过）&ndash;锁屏不安全，通知的完整内容是否可在锁屏上。

<a name="category-settings" />

### <a name="category-settings"></a>类别设置

从 Android 5.0 开始，预定义的类别均可供排名和筛选通知。 Xamarin.Android 提供以下枚举这些类别：

-   `Notification.CategoryCall` &ndash; 传入的电话呼叫。

-   `Notification.CategoryMessage` &ndash; 传入的文本消息。

-   `Notification.CategoryAlarm` &ndash; 警报条件或计时器过期。

-   `Notification.CategoryEmail` &ndash; 传入的电子邮件消息。

-   `Notification.CategoryEvent` &ndash; 日历事件。

-   `Notification.CategoryPromo` &ndash; 促销消息或播发。

-   `Notification.CategoryProgress` &ndash; 后台操作的进度。

-   `Notification.CategorySocial` &ndash; 社交网络更新。

-   `Notification.CategoryError` &ndash; 后台操作或身份验证进程失败。

-   `Notification.CategoryTransport` &ndash; 媒体播放更新。

-   `Notification.CategorySystem` &ndash; 保留供系统使用 （系统或设备状态）。

-   `Notification.CategoryService` &ndash; 指示后台服务正在运行。

-   `Notification.CategoryRecommendation` &ndash; 与当前正在运行的应用相关建议消息。

-   `Notification.CategoryStatus` &ndash; 有关设备的信息。

当对通知进行排序时，通知的优先级优先于其类别设置。 例如，优先级较高的通知将显示为警告即使它属于`Promo`类别。 若要设置通知的类别，你可以调用`SetCategory`方法`Notification.Builder`对象类别设置中传递。 例如:

```csharp
builder.SetCategory (Notification.CategoryCall);
```

*不打扰*功能 （Android 5.0 中的新） 筛选器基于类别的通知。 例如，*不打扰*屏幕中**设置**电话呼叫消息和消息允许免除将通知用户：

![不打扰屏幕开关](local-notifications-images/26-do-not-disturb.png)

当用户配置*不打扰*若要阻止所有中断除外 （如上面的屏幕截图中所示） 的电话呼叫，Android 允许的类别设置通知`Notification.CategoryCall`设备时显示处于*不打扰*模式。 请注意，`Notification.CategoryAlarm`在永远不会阻止通知*不打扰*模式。


<a name="compatibility" />

## <a name="compatibility"></a>兼容性

如果你要创建应用程序，将还运行在早期版本的 Android （尽早 API 级别 4），你将使用[NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)类而不是`Notification.Builder`。 当你创建通知`NotificationCompat.Builder`，Android 可确保基本通知内容能够正确显示在较旧的设备上。 但是，由于某些高级的通知功能不可用在较旧版本的 Android，你的代码必须显式处理扩展的通知样式、 类别和可见性级别如下所述的兼容性的问题。

若要使用`NotificationCompat.Builder`在你的应用，你必须包括[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)项目中的 NuGet。

下面的代码示例演示如何创建基本通知使用`NotificationCompat.Builder`:

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();
```

如本示例所示，为基本的通知选项的方法调用是相同的`Notification.Builder`。 但是，你的代码必须处理的更复杂的通知 （在下一部分中所述） 的向下兼容性问题。

[LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications)示例演示如何使用`NotificationCompat.Builder`以启动从通知第二个活动。 此代码示例说明了[Xamarin.Android 中使用本地通知](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)演练。

<a name="notification-styles" />

### <a name="notification-styles"></a>通知样式

若要创建*大文本*，*映像*，或*收件箱*样式通知`NotificationCompat.Builder`，你的应用程序必须使用这些样式的兼容性版本。 例如，若要使用*大文本*样式，请实例化`NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

同样，你的应用程序可以使用`NotificationCompat.InboxStyle`和`NotificationCompat.BigPictureStyle`为*收件箱*和*映像*样式，分别。

<a name="priority-and-category" />

### <a name="notification-priority-and-category"></a>通知优先级和类别

`NotificationCompat.Builder` 支持`SetPriority`（从开始提供 Android 4.1） 的方法。 但是，`SetCategory`方法是*不*支持`NotificationCompat.Builder`因为类别是 Android 5.0 中引入了新通知元数据系统的一部分。

若要支持较旧版本的 Android、 where`SetCategory`是不可用，你的代码可以检查在运行时有条件地调用的 API 级别`SetCategory`当 API 级别为等于或大于 Android 5.0 （API 级别 21）：

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

在此示例中，应用程序的**目标框架**设置为 Android 5.0 和**最低 Android 版本**设置为**Android 4.1 (API 级别 16)**。 因为`SetCategory`是 API 级别 21 及更高版本中可用，将调用此代码示例`SetCategory`仅当有可用&ndash;它将不会调用`SetCategory`API 级别时小于
21.

<a name="lockscreen-visibility" />

### <a name="lockscreen-visibility"></a>锁屏可见性

因为 Android 不支持 Android 5.0 （API 级别 21） 之前, 进行锁屏通知`NotificationCompat.Builder`不支持`SetVisibility`方法。 如上文所述的`SetCategory`，你的代码可以检查在运行时调用的 API 级别`SetVisiblity`仅当有可用：

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetVisibility (Notification.Public);
}
```

<a name="summary" />

## <a name="summary"></a>摘要

本文介绍如何在 Android 中创建本地通知。 它所述的通知剖析、 介绍了如何使用`Notification.Builder`创建通知，如何在大图标的样式通知*大文本*，*映像*和*收件箱*格式、 如何设置优先级、 可见性和类别，如元数据设置的通知以及如何启动从通知的活动。 本文还介绍了这些通知设置如何使用新的提醒，锁屏，和*不打扰*Android 5.0 中引入的功能。 最后，您学习了如何使用`NotificationCompat.Builder`为了保持与早期版本的 Android 的通知兼容性。

有关适用于 Android 的设计通知的准则，请参阅[通知](http://developer.android.com/preview/notifications.html)。


## <a name="related-links"></a>相关链接

- [NotificationsLab （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications （示例）](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [在 Android 演练中的本地通知](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [通知](http://developer.android.com/design/patterns/notifications.html)
- [通知用户](http://developer.android.com/training/notify-user/index.html)
- [通知](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
