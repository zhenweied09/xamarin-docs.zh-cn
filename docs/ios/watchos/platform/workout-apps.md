---
title: "测验应用"
description: "本文介绍如何增强功能 Apple 已针对锻炼应用 watchOS 3 以及如何在 Xamarin 实现它们。"
ms.topic: article
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: f375f58215fbef13298e31b03b969c9388d2bfa2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="workout-apps"></a>测验应用

_本文介绍如何增强功能 Apple 已针对锻炼应用 watchOS 3 以及如何在 Xamarin 实现它们。_


新与 watchOS 3，测验相关应用程序能够在 Apple Watch 上在后台运行，并获得对 HealthKit 数据的访问。 其父 iOS 10 基于应用程序还能够启动 watchOS 3 基于应用程序无需用户干预。

将详细介绍以下主题：

## <a name="about-workout-apps"></a>有关锻炼应用

适用性和测验的应用的用户可以是高度专用，花几个小时，向其运行状况和适用性的目标的一天。 因此，他们期望响应，能够轻松使用准确地收集和显示数据，并与 Apple 运行状况无缝集成的应用。

设计良好的适用性或锻炼应用程序可帮助用户图表来访问其适用性目标其活动。 通过使用 Apple Watch，适用性和锻炼应用能够即时访问的核心速率卡路里刻录和活动检测。

[ ![](workout-apps-images/workout01.png "适用性和锻炼应用示例")](workout-apps-images/workout01.png)

到 watchOS 3，新_后台运行_提供锻炼相关应用程序能够在 Apple Watch 上在后台运行，并获取对 HealthKit 数据访问。

本文档将引入的后台运行的功能，涵盖锻炼应用生命周期并显示测验应用可参与用户的_活动环_Apple Watch 上。

## <a name="about-workout-sessions"></a>有关锻炼会话

每个锻炼应用的核心是_锻炼会话_(`HKWorkoutSession`)，用户可以启动和停止。 测验会话 API 轻松地实现，并提供以下几个好处向测验应用程序，如：

- 运动和卡路里刻录基于活动类型的检测。
- 对用户的活动环的自动贡献。
- 在会话中，应用程序将自动显示每当用户中唤醒设备 （不管是通过引发其静电腕带或与 Apple Watch 交互）。

## <a name="about-background-running"></a>有关后台运行

如上面所述，使用 watchOS 3 锻炼应用程序可以设置要在后台运行。 使用后台运行的锻炼应用可以在后台运行时处理从 Apple Watch 的传感器数据。 例如，应用程序可以继续监视用户的核心速率，即使它不会再显示在屏幕上也是如此。

后台运行还能够在处于活动状态的锻炼会话，例如发送一个 haptic 警报以通知用户他们的当前进度期间随时向用户显示实时反馈。

此外，后台运行允许快速更新其用户界面，使用户具有的最新的数据时它们快速粗略地看一下其 Apple Watch 应用。

若要维护在 Apple Watch 上的高性能，使用后台运行的监视应用程序应限制后台工作，以节省电池的量。 如果应用程序使用过多的 CPU 在后台，它可以获取 watchOS 挂起。

### <a name="enabling-background-running"></a>启用后台运行

若要启用后台运行，请执行以下操作：

1. 在**解决方案资源管理器**，双击监视扩展助理 iPhone 应用`Info.plist`文件以打开进行编辑。
2. 切换到**源**视图： 

    [ ![](workout-apps-images/plist01.png "源视图")](workout-apps-images/plist01.png)
3. 添加名为的新键`WKBackgroundModes`并设置**类型**到`Array`: 

    [ ![](workout-apps-images/plist02.png "添加调用 WKBackgroundModes 的新键")](workout-apps-images/plist02.png)
4. 将新项添加到该数组**类型**的`String`和的值`workout-processing`: 

    [ ![](workout-apps-images/plist03.png "将新项添加到具有字符串类型和值的锻炼处理数组")](workout-apps-images/plist03.png)
5. 保存对文件所做的更改。

## <a name="starting-a-workout-session"></a>启动测验会话

有三个主要步骤开始测验会话：

[ ![](workout-apps-images/workout02.png "启动测验会话三个主要步骤")](workout-apps-images/workout02.png)

1. 应用程序必须请求授权 HealthKit 中访问数据。
2. 正在启动的锻炼类型创建锻炼配置对象。
3. 创建并启动使用新创建的测验配置的锻炼会话。

### <a name="requesting-authorization"></a>请求的授权

应用程序可以访问用户的 HealthKit 数据之前，它必须请求和接收来自用户的授权。 根据锻炼应用程序的性质，它可能会使以下类型的请求：

- 若要将数据写入的授权：
    - 锻炼
- 读取数据的授权：
    - 能量刻录
    - 距离
    - 核心速率  

应用程序可以请求授权之前，需要将配置为访问 HealthKit。

请执行以下操作：

1. 在“解决方案资源管理器”中，双击 `Entitlements.plist` 文件，将其打开进行编辑。
2. 滚动到底部并检查**启用 HealthKit**: 

    [ ![](workout-apps-images/auth01.png "检查启用 HealthKit")](workout-apps-images/auth01.png)
3. 保存对文件所做的更改。
4. 按照中的说明[显式应用 ID 和预配配置文件](~/ios/platform/healthkit.md)和[关联的应用程序 ID 和预配配置文件与你的 Xamarin.iOS 应用程序](~/ios/platform/healthkit.md)的部分[简介HealthKit](~/ios/platform/healthkit.md)文章以正确配置应用。
5. 最后，使用中的说明[编程运行状况工具包](~/ios/platform/healthkit.md)和[从用户请求权限](~/ios/platform/healthkit.md)的部分[简介 HealthKit](~/ios/platform/healthkit.md)文章请求到若要访问用户的 HealthKit 数据存储的授权。

### <a name="setting-the-workout-configuration"></a>设置测验配置

创建使用锻炼配置对象的锻炼会话 (`HKWorkoutConfiguration`)，它指定锻炼类型 (如`HKWorkoutActivityType.Running`) 和锻炼位置 (如`HKWorkoutSessionLocationType.Outdoor`):

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>创建锻炼会话委托 

若要处理锻炼会话期间可以发生的事件，应用程序将需要创建的测验会话委托实例。 向项目添加新类和基本它注销`HKWorkoutSessionDelegate`类。 有关将户外运行示例时，它可能如下所示：

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }
        #endregion
    }
}
```

此类创建多个将作为锻炼会话更改的状态引发的事件 (`DidChangeToState`) 并且如果锻炼会话失败 (`DidFail`)。 

### <a name="creating-a-workout-session"></a>创建锻炼会话

使用锻炼配置和锻炼会话委托上面创建，以创建新的锻炼会话并开始针对用户的默认 HealthKit 存储：

```csharp
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

private void StartOutdoorRun ()
{
    // Create a workout configuration
    var configuration = new HKWorkoutConfiguration () {
        ActivityType = HKWorkoutActivityType.Running,
        LocationType = HKWorkoutSessionLocationType.Outdoor
    };

    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (configuration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

如果应用程序启动此锻炼会话和用户切换回其手表表盘，将面临的上方显示小绿色"running man"图标：

[ ![](workout-apps-images/workout03.png "如上所示面临的一个小绿色运行 man 图标")](workout-apps-images/workout03.png)

如果用户点击此图标，它们将被带回应用程序。

## <a name="data-collection-and-control"></a>数据收集和控制

配置并开始测验会话后，将需要应用程序来收集有关会话 （如用户的核心速率） 的数据和控制会话的状态：

[ ![](workout-apps-images/workout04.png "数据收集和控制关系图")](workout-apps-images/workout04.png)

1. **观察示例**-应用程序将需要从将得到处理并向用户显示的 HealthKit 中检索信息。
2. **观察事件**-应用程序将需要通过 HealthKit 或从 （例如暂停测验的用户） 的应用程序的 UI 生成的事件做出响应。
3. **进入运行状态**-会话已启动，并且当前正在运行。
4. **输入已暂停状态**-用户已暂停当前锻炼会话，并且可以在重新启动它更高版本的日期。 用户可能多次在的单个锻炼会话的正在运行和已暂停状态之间切换。
5. **结束锻炼会话**-用户可以随时结束锻炼会话或者可能过期并将其自己是否 （例如两个英里运行） 按流量计费的锻炼上结束。

最后一步是将锻炼会话的结果保存到用户的 HealthKit 数据存储。

### <a name="observing-healthkit-samples"></a>观察 HealthKit 示例

应用程序将需要打开_定位点对象查询_HealthKit 数据的每个点，它主要有兴趣，例如核心速率或活动能量刻录。 对于正在观察到每个数据点，一个更新处理程序将需要创建发送到应用程序中捕获新数据。

从这些数据点，应用程序能够累积总计 （如的总运行距离） 和更新它的用户界面根据需要。 此外，应用程序可以在它们已达到特定目标得以实现，如完成运行的下一步英里时通知用户。

看一看下面的示例代码：

```csharp
private void ObserveHealthKitSamples ()
{
    // Get the starting date of the required samples
    var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

    // Get data from the local device
    var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
    var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

    // Assemble compound predicate
    var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

    // Get ActiveEnergyBurned
    var queryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
        // Valid?
        if (error == null) {
            // Yes, process all returned samples
            foreach (HKSample sample in addedObjects) {
                var quantitySample = sample as HKQuantitySample;
                ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);
            }
            
            // Update User Interface
            ...
        }
    });

    // Start Query
    HealthStore.ExecuteQuery (queryActiveEnergyBurned);
                                          
}
```

它将创建一个谓词，用于设置它想要获取的使用数据的起始日期`GetPredicateForSamples`方法。 它会创建一组设备拉入 HealthKit 信息从使用`GetPredicateForObjectsFromDevices`方法，在此情况下本地 Apple Watch (`HKDevice.LocalDevice`)。 两个谓词合并为复合谓词 (`NSCompoundPredicate`) 使用`CreateAndPredicate`方法。

一个新`HKAnchoredObjectQuery`创建所需的数据点 (在这种情况下`HKQuantityTypeIdentifier.ActiveEnergyBurned`Active 能量刻录数据点)，返回的数据量没有限制 (`HKSampleQuery.NoLimit`) 并定义一个更新处理程序来处理数据被返回到应用程序从 HealthKit。 

将新数据传递到适用于给定的数据点的应用任何次调用更新处理程序。 如果不返回任何错误，应用程序能够安全地读取数据，进行任何所需的计算和更新所需其 UI。

该代码循环访问所有这些示例 (`HKSample`) 中返回`addedObjects`数组并将其强制转换为数量示例 (`HKQuantitySample`)。 然后，它获取 joule 作为示例的双精度值 (`HKUnit.Joule`) 并将其累积到正在使用的活动能源的锻炼刻录总和更新用户界面。

### <a name="achieved-goal-notification"></a>达到的目标通知

如上所述，当用户来实现的目标在测验应用程序 （例如完成运行的第一个英里），它可以发送 haptic 反馈用户可以通过 Taptic 引擎。 应用程序还应更新其 UI 此时，由于用户将多个可能引发其静电腕带看到提示反馈事件。

若要播放 haptic 反馈，请使用以下代码：

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>观察事件

事件是应用程序可用于在用户的测验期间突出显示的某些点的时间戳。 将直接由应用程序的某些事件，并且将其保存到锻炼和由 HealthKit 将自动创建某些事件。

若要观察由 HealthKit 创建的事件，应用将重写`DidGenerateEvent`方法`HKWorkoutSessionDelegate`:

```csharp
using System.Collections.Generic;
...

public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Save HealthKit generated event
    WorkoutEvents.Add (@event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Lap:
        break;
    case HKWorkoutEventType.Marker:
        break;
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

Apple watchOS 3 中新增了以下新的事件类型：

- `HKWorkoutEventType.Lap` -适用于锻炼分成相等距离部分的事件。 例如，对于标记一个浏览在运行时跟踪。
- `HKWorkoutEventType.Marker` -适用于感兴趣的任意点内测验。 例如，到达户外运行的特定点的路由。

可以创建的应用程序和存储中以便以后用于在创建关系图和统计信息锻炼这些新类型。

若要创建标记事件，请执行以下操作：

```csharp
using System.Collections.Generic;
...

public float MilesRun { get; set; }
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public void ReachedNextMile ()
{
    // Create and save marker event
    var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
    WorkoutEvents.Add (markerEvent);

    // Notify user
    NotifyUserOfReachedMileGoal (++MilesRun);
}
```

此代码将创建一个标记事件的新实例 (`HKWorkoutEvent`) 并将其保存到专用的事件集合 （稍后将写入到锻炼会话），通知通过 haptics 事件的用户。

### <a name="pausing-and-resuming-workouts"></a>暂停和恢复锻炼

测验会话中的任何时刻，用户可以暂停测验，并在更高版本的时间继续。 例如，它们可能会暂停以采取的重要的调用和调用完成后继续运行将室内运行。

应用程序的 UI 应提供一种方法来暂停和恢复锻炼 （调入 HealthKit），以便用户具有挂起其活动时，Apple Watch 可以节省两个电源和数据的空间。 此外，应用程序应忽略锻炼会话处于暂停状态时，可能会收到任何新数据点。

HealthKit 将进行响应来暂停和恢复调用生成暂停和恢复的事件。 在测验会话暂停时，任何新事件或数据将发送到应用程序通过 HealthKit 直到会话恢复。

使用下面的代码以暂停和继续锻炼会话：

```csharp
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public HKWorkoutSession WorkoutSession { get; set;}
...

public void PauseWorkout ()
{
    // Pause the current workout
    HealthStore.PauseWorkoutSession (WorkoutSession);
}

public void ResumeWorkout ()
{
    // Pause the current workout
    HealthStore.ResumeWorkoutSession (WorkoutSession);
}
```

可以通过重写处理将从 HealthKit 生成的暂停和恢复事件`DidGenerateEvent`方法`HKWorkoutSessionDelegate`:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);

    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

### <a name="motion-events"></a>运动事件

此外新增 watchOS 3，为不受运动暂停 (`HKWorkoutEventType.MotionPaused`) 和运动恢复 (`HKWorkoutEventType.MotionResumed`) 事件。 会引发这些事件自动通过 HealthKit 期间运行锻炼用户启动和停止移动时。

当应用程序收到运动暂停事件时，它应停止收集数据，直到用户将恢复运动，收到运动恢复事件。 应用程序应用程序不应暂停的锻炼会话中对运动暂停事件作出响应。

> [!IMPORTANT]
> **注意：** RunningWorkout 活动类型仅支持的运动暂停和运动恢复事件 (`HKWorkoutActivityType.Running`)。

同样，可以通过重写处理这些事件`DidGenerateEvent`方法`HKWorkoutSessionDelegate`:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    }
}

```

## <a name="ending-and-saving-the-workout-session"></a>结束和保存锻炼会话

当用户完成其测验时，应用程序将需要结束当前的锻炼会话并将其保存到 HealthKit 数据库。 保存到 HealthKit 锻炼将自动显示在测验活动列表中。

新到 iOS 10，这包括以及用户的 iPhone 上的锻炼活动列表列表。 因此即使 Apple Watch 不可附近，测验将手机上提供。

包括能量样本的锻炼将更新用户的活动应用移动环，使第三方应用程序现在会导致用户的每日移动目标。

需要执行以下步骤以结束并保存锻炼会话：

[ ![](workout-apps-images/workout05.png "结束和保存锻炼会话关系图")](workout-apps-images/workout05.png)

1. 首先，应用程序将需要结束锻炼会话。
2. 测验会话保存到 HealthKit。
3. 将任何示例 （如能量刻录或距离） 添加到已保存的锻炼会话。

### <a name="ending-the-session"></a>结束会话

若要结束锻炼会话，请调用`EndWorkoutSession`方法`HKHealthStore`传入`HKWorkoutSession`:

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
...

public void EndOutdoorRun ()
{
    // End the current workout session
    HealthStore.EndWorkoutSession (WorkoutSession);
}
```

这将为其正常模式下重置设备传感器。 HealthKit 完成时结束测验，它将接收到的回调`DidChangeToState`方法`HKWorkoutSessionDelegate`:

```csharp
public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
{
    // Take action based on the change in state
    switch (toState) {
    ...
    case HKWorkoutSessionState.Ended:
        StopObservingHealthKitSamples ();
        RaiseEnded ();
        break;
    }

}
```

### <a name="saving-the-session"></a>保存会话

一旦应用程序已结束锻炼会话，它将需要创建锻炼 (`HKWorkout`) 并将它 （以及事件） 保存到 HealthKit 数据存储区 (`HKHealthStore`):

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
public float MilesRun { get; set; }
public double ActiveEnergyBurned { get; set;}
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

private void SaveWorkoutSession ()
{
    // Build required workout quantities 
    var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
    var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

    // Create any required metadata
    var metadata = new NSMutableDictionary ();
    metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

    // Create workout
    var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                    WorkoutSession.StartDate, 
                                    NSDate.Now, 
                                    WorkoutEvents.ToArray (), 
                                    energyBurned, 
                                    distance, 
                                    metadata);

    // Save to HealthKit
    HealthStore.SaveObject (workout, (successful, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (successful) {

            }
        } else {
            // Report error
        }
    });

}
```

此代码将创建需要的能量刻录总量和距离来作为锻炼`HKQuantity`对象。 创建定义测验元数据的字典和测验的位置指定：

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

一个新`HKWorkout`对象创建具有相同`HKWorkoutActivityType`作为`HKWorkoutSession`，开始和结束日期，事件列表中的 （正在累加从前面的章节） 能量刻录，总距离和元数据字典。 此对象将保存到运行状况存储，并且处理任何错误。  

### <a name="adding-samples"></a>添加的示例

在应用程序将一组示例保存到测验，HealthKit 将生成这些示例和锻炼本身之间的连接，以便应用程序可以在以后的日子与给定锻炼关联的所有示例查询 HealthKit。 使用此信息，该应用可以从锻炼数据生成关系图并绘制其针对锻炼时间线。

为应用程序参与活动应用的移动环，它必须包括与已保存的锻炼能量示例。 此外，距离和能量的总计值必须与匹配的任何应用程序将与保存的锻炼相关联的样本的总和。

若要添加到已保存的锻炼示例，请执行以下操作：

```csharp
using System.Collections.Generic;
using WatchKit;
using HealthKit;
...

public HKHealthStore HealthStore { get; private set; }
public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
...


private void SaveWorkoutSamples (HKWorkout workout)
{
    // Add samples to saved workout
    HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (success) {

            }
        } else {
            // Report error
        }
    });
}
```

（可选） 应用程序可以计算以及创建示例或一个兆示例 （跨越测验的整个范围），然后获取与保存的锻炼相关联的较小子集。

## <a name="workouts-and-ios-10"></a>锻炼和 iOS 10

每个 watchOS 3 锻炼应用具有父 iOS 10 基于的锻炼应用和，新到 iOS 10，此 iOS 应用程序可以用于启动测验，将 Apple Watch 置于锻炼模式 （无需用户干预） 并在后台运行模式下运行 watchOS 应用 (请参阅[有关后台运行](#About-Background-Running)上面有关详细信息)。

在运行时 watchOS 应用程序，它可以用于消息传送和与父 iOS 应用程序的通信使用 WatchConnectivity。

看一看此过程的工作原理：

[ ![](workout-apps-images/workout06.png "iPhone 和 Apple Watch 通信关系图")](workout-apps-images/workout06.png)

1. IPhone 应用创建`HKWorkoutConfiguration`对象和设置的锻炼类型和位置。
2. `HKWorkoutConfiguration`对象发送应用程序的 Apple Watch 版本，并且，如果尚未运行，它由操作系统中启动。
3. 在测验配置中使用传递，watchOS 3 应用程序将启动新的锻炼会话 (`HKWorkoutSession`)。

> [!IMPORTANT]
> **注意：**使父 iPhone 应用程序，以在 Apple Watch 上启动测验，watchOS 3 应用程序必须具有启用的后台运行。 请参阅[启用后台运行](#Enabling-Background-Running)上面有关详细信息。

此过程是直接 watchOS 3 应用程序以开始测验会话的过程非常相似。 在 iPhone 上使用下面的代码：

```csharp
using System;
using HealthKit;
using WatchConnectivity;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
#endregion
...

private void StartOutdoorRun ()
{
    // Can the app communicate with the watchOS version of the app?
    if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
        // Create a workout configuration
        var configuration = new HKWorkoutConfiguration () {
            ActivityType = HKWorkoutActivityType.Running,
            LocationType = HKWorkoutSessionLocationType.Outdoor
        };

        // Start watch app
        HealthStore.StartWatchApp (configuration, (success, error) => {
            // Handle any errors
            if (error == null) {
                // Was the save successful
                if (success) {
                    ...
                }
            } else {
                // Report error
                ...
            }
        });
    }
}
```

此代码可确保安装 watchOS 版本的应用程序和 iPhone 版本可以首先连接到它：

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    ...
}
```

然后它创建`HKWorkoutConfiguration`像往常一样，并使用`StartWatchApp`方法`HKHealthStore`以将其发送到 Apple Watch 并启动应用程序和锻炼会话。

在监视 OS 应用程序中，使用下面的代码和`WKExtensionDelegate`:

```csharp
using WatchKit;
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...


public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
{
    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

它采用`HKWorkoutConfiguration`，并创建新`HKWorkoutSession`，并将附加的自定义实例`HKWorkoutSessionDelegate`。 针对用户的 HealthKit 运行状况存储启动测验会话。

## <a name="bringing-all-the-pieces-together"></a>将所有部分集合在一起

将所有本文档中提供的信息，watchOS 3 基于的锻炼应用程序和其父 iOS 10 基于的锻炼应用程序可能包括以下部分：

1. **iOS 10 `ViewController.cs`**  -处理开始监视连接会话，并且在 Apple Watch 上的测验。
2. **watchOS 3 `ExtensionDelegate.cs`**  -处理锻炼应用程序的 watchOS 3 版本。
3. **watchOS 3 `OutdoorRunDelegate.cs`**  -自定义`HKWorkoutSessionDelegate`来为锻炼处理事件。

> [!IMPORTANT]
> **注意：**下列部分中所示的代码仅包含实现提供给 watchOS 3 中的锻炼应用新的增强功能所需的部分。 所有支持的代码，并且要显示和更新 UI 的代码不包含，但可以轻松创建按照其他 watchOS 文档。<p/>



### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController.cs`锻炼应用程序的父 iOS 10 版本中的文件应包括以下代码：

```csharp
using System;
using HealthKit;
using UIKit;
using WatchConnectivity;

namespace MonkeyWorkout
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
        public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Private Methods
        private void InitializeWatchConnectivity ()
        {
            // Is Watch Connectivity supported?
            if (!WCSession.IsSupported) {
                // No, abort
                return;
            }

            // Is the session already active?
            if (ConnectivitySession.ActivationState != WCSessionActivationState.Activated) {
                // No, start session
                ConnectivitySession.ActivateSession ();
            }
        }

        private void StartOutdoorRun ()
        {
            // Can the app communicate with the watchOS version of the app?
            if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
                // Create a workout configuration
                var configuration = new HKWorkoutConfiguration () {
                    ActivityType = HKWorkoutActivityType.Running,
                    LocationType = HKWorkoutSessionLocationType.Outdoor
                };

                // Start watch app
                HealthStore.StartWatchApp (configuration, (success, error) => {
                    // Handle any errors
                    if (error == null) {
                        // Was the save successful
                        if (success) {
                            ...
                        }
                    } else {
                        // Report error
                        ...
                    }
                });
            }
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Start Watch Connectivity
            InitializeWatchConnectivity ();
        }
        #endregion
    }
}
```

### <a name="extensiondelegatecs"></a>ExtensionDelegate.cs

`ExtensionDelegate.cs`锻炼应用程序的 watchOS 3 版本中的文件应包括以下代码：

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
        public OutdoorRunDelegate RunDelegate { get; set; }
        #endregion

        #region Constructors
        public ExtensionDelegate ()
        {
            
        }
        #endregion

        #region Private Methods
        private void StartWorkoutSession (HKWorkoutConfiguration workoutConfiguration)
        {
            // Create workout session
            // Start workout session
            NSError error = null;
            var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

            // Successful?
            if (error != null) {
                // Report error to user and return
                return;
            }

            // Create workout session delegate and wire-up events
            RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

            RunDelegate.Failed += () => {
                // Handle the session failing
                ...
            };

            RunDelegate.Paused += () => {
                // Handle the session being paused
                ...
            };

            RunDelegate.Running += () => {
                // Handle the session running
                ...
            };

            RunDelegate.Ended += () => {
                // Handle the session ending
                ...
            };
            
            RunDelegate.ReachedMileGoal += (miles) => {
                // Handle the reaching a session goal
                ...
            };

            RunDelegate.HealthKitSamplesUpdated += () => {
                // Update UI as required
                ...
            };

            // Start session
            HealthStore.StartWorkoutSession (workoutSession);
        }

        private void StartOutdoorRun ()
        {
            // Create a workout configuration
            var workoutConfiguration = new HKWorkoutConfiguration () {
                ActivityType = HKWorkoutActivityType.Running,
                LocationType = HKWorkoutSessionLocationType.Outdoor
            };

            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion

        #region Override Methods
        public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
        {
            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion
    }
}
```

### <a name="outdoorrundelegatecs"></a>OutdoorRunDelegate.cs

`OutdoorRunDelegate.cs`锻炼应用程序的 watchOS 3 版本中的文件应包括以下代码：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Private Variables
        private HKAnchoredObjectQuery QueryActiveEnergyBurned;
        #endregion

        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        public float MilesRun { get; set; }
        public double ActiveEnergyBurned { get; set;}
        public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
        public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;

        }
        #endregion

        #region Private Methods
        private void ObserveHealthKitSamples ()
        {
            // Get the starting date of the required samples
            var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

            // Get data from the local device
            var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
            var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

            // Assemble compound predicate
            var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

            // Get ActiveEnergyBurned
            QueryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
                // Valid?
                if (error == null) {
                    // Yes, process all returned samples
                    foreach (HKSample sample in addedObjects) {
                        // Accumulate totals
                        var quantitySample = sample as HKQuantitySample;
                        ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);

                        // Save samples
                        WorkoutSamples.Add (sample);
                    }

                    // Inform caller
                    RaiseHealthKitSamplesUpdated ();
                }
            });

            // Start Query
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
                                                  
        }

        private void StopObservingHealthKitSamples ()
        {
            // Stop query
            HealthStore.StopQuery (QueryActiveEnergyBurned);
        }

        private void ResumeObservingHealthkitSamples ()
        {
            // Resume current queries 
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
        }

        private void NotifyUserOfReachedMileGoal (float miles)
        {
            // Play haptic feedback
            WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);

            // Raise event
            RaiseReachedMileGoal (miles);
        }

        private void SaveWorkoutSession ()
        {
            // Build required workout quantities
            var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
            var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

            // Create any required metadata
            var metadata = new NSMutableDictionary ();
            metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

            // Create workout
            var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                            WorkoutSession.StartDate, 
                                            NSDate.Now, 
                                            WorkoutEvents.ToArray (), 
                                            energyBurned, 
                                            distance, 
                                            metadata);

            // Save to HealthKit
            HealthStore.SaveObject (workout, (successful, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (successful) {
                        // Add samples to workout
                        SaveWorkoutSamples (workout);
                    }
                } else {
                    // Report error
                    ...
                }
            });

        }

        private void SaveWorkoutSamples (HKWorkout workout)
        {
            // Add samples to saved workout
            HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (success) {
                        ...
                    }
                } else {
                    // Report error
                    ...
                }
            });
        }
        #endregion

        #region Public Methods
        public void PauseWorkout ()
        {
            // Pause the current workout
            HealthStore.PauseWorkoutSession (WorkoutSession);
        }

        public void ResumeWorkout ()
        {
            // Pause the current workout
            HealthStore.ResumeWorkoutSession (WorkoutSession);
        }

        public void ReachedNextMile ()
        {
            // Create and save marker event
            var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
            WorkoutEvents.Add (markerEvent);

            // Notify user
            NotifyUserOfReachedMileGoal (++MilesRun);
        }

        public void EndOutdoorRun ()
        {
            // End the current workout session
            HealthStore.EndWorkoutSession (WorkoutSession);
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                StopObservingHealthKitSamples ();
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                if (fromState == HKWorkoutSessionState.Paused) {
                    ResumeObservingHealthkitSamples ();
                } else {
                    ObserveHealthKitSamples ();
                }
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                StopObservingHealthKitSamples ();
                SaveWorkoutSession ();
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);

            // Save HealthKit generated event
            WorkoutEvents.Add (@event);

            // Take action based on the type of event
            switch (@event.Type) {
            case HKWorkoutEventType.Lap:
                ...
                break;
            case HKWorkoutEventType.Marker:
                ...
                break;
            case HKWorkoutEventType.MotionPaused:
                ...
                break;
            case HKWorkoutEventType.MotionResumed:
                ...
                break;
            case HKWorkoutEventType.Pause:
                ...
                break;
            case HKWorkoutEventType.Resume:
                ...
                break;
            }
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();
        public delegate void OutdoorRunMileGoalDelegate (float miles);

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }

        public event OutdoorRunMileGoalDelegate ReachedMileGoal;
        internal void RaiseReachedMileGoal (float miles)
        {
            if (this.ReachedMileGoal != null) this.ReachedMileGoal (miles);
        }

        public event OutdoorRunEventDelegate HealthKitSamplesUpdated;
        internal void RaiseHealthKitSamplesUpdated ()
        {
            if (this.HealthKitSamplesUpdated != null) this.HealthKitSamplesUpdated ();
        }
        #endregion
    }
}
```

## <a name="best-practices"></a>最佳做法

Apple 提供的建议使用下列最佳方案设计和实现中 watchOS 3 和 iOS 10 锻炼应用时：

- 确保 watchOS 3 锻炼应用是仍正常工作，即使在无法连接到 iPhone 和 iOS 10 版本的应用程序。
- GPS 时不可用，因为它是能够生成而无需 GPS 距离示例，请使用 HealthKit 距离。
- 允许用户从 Apple Watch 或 iPhone 开始测验。
- 允许应用从其他源 （如其他第三方应用程序） 中其历史数据视图中显示测验。
- 确保应用程序不显示删除锻炼历史数据中。

## <a name="summary"></a>摘要

本文已覆盖增强功能 Apple 已针对锻炼应用 watchOS 3 以及如何在 Xamarin 实现它们。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [HealthKit 简介](~/ios/platform/healthkit.md)
