---
title: 演练-在 Xamarin.Android 中使用本地通知
description: 本演练演示如何在 Xamarin.Android 应用程序中使用本地通知。 它演示了创建和发布本地通知的基础知识。 当用户单击通知区域中的通知时，它会启动第二个活动。
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/30/2018
ms.openlocfilehash: a2ca3755e3201263584447ba47ec36d2096386da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766577"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>演练-在 Xamarin.Android 中使用本地通知

_本演练演示如何在 Xamarin.Android 应用程序中使用本地通知。它演示了创建和发布本地通知的基础知识。当用户单击通知区域中的通知时，它会启动第二个活动。_


## <a name="overview"></a>概述

在本演练中，我们将创建的 Android 应用程序引发一条通知，当用户单击的按钮在活动中。 当用户单击通知时，它将启动显示的用户必须单击中的第一个活动的按钮的次数的第二个活动。

以下屏幕快照说明了此应用程序的一些示例：

[![与通知的示例屏幕快照](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)



## <a name="walkthrough"></a>演练

若要开始，让我们来创建新的 Android 项目使用**Android 应用**模板。 让我们调用此项目**LocalNotifications**。 (如果你不熟悉创建 Xamarin.Android 项目，请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)。)


### <a name="add-the-androidsupportv4app-component"></a>添加 Android.Support.V4.App 组件

在本演练中，我们将使用`NotificationCompat.Builder`生成我们本地通知。 中所述[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)，我们必须包括[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet 项目中的使用`NotificationCompat.Builder`。

接下来，让我们编辑**MainActivity.cs**并添加以下`using`语句以便中的类型`Android.Support.V4.App`可供我们的代码：

```csharp
using Android.Support.V4.App;
```

此外，我们必须使其成为清楚地了解我们将使用的编译器`Android.Support.V4.App`版本`TaskStackBuilder`而不是`Android.App`版本。 添加以下`using`语句以解决任何多义性：

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```


### <a name="define-the-notification-id"></a>定义通知 ID

我们将需要我们通知的唯一 ID。 让我们编辑**MainActivity.cs**并添加以下静态实例变量`MainActivity`类：

```csharp
private static readonly int ButtonClickNotificationId = 1000;
```


### <a name="add-code-to-generate-the-notification"></a>添加代码以生成通知

接下来，我们需要创建一个新的事件处理程序，该按钮`Click`事件。 添加以下方法`MainActivity`:

```csharp
private void ButtonOnClick (object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    Bundle valuesForActivity = new Bundle();
    valuesForActivity.PutInt ("count", count);

    // When the user clicks the notification, SecondActivity will start up.
    Intent resultIntent = new Intent(this, typeof (SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras (valuesForActivity);

    // Construct a back stack for cross-task navigation:
    TaskStackBuilder stackBuilder = TaskStackBuilder.Create (this);
    stackBuilder.AddParentStack (Java.Lang.Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent (resultIntent);

    // Create the PendingIntent with the back stack:            
    PendingIntent resultPendingIntent =
        stackBuilder.GetPendingIntent (0, (int)PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    NotificationCompat.Builder builder = new NotificationCompat.Builder (this)
        .SetAutoCancel (true)                    // Dismiss from the notif. area when clicked
        .SetContentIntent (resultPendingIntent)  // Start 2nd activity when the intent is clicked.
        .SetContentTitle ("Button Clicked")      // Set its title
        .SetNumber (count)                       // Display the count in the Content Info
        .SetSmallIcon(Resource.Drawable.ic_stat_button_click)  // Display this icon
        .SetContentText (String.Format(
            "The button has been clicked {0} times.", count)); // The message to display.

    // Finally, publish the notification:
    NotificationManager notificationManager =
        (NotificationManager)GetSystemService(Context.NotificationService);
    notificationManager.Notify(ButtonClickNotificationId, builder.Build());

    // Increment the button press count:
    count++;
}
```

在`OnCreate`方法，将该日历指定`ButtonOnClick`方法`Click`按钮 （替换模板提供的委托事件处理） 的事件：

```csharp
button.Click += ButtonOnClick;
```


### <a name="create-a-second-activity"></a>创建第二个活动

现在，我们需要创建 Android 将显示当用户单击我们通知的另一个活动。 将另一个 Android 活动添加到你的项目称为**SecondActivity**。 打开**SecondActivity.cs**并将其内容替换此代码：

```csharp
using System;
using Android.App;
using Android.OS;
using Android.Widget;

namespace LocalNotifications
{
    [Activity(Label = "Second Activity")]
    public class SecondActivity : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Get the count value passed to us from MainActivity:
            int count = Intent.Extras.GetInt ("count", -1);

            // No count was passed? Then just return.
            if (count <= 0)
                return;

            // Display the count sent from the first activity:
            SetContentView (Resource.Layout.Second);
            TextView textView = FindViewById<TextView>(Resource.Id.textView);
            textView.Text = String.Format (
                "You clicked the button {0} times in the previous activity.", count);
        }
    }
}
```

我们还必须创建的资源布局**SecondActivity**。 添加新**Android 布局**文件到你的项目调用**Second.axml**。 编辑**Second.axml**然后粘贴以下的布局代码：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text=""
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/textView" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>添加通知图标

最后，让我们添加小图标将出现在通知区域中，启动我们通知时。 你可以复制[此图标](local-notifications-walkthrough-images/ic-stat-button-click.png)到你的项目或创建你自己的自定义图标。 我们将名称的图标文件**ic\_stat\_按钮\_click.png**和将其复制到**可资源/绘制**文件夹。 请记住使用**添加 > 现有项...** 要包含在你的项目中的此图标文件。


### <a name="run-the-application"></a>运行应用程序

让我们生成并运行应用程序。 你应该会看到与第一个活动，类似于以下屏幕截图：

[![第一个活动屏幕快照](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

当你单击按钮时，您应该注意到在通知区域中显示的小图标通知：

[![将显示通知图标](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

如果你向下轻扫，并公开通知抽屉，你应该会看到通知：

[![通知消息](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

在单击通知时，它应该会消失，并且应该启动我们其他活动&ndash;查找类似于下面的屏幕快照：

[![第二个活动屏幕快照](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

祝贺你！ 此时已完成 Android 本地通知演练，您必须可以引用的工作示例。 有很多通知的详细信息比我们已经演示了在这里，因此如果你想详细信息，看一看[通知 Google 文档](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)和 Android[通知](http://developer.android.com/design/patterns/notifications.html)设计指南。



## <a name="summary"></a>总结

在本演练中，我们使用`NotificationCompat.Builder`创建和显示通知。 我们已了解如何启动作为一种方法对通知，与用户交互作出响应的第二个活动的基本示例，我们从第一个活动中演示的数据传输到第二个活动。


## <a name="related-links"></a>相关链接

- [LocalNotifications （示例）](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [通知的设计指南模式](http://developer.android.com/design/patterns/notifications.html)
- [通知](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
