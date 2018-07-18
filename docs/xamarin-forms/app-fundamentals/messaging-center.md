---
title: Xamarin.Forms MessagingCenter
description: 此文章介绍了如何使用 Xamarin.Forms MessagingCenter 发送和接收消息，以减少的类，例如查看模型之间的耦合。
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 49a7ecdad53c7820594f7ebc047ae6fbc5a9bc56
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209409"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

_Xamarin.Forms 包括一个简单的消息传送服务发送和接收消息。_

<a name="Overview" />

## <a name="overview"></a>概述

Xamarin.Forms`MessagingCenter`启用视图模型和其他组件以与通信而无需知道任何有关每个其他除了简单的消息协定。

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>MessagingCenter 的工作原理

有两个部分`MessagingCenter`:

-  **订阅**-侦听的具有特定签名的消息并接收到执行某些操作。 多个订阅服务器可以侦听同一条消息。
-  **发送**-发布侦听器执行操作的消息。 如果订阅了没有侦听器，则忽略此消息。


`MessagingService`是与一个静态类`Subscribe`和`Send`整个解决方案中使用的方法。

消息具有字符串`message`用作方法的参数*地址*消息。 `Subscribe`和`Send`方法使用泛型参数以进一步控制如何传递消息-两个具有相同消息`message`文本，但不同的泛型类型参数不将传递到相同的订阅服务器。

有关 API`MessagingCenter`很简单：

-  订阅&lt;TSender > (对象订阅服务器、 操作的字符串消息&lt;TSender > 回调，TSender 源 = null)
-  订阅&lt;，TArgs > (对象订阅服务器、 操作的字符串消息&lt;，TArgs > 回调，TSender 源 = null)
-  发送&lt;TSender > （TSender 发件人，字符串消息）
-  发送&lt;，TArgs > （TSender 发件人，字符串消息，TArgs 参数）
-  取消订阅&lt;，TArgs > （对象订阅服务器，字符串消息）
-  取消订阅&lt;TSender > （对象订阅服务器，字符串消息）


下面解释了这些方法。

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>使用 MessagingCenter

消息可能会发送用户交互 （例如按钮单击），系统事件 （如更改状态的控件） 或某些其他事件 （如异步下载完成） 的结果。 订阅服务器可能正在侦听更改用户界面的外观、 将数据保存或触发某个其他操作。

### <a name="simple-string-message"></a>简单的字符串消息

最简单的消息包含只是在一个字符串`message`参数。 A`Subscribe`方法，*侦听*为简单的字符串消息如下所示-请注意的泛型类型，指定应发件人的类型为`MainPage`。 使用此语法的消息可以订阅解决方案中的任何类：

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

在`MainPage`类下面的代码*发送*消息。 `this`参数是的一个实例`MainPage`。

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

它不会更改字符串-指示*消息类型*和用于确定哪些订阅服务器，以通知。 这种消息用于指示发生某些事件，但"上载已完成"，例如要求任何进一步的信息的位置。

### <a name="passing-an-argument"></a>传递自变量传递

若要将传递消息的参数，指定自变量类型中`Subscribe`泛型自变量和操作签名中。

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

若要发送的消息的自变量，包含类型的泛型参数和中的自变量的值`Send`方法调用。

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

这个简单的示例使用`string`可以传递自变量，但任何 C# 对象。

### <a name="unsubscribe"></a>取消订阅

对象可以取消订阅从消息签名，以便不传递任何将来的邮件。 `Unsubscribe`方法语法应反映的消息的签名 （因此可能需要包括消息参数的泛型类型参数）。

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>总结

MessagingCenter 是一种简单的方法，以减少耦合，尤其是之间查看模型。 它可以用于发送和接收简单消息或两个类之间传递自变量。 类应取消订阅它们不再想要接收的消息。


## <a name="related-links"></a>相关链接

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Xamarin.Forms 示例](https://github.com/xamarin/xamarin-forms-samples)
