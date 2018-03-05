---
title: "Marshmallow 功能"
description: "本文可帮助你开始使用 Xamarin.Android 开发 Android 6.0 Marshmallow 的应用中使用。"
ms.topic: article
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: b28ca68701394a8b7b0b543a5ae646910e7c8361
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="marshmallow-features"></a>Marshmallow 功能

_本文可帮助你开始使用 Xamarin.Android 开发 Android 6.0 Marshmallow 的应用中使用。_

本文章提供了在 Android 6.0 Marshmallow 的新功能的概述，介绍如何准备要 Xamarin.Android 的 Android Marshmallow 开发，并提供指向示例应用程序演示如何使用新的 Android Marshmallow在 Xamarin.Android 应用中的功能。 

<a name="overview" />

## <a name="overview"></a>概述

[Android 6.0 Marshmallow](http://developer.android.com/about/versions/marshmallow/index.html)，是下一步的主要 Android Android 棒糖形后的版本。
Xamarin.Android 支持 Android Marshmallow，包括：

-   **API 23/Android 6.0 绑定** &ndash; Android 6.0 将添加新功能如下所述的许多新 Api 中; 这些 API 可供 Xamarin.Android 应用程序面向 API 级别 23 时。 有关 Android 6.0 Api 的详细信息，请参阅[Android 6.0 Api](http://developer.android.com/preview/api-overview.html)。 

[![平板电脑和手机运行 Marshmallow 英雄映像](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png)

尽管 Marshmallow 版本主要侧重于"波兰语和质量"，但它还向 Xamarin.Android 开发人员提供感兴趣的许多新功能。 这些功能包括： 

-   **运行时权限**&ndash;此增强功能使用户在运行时批准的情况的安全权限。 

-   **身份验证的改进**&ndash;从 Android Marshmallow 开始，应用程序现在可以使用指纹传感器来进行身份验证用户，以及新*确认凭据*功能最大程度减少需要输入密码。 

-   **应用程序链接**&ndash;此功能有助于消除版**应用选择器**弹出通过自动将应用与 web 域相关联。 

-   **直接共享**&ndash;可以定义*直接共享目标*，这使共享快速、 直观的用户; 此功能允许用户与其他应用共享内容。 

-   **语音交互**&ndash;这个新的 API，可将会话语音功能内置于你的应用程序。 

-   **4 K 显示模式**&ndash;中 Android Marshmallow，你的应用程序可以请求 4 K 上支持该硬件的显示分辨率。 

-   **新的音频功能** &ndash; Marshmallow 从开始，Android 现在支持 MIDI 协议。 它还提供用于创建数字音频捕获和播放对象的新类并提供将音频和输入设备相关联的新 API 挂钩。 

-   **新的视频功能** &ndash; Marshmallow 提供了一个新类，可帮助应用程序呈现音频和视频流同步; 此类还提供对动态播放速率的支持。 

-   **Android for Work** &ndash; Marshmallow 包括增强的控件，为公司拥有，单用户的设备。 它支持无提示安装和卸载的应用程序的设备所有者、 自动接受系统更新、 改进的证书管理、 数据使用情况跟踪、 权限管理和工作状态通知。 

-   **材料设计支持库**&ndash;新*设计支持库*提供设计组件和模式，这样可以更轻松地生成材料设计外观和感觉到你的应用。 

此外，与 Android M，已发布了许多核心 Android 库更新并且这些更新 Android M 和早期版本的 Android 提供新功能。

此外，与 Android Marshmallow 已发布了许多核心 Android 库更新并且这些更新为 Android Marshmallow 和早期版本的 Android 提供新功能。 此文章介绍了如何开始构建使用 Android Marshmallow 应用并且它提供在 Android 6.0 中突出显示的新功能的概述。 


<a name="requirements" />

## <a name="requirements"></a>惠?

以下是所需基于 Xamarin 的应用中使用新的 Android Marshmallow 功能： 

-   **Xamarin.Android** &ndash; Xamarin.Android 5.1.7.12 或更高版本必须安装和配置 Visual Studio 或 Xamarin Studio 使用。

-   **Visual Studio for Mac**或**Visual Studio** &ndash;如果你使用的 Visual Studio for Mac，版本 5.9.7.22 或更高版本。 如果你使用 Visual Studio 中，版本 3.11.1537 或更高版本的 Visual Studio 的 Xamarin 工具是必需的。 

-   **Android SDK** &ndash; Android SDK 6.0 (API 23) 或更高版本必须安装通过 Android SDK 管理器。

-   **Java 开发人员工具包** &ndash; Xamarin.Android 需要[JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或如果你正在开发的 API 级别 24，更高版本或更高版本 （JDK 1.8 还支持 API 级别早于 24 个，包括 Marshmallow）。 如果你正在使用自定义控件或窗体预览程序，64 位版本的 JDK 1.8 是必需的。

你可以继续使用[JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)如果你是专门为 API 级别 23 开发或更早版本。 

<a name="gettingstarted" />

## <a name="getting-started"></a>入门

若要开始使用 Xamarin.Android Android Marshmallow，必须下载并安装最新工具和 SDK 包，然后才能创建 Android Marshmallow 项目： 

1.  安装最新的 Xamarin 更新从**稳定**通道。 

2.  安装 Android 6.0 Marshmallow SDK 包和工具。

3.  创建新的 Xamarin.Android 项目面向 Android 6.0 Marshmallow (API 级别 23)。 

4.  为 Android Marshmallow 配置的仿真程序或设备。

下列各节介绍了每个步骤：

<a name="updates" />

### <a name="install-xamarin-updates"></a>安装 Xamarin 更新

若要更新 Xamarin，以使其包括对 Android 6.0 Marshmallow 的支持，更改的更新通道**稳定**并安装所有更新。 有关从更新通道安装更新的详细信息，请参阅[更改更新通道](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/)。 

<a name="sdkpreview" />

### <a name="install-the-android-60-sdk"></a>安装 Android 6.0 SDK

若要创建 Android Marshmallow Xamarin.Android 项目，你必须先使用 Android SDK 管理器安装 Android 6.0 SDK:

-   启动 Android SDK 管理器 (在适用于 Mac 的 Visual Studio，使用**工具 > SDK 管理器**; 在 Visual Studio 中，使用**工具 > Android > Android SDK 管理器**) 并安装最新的 Android SDK 工具：

    [![选择在 Android SDK 管理器中的 Android SDK 工具](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png)

-   此外，安装最新**Android 6.0** SDK 包：

    [![选择在 Android SDK 管理器中的 Android 6.0 SDK 包](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png)

你必须安装 Android SDK 工具修订 24.3.4 或更高版本。
有关使用 Android SDK 管理器安装 Android 6.0 SDK 的详细信息，请参阅[SDK 管理器](http://developer.android.com/tools/help/sdk-manager.html)。


<a name="xaproject" />

### <a name="start-a-xamarinandroid-project"></a>启动 Xamarin.Android 项目

创建新的 Xamarin.Android 项目。 如果你不熟悉如何使用 Xamarin Android 开发，请参阅[Hello，Android](~/android/get-started/hello-android/index.md)若要了解有关创建 Android 项目。 

在创建 Android 项目时，必须配置为面向 Android 6.0 MarshMallow 的版本设置。 若要针对适用于 Marshmallow 的你的项目，必须配置的项目**API 级别 23 （Xamarin.Android 6.0 版支持）**。 有关详细信息配置 Android API 级别级别，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。


<a name="emudev" />

### <a name="configure-an-emulator-or-device"></a>配置仿真程序或设备

如果使用仿真程序，启动 Android AVD 管理器，并创建新的设备使用以下设置：

-   设备： Nexus 5、 6 或 9。
-   目标： Android 6.0 的 API 级别 23
-   ABI: x86

例如，此虚拟设备被配置为模拟各种 Nexus 5:

[![配置使用 Nexus 5 设备、 Android 6.0 目标和 Intel Atom (x86) AVD](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png)

如果你使用的物理设备如 Nexus 5，6，或 9，你可以安装 Android Marshmallow 的预览图像。 有关为 Android Marshmallow 更新你的设备的详细信息，请参阅[硬件系统映像](http://developer.android.com/preview/download.html#images)。


<a name="newfeatures" />

## <a name="new-features"></a>新增功能

许多 Android Marshmallow 中引入的更改被侧重于 Android 用户可以改进体验，从而提高性能，和修复 bug。 但是，Marshmallow 还引入了一些大量更改到 Android 平台的基础知识。 以下部分突出显示这些增强功能，并提供链接，以帮助你开始在应用程序中使用新的 Android Marshmallow 功能。 


<a name="permissions" />

### <a name="runtime-permissions"></a>运行时权限

Android 权限系统已显著优化和简化以来 Android 棒糖形。 在 Android Marshmallow 用户授予权限的情况提出在运行时中，而不是在安装时间。 若要支持此功能 Android Marshmallow 及更高版本，你可以设计您的应用程序提示用户提供在运行时 （在其中需要的权限的上下文中） 的权限。 此项更改使用户更轻松地开始使用你的应用程序立即因为它简化了安装和升级你的应用程序的过程。 

请参阅[中 Android Marshmallow 请求运行时权限](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/)有关 Xamarin.Android 应用程序中实现运行时权限 （其中包括代码示例） 的详细信息。
Xamarin 还提供了说明了运行时权限 Android Marshmallow （及更高版本） 的工作的示例应用程序： [RuntimePermissions](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions)。

此示例应用程序将演示如下：

-   如何检查以及在运行时的请求权限。
-   如何声明 Android M 设备的权限。

若要使用此示例应用程序：

1.  点击**相机**或**联系人**按钮以显示权限请求对话框。
2.  授予权限以查看照相机或联系人的片段。

有关 Android Marshmallow 的新的运行时权限功能的详细信息，请参阅[使用系统权限](https://developer.android.com/preview/features/runtime-permissions.html)。


<a name="authentication" />

### <a name="authentication-enhancements"></a>身份验证增强功能

Android Marshmallow 包括两个以帮助消除需密码的身份验证增强功能：

-   **指纹身份验证**&ndash;使用指纹扫描用户进行身份验证。

-   **确认凭据**&ndash;基于多长时间已解锁设备的用户进行身份验证。

链接和下文所述的示例应用可帮助你变得很熟悉，借助这些新功能。


<a name="fingerprint" />

#### <a name="fingerprint-authentication"></a>指纹身份验证

在设备上支持指纹扫描硬件，你可以使用新`FingerPrintManager`类对用户进行身份验证。
有关 Android Marshmallow 中的指纹身份验证功能的详细信息，请参阅[指纹身份验证](https://developer.android.com/preview/api-overview.html#fingerprint-authentication)。

Xamarin 提供了演示如何使用已注册的指纹来对你的应用程序中的用户进行身份验证的示例应用程序： [FingerprintDialog](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog)。

若要使用此示例应用程序：

1.  Touch**购买**按钮以打开指纹身份验证对话框。
2.  在已注册指纹进行身份验证中进行扫描。

请注意，此示例应用程序要求使用指纹读取器的设备。
此应用不存储您的指纹 （或你的密码）。


<a name="voice" />

#### <a name="voice-interactions"></a>语音交互

Android Marshmallow 中引入的新语音交互功能允许你的应用程序的用户使用其语音确认操作并从选项列表中选择。 有关语音交互的详细信息，请参阅[语音交互 API 概述](https://developers.google.com/voice-actions/interaction/)。 

请参阅[将会话添加到 Android 应用程序与语音交互](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/)有关 Xamarin.Android 应用程序中实现语音交互 （包括代码示例） 的详细信息。
示例应用是可用的阐释了如何在 Xamarin.Android 应用程序中使用语音交互 API:[语音交互](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions)。


<a name="confirmcred" />

#### <a name="confirm-credential"></a>确认凭据

使用新*确认凭据*功能的 Android Marshmallow，您可以释放无需记住并输入应用程序特定的密码通过基于其设备已解锁多长时间对其进行身份验证的用户。
若要执行此操作，你使用新`SetUserAuthenticationValidityDurationSeconds`方法`KeyGenerator`。 使用`KeyGuardManager`的`CreateConfirmDeviceCredentialIntent`方法重新进行身份验证的用户在应用程序。 有关在 Android Marshmallow 此新功能的详细信息，请参阅[确认凭据](https://developer.android.com/preview/api-overview.html#confirm-credential)。

Xamarin 提供了演示如何在应用中使用设备凭据 （如 PIN、 模式或密码） 的示例应用程序： [ConfirmCredential](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential/)

若要使用此示例应用程序：

1.  安装程序在你的设备上安全锁定屏幕 (**安全 > 安全 > Screenlock**)。
2.  点击**购买**按钮，然后确认安全锁定屏幕凭据。


<a name="chrometabs" />

### <a name="chrome-custom-tabs"></a>Chrome 自定义选项卡

在用户点击 URL 时，应用程序开发人员面临一个选择： 应用程序可以启动浏览器或使用应用内浏览器基于`WebView`。 这两个选项面临的挑战&ndash;启动浏览器是不是可自定义，时的大量上下文切换`WebView`s 使用浏览器不共享状态。 此外，利用`WebView`s 可以添加额外维护开销。 

*Chrome 自定义选项卡*使你可以轻松地像而无需离开你的应用程序用户显示的 Chrome power 具有网站。 此功能可为你的应用程序提供更好地控制用户的 web 体验;它使本机之间的转换和 web 内容更加无缝而无需采用`WebView`。 Chrome 的外观和通过自定义以下大家也会影响你的应用程序： 

-   工具栏颜色

-   进入和退出动画

-   在 Chrome 工具栏和溢出菜单的自定义操作

-   Chrome 开始前和内容预提取 （用于更快加载）

若要充分利用此功能的 Xamarin.Android 应用程序中，下载并安装[Android 支持自定义选项卡库](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/)。
有关此功能的详细信息，请参阅[Chrome 自定义选项卡](https://developer.chrome.com/multidevice/android/customtabs)。


<a name="designlib" />

### <a name="material-design-support-library"></a>材料设计支持库

Android 棒糖形引入[材料设计](http://www.google.com/design/spec/material-design/introduction.html)作为新的设计语言，若要刷新的 Android 体验 (请参阅[材料主题](~/android/user-interface/material-theme.md)有关 Xamarin.Android 应用程序中使用材料设计信息)。 Android Marshmallow Google 引入*Android 设计支持库*方便应用程序开发人员可以采用材料设计的外观和感觉。 此库包括以下组件：

-   **CoordinatorLayout** &ndash;新`CoordinatorLayout`构件是相似但比功能更强大`FrameLayout`。 你可以使用`CoordinatorLayout`作为子视图的容器或为顶级布局，并且该提供`layout_anchor`可以用于定位点相对于其他视图的视图的属性。

-   **折叠工具栏**&ndash;新`CollapsingToolbarLayout`是包装器的折叠应用条`Toolbar`。 (请注意，*应用栏*是什么以前称为*操作栏*。)

-   **浮点操作按钮**&ndash;圆形按钮指示您的应用程序的接口上的主操作。

-   **编辑文本浮点标签**&ndash;使用新`TextInputLayout`小组件 (以便包装`EditText`) 时提示用户输入文本将被隐藏显示浮动的标签。

-   **导航窗格视图**&ndash;新`NavigationView`小组件可帮助你为用户更轻松地导航的方式使用导航抽屉。

-   **Snackbar** &ndash;新`SnackBar`小组件是轻量的反馈机制 （类似于 toast） 屏幕中，最重要的显示在屏幕上的其他元素底部显示简要的消息。

-   **材料的选项卡**&ndash;新`TabLayout`小组件显示地在你的应用程序中实现顶级导航选项卡提供了水平布局。

若要利用[设计支持库](http://developer.android.com/tools/support-library/features.html#design)Xamarin.Android 应用中下载和安装 Xamarin [Xamarin 支持库设计](https://www.nuget.org/packages/Xamarin.Android.Support.Design/)NuGet 包。

请参阅[使用 Android 支持设计库设计美观的材料](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/)有关使用 Xamarin.Android 应用程序中的材料设计支持库 （其中包括代码示例） 的详细信息。
Xamarin 提供的示例应用程序演示 Xamarin.Android 上的新 Android 设计库&ndash; [Cheesesquare](https://developer.xamarin.com/samples/monodroid/android5.0/Cheesesquare)。
此示例演示如何设计库的以下功能：


-   折叠工具栏
-   浮点操作按钮
-   视图锚定
-   NavigationView
-   Snackbar

有关设计库的详细信息，请参阅[Android 设计支持库](http://android-developers.blogspot.co.at/2015/05/android-design-support-library.html)Android 开发人员博客中。


<a name="libraries" />

### <a name="additional-library-updates"></a>其他库更新

除了 Android Marshmallow Google 已宣布到几个核心 Android 库的相关的更新。 Xamarin 提供 Xamarin.Android 支持对这些更新通过多个预览版本的 NuGet 包： 

-   [Google Play 服务](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; Google Play 服务的最新版本包括新*应用邀请*功能，使用户与朋友共享其应用程序。 有关此功能的详细信息，请参阅[与 Google 应用邀请展开应用的 Reach](http://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/)。 

-   [Android 支持库](https://www.nuget.org/packages?q=xamarin+support+library)&ndash;这些 NuGets 提供的同时提供向后兼容版本的 Android framework Api 属性仅适用于库 Api 的功能。 

-   [Android Wearable 库](https://www.nuget.org/packages/Xamarin.Android.Wear)&ndash;此 NuGet 包括 Google Play 服务绑定。 Wearable 库的最新版本对 Android 磨损平台提供了新功能 （包括用于自定义的应用程序更易于导航）。 


## <a name="summary"></a>摘要

这篇文章引入 Android Marshmallow 并且说明了如何安装和 Marshmallow 上配置的最新的工具和 Xamarin.Android 开发的包。 它还提供用于 Xamarin.Android 开发了最令人兴奋的新 Android Marshmallow 功能的概述。


## <a name="related-links"></a>相关链接

- [Android 6.0 Marshmallow](http://developer.android.com/about/versions/marshmallow/index.html)
- [获取 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [功能概述](https://developer.android.com/preview/api-overview.html)
- [发行说明](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1.99/)
- [RuntimePermissions （示例）](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions)
- [ConfirmCredential （示例）](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential)
- [FingerprintDialog （示例）](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog)
