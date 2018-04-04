---
title: 通信之间松散耦合的组件
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 01669573f215c5a13bb918c9f9ba80aa5ca528c9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="communicating-between-loosely-coupled-components"></a>通信之间松散耦合的组件

发布-订阅模式是一种消息传递模式，发布服务器将消息发送无任何接收方，名为订阅服务器的知识。 同样，订阅服务器侦听的特定消息，而无任何发布服务器的知识。

.NET 中的事件实现发布-订阅模式，并是最简单明了的方法的通信层之间组件如果松散耦合不是必需的如控制和包含它的页。 但是，发布服务器和订阅服务器生存期耦合到对方，对象引用，并且订阅服务器类型必须具有对发布服务器类型的引用。 这可能会造成内存管理问题，尤其是当订阅静态或长生存期对象的事件的短生存期的对象。 如果不删除事件处理程序，订阅服务器将会保持活动状态，对它的引用在发布服务器，并且这将阻止或延迟订阅服务器的垃圾回收。

## <a name="introduction-to-messagingcenter"></a>MessagingCenter 简介

Xamarin.Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)类实现发布-订阅模式，允许基于消息的是很不方便链接对象和类型引用的组件之间的通信。 此机制允许发布服务器和订阅服务器进行通信而无到对方，有助于降低组件，同时还允许独立开发和测试的组件之间的依赖关系的引用。

[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)类提供多播发布-订阅功能。 这意味着可以有多个发布服务器发布单个消息，并且可以有多个订阅服务器用于侦听相同的消息。 图 4-1 阐释了此关系：

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "多播发布-订阅功能")

**图 4-1:**多播发布-订阅功能

发布服务器将使用的消息发送[ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender%7D/p/TSender/System.String/)方法，而订阅服务器侦听的消息使用[ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/)方法。 此外，订阅服务器可以还取消订阅消息，如有必要，使用[ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender%7D/p/System.Object/System.String/)方法。

在内部， [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)类使用弱引用。 这意味着它不会对对象处于活动状态，并将允许它们进行垃圾回收。 因此，它应仅有必要类不再希望收到消息时从消息取消订阅。

EShopOnContainers 移动应用程序使用[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)类之间进行通信松散耦合的组件。 应用程序定义三条消息：

-   `AddProduct`消息发布的`CatalogViewModel`类项添加到购物篮中时。 反过来，`BasketViewModel`类订阅消息，并递增响应中购物篮中的项的数量。 此外，`BasketViewModel`类还向取消订阅此消息。
-   `Filter`消息发布的`CatalogViewModel`类时用户适用于从目录中显示的项的品牌或类型的筛选器。 反过来，`CatalogView`类订阅消息，并更新 UI 以显示与筛选条件匹配的项。
-   `ChangeTab`消息发布的`MainViewModel`类时`CheckoutViewModel`导航到`MainViewModel`以下成功地创建和提交新订单。 反过来，`MainView`类订阅的消息和更新 UI 因此，**我的档案**选项卡处于活动状态，以显示用户的订单。

> [!NOTE]
> 虽然[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)类允许松散耦合类之间的通信，它不提供与此问题仅体系结构的解决方案。 例如，仅由绑定引擎和通过属性更改通知时，还可以实现视图模型和视图之间的通信。 此外，还可以通过将数据传递期间导航实现两个视图模型之间的通信。

在 eShopOnContainers 移动应用中，[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)用于更新于另一个类中发生的操作的响应中的用户界面中。 因此，消息是通过发布的 UI 线程上接收同一线程上的消息的订阅服务器。

> [!TIP]
> 封送处理为 UI 线程在执行 UI 更新的时间。 如果从后台线程发送的消息，需要以更新 UI，处理该消息在订阅服务器中的 UI 线程上调用[ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/)方法。

有关详细信息[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)，请参阅[MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)。

## <a name="defining-a-message"></a>定义一条消息

[`MessagingCenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) 消息是用于标识消息的字符串。 下面的代码示例演示在 eShopOnContainers 移动应用程序中定义的消息：

```csharp
public class MessengerKeys  
{  
    // Add product to basket  
    public const string AddProduct = "AddProduct";  

    // Filter  
    public const string Filter = "Filter";  

    // Change selected Tab programmatically  
    public const string ChangeTab = "ChangeTab";  
}
```

在此示例中，使用常量定义消息。 此方法的优点在于，它提供编译时类型安全和重构支持。

## <a name="publishing-a-message"></a>发布消息

发布者通知订阅服务器的其中一条消息的[ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/)重载。 下面的代码示例演示如何发布`AddProduct`消息：

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

在此示例中， [ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/)方法指定三个自变量：

-   第一个参数指定的发件人类。 必须按任何订阅者想要接收该消息指定发件人类。
-   第二个参数指定的消息。
-   第三个参数指定要发送到订阅服务器上的负载数据。 在这种情况下负载数据是`CatalogItem`实例。

[ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/)方法将发布的消息和其负载数据，使用即发即弃方法。 因此，即使不没有注册接收消息，任何订户发送消息。 在此情况下，忽略发送的消息。

> [!NOTE]
> [ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/)方法可以使用泛型参数来控制传递消息的方式。 因此，不同的订阅服务器可以接收共享消息标识，但发送不同的负载数据类型的多个消息。

## <a name="subscribing-to-a-message"></a>璹綷一条消息

订阅服务器可以注册以使用之一接收消息[ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/)重载。 下面的代码示例演示如何 eShopOnContainers 移动应用程序订阅，并处理，`AddProduct`消息：

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

在此示例中， [ `Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/)方法订阅`AddProduct`消息，并在接收消息的响应中执行的回调委托。 指定作为 lambda 表达式，此回调委托执行更新 UI 的代码。

> [!TIP]
> 请考虑使用不可变的负载数据。 不要尝试修改从负载数据中的回调委托，因为多个线程无法接收到的数据同时访问。 在此方案中，负载数据应该是不可变，以避免并发错误。

订阅服务器可能不需要处理的已发布消息，每个实例，这可以通过泛型类型参数上指定控制[ `Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/)方法。 在此示例中，订阅服务器将仅收到`AddProduct`从发送的消息`CatalogViewModel`类，其负载数据是`CatalogItem`实例。

## <a name="unsubscribing-from-a-message"></a>取消向从消息

订阅服务器可以取消订阅他们不再想要接收的消息。 这与之一来实现[ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/)重载，如下面的代码示例中所示：

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

在此示例中， [ `Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/)方法语法反映订阅接收时指定的类型自变量`AddProduct`消息。

## <a name="summary"></a>总结

Xamarin.Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)类实现发布-订阅模式，允许基于消息的是很不方便链接对象和类型引用的组件之间的通信。 此机制允许发布服务器和订阅服务器进行通信而无到对方，有助于降低组件，同时还允许独立开发和测试的组件之间的依赖关系的引用。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
