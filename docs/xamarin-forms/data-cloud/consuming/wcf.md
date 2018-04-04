---
title: 使用 Windows Communication Foundation (WCF) Web 服务
description: WCF 是 Microsoft 的统一的框架，用于构建面向服务的应用程序。 它允许开发人员生成安全、 可靠、 事务处理，且可互操作的分布式应用程序。 本文演示如何使用从 Xamarin.Forms 应用程序的 WCF 简单对象访问协议 (SOAP) 服务。
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: c626008012ccdab2f8ed2c719b34a45471598d47
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="consuming-a-windows-communication-foundation-wcf-web-service"></a>使用 Windows Communication Foundation (WCF) Web 服务

_WCF 是 Microsoft 的统一的框架，用于构建面向服务的应用程序。它允许开发人员生成安全、 可靠、 事务处理，且可互操作的分布式应用程序。本文演示如何使用从 Xamarin.Forms 应用程序的 WCF 简单对象访问协议 (SOAP) 服务。_

WCF 描述了与各种不同的协定包括以下服务：

- **数据协定**– 定义构成一条消息中的内容的基础的数据结构。
- **消息协定搭配**– 撰写从现有数据协定的消息。
- **错误协定**– 允许指定的自定义 SOAP 错误。
- **服务协定**– 指定服务支持的操作和消息所需的每个操作与之进行交互。 它们还指定可以与每个服务操作相关联的任何自定义错误行为。

ASP.NET Web 服务 (ASMX) 和 WCF，之间的差异，但是务必了解 WCF 支持相同的功能，提供的 ASMX 服务 – 通过 HTTP 的 SOAP 消息。 有关使用 ASMX 服务的详细信息，请参阅[使用 ASP.NET Web 服务 (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)。

一般情况下，Xamarin 平台支持相同的客户端将一部分附带 Silverlight 运行时的 WCF。 这包括 WCF 的最常见的编码和协议实现 — 通过 HTTP 文本编码 SOAP 消息传输协议使用`BasicHttpBinding`类。 此外，WCF 支持需要仅在一个用于生成代理的 Windows 环境中可用的工具的用法。

附带的示例应用程序的自述文件中找不到 WCF 服务的设置说明。 但是，运行示例应用程序时它将连接到 Xamarin 承载的 WCF 服务提供只读访问数据，如下面的屏幕截图中所示：

![](wcf-images/portal.png "示例应用程序")

> [!NOTE]
> 在 iOS 9 及更高版本中，应用程序传输安全 (ATS) 强制实施安全连接之间 internet 资源 （如应用程序的后端服务器） 和应用程序，从而防止意外泄露的敏感信息。 由于默认情况下，生成的 ios 9 应用中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，则会失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议和安全的 internet 资源的通信。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-the-web-service"></a>使用 Web 服务

WCF 服务提供以下操作：

|操作|描述|参数|
|--- |--- |--- |
|GetTodoItems|获取待办事项的列表|
|CreateTodoItem|创建新的待办事项|XML 序列化 TodoItem|
|EditTodoItem|更新待办事项|XML 序列化 TodoItem|
|DeleteTodoItem|删除待办事项|XML 序列化 TodoItem|

有关应用程序中使用的数据模型的详细信息，请参阅[对数据进行建模](~/xamarin-forms/data-cloud/walkthrough.md)。

> [!NOTE]
> 示例应用程序使用 Xamarin 承载的 WCF 服务，提供对 web 服务的只读访问权限。 因此，创建、 更新和删除数据的操作不会更改应用程序中使用的数据。 但是，ASMX 服务的可承载版本位于**TodoWCFService**随附的示例应用程序中的文件夹。 此可承载的 WCF 服务允许完整版本创建、 更新、 读取，和删除的数据访问权限。

A*代理*必须生成要使用 WCF 服务，它允许应用程序连接到服务。 代理是通过使用以定义的方法和关联的服务配置的服务元数据构造的。 由 web 服务生成 Web 服务描述语言 (WSDL) 文档形式公开此元数据。 可以使用在 Visual Studio 2017 Microsoft WCF Web 服务引用提供程序将 web 服务的服务引用添加到.NET 标准库生成代理。 创建在 Visual Studio 2017 中使用 Microsoft WCF Web 服务引用提供程序的代理的替代方法是使用 ServiceModel 元数据实用工具 (svcutil.exe)。 有关详细信息，请参阅[ServiceModel 元数据实用工具 (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/)。

生成的代理类提供用于使用使用异步编程模型 (APM) 设计模式的 web 服务方法。 在此模式中，异步操作实现这两个方法名为*BeginOperationName*和*EndOperationName*，其开始和结束异步操作。

*BeginOperationName*方法开始异步操作并返回一个对象，实现`IAsyncResult`接口。 在调用*BeginOperationName*，应用程序可以继续在调用的线程上执行指令，同时异步操作在有线程池线程上的发生。

每次调用*BeginOperationName*，应用程序还应调用*EndOperationName*来获取该操作的结果。 返回值*EndOperationName*同步 web 服务方法返回的类型相同。 例如，`EndGetTodoItems`方法返回的集合`TodoItem`实例。 *EndOperationName*方法还包括`IAsyncResult`应设置为相应地调用所返回的实例的参数*BeginOperationName*方法。

任务并行库 (TPL) 可以简化通过封装在同一个异步操作来使用的 APM begin/end 方法对的过程`Task`对象。 此封装提供的多个重载的`TaskFactory.FromAsync`方法。

APM 有关的详细信息请参阅[异步编程模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)和[TPL 和传统.NET Framework 异步编程](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)MSDN 上。

### <a name="creating-the-todoserviceclient-object"></a>创建 TodoServiceClient 对象

生成的代理类提供`TodoServiceClient`类，该类用于通过 HTTP 与 WCF 服务进行通信。 为了异步操作从 URI 标识的服务实例来调用 web 服务方法，它提供功能。 有关异步操作的详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

`TodoServiceClient`实例在类级别声明的以便对象存活，只要该应用程序需要使用 WCF 服务，如下面的代码示例中所示：

```csharp
public class SoapService : ISoapService
{
  ITodoService todoService;
  ...

  public SoapService ()
  {
    todoService = new TodoServiceClient (
      new BasicHttpBinding (),
      new EndpointAddress (Constants.SoapUrl));
  }
  ...
}
```

`TodoServiceClient`实例配置和绑定信息和终结点地址。 绑定用于指定传输、 编码和协议详细信息所需的应用程序和服务相互通信。 `BasicHttpBinding`指定，将通过 HTTP 传输协议发送文本编码 SOAP 消息。 指定终结点地址启用应用程序连接到 WCF 服务的不同实例，前提是有多个已发布的实例。

有关配置服务引用的详细信息，请参阅[配置服务引用](~/cross-platform/data-cloud/web-services/index.md#wcf)。

### <a name="creating-data-transfer-objects"></a>创建数据传输对象

示例应用程序使用`TodoItem`模型数据的类。 若要存储`TodoItem`中 web 服务必须首先将转换为生成的代理项`TodoItem`类型。 这通过实现`ToWCFServiceTodoItem`方法，如下面的代码示例中所示：

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

此方法只需创建一个新`TodoWCFService.TodoItem`实例，并将每个属性设置为相同的属性从`TodoItem`实例。

同样，从 web 服务检索数据时，必须将它转换从生成的代理`TodoItem`键入到`TodoItem`实例。 这实现的`FromWCFServiceTodoItem`方法，如下面的代码示例中所示：

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
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

`TodoServiceClient.BeginGetTodoItems`和`TodoServiceClient.EndGetTodoItems`方法用于调用`GetTodoItems`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems) {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

`Task.Factory.FromAsync`方法创建`Task`执行`TodoServiceClient.EndGetTodoItems`方法一次`TodoServiceClient.BeginGetTodoItems`方法完成时，与`null`表示没有数据传递到参数`BeginGetTodoItems`委托。 最后，值`TaskCreationOptions`枚举指定应使用的创建和执行的任务的默认行为。

`TodoServiceClient.EndGetTodoItems`方法返回`ObservableCollection`的`TodoWCFService.TodoItem`实例，然后转换为`List`的`TodoItem`显示的实例。

### <a name="creating-data"></a>创建数据

`TodoServiceClient.BeginCreateTodoItem`和`TodoServiceClient.EndCreateTodoItem`方法用于调用`CreateTodoItem`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法创建`Task`执行`TodoServiceClient.EndCreateTodoItem`方法一次`TodoServiceClient.BeginCreateTodoItem`方法完成时，与`todoItem`参数被传递到的数据`BeginCreateTodoItem`委托来指定`TodoItem`由 web 服务来创建。 最后，值`TaskCreationOptions`枚举指定应使用的创建和执行的任务的默认行为。

Web 服务引发`FaultException`未能创建`TodoItem`，这由应用程序。

### <a name="updating-data"></a>更新数据

`TodoServiceClient.BeginEditTodoItem`和`TodoServiceClient.EndEditTodoItem`方法用于调用`EditTodoItem`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法创建`Task`执行`TodoServiceClient.EndEditTodoItem`方法一次`TodoServiceClient.BeginCreateTodoItem`方法完成时，与`todoItem`参数被传递到的数据`BeginEditTodoItem`委托来指定`TodoItem` web 服务更新。 最后，值`TaskCreationOptions`枚举指定应使用的创建和执行的任务的默认行为。

Web 服务引发`FaultException`未能找到或更新`TodoItem`，这由应用程序。

### <a name="deleting-data"></a>删除数据

`TodoServiceClient.BeginDeleteTodoItem`和`TodoServiceClient.EndDeleteTodoItem`方法用于调用`DeleteTodoItem`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

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

`Task.Factory.FromAsync`方法创建`Task`执行`TodoServiceClient.EndDeleteTodoItem`方法一次`TodoServiceClient.BeginDeleteTodoItem`方法完成时，与`id`参数被传递到的数据`BeginDeleteTodoItem`委托来指定`TodoItem`要删除 web 服务。 最后，值`TaskCreationOptions`枚举指定应使用的创建和执行的任务的默认行为。

Web 服务引发`FaultException`未能找到或删除`TodoItem`，这由应用程序。

## <a name="summary"></a>总结

这篇文章演示了如何使用 Xamarin.Forms 应用程序从 WCF SOAP 服务。 一般情况下，Xamarin 平台支持相同的客户端将一部分附带 Silverlight 运行时的 WCF。 这包括 WCF 的最常见的编码和协议实现 — 通过 HTTP 文本编码 SOAP 消息传输协议使用`BasicHttpBinding`类。 此外，WCF 支持需要仅在一个用于生成代理的 Windows 环境中可用的工具的用法。


## <a name="related-links"></a>相关链接

- [TodoWCF （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
