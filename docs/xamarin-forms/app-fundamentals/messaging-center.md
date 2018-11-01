---
title: Xamarin.Forms MessagingCenter
description: 本文介绍如何使用 Xamarin.Forms MessagingCenter 来发送和接收消息，以减少如视图模型的类之间的耦合。
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 7fef4443cacba0fa8bdb8d5df070c4244730b4f5
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675167"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

_Xamarin.Forms 具有简单的消息传送服务以发送和接收消息。_

<a name="Overview" />

## <a name="overview"></a>概述

Xamarin.Forms`MessagingCenter`启用视图模型和其他组件以与通信而无需知道有关彼此的任何除了简单的消息约定。

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>MessagingCenter 的工作原理

有两个部分`MessagingCenter`:

-  **订阅**-侦听的具有特定签名的消息，并在接收时执行某些操作。 多个订阅者可以侦听同一条消息。
-  **发送**-发布侦听器来对其执行操作的消息。 如果订阅了没有侦听器被忽略该消息。


`MessagingService`是与一个静态类`Subscribe`和`Send`在整个解决方案所用的方法。

消息具有一个字符串`message`用作方法的参数*地址*消息。 `Subscribe`并`Send`方法使用泛型参数以进一步控制如何传递的消息-两个相同的消息`message`文本，但不同的泛型类型参数不会传递到相同的订阅服务器。

有关 API`MessagingCenter`很简单：

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

下面介绍了这些方法。

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>使用 MessagingCenter

由于用户交互 （例如单击按钮）、 系统事件 （如更改状态的控件） 或某些其他事件 （如异步下载完成），可能会发送消息。 订阅服务器可能在侦听更改的用户界面的外观、 将数据保存或触发某个其他操作。

### <a name="simple-string-message"></a>简单的字符串消息

最简单的消息包含只是一个字符串中的`message`参数。 一个`Subscribe`方法的*侦听*简单的字符串消息，显示如下-请注意，指定应发件人为的类型的泛型类型`MainPage`。 使用此语法的消息可以订阅该解决方案中的任何类：

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

在中`MainPage`类的以下代码*发送*消息。 `this`参数是的一个实例`MainPage`。

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

它不会更改字符串-指示*消息类型*和用于确定哪些订阅服务器，以通知。 这种消息用来指示发生某些事件，但"上传已完成"，例如需要任何进一步的信息的。

### <a name="passing-an-argument"></a>将参数传递

与消息的自变量传递，指定自变量类型中`Subscribe`泛型参数和操作签名中。

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

若要发送的消息的参数，包括类型泛型参数和中的参数的值`Send`方法调用。

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

此简单示例使用`string`自变量，但任何C#对象可以传递。

### <a name="unsubscribe"></a>取消订阅

对象可以随时取消订阅的消息签名，以便不传递任何将来的消息。 `Unsubscribe`方法语法应反映消息的签名 （因此可能需要包括消息参数的泛型类型参数）。

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>总结

MessagingCenter 是减小耦合度，尤其是视图模型之间的简单方法。 它可以用于发送和接收简单消息或类之间传递参数。 类应取消订阅它们不再想要接收的消息。


## <a name="related-links"></a>相关链接

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Xamarin.Forms 示例](https://github.com/xamarin/xamarin-forms-samples)
