---
title: "iOS Backgrounding 任务"
ms.topic: article
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 5e05cf0f13512478b3957070e7fa6329ea84337f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="ios-backgrounding-with-tasks"></a>iOS Backgrounding 任务

执行 backgrounding 在 iOS 上的最简单方法是将 backgrounding 要求拆分为任务，并在后台运行的任务。 任务下一个严格的时间限制，通常在 iOS 7 + 上获取的处理时间之后应用程序已移动到后台在 iOS 6，大约 600 秒 （10 分钟） 和小于 10 分钟。

后台任务可以分解为三个类别：

1.  **后台安全任务**-中任何位置调用应用程序必须任务你不希望中断应用程序，应输入背景。
1.  **DidEnterBackground 任务**-调用期间`DidEnterBackground`应用程序生命周期方法，以帮助清理和状态保存。
1.  **后台传输 (iOS 7 +)** -一种特殊类型的后台任务用来在 iOS 7 上执行网络传输。 不同于常规任务，后台传输没有预先确定的时间限制。


后台安全和`DidEnterBackground`任务才可以安全地在 iOS 6 和 iOS 7，存在一些细微差别上使用。 让我们调查这些两种类型的更多详细信息中的任务。

## <a name="creating-background-safe-tasks"></a>创建后台安全任务

某些应用程序包含不应在应用程序应更改状态 ios 中断的任务。 若要防止这些任务被中断的一种方法是将它们注册为长时间运行的任务的 iOS。 应用程序中任何不希望被中断的任务到后台应用户 put 应用程序的任何位置可以使用此模式。 此模式非常适合将任务，例如将新用户的注册信息发送到你的服务器，或验证登录信息。

下面的代码段说明如何注册要在后台运行的任务：

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

注册过程对具有唯一标识符，任务`taskID`，然后将其包装在匹配中和`BeginBackgroundTask`和`EndBackgroundTask`调用。 若要生成标识符，我们使调用`BeginBackgroundTask`方法`UIApplication`对象，以及然后启动长时间运行的任务，通常新线程上。 在任务完成后，我们调用`EndBackgroundTask`并传入相同的标识符。 这是重要的因为 iOS 将终止应用程序，如果`BeginBackgroundTask`调用时，没有匹配`EndBackgroundTask`。

> [!IMPORTANT]
> **请注意**： 背景安全任务可以运行在主线程或后台线程，具体取决于应用程序的需求上。


## <a name="performing-tasks-during-didenterbackground"></a>在 DidEnterBackground 过程中执行任务

除了后台安全进行的长时间运行的任务，可以使用注册可视为正在将应用程序放在后台启动任务。 iOS 提供的事件方法*AppDelegate*类调用`DidEnterBackground`可用来保存应用程序状态、 保存用户数据和应用程序进入后台之前加密敏感内容。 应用程序有大约五秒，若要通过此方法返回，或将获取终止。 因此，可以从调用可能需要多个五秒钟来完成的清理任务内`DidEnterBackground`方法。 必须在单独的线程调用这些任务。

过程是几乎完全相同的注册的长时间运行的任务。 下面的代码段阐释了这一点在操作中：

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

首先，我们重写`DidEnterBackground`中的方法`AppDelegate`，其中我们注册通过我们任务`BeginBackgroundTask`正如我们在前面的示例做。 接下来，我们生成新线程和执行我们长时间运行的任务。 请注意，`EndBackgroundTask`现在从进行调用内长时间运行的任务，因为`DidEnterBackground`方法将具有已返回。

> [!IMPORTANT]
> **请注意**: iOS 使用[监视程序机制](http://developer.apple.com/library/ios/qa/qa1693/_index.html)以确保应用程序的 UI 保持响应性。 应用程序花费太多时间`DidEnterBackground`将变得不响应用户界面中。 手动启动任务在后台运行允许`DidEnterBackground`返回及时，使 UI 保持响应状态，并阻止监视器终止应用程序中。


## <a name="handling-background-task-time-limits"></a>处理后台任务时间限制

iOS 上放置严格限制，如何可以运行长时间的后台任务，而如果`EndBackgroundTask`分配的时间内未进行调用，将终止应用程序。 通过跟踪剩余 backgrounding 时间，并使用过期处理程序在必要时，我们可以避免 iOS 终止应用程序。

### <a name="accessing-background-time-remaining"></a>访问剩余的后台时间

如果已注册的任务的应用程序获取移到后台，已注册的任务可能会大约 600 秒运行。 我们可以检查多长时间的任务必须使用静态来完成`BackgroundTimeRemaining`属性`UIApplication`类。 下面的代码将向我们提供的时间，以秒为单位，离开我们后台任务：

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>避免与过期处理程序的应用程序终止

除了提供访问`BackgroundTimeRemaining`属性，iOS 提供一种处理背景时间过期通过正常方法**过期处理程序**。 这是代码的为任务分配的时间即将过期时，将获取执行的可选块。 过期处理程序中的代码调用`EndBackgroundTask`，并传入任务 ID，以指示应用程序也行为，并防止 iOS 终止应用程序，即使在任务运行时间不够。 `EndBackgroundTask` 必须调用内的过期处理程序，以及在执行的正常运行。 

过期处理程序表示为匿名函数使用 lambda 表达式，如下所示：

```csharp
Task.Factory.StartNew( () => {

    //expirationHandler only called if background time allowed exceeded
    var taskId = UIApplication.SharedApplication.BeginBackgroundTask(() => {
        Console.WriteLine("Exhausted time");
        UIApplication.SharedApplication.EndBackgroundTask(taskId); 
    });
    while(myFlag == true)
    {
        Console.WriteLine(UIApplication.SharedApplication.TimeRemaining);
        myFlag = SomeCalculationNeedsMoreTime();
    }
    //Only called if loop terminated due to myFlag and not expiration of time
    UIApplication.SharedApplication.EndBackgroundTask(taskId);
});
```

时过期处理程序不需要的要运行的代码，你应始终使用过期的处理程序，但后台任务。

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>在 iOS 7 + 中的后台任务

对于后台任务的 iOS 7 中的最大更改是不任务的实现方式，但在运行时。

回想一下，预 iOS 7 中，在后台运行的任务了 600 秒才能完成。 此限制的原因之一是任务的，在后台运行的任务将保留设备唤醒状态的持续时间内：

 [![](ios-backgrounding-with-tasks-images/ios6.png "保持处于唤醒状态预 iOS 7 的应用程序的任务的关系图")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

iOS 7 后台处理较长的电池使用时间进行了优化。 在 iOS 7，backgrounding 成为机会： 而不是保留设备唤醒状态的情况下，任务时，尊重设备进入睡眠状态，并改为其在中执行处理区块时设备唤醒以处理电话呼叫、 通知、 传入电子邮件和其他常见中断。 下图提供深入了解如何任务可能会被破坏向上：

 [![](ios-backgrounding-with-tasks-images/ios7.png "关系图被分成区块后 iOS 7 的任务")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

因为任务运行时不会再连续，执行网络传输的任务必须以不同方式处理在 iOS 7。 开发人员建议使用`NSURlSession`API 以处理网络传输。 下一部分是后台传输的概述。

 <a name="background-transfers" />

## <a name="background-transfers"></a>后台传输

在 iOS 7 中的后台传输的主干是新`NSURLSession`API。 `NSURLSession` 让我们创建的任务：

1.  将通过网络和设备中断的内容传输。
1.  上载和下载大型文件 (*后台传输服务*)。


让我们进一步查看此工作原理。

### <a name="nsurlsession-api"></a>NSURLSession API

 `NSURLSession` 是功能强大的 API，用于通过网络传输内容。 它提供了一套工具来处理通过网络中断和应用程序状态的更改的数据传输。

`NSURLSession` API 创建一个或多个会话，反过来生成任务以在网络的快速相关的数据块区。 任务以异步方式运行，以快速且可靠地传输数据。 因为`NSURLSession`是异步的每个会话需要让系统和应用程序知道传输完成时完成处理程序块。

若要执行对预 iOS 7 和后 iOS 7 有效的网络传输，检查如果`NSURLSession`可供排队传输，并且使用正则后台任务来执行传输，如果不是：

```csharp
if ([NSURLSession class]) {
  // Create a background session and enqueue transfers
}
else {
  // Start a background task and transfer directly
  // Do NOT make calls to update the UI here!
}
```

> [!IMPORTANT]
> **请注意**： 避免进行调用来更新 UI。 iOS 6 符合代码中的背景，如 iOS 6 不支持背景 UI 更新和将终止应用程序。


`NSURLSession` API 包括一套丰富的功能来处理身份验证、 管理失败的传输和报告客户端-但不是服务器端的错误。 它有助于在任务中中断 iOS 7，在运行时引入的桥，并还提供对传输大型文件快速且可靠地支持。 下一节介绍了此第二个功能。

### <a name="background-transfer-service"></a>后台传输服务

在 iOS 7 之前, 上载或下载在后台的文件的不可靠。 后台任务获得有限的时间来运行，但若要将文件传输的时间因网络和文件的大小。 在 iOS 7，我们可以使用`NSURLSession`成功上载和下载大型文件。 特定`NSURLSession`处理在后台的大型文件的网络传输的会话类型被称为*后台传输服务*。

使用后台传输服务启动的传输由操作系统管理，并提供 Api，以便处理身份验证和错误。 传输不受任意的时间限制，因为它们可以用于上载或下载大文件，自动更新内容在后台，等等。 请参阅[后台传输演练](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md)有关如何实现服务的详细信息。

后台传输服务通常配合背景提取或远程通知，以帮助应用程序刷新在后台的内容。 在接下来的两部分中，我们引入注册在后台运行 iOS 6 和 iOS 7 上的整个应用程序的概念。

