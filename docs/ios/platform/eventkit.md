---
title: EventKit
description: "本指南提供有关如何访问和处理日历、 CalendarEvents 和提醒存储的数据在日历数据库中，通过 EventKit 公开的概述。 它涉及主要的类以及在 EventKit 编程以及大量与 EventKit framework 关联的常见任务中的角色。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: db3662db50d8f3538f16f2af1f9e7880957dc25c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="eventkit"></a>EventKit

_本指南提供有关如何访问和处理日历、 CalendarEvents 和提醒存储的数据在日历数据库中，通过 EventKit 公开的概述。它涉及主要的类以及在 EventKit 编程以及大量与 EventKit framework 关联的常见任务中的角色。_

iOS 具有两个日历相关的应用程序内置： 日历应用程序中，和提醒应用程序。 它足够简单，若要了解如何日历应用程序管理日历数据，但提醒应用程序是不太明显。 提醒可能会遇到这样时它们到期，当它们在完成后，与它们关联的日期等。在这种情况下，iOS 日历将所有数据都存储，无论是日历事件或提醒，在一个位置，调用*日历数据库*。

EventKit framework 提供了一种访问方法*日历*，*日历事件*，和*提醒*日历数据库存储的数据。 访问对日历和日历事件后的可用 iOS 4，但对提醒的访问是 iOS 6 中的新增功能。

在本指南中我们将介绍：

-   **EventKit 基础知识**– 这将引入的主要类通过 EventKit 的基础部分，并提供了解它们的用法。 需要在处理文档的下一部分之前阅读此部分。 
-   **常见任务**– 常见的任务部分用于如何如执行常见操作的快速参考; 枚举日历、 创建、 保存和检索日历事件和提醒，以及使用的内置控制器创建和修改日历事件。 本部分需要读取从前到后，如其目的是为特定任务的引用。 


本指南中的所有任务的配套示例应用中都有：

 [ ![](eventkit-images/01.png "配套示例应用程序屏幕")](eventkit-images/01.png)

## <a name="requirements"></a>惠?

在 iOS 4.0 中引入了 EventKit 但 iOS 6.0 中引入了对提醒数据的访问。 在这种情况下，为常规 EventKit 开发，你将需要至少目标版本 4.0 和提醒的 6.0。

此外，提醒应用程序不是在模拟器中，这意味着，提醒数据将还不可用，除非你先将他们添加上可用的。 此外，到实际设备上的用户仅显示访问请求。 在这种情况下，最好在设备上测试 EventKit 开发。

## <a name="event-kit-basics"></a>事件工具包基础知识

在使用 EventKit，务必已掌握的公共类和它们的用法。 所有这些类可在`EventKit`和`EventKitUI`(有关`EKEventEditController`)。

### <a name="eventstore"></a>EventStore

*EventStore*类是 EventKit 中的最重要类，因为它是必需 EventKit 中执行任何操作。 它可以被视为持久存储或数据库引擎，为所有 EventKit 数据。 从`EventStore`有权访问的日历和日历应用程序中的日历事件以及提醒应用程序中的提醒。

因为`EventStore`是与数据库引擎，一样，它应是一种生存期很长，这意味着，应创建并应用程序实例的生存期内销毁降至最低。 事实上，建议你，一旦你创建的一个实例`EventStore`的应用程序，使保持围绕该引用的整个生存期内应用程序，除非您确定不会再次需要它。 此外，所有调用应都转到单个`EventStore`实例。 为此，单独模式被建议用于保持可用的单个实例。

#### <a name="creating-an-event-store"></a>创建事件存储

下面的代码演示一种高效的方式创建的单个实例`EventStore`类并将其提供静态从应用程序中：

```csharp
public class App
{
    public static App Current {
            get { return current; }
    }
    private static App current;

    public EKEventStore EventStore {
            get { return eventStore; }
    }
    protected EKEventStore eventStore;

    static App ()
    {
            current = new App();
    }
    protected App () 
    {
            eventStore = new EKEventStore ( );
    }
}
```

上面的代码中使用的单一实例模式来实例化的实例`EventStore`时应用程序加载。 `EventStore`可以然后全局从访问应用程序中，如下所示：

```csharp
App.Current.EventStore;
```

请注意，此处中的所有示例将都使用此模式中，以便它们引用`EventStore`通过`App.Current.EventStore`。

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>请求访问日历和提醒数据

然后才允许访问通过 EventStore 任何数据，应用程序必须首先请求访问日历事件数据或具体取决于所需的提醒数据。 若要简化该过程，`EventStore`公开一种称为方法`RequestAccess`其中-调用时-将向用户告知他们的应用程序正在请求访问日历数据或提醒数据，具体取决于哪些显示警报视图`EKEntityType`传递给它。 它会发出警报视图，因为调用是异步的并且将调用作为传递完成处理程序`NSAction`（或 Lambda） 到它，从而将收到两个参数; 布尔值是否已授予访问权限，和`NSError`，后者，如果将非 null包含在请求中的任何错误信息。 例如，以下编码将请求对日历事件数据和显示警报查看如果请求未被授予的访问。

```csharp
App.Current.EventStore.RequestAccess (EKEntityType.Event, 
    (bool granted, NSError e) => {
            if (granted)
                    //do something here
            else
                    new UIAlertView ( "Access Denied", 
"User Denied Access to Calendar Data", null,
"ok", null).Show ();
            } );
```

一旦已授予请求，将被记住，只要该应用程序在设备上安装，并且将不会弹出向用户警报。
但是，访问权限仅授予对资源，日历事件或提醒授予的类型。 如果应用程序所需访问权限，它应将两者都要求。

由于将记住权限，它是相对比较便宜发出请求每次，所以最好始终在执行操作之前请求的访问权限。

此外，因为在独立 (非 UI) 线程上调用完成处理程序时，完成处理程序中的 UI 的任何更新应该调用通过`InvokeOnMainThread`，否则将引发异常，并且如果没有捕获，该应用程序将崩溃。

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` 是一个枚举，描述的类型`EventKit`项或数据。 它具有两个值：`Event`和提醒。 采用多种方法，包括使用`EventStore.RequestAccess`告诉`EventKit`什么类型的数据来获取对访问权限或检索。

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar*表示日历，其中包含一组日历事件。 日历可以存储在大量的不同位置，如在本地， *iCloud*中的第三方提供程序位置如*Exchange Server*或*Google*等。多次`EKCalendar`用于告知`EventKit`位置查看的事件或将它们保存到的位置。

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController*可以位于`EventKitUI`命名空间，并且是内置控制器可以用于编辑或创建日历事件。 非常像内置相机控制器`EKEventEditController`会为你显示 UI 和处理保存完成的繁重任务。

### <a name="ekevent"></a>EKEvent

 *EKEvent*表示日历事件。 同时`EKEvent`和`EKReminder`继承`EKCalendarItem`和具有字段，如`Title`， `Notes`，依次类推。

### <a name="ekreminder"></a>EKReminder

 *EKReminder*表示提醒项。

### <a name="ekspan"></a>EKSpan

*EKSpan*是一个枚举，描述时修改可以重复发生，并且具有两个值的事件的事件的跨度： *ThisEvent*和*FutureEvents*。 `ThisEvent` 意味着，任何更改将仅发生到正在引用的系列中的特定事件而`FutureEvents`会影响该事件和所有将来的重复执行。

## <a name="tasks"></a>任务

为了便于使用，EventKit 用法具有已分解成下列部分中所述的常见任务。

### <a name="enumerate-calendars"></a>枚举日历

若要枚举在设备配置了用户的日历，调用`GetCalendars`上`EventStore`并传递你希望接收的日历 （提醒或事件） 的类型：

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>添加或修改使用内置的控制器的事件

*EKEventEditViewController*为你执行完成的繁重任务了很多，如果你想要创建或编辑具有同一 UI，使用日历应用程序时，向用户显示的事件：

 [ ![](eventkit-images/02.png "使用日历应用程序时，向用户显示 UI")](eventkit-images/02.png)

若要使用它，你需要将其声明为一个类级变量，以便它不获取垃圾回收如果它在方法内声明：

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

然后，以启动它： 其实例化，为其提供对引用`EventStore`，连接*EKEventEditViewDelegate*委托给它，，然后显示使用`PresentViewController`:

```csharp
EventKitUI.EKEventEditViewController eventController = 
        new EventKitUI.EKEventEditViewController ();

// set the controller's event store - it needs to know where/how to save the event
eventController.EventStore = App.Current.EventStore;

// wire up a delegate to handle events from the controller
eventControllerDelegate = new CreateEventEditViewDelegate ( eventController );
eventController.EditViewDelegate = eventControllerDelegate;

// show the event controller
PresentViewController ( eventController, true, null );
```

（可选） 如果你想要预先填充事件，你可以创建一个全新的事件 （如下所示），也可以检索已保存的事件：

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and exercise!";
newEvent.Notes = "This is your reminder to go and exercise for 30 minutes.”;
```

如果你希望预填充用户界面，请确保在控制器上设置的事件属性：

```csharp
eventController.Event = newEvent;
```

若要使用的现有事件，请参阅*检索按 ID 事件*稍后部分。

委托应重写`Completed`方法，该用户完成与对话框时调用由控制器方法：

```csharp
protected class CreateEventEditViewDelegate : EventKitUI.EKEventEditViewDelegate
{
        // we need to keep a reference to the controller so we can dismiss it
        protected EventKitUI.EKEventEditViewController eventController;

        public CreateEventEditViewDelegate (EventKitUI.EKEventEditViewController eventController)
        {
                // save our controller reference
                this.eventController = eventController;
        }

        // completed is called when a user eith
        public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
        {
                eventController.DismissViewController (true, null);
                }
        }
}
```

（可选） 在委托中，你可以检查*操作*中`Completed`方法可以修改的事件和重新保存，或执行其他操作，如果它已取消、 etcetera:

```csharp
public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
{
        eventController.DismissViewController (true, null);

        switch ( action ) {

        case EKEventEditViewAction.Canceled:
                break;
        case EKEventEditViewAction.Deleted:
                break;
        case EKEventEditViewAction.Saved:
                // if you wanted to modify the event you could do so here,
// and then save:
                //App.Current.EventStore.SaveEvent ( controller.Event, )
                break;
        }
}
```

### <a name="creating-an-event-programmatically"></a>以编程方式创建事件

若要在代码中创建事件，请使用*FromStore*上的工厂方法`EKEvent`类，并在其上设置任何数据：

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and do some exercise!";
newEvent.Notes = "This is your motivational event to go and do 30 minutes of exercise. Super important. Do this.";
```

你必须设置你想保存在中，该事件的日历，但如果你有没有首选项，则可以使用默认值：

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

若要保存的事件，调用*SaveEvent*方法`EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

保存后， *EventIdentifier*属性将更新为可以在稍后用于检索的事件的唯一标识符：

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` 是一个字符串格式的 GUID。

### <a name="create-a-reminder-programmatically"></a>以编程方式创建一条提醒

在代码中创建一条提醒是大致相同创建日历事件：

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

若要保存，请调用*SaveReminder*方法`EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>检索 ID 的事件

若要检索其事件 ID，请使用*EventFromIdentifier*方法`EventStore`并将其传递`EventIdentifier`，从其拉入事件：

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

对于事件，没有两个其他标识符属性，但`EventIdentifier`是适用于这只有一个。

### <a name="retrieving-a-reminder-by-id"></a>检索按 ID 提醒

若要检索一条提醒，使用*GetCalendarItem*方法`EventStore`并将其传递*CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

因为`GetCalendarItem`返回`EKCalendarItem`，必须强制转换为`EKReminder`如果需要访问提醒数据或使用实例作为`EKReminder`更高版本。

不要使用`GetCalendarItem`对于日历事件，如在撰写本文之际，它不起作用。

### <a name="deleting-an-event"></a>删除事件

若要删除日历事件，调用*RemoveEvent*上你`EventStore`和传递对该事件，且相应的引用`EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

请注意但是，事件已被删除后，将会将事件引用`null`。

### <a name="deleting-a-reminder"></a>删除一条提醒

若要删除一条提醒，调用*RemoveReminder*上`EventStore`和传递对提醒的引用：

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

请注意，在上述代码中没有强制转换为`EKReminder`，这是因为`GetCalendarItem`用于检索它

### <a name="searching-for-events"></a>搜索事件

若要搜索的日历事件，必须创建*NSPredicate*对象通过*PredicateForEvents*方法`EventStore`。 `NSPredicate`是查询数据对象该 iOS 使用查找匹配项：

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

创建后`NSPredicate`，使用*EventsMatching*方法`EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

请注意，查询是同步 （阻塞），并且可能需要很长时间，具体取决于查询，因此你可能想要启动新线程或任务以执行此操作。

### <a name="searching-for-reminders"></a>搜索提醒

搜索提醒非常类似于事件;它需要一个谓词，但调用已是异步的因此不需要担心阻止线程：

```csharp
// create our NSPredicate which we'll use for the query
NSPredicate query = App.Current.EventStore.PredicateForReminders ( null );

// execute the query
App.Current.EventStore.FetchReminders (
        query, ( EKReminder[] items ) => {
                // do someting with the items
        } );
```

## <a name="summary"></a>摘要

本文档提供 EventKit framework 中，这两个重要片段和大量最常见的任务的概述。 但是，EventKit framework 非常大且功能强大，并且包含尚未引入了在这里，如的功能： 批处理更新，配置警报，重复执行配置上的事件、 注册和侦听日历数据库上的更改设置 GeoFences 和的详细信息。  有关详细信息，请参阅 Apple 的[日历和提醒编程指南](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)。


## <a name="related-links"></a>相关链接

- [日历 （示例）](https://developer.xamarin.com/samples/monotouch/Calendars/)
- [iOS 6 简介](~/ios/platform/introduction-to-ios6/index.md)
- [日历和提醒简介](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
