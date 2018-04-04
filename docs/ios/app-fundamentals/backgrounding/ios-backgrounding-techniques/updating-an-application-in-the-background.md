---
title: 更新在后台应用程序
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 370d1cba71fa695e4e01dfb93241536a8df01b11
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="updating-an-application-in-the-background"></a>更新在后台应用程序

后台刷新是唤醒的应用程序已挂起或未运行，并使用新内容对其进行更新的过程。 iOS 提供刷新内容在后台的三个选项：

1.  *区域监视*和*重要位置更改服务*-位置识别 Api 触发器后台更新用户的位置根据的更改。 这些 Api 可以用于做出判断刷新非基于位置的 iOS 6 应用程序中的内容不可用的其他选项。
1.  *背景提取 (iOS 7 +)* -的临时方法刷新*非关键*更新的内容*经常*。
1.  *远程通知 (iOS 7 +)* -接收推送通知的应用程序可以使用通知触发后台内容刷新。 此方法可以用于使用更新*重要、 时间敏感型*更新的内容*偶尔*。


以下部分介绍这些选项的基础知识。

## <a name="region-monitoring-and-significant-location-changes"></a>区域监视和有效位置更改

iOS 提供两个位置识别 Api backgrounding 功能：

1.  *区域监视*是来与边界，区域设置和用户进入或退出区域时唤醒设备的过程。 区域是循环的并且可以属于不同的大小。 如果用户跨越区域边界，设备将唤醒对事件进行处理，通常通过触发通知或手动启动任务。 区域监视需要 GPS，并提高电池和数据使用情况。
1.  *重要位置更改服务*是一个更简单、 power 节省选项可用于与移动电话无线电的设备。 当设备切换单元格 towers，将通知侦听的重要位置更改的应用程序。 此服务可用于唤醒挂起或终止的应用程序，并为检查有在后台的新内容提供了机会。 除非与成对出现，后台活动仅限于大约 10 秒，[后台任务](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md)。


应用程序不需要位置`UIBackgroundMode`若要使用这些位置识别的 Api。 因为 iOS 不会跟踪更改用户的位置中唤醒设备时，可以运行的任务的类型，这些 Api 提供一种解决为更新在后台对 iOS 6 的内容。 *请记住，触发与基于位置的 Api 的后台更新将在设备资源上进行绘制，并且可能混淆不了解应用程序为什么需要对其位置的访问的用户*。 为后台处理未用过的位置 Api 的应用程序中实施区域监视或位置的重大更改时，请格外谨慎。

使用位置监视后台处理应用公开 iOS 6 中存在的缺陷： 如果应用程序的需求不适合背景需要类别，它具有有限的 backgrounding 选项。 两个新的 Api，随着*背景提取*和*远程通知*，iOS 7 （和更高版本） 提供更多应用程序的 backgrounding 机会。 接下来的两部分介绍这些新的 Api。

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>背景提取 (iOS 7 和更高版本)

在 iOS 6 中，输入前台的应用程序需要时间来加载新内容，简要他们已经了解的内容与提供的用户。 背景提取允许应用程序加载新数据*之前*用户启动应用程序，并为用户提供的最新内容。

若要实现背景提取，编辑*Info.plist*并检查**启用后台模式**和**背景提取**复选框：

 [![](updating-an-application-in-the-background-images/fetch.png "编辑 Info.plist 并选中启用后台模式和背景提取复选框")](updating-an-application-in-the-background-images/fetch.png#lightbox)

接下来，在`AppDelegate`，重写`FinishedLaunching`方法以设置的最小的提取间隔。 在此示例中，我们让决定通常提取新内容的方式的操作系统：

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

最后，通过重写中执行提取`PerformFetch`中的方法`AppDelegate`，并传入*完成处理程序*。 完成处理程序是一个委托，采用`UIBackgroundFetchResult`:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

当我们完毕后更新内容时，我们会让知道通过调用适当的状态的完成处理程序的操作系统。 iOS 提供完成处理程序状态的三个选项：

1.  `UIBackgroundFetchResult.NewData` -已提取新内容，并且已更新应用程序时调用。
1.  `UIBackgroundFetchResult.NoData` -当为新的内容提取已完成，但不会提供任何内容时调用。
1.  `UIBackgroundFetchResult.Failed` -用于错误处理，这称为提取无法完成。


使用背景提取应用程序可以进行调用以从后台更新 UI。 当用户打开应用程序时，则 UI 将最新并显示新内容。 这也将更新应用程序的应用程序切换器快照，以便用户可以看到该应用程序时有新内容。

> [!IMPORTANT]
> 一次`PerformFetch`是调用，则应用程序必须大约 30 秒开始下载新内容，并调用完成处理程序块。 如果这所用时间过长，则将终止应用程序。 请考虑使用背景提取与_后台传输服务_下载媒体文件或其他大型文件时。


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

在上面的示例代码中，我们让决定通常通过将最小的提取间隔设置为提取新内容的方式的 OS `BackgroundFetchIntervalMinimum`。 iOS 提取间隔提供三个选项：

1.  `BackgroundFetchIntervalNever` -告诉系统永远不会提取新内容。 用于关闭提取在某些情况下，例如当用户未登录。 提取间隔为默认值。 
1.  `BackgroundFetchIntervalMinimum` -，让系统通常来提取决定如何基于用户模式、 电池寿命、 数据使用情况和其他应用程序的需求。
1.  `BackgroundFetchIntervalCustom` -如果你知道应用程序的内容获取更新的频率，你可以指定"休眠"时间间隔后每个提取时，在此期间应用程序将无法从提取新内容。 向上的间隔后，系统将确定何时要获取其内容。


同时`BackgroundFetchIntervalMinimum`和`BackgroundFetchIntervalCustom`依赖于系统计划提取。 此间隔是动态的调整设备的需求，以及每个用户的习惯。 例如，如果一个用户签入的应用程序每天早上，并且另一个检查每隔一小时，iOS 将确保内容是最新两个用户每次打开应用程序。

应使用非关键内容经常更新的应用程序使用背景提取。 对于具有关键更新的应用程序，应使用远程通知。 远程通知基于后台提取，并共享相同的完成处理程序。 我们将深入探讨远程通知下一步。

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>远程通知 (iOS 7 和更高版本)

推送通知是从提供程序发送到通过设备的 JSON 消息*Apple 推送通知服务 (APNs)*。

IOS 6 中, 传入的推送通知告诉系统一些有趣的内容发生应用程序中的用户发出警报。 点击通知的通知超出了挂起或终止状态，提取应用程序和应用程序将开始更新内容。 iOS 7 （和更高版本） 扩展了普通的推送通知，从而使应用程序有机会更新内容在后台*之前*通知用户，以便用户可以打开应用程序，并显示新内容立即。

若要实现远程通知，编辑*Info.plist*并检查**启用后台模式**和**远程通知**复选框：

 [![](updating-an-application-in-the-background-images/remote.png "后台模式下设置为启用后台模式和远程通知")](updating-an-application-in-the-background-images/remote.png#lightbox)

接下来，设置`content-available`本身为 1 的推送通知的标志。 这允许应用程序确定在显示警报之前提取新内容：

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

在*AppDelegate*，重写`DidReceiveRemoteNotification`方法来检查可用内容的通知负载并调用相应的完成处理程序块：

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

远程通知应该用于对应用程序的功能至关重要的内容不太频繁更新。 有关远程通知的详细信息，请参阅 Xamarin[在 iOS 中的推送通知](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)指南。

> [!IMPORTANT]
> 由于远程通知中的更新机制基于后台提取，因此应用程序必须下载新的内容启动并在接收通知，30 秒内调用完成处理程序块或 iOS 将终止应用程序。 请考虑配对远程通知_后台传输服务_下载媒体文件或其他大型文件在后台时。


### <a name="silent-remote-notifications"></a>无提示的远程通知

远程通知是一种简单的方法，以通知应用程序的更新并将其启动提取新内容，不过还有一些我们不需要通知用户内容已更改的情况。 例如，如果用户标记为同步的文件，我们不需要每次文件更新时通知他们。 文件同步不令人惊讶的事件，也不需要用户立即关注。 用户只需预期要在打开时是最新的文件。

上面所示的情况下，对于 iOS 允许推送通知，若要以无提示方式-即，无需发送警报。 若要打开的常规到一个无提示通知，只需从通知负载删除警报：

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>速率限制

从开发人员的角度的正常和无提示通知的最大区别是无提示推送速率限制。 推送速率过高时，APNs 将延迟无提示推送的传递到设备。 这是为了确保应用程序，不会耗尽使用太多的无提示通知的设备资源。

但是，APNs 将使"直接返回"普通远程通知或保持活动状态的响应旁边的无提示通知。 由于正则通知不限制的速率，它们可以用于将存储上无提示通知从 APNs 推送到设备，如下图所示：

 [![](updating-an-application-in-the-background-images/silent.png "正则通知可用于将存储的无提示通知从 APNs 推送到设备，，此图所示")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> Apple 鼓励开发人员发送无提示的推送通知，每当应用程序要求，并让计划将其传递的 APNs。


在此部分中，我们已介绍了各种选项刷新内容在后台运行后台需要类别不适合的任务。 现在，让我们看到某些操作中的这些 Api。

 [下一步： 第 4 部分-iOS Backgrounding 演练](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
