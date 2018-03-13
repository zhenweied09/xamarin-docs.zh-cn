---
title: "在 Xamarin.Android 中绑定服务"
description: "绑定的服务是提供一个客户端 （如 Android 的活动） 可以与进行交互的客户端-服务器接口的 Android 服务。 本指南将讨论与创建绑定的服务以及如何在 Xamarin.Android 应用程序中使用它所涉及的关键组件。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 04307eab1bc8dc28fa69315809e254c920fb6d56
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="bound-services-in-xamarinandroid"></a>在 Xamarin.Android 中绑定服务

_绑定的服务是提供一个客户端 （如 Android 的活动） 可以与进行交互的客户端-服务器接口的 Android 服务。本指南将讨论与创建绑定的服务以及如何在 Xamarin.Android 应用程序中使用它所涉及的关键组件。_

## <a name="bound-services-overview"></a>绑定的服务概述

与服务提供客户端进行直接交互的客户端-服务器接口的服务被称为_绑定服务_。  可以有多个客户端在同一时间连接到服务的单个实例。 绑定的服务和客户端是相互隔离的。 相反，Android 提供一系列中间管理这两者之间的连接状态的对象。 此状态由对象实现[ `Android.Content.IServiceConnection` ](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/)接口。  此对象是由客户端创建，作为参数传递给传递[ `BindService` ](https://developer.xamarin.com/api/member/Android.Content.Context.BindService/)方法。 `BindService`可在上找到任何[ `Android.Content.Context` ](https://developer.xamarin.com/api/type/Android.Content.Context)对象 （如活动）。 这是一个请求到 Android 操作系统以启动服务并将客户端绑定到它。 有三种方法向客户端可能将绑定到服务使用`BindService`方法：

* **服务联编程序**&ndash;服务联编程序是一个类，实现[ `Android.OS.IBinder` ](https://developer.xamarin.com/api/type/Android.OS.IBinder)接口。 大多数应用程序将不会直接实现此接口，而是它们将扩展[ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder)类。 这是最常用的方法，适用于服务和客户端在同一进程的存在时。
* **使用 Messenger** &ndash;这种技术很适合当该服务可能存在一个单独进程中。 服务请求客户端和服务通过之间封送的相反， [ `Android.OS.Messenger` ](https://developer.xamarin.com/api/type/Android.OS.Messenger)。 [ `Android.OS.Handler` ](https://developer.xamarin.com/api/type/Android.OS.Handler)将处理的服务中创建`Messenger`请求。 这将在另一个的指南进行介绍。
* **使用 AIDL** &ndash;这袭击恐怖活动核心的大多数 Android 开发人员，并不将在本指南中介绍。

在客户端已绑定到服务，这两者之间的通信是通过`Android.OS.IBinder`对象。  此对象负责将允许客户端与服务交互的接口。 没有必要的每个 Xamarin.Android 应用程序实现此接口从零开始，Android SDK 提供[ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder)负责的大部分所需的之间的对象的封送处理代码的类客户端和服务。

客户端与服务完成操作后，它必须通过调用从它取消绑定`UnbindService`方法。 一旦从服务最后一个客户端未绑定 Android 将停止并释放绑定的服务。

此图说明了如何在活动、 服务连接、 联编程序和服务所有彼此相关：

![显示每个服务组件之间的关系的关系图](bound-services-images/bound-services-02.png "关系图显示每个服务组件之间的关系。")

本指南将讨论如何扩展`Service`类以实现绑定的服务。 它还将介绍实现`IServiceConnection`和扩展`Binder`允许客户端与服务进行通信。 示例应用程序附带本指南中，其中包含一个名为的单个 Xamarin.Android 项目与解决方案 **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)**  。 这是一个非常基本的应用程序，其演示了如何实现服务以及如何将活动绑定到它。 绑定的服务具有只有一个方法具有非常简单的 API `GetFormattedTimestamp`，这将返回一个字符串，告知用户，当服务已开始时，多长时间运行。 该应用还让用户手动取消绑定和绑定到服务。

[![在 Android 手机上运行的应用程序的屏幕截图](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>实现和使用的绑定的服务

有三个组件必须实现使 Android 应用程序使用的绑定的服务：

1. **扩展`Service`类，实现生命周期回调方法**&ndash;此类将包含的代码将执行将请求的服务的工作。 这将在下面更详细地介绍。
2. **创建一个类用于实现`IServiceConnection`**  &ndash;此对象包含从服务的回调方法，以通知客户端时它已连接到 （或已断开的连接）。 服务连接还将提供对客户端可以使用直接与服务交互的对象的引用。 此引用名为_联编程序_。
3. **创建一个类用于实现`IBinder`**  &ndash; A_联编程序_实现提供的 API 的客户端使用与服务进行通信。 联编程序可以提供对绑定的服务中，引用允许直接调用方法或联编程序可以提供客户端 API 可封装和隐藏从应用程序绑定的服务。 `IBinder`必须为远程过程调用提供必要的代码。 它不是必需的 （或推荐的） 来实现`IBinder`直接接口。 `IBinder`而不是应用程序应扩展`Binder`这提供了大部分所需的基本功能`IBinder`。
4. **启动和绑定到服务**&ndash;创建服务连接、 联编程序和服务后 Android 应用程序负责启动该服务并将绑定到它。

将以下各节更详细地讨论每个步骤。

### <a name="extend-the-service-class"></a>扩展`Service`类

若要创建使用 Xamarin.Android 的服务，则有必要进行子类`Service`和修饰的类具有[ `ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)。 该属性用于 Xamarin.Android 生成工具正确注册该服务中应用的**AndroidManifest.xml**文件非常类似于活动中，绑定的服务具有其自己的生命周期和回调的方法与关联在它的生命周期中的重要事件。 下面的列表是一些较常用服务将实现的回调方法的一个示例：

* `OnCreate` &ndash; 因为它实例化服务，Android 会调用此方法。 它用于初始化的任何变量或服务必须在其生存期期间的对象。 此方法是可选的。
* `OnBind` &ndash; 所有绑定的服务必须实现此方法。 当第一个客户端尝试连接到服务时，调用此操作。 它将返回的实例`IBinder`，以便客户端可能会与服务交互。 只要服务正在运行，`IBinder`对象将用于满足绑定到服务的任何将来的客户端请求。
* `OnUnbind` &ndash; 所有绑定的客户端具有未绑定时调用此方法。 通过返回`true`服务将更高版本调用此方法，从`OnRebind`使用传递给意向`OnUnbind`新客户端时绑定到它。 当服务继续运行未绑定之后，你将需要执行此操作。 如果最近未绑定的服务也已启动的服务，将发生此问题和`StopService`或`StopSelf`未调用。 在这种方案，`OnRebind`允许要检索的意图。 默认值返回`false`，其中不执行任何操作。 可选。
* `OnDestroy` &ndash; Android 销毁该服务时，调用此方法。 任何必要的清除，如释放资源，应在此方法中执行。 可选。

绑定的服务的密钥生命周期事件显示在此关系图中：

![关系图显示生命周期方法将调用的顺序](bound-services-images/bound-services-01.png "关系图显示生命周期方法将调用的顺序。")

从助理应用程序随附本指南中，下面的代码段演示如何在 Xamarin.Android 中实现的绑定的服务：

```csharp
using Android.App;
using Android.Util;
using Android.Content;
using Android.OS;

namespace BoundServiceDemo
{
    [Service(Name="com.xamarin.ServicesDemo1")]
    public class TimestampService : Service, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampService).FullName;
        IGetTimestamp timestamper;

        public IBinder Binder { get; private set; }

        public override void OnCreate()
        {
            // This method is optional to implement
            base.OnCreate();
            Log.Debug(TAG, "OnCreate");
            timestamper = new UtcTimestamper();
        }

        public override IBinder OnBind(Intent intent)
        {
            // This method must always be implemented
            Log.Debug(TAG, "OnBind");
            this.Binder = new TimestampBinder(this);
            return this.Binder;
        }

        public override bool OnUnbind(Intent intent)
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnUnbind");
            return base.OnUnbind(intent);
        }

        public override void OnDestroy()
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnDestroy");
            Binder = null;
            timestamper = null;
            base.OnDestroy();
        }

        /// <summary>
        /// This method will return a formatted timestamp to the client.
        /// </summary>
        /// <returns>A string that details what time the service started and how long it has been running.</returns>
        public string GetFormattedTimestamp()
        {
            return timestamper?.GetFormattedTimestamp();
        }
    }
}
```

在示例中，`OnCreate`方法初始化对象包含用于检索和格式设置的客户端将请求的时间戳的逻辑。 当第一个客户端尝试绑定到服务时，将调用 Android`OnBind`方法。 此服务将实例化`TimestampServiceBinder`将允许客户端访问正在运行的服务的此实例的对象。 `TimestampServiceBinder`在下一节中讨论类。

### <a name="implementing-ibinder"></a>实现 IBinder

如前文所述，`IBinder`对象提供客户端和服务之间的通信通道。 Android 应用程序不应实现`IBinder`接口， [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder/)应扩展。 `Binder`类提供了许多所需的基础结构，这是必要的封送到客户端从服务 （它可能在一个单独的进程中运行） 联编程序对象。 在大多数情况下，`Binder`子类是只几行代码并将包装对服务的引用。 在此示例中，`TimestampBinder`具有一个属性，公开`TimestampService`到客户端：

```csharp
public class TimestampBinder : Binder
{
    public TimestampBinder(TimestampService service)
    {
        this.Service = service;
    }

    public TimestampService Service { get; private set; }
}
```

这`Binder`使得来调用公共方法服务; 例如：

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

一次`Binder`已扩展，它是实现所必需`IServiceConnection`将所有内容都连接在一起。

### <a name="creating-the-service-connection"></a>创建服务连接

`IServiceConnection`将显示 | 引入 | 公开 | 连接`Binder`到客户端的对象。 除了实现`IServiceConnection`接口，类必须扩展`Java.Lang.Object`。 服务连接还应提供客户端可以访问某种方式`Binder`（并因此与绑定的服务通信）。

此代码摘自随附了示例项目的一种可能方式实现`IServiceConnection`:

```csharp
using Android.Util;
using Android.OS;
using Android.Content;

namespace BoundServiceDemo
{
    public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampServiceConnection).FullName;

        MainActivity mainActivity;
        public TimestampServiceConnection(MainActivity activity)
        {
            IsConnected = false;
            Binder = null;
            mainActivity = activity;
        }

        public bool IsConnected { get; private set; }
        public TimestampBinder Binder { get; private set; }

        public void OnServiceConnected(ComponentName name, IBinder service)
        {
            Binder = service as TimestampBinder;
            IsConnected = this.Binder != null;

            string message = "onServiceConnected - ";
            Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

            if (IsConnected)
            {
                message = message + " bound to service " + name.ClassName;
                mainActivity.UpdateUiForBoundService();
            }
            else
            {
                message = message + " not bound to service " + name.ClassName;
                mainActivity.UpdateUiForUnboundService();
            }

            Log.Info(TAG, message);
            mainActivity.timestampMessageTextView.Text = message;

        }

        public void OnServiceDisconnected(ComponentName name)
        {
            Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
            IsConnected = false;
            Binder = null;
            mainActivity.UpdateUiForUnboundService();
        }

        public string GetFormattedTimestamp()
        {
            if (!IsConnected)
            {
                return null;
            }

            return Binder?.GetFormattedTimestamp();
        }
    }
}

```

作为绑定过程的一部分，将调用 Android`OnServiceConnected`方法，提供`name`要绑定的服务和`binder`，保存对服务本身的引用。 在此示例中，服务连接都有两个属性，如果客户端连接到服务，或不包含对活页夹和为一个布尔型标志的引用的一个。

`OnServiceDisconnected`客户端和服务之间的连接意外丢失或损坏时，才会激活方法。 此方法允许客户端有机会响应服务中断。  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>启动并将绑定到具有显式意向的服务

若要使用的绑定的服务，一个客户端 （如活动） 必须实例化对象实现`Android.Content.IServiceConnection`并调用`BindService`方法。` BindService` 将返回`true`如果服务绑定到`false`如果它不是。 `BindService` 方法采用三个参数：

* **`Intent`**  &ndash;意向应显式标识要连接到的服务。
* **`IServiceConnection`对象**&ndash;此对象是中介，提供用于在绑定的服务启动和停止时通知客户端的回调方法。
* **[`Android.Content.Bind`](https://developer.xamarin.com/api/type/Android.Content.Bind/) 枚举**&ndash;此参数是一组标志是系统用于在绑定的对象。 最常用的值是[ `Bind.AutoCreate` ](https://developer.xamarin.com/api/field/Android.Content.Bind.AutoCreate/)，这将自动启动服务，如果它尚未运行。

下面的代码段演示了如何在使用显式意向活动启动绑定的服务：

```csharp
protected override void OnStart ()
{
    base.OnStart ();

    if (serviceConnection == null)
    {
        this.serviceConnection = new TimestampServiceConnection(this);
    }

    Intent serviceToStart = new Intent(this, typeof(TimestampService));
    BindService(serviceToStart, this.serviceConnection, Bind.AutoCreate);

}
```

> [!IMPORTANT]
> 启动 Android 5.0 （API 级别 21） 后它才可以将绑定到具有显式意向的服务。

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>有关服务连接以及在联编程序的体系结构说明。

某些 OOP 纯粹主义者可能否决的以前的实现的`TimestampBinder`类作为违反[Demeter 法律](https://en.wikipedia.org/wiki/Law_of_Demeter)，最简单窗体中指出"不通信到陌生人;仅与你的好友"。 此特定的实现公开具体`TimestampService`类向所有客户端。

严格地说，不需要客户端若想了解有关`TimestampService`和公开该类具体到客户端程序可以使应用程序更脆弱并且难以维护它的生存期内。 另一种方法是使用公开的接口`GetFormattedTimestamp()`方法，并对通过该服务的代理调用`Binder`（或可能的服务连接类）：  

```csharp
public class TimestampBinder : Binder, IGetTimesamp
{
    TimestampService service;
    public TimestampBinder(TimestampService service)
    {
        this.service = service;
    }

    public string GetFormattedTimestamp()
    {
        return service?.GetFormattedTimestamp();
    }
}
```

此特定示例允许用于对服务本身调用的方法的活动：

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```


## <a name="related-links"></a>相关链接

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.Content.Bind](https://developer.xamarin.com/api/type/Android.Content.Bind/)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [Android.Content.IServiceConnection](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/)
- [Android.OS.Binder](https://developer.xamarin.com/api/type/Android.OS.Binder/)
- [Android.OS.IBinder](https://developer.xamarin.com/api/type/Android.OS.IBinder)
- [BoundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/BoundServiceDemo/)
