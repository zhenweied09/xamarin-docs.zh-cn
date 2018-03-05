---
title: "Firebase 作业调度程序"
description: "本指南讨论使用从 Google Firebase 作业调度程序库的后台工作。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 6b55e525849d57f2ad9e40ea64b75cfc65ef0727
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="firebase-job-dispatcher"></a>Firebase 作业调度程序

_本指南讨论使用从 Google Firebase 作业调度程序库的后台工作。_

## <a name="firebase-job-dispatcher-overview"></a>Firebase 作业调度程序概述

若要使 Android 应用程序保持响应状态，向用户的最佳方式之一是确保，在后台执行复杂或长时间运行的工作。 但是，很重要，后台工作不会产生负面影响用户的体验与该设备。 

例如，后台作业可能会轮询网站的更改特定的数据集查询每隔几分钟。 这看起来良性的但是它无法在设备上产生灾难性影响。 应用程序将结束唤醒设备、 从而 CPU 提升到更高版本的电源状态、 开机无线电、 进行网络请求，然后处理结果。 因为设备将不立即关闭电源，并返回到低能耗空闲状态，它获取更糟。 不佳计划的后台工作可能会无意中保持的状态具有不必要的和过度 power 要求设备。 实际上，（轮询网站） 此了无害活动将导致设备在相对较短的时间内不可用。

Android 已经提供了几个 Api，以帮助在后台中执行工作，但是这些都是一个全面的解决方案：

* **[意向服务](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash;意向服务则非常适合用于执行工作，但它们不提供任何方法来计划作业。
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager)**  &ndash;这些 Api 只允许计划的、 但不提供任何方法来实际执行工作的工作。 此外，AlarmManager 仅允许基于时间约束，这意味着在某个时间或经过一段时间后引发警报。 
* **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)**  &ndash; JobSchedule 是一种很好的 API，适用于操作系统可用于计划作业。 但是，它才可用于这些目标 API 级别 21 的 Android 应用程序或更高版本。 
* **[广播接收方](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Android 应用程序可以设置广播接收方在响应系统宽事件或方法中执行工作。 但是，广播的接收方未提供任何控制作业应何时运行。 在 Android 操作系统中的更改也将限制时广播的接收方将起作用，或者它们可以响应的工作的类型。 
* **Google 云消息网络管理器**&ndash;这是，重要性存在争议，长时间智能地计划背景的最佳方式工作。 但是，由于已弃用 GCMNetworkManager。 

有两个有效地执行后台工作的重要功能 (有时称为_后台作业_或_作业_):

1. **智能地计划工作**&ndash;很重要，应用程序执行时在后台工作，则它会以作为好公民。 理想情况下，应用程序不应要求，运行作业。 相反，应用程序应指定必须满足的中，可以在运行，然后计划，该作业时，它们共同以满足条件时运行的条件。 这使 Android 智能地执行工作。 例如，网络请求可能批处理运行在同一时间，以便与网络相关的开销最大使用全部。
2. **封装工作**&ndash;代码来执行后台工作应封装在可以运行独立于用户界面，将相对较容易重新计划如果未能完成工作的离散组件由于某种原因。

Firebase 作业调度程序是从 Google 提供 fluent API 来简化计划后台工作的库。 它旨在将替换为 Google 云管理器。 Firebase 作业调度程序包括以下 Api:

* A`Firebase.JobDispatcher.JobService`是一个抽象类，必须使用将在后台作业中运行的逻辑扩展。
* A`Firebase.JobDispatcher.JobTrigger`声明应启动作业时。 这通常表示为一个窗口的时间，例如，等待至少 30 秒，然后启动作业，但在 5 分钟内运行作业。
* A`Firebase.JobDispatcher.RetryStrategy`包含有关作业无法正确执行时应完成的操作的信息。 重试策略指定在尝试重新运行该作业之前等待多长时间。 
* A`Firebase.JobDispatcher.Constraint`是一个可选值，描述必须满足才能运行作业，例如设备位于 unmetered 网络条件或计费。
* `Firebase.JobDispatcher.Job`是统一到单元工作，可以通过计划中的上一 Api 的 API `JobDispatcher`。 `Job.Builder`类用于实例化`Job`。
* A`Firebasee.JobDispatcher.JobDispatcher`使用以前的三个 Api，来计划随操作系统一起工作，并提供一种方法可以取消作业，如有必要。

若要计划与 Firebase 作业调度程序的工作，Xamarin.Android 应用程序必须封装扩展的类型中的代码`JobService`类。 `JobService` 有三个生命周期方法，可以在作业的生存期期间调用：

* **`bool OnStartJob(IJobParameters parameters)`** &ndash; 此方法是其中工作将发生，并且应始终实现。 它在主线程上运行。 此方法将返回`true`剩余，如果没有工时或`false`如果完成的工作。 
* **`bool OnStopJob(IJobParameters parameters)`** &ndash; 这称为时由于某种原因停止作业。 它应返回`true`如果供以后应重新安排作业。
* **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; 当调用此方法`JobService`已完成任何异步工作。 

若要计划某个作业，该应用程序将实例化`JobDispatcher`对象。 然后，`Job.Builder`用于创建`Job`对象，提供给`JobDispatcher`以尝试并计划要运行的作业。

本指南将讨论如何将 Firebase 作业调度程序添加到 Xamarin.Android 应用程序并使用它来计划后台工作。

## <a name="requirements"></a>惠?

Firebase 作业调度程序需要 Android API 级别 9 或更高版本。 Firebase 作业调度程序库依赖于某些组件由 Google Play 服务;设备必须安装的 Google Play 服务。

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>使用中 Xamarin.Android 的 Firebase 作业调度程序库

若要开始使用 Firebase 作业调度程序，首先添加[Xamarin.Firebase.JobDispatcher NuGet 包](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher/0.6.0-beta1)到 Xamarin.Android 项目。 搜索 NuGet 程序包管理器**Xamarin.Firebase.Jobdispatcher**包。  

在添加 Firebase 作业调度程序库之后, 创建`JobService`类，然后安排其运行的实例`FirebaseJobDispatcher`。

### <a name="creating-a-jobservice"></a>创建 `JobService`

扩展的类型，必须完成通过 Firebase 作业调度程序库执行的所有工作`Firebase.JobDispatcher.JobService`抽象类。 创建`JobService`非常类似于创建`Service`与 Android 框架： 

1. 扩展`JobService`类
2. 修饰与子类`ServiceAttribute`。 尽管不是严格要求，建议显式设置`Name`参数，以帮助进行调试`JobService`。 
3. 添加`IntentFilter`声明`JobService`中**AndroidManifest.xml**。 这将帮助您找到和调用的 Firebase 作业调度程序库`JobService`。

下面的代码是最简单的示例`JobService`应用程序：

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // Note: This runs on the main thread. Anything that takes longer than 16 milliseconds
         // should be run on a seperate thread.
        
        return false; // return false because there is no more work to do.
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>创建 `FirebaseJobDispatcher`

可以计划任何工作之前，有必要创建`Firebase.JobDispatcher.FirebaseJobDispatcher`对象。 `FirebaseJobDispatcher`负责计划`JobService`。 下面的代码段是一种方法创建的实例`FirebaseJobDispatcher`: 
 
 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

在前面的代码段中，`GooglePlayDriver`是类，可帮助`FirebaseJobDispatcher`设备上与计划中一些 Api Google Play 服务进行交互。 参数`context`是任何 Android `Context`，如的活动。 当前`GooglePlayDriver`是唯一`IDriver`Firebase 作业调度程序库中的实现。 

Firebase 作业调度程序的 Xamarin.Android 绑定提供了一个扩展方法来创建`FirebaseJobDispatcher`从`Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

一次`FirebaseJobDispatcher`已实例化，它是可以创建`Job`和运行中的代码`JobService`类。 `Job`由创建`Job.Builder`对象，并将在下一部分中讨论。

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>创建`Firebase.JobDispatcher.Job`与 `Job.Builder`

`Firebase.JobDispatcher.Job`类负责封装元数据运行所需`JobService`。 A`Job`包含信息，如之前必须满足作业运行，如果任何约束`Job`重复进行的或将导致作业要运行任何触发器。  裸机最少`Job`必须具有_标记_(标识到作业的唯一字符串`FirebaseJobDispatcher`) 和类型`JobService`应运行。 Firebase 作业调度程序将实例化`JobService`时的时间来运行作业。  A`Job`使用的实例创建`Firebase.JobDispatcher.Job.JobBuilder`类。 

下面的代码段是如何创建的最简单的示例`Job`使用 Xamarin.Android 绑定：

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

`Job.Builder`将执行作业的输入值一些基本的验证检查。 如果它不可能，将引发异常`Job.Builder`创建`Job`。  `Job.Builder`将创建`Job`使用以下默认值：

* A`Job`的_生存期_（多长时间它将安排运行） 是仅在设备重启之前&ndash;设备重启后`Job`都将丢失。
* A`Job`不定期&ndash;它将仅运行一次。
* A`Job`将按计划运行越早越好。
* 默认重试策略`Job`是使用_指数退让_(在下面的部分中的更详细讨论[设置 RetryStrategy](#Setting_a_RestryStrategy))

### <a name="scheduling-a-job"></a>计划 `Job`

在创建后`Job`，它需要与计划`FirebaseJobDispatcher`运行之前。 有两种方法来计划`Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

返回的值`FirebaseJobDispatcher.Schedule`将是以下整数值之一：

* `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; `Job`已成功安排。
* `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; 导致出现某种未知的错误`Job`于计划。
* `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; 无效`IDriver`已使用或`IDriver`已以某种方式不可用。 
* `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; `Trigger`不支持。
* `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; 服务配置不正确，或者它不可用。
 
### <a name="configuring-a-job"></a>配置作业

它是可以自定义作业。 作业可以自定义的示例包括：

* [将参数传递到作业](#Passing_Parameters_to_a_Job) &ndash; A`Job`可能需要其他值以执行其工作，例如下载文件。
* [设置约束](#Setting_Constraints)&ndash;可能有必要仅在满足某些条件时运行的作业。 例如，仅运行`Job`设备正在充电时。 
* [指定何时`Job`应运行](#Setting_Job_Triggers) &ndash; Firebase 作业调度程序，应用程序可以指定作业应何时运行的时间。  
* [声明对于失败的作业重试策略](#Setting_a_RetryStrategy) &ndash; A_重试策略_提供指南`FirebaseJobDispatcher`上应如何处理`Jobs`，无法完成。 

每个这些主题将讨论多下列部分中。

#### <a name="passing-parameters-to-a-job"></a>将参数传递到作业

参数传递给一个作业通过创建`Bundle`连同传递`Job.Builder.SetExtras`方法：

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

`Bundle`从访问`IJobParameters.Extras`属性`OnStartJob`方法：

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```


#### <a name="setting-constraints"></a>设置约束

约束可以帮助减少设备成本或电池消耗。 `Firebase.JobDispatcher.Constraint`类将这些约束定义为整数值：

* `Constraint.OnUnmeteredNetwork` &ndash; 设备连接到 unmetered 网络时，仅运行该作业。 这可用于防止用户产生数据费用。
* `Constraint.OnAnyNetwork` &ndash; 设备连接到任何网络上运行作业。 如果指定连同`Constraint.OnUnmeteredNetwork`，此值将优先。
* `Constraint.DeviceCharging` &ndash; 仅当设备正在充电时，请运行作业。

使用设置约束`Job.Builder.SetConstraint`方法： 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

#### <a name="setting-job-triggers"></a>设置作业触发器

`JobTrigger`提供有关作业应何时开始操作系统指南。 A`JobTrigger`具有_执行窗口_，它定义的时间计划的时间`Job`应运行。 执行窗口具有_启动窗口_值和_结束窗口_值。 启动窗口是运行作业之前，设备应等待的秒数和结束窗口值是在运行前等待的秒的最大数量`Job`。 

A`JobTrigger`可用于创建`Firebase.Jobdispatcher.Trigger.ExecutionWindow`方法。  例如`Trigger.ExecutionWindow(15,60)`表示，作业应运行从计划时的 15 到 60 秒之间。 `Job.Builder.SetTrigger`方法是用于 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

默认值`JobTrigger`的值表示作业`Trigger.Now`，它指定计划后，尽快运行作业...

#### <a name="setting-a-retrystrategy"></a>设置 RetryStrategy

`Firebase.JobDispatcher.RetryStrategy`用于指定多少设备延迟应使用然后再尝试重新运行失败的作业。 A`RetryStrategy`具有_策略_，后者定义了哪些时间基数算法将用于重新计划失败的作业，并指定应将作业安排在其中一个时段执行窗口。 这_重新安排窗口_由两个值定义。 第一个值是在重新安排作业之前等待的秒数 (_初始回退_值)，第二个数字是必须运行作业之前等待的秒的最大数量 (_最大回退_值)。 
 
由这些 int 值标识两种类型的重试策略：

* `RetryStrategy.RetryPolicyExponential` &ndash; _指数退让_策略将初始回退值成指数增加每次故障后。 第一次作业失败，库将等待重新安排作业前指定 _initial 间隔&ndash;示例 30 秒。 第二次作业失败，库将等待至少 60 秒，然后再尝试运行该作业。 第三个尝试失败后，库将等待 120 秒，依此类推。 默认值`RetryStrategy`Firebase 作业调度程序由库`RetryStrategy.DefaultExponential`对象。 它具有 30 秒初始回退和最大回退值为 3600 秒。
* `RetryStrategy.RetryPolicyLinear` &ndash; 此策略是_线性退让_（直到成功），应重新作业计划为在运行设置的时间间隔。 线性退让最适合必须尽快完成的工作或快速将自身解决的问题。 Firebase 作业调度程序库定义`RetryStrategy.DefaultLinear`具有至少 30 秒的重新安排窗口和高达 3600 秒。

可以定义一个自定义`RetryStrategy`与`FirebaseJobDispatcher.NewRetryStrategy`方法。 它采用三个参数：

1. `int policy` &ndash; _策略_是上一个`RetryStrategy`值， `RetryStrategy.RetryPolicyLinear`，或`RetryStrategy.RetryPolicyExponential`。
2. `int intialBackoffSeconds` &ndash; _初始回退_一段延迟，以秒为单位，所需之前尝试重新运行该作业。 此默认值为 30 秒。 
3. `int maximumBackoffSeconds` &ndash; _最大回退_值声明在尝试重新运行该作业之前延迟的秒数最大数量。 默认值为 3600 秒。 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>取消作业

它是可以取消已安排的所有作业或只是单个作业使用`FirebaseJobDispatcher.CancelAll()`方法或`FirebaseJobDispatcher.Cancel(string)`方法：

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

任何一种方法将返回一个整数值：

* `FirebaseJobDispatcher.CancelResultSuccess` &ndash; 成功取消作业。
* `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; 某个错误导致无法被取消的作业。
* `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; `FirebaseJobDispatcher`无法取消作业，因为没有有效`IDriver`可用。

## <a name="summary"></a>摘要

本指南讨论如何使用 Firebase 作业调度程序以智能方式在后台中执行工作。 它讨论如何封装为执行的工作`JobService`和如何`FirebaseJobDispatcher`来计划该作业，指定与条件`JobTrigger`和如何与处理故障`RetryStrategy`。


## <a name="related-links"></a>相关链接

- [在 NuGet 上 Xamarin.Firebase.JobDispatcher](https://www.nuget.org/packages/Xamarin.FirebaseJobDispatcher)
- [firebase-job-dispatcher on GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.Firebase.JobDispatcher 绑定](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [智能作业计划](https://developer.android.com/topic/performance/scheduling.html)
- [Android 电池和内存优化的 Google I/O 2016 （视频）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
