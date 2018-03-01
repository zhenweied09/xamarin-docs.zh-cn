---
title: "果冻 Bean 功能"
description: "本文档将为 Android 4.1 中引入的开发人员提供新功能的高级别的概述。 这些功能包括： 增强型通知，到 Android 无线发送共享大文件，对多媒体、 对等网络发现、 动画、 新的权限更新的更新。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/14/2017
ms.openlocfilehash: 2e54bfc4bea3955dc80a747c4ecce485b78ada1d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="jelly-bean-features"></a>果冻 Bean 功能

_本文档将为 Android 4.1 中引入的开发人员提供新功能的高级别的概述。这些功能包括： 增强型通知，到 Android 无线发送共享大文件，对多媒体、 对等网络发现、 动画、 新的权限更新的更新。_

<a name="Overview" />


## <a name="overview"></a>概述

Android 4.1 (API 级别 16)，也称为"果冻 Bean"，已在 2012 年 7 月 9 日发布。 本文将为使用 Xamarin.Android 的开发人员提供一些 Android 4.1 中的新功能的高级别介绍。 某些引入这些新功能是用于启动活动、 相机、 新的声音和改进的应用程序堆栈导航支持动画的增强功能。 现在可与意向剪切和粘贴。

通过隔离对不稳定的内容提供商的依赖关系的功能得到了改进的 Android 应用程序的稳定性。 服务也可能是独立，因此只能由启动它们的活动可以访问它们。

支持已添加了用于使用的网络服务发现 Bonjour、 UPnP 或多播的 DNS 基于服务。 现已设置格式的文本、 操作按钮和大型图像的更丰富通知可能。

最后 Android 4.1 已添加几个新的权限。

 <a name="Requirements" />


## <a name="requirements"></a>惠?

若要开发 Xamarin.Android 应用程序使用果冻 Bean 需要 Xamarin.Android 4.2.6 或更高版本和 Android 4.1 (API 级别 16) 安装通过 Android SDK 管理器中下面的屏幕快照所示：

[![在 Android SDK 管理器中选择 Android 4.1](jelly-bean-images/image1.png)](jelly-bean-images/image1.png)

 <a name="What's_New" />


## <a name="whats-new"></a>新增功能

 <a name="Animations" />


### <a name="animations"></a>动画

可能通过使用缩放动画或自定义动画启动活动`ActivityOptions`类。 提供了以下新方法，以支持这些动画：

-   `MakeScaleUpAnimation` – 这将创建可从起始位置和大小在屏幕上的非活动窗口内扩展动画。
-   `MakeThumbnailScaleUpAnimation` – 这将从缩略图的形式从屏幕上的指定位置创建的扩大动画。
-   `MakeCustomAnimation` – 这将从应用程序中的资源创建一种动画效果。 没有活动的打开时的一个动画，另一个用于当活动都将停止。


新`TimeAnimator`类为接口提供`TimeAnimator.ITimeListener`每次以动画帧发生更改时，可以通知应用程序。 例如，考虑的以下实现`TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

现在以使用类的实例`TimeAnimator`创建，并设置该侦听器：

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

作为`TimeAnimator`实例正在运行，它将会调用`ITimeAnimator.ITimeListener`，哪个然后将日志如何长生成器已运行，且多长时间它如已在上次方法调用。

 <a name="Application_Stack_Navigation" />


### <a name="application-stack-navigation"></a>应用程序堆栈导航

Android 4.1 对应用程序堆栈导航 Android 3.0 中引入了改进。 通过指定`ParentName`属性`ActivityAttribute`，当用户按下时，Android 可以打开适当的父活动[向上按钮](http://developer.android.com/design/patterns/navigation.html#up-vs-back)上的操作栏中的 Android 将实例化所指定的活动`ParentName`属性。 这允许应用程序保留的活动使给定的任务的层次结构。

对于大多数应用程序设置`ParentName`活动上是适用于 Android 为导航的应用程序堆栈; 提供正确的行为的足够信息Android 将通过创建为每个父活动意向一系列合成必要后退堆栈。 但是，由于这是将人工应用程序堆栈，综合的每个活动将不能将具有自然活动的已保存的状态。 若要提供到综合父活动的已保存的状态，活动可能会重写`OnPrepareNavigationUpTaskStack`方法。 此方法接收`TaskStackBuilder`将具有意向一个集合的实例对象 Android 将用于创建后的堆栈。 活动可以修改这些方法，以便创建综合的活动时，它将接收的正确状态信息。

对于更复杂的方案，可能用于处理行为的导航和构造后的堆栈的活动类有新方法：

-   `OnNavigateUp` -通过重写此方法就可以执行自定义操作时<span class="ui">向上</span>按下按钮。
-   `NavigateUpTo` – 调用此方法将导致应用程序从当前活动导航到给定意向所指定的活动。
-   `ParentActivityIntent` -这用于获取将启动当前活动的父活动的意图。
-   `ShouldUpRecreateTask` – 此方法用于查询是否必须创建综合的后退堆栈以导航到父活动。 返回`true`如果必须创建综合的堆栈。 
-   `FinishAffinity` – 调用此方法将完成当前活动和中的所有活动下面它当前的任务，具有相同的任务关联。
-   `OnCreateNavigateUpTaskStack` – 如何创建综合的堆栈的完全控制所需时，此方法被重写。


 <a name="Camera" />


### <a name="camera"></a>照相机

没有新接口， `Camera.IAutoFocusMoveCallback`，这可以用于检测时自动对焦已启动或停止移动。 在下面的代码段中，可以看到举例说明此新界面：

```csharp
public class AutoFocusCallbackActivity : Activity, Camera.IAutoFocusCallback
{
    public void OnAutoFocus(bool success, Camera camera)
    {
        // camera is an instance of the camera service object.

        if (success)
        {
            // Auto focus was successful - do something here.
        }
        else
        {
            // Auto focus didn't happen for some reason - react to that here.
        }
    }
}
```

新类`MediaActionSound`提供的 API 的一组用于生成适用于各种媒体操作的声音。 有多个操作可能出现的相机、 这些定义的枚举`Android.Media.MediaActionSoundType`:

-   `MediaActionSoundType.FocusComplete` – 在将精力集中已完成时，将会播放此声音。
-   `MediaActionSoundType.ShutterClick` – 拍摄静态映像图片时，将播放此声音。
-   `MediaActionSoundType.StartVideoRecording` – 使用此声音指示开始的视频录制。
-   `MediaActionSoundType.StopVideoRecording` – 若要指示视频录制的末尾，将播放此声音。


举例说明如何使用`MediaActionSound`类可以在下面的代码段中看到：

```csharp
var mediaActionPlayer = new MediaActionSound();

// Preload the sound for a shutter click.
mediaActionPlayer.Load(MediaActionSoundType.ShutterClick);
var button = FindViewById<Button>(Resource.Id.MyButton);

// Play the sound on a button click.
button.Click += (sender, args) => mediaActionPlayer.Play(MediaActionSoundType.ShutterClick);

// This releases the preloaded resources. Don’t make any calls on
// mediaActionPlayer after this.
mediaActionPlayer.Release();
```

 <a name="Connectivity" />


### <a name="connectivity"></a>连接

 <a name="Android_Beam" />


#### <a name="android-beam"></a>Android 无线发送

Android 无线数据交换是一种基于 NFC 技术，允许两个 Android 设备相互之间进行通信。 Android 4.1 提供更好地支持的大型文件传输。 使用新的方法时`NfcAdapter.SetBeamPushUris()`Android 将切换备用传输机制 （例如蓝牙） 之间以获得快速的传输速度。

 <a name="Network_Services_Discovery" />


#### <a name="network-services-discovery"></a>网络服务发现

Android 4.1 包含新的 API 的多路广播基于 DNS 的服务发现的。
这允许应用程序检测并通过 Wi-fi 连接到其他设备，例如打印机、 相机和媒体设备。 这些新的 API 位于`Android.Net.Nsd`包。

若要创建的服务，可供其他服务，`NsdServiceInfo`类用于创建将定义服务的属性的对象。 此对象然后提供给`NsdManager.RegisterService()`的实现以及`NsdManager.ResolveListener`。 实现`NsdManager.ResolveListener`用于通知的成功注册并撤消服务注册。

若要发现的网络和实现上的服务`Nsd.DiscoveryListener`传递给`NsdManager.discoverServices()`。

 <a name="Network_Usage" />


#### <a name="network-usage"></a>网络使用情况

新方法，`ConnectivityManager.IsActiveNetworkMetered`允许检查它是否连接到按流量计费的网络设备。 此方法可以用于帮助管理通过准确地告知用户可能昂贵的费用数据操作的数据使用情况。

 <a name="WiFi_Direct_Service_Discovery" />


#### <a name="wifi-direct-service-discovery"></a>WiFi Direct 服务发现

`WifiP2pManager`支持 Android 4.0 中引入了类*zeroconf*。 Zeroconf （零配置网络） 是一组技术，允许设备 （计算机、 打印机、 电话） 以自动连接到网络的人工网络运营商或特殊配置服务器的干预。

在果冻 Bean`WifiP2pManager`可以发现附近的设备使用*Bonjour*或*Upnp*。 Bonjour 是 zeroconf Apple 的实现。 Upnp 是一组的还支持 zeroconf 的网络协议。 以下方法添加到`WiFiP2pManager`以支持的 Wi-fi 服务发现：

-   `AddLocalService()` – 使用此方法发现的对等方通过 Wi-fi 宣布作为服务的应用程序。
-   `AddServiceRequest(` ) – 此方法是将服务发现请求发送到框架。 它用于初始化 Wi-fi 服务发现。
-   `SetDnsSdResponseListeners()` – 此方法用于注册在接收从 Bonjour 对发现请求的响应要调用的回调。
-   `SetUpnpServiceResponseListener()` – 此方法用于注册在接收到响应发现请求 Upnp 要调用的回调。


 <a name="Content_Providers" />


### <a name="content-providers"></a>内容提供商

`ContentResolver`类已收到新方法， `AcquireUnstableContentProvider`。 此方法允许应用程序获取"不稳定"的内容提供程序。 通常情况下，当应用程序获取内容提供程序，并且该内容提供程序发生崩溃，因此将应用程序。 用此方法调用，如果内容提供程序崩溃时将不崩溃应用程序。 相反，`Android.OS.DeadObjectionException`将引发从上的内容提供程序的调用以通知的应用程序的内容提供程序是否已消失。 与从其他应用程序的内容提供程序交互时，"不稳定"的内容提供程序非常有用，它是不太可能错误的代码，从另一个应用程序将会影响另一个应用程序。

 <a name="Copy_and_Paste_With_Intents" />


### <a name="copy-and-paste-with-intents"></a>复制和粘贴与意向

`Intent`类现在可以`ClipData`对象与之通过关联`Intent.ClipData`属性。 此方法为额外的数据从剪贴板允许使用意向传输。 实例`ClipData`可以包含一个或多`ClipData.Item`。 `ClipData.Item`是以下类型的项：

-   **文本**– 这是任何文本，任一 HTML 字符串或其格式支持内置的 Android 样式的任何字符串跨越。
-  **意向**– 任何`Intent`对象。
-   **Uri** – 这可以是任何 URI，例如 HTTP 书签或内容提供程序的 URI。


 <a name="Isolated_Services" />


### <a name="isolated-services"></a>隔离的服务

隔离的服务是在其自己的特殊进程下运行和它自己的没有权限的服务。 仅与服务进行通信时启动该服务并通过服务 API 将绑定到它。 可以通过将属性设置声明作为独立服务`IsolatedProcess="true"`中`ServiceAttribute`装饰服务类。

 <a name="Media" />


### <a name="media"></a>媒体

新`Android.Media.MediaCodec`类提供低级别的媒体编解码器到 API。 应用程序可以查询系统以了解哪些较低级别的编解码器正在设备上可用。

新`Android.Media.Audiofx.AudioEffect`添加了子类以支持其他音频捕获音频上预先处理：

-   `Android.Media.Audiofx.AcousticEchoCanceler` – 此类用于预处理音频以移除远程方从捕获的音频信号的信号。 例如，从语音通信应用程序中删除 echo。
-   `Android.Media.Audiofx.AutomaticGainControl` – 此类用于规范化捕获的信号通过提高或降低输入的信号，以便输出信号为常数。
-   `Android.Media.Audiofx.NoiseSuppressor` — 此类将从捕获信号删除背景噪音。


并非所有设备将都支持这些效果。 该方法`AudioEffect.IsAvailable`应该调用应用程序以在运行应用程序的设备上是否受支持音频效果问题。

`MediaPlayer`类现在支持 gapless playback 与`SetNextMediaPlayer()`方法。 此新方法指定下一步 MediaPlayer 启动时的当前的媒体播放器完成其播放。

以下新类提供用于选择其中，将播放媒体的标准机制和 UI:

-   `MediaRouter` — 此类允许应用程序控制从外部发言人到设备或其他设备的媒体通道的路由。
-   `MediaRouterActionProvider` 和`MediaRouteButton`– 这些类帮助提供一致的用户界面，用于选择并播放媒体。


 <a name="Notifications" />


### <a name="notifications"></a>通知

Android 4.1 允许应用程序更多灵活性和控件的显示通知。 应用程序现在可以向用户显示更大、 更通知。 新方法，`NotificationBuilder.SetStyle()`新三个新的样式之一，即可在通知上设置：

-   `Notification.BigPictureStyle` – 这是将生成通知，其中将包含映像的一个帮助器类。 下图显示大图像包含的通知的示例：


 [ ![BigPictureStyle 通知的示例屏幕快照](jelly-bean-images/image2.png)](jelly-bean-images/image2.png)

-   `Notification.BigTextStyle` – 这是将生成将具有多行文本，如电子邮件通知的帮助器类。 可以在下面的屏幕截图中看到举例说明此新的通知样式：


 [ ![BigTextStyle 通知的示例屏幕快照](jelly-bean-images/image3.png)](jelly-bean-images/image3.png)

-   `Notification.InboxStyle` – 这是将生成包含一个字符串，例如，从电子邮件，代码段的列表的通知，此屏幕截图中所示的帮助器类：


 [ ![Notification.InboxStyle 通知的示例屏幕快照](jelly-bean-images/image4.png)](jelly-bean-images/image4.png)

它是可以将最多两个操作按钮添加底部的通知消息，通知使用的全角或更大的样式时。
可以在以下屏幕截图中，操作按钮的可见底部的通知中看到举例说明：

 [ ![示例屏幕快照显示一条通知消息的下面的操作按钮](jelly-bean-images/image5.png)](jelly-bean-images/image5.png)

`Notification`类已收到允许开发人员指定一个通知的五个优先级级别的新常量。 可以对通知使用设置这些`Priority`属性。

 <a name="Permissions" />


### <a name="permissions"></a>权限

已添加以下新权限：

-   `READ_EXTERNAL_STORAGE` -应用程序需要只读的访问到外部存储。 所有应用程序当前默认情况下，具有读取访问权限，但将来版本的 Android 会要求应用程序显式请求读取访问权限。
-   `READ_USER_DICTIONARY` -允许对用户的 word 字典的读取访问。
-   `READ_CALL_LOG` -允许应用程序通过读取调用日志，获取有关传入和传出调用的信息。
-   `WRITE_CALL_LOG` -允许应用程序在手机上写入调用日志。
-   `WRITE_USER_DICTIONARY` -允许应用程序要写入到用户的 word 字典。


要注意的重要更改`READ_EXTERNAL_STORAGE`– 当前 android 自动授予此权限。 将来版本的 Android 将需要的应用程序请求之前的此权限授予该权限。

 <a name="Summary" />


## <a name="summary"></a>摘要

这篇文章引入了一些新的 API 的 Android 4.1 (API 级别 16) 中可用。 它突出显示的一些动画和进行动画处理的活动，启动的更改，并引入了用于使用协议，如 Bonjour 或 UPnP 其他设备的网络发现的新 API。 API 的其他更改，重点讲解了，如能够剪切和粘贴数据意向，通过使用独立的服务或"不稳定的"内容提供商的能力。

这篇文章然后引入更新通知，发往，并讨论了部分已 Android 4.1 中引入的新权限


## <a name="related-links"></a>相关链接

- [时间动画示例 （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TimeAnimatorExample/)
- [Android 4.1 Api](http://developer.android.com/about/versions/android-4.1.html)
- [任务和后堆栈](http://developer.android.com/guide/components/tasks-and-back-stack.html)
- [与上一页和向上导航](http://developer.android.com/design/patterns/navigation.html)
