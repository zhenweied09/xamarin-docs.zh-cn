---
title: "使用 ASP.NET Web 服务 (ASMX)"
description: "ASMX 能够生成使用简单对象访问协议 (SOAP) 发送消息的 web 服务。 SOAP 是一个独立于平台的独立于语言的协议用于构建和访问 web 服务。 ASMX 服务的使用者不需要知道任何有关平台、 对象模型或用于实现服务的编程语言。 它们只需了解如何发送和接收 SOAP 消息。 本文演示如何使用 Xamarin.Forms 应用程序中的 ASMX SOAP 服务。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: a095dbbb78ad1517791356ae0b7cbeaa94d1336f
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2018
---
# <a name="consuming-an-aspnet-web-service-asmx"></a>使用 ASP.NET Web 服务 (ASMX)

_ASMX 能够生成使用简单对象访问协议 (SOAP) 发送消息的 web 服务。SOAP 是一个独立于平台的独立于语言的协议用于构建和访问 web 服务。ASMX 服务的使用者不需要知道任何有关平台、 对象模型或用于实现服务的编程语言。它们只需了解如何发送和接收 SOAP 消息。本文演示如何使用 Xamarin.Forms 应用程序中的 ASMX SOAP 服务。_

SOAP 消息是 XML 文档包含以下元素：

- 名为的根元素*信封*标识 XML 文档作为 SOAP 消息。
- 一个可选*标头*包含特定于应用程序的信息，例如身份验证数据的元素。 如果*标头*存在元素，则它必须是第一个子元素*信封*元素。
- 必需*正文*包含适用于接收方的 SOAP 消息的元素。
- 一个可选*错误*元素，用于指示错误消息。 如果*错误*元素存在，它必须是子元素的*正文*元素。

SOAP 可以对许多传输协议，包括 HTTP、 SMTP、 TCP 和 UDP 进行操作。 但是，ASMX 服务可以仅通过 HTTP 操作。 Xamarin 平台支持标准 SOAP 1.1 实现通过 HTTP，并且这会包含对许多标准 ASMX 服务配置的支持。

附带的示例应用程序的自述文件中找不到设置 ASMX 服务说明。 但是，当运行示例应用程序时，它将连接到 Xamarin 托管 ASMX 服务，用于提供只读访问数据，如下面的屏幕截图中所示：

![](asmx-images/portal.png "示例应用程序")

> [!NOTE]
> 在 iOS 9 及更高版本中，应用程序传输安全 (ATS) 强制实施安全连接之间 internet 资源 （如应用程序的后端服务器） 和应用程序，从而防止意外泄露的敏感信息。 由于默认情况下，生成的 ios 9 应用中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，则会失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议和安全的 internet 资源的通信。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-the-web-service"></a>使用 Web 服务

ASMX 服务提供以下操作：

|操作|描述|参数|
|--- |--- |--- |
|GetTodoItems|获取待办事项的列表|
|CreateTodoItem|创建新的待办事项|XML 序列化 TodoItem|
|EditTodoItem|更新待办事项|XML 序列化 TodoItem|
|DeleteTodoItem|删除待办事项|XML 序列化 TodoItem|

有关应用程序中使用的数据模型的详细信息，请参阅[对数据进行建模](~/xamarin-forms/data-cloud/walkthrough.md)。

> [!NOTE]
> 示例应用程序使用 Xamarin 托管 ASMX 服务，用于提供对 web 服务的只读访问权限。 因此，创建、 更新和删除数据的操作不会更改应用程序中使用的数据。 但是，ASMX 服务的可承载版本位于**TodoASMXService**随附的示例应用程序中的文件夹。 此可承载版本的完整 ASMX 服务允许创建、 更新、 读取，和删除的数据访问权限。

A*代理*必须生成能够使用 ASMX 服务，允许应用程序连接到服务。 代理是通过使用以定义的方法和关联的服务配置的服务元数据构造的。 由 web 服务生成 Web 服务描述语言 (WSDL) 文档形式公开此元数据。 将 web 服务的 web 引用添加到特定于平台的项目生成代理。

生成的代理类提供用于使用 web 服务使用的异步编程模型 (APM) 设计模式的方法。 在此模式中异步操作实现这两个方法名为*BeginOperationName*和*EndOperationName*，其开始和结束异步操作。

*BeginOperationName*方法开始异步操作并返回一个对象，实现`IAsyncResult`接口。 在调用*BeginOperationName*，应用程序可以继续在调用的线程上执行指令，同时异步操作在有线程池线程上的发生。

每次调用*BeginOperationName*，应用程序还应调用*EndOperationName*来获取该操作的结果。 返回值*EndOperationName*同步 web 服务方法返回的类型相同。 例如，`EndGetTodoItems`方法返回的集合`TodoItem`实例。 *EndOperationName*方法还包括`IAsyncResult`应设置为相应地调用所返回的实例的参数*BeginOperationName*方法。

任务并行库 (TPL) 可以简化通过封装在同一个异步操作来使用的 APM begin/end 方法对的过程`Task`对象。 此封装提供的多个重载的`TaskFactory.FromAsync`方法。

APM 有关的详细信息请参阅[异步编程模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)和[TPL 和传统.NET Framework 异步编程](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)MSDN 上。

### <a name="creating-the-todoservice-object"></a>创建 TodoService 对象

生成的代理类提供`TodoService`类，该类用于通过 HTTP 与 ASMX 服务进行通信。 为了异步操作从 URI 标识的服务实例来调用 web 服务方法，它提供功能。 有关异步操作的详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

`TodoService`实例在类级别声明的以便对象存活，只要该应用程序需要使用 ASMX 服务，如下面的代码示例中所示：

```csharp
public class SoapService : ISoapService
{
  ASMXService.TodoService asmxService;
  ...

  public SoapService ()
  {
    asmxService = new ASMXService.TodoService (Constants.SoapUrl);
  }
  ...
}
```

`TodoService`构造函数采用一个可选的字符串参数，指定 ASMX 服务实例的 URL。 这使应用程序连接到 ASMX 服务的不同实例，前提是有多个已发布的实例。

### <a name="creating-data-transfer-objects"></a>创建数据传输对象

示例应用程序使用`TodoItem`模型数据的类。 若要存储`TodoItem`中 web 服务必须首先将转换为生成的代理项`TodoItem`类型。 这通过实现`ToASMXServiceTodoItem`方法，如下面的代码示例中所示：

```csharp
ASMXService.TodoItem ToASMXServiceTodoItem (TodoItem item)
{
  return new ASMXService.TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

此方法只需创建一个新`ASMService.TodoItem`实例，并将每个属性设置为相同的属性从`TodoItem`实例。

同样，从 web 服务检索数据时，必须将它转换从生成的代理`TodoItem`键入到`TodoItem`实例。 这实现的`FromASMXServiceTodoItem`方法，如下面的代码示例中所示：

```csharp
static TodoItem FromASMXServiceTodoItem (ASMXService.TodoItem item)
{
  return new TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

此方法只需从生成的代理中检索数据`TodoItem`键入，并将其设置在新创建`TodoItem`实例。

### <a name="retrieving-data"></a>检索数据

`TodoService.BeginGetTodoItems`和`TodoService.EndGetTodoItems`方法用于调用`GetTodoItems`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems) {
    Items.Add (FromASMXServiceTodoItem (item));
  }
  ...
}
```

`Task.Factory.FromAsync`方法创建`Task`执行`TodoService.EndGetTodoItems`方法一次`TodoService.BeginGetTodoItems`方法完成时，与`null`表示没有数据传递到参数`BeginGetTodoItems`委托。 最后，值`TaskCreationOptions`枚举指定应使用的创建和执行的任务的默认行为。

`TodoService.EndGetTodoItems`方法返回的数组`ASMXService.TodoItem`实例，然后转换为`List`的`TodoItem`显示的实例。

### <a name="creating-data"></a>创建数据

`TodoService.BeginCreateTodoItem`和`TodoService.EndCreateTodoItem`方法用于调用`CreateTodoItem`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToASMXServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法创建`Task`执行`TodoService.EndCreateTodoItem`方法一次`TodoService.BeginCreateTodoItem`方法完成时，与`todoItem`参数被传递到的数据`BeginCreateTodoItem`委托来指定`TodoItem`由 web 服务来创建。 最后，值`TaskCreationOptions`枚举指定应使用的创建和执行的任务的默认行为。

Web 服务引发`SoapException`未能创建`TodoItem`，这由应用程序。

### <a name="updating-data"></a>更新数据

`TodoService.BeginEditTodoItem`和`TodoService.EndEditTodoItem`方法用于调用`EditTodoItem`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToASMXServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法创建`Task`执行`TodoService.EndEditTodoItem`方法一次`TodoService.BeginCreateTodoItem`方法完成时，与`todoItem`参数被传递到的数据`BeginEditTodoItem`委托来指定`TodoItem` web 服务更新。 最后，值`TaskCreationOptions`枚举指定应使用的创建和执行的任务的默认行为。

Web 服务引发`SoapException`未能找到或更新`TodoItem`，这由应用程序。

### <a name="deleting-data"></a>删除数据

`TodoService.BeginDeleteTodoItem`和`TodoService.EndDeleteTodoItem`方法用于调用`DeleteTodoItem`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  ...
  await Task.Factory.FromAsync (
    todoService.BeginDeleteTodoItem,
    todoService.EndDeleteTodoItem,
    id,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法创建`Task`执行`TodoService.EndDeleteTodoItem`方法一次`TodoService.BeginDeleteTodoItem`方法完成时，与`id`参数被传递到的数据`BeginDeleteTodoItem`委托来指定`TodoItem`要删除 web 服务。 最后，值`TaskCreationOptions`枚举指定应使用的创建和执行的任务的默认行为。

Web 服务引发`SoapException`未能找到或删除`TodoItem`，这由应用程序。

## <a name="summary"></a>摘要

这篇文章演示了如何使用 Xamarin.Forms 应用程序从一个 ASMX web 服务。 ASMX 能够构建通过 HTTP 发送消息使用 SOAP 的 web 服务。 ASMX 服务的使用者不需要知道任何有关平台、 对象模型或用于实现服务的编程语言。 它们只需了解如何发送和接收 SOAP 消息。


## <a name="related-links"></a>相关链接

- [TodoASMX （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
