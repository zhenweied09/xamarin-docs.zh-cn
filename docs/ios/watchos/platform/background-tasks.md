---
title: watchOS Xamarin 中的后台任务
description: 本文档介绍如何使用 Xamarin，先了解一下类型的后台任务，请使用资源，实施后台任务、 计划、 最佳实践，和的详细信息中的 watchOS 后台任务。
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/13/2017
ms.openlocfilehash: 5ab53d4aea32cf41c492e286c18cbe85a619889a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792037"
---
# <a name="watchos-background-tasks-in-xamarin"></a>watchOS Xamarin 中的后台任务

与 watchOS 3，有，watch 应用可以使其信息保持最新的三个方面： 

- 使用多个新的后台任务之一。 
- 具有一个其复杂性手表表盘 （授予它更长时间来更新）。 
- 有新的停靠到应用程序的用户 pin (其中其保留在内存和频繁地更新)。 

## <a name="keeping-an-app-up-to-date"></a>使应用程序保持最新

在讨论之前所有开发人员可以保持 watchOS 应用的数据和用户界面当前和已更新的方式，本节将看一看一组典型的使用模式和用户可能在其 iPhone 和全天基于其 Apple Watch 之间进行移动 时间和活动他们当前执行的操作 （如驱动）。

请参见以下示例：

[![](background-tasks-images/update00.png "用户可能在其 iPhone 和全天其 Apple Watch 之间进行移动")](background-tasks-images/update00.png#lightbox)

1. 在上午杯咖啡，行中等待时用户几分钟内浏览其 iPhone 上的当前消息。
2. 在离开咖啡店之前, 它们快速检查其手表表盘上一个问题与天气。
3. 在之前午餐，它们使用地图应用在 iPhone 上查找附近的餐馆和预订保留以满足客户端。
4. 在旅行到餐馆，它们将看到一条通知，以及的快速浏览其 Apple Watch 上，他们知道后期运行其午餐约会。
5. 在晚上，它们使用该地图应用在 iPhone 上主页驱动之前检查流量。
6. 转家庭，他们收到要求他们其 Apple Watch 上的 iMessage 通知，以拾取某些牛奶时，它们使用快速答复功能将发送响应"确定"。

由于"快速浏览"（不超过三秒） 性质如何用户希望通常存在使用 Apple Watch 应用程序，没有足够的时间让应用程序以提取所需的信息，并将向用户显示之前更新其 UI。

使用新的 Api Apple 包含 watchOS 3，在应用程序可以计划为_后台刷新_和之前的用户请求它已准备就绪所需的信息。 执行天气复杂性上文所述的示例：

[![](background-tasks-images/update01.png "下面举例说明天气复杂性")](background-tasks-images/update01.png#lightbox)

1. 要唤醒系统在特定时间的应用程序计划。 
2. 此应用程序，它将需要生成更新的信息。
3. 应用程序将重新生成其用户界面，以反映新的数据。
4. 当用户在应用程序的复杂性向看时，它无需等待的更新用户的情况下具有最新信息。

如上图所示，watchOS 系统中唤醒使用一个或多个任务，其中它具有一个非常有限的可用池的应用：

[![](background-tasks-images/update02.png "WatchOS 系统中唤醒使用一个或多个任务的应用")](background-tasks-images/update02.png#lightbox)

Apple 建议此任务的最有效地利用 （因为它是此类限制的应用到资源） 通过在按住放到它上面，直到应用程序已完成更新本身的过程。

系统提供了这些任务通过调用新`HandleBackgroundTasks`方法`WKExtensionDelegate`委托。 例如：

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeyWatch.MonkeySeeExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Constructors
        public ExtensionDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
        {
            // Handle background request here
            ...
        }
        #endregion
    }
}
```

当应用程序完成给定的任务时，则该层会返回到系统来标记已完成：

[![](background-tasks-images/update03.png "该任务返回到系统来标记已完成")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>新的后台任务

watchOS 3 引入了应用程序可用于更新用户需要后如打开应用程序中，确保它具有内容其信息的多个后台任务：

- **后台应用程序刷新**- [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask)任务允许应用程序以更新其状态在后台。 通常，这将包括另一个任务，例如从 internet 使用下载的新内容[NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)。
- **后台快照刷新**- [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)任务允许应用程序以更新其内容和 UI 之前系统采用将用于填充停靠的快照。
- **后台监视连接**- [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask)从配对 iPhone 接收背景数据时，应用程序启动的任务。
- **后台 URL 会话**- [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask)后台传输要求获得授权或完成 （成功或错误） 时，为应用程序启动的任务。

在下面各部分详细介绍了这些任务。

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

`WKApplicationRefreshBackgroundTask`是一种泛型的任务，可以按计划安装应用程序在未来某个日期唤醒：

[![](background-tasks-images/update04.png "在未来某个日期唤醒 WKApplicationRefreshBackgroundTask")](background-tasks-images/update04.png#lightbox)

中的任务运行时，应用程序可以执行复杂性时间线的任何类型的本地处理，如更新或提取与某些所需的数据`NSUrlSession`。


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

系统将发送`WKURLSessionRefreshBackgroundTask`在正在下载和准备好可通过应用程序处理完数据后：

[![](background-tasks-images/update05.png "下载完成后数据 WKURLSessionRefreshBackgroundTask")](background-tasks-images/update05.png#lightbox)

运行时在后台下载数据不会保留应用程序。 相反，应用程序计划请求数据，则它挂起的期间和系统处理的数据，仅在下载完毕后 reawakening 应用程序的下载。

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

在 watchOS 3，Apple 添加了的停靠用户可以固定其最喜欢的应用并快速访问它们。 当用户按端按钮下 Apple Watch 上时，将显示固定的应用快照的库。 左或向右以查找所需的应用，然后点击应用程序以启动该快照将替换为正在运行的应用的接口，用户可以往下轻扫。

[![](background-tasks-images/update06.png "将替换为正在运行的应用程序接口的快照")](background-tasks-images/update06.png#lightbox)

系统定期将快照应用程序的 UI (通过发送`WKSnapshotRefreshBackgroundTask`)，并使用这些快照以填充停靠。 watchOS 使应用程序可以拍摄此快照之前更新其内容和 UI。

快照是在 watchOS 3 中非常重要，因为它们充当应用程序的预览和启动映像。 如果用户结算上的应用中停靠，它将展开到全屏显示、 输入前台并开始运行，因此它是命令性是最新的快照：

[![](background-tasks-images/update07.png "如果用户结算上停靠在应用程序，它将扩展到全屏显示")](background-tasks-images/update07.png#lightbox)

同样，则系统将发出`WKSnapshotRefreshBackgroundTask`，以便应用程序可以 （通过更新数据和 UI） 来准备前拍摄快照：

[![](background-tasks-images/update08.png "应用程序可以通过更新数据和 UI 拍摄快照之前准备")](background-tasks-images/update08.png#lightbox)

当应用程序将标记`WKSnapshotRefreshBackgroundTask`完成后，系统会自动使应用程序的 UI 的快照。

> [!IMPORTANT]
> 务必要始终排定` WKSnapshotRefreshBackgroundTask`后应用程序接收到新的数据，并更新其用户界面或用户将无法看到已修改的信息。




此外，当用户从应用程序接收通知，并点击以将应用程序置于前台，需要为最新，因为此组充当启动屏幕快照：

[![](background-tasks-images/update09.png "用户从应用程序接收通知，并点击以将应用程序置于前台")](background-tasks-images/update09.png#lightbox)

如果因为用户具有 watchOS 应用程序与之交互，它已被一个多小时，它将能够返回到其默认状态。 默认状态不同的含义到不同的应用程序，并根据应用程序的设计，它可能不会默认状态根本。

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

WatchOS 3，Apple 具有与集成，监视连接通过新后台刷新 API `WKWatchConnectivityRefreshBackgroundTask`。 使用此新功能，在 iPhone 应用可以全新将数据传递到其监视应用程序对应的方法时在后台运行 watchOS 应用：

[![](background-tasks-images/update10.png "在后台运行 watchOS 应用时 iPhone 应用可以将新数据传递到其监视应用程序对应的方法")](background-tasks-images/update10.png#lightbox)

启动复杂性推送时，应用程序上下文，发送的文件或更新 iphone 版应用中的用户信息将唤醒在后台 Apple Watch 应用。

当通过唤醒 watch 应用`WKWatchConnectivityRefreshBackgroundTask`它将需要使用标准的 API 方法来接收 iphone 版应用中的数据。

[![](background-tasks-images/update11.png "WKWatchConnectivityRefreshBackgroundTask 数据流")](background-tasks-images/update11.png#lightbox)

1. 确保会话已经激活。
2. 监视新`HasContentPending`属性只要值是`true`，应用程序仍然具有要处理的数据。 如之前，应用程序应保留到任务，直到它已完成处理的所有数据。
3. 没有要处理的更多数据时 (`HasContentPending = false`)，将标记完成以使其返回到系统的任务。 如果不这样做，则将耗尽应用程序的分配的后台运行时导致崩溃报告。

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>后台 API 生命周期

将所有新的后台任务 API 的部分放在一起，一组典型的交互将如下所示：

[![](background-tasks-images/update12.png "后台 API 生命周期")](background-tasks-images/update12.png#lightbox)

1. 首先，watchOS 应用计划后台任务以将而唤醒为某些点在将来。
2. 系统会将其唤醒应用并将其发送任务。
3. 应用程序处理任务完成任何工作是必需的。
4. 因此的处理该任务，该应用程序可能需要安排在将来，完成更多的工作，如下载更多的内容使用任务的更多背景`NSUrlSession`。
5. 该应用将标记完成的任务，并将其返回给系统。

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>负责任地使用资源

这一点至关重要，watchOS 应用行为的行为负责任地利用这个生态系统中通过限制其消耗系统的共享资源。

看一看以下方案：

[![](background-tasks-images/update13.png "WatchOS 应用限制其消耗系统的共享资源")](background-tasks-images/update13.png#lightbox)

1. 用户在下午 1:00 启动 watchOS 应用。
2. 应用程序计划任务以唤醒并在下午 2:00 一小时内下载新内容。
3. 下午 1:50 用户重新打开应用，从而让以这次更新其数据和 UI。
4. 而不是让任务唤醒应用程序在 10 分钟后，应用程序应重新计划任务更高版本在下午 2:50 运行一小时。

不同每个应用时，Apple 提供的建议查找模式的使用情况，如表所列的更高版本，以帮助节省系统资源。

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>实施后台任务

对于示例，本文档将使用假 MonkeySoccer 体育应用程序向用户报告足球评分。 

看一看下面的典型使用方案：

[![](background-tasks-images/update14.png "典型使用方案")](background-tasks-images/update14.png#lightbox)

用户的收藏足球团队播放时从下午 7:00 到下午 9:00 大匹配，因此应用程序应该会用户会定期检查分数和它决定在 30 分钟的更新时间间隔。

1. 在用户打开应用程序和 30 分钟后计划后台更新的任务。 使用后台 API，只有一种类型的后台任务在给定时间运行。
2. 应用程序接收任务并更新其数据和 UI 中，然后为另一个计划后台任务 30 分钟后。 很重要，开发人员会记住计划另一个后台任务，或应用程序将永远不会被重新恢复以获取更多更新。
3. 同样，应用程序接收任务并更新其数据，更新其 UI，计划另一个后台任务 30 分钟后。
4. 再次重复相同的进程。
5. 任务接收到的最后一个背景和应用程序再次更新其数据和 UI。 由于这是它不会为新的后台刷新计划的最终分数。 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>计划后台更新

给定上面的方案，MonkeySoccer 应用程序可以使用下面的代码来为后台更新计划：

```csharp
private void ScheduleNextBackgroundUpdate ()
{
    // Create a fire date 30 minutes into the future
    var fireDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

    // Create 
    var userInfo = new NSMutableDictionary ();
    userInfo.Add (new NSString ("LastActiveDate"), NSDate.FromTimeIntervalSinceNow(0));
    userInfo.Add (new NSString ("Reason"), new NSString ("UpdateScore"));

    // Schedule for update
    WKExtension.SharedExtension.ScheduleBackgroundRefresh (fireDate, userInfo, (error) => {
        // Was the Task successfully scheduled?
        if (error == null) {
            // Yes, handle if needed
        } else {
            // No, report error
        }
    });
}
```

它创建一个新`NSDate`30 分钟的将来在应用程序想要将恢复并创建`NSMutableDictionary`来保存请求的任务的详细信息。 `ScheduleBackgroundRefresh`方法`SharedExtension`用于请求在任务安排。

系统将返回`NSError`如果无法计划请求的任务。

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>处理更新

接下来，要进一步查看在 5 分钟窗口中显示更新评分所需的步骤：

[![](background-tasks-images/update15.png "显示更新评分所需的步骤 5 分钟窗口")](background-tasks-images/update15.png#lightbox)

1. 下午 7:30:02 应用唤醒，系统并给定更新后台任务。 其最重要的是要从服务器获取最新的分数。 请参阅[计划 NSUrlSession](#Scheduling-a-NSUrlSession)下面。
2. 7:30:05 应用完成原始任务，则系统会将应用程序进入睡眠状态，并继续下载在后台请求的数据。
3. 当系统完成下载时，它会创建用于唤醒应用程序，以便它可以处理的下载的信息的新任务。 请参阅[后台任务的处理](#Handling-Background-Tasks)和[处理下载完成](#Handling-the-Download-Completing)下面。 
4. 该应用将保存更新的信息，并将标记完成的任务。 但是，Apple 提供的建议计划快照任务，用于处理该过程，开发人员可能想要在此时，更新应用程序的用户界面。 请参阅[计划快照更新](#Scheduling-a-Snapshot-Update)下面。
5. 应用程序接收快照任务，更新其用户界面并标记完成的任务。 请参阅[处理快照更新](#Handling-a-Snapshot-Update)下面。

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>计划 NSUrlSession

下面的代码可以用于计划下载最新的评分：

```csharp
private void ScheduleURLUpdateSession ()
{
    // Create new configuration
    var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.example.urlsession");

    // Create new session
    var backgroundSession = NSUrlSession.FromConfiguration (configuration);

    // Create and start download task
    var downloadTask = backgroundSession.CreateDownloadTask (new NSUrl ("https://example.com/gamexxx/currentScores.json"));
    downloadTask.Resume ();
}
```

它将配置并创建一个新`NSUrlSession`，然后使用该会话创建新的下载任务使用`CreateDownloadTask`方法。 它调用`Resume`的下载任务以便启动会话的方法。

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>处理后台任务

通过重写`HandleBackgroundTasks`方法`WKExtensionDelegate`，应用程序可以处理传入的后台任务：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
        #endregion

        ...
        
        #region Public Methods
        public void CompleteTask (WKRefreshBackgroundTask task)
        {
            // Mark the task completed and remove from the collection
            task.SetTaskCompleted ();
            PendingTasks.Remove (task);
        }
        #endregion 

        #region Override Methods
        public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
        {
            // Handle background request
            foreach (WKRefreshBackgroundTask task in backgroundTasks) {
                // Is this a background session task?
                var urlTask = task as WKUrlSessionRefreshBackgroundTask;
                if (urlTask != null) {
                    // Create new configuration
                    var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

                    // Create new session
                    var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

                    // Keep track of all pending tasks
                    PendingTasks.Add (task);
                } else {
                    // Ensure that all tasks are completed
                    task.SetTaskCompleted ();
                }
            }
        }
        #endregion
        
        ...
    }
}
```

`HandleBackgroundTasks`方法会循环访问的所有任务系统已发送应用程序 (在`backgroundTasks`) 搜索`WKUrlSessionRefreshBackgroundTask`。 如果找到一个对象，它将重新加入会话，将附加`NSUrlSessionDownloadDelegate`来处理下载完成 (请参阅[处理下载完成](#Handling-the-Download-Completing)下面):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

完成之前都将通过将其添加到集合，它会在任务上使句柄：

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

所有任务发送到应用需要为不当前正在处理的任何任务完成标记为完成：

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>处理下载完成

MonkeySoccer 应用程序使用以下`NSUrlSessionDownloadDelegate`委托以处理下载完成并处理请求的数据：

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
    public class BackgroundSessionDelegate : NSUrlSessionDownloadDelegate
    {
        #region Computed Properties
        public ExtensionDelegate WatchExtensionDelegate { get; set; }

        public WKRefreshBackgroundTask Task { get; set; }
        #endregion

        #region Constructors
        public BackgroundSessionDelegate (ExtensionDelegate extensionDelegate, WKRefreshBackgroundTask task)
        {
            // Initialize
            this.WatchExtensionDelegate = extensionDelegate;
            this.Task = task;
        }
        #endregion

        #region Override Methods
        public override void DidFinishDownloading (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, NSUrl location)
        {
            // Handle the downloaded data
            ...

            // Mark the task completed
            WatchExtensionDelegate.CompleteTask (Task);

        }
        #endregion
    }
}
```

在初始化时，它使句柄同时向`ExtensionDelegate`和`WKRefreshBackgroundTask`，生成它。 它将重写`DidFinishDownloading`方法以处理下载完成。 然后使用`CompleteTask`方法`ExtensionDelegate`以通知任务它已完成和从集合中的挂起任务删除。 请参阅[后台任务的处理](#Handling-Background-Tasks)上面。

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>计划快照更新

下面的代码可以用于计划快照任务以更新 UI 具有最新的评分：

```csharp
private void ScheduleSnapshotUpdate ()
{
    // Create a fire date of now
    var fireDate = NSDate.FromTimeIntervalSinceNow (0);

    // Create user info dictionary
    var userInfo = new NSMutableDictionary ();
    userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
    userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

    // Schedule for update
    WKExtension.SharedExtension.ScheduleSnapshotRefresh (fireDate, userInfo, (error) => {
        // Was the Task successfully scheduled?
        if (error == null) {
            // Yes, handle if needed
        } else {
            // No, report error
        }
    });
}
```

就像`ScheduleURLUpdateSession`上面的方法，它创建一个新`NSDate`针对该应用时想要将恢复和创建`NSMutableDictionary`来保存请求的任务的详细信息。 `ScheduleSnapshotRefresh`方法`SharedExtension`用于请求在任务安排。

系统将返回`NSError`如果无法计划请求的任务。

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>处理快照更新

若要处理快照任务`HandleBackgroundTasks`方法 (请参阅[处理后台任务](#Handling-Background-Tasks)上面) 修改为如下所示：

```csharp
public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
{
    // Handle background request
    foreach (WKRefreshBackgroundTask task in backgroundTasks) {
        // Take action based on task type
        if (task is WKUrlSessionRefreshBackgroundTask) {
            var urlTask = task as WKUrlSessionRefreshBackgroundTask;

            // Create new configuration
            var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

            // Create new session
            var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

            // Keep track of all pending tasks
            PendingTasks.Add (task);
        } else if (task is WKSnapshotRefreshBackgroundTask) {
            var snapshotTask = task as WKSnapshotRefreshBackgroundTask;

            // Update UI
            ...

            // Create a expiration date 30 minutes into the future
            var expirationDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

            // Create user info dictionary
            var userInfo = new NSMutableDictionary ();
            userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
            userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

            // Mark task complete
            snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
        } else {
            // Ensure that all tasks are completed
            task.SetTaskCompleted ();
        }
    }
}
```

方法正在处理的任务的类型的测试。 如果它是`WKSnapshotRefreshBackgroundTask`它会获得对任务的访问：

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

该方法将更新用户界面，然后创建`NSDate`告诉系统时则快照将过时。 它将创建`NSMutableDictionary`使用来描述新的快照和标记快照任务已完成，但此信息的用户信息：

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

此外，它还会通知快照任务应用程序未返回到 （中的第一个参数） 的默认状态。 具有默认状态没有概念应用程序应始终将此属性设置为`true`。

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>有效地工作

3 个后台任务中所花费的 MonkeySoccer 应用通过有效地工作，并使用新 watchOS 更新其评分的 5 分钟窗口上面的示例所示，应用程序是仅 active 总量为 15 秒： 

[![](background-tasks-images/update16.png "应用程序仅处于活动状态 15 秒总数")](background-tasks-images/update16.png#lightbox)

这会降低应用程序将对可用的 Apple Watch 资源和的电池使用时间的影响，并允许应用程序以更好地符合在手表上运行的其他应用。

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>计划的工作原理

在前台 watchOS 3 应用程序时，它始终计划运行，并可以执行如更新数据所需的处理任何类型或重绘其 UI。 当应用程序移动到后台时，系统通常挂起，并且所有运行时操作暂停。 

在后台应用程序时，它可能成为受系统快速运行特定任务目标。 因此，在 watchOS 2，系统可能会暂时唤醒的后台应用程序来执行诸如处理长查看通知或更新应用程序的复杂性。 在 watchOS 3，有几种应用程序可以在后台运行的新方法。

在后台应用程序时，系统会有几个限制：

- 它仅提供几秒钟来完成任何给定的任务。 传递的时间量不仅还多少 CPU 处理能力的应用程序使用派生此限制，系统将考虑。
- 超出其限制任何应用程序将终止与以下的错误代码：
    - **CPU** -0xc51bad01
    - **时间**-0xc51bad02
- 系统将会根据它要求应用程序以执行后台任务的类型的不同限制。 例如，`WKApplicationRefreshBackgroundTask`和`WKURLSessionRefreshBackgroundTask`任务获得的较长的运行时高于其他类型的后台任务。

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>复杂性和应用程序更新

除了新后台任务 Apple 添加到 watchOS 3，watchOS 应用的复杂性可以影响应用程序如何以及何时接收后台更新。

复杂性是提供一目了然的有用信息的小型可视元素。 具体取决于选择手表表盘，用户已具有可由 watchOS 3 中的监视应用提供的一个或多个复杂性手表表盘的自定义功能。

如果用户包括在其手表表盘上的应用程序的复杂性之一，它使应用程序更新以下好处：

- 它会导致系统以使应用程序保持在准备就绪，可以启动状态，其中它将尝试以启动应用程序在后台，将，将其保留在内存和向提供了它额外时间更新。
- 复杂性被保证每日至少 50 推送更新。

开发人员应始终致力于使创建引人注目的复杂性，为其应用程序以诱使用户将它们添加到其手表表盘 for 上面列出的原因。

在 watchOS 2，复杂性是应用程序收到在后台运行时的主要方法。 在 watchOS 3，复杂性应用程序将仍确保以接收每小时的多个更新，但是，它可以使用`WKExtensions`来请求更多的运行时，更新其复杂性。

看一看下面的代码用于更新连接在 iPhone 应用中出现的错误：

```csharp
using System;
using WatchConnectivity;
using UIKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
...

private void UpdateComplication ()
{

    // Get session and the number of remaining transfers
    var session = WCSession.DefaultSession;
    var transfers = session.RemainingComplicationUserInfoTransfers;

    // Create user info dictionary
    var iconattrs = new Dictionary<NSString, NSObject>
        {
            {new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0)},
            {new NSString ("reason"), new NSString ("UpdateScore")}
        };

    var userInfo = NSDictionary<NSString, NSObject>.FromObjectsAndKeys (iconattrs.Values.ToArray (), iconattrs.Keys.ToArray ());

    // Take action based on the number of transfers left
    if (transfers < 1) {
        // No transfers left, either attempt to send or inform
        // user of situation.
        ...
    } else if (transfers < 11) {
        // Running low on transfers, only send on important updates
        // else conserve for a significant change.
        ...
    } else {
        // Send data
        session.TransferCurrentComplicationUserInfo (userInfo);
    }
}
```

它使用`RemainingComplicationUserInfoTransfers`属性`WCSession`若要查看高优先级的数量传输应用程序已离开一天，然后基于该数量的采取行动。 如果应用程序开始在传输上低效运行，它可以发送较少更新拖延并仅在显著的更改时发送信息。

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>计划和停靠

在 watchOS 3，Apple 添加了的停靠用户可以固定其最喜欢的应用并快速访问它们。 当用户按端按钮下 Apple Watch 上时，将显示固定的应用快照的库。 左或向右以查找所需的应用，然后点击应用程序以启动该快照将替换为正在运行的应用的接口，用户可以往下轻扫。

[![](background-tasks-images/dock01.png "停靠")](background-tasks-images/dock01.png#lightbox)

系统定期将快照应用程序的 UI，并使用这些快照来填充文档。watchOS 使应用程序可以拍摄此快照之前更新其内容和 UI。

已固定到停靠的应用可以期待以下结果：

- 他们将收到一个每小时更新的最小值。 这包括应用程序刷新任务和快照任务。
- 所有停靠在应用之间分发更新预算。 因此的较少的应用用户已固定，每个应用程序将收到的更多潜在更新。
- 将在内存中保持应用程序，以便当选择从停靠时，应用程序将快速恢复。

最后一个用户运行的应用程序将被视为_最近使用过_应用并将占用中停靠的最后一个槽。 从这里，存在用户可以选择将其固定永久停靠。 像任何其他收藏应用程序用户具有已固定到停靠，将被视为最近使用过。

> [!IMPORTANT]
> 该任务仅添加到主页屏幕的应用将不会获得任何定期计划。 若要接收定期计划和背景更新，应用程序_必须_添加到停靠。

如本文前面所述，快照是在 watchOS 3 中非常重要，因为它们充当应用程序的预览和启动映像。 如果用户结算上的应用中停靠，它将展开到全屏显示、 输入前台并开始运行，因此它是命令性是最新的快照。

可能有系统决定它需要的应用程序的 UI 全新快照的时间。 在此情况下，快照请求将不会抵消应用程序的运行时消耗。 以下将触发系统快照请求：

- 复杂性时间线更新。
- 与应用程序的通知的用户交互。
- 从前台切换到背景状态。
- 一个小时后处于后台状态，因此应用程序可以返回到默认状态。
- 当 watchOS 首次启动。

<a name="Best-Practices" />

## <a name="best-practices"></a>最佳做法 

Apple 提供的功能使用后台任务时，则建议下列最佳方案：

- 按应用程序需要更新的频率计划。 每次应用程序运行时它应重新评估其未来的需求并调整根据需要此计划。
- 如果系统发送后台刷新任务和应用程序不需要更新，工作推迟到直到实际需要进行更新时。
- 请考虑到应用程序可用的所有运行时机会：
    - 停靠和前景激活。
    - 发送通知。
    - 复杂性更新。
    - 后台刷新过程中。
- 使用`ScheduleBackgroundRefresh`通用后台运行时，如：
    - 轮询系统中的信息。
    - 计划将来`NSURLSessions`请求后台数据。 
    - 未知的时间进行的转换。
    - 触发复杂性更新。

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>快照最佳做法

当使用快照更新时，Apple 将使以下建议：

- 使快照仅在需要时，例如，一个重要的内容变化时无效。
- 避免高频率快照失效。 例如，计时器应用程序不应在每秒更新快照，它应仅为完成时计时器已结束。

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>应用程序数据流

Apple 提供用于处理数据流以下建议：

[![](background-tasks-images/update17.png "应用程序数据流关系图")](background-tasks-images/update17.png#lightbox)

外部事件 （如监视连接） 中唤醒应用程序。 这将强制应用以更新其数据模型 （表示应用程序的当前状态）。 因此数据模型更改的应用程序将需要更新其复杂性，请求新的快照时，可能是启动后台`NSURLSession`来请求更多的数据，并安排进一步后台刷新。

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>应用生命周期

由于停靠和要固定到其中，将更多的应用之间移动用户的 Apple 认为最喜爱的应用的功能更多时候，然后它们像使用 watchOS 2。 因此，应用程序应该已准备好处理此更改和前景色和背景状态之间快速移动。

Apple 具有以下建议：

- 确保应用完成尽可能快地进入前景激活任何后台任务。
- 确保以完成所有前景工作之前通过调用输入后台`NSProcessInfo.PerformExpiringActivity`。
- 当在 watchOS 模拟器中测试应用，无任务预算将会强制执行应用程序才能刷新尽可能正确测试功能所需。
- 始终在真实的 Apple Watch 硬件，以确保应用程序不在发布前其预算之后运行到 iTunes Connect 上进行测试。
- Apple 提供的建议在测试和调试的同时将 Apple Watch 保留在充电器。
- 确保 throughly 测试冷启动并恢复应用程序。
- 验证已完成应用程序的所有任务。
- 改变应用程序在要测试的最佳和最差停靠中固定数目例方案。

<a name="Summary" />

## <a name="summary"></a>总结

本文已覆盖 Apple 已 watchOS 以及如何使用它们来跟踪 watch 应用最新的增强功能。 首先，涵盖的所有新后台任务 Apple 都增加 watchOS 3 中。 然后，则覆盖背景 API 生命周期和如何在 Xamarin watchOS 应用程序中实施后台任务。 最后，它涵盖如何计划的工作原理，并提供的一些最佳做法。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
