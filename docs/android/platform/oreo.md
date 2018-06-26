---
title: Oreo 功能
description: 如何开始使用 Xamarin.Android 开发适用于 Android 的最新版本的应用。
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: a23072427a74119bfa339fea8a695cd13b775685
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935098"
---
# <a name="oreo-features"></a>Oreo 功能

_如何开始使用 Xamarin.Android 开发适用于 Android 的最新版本的应用。_

[Android 8.0 Oreo](https://developer.android.com/index.html)是最新的 Android 版本可从 Google。 Android Oreo 为 Xamarin.Android 开发人员提供感兴趣的许多新功能。 这些功能包括 XML、 可下载的字体、 自动填充和图片 (PIP) 中的图片中的通知通道、 通知徽章、 自定义字体。 Android Oreo 包括新 Api，这些新的进行，并且这些 Api 可用于 Xamarin.Android 应用时使用 Xamarin.Android 8.0 及更高版本。

[![Android Oreo 英雄映像](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

本文旨在帮助你开始开发 Android 8.0 Oreo Xamarin.Android 应用中。 它还说明了如何安装所需更新、 配置 SDK，并为测试中创建的仿真程序 （或设备）。 它还提供指向示例应用演示如何在 Xamarin.Android 应用程序中使用 Android Oreo 功能 Android 8.0 Oreo 中的新功能的概述。


## <a name="requirements"></a>要求

以下被需要使用 Android Oreo 功能基于 Xamarin 的应用中：

-   **Visual Studio** &ndash; 15.5 或更高版本的 Visual Studio 的版本使用的 Windows，如果是必需的。  如果你使用的是 Mac，Visual Studio for Mac 版本 7.2.0 是必需的。

-   **Xamarin.Android** &ndash; Xamarin.Android 8.0 或更高版本必须安装并配置了 Visual Studio。

-   **Android SDK** &ndash; Android SDK 8.0 (API 26) 或更高版本必须安装通过 Android SDK 管理器。



## <a name="getting-started"></a>入门

若要开始使用 Xamarin.Android Android Oreo，必须下载并安装最新工具和 SDK 包，然后才能创建 Android Oreo 项目：

1. 更新到最新版本的 Visual Studio。

2. 安装**Android 8.0.0 (API 26)** 或更高版本的包和工具通过 SDK 管理器。

3. 创建新的 Xamarin.Android 项目面向 Android Oreo (API 26)。

4. 配置仿真程序或设备以进行测试 Android Oreo 应用。

下列各节介绍了每个步骤：



### <a name="update-visual-studio-and-xamarinandroid"></a>更新 Visual Studio 和 Xamarin.Android

若要添加到 Visual Studio Android Oreo 支持，请执行以下操作：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   如果你在使用 Visual Studio 2017: 

    1. Visual Studio 2017 15.5 或更高版本的更新 (请参阅[更新 Visual Studio 2017](https://docs.microsoft.com/en-us/visualstudio/install/update-visual-studio))。

    2. 使用 SDK 管理器[安装说明](~/android/get-started/installation/android-sdk.md#installation)安装 Xamarin SDK 管理器。
       必须安装 Xamarin SDK 管理器，因为 Google 不提供独立支持 API 26.0 及更高版本的 GUI SDK 管理器。

-   如果你使用的 Visual Studio 2015 中，我们建议降级到 25 的 SDK 工具并使用旧 Google 仿真程序管理器 GUI。 SDK 工具 25 仍可以一起 API 26，月 27 日和更新版本中，使用，并且不会影响开发新的平台。 这将使你接口管理较旧版本的 VS 的 Android SDK。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

-   更新到最新稳定版本的 Visual Studio 2017 Mac 中所述[更新适用于 Mac 的 Visual Studio](https://docs.microsoft.com/en-us/visualstudio/mac/update)。

-----

有关 Android Oreo Xamarin 支持的详细信息，请参阅[Xamarin.Android 8.0 发行说明](https://developer.xamarin.com/releases/android/xamarin.android_8/xamarin.android_8.0/)。



### <a name="install-the-android-sdk"></a>安装 Android SDK

若要使用 Xamarin.Android 8.0 创建项目，你必须首先使用 Xamarin Android SDK 管理器安装的 SDK 平台**Android 8.0-Oreo**或更高版本。 你还必须安装 Android SDK 工具 26.0 或更高版本。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 启动 SDK 管理器 (在 Visual Studio 中，单击**工具 > Android > Android SDK 管理器**)。

2. 安装**Android 8.0-Oreo**包。 如果使用 Android SDK 仿真程序，请务必包括**x86**你将需要的系统映像：

    [![选择在 Android SDK 管理器中的 Android 8.0 包](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. 安装**Android SDK 工具 26.0.2**或更高版本， **Android 的 SDK 平台工具 26.0.0**或更高版本，和**Android SDK Build-tools 26.0.0** （或更高版本）：

    [![在 Android SDK 管理器中选择 Android SDK 工具 26](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 启动 SDK 管理器 (在 Visual Studio for Mac 中，单击**工具 > SDK 管理器**)。

2. 安装**Android 8.0-Oreo** SDK 包。 如果使用 Android SDK 仿真程序，请务必包括**x86**你将需要的系统映像：

    [![选择在 SDK 管理器中的 Android 8.0 包](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. 安装**Android SDK 工具 26.0.2**或更高版本， **Android 的 SDK 平台工具 26.0.0**或更高版本，和**Android SDK Build-tools 26.0.0** （或更高版本）：

    [![在 SDK 管理器中选择 Android SDK 工具 26](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin.Android 项目

创建新的 Xamarin.Android 项目。 如果你不熟悉如何使用 Xamarin Android 开发，请参阅[Hello，Android](~/android/get-started/hello-android/index.md)若要了解有关创建 Xamarin.Android 项目。

在创建 Android 项目时，必须配置为面向 Android 8.0 或更高的版本设置。 例如，若要为 Android 8.0 针对你的项目，必须配置您的项目的目标 Android API 级别**Android 8.0 (API 26)**。 建议你还设置你的目标 framework 级别到 API 26 或更高版本。 有关详细信息配置 Android API 级别级别，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。


### <a name="configure-an-emulator-or-device"></a>配置仿真程序或设备

如果你尝试安装 Android SDK 工具 26.0 后启动默认值基于 Google GUI AVD 管理器或更高版本，可能会收到以下错误对话框，指示你使用命令行 AVD 管理器工具**avdmanager**改为:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Android 模拟器管理器警告对话框](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![Android 模拟器管理器警告对话框](oreo-images/mac/03-avd-warning.png)

-----

因为 Google 不再提供独立支持 API 26.0 及更高版本的 GUI AVD 管理器，将显示此消息。 对于 Android 8.0 Oreo，你必须使用 Xamarin Android 模拟器管理器或命令行`avdmanager`工具为 Android Oreo 创建虚拟设备。

若要使用 Android 设备管理器创建和管理虚拟设备，请参阅[管理虚拟设备使用 Android 设备管理器](~/android/get-started/installation/android-emulator/device-manager.md)。
若要创建虚拟设备而无需 Android 设备管理器，请按照下一节中的步骤。


#### <a name="creating-virtual-devices-using-avdmanager"></a>创建虚拟设备使用 avdmanager

若要使用**avdmanager**若要创建新的虚拟设备，请按照下列步骤：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  打开命令提示符窗口，并设置`JAVA_HOME`到您的计算机上的 Java sdk 的位置。 对于典型的 Xamarin 安装，你可以使用以下命令：

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  将 Android SDK 的位置添加`bin`文件夹转换为你`PATH`。
    对于典型的 Xamarin 安装，你可以使用以下命令：

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  关闭命令提示符窗口并打开一个新的命令提示符窗口。 通过创建新的虚拟设备[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令。 例如，若要创建 AVD 名为**AVD Oreo 8.0**使用 x86 系统映像的 API 级别 26，使用以下命令：

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  在提示你时与**你想要创建自定义硬件配置文件 [号]** 可以输入**没有**并接受默认硬件配置文件。 如果您回答**是**， **avdmanager**将提示你使用的自定义的硬件配置文件的问题列表。

检查完**avdmanager**若要创建你的虚拟设备，它将包含在设备下拉列表中：

[![新 AVD 添加到设备下拉菜单](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  打开**终端**窗口并在 mac 上的 Android SDK 工具目录的位置的更改 对于典型的 Xamarin 安装，你可以使用以下命令：

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  通过创建新的虚拟设备[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)命令。 例如，若要创建 AVD 名为**AVD Oreo 8.0**使用 x86 系统映像的 API 级别 26，使用以下命令：

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  在提示你时与**你想要创建自定义硬件配置文件 [号]** 可以输入**没有**并接受默认硬件配置文件。 如果您回答**是**， **avdmanager**将提示你使用的自定义的问题列表的硬件配置文件。

在使用后**avdmanager**若要创建你的虚拟设备，它将包含在设备下拉列表中：

[![新 AVD 添加到设备下拉菜单](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

有关用于测试和调试配置的 Android 仿真程序的详细信息，请参阅[在 Android 仿真程序上进行调试](~/android/deploy-test/debugging/debug-on-emulator.md)。

如果你使用如 Nexus 或像素的物理设备，可以通过无线 (OTA) 更新通过自动更新你的设备或下载的系统映像并直接刷新你的设备。 有关手动到 Android Oreo 更新你的设备的详细信息，请参阅[Nexus 和像素设备的出厂映像](https://developers.google.com/android/images)。



## <a name="new-features"></a>新增功能

Android Oreo 引入了大量新特性和功能，例如通知通道、 通知徽章、 在 XML 中的自定义字体、 可下载的字体、 自动填充和图片中图片。 以下部分突出显示这些功能，并提供链接，以帮助你开始在你的应用程序中使用它们。



### <a name="notification-channels"></a>通知通道

*通知通道*是应用程序定义通知的类别。
你可以创建用于每种类型的发送，所需的通知的通知通道，并且可以创建通知通道，以反映选项所做的应用程序的用户。 新的通知通道功能使你能够在用户可对不同类型的通知细化的控制。 例如，如果你要实现的消息的应用，你可以创建由某个用户创建每个会话组的单独通知通道。

[通知通道](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan)说明如何创建通知通道并将其用于发布本地通知。 实际的代码示例，请参阅[NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels)示例; 此示例应用程序管理两个通道，并设置其他通知选项。



### <a name="notification-badges"></a>通知徽章

通知徽章是出现在应用程序图标上，此屏幕截图中所示的小点：

[![示例应用程序的图标上的通知徽章](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

这些点表示有与该应用程序图标关联的应用中的一个或多个通知通道的新通知&ndash;这些是用户尚未已解除或操作的通知。 用户可以 long 类型的值的按下的图标，以解除或作用于从 long 类型的值按菜单的通知该 appeaars 观察一下通知徽章，与关联的通知。

有关通知徽章的详细信息，请参阅 Android 开发人员[通知徽章](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges)主题。



### <a name="custom-fonts-in-xml"></a>在 XML 中的自定义字体

Android Oreo 引入了*在 XML 中的字体*，从而使你能够将合并为资源的自定义字体。 OpenType (**.otf**) 和 TrueType (**.ttf**) 支持字体格式。 若要将字体添加为资源，请执行以下操作：

1. 创建**资源/字体**文件夹。

2. 复制你的字体文件 (示例中， **.ttf**和 **.otf**文件) 到**资源/字体**。 

3. 如有必要，重命名每个字体文件，以便符合的 Android 文件命名约定 (即，使用仅小写*a 到 z*， *0-9*，和在文件名中的下划线)。 例如，字体文件`Pacifico-Regular.ttf`无法重命名为类似`pacifico.ttf`。

4. 使用新的应用的自定义字体`android:fontFamily`布局 XML 中的属性。 例如，以下`TextView`声明使用添加**pacifico.ttf**字体资源：

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

你还可以创建字体系列 XML 文件，描述多个字体，以及样式和权重的详细信息。 有关详细信息，请参阅 Android 开发人员[在 XML 中的字体](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html)主题。


### <a name="downloadable-fonts"></a>可下载的字体

从 Android Oreo 开始，应用可以从提供程序，而不是将它们捆绑到 APK 申请字体。 仅在需要时，将从网络下载字体。 此功能降低了 APK 大小，节省电话内存和移动电话网络数据使用情况。 你还可以在 Android API 版本 14 和更高版本上使用此功能，通过安装 Android 支持库 26 包。

当你的应用程序需要一种字体时，你创建`FontsRequest`（指定要下载的字体） 的对象，然后将传递到`FontsContract`方法以下载字体。 以下步骤介绍更多详细信息中的字体下载过程：

1.  实例化[FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html)对象。 

2.  子类并实例化[FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html)。

3.  实现[FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29)方法，用于处理完成字体请求。

4.  实现[FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29)方法，用来通知你的应用的字体请求过程中发生任何错误。

5.  调用[FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback))方法来从字体提供程序检索字体。 

当调用`RequestFonts`方法，它首先检查是否本地缓存字体 (从上次调用`RequestFont`)。 如果它不进行缓存，它调用字体提供程序以异步方式检索字体，然后将结果返回到你的应用程序传递通过调用你`OnTypeFaceRetrieved`方法。

[可下载字体](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts)示例演示如何使用 Android Oreo 中引入的可下载字体功能。 

有关下载字体的详细信息，请参阅 Android 开发人员[可下载字体](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html)主题。



### <a name="autofill"></a>自动填充

新_自动填充_Android Oreo 中的框架，使用户更轻松地处理重复的任务，例如登录名、 帐户创建和信用卡交易。 用户花费更少的时间重新键入信息 （这可能导致输入错误）。 你的应用程序可以使用自动填充 Framework 之前，必须在系统设置 （用户可以启用或禁用自动填充） 中启用自动填充服务。

[AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/)示例演示如何将自动填充框架。 它包括客户端活动与视图应 autofilled，并且可以提供给客户端活动的自动填充数据的服务的实现。

有关新的自动填充功能和如何优化应用程序以自动填充的详细信息，请参阅 Android 开发人员[自动填充 Framework](https://developer.android.com/guide/topics/text/autofill.html)主题。



### <a name="picture-in-picture-pip"></a>图片 (PIP) 中的图片

Android Oreo 使它用于在图片中图片 (PIP) 模式下，启动的活动覆盖另一个活动的屏幕。 此功能旨在用于视频播放。

若要指定应用的活动可以使用 PIP 模式，请将以下的标志设置为 true Android 清单中：

```xml
android:supportsPictureInPicture
```

若要指定您的活动的行为方式在 PIP 模式下时，你使用新[PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html)对象。 `PictureInPictureParams` 表示一组用于初始化和更新 PIP 模式 （例如，活动的首选纵横比） 中的活动的参数。 以下新 PIP 方法已添加到`Activity`Android Oreo 中：

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash;将活动置于 PIP 模式。 活动放置在屏幕的角，并在屏幕上的上一个活动填充屏幕的其余部分。

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash;更新活动的 PIP 配置设置 （例如，更改的纵横比）。

[PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture)示例演示如何在 Oreo 中引入的手持设备的图片中图片 (PiP) 模式的基本用法。 示例播放不间断时显示模式或其他活动之间来回切换的视频，这将继续。



### <a name="other-features"></a>其他功能

Android Oreo 包含许多其他新功能，如 Emoji 支持库中，位置 API 后台限制、 宽色域针对应用、 新的音频编解码器、 WebView 增强功能、 改进的键盘导航支持和为一个新的 AAudio （pro 音频） API 的颜色高性能低延迟音频，这些功能，有关详细信息，请参阅 Android 开发人员[Android Oreo 功能和 Api](https://developer.android.com/about/versions/oreo/android-8.0.html)主题。



## <a name="behavior-changes"></a>行为更改

Android Oreo 包括各种系统和现有应用的功能造成的影响的 API 的行为更改。 这些更改将如下所述。


### <a name="background-execution-limits"></a>后台执行限制

为改善用户体验，Android Oreo 施加了限制应用程序可以执行哪些操作时在后台运行。 例如，如果用户是观看视频或者玩游戏，在后台运行的应用程序可能会影响在前台运行需要进行大量视频的应用的性能。 因此，Android Oreo 将放置在不直接与用户交互的应用的以下限制：

1.  **后台服务限制**&ndash;当应用在后台运行时，它具有在其中的几分钟的它仍可以创建和使用服务的窗口。 在该窗口结束时，Android 将停止应用程序的后台服务，并且会将其视为正在_空闲_。

2.  **广播限制** &ndash; Android 7.0 (API 25) 放置在应用注册以接收的广播的限制。 Android Oreo 使这些限制更严格。 例如，Android Oreo 应用不再可以在其清单中注册隐式广播的广播接收的方。

有关新的后台执行限制的详细信息，请参阅 Android 开发人员[后台执行限制](https://developer.android.com/about/versions/oreo/background.html)主题。


### <a name="breaking-changes"></a>重大更改

面向 Android Oreo 或更高版本必须修改 （如果适用） 支持以下更改，其应用的应用：

- Android Oreo 弃用的功能将单个通知的优先级设置。 相反，创建通知通道时设置建议的重要性级别。 你将分配给通知通道的重要性级别适用于所有通知消息，你发布到它。

- 有关面向 Android Oreo 应用`PendingIntent.GetService()`不工作，因为加上在后台启动的服务的新限制。 如果你面向 Android Oreo，则应使用[PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/)相反。  


## <a name="sample-code"></a>代码示例

多个 Xamarin.Android 示例可以用来向您展示如何利用 Android Oreo 功能：

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels)演示如何使用 Android Oreo 中引入新的通知通道系统。 此示例管理两个通知通道： 一种具有默认重要性，另一种具有高的重要性。

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture)演示的手持设备 Oreo 中引入的图片中图片 (PiP) 模式的基本用法。 示例播放不间断时显示模式或其他活动之间来回切换的视频，这将继续。

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework)演示如何使用自动填充框架。 它包括客户端活动与视图应 autofilled，并且可以提供给客户端活动的自动填充数据的服务的实现。

-   [可下载字体](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts)举例说明如何使用前面所述的可下载字体功能。

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat)演示如何使用 EmojiCompat 支持库。 此库可用于防止你的应用显示缺少 emoji 字符作为"豆腐"字符。

-   [位置更新挂起意向](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent)阐释了位置 api 来获取有关设备的位置的更新的用法使用`PendingIntent`。

-   [位置更新前景服务](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService)演示如何使用位置 API 来获取有关设备的位置使用的绑定并已启动前景服务的更新。


## <a name="video"></a>视频

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**使用 C# 的 android 8.0 Oreo 开发**


## <a name="summary"></a>总结

这篇文章引入 Android Oreo 并且说明了如何安装和在 Android Oreo 上配置的最新的工具和 Xamarin.Android 开发的包。 它提供了 Android Oreo 中可用的关键功能的概述，链接到示例源代码几项新功能。 它包含 API 文档的链接和 Android 开发人员主题来帮助你开始创建应用程序 Android Oreo 中。 它也突出显示可能会影响现有应用的最重要的 Android Oreo 行为更改。


## <a name="related-links"></a>相关链接

- [Android 8.0 Oreo](https://developer.android.com/index.html)
