---
title: 在 Xamarin.Android 的意向服务
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 80849213649707615f8bd8e941e1a51c6b54e76e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="intent-services-in-xamarinandroid"></a>在 Xamarin.Android 的意向服务

## <a name="intent-services-overview"></a>意向服务概述

同时启动，并且绑定在主线程，这意味着，若要保留性能平滑，服务需要异步执行工作上运行的服务。 若要解决此问题的最简单方法之一是其中_辅助队列处理器模式_，其中将进行的工作置于单个线程处理的队列。 

[ `IntentService` ](https://developer.xamarin.com/api/type/Android.App.IntentService/)是的一个子类`Service`提供此模式的特定 Android 实现的类。 它将管理队列工作，启动工作线程来服务队列，并请求请求关闭要在辅助线程上运行的队列。 `IntentService`将安静地自行停止和删除工作线程，队列中的没有更多工作时。
 
通过创建将工作提交到的队列`Intent`，然后将该`Intent`到`StartService`方法。

不能停止或中断`OnHandleIntent`方法`IntentService`时它正在工作。 由于此设计，`IntentService`应保持无状态&ndash;它不应依赖于某个活动的连接或从应用程序的其余部分通信。 `IntentService`旨在 statelessly 处理工作请求。

有两个要求子类化`IntentService`:

1. 新的类型 (由子类化`IntentService`) 只会终止`OnHandleIntent`方法。
2. 为新的类型构造函数需要一个字符串，它用于命名辅助线程将处理请求。 调试应用程序时，主要使用此工作线程的名称。

下面的代码演示`IntentService`实现重写的`OnHandleIntent`方法：

```csharp
[Service]
public class DemoIntentService: IntentService
{
    public DemoIntentService () : base("DemoIntentService")
    {
    }
    
    protected override void OnHandleIntent (Android.Content.Intent intent)
    {
        Console.WriteLine ("perform some long running work");
        ...
        Console.WriteLine ("work complete");
    }
}
```

工作发送到`IntentService`方法是实例化`Intent`，然后再调用[ `StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)使用该意向作为参数的方法。 其目的将作为中的参数传递到服务`OnHandleIntent`方法。 此代码片段演示了将工作请求发送到为打算： 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

`IntentService`可以从中提取值的意图，此代码段中所示：  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");
    
    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```


## <a name="related-links"></a>相关链接

- [IntentService](https://developer.xamarin.com/api/type/Android.App.IntentService/)
- [StartService](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)
