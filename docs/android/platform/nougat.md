---
title: "Nougat 功能"
description: "如何开始使用 Xamarin.Android 来开发 Android Nougat 的应用。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: c666b7d5b680eab3c990950569868eacdb6f30af
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="nougat-features"></a>Nougat 功能

_如何开始使用 Xamarin.Android 来开发 Android Nougat 的应用。_

本文提供了 Android Nougat 中引入的功能概述介绍如何准备要 Xamarin.Android 的 Android Nougat 开发，并提供指向示例应用程序演示如何使用中的 Android Nougat 功能Xamarin.Android 应用程序。


## <a name="overview"></a>概述

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html)是到 Android 6.0 Marshmallow Google 进行追踪。 Xamarin.Android 支持**Android 7.x 绑定**Xamarin Android 7.0 及更高版本。 Android Nougat 添加 Nougat 功能; 如下所述的许多新 Api这些 Api 可供 Xamarin.Android 应用程序时使用 Xamarin.Android 7.0。

[![Android 平板电脑和手机运行 Android Nougat 的英雄映像](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

有关 Android 7.x Api 的详细信息，请参阅[面向开发人员的 Android 7.1](http://developer.android.com/preview/api-overview.html)。
有关 Xamarin.Android 7.0 的已知问题的列表，请参阅[发行说明](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)。

Android Nougat 提供给 Xamarin.Android 开发人员感兴趣的许多新功能。 这些功能包括：

-   **多窗口支持**&ndash;此增强功能使用户在一次打开在屏幕上的两个应用程序。

-   **通知增强功能**&ndash;经过重新设计的通知系统中 Android Nougat 包括*直接答复*允许用户直接从通知对文本消息快速做出响应的功能UI。 此外，如果你的应用程序创建的通知收到消息，则新*捆绑通知*功能可以将捆绑通知在一起作为一个组时收到多条消息。

-   **数据保护程序**&ndash;此功能是一种新的系统服务，可帮助减少应用的移动电话网络数据使用; 它能让用户通过应用如何使用移动电话网络数据的控制。

此外，Android Nougat 带来许多其他增强功能的如新的网络安全配置功能的应用程序开发人员感兴趣，Doze 随时随地、 密钥证明，新在快速设置的 Api 中，多语言环境支持，ICU4J Api WebView 增强功能，对 Java 8 语言功能的访问、 指定了作用域的目录访问权限，自定义指针 API、 平台 VR 支持、 虚拟文件和后台处理优化。

此文章介绍了如何开始构建使用 Android Nougat 尝试的新功能，计划迁移或功能的工作，以面向新的 Android Nougat 平台的应用程序。


## <a name="requirements"></a>惠?

以下是所需基于 Xamarin 的应用中使用新的 Android Nougat 功能：

-   **Visual Studio 或 Visual Studio for Mac** &ndash;如果你使用 Visual Studio 中，版本 4.2.0.628 或 Xamarin for Visual Studio 的更高版本是必需的。 如果你正在使用 Visual Studio 适用于 Mac 的版本 6.1.0 或更高版本的 Visual Studio 为 Mac。

-   **Xamarin.Android** &ndash; Xamarin.Android 7.0 或更高版本必须安装并配置了 Visual Studio 或 Visual Studio for mac。

-   **Android SDK** -Android SDK 7.0 (API 24) 或更高版本必须安装通过 Android SDK 管理器。

-   **Java 开发人员工具包** &ndash; Xamarin Android 7.0 开发需要[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或如果你正在开发的 API 级别 24，更高版本或更高版本 （JDK 8 还支持 API 级别早于 24）。 如果你正在使用自定义控件或窗体预览程序，64 位版本的 JDK 8 是必需的。

> [!IMPORTANT]
> Xamarin.Android 不支持 JDK 9。

请注意，应用程序必须重新生成 Xamarin C6SR4 或更高版本才能可靠地使用 Android Nougat。 因为 Android Nougat 可以链接仅[提供 NDK 本机库](https://developer.android.com/about/versions/nougat/android-7.0-changes.html)，如使用库的现有应用**Mono.Data.Sqlite.dll**在 Android Nougat 上运行，如果它们不正确时可能会崩溃重新生成。



## <a name="getting-started"></a>入门

若要开始使用 Xamarin.Android Android Nougat，必须下载并安装最新工具和 SDK 包，然后才能创建 Android Nougat 项目：

1.  从 Xamarin 安装最新的 Xamarin.Android 更新。

2.  安装**Android 7.0 (API 24)**包和工具或更高版本。

3.  创建新的 Xamarin.Android 项目面向 Android Nougat。

4.  为 Android Nougat 配置的仿真程序或设备。

下列各节介绍了每个步骤：


### <a name="install-xamarin-updates"></a>安装 Xamarin 更新

若要添加对 Android Nougat Xamarin 支持，适用于 Mac 的 Visual Studio 或 Visual Studio 中的更新通道变为稳定的通道并应用最新的更新。 如果你还需要仅在 Alpha 通道或 Beta 通道中当前可用的功能，你可以切换到 Alpha 通道或 Beta 通道 （字母和 Beta 通道还提供支持 Android 7.x）。 有关如何更改更新 （版本） 通道的信息，请参阅[更改更新通道](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/)。



### <a name="install-the-android-sdk"></a>安装 Android SDK

若要使用 Xamarin Android 7.0 创建项目，你必须先使用 Android SDK 管理器安装**SDK 平台 Android N (API 24)**或更高版本。 你还必须安装最新**Android SDK 工具**:

1.  启动 Android SDK 管理器 (在适用于 Mac 的 Visual Studio，使用**工具 > 打开 Android SDK 管理器&hellip;**; 在 Visual Studio 中，使用**工具 > Android > Android SDK 管理器**)。

2.  安装**Android 7.0 (API 24)**或更高版本：

    [![选择在 Android SDK 管理器中的 Android 7.0 包](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  安装最新的 Android SDK 工具：

    [![选择在 Android SDK 管理器中的最新的 Android SDK 工具](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    必须安装 Android SDK 工具修订 25.2.2 或更高版本、 Android SDK 平台工具 24.0.3 或更高版本，且 Android SDK 生成工具 24.0.2 或更高版本。

4.  验证**Java 开发工具包位置**为 JDK 1.8 配置：

    [![配置工具选项下的 JDK 8 路径](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    若要在 Visual Studio 中查看此设置，请单击**工具 > 选项 > Xamarin > Android 设置**。 在适用于 Mac 的 Visual Studio，单击**首选项 > 项目 > SDK 位置 > Android**。



### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin.Android 项目

创建新的 Xamarin.Android 项目。 如果你不熟悉如何使用 Xamarin Android 开发，请参阅[Hello，Android](~/android/get-started/hello-android/index.md)若要了解有关创建 Xamarin.Android 项目。

在创建 Android 项目时，必须配置为面向 Android 7.0 或更高的版本设置。 例如，若要为 Android 7.0 针对你的项目，必须配置您的项目的目标 Android API 级别**Android 7.0 (API 24-Nougat)**。 建议你设置你的目标 framework 级别为 API 24 或更高版本。 有关详细信息配置 Android API 级别级别，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。


> [!NOTE]
> 当前必须设置**最低 Android 版本**到**Android 7.0 (API 24-Nougat)**将您的应用程序部署到 Android Nougat 设备或仿真程序。



### <a name="configure-an-emulator-or-device"></a>配置仿真程序或设备

如果使用仿真程序，启动 Android AVD 管理器，并创建新的设备使用以下设置：

-   设备： Nexus 5 X，Nexus 6，Nexus 6 P，Nexus 播放器 Nexus 9 或像素 c。
-   目标： Android 7.0 的 API 级别 24
-   ABI: x86 或 x86\_64

例如，此虚拟设备被配置为模拟各种 Nexus 6:

[![配置使用 Nexus 6 设备、 Android 7.0 目标和 Intel Atom x86 CPU/ABI 的 AVD](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

如果你使用的物理设备如 Nexus 5 X、 6 或 9，可以通过无线 (OTA) 更新通过自动更新你的设备或下载的系统映像并直接刷新你的设备。 有关手动到 Android Nougat 更新你的设备的详细信息，请参阅[Nexus 设备的 OTA 映像](https://developers.google.com/android/nexus/ota)。

请注意 Android Nougat 不支持 Nexus 5 设备。



## <a name="new-features"></a>新增功能

Android Nougat 引入了大量新特性和功能，如多窗口支持、 通知增强功能和数据保护程序。 以下部分突出显示这些功能，并提供链接，以帮助你开始在你的应用程序中使用它们。



### <a name="multi-window-mode"></a>多窗口模式下运行

多窗口模式下运行，使用户可以使用完整的多任务支持同时打开两个应用程序。 这些应用可以在拆分屏幕模式下运行并行 （横向） 或一个-上面--其他 （纵向）。
用户可以拖动以调整其大小，在应用之间的分隔符，并且他们可以剪切和粘贴的内容应用之间。 在两个应用程序均以多窗口模式下运行，所选的活动将继续运行未选定的活动时暂停但仍将可见。 多窗口模式下运行不会修改 Android 活动生命周期。

[![在纵向与横向多窗口模式下运行的示例应用](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

你可以配置如何对 Xamarin.Android 应用程序的活动支持多窗口模式下运行。 例如，你可以配置在多窗口模式下设置的最小大小的默认高度和宽度的您的应用程序的属性。 你可以使用新`Activity.IsInMultiWindowMode`属性来确定你的活动是否在多窗口模式下运行。 例如:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

[MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/)示例应用程序包含 C# 代码演示如何利用多个窗口具有您的应用程序的用户界面。

有关多窗口模式下运行的详细信息，请参阅[多窗口支持](https://developer.android.com/guide/topics/ui/multi-window.html)。



### <a name="enhanced-notifications"></a>增强的通知

Android Nougat 引入了重新设计的通知系统。 它提供了一个新的直接回复功能，使得用户到快速通知的通知 UI 中直接传入短信回复。 从 Android 7.0，消息可以捆绑在一起作为单个组接收多个消息时的通知。 此外，开发人员可以自定义视图，利用系统修饰在通知中，并利用新的通知模板时生成通知的通知。


#### <a name="direct-reply"></a>直接答复

当用户收到传入消息的通知时，Android Nougat 使的就是它可以回复中通知的消息 （而不是为了发送答复消息传递应用程序打开）。
此内联答复功能使用户能够快速响应直接在通知接口内 SMS 或文本消息：

[![具有内联直接答复字段通知的屏幕截图](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

若要在你的应用程序中支持此功能，你必须添加*内联答复操作*向应用程序通过[RemoteInput](https://developer.xamarin.com/api/type/Android.App.RemoteInput/)对象，以便用户可以直接从通知 UI 文本通过进行回复。
例如，下面的代码生成`RemoteInput`用于接收文本输入，生成答复操作，挂起意向和创建远程输入已启用的操作：

```csharp
// Build a RemoteInput for receiving text input:
var remoteInput = new Android.Support.V4.App.RemoteInput.Builder (EXTRA_REMOTE_REPLY)
    .SetLabel (GetString (Resource.String.reply))
    .Build ();

// Build a Pending Intent for the reply action to trigger:
PendingIntent replyIntent = PendingIntent.GetBroadcast (ApplicationContext,
                                conversation.ConversationId,
                                GetMessageReplyIntent (conversation.ConversationId),
                                PendingIntentFlags.UpdateCurrent);

// Build an Android 7.0 compatible Remote Input enabled action:
NotificationCompat.Action actionReplyByRemoteInput = new NotificationCompat.Action.Builder (
    Resource.Drawable.notification_icon,
    GetString (Resource.String.reply),
    replyIntent).AddRemoteInput (remoteInput).Build ();
```

此操作已添加到通知：

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

[消息服务](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/)示例应用程序包含 C# 代码演示如何扩展通知`RemoteInput`对象。 有关添加内联答复向应用程序的操作对于 Android 7.0 或更高版本，请参阅 Android[答复通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct)主题。


#### <a name="bundled-notifications"></a>捆绑的通知

Android Nougat 可以 （例如，通过消息主题） 的通知消息组合在一起并显示组，而不是每个单独的消息。
这*捆绑通知*功能使用户关闭或存档在一个操作的通知的组。 用户可以向下滑动，以展开的通知，以详细查看每个通知捆绑包：

[![屏幕快照的捆绑通知的示例](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

若要支持捆绑的通知，应用程序可以使用[Builder.SetGroup](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetGroup/p/System.String/)方法捆绑类似的通知。 有关 Android N 中捆绑的通知组的详细信息，请参阅 Android[绑定通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle)主题。


#### <a name="custom-views"></a>自定义视图

Android Nougat 使你可以使用系统通知标头、 操作和可扩展的布局中创建自定义通知视图。 有关 Android Nougat 中的自定义通知视图的详细信息，请参阅 Android[通知增强功能](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements)主题。



### <a name="data-saver"></a>数据保护程序

从 Android Nougat 开始，用户可以启用新*数据保护程序*设置块后台数据使用情况。 此设置还向你的应用程序，以在前台使用较少的数据，只要有可能发出信号。 [ConnectivityManager](https://developer.xamarin.com/api/type/Android.Net.ConnectivityManager/) ，以便你的应用程序可以检查，这样你的应用程序可以为了限制其数据使用情况，启用数据保护程序后，用户是否已启用数据保护程序已在 Android Nougat 扩展。

有关 Android Nougat 中的新数据保护功能的详细信息，请参阅 Android[优化网络数据使用情况](https://developer.android.com/training/basics/network-ops/data-saver.html)主题。



### <a name="app-shortcuts"></a>应用程序快捷方式

引入的 android 7.1*应用快捷方式*功能就到快速启动通用或建议任务与你的应用的用户。
若要激活的快捷方式菜单，用户 long 类型的值的按可实现的应用程序图标为第二个或多个&ndash;快速振动与显示的菜单。
释放按导致保持菜单：

[![应用程序快捷菜单的消息传送应用程序的示例屏幕](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

此功能是 25 或更高版本可用的唯一 API 级别。
有关 Android 7.1 中的新应用程序快捷方式功能的详细信息，请参阅 Android[应用快捷方式](https://developer.android.com/guide/topics/ui/shortcuts.html)主题。


### <a name="sample-code"></a>代码示例

多个 Xamarin.Android 示例可以用来向您展示如何利用 Android Nougat 功能：

-   [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/)演示如何在 Android Nougat 中可用的多窗口 api 使用。 你可以切换到多 windows 模式，以了解它如何影响应用的生命周期和行为的示例应用程序。

-   [消息传递服务](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/)将发送通知的简单服务使用`NotificationCompatManager`。 它还将扩展与通知`RemoteInput`允许 Android Nougat 设备通过文本答复直接从收到通知，而无需打开应用程序的对象。

-   [活动通知](https://developer.xamarin.com/samples/monodroid/android-n/ActiveNotifications/)演示如何使用`NotificationManager`API 要告诉你当前正在显示你的应用程序的多少通知。

-   [作用域目录访问权限](https://developer.xamarin.com/samples/monodroid/android-n/ScopedDirectoryAccess/)演示如何使用作用域的目录访问 API 轻松地访问特定的目录。 这将作为无需定义的替代方法`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`在清单中的权限。

-   [直接启动](https://developer.xamarin.com/samples/monodroid/android-n/DirectBoot/)演示如何将数据存储在设备加密的存储，这是始终引导设备同时之前和之后输入任何用户 credentials(PIN/Pattern/Password) 时才可用。


## <a name="summary"></a>摘要

这篇文章引入 Android Nougat 并且说明了如何安装和在 Android Nougat 上配置的最新的工具和 Xamarin.Android 开发的包。 它还提供指向示例源代码，以帮助你开始在创建应用程序 Android Nougat Android Nougat 中可用的关键功能的概述。


## <a name="related-links"></a>相关链接

- [开发人员的的 android 7.1](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Xamarin Android 7.0 发行说明](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)
