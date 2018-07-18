---
title: Android 作业计划程序
description: 本指南讨论 使用 Android 的作业计划程序 API 的后台工作。
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/19/2018
ms.openlocfilehash: dc72b7e4da330185b00541f923d9c4b64b91bc95
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2018
ms.locfileid: "30005683"
---
# <a name="android-job-scheduler"></a>Android 作业计划程序

_本指南讨论使用 Android 的作业计划程序 API，这是在运行 Android 5.0 （API 级别 21） 的 Android 设备上可用的后台工作和更高版本。_


## <a name="overview"></a>概述 

若要使 Android 应用程序保持响应状态，向用户的最佳方式之一是确保，在后台执行复杂或长时间运行的工作。 但是，很重要，后台工作不会产生负面影响用户的体验与该设备。 

例如，后台作业可能分钟轮询一次网站每三个或四个查询的特定的数据集的更改。 但是，它将带来灾难性影响电池使用时间，这看起来无害。 应用程序将重复唤醒设备，将 CPU 提升到更高版本的电源状态、 无线电接通电源，使网络请求，然后处理结果。 因为设备将不立即关闭电源，并返回到低能耗空闲状态，它获取更糟。 不佳计划的后台工作可能会无意中保持的状态具有不必要的和过度 power 要求设备。 此看似无害的活动 （轮询网站） 将导致设备在相对较短的时间内不可用。

Android 提供以下 Api 来帮助与在后台中执行工作，但本身它们不能满足需求的智能作业计划。 

* **[意向服务](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash;意向服务则非常适合用于执行工作，但它们不提供任何方法来计划作业。
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash;这些 Api 只允许安排延续任务，但不提供任何方法来实际执行工作的工作。 此外，AlarmManager 仅允许基于时间约束，这意味着在某个时间或经过一段时间后引发警报。 
* **[广播接收方](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Android 应用程序可以设置广播接收方在对系统级事件或意向响应中执行工作。 但是，广播的接收方未提供任何控制作业应何时运行。 在 Android 操作系统中的更改也将限制时广播的接收方将起作用，或者它们可以响应的工作的类型。 

有两个有效地执行后台工作的关键功能 (有时称为_后台作业_或_作业_):

1. **智能地计划工作**&ndash;很重要，应用程序执行时在后台工作，则它会以作为好公民。 理想情况下，应用程序不应要求，运行作业。 相反，应用程序应指定作业何时可以运行，然后安排该作业与满足条件时，将执行工作的操作系统必须满足的条件。 这使 Android 运行作业，以确保在设备上的最大效率。 例如，网络请求可能批处理运行在同一时间，以便与网络相关的开销最大使用全部。
2. **封装工作**&ndash;代码来执行后台工作应封装在可以运行独立于用户界面，将相对较容易重新计划如果未能完成工作的离散组件由于某种原因。

Android 作业计划程序是提供 fluent API 来简化计划后台工作 Android 操作系统中内置的框架。  Android 作业计划程序包含以下类型：

* `Android.App.Job.JobScheduler`是一种系统服务，用于计划、 执行和必要时取消，代表 Android 应用程序的作业。
* `Android.App.Job.JobService`是一个抽象类，必须使用将在应用程序的主线程运行作业的逻辑扩展。 这意味着，`JobService`负责以异步方式执行工作的方式。
* `Android.App.Job.JobInfo`对象保存要指导 Android，应在运行作业时的条件。

若要计划使用 Android 作业计划程序的工作，Xamarin.Android 应用程序必须将代码封装在扩展的类的`JobService`类。 `JobService` 有三个生命周期方法，可以在作业的生存期期间调用：

* **bool OnStartJob （JobParameters 参数）** &ndash;调用此方法`JobScheduler`执行工作，并在应用程序的主线程上运行。 它负责`JobService`来以异步方式执行工作和`true`剩余，如果没有工时或`false`如果完成的工作。
    
    当`JobScheduler`调用此方法，它将请求并将从 Android wakelock 保留作业持续时间。 完成作业后，它负责`JobService`告诉`JobScheduler`这一事实调用`JobFinished`（下文所述） 的方法。

* **JobFinished （JobParameters 参数、 bool needsReschedule）** &ndash;必须调用此方法`JobService`告诉`JobScheduler`的工作都完成。 如果`JobFinished`不调用，`JobScheduler`不会删除 wakelock，导致不必要的电池消耗。 

* **bool OnStopJob （JobParameters 参数）** &ndash; Android 过早停止作业时将调用此。 它应返回`true`如果作业应能重新计划基于重试条件 （下面详细讨论）。

可以指定_约束_或_触发器_用于将控制作业可以或应运行时。 例如，很可能要约束作业，以便它仅设备正在充电或启动作业时图片执行时运行。

本指南将详细地讨论如何实现`JobService`类并将其与安排`JobScheduler`。

## <a name="requirements"></a>要求

Android 作业计划程序需要 Android API 级别 21 (Android 5.0) 或更高版本。 

## <a name="using-the-android-job-scheduler"></a>使用 Android 作业计划程序

有使用 Android JobScheduler API 的三个步骤：

1. 实现一个 JobService 类型，封装工作。
2. 使用`JobInfo.Builder`对象以创建`JobInfo`将保存的条件的对象`JobScheduler`以运行该作业。 
3. 计划作业使用`JobScheduler.Schedule`。

### <a name="implement-a-jobservice"></a>实现 JobService

扩展的类型，必须完成执行的 Android 作业计划程序库的所有工作`Android.App.Job.JobService`抽象类。 创建`JobService`非常类似于创建`Service`与 Android 框架： 

1. 扩展`JobService`类。
2. 修饰与子类`ServiceAttribute`并设置`Name`参数为包名称和类的名称组成的字符串 （请参阅下面的示例）。
3. 设置`Permission`属性`ServiceAttribute`到字符串`android.permission.BIND_JOB_SERVICE`。
4. 重写`OnStartJob`方法，添加代码来执行工作。 Android 将调用应用程序以运行该作业的主线程上的此方法。 将花较长时间，应在以避免阻止应用程序的线程上执行几毫秒的工作。
5. 完成工作后，`JobService`必须调用`JobFinished`方法。 此方法是如何`JobService`告知`JobScheduler`，完成的工作。 调用失败`JobFinished`将导致`JobService`将不必要的需求放置在设备上，缩短的电池寿命。 
6. 它是一个好办法还重写`OnStopJob`方法。 作业正在关闭时完成并提供之前，此方法称为 android`JobService`正确释放的任何资源的机会。 此方法应返回`true`如有必要，重新计划作业，或`false`是否不 desireable 以重新运行该作业。
   

下面的代码是最简单的示例`JobService`对于应用程序，使用 TPL 以异步方式执行某项操作：

```csharp
[Service(Name = "com.xamarin.samples.downloadscheduler.DownloadJob", 
         Permission = "android.permission.BIND_JOB_SERVICE")]
public class DownloadJob : JobService
{
    public override bool OnStartJob(JobParameters jobParams)
    {            
        Task.Run(() =>
        {
            // Work is happening asynchronously
                      
            // Have to tell the JobScheduler the work is done. 
            JobFinished(jobParams, false);
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(JobParameters jobParams)
    {
        // we don't want to reschedule the job if it is stopped or cancelled.
        return false; 
    }
}
```

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>创建 JobInfo 计划作业

Xamarin.Android 应用程序不要实例化`JobService`直接，改为将传递`JobInfo`对象传递给`JobScheduler`。 `JobScheduler`将实例化请求`JobService`对象，计划和运行`JobService`中的元数据根据`JobInfo`。 A`JobInfo`对象必须包含以下信息：

* **JobId** &ndash;这是`int`值，用于标识的作业`JobScheduler`。 重复使用此值将更新任何现有的作业。 值必须是唯一的应用程序。 
* **JobService** &ndash;此参数是`ComponentName`，显式标识的类型，`JobScheduler`应用来运行作业。 
  

此扩展方法演示如何创建`JobInfo.Builder`android `Context`，如的活动：

```csharp
public static class JobSchedulerHelpers
{
    public static JobInfo.Builder CreateJobBuilderUsingJobId<T>(this Context context, int jobId) where T:JobService
    {
        var javaClass = Java.Lang.Class.FromType(typeof(T));
        var componentName = new ComponentName(context, javaClass);
        return new JobInfo.Builder(jobId, componentName);
    }
}

// Sample usage - creates a JobBuilder for a DownloadJob andsets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creats a JobInfo object.
```

Android 作业计划程序的一个强大功能是能够控制当作业运行或在什么条件下作业可能会运行。 下表描述的某些方法上`JobInfo.Builder`，可让应用来影响一个作业的运行时：  


|  方法 | 描述   |
|---|---|
| `SetMinimumLatency`  | 指定运行应在作业之前观察到延迟时间 （以毫秒为单位）。 |
| `SetOverridingDeadline`  | 声明作业必须运行之前经过此时间 （以毫秒为单位）。 |
| `SetRequiredNetworkType`  | 指定作业的网络要求。 |
| `SetRequiresBatteryNotLow` | 设备不向用户显示"电池电量不足"警报时，可能仅运行作业。 |
| `SetRequiresCharging` | 电池正在充电时，可能仅运行该作业。 |
| `SetDeviceIdle` | 设备处于忙时，将运行作业。 |
| `SetPeriodic` | 指定应定期运行作业。 |
| `SetPersisted` | 作业在设备重新引导后应 perisist。 | 


`SetBackoffCriteria`提供一些指导如何长`JobScheduler`尝试再次运行作业之前应等待。 有两个部分的回退条件： 应使用毫秒 （30 秒的默认值） 以及类型的退让延迟 (有时称为_退让策略_或_重试策略_). 两个策略封装在`Android.App.Job.BackoffPolicy`枚举：

* `BackoffPolicy.Exponential` &ndash; 指数退让策略将在每次失败后成指数增加初始回退值。 第一次作业失败，库将等待重新安排作业 – 示例 30 秒之前指定的初始间隔。 第二次作业失败，库将等待至少 60 秒，然后再尝试运行该作业。 第三个尝试失败后，库将等待 120 秒，依此类推。 这是默认值。
* `BackoffPolicy.Linear` &ndash; 此策略是作业应能重新安排给线性退让 （直到成功） 按设置的间隔运行。 线性退让最适合必须尽快完成的工作或快速将自身解决的问题。 

有关更多详细信息创建`JobInfo`对象，请阅读[Google 文档`JobInfo.Builder`类](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html)。

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>将参数传递到通过 JobInfo 作业

参数传递给一个作业通过创建`PersistableBundle`连同传递`Job.Builder.SetExtras`方法：

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

`PersistableBundle`从访问`Android.App.Job.JobParameters.Extras`中的属性`OnStartJob`方法`JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>计划作业

若要计划某个作业，Xamarin.Android 应用程序将获取对引用`JobScheduler`系统服务，并调用`JobScheduler.Schedule`方法替换`JobInfo`在上一步中创建的对象。 `JobScheduler.Schedule` 将立即返回与两个整数值之一：

* **JobScheduler.ResultSuccess** &ndash;作业已成功计划。 
* **JobScheduler.ResultFailure** &ndash;无法计划作业。 这通常由冲突引起`JobInfo`参数。

此代码演示了计划作业和通知的计划尝试结果的用户：

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
}
else
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
}
```
 
### <a name="cancelling-a-job"></a>取消作业

它是可以取消已安排的所有作业或只是单个作业使用`JobsScheduler.CancelAll()`方法或`JobScheduler.Cancel(jobId)`方法：

```csharp
// Cancel all jobs
jobSchduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>总结

本指南讨论如何使用 Android 作业计划程序以智能方式在后台中执行工作。 它讨论如何封装为执行的工作`JobService`以及如何使用`JobScheduler`来计划该作业，指定与条件`JobTrigger`和如何与处理故障`RetryStrategy`。

## <a name="related-links"></a>相关链接

- [智能作业计划](https://developer.android.com/topic/performance/scheduling.html)
- [JobScheduler API 参考](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [计划作业，如与 JobScheduler pro](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android 电池和内存优化的 Google I/O 2016 （视频）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler-René Ruppert-Xamarin 大学](https://www.youtube.com/watch?v=aSjBBPYjelE)