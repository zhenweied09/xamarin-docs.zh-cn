---
title: "远程进程中运行 Android 服务"
description: "通常情况下，Android 应用程序中的所有组件将相同的进程中都运行。 Android 服务是值得注意的例外，因为它们可以配置为在其自己的过程中运行和与其他应用程序，包括来自其他 Android 开发人员共享。 本指南将讨论如何创建和使用使用 Xamarin Android 远程服务。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 26609043e872241a2ec4f878086b97b12b064e87
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="running-android-services-in-remote-processes"></a>远程进程中运行 Android 服务

_通常情况下，Android 应用程序中的所有组件将相同的进程中都运行。Android 服务是值得注意的例外，因为它们可以配置为在其自己的过程中运行和与其他应用程序，包括来自其他 Android 开发人员共享。本指南将讨论如何创建和使用使用 Xamarin Android 远程服务。_

## <a name="out-of-process-services-overview"></a>不足的过程服务概述

当应用程序启动时，Android 创建在其中运行该应用程序的进程。 通常情况下，所有组件应用程序将在都运行此一个过程。 Android 服务是值得注意的例外，因为它们可以配置为在其自己的过程中运行和与其他应用程序，包括来自其他 Android 开发人员共享。 这些类型的服务被称为_远程服务_或_进程外 services_。 这些服务的代码将包含在主应用程序; 作为相同 APK但是，当服务启动 Android 将创建只是该服务的新进程。 与此相反，应用程序的其余部分所在相同的进程中运行的服务有时简称为_本地服务_。

通常情况下，不需要应用程序，以实现远程服务。 本地服务是足够的 （和所需的） 在许多情况下的应用程序的要求。 进程外具有其自己的内存空间必须管理的 Android。 虽然这会引入到整个应用程序的系统开销更大，但在某些情况下，它可以是在其自己的进程中运行服务更有利：

1. **共享功能**&ndash;某些应用程序开发人员可能有多个应用程序和所有应用程序之间共享的功能。 打包在哪些将在其自己的进程中运行可以简化应用程序维护 Android 服务中该功能。 还有可能在其自己的独立 APK 服务打包并将其单独部署应用程序的其余部分。
2. **改进用户体验**&ndash;有两种可能的方法的进程外服务可以提高应用程序的用户体验。 第一种方法涉及到内存管理。 当垃圾回收 (GC) 周期时，GC 完成之前，Android 将暂停过程中的所有活动。 用户可能会发现已作为"口吃"或"jank"此暂停。 当服务在运行时它自己的进程也就越服务进程将暂停，不是应用程序的过程。 这一暂停将会大大降低向用户明显未暂停应用程序进程 （和用户界面）。

    其次，如果进程的内存需求量变得过大，Android 可能终止该进程以释放资源的设备。 如果服务具有很大的内存需求量，并且在用户界面与相同的进程中运行，然后 Android 强制回收这些资源时用户界面将关闭，强制用户启动应用程序。 但是，如果在其自己的进程中运行的服务关闭的情况下 android，UI 过程仍然受影响。 UI 可以将绑定 （和重新启动） 服务，是透明的用户，并恢复正常工作。

3. **提高应用程序性能**&ndash;的 UI 进程可能终止或关闭的情况下独立于服务进程。 将时间较长启动任务移动到进程外服务，它就可能 UI 的启动时间可能改进 （假设服务进程保持活动状态之间的时间启动 UI）。

在许多方面，在另一个进程中运行的服务的绑定是相同[绑定到本地服务](~/android/app-fundamentals/services/creating-a-service/bound-services.md)。 客户端将调用`BindService`来绑定 （和启动，如有必要） 服务。 `Android.OS.IServiceConnection`将创建对象来管理客户端和服务之间的连接。 如果客户端已成功将绑定到该服务，则 Android 将返回的对象通过`IServiceConnection`可以用于对服务调用的方法。 客户端然后使用此对象与服务交互。 若要查看，此处是绑定到服务的步骤：

* **创建为打算**&ndash;显式意向必须可用于对服务的绑定。
* **实现并实例化`IServiceConnection`对象** &ndash; `IServiceConnection`对象充当客户端和服务之间的中介。  它负责监视客户端和服务器之间的连接。
* **调用`BindService`方法**&ndash;调用`BindService`将调度意向和 Android，将会负责启动该服务并建立通信的前面步骤中创建的服务连接客户端和服务。

需要跨进程边界会引入额外的复杂性： 通信为单向 （客户端到服务器） 和客户端不能直接调用服务类上的方法。 回想一下，当服务作为客户端运行时相同的进程，提供了 Android`IBinder`这可能会让进行双向通信的对象。 这不是服务在其自己的进程中运行这种情况。 客户端通信的远程服务的帮助`Android.OS.Messenger`类。

当客户端请求与远程服务绑定时，将调用 Android`Service.OnBind`生命周期方法，它将返回内部`IBinder`包装的对象`Messenger`。 `Messenger`位于特殊的瘦包装器`IBinder`由 Android SDK 提供的实现。 `Messenger`负责的两个不同进程之间的通信。 开发人员是不涉及使用序列化消息跨进程边界的封送处理消息，然后在客户端上对它进行反序列化的详细信息。 此工作由`Messenger`对象。 此图显示当客户端启动对进程外服务的绑定时涉及的客户端 Android 组件：

![显示绑定到服务的客户端的步骤和组件的关系图](out-of-process-services-images/ipc-01.png "显示绑定到服务的客户端的步骤和组件的关系图。")

`Service`远程进程中的类将经历本地进程中的绑定的服务将经历的相同生命周期回调和许多相关的 Api 是相同的。 `Service.OnCreate` 用于初始化`Handler`和插入为`Messenger`对象。 同样，`OnBind`重写，但而不是返回`IBinder`对象，该服务将返回`Messenger`。  此图显示了发生在服务中时，客户端绑定到它：

![演示的步骤和组件的服务的关系图时要绑定到由远程客户端经历](out-of-process-services-images/ipc-02.png "时通过远程客户端绑定到经历演示的步骤和组件的服务的关系图。")

当`Message`收到由一种服务，它处理由的实例中`Android.OS.Handler`。 该服务将实现其自己`Handler`必须重写的子类`HandleMessage`方法。 通过调用此方法`Messenger`并接收`Message`作为参数。 `Handler`将检查`Message`元数据并使用该信息来对服务调用的方法。

当客户端创建单向通信时发生`Message`对象和调度到服务使用`Messenger.Send`方法。 `Messenger.Send` 将序列化`Message`和手动关闭序列化数据，到 Android，会将消息路由跨进程边界和服务。  `Messenger`由承载该服务将创建`Message`传入数据中的对象。 这`Message`放入队列，其中的消息是已提交一次一个地到`Handler`。 `Handler`将检查中包含的元数据`Message`上调用适当的方法和`Service`。 下图阐释了这些操作中的各种概念：

![显示如何将消息传递进程间的关系图](out-of-process-services-images/ipc-03.png "显示如何将消息传递进程间的关系图。")

本指南将讨论实现进程外服务的详细信息。 它将讨论如何实现用于在其自己的进程中运行的服务以及客户端与该服务使用可能通信的方式`Messenger`framework。 它将还简要讨论双向通信： 客户端将消息发送到服务和将消息发送回客户端服务。 因为服务可以在不同的应用程序之间共享，本指南还将讨论通过使用 Android 的权限来限制对服务的客户端访问的一种方法。

> [!IMPORTANT]
> [Bugzilla 51940-具有隔离的进程和自定义应用程序类的服务不能正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)Xamarin.Android 服务将不会启动正常的报表时`IsolatedProcess`设置为`true`。 本指南供参考。 Xamarin.Android 应用程序仍应能够与用 Java 编写的进程外服务进行通信。

## <a name="requirements"></a>惠?

本指南假定你熟悉创建服务。

尽管可以将用于应用程序面向较旧的隐式意向 Android Api，本指南将重点介绍以独占方式使用显式意向。 面向 Android 5.0 （API 级别 21） 应用或更高版本必须使用为显式打算将绑定与服务;这是将本指南中演示的技术...

## <a name="create-a-service-that-runs-in-a-separate-process"></a>创建在一个单独的进程中运行的服务

按上文所述，服务在其自己的进程中运行的事实意味着不同的一些 Api 是涉及。 作为的快速概述，以下是使用将绑定和使用远程服务的步骤：  

* **创建`Service`子类**&ndash;子类`Service`键入和实现的绑定的服务的生命周期方法。 还有必要将元数据，用于将通知服务将在其自己的进程中运行的 Android 设置。
* **实现`Handler`**  &ndash; `Handler`负责分析客户端请求、 提取来自客户端，传递任何参数和调用服务上的适当方法。
* **实例化`Messenger`**  &ndash;如上所述，每个`Service`必须保留的实例`Messenger`将客户端将请求路由到的类`Handler`上一步中创建。

要在其自己的进程中运行的服务，从根本上说，仍然是绑定的服务。 服务类将扩展基`Service`类并使用修饰`ServiceAttribute`包含 Android 需要捆绑 Android 清单中的元数据。 以开头的下列属性`ServiceAttribute`对进程外服务很重要的：

1. `Exported` &ndash; 此属性必须设置为`true`以允许其他应用程序与服务交互。 此属性的默认值为 `false`。
2. `Process` &ndash; 必须设置此属性。 它用于指定该服务将在运行进程的名称。
3. `IsolatedProcess` &ndash; 此属性将启用额外的安全性，告知在最小权限的系统的其余部分一起 iteract 独立沙盒中运行服务的 Android。 请参阅[Bugzilla 51940-服务具有隔离的进程和自定义应用程序类不能正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)。
4. `Permission` &ndash; 可通过指定客户端必须请求 （并被授予） 的权限控制对服务的客户端访问。

若要运行其自己的进程的服务`Process`属性`ServiceAttribute`必须设置为服务的名称。 与外部应用程序交互`Exported`属性应设置为`true`。 如果`Exported`是`false`，则相同 APK （即相同应用程序） 中的唯一客户端和运行在同一进程中将能够与服务进行交互。

该服务将在运行的进程哪种依赖于的值`Process`属性。 Android 标识三种不同的进程：

-   **私有过程**&ndash;专用流程是指才可用的应用程序启动它。 若要标识为私有的进程，其名称必须启动**:** （用分号）。 图中的上一个代码段和屏幕截图所示的服务是一个专用的过程。 下面的代码段演示了`ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

-   **全局进程**&ndash;在全局进程中运行的服务是在设备上运行的所有应用程序可以访问。 全局过程必须是小写字母字符开头的完全限定的类名。
    （除非采取步骤来保护服务，其他应用程序可能将绑定和与之进行交互。 针对未经授权的使用服务的安全将讨论在本指南的后面部分。）

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

-   **隔离进程**&ndash;隔离的进程是在隔离的其余部分的系统和具有自己的任何特殊权限从其自己沙盒中运行的进程。 在隔离的进程中运行服务`IsolatedProcess`属性`ServiceAttribute`设置为`true`此代码段中所示：
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> 请参阅[Bugzilla 51940-服务具有隔离的进程和自定义应用程序类不能正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

隔离的服务是一种简单的方法，以保护应用程序和设备免受不受信任的代码。 例如，应用可能只下载，并从网站中执行脚本。 在这种情况下，在隔离的进程中执行这提供了额外的安全性，以防止不受信任的代码会影响在 Android 设备。

> [!IMPORTANT]
> 一旦被导出服务，不应更改服务的名称。 更改服务的名称可能会中断其他应用程序使用的服务。

若要查看的效果，`Process`属性具有，以下屏幕截图显示在其自己专用的进程中运行的服务：

![演示在专用的进程中运行的服务的屏幕截图](out-of-process-services-images/ipc-04.png "演示在专用的进程中运行的服务的屏幕截图。")

此下一步的屏幕快照显示`Process="com.xamarin.xample.messengerservice.timestampservice_process"`和全局过程中运行的服务：

![在全局进程中运行的服务的屏幕截图](out-of-process-services-images/ipc-05.png "全局的进程中运行的服务的屏幕截图。")

一次`ServiceAttribute`已设置，需要实现该服务`Handler`。

### <a name="implementing-a-handler"></a>实现一个处理程序

若要处理客户端请求，服务必须实现`Handler`，并重写`HandleMessage`methodThis 是方法采用`Message`实例哪些其封装来自客户端的方法调用和将转换为某种操作该调用或此服务将执行的任务。 `Message`对象公开一个名为属性`What`这是一个整数值，其含义客户端和服务之间共享以及与服务将为客户端执行某些任务相关。

下面的代码段从示例应用程序显示的一个示例`HandleMessage`。 在此示例中，有两个操作可以请求客户端的服务：

* 第一项操作_Hello，World_消息，客户端已将简单信息发送到服务。
* 第二个操作将调用服务上的方法和检索字符串，字符串在此情况下是一条消息，返回已运行的服务已启动并在多长时间的时间：

```csharp
public class TimestampRequestHandler : Android.OS.Handler
{
    // other code omitted for clarity

    public override void HandleMessage(Message msg)
    {
        int messageType = msg.What;
        Log.Debug(TAG, $"Message type: {messageType}.");

        switch (messageType)
        {
            case Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE:
                // The client as sent a simple Hello, say in the Android Log.
                break;

            case Constants.GET_UTC_TIMESTAMP:
                // Call methods on the service to retrive a timestamp message.
                break;
            default:
                Log.Warn(TAG, $"Unknown messageType, ignoring the value {messageType}.");
                base.HandleMessage(msg);
                break;
        }
    }
}
```

还有可能为中的服务包参数`Message`。 这将在本指南的后面部分讨论。 创建要考虑的下一步主题`Messenger`对象来处理传入`Message`s。

### <a name="instantiating-the-messenger"></a>实例化 Messenger

如前所述，反序列化`Message`对象并调用`Handler.HandleMessage`是的 responsibilty`Messenger`对象。 `Messenger`类还提供了`IBinder`对象客户端将用于向服务发送消息。  

当服务启动时，它会实例`Messenger`和注入`Handler`。 要执行此初始化一个好位于`OnCreate`服务的方法。 此代码片段是初始化其自己的服务的一个示例`Handler`和`Messenger`:

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

此时，最后一步是为`Service`重写`OnBind`。

### <a name="implementing-serviceonbind"></a>实现 Service.OnBind

所有绑定的服务，它们在它们自己的进程中运行或运行不是，是否必须实现`OnBind`方法。 此方法的返回值是某些客户端可用来与服务交互的对象。 完全该对象是什么依赖的服务已本地服务或远程服务。 虽然本地服务将返回自定义`IBinder`实现中，远程服务将返回`IBinder`，封装但`Messenger`创建上一节中：

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

远程服务后可以实现以下三个步骤，便可被视为完成。

## <a name="consuming-the-service"></a>使用服务

所有客户端必须实现一些代码以能够将绑定和使用远程服务。 从概念上讲，从客户端的角度来看，很少之间有差异对本地服务或远程服务绑定。 客户端时，将调用`BindService`方法，并传递为显式打算用于标识服务和`IServiceConnection`可帮助管理客户端和服务之间的连接。

此代码片段演示了如何创建**显式意向**向远程服务绑定。 意图必须标识的包，包含服务和服务的名称。 设置此信息的一种方法是使用`Android.Content.ComponentName`对象并提供给意图。 此代码段是一个示例：  

```csharp
// This is the package name of the APK, set in the Android manifest
const string REMOTE_SERVICE_COMPONENT_NAME = "com.xamarin.TimestampService";
// This is the name of the service, according the value of ServiceAttribute.Name
const string REMOTE_SERVICE_PACKAGE_NAME   = "com.xamarin.xample.messengerservice";

// Provide the package name and the name of the service with a ComponentName object.
ComponentName cn = new ComponentName(REMOTE_SERVICE_PACKAGE_NAME, REMOTE_SERVICE_COMPONENT_NAME);
Intent serviceToStart = new Intent();
serviceToStart.SetComponent(cn);
```

当服务绑定时，`IServiceConnection.OnServiceConnected`方法调用，并且提供`IBinder`到客户端。 但是，客户端将不直接使用`IBinder`。 相反，它将实例化`Messenger`中的对象`IBinder`。 这是`Messenger`客户端将用来与远程服务进行交互。

下面是非常基本的一个示例`IServiceConnection`演示客户端将如何处理连接到和从服务断开连接的实现。 请注意，`OnServiceConnected`方法接收和`IBinder`，和客户端创建`Messenger`于`IBinder`:

```csharp
public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection
{
    static readonly string TAG = typeof(TimestampServiceConnection).FullName;

    MainActivity mainActivity;
    Messenger messenger;

    public TimestampServiceConnection(MainActivity activity)
    {
        IsConnected = false;
        mainActivity = activity;
    }

    public bool IsConnected { get; private set; }
    public Messenger Messenger { get; private set; }

    public void OnServiceConnected(ComponentName name, IBinder service)
    {
        Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

        IsConnected = service != null
        Messenger = new Messenger(service);

        if (IsConnected)
        {
            // things to do when the connection is successful. perhaps notify the client? enable UI features?
        }
        else
        {
            // things to do when the connection isn't successful.
        }
    }

    public void OnServiceDisconnected(ComponentName name)
    {
        Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
        IsConnected = false;
        Messenger = null;

        // Things to do when the service disconnects. perhaps notify the client? disable UI features?

    }
}
```

一旦创建了服务连接和意向，则客户端调用可能`BindService`和启动绑定过程：

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

客户端已成功绑定到的服务后和`Messenger`是可用的可能会在客户端发送`Messages`到服务。

## <a name="sending-messages-to-the-service"></a>将消息发送到服务

只要客户端连接，并具有`Messenger`对象，它可以与服务通信通过调度`Message`对象通过`Messenger`。 此通信为单向通信，客户端发送消息但没有向客户端从服务返回消息。 在这一方面，`Message`是即发即弃机制。

若要创建的首选的方式`Message`对象是使用[ `Message.Obtain` ](https://developer.xamarin.com/api/type/Android.OS.Message/#Public%20Methods)工厂方法。 此方法将请求`Message`从全局池由 Android 维护的对象。 `Message.Obtain` 此外包含一些的重载方法，允许`Message`要使用的值和服务所需的参数初始化对象。  一次`Message`是实例化，它调度到服务通过调用`Messenger.Send`。 此代码段是一个示例创建和调度`Message`到服务进程：

```csharp
Message msg = Message.Obtain(null, Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE);
try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a error trying to send the message.");
}
```

有几种不同形式的`Message.Obtain`方法。 上一个示例使用[ `Message.Obtain(Handler h, Int32 what)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/)。 因为这是发出异步请求到的进程外服务;将该服务，无响应因此`Handler`设置为`null`。 第二个参数， `Int32 what`，将存储在`.What`属性`Message`对象。 `.What`服务进程中的代码使用属性来调用服务上的方法。

`Message`类还公开两个其他属性，可能会利用收件人：`Arg1`和`Arg2`。 这两个属性是可能有一些特殊统一的客户端和服务之间的含义的值的整数值。 例如，`Arg1`可以容纳客户 ID 和`Arg2`可以容纳该客户采购订单号。 [ `Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/System.Int32/System.Int32/)可以用来设置两个属性时`Message`创建。 另一种方法来填充这两个值是将设置`.Arg`和`.Arg2`属性直接基于`Message`对象已被创建后。

### <a name="passing-additional-values-to-the-service"></a>传递到服务的其他值

可以通过将更复杂的数据传递给服务`Bundle`。 在这种情况下，多余的值可以放置在`Bundle`和以及发送`Message`通过设置[`.Data`属性](https://developer.xamarin.com/api/property/Android.OS.Message.Data/)之前发送的属性。

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```


> [!NOTE]
> 一般情况下，`Message`不应有负载大于 1 MB。 大小限制可能会有所不同，根据的 Android 版本并上专有的任何更改可能会对其实现的 Android 开放源项目 (AOSP)，与设备捆绑做出供应商。

## <a name="returning-values-from-the-service"></a>从服务返回值

为单向通信的消息传递体系结构，我们已经讨论了到目前为止，客户端向服务发送一条消息。 如果有必要则服务无法将值返回给客户端然后我们已经讨论了到目前为止的所有内容将被反转。 该服务必须创建`Message`，封装任何返回值和调度`Message`通过`Messenger`到客户端。 但是，服务不创建其自己`Messenger`; 相反，它依赖于客户端实例化和包`Messenger`初始请求的一部分。 该服务会`Send`使用此提供客户端的消息`Messenger`。  

这是双向通信的事件的顺序：

1. 客户端将绑定到该服务。 当服务和客户端连接时，`IServiceConnection`由维护该客户端将具有对引用`Messenger`用于传输对象`Message`s 到服务。 为了避免混淆，这将称为_服务 Messenger_。
2. 客户端实例化`Handler`(称为_客户端处理程序_) 并使用该值来初始化其自己`Messenger`(_客户端 Messenger_)。 请注意，服务 Messenger 和客户端 Messenger 处理在两个不同的方向的流量的两个不同的对象。 服务 Messenger 处理消息从客户端到服务，而客户端 Messenger 将处理从服务向客户端的消息。
3. 客户端创建`Message`对象，并设置`ReplyTo`具有客户端 Messenger 属性。 然后，消息是发送到使用服务 Messenger 的服务。
4. 服务从客户端，接收消息，并执行请求的工作。
5. 服务以发送到客户端的响应时间时，它将使用`Message.Obtain`创建新`Message`对象。
6. 若要向客户端发送此消息，服务将提取从客户端 Messenger`.ReplyTo`属性的客户端消息，然后使用，则为`.Send``Message`回客户端。
7. 当客户端收到响应时，它具有其自己`Handler`，它将处理`Message`通过检查`.What`属性 (如有必要，提取包含的任何参数和`Message`)。

此代码示例演示如何对客户端将实例化`Message`并打包`Messenger`服务应使用用于响应：

```csharp
Handler clientHandler = new ActivityHandler();
Messenger clientMessenger = new Messenger(activityHandler);

Message msg = Message.Obtain(null, Constants.GET_UTC_TIMESTAMP);
msg.ReplyTo = clientMessenger;

try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a problem sending the message.");
}
```

服务必须进行一些更改到其自身`Handler`提取`Messenger`并使用它来发送到客户端的答复。 此代码片段演示了如何服务的`Handler`将创建`Message`并将其发送回客户端：  

```csharp
// This is the message that the service will send to the client.
Message responseMessage = Message.Obtain(null, Constants.RESPONSE_TO_SERVICE);
Bundle dataToReturn = new Bundle();
dataToReturn.PutString(Constants.RESPONSE_MESSAGE_KEY, "This is the result from the service.");
responseMessage.Data = dataToReturn;

// The msg object here is the message that was received by the service. The service will not instantiate a client,
// It will use the client that is encapsulated by the message from the client.
Messenger clientMessenger = msg.ReplyTo;
if (clientMessenger!= null)
{
    try
    {
        clientMessenger.Send(responseMessage);
    }
    catch (Exception ex)
    {
        Log.Error(TAG, ex, "There was a problem sending the message.");
    }
}
```

请注意，在上面的代码示例`Messenger`由客户端创建的实例是*不*服务接收到的相同对象。 这些是两个不同`Messenger`在两个表示通信通道的单独进程中运行的对象。

## <a name="securing-the-service-with-android-permissions"></a>保护具有 Android 权限的服务

可通过该 Android 设备上运行的所有应用程序进行访问的全局进程中运行的服务。 在某些情况下，此开放性和可用性是不可取，并需要对访问服务免遭未经授权的客户端。 限制对远程服务的访问的一种方法是使用 Android 的权限。

权限可以由标识`Permission`属性`ServiceAttribute`修饰`Service`其子类。 这将名称绑定到该服务时，必须授予客户端的权限。 如果客户端没有适当的权限，则将引发 Android`Java.Lang.SecurityException`客户端尝试绑定到服务。

有四个不同的权限级别，Android 提供：

* **正常**&ndash;这是默认权限级别。 它用于标识可自动授予 android 请求它的客户端的较低的风险权限。 用户不必显式授予这些权限，但可以在应用程序设置中查看权限。
* **签名**&ndash;这是将被授予自动 android 所有签名使用同一证书的应用程序的权限的特殊类别。 此权限旨在使其可轻松地为应用程序开发人员共享组件或其应用没有仔细常量审批的用户之间的数据。
* **signatureOrSystem** &ndash;它非常类似于**签名**上面所述的权限。 除了成为自动授予对由相同的证书签名的应用，此权限将也将授予进行签名的应用相同的证书用于签署应用安装与 Android 系统映像。 此权限通常仅用于 Android ROM 开发人员通过允许他们使用第三方应用的应用程序。 它不常使用的为了在大型则公众的常规分发的应用。
* **危险**&ndash;危险权限是指那些可能会导致用户的问题。 为此，**危险**必须由用户显式审批权限。

因为`signature`和`normal`权限会自动将授予在安装时 android，这一点至关重要，安装 APK 承载服务**之前**APK 包含客户端。 如果第一次安装客户端，则 Android 将不会授予的权限。 在这种情况下，它将必须卸载客户端 APK、 安装服务 APK，，然后重新安装客户端 APK。

有两种常见的方式使用 Android 权限的服务安全：

1.  **实现签名级别安全性**&ndash;签名级安全性意味着，权限被自动授予这些应用程序使用相同的密钥用于签署持有服务 APK 签名。 这是一种面向开发人员保护其服务，使其保持为可从他们自己的应用程序访问的简单方法。 通过设置声明签名级别权限`Permission`属性`ServiceAttribute`到`signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2.  **创建自定义权限**&ndash;很可能该服务的开发人员用来创建服务的自定义权限。 这是最适合于开发人员需要与来自其他开发人员的应用程序共享其服务。 自定义权限需要更多的工作来实现，并将下面介绍。

创建自定义的简化的示例`normal`将在下一节中介绍的权限。 有关 Android 权限的详细信息，请参阅 Google 文档[最佳做法和安全](https://developer.android.com/training/articles/security-tips.html)。 有关 Android 权限的详细信息，请参阅[权限部分](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)的有关 Android 权限的详细信息的应用程序清单的 Android 文档。

> [!NOTE]
> 一般情况下， [Google 不鼓励使用自定义权限](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions)因为它们可能会给用户造成混淆。

### <a name="creating-a-custom-permission"></a>创建自定义权限

若要使用的自定义权限，来声明它的服务时客户端显式请求该权限。

在服务 APK 创建权限`permission`元素添加到`manifest`中的元素**AndroidManifest.xml**。 必须具有此权限`name`， `protectionLevel`，和`label`属性集。 `name`属性必须设置为一个字符串，唯一标识该权限。 名称将显示在**应用程序信息**视图**Android 设置**（如在下一部分中所示）。

`protectionLevel`属性必须设置为上面所述的四个字符串值之一。  `label`和`description`必须引用字符串资源，用于向用户提供的用户友好名称和描述。

此代码段是一个示例声明一个自定义的`permission`属性中**AndroidManifest.xml**的 APK 是包含该服务：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerservice">

    <uses-sdk android:minSdkVersion="21" />

    <permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP"
                android:protectionLevel="signature"
                android:label="@string/permission_label"
                android:description="@string/permission_description"
                />

    <application android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">

    </application>
</manifest>
```

然后， **AndroidManifest.xml**的客户端 APK 必须显式请求此新权限。 这可通过添加`users-permission`属性设为**AndroidManifest.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerclient">

    <uses-sdk android:minSdkVersion="21" />

    <uses-permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP" />

    <application
            android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">
    </application>
    </manifest>
```

### <a name="view-the-permissions-granted-to-an-app"></a>查看到应用程序授予的权限

若要查看应用程序已被授予的权限，请打开 Android 设置应用程序，并选择**应用**。 查找和选择列表中的应用程序。 从**应用程序信息**屏幕上，点击**权限**这会弹出显示所有应用程序授予的权限的视图：

[![从显示如何查找应用程序授予的权限的 Android 设备的屏幕截图](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png)

## <a name="summary"></a>摘要

本指南是有关如何在远程进程中运行 Android 的服务的高级的讨论。 本地和远程服务之间的差异介绍过的方法，以及为什么远程服务可以是对稳定性和性能的 Android 应用程序帮助的一些原因。 说明如何实现远程服务和客户端可以在与服务进行通信后, 指南发往提供一种方法来限制访问的服务仅从授权客户端。


## <a name="related-links"></a>相关链接

- [Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Message](https://developer.xamarin.com/api/type/Android.OS.Message/)
- [Messenger](https://developer.xamarin.com/api/type/Android.OS.Messenger/)
- [ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)
- [导出属性](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [具有隔离的进程和自定义应用程序类的服务不能正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [进程和线程](https://developer.android.com/guide/components/processes-and-threads.html)
- [Android 清单的权限](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [安全提示](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/MessengerServiceDemo/)
