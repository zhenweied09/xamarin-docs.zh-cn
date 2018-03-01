---
title: "与 Xamarin.Android 启动的服务"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: b6c0e67e3411aa5b7846a1b7bc0de2473a3546fd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="started-services-with-xamarinandroid"></a>与 Xamarin.Android 启动的服务

## <a name="started-services-overview"></a>启动的服务概述

启动的服务通常执行工作的单元，而无需向客户端提供的任何直接反馈或结果。 工作单元的一个示例是一种服务，将文件上载到服务器。 客户端将对上载到网站文件从设备的服务进行请求。 （即使应用程序在前台出现没有任何活动），服务安静地将上载文件，并完成上载后终止本身。 请务必认识到启动的服务将在应用程序的 UI 线程上运行。 这意味着如果某个服务是执行将阻塞 UI 线程的工作，它必须创建并根据需要释放线程。

绑定与服务不同，"纯"启动的服务及其客户端之间没有通信通道。 这意味着启动的服务将执行比绑定服务一些不同的生命周期方法。 下表列出了启动的服务中常见的生命周期方法：

* `OnCreate` &ndash; 调用一次当首次启动该服务。 这是初始化代码应实现了。
* `OnBind` &ndash; 但是，启动的服务通常不能绑定到它的客户端，所有服务类，必须都实现此方法。 因此，启动的服务只返回`null`。 混合服务 （这是绑定的服务和启动的服务的组合） 与此相反，必须实现并返回`Binder`客户端。
* `OnStartCommand` &ndash; 为每个请求以启动服务，在响应调用调用`StartService`或由系统重新启动。 这是服务开始任何长时间运行的任务的位置。 该方法返回`StartCommandResult`值，该值指示如何或如果系统应处理由于内存不足导致关闭后重新启动服务。 此调用将主线程上的发生。 下面更详细地介绍了这些方法。
* `OnDestroy` &ndash; 在销毁该服务时，调用此方法。 要使用它来执行任何最终清理所需。

启动的服务的重要方法是`OnStartCommand`方法。 它将调用每个时间服务接收的请求执行某项操作。 下面的代码段演示了`OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

第一个参数是`Intent`对象，其中包含有关执行的工作的元数据。 第二个参数包含`StartCommandFlags`值，该值提供有关方法调用的一些信息。 此参数具有两个可能值之一：

* `StartCommandFlag.Redelivery` &ndash; 这意味着，`Intent`是以前的重新传递`Intent`。 返回的服务时提供此值`StartCommandResult.RedeliverIntent`但已停止，但它无法正确关闭。
* `StartCommandFlag.Retry` &dash; 在以前时收到此值，则`OnStartCommand`调用失败，并且 Android 尝试使用相同意向为以前的失败尝试再次启动服务。
 
最后，第三个参数是一个整数值，是唯一的标识请求的应用程序。 很可能多个调用方可能会调用相同的服务对象。 此值用于将停止具有给定的请求来启动服务的服务请求相关联。 它将部分中的更详细地讨论[停止服务](#Stopping_the_Service)。 

值`StartCommandResult`就会归还服务为一个建议 Android 上要执行的操作如果服务终止由于资源限制。 有三个可能值`StartCommandResult`:

* **[StartCommandResult.NotSticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.NotSticky/)**  &ndash;此值指示不需要重新启动已终止该服务的 Android。 此示例，请考虑在应用程序生成为库的缩略图的服务。 如果服务终止，并不重要立即重新创建缩略图&ndash;缩略图可以重新创建在下次运行应用程序。
* **[StartCommandResult.Sticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.Sticky/)**  &ndash;这将告知 Android 来重新启动服务，但不是能提供已启动的服务的最后一个目的。 如果没有任何挂起的意向，若要处理，则`null`将为意向的参数提供。 此示例可能是一个音乐播放器应用，;该服务将会重新启动已准备好播放音乐，但它将播放最后一首歌曲。 
* **[StartCommandResult.RedeliverIntent](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.RedeliverIntent/)**  &ndash;此值是将告诉 Android 以重新启动服务并重新提供上次`Intent`。 此示例是下载应用程序的数据文件服务。 如果终止该服务，数据文件将仍需要下载。 通过返回`StartCommandResult.RedeliverIntent`，当 Android 到服务，重新启动的服务还将提供的意图 （其中包含要下载的文件的 URL）。 这将允许您下载后，重新启动或恢复 （具体取决于代码的具体实现）。

没有第四个值，则为`StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`。 此值由返回`OnStartCommand`并描述了在 Android 仍服务已终止。 通常，此值不用于启动服务。

启动的服务的密钥生命周期事件显示在此关系图中： 

![关系图显示生命周期方法将调用的顺序](started-services-images/started-service-01.png "关系图显示生命周期方法将调用的顺序。")


## <a name="stopping-the-service"></a>停止服务

启动的服务将继续无限期地; 运行只要有足够的系统资源，android 将使服务正在运行。 客户端必须停止服务，或者服务可能自行停止，如果它完成其工作。 有两种方法来停止服务： 
 
* **[Android.Content.Context.StopService()](https://developer.xamarin.com/api/member/Android.Content.Context.StopService/p/Android.Content.Intent/)**  &ndash;一个客户端 （如活动） 可以请求停止服务调用`StopService`方法： 

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

* **[Android.App.Service.StopSelf()](https://developer.xamarin.com/api/member/Android.App.Service.StopSelf()/)**  &ndash;服务可能会让本身关闭，通过调用`StopSelf`:

    ```csharp
    StopSelf();
    ```
    
### <a name="using-startid-to-stop-a-service"></a>使用 startId 来停止服务

多个调用方可以请求启动服务。 如果未完成的启动请求，服务可以使用`startId`传递到`OnStartCommand`将阻止从正在过早停止的服务。 `startId`将对应于最新调用`StartService`，并将会递增每次调用时。 因此，如果对的后续请求`StartService`尚未不导致调用`OnStartCommand`，服务可以调用`StopSelfResult`，将其传递的最新值`startId`已接收到 (而不是只需调用`StopSelf`)。 如果调用`StartService`尚未不导致相应地调用`OnStartCommand`，系统不会停止该服务，因为`startId`中使用`StopSelf`调用将不对应于最新`StartService`调用。


## <a name="related-links"></a>相关链接

- [StartedServicesDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/StartedServicesDemo/)
- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service)
- [Android.App.StartCommandFlags](https://developer.xamarin.com/api/type/Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](https://developer.xamarin.com/api/type/Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Android.Content.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent)
- [Android.OS.Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Android.Widget.Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
- [状态栏图标](http://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
