---
title: "使用 Google Cloud Messaging 的远程通知"
description: "本演练提供在 Xamarin.Android 应用程序中如何使用 Google Cloud Messaging 实现远程通知 （也称为推送通知） 的分步说明。 描述你必须实现的通信使用 Google Cloud Messaging (GCM) 的各种类，它还说明了如何在 Android 的 GCM，访问清单中设置权限以及它演示了端到端消息中，并且示例测试程序。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 64961e9c45c28ede4cc84f7b978da565be4426d9
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>使用 Google Cloud Messaging 的远程通知

_本演练提供在 Xamarin.Android 应用程序中如何使用 Google Cloud Messaging 实现远程通知 （也称为推送通知） 的分步说明。描述你必须实现的通信使用 Google Cloud Messaging (GCM) 的各种类，它还说明了如何在 Android 的 GCM，访问清单中设置权限以及它演示了端到端消息中，并且示例测试程序。_

## <a name="gcm-notifications-overview"></a>GCM 通知概述

在本演练中，我们将创建一个使用 Google Cloud Messaging (GCM) 实现远程通知的 Xamarin.Android 应用程序 (也称为*推送通知*)。 我们将实现的各种的意向和侦听器服务，用于远程发送消息，使用 GCM 和我们将测试模拟应用程序服务器的命令行程序我们实现。 

请注意，Firebase 云消息传送 (FCM) 是新版本的 GCM &ndash; Google 强烈建议使用 FCM 而不是 GCM。 如果你当前使用 GCM，升级到 FCM 建议。 有关 FCM 的详细信息，请参阅[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)。 

你可以继续执行本演练之前，必须获取所需的凭据以使用 Google 的 GCM 服务器;中介绍了此过程[Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)。 具体而言，你将需要*API 密钥*和*发件人 ID*要插入到本演练的示例代码。 

我们将使用以下步骤创建一个启用 GCM 的 Xamarin.Android 客户端应用程序：

1.  安装与 GCM 服务器的通信所需的其他软件包。
2.  配置应用到 GCM 服务器的访问权限。
3.  实现代码以检查存在的 Google Play 服务。 
4.  实现与 GCM 注册令牌协商的注册意向服务。
5.  实现从 GCM 注册令牌更新所侦听的实例 ID 侦听器服务。
6.  实现从应用程序服务器通过 GCM 中接收远程消息的 GCM 侦听器服务。

此应用程序将使用名为的新 GCM 功能*主题消息传送*。 在主题消息传递，应用程序服务器发送一条消息到主题，而不是单个设备的列表。 该主题订阅的设备可能会收到主题消息作为推送通知。 有关 GCM 主题消息传送的详细信息，请参阅 Google[实现主题的消息传送](https://developers.google.com/cloud-messaging/topic-messaging)。 

当客户端应用程序已准备就绪时，我们将实现的命令行 C# 应用程序将一条推送通知发送到 GCM 通过我们的客户端应用程序。 

## <a name="walkthrough"></a>演练

若要开始，让我们创建一个新的空解决方案，称为**RemoteNotifications**。 接下来，让我们将新的 Android 项目添加到基于此解决方案**Android 应用**模板。 让我们调用此项目**ClientApp**。 (如果你不熟悉创建 Xamarin.Android 项目，请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)。)**ClientApp**项目将包含 Xamarin.Android 客户端接收的应用程序通过 GCM 远程通知的代码。 

### <a name="add-required-packages"></a>添加所需的程序包

我们可以实现我们的客户端应用程序代码前，我们必须安装我们将使用与 GCM 通信的多个包。 此外，我们必须添加到我们设备 Google Play 商店应用程序，如果尚未安装。

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>添加 Xamarin Google Play Services GCM 包

从 Google Cloud Messaging，接收消息[Google Play 服务](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/)framework 必须存在于设备上。 缺少这种框架，Android 应用程序无法从 GCM 服务器接收消息。 Google Play 服务在后台运行时在 Android 设备处于打开状态，静默侦听来自 GCM 的消息。 这些消息到达时, Google Play 服务将消息转换为意向，然后广播到已注册了它们的应用程序的这些方法。 

在 Visual Studio 中，右键单击**引用 > 管理 NuGet 包...**; Visual Studio 中用于 Mac 上，右键单击**包 > 添加包...**.搜索**Xamarin Google Play 服务-GCM**并安装到此包**ClientApp**项目： 

[![安装 Google Play Services](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

当你安装**Xamarin Google Play 服务-GCM**， **Xamarin Google Play 服务的基**会自动安装。 如果遇到错误时，更改项目的*到目标最低 Android*以外设置为值**使用 SDK 版本进行编译**，然后重试 NuGet 安装。 

接下来，编辑**MainActivity.cs**并添加以下`using`语句：

```csharp
using Android.Gms.Common;
using Android.Util;
```

这使得类型 Google Play 服务 GMS 包中适用于我们的代码，并添加日志记录功能，我们将用来跟踪与 GMS 我们事务。 

#### <a name="google-play-store"></a>Google Play 商店

若要从 GCM 中接收消息，必须在设备上安装 Google Play 商店应用程序。 （每次在设备上安装 Google Play 应用程序时，Google Play 商店也会安装，因此很可能，已安装在测试设备。）而无需 Google Play，Android 应用程序无法从 GCM 中接收消息。 如果你还没有安装在你的设备上的 Google Play 商店应用程序，请访问[Google Play](https://support.google.com/googleplay)网站下载并安装 Google Play。 

或者，你可以使用运行 Android 2.2 或更高版本，而不是测试设备 （您不必在 Android 仿真程序上安装 Google Play 商店） 的 Android 仿真程序。 但是，如果你使用仿真程序，你必须使用 Wi-fi 连接到 GCM，并且必须在你的 Wi-fi 防火墙中打开多个端口，如稍后在本演练所述。 

### <a name="set-the-package-name"></a>设置包名称

在[Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)，我们指定我们 GCM 启用的应用程序的包名称 (此包名称也可作为*应用程序 ID*我们的 API 密钥和发件人 ID 与该键相关联)。 让我们打开的属性**ClientApp**项目，然后将包名称设置为此字符串。 在此示例中，我们可以将包名称设置为`com.xamarin.gcmexample`:

[![设置包名称](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

请注意，客户端应用程序将无法从 GCM 中接收的注册令牌，如果此包名称不*完全*匹配的包名，我们进入 Google 开发人员控制台。 

### <a name="add-permissions-to-the-android-manifest"></a>将权限添加到 Android 清单

Android 应用程序必须具有配置才能从 Google Cloud Messaging 接收通知的以下权限： 

-   `com.google.android.c2dm.permission.RECEIVE` &ndash; 向我们的应用程序以注册和接收来自 Google Cloud Messaging 消息授予权限。 (用途`c2dm`意味着？ 这代表_云到设备消息传送_，即到 GCM 现在已弃用前置任务。 
    GCM 仍使用`c2dm`在很多其权限字符串。) 

-   `android.permission.WAKE_LOCK` &ndash; （可选）设备从 CPU 防止进入睡眠状态时用于侦听消息。 

-   `android.permission.INTERNET` &ndash; 授予访问 internet，以便客户端应用程序能够与 GCM。 

-   *包名称*`.permission.C2D_MESSAGE` &ndash;与 Android 注册该应用程序和请求权限以独占方式接收所有 C2D （云到设备） 消息。 *Package_name*前缀等同于你的应用程序 id。 

我们将在 Android 清单中设置这些权限。 让我们编辑**AndroidManifest.xml**并将内容替换为以下 XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
    package="YOUR_PACKAGE_NAME" 
    android:versionCode="1" 
    android:versionName="1.0" 
    android:installLocation="auto">
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" />
    <permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" 
                android:protectionLevel="signature" />
    <application android:label="ClientApp" android:icon="@drawable/Icon">
    </application>
</manifest>
```

在上面的 XML 中，更改*您的包名*到客户端应用程序项目的包名称。 例如 `com.xamarin.gcmexample`。 

### <a name="check-for-google-play-services"></a>Google Play Services 检查

对于本演练，我们正在创建基本应用程序使用单个`TextView`在 UI 中。 此应用程序并不直接表明与 GCM 交互。 相反，我们将观察输出窗口以查看如何使用 GCM，我们应用握手和到达时，我们将检查新通知的通知栏。 

首先，让我们来创建消息区域的布局。 编辑**Resources.layout.Main.axml**并将内容替换为以下 XML: 

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

保存**main.axml**并将其关闭。

当客户端应用程序启动时，我们希望它以验证之前我们尝试联系 GCM Google Play 服务可用。 编辑**MainActivity.cs**和替换``count``实例使用以下实例变量声明的变量声明： 

```csharp
TextView msgText;
```

接下来，添加以下方法**MainActivity**类： 

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
            msgText.Text = "Sorry, this device is not supported";
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

此代码检查设备以查看是否已安装 Google Play 服务 APK。 如果未安装，则在消息区域中，指示用户从 Google Play 商店下载 APK （或在设备的系统设置中启用它） 被显示一条消息。 因为我们想要运行此检查，客户端应用程序启动时，我们将在末尾添加对此方法的调用`OnCreate`。 


接下来，替换`OnCreate`方法替换为以下代码：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

此代码检查存在的 Google Play 服务 APK，并将结果写入消息区域中。 

让我们完全重新生成并运行应用程序。 你应看到类似于以下屏幕截图的屏幕： 

[![Google Play 服务是可用](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

如果没有获得此结果，请验证你的设备和上是否安装了 Google Play 服务 APK **Xamarin Google Play 服务-GCM**包添加到你**ClientApp**项目所述更早版本。 如果你收到生成错误，请尝试清除解决方案并再次生成项目。 

接下来，我们将编写代码以联系 GCM 并取回注册令牌。

### <a name="register-with-gcm"></a>注册到 GCM

应用程序可以接收来自应用程序服务器的远程通知之前，它必须注册到 GCM 并取回注册令牌。 向 GCM 注册我们的应用程序的工作由`IntentService`我们创建的。 我们`IntentService`执行以下步骤： 

1.  使用[InstanceID](https://developers.google.com/instance-id/) API 以生成授权我们的客户端应用程序来访问应用程序服务器的安全令牌。 反过来，我们将取回注册令牌从 GCM。

2.  （如果应用程序服务器需要它），将转发到的应用服务器的注册令牌。

3.  订阅一个或多个通知主题通道。

我们实现此后`IntentService`，我们将此项可查看是否收到注册令牌从 GCM 中测试。

添加新的文件称为**RegistrationIntentService.cs**和模板代码替换为以下：


```csharp
using System;
using Android.App;
using Android.Content;
using Android.Util;
using Android.Gms.Gcm;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false)]
    class RegistrationIntentService : IntentService
    {
        static object locker = new object();

        public RegistrationIntentService() : base("RegistrationIntentService") { }

        protected override void OnHandleIntent (Intent intent)
        {
            try
            {
                Log.Info ("RegistrationIntentService", "Calling InstanceID.GetToken");
                lock (locker)
                {
                    var instanceID = InstanceID.GetInstance (this);
                    var token = instanceID.GetToken (
                        "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);

                    Log.Info ("RegistrationIntentService", "GCM Registration Token: " + token);
                    SendRegistrationToAppServer (token);
                    Subscribe (token);
                }
            }
            catch (Exception e)
            {
                Log.Debug("RegistrationIntentService", "Failed to get a registration token");
                return;
            }
        }

        void SendRegistrationToAppServer (string token)
        {
            // Add custom implementation here as needed.
        }

        void Subscribe (string token)
        {
            var pubSub = GcmPubSub.GetInstance(this);
            pubSub.Subscribe(token, "/topics/global", null);
        }
    }
}
```

在上面的示例代码中，更改*YOUR_SENDER_ID*到客户端应用程序项目的发件人 ID 号。 若要为你的项目获取发件人 ID: 

1.  登录到[Google Cloud Console](https://console.cloud.google.com/)然后从下拉菜单中选择你的项目名称。 在**项目信息**为你的项目，显示的窗格中单击**转到项目设置**:

    [![选择 XamarinGCM 项目](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2.  上**设置**页上，找到**项目编号**&ndash;这是你的项目的发件人 ID:

    [![显示的项目编号](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

我们想要启动我们`RegistrationIntentService`在我们的应用程序开始运行时。 编辑**MainActivity.cs**和修改`OnCreate`方法，以便我们`RegistrationIntentService`后我们检查是否存在的 Google Play 服务已启动： 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView(Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    if (IsPlayServicesAvailable ())
    {
        var intent = new Intent (this, typeof (RegistrationIntentService));
        StartService (intent);
    }
}
```

现在让我们看一看每个部分`RegistrationIntentService`了解它的工作方式。 

首先，我们批注我们`RegistrationIntentService`与要标明我们的服务时不应由系统进行实例化的以下属性： 

```csharp
[Service (Exported = false)]
```

`RegistrationIntentService`构造函数名称工作线程*RegistrationIntentService*使调试变得更容易。 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

核心功能`RegistrationIntentService`驻留在`OnHandleIntent`方法。 让我们演练一下此代码，以了解如何向 GCM 注册我们的应用程序。


##### <a name="request-a-registration-token"></a>请求注册令牌

`OnHandleIntent` 首先调用 Google [InstanceID.GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;)方法以从 GCM 请求注册令牌。 我们将在此代码包装`lock`防止同时发生的多个注册意向的可能性&ndash;`lock`可确保按顺序处理这些意向。 如果我们无法获取注册令牌，将引发异常，并且我们将记录错误。 如果注册成功，`token`设置为从 GCM 得到注册令牌： 

```csharp
static object locker = new object ();
...
try
{
    lock (locker)
    {
        var instanceID = InstanceID.GetInstance (this);
        var token = instanceID.GetToken (
            "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);
        ...
    }
}
catch (Exception e)
{
    Log.Debug ...
```

##### <a name="forward-the-registration-token-to-the-app-server"></a>将注册令牌转发给应用程序服务器

如果我们获取注册令牌 （即，没有引发了异常），我们调用`SendRegistrationToAppServer`关联用户的注册令牌与服务器端帐户 （如果有），由维护我们的应用程序。 因为此实现取决于应用程序服务器的设计，此处提供了一个空的方法： 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

在某些情况下，应用程序服务器不需要用户的注册令牌;在这种情况下，可以省略此方法。 当注册令牌发送到应用程序服务器中、`SendRegistrationToAppServer`应维护一个布尔值以指示令牌是否已发送到服务器。 如果此布尔值为 false，`SendRegistrationToAppServer`将令牌发送到应用程序服务器&ndash;否则，该令牌已发送到上一个调用中的应用程序服务器。 


##### <a name="subscribe-to-the-notification-topic"></a>订阅通知主题

接下来，我们调用我们`Subscribe`方法，以指示到我们想要通知主题订阅的 GCM。 在`Subscribe`，我们调用[GcmPubSub.Subscribe](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;) API 订阅我们的客户端应用程序下的所有邮件`/topics/global`:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

应用程序服务器必须通知将消息发送到`/topics/global`如果我们要接收它们。 请注意，主题名称下`/topics`可以是任何你想，只要应用程序服务器和客户端应用程序都达成这些名称。 (在这里，我们选择名称`global`以指示我们想要接收应用程序服务器支持的所有主题上的消息。) 

有关在服务器端消息传送的 GCM 主题的信息，请参阅 Google[发送消息的主题](https://developers.google.com/cloud-messaging/topic-messaging)。 


#### <a name="implement-an-instance-id-listener-service"></a>实现实例 ID 侦听器服务

注册令牌是唯一的并且安全;但是，客户端应用程序 （或 GCM） 可能需要刷新出现应用程序重新安装或安全问题注册令牌。 为此，我们必须实现`InstanceIdListenerService`从 GCM 响应令牌刷新请求。 

添加新的文件称为**InstanceIdListenerService.cs**和模板代码替换为以下： 

```csharp
using Android.App;
using Android.Content;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
    class MyInstanceIDListenerService : InstanceIDListenerService
    {
        public override void OnTokenRefresh()
        {
            var intent = new Intent (this, typeof (RegistrationIntentService));
            StartService (intent);
        }
    }
}
```

批注`InstanceIdListenerService`通过以下特性以指示该服务是无法由系统进行实例化，它可以接收 GCM 注册令牌 (也称为*实例 ID*) 刷新请求： 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

`OnTokenRefresh`在我们的服务的方法会启动`RegistrationIntentService`，以便它可以截取新的注册令牌。


#### <a name="test-registration-with-gcm"></a>测试注册到 GCM

让我们完全重新生成并运行应用程序。 如果你已成功从 GCM 收到的注册令牌，注册令牌应显示在输出窗口中。 例如: 

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>句柄下游消息 

我们到目前为止已实现的代码是仅"设置"代码;它会检查是否安装 Google Play 服务，并协商使用 GCM 和应用程序服务器，若要准备接收远程通知我们的客户端应用程序。 但是，我们还实际接收和处理下游通知消息的代码中实现。 若要执行此操作，我们必须实现*GCM 侦听器服务*。 此服务接收来自应用程序服务器的主题消息，并作为通知本地广播它们。 我们实现此服务后，我们将创建一个测试程序，将消息发送到 GCM，以便我们可以看到如果我们实现能够正常运行。 


#### <a name="add-a-notification-icon"></a>添加通知图标

让我们首先添加小图标将出现在通知区域中，启动我们通知时。 你可以复制[此图标](remote-notifications-with-gcm-images/ic-stat-ic-notification.png)到你的项目或创建你自己的自定义图标。 我们将名称的图标文件**ic_stat_button_click.png**和将其复制到**可资源/绘制**文件夹。 请记住使用**添加 > 现有项...**要包含在你的项目中的此图标文件。


#### <a name="implement-a-gcm-listener-service"></a>实现 GCM 侦听器服务

添加新的文件称为**GcmListenerService.cs**和模板代码替换为以下：

```csharp
using Android.App;
using Android.Content;
using Android.OS;
using Android.Gms.Gcm;
using Android.Util;

namespace ClientApp
{
    [Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
    public class MyGcmListenerService : GcmListenerService
    {
        public override void OnMessageReceived (string from, Bundle data)
        {
            var message = data.GetString ("message");
            Log.Debug ("MyGcmListenerService", "From:    " + from);
            Log.Debug ("MyGcmListenerService", "Message: " + message);
            SendNotification (message);
        }

        void SendNotification (string message)
        {
            var intent = new Intent (this, typeof(MainActivity));
            intent.AddFlags (ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity (this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                .SetSmallIcon (Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle ("GCM Message")
                .SetContentText (message)
                .SetAutoCancel (true)
                .SetContentIntent (pendingIntent);

            var notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
            notificationManager.Notify (0, notificationBuilder.Build());
        }
    }
}
```

让我们看一看每个部分我们`GcmListenerService`了解它的工作方式。 

首先，我们批注`GcmListenerService`具有一个属性以指示此服务是不由系统进行实例化，并且我们包含了一个意向的筛选器，以指示它将接收 GCM 消息： 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

当`GcmListenerService`从 GCM，接收消息`OnMessageReceived`调用方法。 此方法从传入的提取消息内容`Bundle`，记录消息内容 （以便我们可以在输出窗口中查看它），并调用`SendNotification`以启动包含收到的消息内容的本地通知： 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

`SendNotification`方法使用`Notification.Builder`创建通知，然后使用`NotificationManager`以启动通知。 实际上，这将远程通知消息转换为本地通知向用户显示。
有关使用`Notification.Builder`和`NotificationManager`，请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。


#### <a name="declare-the-receiver-in-the-manifest"></a>声明在清单中的接收方

我们可以从 GCM 中接收消息之前，我们必须声明 Android 清单中的 GCM 侦听器。 让我们编辑**AndroidManifest.xml**和替换`<application>`部分使用以下 XML: 

```xml
<application android:label="RemoteNotifications" android:icon="@drawable/Icon">
    <receiver android:name="com.google.android.gms.gcm.GcmReceiver" 
              android:exported="true" 
              android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="YOUR_PACKAGE_NAME" />
        </intent-filter>
    </receiver>
</application>
```

在上面的 XML 中，更改*您的包名*到客户端应用程序项目的包名称。 在本演练示例中，包名称是`com.xamarin.gcmexample`。 

让我们看一下此 XML 中的每个设置的作用：

<table>
    <thead>
        <tr>
            <th>设置</th>
            <th>描述</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><code>com.google.android.gms.gcm.GcmReceiver</code></td>
            <td>声明，我们的应用程序可实现 GCM 接收方来捕获和处理传入的推送通知消息。</td>
        </tr>
        <tr>
            <td><code>com.google.android.c2dm.permission.SEND</code></td>
            <td>声明仅 GCM 服务器可以直接向应用程序发送消息。</td>
        </tr>
        <tr>
            <td><code>com.google.android.c2dm.intent.RECEIVE</code></td> 
            <td>意向播发我们的应用程序处理从 GCM 广播的消息的筛选器。</td>
        </tr>
        <tr>
            <td><code>com.google.android.c2dm.intent.REGISTRATION</code></td>
            <td>播发我们的应用程序处理新的注册意向的意向筛选器 （即，我们已实现的实例 ID 侦听器服务）。</td>
        </tr>
    </tbody>
</table>

或者，你可以按照声明`GcmListenerService`使用这些属性，而不是在 XML; 中指定它们在这里我们指定在**AndroidManifest.xml**以便更轻松地遵循的代码示例。 


### <a name="create-a-message-sender-to-test-the-app"></a>创建消息发送方测试应用程序

让我们将 C# 桌面控制台应用程序项目添加到解决方案，调用它**MessageSender**。 我们将使用此控制台应用程序来模拟应用程序服务器&ndash;将发送到的通知消息**ClientApp**通过 GCM。 


#### <a name="add-the-jsonnet-package"></a>添加 Json.NET 包

在此控制台应用中，我们正在构建包含我们想要向客户端应用程序发送通知消息的 JSON 负载。 我们将使用**Json.NET**中打包**MessageSender**以使其更轻松地生成所需的 GCM 的 JSON 对象。 在 Visual Studio 中，右键单击**引用 > 管理 NuGet 包...**; Visual Studio 中用于 Mac 上，右键单击**包 > 添加包...**. 

让我们搜索**Json.NET**包并将它安装在项目中： 

[![安装 Json.NET 包](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)


#### <a name="add-a-reference-to-systemnethttp"></a>添加对 System.Net.Http 的引用

我们还需要添加对的引用`System.Net.Http`，以便我们可以实例化`HttpClient`将我们的测试消息发送到 GCM。 在**MessageSender**项目，右键单击**引用 > 添加引用**和向下滚动，直到你看到**System.Net.Http**。 旁边放置一个复选标记**System.Net.Http**单击**确定**。 


#### <a name="implement-code-that-sends-a-test-message"></a>发送测试消息的实现代码

在**MessageSender**，编辑**Program.cs**并将内容替换为以下代码：

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq;

namespace MessageSender
{
    class MessageSender
    {
        public const string API_KEY = "YOUR_API_KEY";
        public const string MESSAGE = "Hello, Xamarin!";

        static void Main (string[] args)
        {
            var jGcmData = new JObject();
            var jData = new JObject();

            jData.Add ("message", MESSAGE);
            jGcmData.Add ("to", "/topics/global");
            jGcmData.Add ("data", jData);

            var url = new Uri ("https://gcm-http.googleapis.com/gcm/send");
            try
            {
                using (var client = new HttpClient())
                {
                    client.DefaultRequestHeaders.Accept.Add(
                        new MediaTypeWithQualityHeaderValue("application/json"));

                    client.DefaultRequestHeaders.TryAddWithoutValidation (
                        "Authorization", "key=" + API_KEY);

                    Task.WaitAll(client.PostAsync (url,
                        new StringContent(jGcmData.ToString(), Encoding.Default, "application/json"))
                            .ContinueWith(response =>
                            {
                                Console.WriteLine(response);
                                Console.WriteLine("Message sent: check the client device notification tray.");
                            }));
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Unable to send GCM message:");
                Console.Error.WriteLine(e.StackTrace);
            }
        }
    }
}
```

在上述代码中，更改*YOUR_API_KEY*到客户端应用程序项目的 API 密钥。 

此测试应用程序服务器将发送到 GCM 的以下 JSON 格式的消息：

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM，反过来，将转发到客户端应用程序的此消息。 让我们生成**MessageSender**和打开我们可以在其中运行命令行从它的控制台窗口。



### <a name="try-it"></a>尝试一下！

现在我们已准备好测试我们的客户端应用程序。 如果你使用仿真器或如果你的设备通过 Wi-fi 与 GCM 通信时，则必须 GCM 消息以获取防火墙上打开以下的 TCP 端口： 5228、 5229 和 5230。

启动客户端应用，并监视输出窗口。 后`RegistrationIntentService`成功收到注册从 GCM 令牌，输出窗口应显示该令牌与日志输出与下面类似的：

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

此时客户端应用程序已准备好接收远程通知消息。 从命令行中，运行**MessageSender.exe**程序将"Hello，Xamarin"的通知消息发送给客户端应用程序。
如果你不生成**MessageSender**项目，请立即重新启动。

若要运行**MessageSender.exe**下 Visual Studio 中，打开命令提示符，将更改为**MessageSender/bin/Debug**目录，并运行命令直接：

```cmd
MessageSender.exe
```

若要运行**MessageSender.exe**下适用于 Mac 的 Visual Studio，打开一个终端会话，将更改为**MessageSender/bin/Debug**的目录，并使用 mono 运行**MessageSender.exe** 

```bash
mono MessageSender.exe
```

它可能需要一分钟传播通过 GCM 和反馈至客户端应用程序的消息。 如果已成功收到消息时，我们应看到类似于输出窗口中的以下输出： 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

此外，你应注意到新的通知图标已显示在通知栏： 

[![Notiication 图标将出现在设备上](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

打开通知栏，以查看通知时，你应看到我们远程通知：

[![显示通知消息](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

祝贺你，你的应用程序已收到其第一次远程通知 ！

请注意强制停止应用程序时，将不再接收 GCM 消息。 若要强制停止后恢复通知，必须在应用中进行手动重新启动。 有关此 Android 策略的详细信息，请参阅[启动停止的应用程序上的控件](https://developer.android.com/about/versions/android-3.1.html#launchcontrols)，这[堆栈溢出文章](http://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267)。 

 
## <a name="summary"></a>摘要

本演练详细 Xamarin.Android 应用程序中实现远程通知的步骤。 它描述如何安装其他包所需的 GCM 通信，并且它说明了如何配置应用程序访问权限的 GCM 服务器。 它提供用于说明如何检查存在的 Google Play 服务、 如何实现注册意向服务和实例 ID 侦听器服务，协商使用 GCM 注册令牌，以及如何实现 GCM 侦听器的示例代码接收和处理远程通知消息的服务。 最后，我们实现的命令行测试程序，将测试通知发送到 GCM 通过我们的客户端应用程序。 


## <a name="related-links"></a>相关链接

- [GCM RemoteNotifications （示例）](https://developer.xamarin.com/samples/monodroid/RemoteNotifications)
- [Google 云消息传送](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
