---
title: "远程通知 Firebase 云消息传送"
description: "本演练提供在 Xamarin.Android 应用程序中如何使用 Firebase Cloud Messaging 实现远程通知 （也称为推送通知） 的分步说明。 它演示如何实现所需的通信使用 Firebase 云消息传送 (FCM)，提供如何配置用于访问 FCM，Android 清单的示例，演示如何使用 Firebase 下游消息传递的各种类控制台。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 7e107fc9ced62027bc793c97a388344d74becd66
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>远程通知 Firebase 云消息传送

_本演练提供在 Xamarin.Android 应用程序中如何使用 Firebase Cloud Messaging 实现远程通知 （也称为推送通知） 的分步说明。它演示如何实现所需的通信使用 Firebase 云消息传送 (FCM)，提供如何配置用于访问 FCM，Android 清单的示例，演示如何使用 Firebase 下游消息传递的各种类控制台。_

## <a name="fcm-notifications-overview"></a>FCM 通知概述

在本演练中，基本应用程序中调用**FCMClient**将创建来阐释 FCM 消息传递的 essentials。 **FCMClient**检查是否存在的 Google Play 服务、 从 FCM 收到注册令牌、 显示从 Firebase 控制台中，发送的远程通知和订阅的主题消息：

[![应用的示例屏幕快照](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

将介绍下列主题领域：

1.  后台通知

2.  主题消息

3.  前景通知

在本演练中，你将以增量方式将功能添加到**FCMClient**和上一个设备或仿真程序以了解它如何与 FCM 交互运行它。 你将使用日志记录，若要了解实时应用事务与 FCM 服务器，并将观察如何从输入到 Firebase 控制台通知 GUI 的 FCM 消息生成的通知。 

## <a name="requirements"></a>惠?

你可以继续执行本演练之前，必须获取所需的凭据以使用 Google 的 FCM 服务器;中介绍了此过程[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。 具体而言，你必须下载**google services.json**要用于此演练的示例代码文件。 如果你尚未创建一个项目在 Firebase 控制台 (如果尚未下载或**google services.json**文件)，请参阅[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。 

若要运行示例应用程序，你将需要 Android 测试设备或仿真程序是与 Firebase 具兼容性。 Firebase Cloud Messaging 支持运行 Android 2.3 （小玩偶） 或更高版本，客户端和这些设备还必须安装的 Google Play 商店应用程序 （Google Play Services 9.2.1 或更高版本）。 如果你还没有安装在你的设备上的 Google Play 商店应用程序，请访问[Google Play](https://support.google.com/googleplay)网站，以下载并安装它。 或者，你可以使用运行 Android 2.3 或更高版本，而不是测试设备 （您无需安装 Google Play 商店，如果你使用 Android SDK 仿真程序） 的 Android SDK 仿真程序。 

## <a name="start-an-app-project"></a>启动应用程序项目

若要开始，创建一个名为的新空 Xamarin.Android 项目**FCMClient**。 如果你不熟悉创建 Xamarin.Android 项目，请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)。 创建新的应用程序后下, 一步是设置包名称，并安装多个将用于与 FCM 进行通信的 NuGet 包。 

### <a name="set-the-package-name"></a>设置包名称

在[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)，指定 FCM 启用应用程序的包名称。 此包名称也可作为*应用程序 ID* API 密钥与该键相关联。 配置应用程序以使用此包名称：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  打开的属性**FCMClient**项目。 

2.  在**Android 清单**页上，设置包名称。 

在下面的示例中，包名称设置为`com.xamarin.fcmexample`: 

[![设置包名称](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

尽管你更新**Android 清单**，另请检查以确保`Internet`启用权限。 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  打开的属性**FCMClient**项目。 

2.  在**Android 应用程序**页上，设置包名称。 

在下面的示例中，包名称设置为`com.xamarin.fcmexample`: 

[![设置包名称](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

尽管你更新**Android 清单**，另请检查以确保`INTERNET`启用权限 (下**所需的权限**)。 

-----

请注意，客户端应用程序将无法从 FCM 接收注册令牌，如果此包名称不*完全*与已输入到 Firebase 控制台的包名称匹配。 

### <a name="add-the-xamarin-google-play-services-base-package"></a>添加 Xamarin Google Play 服务的基本包

因为 Firebase Cloud Messaging 依赖于 Google Play 服务， [Xamarin Google Play 服务的基](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/)NuGet 包必须添加到 Xamarin.Android 项目。 你将需要版本 29.0.0.2 或更高版本。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  在 Visual Studio 中，右键单击**引用 > 管理 NuGet 包...**. 

2.  单击**浏览**选项卡上，搜索**Xamarin.GooglePlayServices.Base**。 

3.  安装到此包**FCMClient**项目： 

    [![安装 Google Play 服务基](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  在适用于 Mac 的 Visual Studio，右键单击**包 > 添加包...**. 

2.  搜索**Xamarin.GooglePlayServices.Base**。 

3.  安装到此包**FCMClient**项目： 

    [![安装 Google Play 服务基](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

如果的 NuGet 的安装过程中遇到错误，关闭**FCMClient**项目，重新打开，然后重试 NuGet 安装。 

当你安装**Xamarin.GooglePlayServices.Base**，还安装了所有必需的依赖项。 编辑**MainActivity.cs**并添加以下`using`语句： 

```csharp
using Android.Gms.Common;
```

此语句使`GoogleApiAvailability`类**Xamarin.GooglePlayServices.Base**供**FCMClient**代码。 
`GoogleApiAvailability` 用于检查的 Google Play 服务存在。 

### <a name="add-the-xamarin-firebase-messaging-package"></a>添加消息传送包 Xamarin Firebase

若要从 FCM，接收消息[Xamarin Firebase-消息传送](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/)NuGet 包必须添加到应用程序项目。 而无需此包，Android 应用程序无法从 FCM 服务器接收消息。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  在 Visual Studio 中，右键单击**引用 > 管理 NuGet 包...**. 

2.  检查**包括预发行版**并搜索**Xamarin.Firebase.Messaging**。 

3.  安装到此包**FCMClient**项目： 

    [![安装 Xamarin Firebase 消息传送](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  在适用于 Mac 的 Visual Studio，右键单击**包 > 添加包...**. 

2.  检查**显示预发行包**并搜索**Xamarin.Firebase.Messaging**。 

3.  安装到此包**FCMClient**项目： 

    [![安装 Xamarin Firebase 消息传送](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----
 
当你安装**Xamarin.Firebase.Messaging**，还安装了所有必需的依赖项。

接下来，编辑**MainActivity.cs**并添加以下`using`语句：

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

前两个语句进行中的类型**Xamarin.Firebase.Messaging** NuGet 包可供**FCMClient**代码。 **Android.Util**添加将用于观察 FMS 具有事务的日志记录功能。 


### <a name="add-the-google-services-json-file"></a>添加 Google 服务 JSON 文件

下一步是添加**google services.json**为你的项目的根目录的文件： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  复制**google services.json**到项目文件夹。

2.  添加**google services.json**向应用程序项目 (单击**显示所有文件**中**解决方案资源管理器**，右键单击**google services.json**，然后选择**包括在项目**)。 

3.  选择**google services.json**中**解决方案资源管理器**窗口。

4.  在**属性**窗格中，设置**生成操作**到**GoogleServicesJson** (如果**GoogleServicesJson**未显示生成操作，保存并关闭解决方案，然后重新打开它）：

    [![将生成操作设置为 GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  复制**google services.json**到项目文件夹。

2.  添加**google services.json**向应用程序项目。 

3.  右键单击**google services.json**。

4.  设置**生成操作**到**GoogleServicesJson**: 

    [![将生成操作设置为 GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)
 
-----
 

当**google services.json**添加到项目 (和**GoogleServicesJson**设置生成操作)，生成过程中提取的客户端 ID 和 API 密钥，然后将这些凭据添加到合并 /生成**AndroidManifest.xml**驻留在**obj/Debug/android/AndroidManifest.xml**。 此合并进程会自动添加的任何权限和其他 FCM 元素所需的到 FCM 服务器的连接。 


## <a name="check-for-google-play-services"></a>Google Play Services 检查

将首先创建应用程序的 UI 的初始布局。 编辑**Resources/layout/Main.axml**并将其内容替换为以下 XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">
    <TextView
        android:text=" "
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/msgText"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:padding="10dp" />
</LinearLayout>
```

这`TextView`将用于显示指明是否已安装 Google Play 服务的消息。 保存对**main.axml**。 编辑**MainActivity.cs**并添加到下面的实例变量声明`MainActivity`类： 

```csharp
TextView msgText;
```

Google 建议，Android 应用的 Google Play 服务 APK 是否存在之前检查访问 Google Play 服务功能 (有关详细信息，请参阅[检查 Google Play services](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play))。 在下面的示例中，`OnCreate`方法将 Google Play 服务之前验证是否可用应用程序将尝试使用 FCM 服务。 添加以下方法`MainActivity`类： 

```csharp
public bool IsPlayServicesAvailable ()
{
    int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable (this);
    if (resultCode != ConnectionResult.Success)
    {
        if (GoogleApiAvailability.Instance.IsUserResolvableError (resultCode))
            msgText.Text = GoogleApiAvailability.Instance.GetErrorString (resultCode);
        else
        {
            msgText.Text = "This device is not supported";
            Finish ();
        }
        return false;
    }
    else
    {
        msgText.Text = "Google Play Services is available.";
        return true;
    }
}
```

此代码检查设备以查看是否已安装 Google Play 服务 APK。 如果未安装，在显示一条消息`TextBox`，指示用户从 Google Play 商店下载 APK （或在设备的系统设置中启用它）。 

将 `OnCreate` 方法替换为以下代码： 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);
    IsPlayServicesAvailable ();
}
```

`IsPlayServicesAvailable` 在的末尾调用`OnCreate`以便 Google Play 服务检查运行每个时间启动应用。 如果你的应用程序`OnResume`方法，则应调用`IsPlayServicesAvailable`从`OnResume`以及。 完全重新生成并运行应用程序。 如果所有已正确配置，你应看到类似于以下屏幕截图的屏幕： 

[![应用指示 Google Play 服务不可用](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

如果没有获得此结果，请验证你的设备上是否安装了 Google Play 服务 APK (有关详细信息，请参阅[设置 Google Play 服务](https://developers.google.com/android/guides/setup))。 还要验证你已添加**Xamarin.Google.Play.Services.Base**包到你**FCMClient**项目如前面所述。


## <a name="add-the-instance-id-receiver"></a>添加实例 ID 接收器

下一步是添加扩展服务`FirebaseInstanceIdService`来处理的创建、 旋转和更新的 Firebase 注册令牌。 (有关注册令牌的详细信息，请参阅[注册到 FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。)`FirebaseInstanceIdService`服务是必要条件 FCM 要能够将消息发送到设备。 当`FirebaseInstanceIdService`服务添加到客户端应用程序，应用程序将自动接收 FCM 消息和将其显示为通知，每当 backgrounded 应用程序。 

### <a name="declare-the-receiver-in-the-android-manifest"></a>声明 Android 清单中的接收方

编辑**AndroidManifest.xml**和插入以下`<receiver>`元素到`<application>`部分： 

```xml
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
    android:exported="false" />
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
    android:exported="true"
    android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
    </intent-filter>
</receiver>
```

此 XML 将执行以下操作：

-   声明`FirebaseInstanceIdReceiver`实现，提供[唯一标识符](https://developers.google.com/instance-id/)为每个应用程序实例。 此接收器还进行身份验证和授权操作。 

-   声明内部`FirebaseInstanceIdInternalReceiver`用于安全地启动服务的实现。

`FirebaseInstanceIdReceiver`是`WakefulBroadcastReceiver`接收`FirebaseInstanceId`和`FirebaseMessaging`事件和传递到类派生自这些`FirebaseInstanceIdService`。 

### <a name="implement-the-firebase-instance-id-service"></a>实现 Firebase 实例 ID 服务

由自定义处理的应用程序注册 FCM 工作`FirebaseInstanceIdService`你提供的服务。 
`FirebaseInstanceIdService` 执行以下步骤： 

1.  使用[实例 ID API](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID)生成授权客户端应用程序访问 FCM 和应用程序服务器的安全令牌。 反过来，应用程序变得重新注册令牌从 FCM。 

2.  如果应用程序服务器需要它，将转发到的应用服务器的注册令牌。

添加新的文件称为**MyFirebaseIIDService.cs**和其模板代码替换为以下代码： 

```csharp
using System;
using Android.App;
using Firebase.Iid;
using Android.Util;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    {
        const string TAG = "MyFirebaseIIDService";
        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }
        void SendRegistrationToServer(string token)
        {
            // Add custom implementation, as needed.
        }
    }
}
```

此服务实现`OnTokenRefresh`注册令牌最初创建或更改时调用的方法。 当`OnTokenRefresh`运行时，它检索的最新令牌`FirebaseInstanceId.Instance.Token`属性 （它以异步方式更新 FCM）。 在此示例中，刷新的令牌将记录，以便可以在输出窗口中查看： 

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` 不经常调用： 它用于更新下列情况下的令牌： 

-   当安装或卸载应用程序。

-   当用户将删除应用数据。 

-   应用程序清除实例 id。

-   当已泄漏的安全令牌。

根据 Google 的[实例 ID](https://developers.google.com/instance-id/guides/android-implementation)文档，FCM 实例 ID 服务将请求应用程序刷新其令牌定期 （通常情况下，每 6 个月）。 

`OnTokenRefresh` 此外会调用`SendRegistrationToAppServer`关联用户的注册令牌与服务器端帐户 （如果有），由维护应用程序： 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

因为此实现取决于应用程序服务器的设计，此示例中提供空的方法体。 如果你的应用程序服务器要求 FCM 注册信息，修改`SendRegistrationToAppServer`若要将用户的 FCM 实例 ID 令牌与由你的应用程序维护任何服务器端帐户相关联。 （请注意该标记是不透明的客户端应用程序。） 

当一个令牌发送到应用程序服务器，`SendRegistrationToAppServer`应维护一个布尔值以指示令牌是否已发送到服务器。 如果此布尔值为 false，`SendRegistrationToAppServer`将令牌发送到应用程序服务器&ndash;否则，该令牌已发送到上一个调用中的应用程序服务器。 在某些情况下 (如这`FCMClient`示例)，应用程序服务器不需要令牌; 因此，此方法不是本示例所需。 

## <a name="implement-client-app-code"></a>实现客户端应用程序代码

现在，接收方服务位于的位置，可以编写客户端应用程序代码，若要利用这些服务。 在以下部分中，一个按钮添加到 UI 以日志注册令牌 (也称为*实例 ID 令牌*)，并更多代码添加到`MainActivity`查看`Intent`当从启动应用程序时的信息通知： 

[![日志令牌按钮添加到应用程序屏幕](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>日志令牌

在此步骤中添加的代码仅用于演示目的&ndash;生产客户端应用程序必须无需登录注册令牌。 编辑**Resources/layout/Main.axml**并添加以下`Button`声明后立即`TextView`元素： 

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

编辑**MainActivity.cs**并添加到以下成员声明`MainActivity`类：

```csharp
const string TAG = "MainActivity";
```

将以下代码添加到末尾`OnCreate`方法： 

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

此代码将当前令牌记录到输出窗口时**日志令牌**点击按钮。 

### <a name="handle-notification-intents"></a>处理通知意向

当用户点击通知从颁发**FCMClient**、 伴随该通知的任何数据消息均可在`Intent`其他功能。 编辑**MainActivity.cs**并将下面的代码添加到顶部`OnCreate`方法 (对的调用之前`IsPlayServicesAvailable`): 

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

应用程序的启动器`Intent`当用户点击其通知消息，因此此代码将记录中的任何随附的数据时触发`Intent`到输出窗口。 如果不同`Intent`必须要激发`click_action`通知消息的字段必须设置为， `Intent` (启动器`Intent`未使用`click_action`指定)。 


## <a name="background-notifications"></a>后台通知

生成并运行**FCMClient**应用。 **日志令牌**显示按钮：

[![显示日志令牌按钮](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

点击**日志令牌**按钮。 IDE 输出窗口中，应显示如下所示的一条消息： 

[![显示在输出窗口中的实例 ID 令牌](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

使用标记一长串**令牌**是实例 ID 令牌，然后将其粘贴到 Firebase 控制台&ndash;选择并将此字符串复制到剪贴板。 如果看不到一个实例 ID 令牌，将以下行添加到顶部`OnCreate`方法来验证是否**google services.json**已正确分析：

```csharp
Log.Debug(TAG, "google app id: " + Resource.String.google_app_id);
```

`google_app_id`记录到输出窗口的值应与匹配`mobilesdk_app_id`值记录在**google services.json**。 

### <a name="send-a-message"></a>发送消息

登录到[Firebase 控制台](https://console.firebase.google.com)，选择你的项目，单击**通知**，然后单击**发送第一条消息**: 

[![发送你的第一条消息按钮](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

上**Compose 消息**页上，输入消息文本并选择**单个设备**。 从 IDE 输出窗口复制的实例 ID 标记并将其粘贴到**FCM 注册令牌**Firebase 控制台字段： 

[![撰写消息对话框](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

在 Android 设备 （或仿真程序），后台应用程序通过点击 Android**概述**按钮和触摸主屏幕。 设备已准备好，单击**发送消息**Firebase 控制台中： 

[![发送消息按钮](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

当**查看消息**显示对话框，请单击**发送**。
通知图标应出现在通知区域中的设备 （或仿真程序）： 

[![显示通知图标](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

打开通知图标以查看该消息。 通知消息应为完全什么已键入**消息正文**Firebase 控制台字段： 

[![在设备上显示通知消息](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

点击通知图标以返回到**FCMClient**应用。 `Intent`发送到的其他功能**FCMClient** IDE 输出窗口中列出： 

[![意向附加程序列出了来自键、 消息 ID 和折叠密钥](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

在此示例中，**从**键设置为应用程序的 Firebase 项目编号 (在此示例中， `41590732`)，和**collapse_key**设置为其包名称 (**com.xamarin.fcmexample**)。 如果你不会收到一条消息，请尝试删除**FCMClient**上的设备 （或仿真程序） 的应用并重复执行上述步骤。 


> [!NOTE]
> 如果强制关闭应用程序，FCM 将停止传递通知。 Android 可阻止后台服务广播无意中或不必要地启动停止的应用程序的组件。 (有关此行为的详细信息，请参阅[启动停止的应用程序上的控件](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)。)出于此原因，它而需要手动卸载该应用每次运行它，并停止调试会话从&ndash;这将强制 FCM，生成新的令牌，以便将继续接收消息。

### <a name="add-a-custom-default-notification-icon"></a>添加自定义默认通知图标

在前面的示例中，通知图标设置为应用程序图标。 下面的 XML 配置通知的自定义默认图标。 Android 显示所有通知消息，其中的通知图标未显式设置此自定义默认图标。 

若要添加一个自定义默认的通知图标，添加到你图标**可资源/绘制**目录中，编辑**AndroidManifest.xml**，并插入以下`<meta-data>`元素插入`<application>`部分： 

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

在此示例中，通知图标驻留在**资源/可绘制/ic\_stat\_ic\_notification.png**将用作自定义默认通知图标。 如果未在中配置自定义默认图标**AndroidManifest.xml**且没有图标设置通知有效负载中时，Android 用作应用程序图标的通知图标 （如上面的通知图标屏幕截图中所示）。 

## <a name="handle-topic-messages"></a>句柄主题消息

到目前为止编写的代码处理注册标记，并将远程通知功能添加到应用程序。 下一个示例将添加代码，可侦听*主题消息*并将其转发到用户为远程通知。 主题的消息是发送到某个特定主题订阅的一个或多个设备的 FCM 消息。 有关主题的消息的详细信息，请参阅[主题消息传送](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。 

### <a name="subscribe-to-a-topic"></a>主题订阅

编辑**Resources/layout/Main.axml**并添加以下`Button`紧跟上一个声明`Button`元素：

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

此 XML 将添加**订阅通知**到布局的按钮。
编辑**MainActivity.cs**并将下面的代码添加到末尾`OnCreate`方法： 

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

此代码查找**订阅通知**布局中的按钮并将其 click 处理程序分配给调用代码`FirebaseMessaging.Instance.SubscribeToTopic`，并在订阅的主题中，传入_新闻_。 当用户点击**订阅**按钮，应用程序订阅_新闻_主题。 在以下部分中，_新闻_主题消息将发送从 Firebase 控制台通知 GUI。 

### <a name="send-a-topic-message"></a>发送主题消息

卸载应用程序，重新生成，并再次运行。 单击**订阅通知**按钮：

[![订阅通知按钮](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

如果成功订阅了应用程序后，你应看到**主题同步成功**在 IDE 中输出窗口： 

[![输出窗口显示主题成功的同步消息](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

使用以下步骤来发送主题消息：

1.  在 Firebase 控制台中，单击**新消息**。 

2.  上**Compose 消息**页上，输入消息文本并选择**主题**。 

3.  在**主题**下拉菜单，选择内置的主题，**新闻**: 

    [![选择新闻主题](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  在 Android 设备 （或仿真程序），后台应用程序通过点击 Android**概述**按钮和触摸主屏幕。 

5.  设备已准备好，单击**发送消息**Firebase 控制台中。 

6.  检查 IDE 输出窗口以查看**/主题/新闻**日志输出中： 

    [![显示从 /topic/news 的消息](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

如果此消息出现在输出窗口，通知图标应也出现在 Android 设备上的通知区域中。 打开通知图标以查看该主题消息： 

[![主题消息显示为一条通知](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

如果你不会收到一条消息，请尝试删除**FCMClient**上的设备 （或仿真程序） 的应用并重复执行上述步骤。 

## <a name="foreground-notifications"></a>前景通知

若要接收 foregrounded 应用中的通知，则必须实现`FirebaseMessagingService`。 此服务，也需要用于接收数据负载和用于发送上游消息。 下面的示例演示如何实现扩展的服务`FirebaseMessagingService`&ndash;生成应用程序将能够在前台运行时处理远程通知。 

### <a name="implement-firebasemessagingservice"></a>实现 FirebaseMessagingService

`FirebaseMessagingService`服务包括`OnMessageReceived`你编写以用于处理传入消息的方法。 请注意，`OnMessageReceived`通知消息调用*仅*在前台应用程序的运行时。 当在后台运行应用程序时，自动生成的通知将显示 （如本演练前面的示例所述）。 

添加新的文件称为**MyFirebaseMessagingService.cs**和其模板代码替换为以下代码： 

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Media;
using Android.Util;
using Firebase.Messaging;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    {
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
        }
    }
}
```

请注意，`MESSAGING_EVENT`必须声明意向的筛选器，以便新 FCM 消息定向到`MyFirebaseMessagingService`: 

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

当客户端应用程序收到消息时从 FCM，`OnMessageReceived`从传入的提取消息内容`RemoteMessage`对象通过调用其`GetNotification`方法。 接下来，它会记录消息内容，以便可以在 IDE 输出窗口中查看： 

```csharp
Log.Debug(TAG, "From: " + message.From);
Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
```

> [!NOTE]
> 如果在中设置断点`FirebaseMessagingService`、 你调试会话可能或不一定会由于 FCM 将消息的传送点击这些断点。
 

### <a name="send-another-message"></a>发送另一条消息

卸载应用程序，重新生成、 它再次运行，并请按照下列步骤来发送其他消息：

1.  在 Firebase 控制台中，单击**新消息**。 

2.  上**Compose 消息**页上，输入消息文本并选择**单个设备**。 

3.  从 IDE 输出窗口复制的标记字符串并将其粘贴到**FCM 注册令牌**Firebase 控制台像以前那样字段。 

4.  确保应用正在运行在前台，然后单击**发送消息**Firebase 控制台中： 

    [![从控制台发送另一条消息](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  当**查看消息**显示对话框，请单击**发送**。

6.  传入的消息记录到 IDE 输出窗口中：

    [![消息正文打印到输出窗口](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notifications-sender"></a>添加本地通知发件人

在此剩余的示例中，传入 FCM 消息将转换为应用程序运行在前台时启动本地通知。 编辑**MyFirebaseMessageService.cs**并添加以下`using`语句： 

```csharp
using FCMClient;
using System.Collections.Generic;
```

添加以下方法`MyFirebaseMessagingService`: 

```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (string key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }
    var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

    var notificationBuilder = new Notification.Builder(this)
        .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
        .SetContentTitle("FCM Message")
        .SetContentText(messageBody)
        .SetAutoCancel(true)
        .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManager.FromContext(this);
    notificationManager.Notify(0, notificationBuilder.Build());
}
```

为了区分来自后台通知此通知，此代码将标记与不同于 applicatiion 图标的图标的通知。 添加[ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png)到**可资源/绘制**和将其包含在**FCMClient**项目。 

`SendNotification`方法使用`Notification.Builder`创建通知，和`NotificationManager`用于启动通知。 通知包含`PendingIntent`，将允许用户打开的应用并查看传入的字符串的内容`messageBody`。 具体取决于你打算使用你的应用面向 Android 的版本，你可能想要使用`NotificationCompat.Builder`相反。 有关详细信息`Notification.Builder`，请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。 

调用`SendNotification`方法末尾`OnMessageReceived`方法： 

```csharp
SendNotification(message.GetNotification().Body, message.Data);
```

由于这些更改，`SendNotification`时收到通知时的应用程序已在前台，而通知将显示在通知区域中将运行。 如果应用程序 backgrounded，`SendNotification`将不会运行，并且将相反，启动 （在本演练前面所示） 背景通知。 

### <a name="send-the-last-message"></a>发送最后一条消息

卸载应用程序，重新生成它，它再次运行，然后使用以下步骤以发送最后一条消息：

1.  在 Firebase 控制台中，单击**新消息**。 

2.  上**Compose 消息**页上，输入消息文本并选择**单个设备**。 

3.  从 IDE 输出窗口复制的标记字符串并将其粘贴到**FCM 注册令牌**Firebase 控制台像以前那样字段。 

4.  确保应用正在运行在前台，然后单击**发送消息**Firebase 控制台中： 

    [![发送前景消息](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

此时，在新的通知中还打包在输出窗口中记录的消息&ndash;在前台运行应用程序时，通知栏中会出现通知图标： 

[![前景消息的通知图标](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

当你打开通知时，你应看到从 Firebase 控制台通知 GUI 发送最后一条消息： 

[![带有前景图标所示的前景通知](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)

 
## <a name="troubleshooting"></a>疑难解答

以下描述问题和可能出现时使用 Firebase Cloud Messaging Xamarin.Android 的解决方法。

### <a name="firebaseapp-is-not-initialized"></a>未初始化 FirebaseApp

在某些情况下，可能会看到此错误消息：

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

这是一个可通过清除解决方案和重新生成项目解决的已知的问题 (**生成 > 清理解决方案**，**生成 > 重新生成解决方案**)。 有关详细信息，请参阅此[论坛讨论](https://forums.xamarin.com/discussion/96263/default-firebaseapp-is-not-initialized-in-this-process)。


## <a name="summary"></a>摘要

本演练详细 Xamarin.Android 应用程序中实现 Firebase Cloud Messaging 远程通知的步骤。 它描述如何安装所需的包所需的 FCM 通信，并且它说明了如何配置用于访问 FCM 服务器 Android 清单。 它提供演示如何以检查存在的 Google Play 服务的示例代码。 它演示了如何实现与 FCM 协商适用于注册令牌，实例 ID 侦听器服务，并且它说明了如何此代码将创建后台通知时 backgrounded 应用程序。 它解释主题消息，并且它提供用于接收和应用程序运行在前台时显示远程通知消息侦听器服务的示例实现。 


## <a name="related-links"></a>相关链接

- [FCMNotifications （示例）](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Firebase 云消息传送](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
