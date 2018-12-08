---
title: 使用 ASP.NET Web 服务 (ASMX)
description: 本文演示如何使用 Xamarin.Forms 应用程序中的 ASMX SOAP 服务。
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 1fa2fee36619a2a443d84b861b2473a1f616ed0e
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055136"
---
# <a name="consuming-an-aspnet-web-service-asmx"></a>使用 ASP.NET Web 服务 (ASMX)

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)

_ASMX 能够构建使用简单对象访问协议 (SOAP) 发送消息的 web 服务。SOAP 是一种独立于平台的和独立于语言的构建和访问 web 服务协议。一个 ASMX 服务的使用者不需要完全了解平台、 对象模型中或用于实现服务的编程语言。它们只需了解如何发送和接收 SOAP 消息。本文演示如何使用 Xamarin.Forms 应用程序中的 ASMX SOAP 服务。_

SOAP 消息是包含以下元素的 XML 文档：

- 名为的根元素*信封*，它标识为 SOAP 消息的 XML 文档。
- 一个可选*标头*包含特定于应用程序的信息，例如身份验证数据的元素。 如果*标头*存在元素，则它必须是第一个子元素*信封*元素。
- 所需*正文*包含适用于接收方的 SOAP 消息的元素。
- 一个可选*容错*元素，用于指示错误消息。 如果*容错*元素不存在，必须为的子元素*正文*元素。

SOAP 可以在许多传输协议，包括 HTTP、 SMTP、 TCP 和 UDP 上操作。 但是，一个 ASMX 服务可以仅通过 HTTP 操作。 Xamarin 平台支持标准 SOAP 1.1 实现通过 HTTP，并且这包括对很多标准的 ASMX 服务配置的支持。

示例应用程序附带的自述文件中可以找到设置 ASMX 服务说明。 但是，当运行示例应用程序时，它将连接到 Xamarin 托管 ASMX 服务，可提供只读访问数据，如以下屏幕截图中所示：

![](asmx-images/portal.png "示例应用程序")

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全 (ATS) 强制执行安全连接之间 （例如应用程序的后端服务器） 的 internet 资源和应用程序中，从而防止意外泄露敏感信息。 由于默认情况下构建适用于 iOS 9 应用程序中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，它们将失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议并确保对 internet 资源的通信安全。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用程序传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-the-web-service"></a>使用 Web 服务

ASMX 服务提供了以下操作：

|操作|描述|参数|
|--- |--- |--- |
|GetTodoItems|获取待办事项的列表|
|CreateTodoItem|创建新的待办事项|XML 序列化 TodoItem|
|EditTodoItem|更新待办事项|XML 序列化 TodoItem|
|DeleteTodoItem|删除待办事项|XML 序列化 TodoItem|

有关在应用程序中使用的数据模型的详细信息，请参阅[为数据建模](~/xamarin-forms/data-cloud/walkthrough.md)。

> [!NOTE]
> 示例应用程序使用提供对 web 服务的只读访问 Xamarin 托管 ASMX 服务。 因此，创建、 更新和删除数据的操作不会更改应用程序中使用的数据。 但是，ASMX 服务的可承载版本现已推出**TodoASMXService**随附的示例应用程序中的文件夹。 此版本可承载的 ASMX 服务允许完全创建、 更新、 读取和删除对数据的访问。

一个*代理*必须生成要使用 ASMX 服务，允许应用程序连接到服务。 代理是通过使用以定义的方法和关联的服务配置的服务元数据构造的。 此元数据中生成的 web 服务的 Web 服务描述语言 (WSDL) 文档的形式公开。 将 web 服务的 web 引用添加到特定于平台的项目生成代理。

生成的代理类提供用于使用 web 服务使用异步编程模型 (APM) 设计模式的方法。 在此模式中异步操作实现这两个方法名为*BeginOperationName*并*EndOperationName*，其中开始和结束异步操作。

*BeginOperationName*方法开始异步操作并返回一个对象，实现`IAsyncResult`接口。 在调用*BeginOperationName*，应用程序可以继续在调用线程上执行指令，同时异步操作在线程池线程。

每次调用*BeginOperationName*，应用程序还应调用*EndOperationName*来获取该操作的结果。 返回值*EndOperationName*同步 web 服务方法返回的类型相同。 例如，`EndGetTodoItems`方法返回的集合`TodoItem`实例。 *EndOperationName*方法还包括`IAsyncResult`应设置为的对应调用返回的实例的参数*BeginOperationName*方法。

任务并行库 (TPL) 可以简化使用 APM begin/end 方法对通过封装中相同的异步操作的过程`Task`对象。 这种封装提供的多个重载`TaskFactory.FromAsync`方法。

有关 APM 的详细信息请参阅[异步编程模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)并[TPL 和传统.NET Framework 异步编程](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)MSDN 上。

### <a name="creating-the-todoservice-object"></a>创建 TodoService 对象

生成的代理类提供了`TodoService`类，该类用于通过 HTTP 与 ASMX 服务进行通信。 它提供用于调用 web 服务方法从 URI 的异步操作来标识服务实例的功能。 有关异步操作的详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

`TodoService`实例在类级别声明的以便在对象存在的只要该应用程序需要使用 ASMX 服务，如下面的代码示例中所示：

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

`TodoService`构造函数采用一个可选的字符串参数，它指定 ASMX 服务实例的 URL。 前提是有多个已发布的实例，这使应用程序连接到 ASMX 服务的不同实例。

### <a name="creating-data-transfer-objects"></a>创建数据传输对象

示例应用程序使用`TodoItem`模型数据的类。 用于存储`TodoItem`中 web 服务必须首先将转换为生成的代理项`TodoItem`类型。 这通过实现`ToASMXServiceTodoItem`方法，如下面的代码示例中所示：

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

同样，当从 web 服务检索数据时，必须将它从转换生成的代理`TodoItem`键入到`TodoItem`实例。 这通过实现`FromASMXServiceTodoItem`方法，如下面的代码示例中所示：

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

此方法只需从生成的代理中检索数据`TodoItem`类型并将其设置在新创建`TodoItem`实例。

### <a name="retrieving-data"></a>检索数据

`TodoService.BeginGetTodoItems`并`TodoService.EndGetTodoItems`方法用于调用`GetTodoItems`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

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

`Task.Factory.FromAsync`方法创建`Task`执行`TodoService.EndGetTodoItems`方法一次`TodoService.BeginGetTodoItems`方法完成，具有`null`参数，该值指示不传递到任何数据`BeginGetTodoItems`委托。 最后的值`TaskCreationOptions`枚举指定应使用的创建和执行任务的默认行为。

`TodoService.EndGetTodoItems`方法返回的数组`ASMXService.TodoItem`实例，然后将转换为`List`的`TodoItem`显示的实例。

### <a name="creating-data"></a>创建数据

`TodoService.BeginCreateTodoItem`并`TodoService.EndCreateTodoItem`方法用于调用`CreateTodoItem`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

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

`Task.Factory.FromAsync`方法创建`Task`执行`TodoService.EndCreateTodoItem`方法一次`TodoService.BeginCreateTodoItem`方法完成，具有`todoItem`参数被传递到的数据`BeginCreateTodoItem`委托来指定`TodoItem`若要创建的 web 服务。 最后的值`TaskCreationOptions`枚举指定应使用的创建和执行任务的默认行为。

Web 服务将引发`SoapException`未能创建`TodoItem`，这由应用程序处理。

### <a name="updating-data"></a>更新数据

`TodoService.BeginEditTodoItem`并`TodoService.EndEditTodoItem`方法用于调用`EditTodoItem`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

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

`Task.Factory.FromAsync`方法创建`Task`执行`TodoService.EndEditTodoItem`方法一次`TodoService.BeginCreateTodoItem`方法完成，具有`todoItem`参数被传递到的数据`BeginEditTodoItem`委托来指定`TodoItem`若要通过 web 服务进行更新。 最后的值`TaskCreationOptions`枚举指定应使用的创建和执行任务的默认行为。

Web 服务将引发`SoapException`如果无法找到或更新`TodoItem`，这由应用程序处理。

### <a name="deleting-data"></a>删除数据

`TodoService.BeginDeleteTodoItem`并`TodoService.EndDeleteTodoItem`方法用于调用`DeleteTodoItem`web 服务所提供的操作。 这些异步方法封装在`Task`对象，如下面的代码示例中所示：

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

`Task.Factory.FromAsync`方法创建`Task`执行`TodoService.EndDeleteTodoItem`方法一次`TodoService.BeginDeleteTodoItem`方法完成，具有`id`参数被传递到的数据`BeginDeleteTodoItem`委托来指定`TodoItem`要删除 web 服务。 最后的值`TaskCreationOptions`枚举指定应使用的创建和执行任务的默认行为。

Web 服务将引发`SoapException`如果无法找到或删除`TodoItem`，这由应用程序处理。

## <a name="summary"></a>总结

本文演示了如何使用 Xamarin.Forms 应用程序中的 ASMX web 服务。 ASMX 提供构建通过 HTTP 发送消息使用 SOAP 的 web 服务的功能。 一个 ASMX 服务的使用者不需要完全了解平台、 对象模型中或用于实现服务的编程语言。 它们只需了解如何发送和接收 SOAP 消息。


## <a name="related-links"></a>相关链接

- [TodoASMX （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
