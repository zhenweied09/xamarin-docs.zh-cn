---
title: "使用 rest 样式 Web 服务"
description: "将 web 服务集成到应用程序是一个常用方案。 本文演示如何使用 rest 样式 web 服务从 Xamarin.Forms 应用程序。"
ms.topic: article
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: f8b748ad1b57218d1e8aab11bdc1037cf3cfa14c
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="consuming-a-restful-web-service"></a>使用 rest 样式 Web 服务

_将 web 服务集成到应用程序是一个常用方案。本文演示如何使用 rest 样式 web 服务从 Xamarin.Forms 应用程序。_

具象状态传输 (REST) 是用于生成 web 服务的架构样式。 REST 请求都通过 HTTP 使用 web 浏览器使用来检索网页并将数据发送到服务器的同一 HTTP 谓词。 谓词是：

- **获取**– 此操作用于从 web 服务中检索数据。
- **POST** – 此操作用于在 web 服务上创建数据的新项。
- **PUT** – 使用此操作更新某个项 web 服务上的数据。
- **修补程序**– 使用此操作通过描述有关应如何修改项的一组的说明更新 web 服务上的数据的某个项。 在示例应用程序未使用此谓词。
- **删除**– 使用此操作删除某项 web 服务上的数据。

遵守 REST 的 Api 调用 RESTful Api 和使用定义的 web 服务：

- 基 URI。
- HTTP 方法，如 GET、 POST、 PUT、 PATCH 或 DELETE。
- 数据，如 JavaScript 对象表示法 (JSON) 媒体类型。

RESTful web 服务通常使用 JSON 消息来将数据返回到客户端。 JSON 是一种基于文本的数据交换格式发送数据时，在生成 compact 负载，这会导致降低带宽要求。 示例应用程序使用的开放源代码[NewtonSoft JSON.NET 库](http://www.newtonsoft.com/json)进行序列化和反序列化消息。

REST 的简单性已帮助使其用于访问移动应用程序中的 web 服务的主要方法。

附带的示例应用程序的自述文件中找不到 REST 服务的设置说明。 但是，当运行示例应用程序时，它将连接到 Xamarin 托管 REST 服务，它提供只读访问数据，如下面的屏幕截图中所示：

![](rest-images/portal.png "示例应用程序")

> [!NOTE]
> 在 iOS 9 及更高版本中，应用程序传输安全 (ATS) 强制实施安全连接之间 internet 资源 （如应用程序的后端服务器） 和应用程序，从而防止意外泄露的敏感信息。 由于默认情况下，生成的 ios 9 应用中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，则会失败并出现异常。
>
>如果不能使用 ATS 可以选择退出的**HTTPS**协议和安全的 internet 资源的通信。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-the-web-service"></a>使用 Web 服务

REST 服务使用 ASP.NET Core 编写，并提供以下操作：

<table>
  <thead>
    <tr>
      <th>操作</th>
      <th>HTTP 方法</th>
      <th>相对 URI</th>
      <th>参数</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>获取待办事项的列表</td>
      <td>GET</td>
      <td>/api/todoitems/</td>
      <td></td>
    </tr>
    <tr>
      <td>创建新的待办事项</td>
      <td>发布</td>
      <td>/api/todoitems/</td>
      <td>JSON 格式的 <code>TodoItem</code></td>
    </tr>
    <tr>
      <td>更新待办事项</td>
      <td>PUT</td>
      <td>/api/todoitems/</td>
      <td>JSON 格式的 <code>TodoItem</code></td>
    </tr>
    <tr>
      <td>删除待办事项</td>
      <td>DELETE</td>
      <td>/api/todoitems/{id}</td>
      <td></td>
    </tr>
  </tbody>
</table>

其中包含 Uri 的大多数包括`TodoItem`路径中的 ID。 例如，若要删除`TodoItem`其 ID 为`6bb8a868-dba1-4f1a-93b7-24ebce87e243`，客户端发送 DELETE 请求到`http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`。 有关示例应用程序中使用的数据模型的详细信息，请参阅[对数据进行建模](~/xamarin-forms/data-cloud/walkthrough.md)。

当 Web API 框架收到请求时它将请求路由到的操作。 这些操作是只是公共方法中的`TodoItemsController`类。 框架使用的路由表来确定要在响应请求，下面的代码示例中所示调用的操作：

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

路由表包含一个路由模板中，和 Web API 框架接收 HTTP 请求时，它尝试匹配对路由表中的路由模板的 URI。 如果匹配无法找到路由，客户端收到 404 （找不到） 错误。 如果找到匹配的路由，则 Web API 选择控制器和操作，如下所示：

- 若要查找控制器，Web API 向"控制器"的值*{controller}*变量。
- 若要查找操作，Web API 审视的 HTTP 方法，并考察使用相同的 HTTP 方法作为特性修饰的控制器操作。
- *{Id}*占位符变量映射到操作参数。

REST 服务使用基本身份验证。 有关详细信息请参阅[RESTful web 服务进行身份验证](~/xamarin-forms/data-cloud/authentication/rest.md)。 有关 ASP.NET Web API 路由的详细信息，请参阅[路由在 ASP.NET Web API 中](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api)ASP.NET 网站上。 有关生成使用 ASP.NET Core 的 REST 服务的详细信息，请参阅[本机移动应用程序创建后端服务](/aspnet/core/mobile/native-mobile-backend/)。

> [!NOTE]
> 示例应用程序使用 Xamarin 托管 REST 服务，提供对 web 服务的只读访问权限。 因此，创建、 更新和删除数据的操作不会更改应用程序中使用的数据。 但是，REST 服务的可承载版本位于**TodoRESTService**中随附的文件夹[示例代码](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)。
> 如果你自行托管 REST 服务，它允许完全创建、 更新、 读取和删除的数据访问权限。

`HttpClient`类用来发送和接收通过 HTTP 请求。 它提供了用于发送 HTTP 请求的功能，并接收从 URI 的 HTTP 响应标识资源。 每个请求将作为异步操作发送。 有关异步操作的详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

`HttpResponseMessage`类表示进行 HTTP 请求之后，从 web 服务收到 HTTP 响应消息。 它包含有关响应，包括状态代码、 标头，以及任何正文的信息。 `HttpContent`类表示的 HTTP 正文和内容标头，如`Content-Type`和`Content-Encoding`。 可以使用任一读取内容`ReadAs`方法，如`ReadAsStringAsync`和`ReadAsByteArrayAsync`根据数据的格式。

### <a name="creating-the-httpclient-object"></a>创建 HTTPClient 对象

`HttpClient`实例在类级别声明的以便对象存活，只要该应用程序需要发出 HTTP 请求，如下面的代码示例中所示：

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    client = new HttpClient ();
    client.MaxResponseContentBufferSize = 256000;
  }
  ...
}
```

`HttpClient.MaxResponseContentBufferSize`属性用于指定的最大读取 HTTP 响应消息中的内容时要缓冲的字节数。 此属性的默认大小是一个整数的最大大小。 因此，该属性设置为较小的值，作为安全措施，以限制的应用程序将接受作为从 web 服务响应的数据量。

### <a name="retrieving-data"></a>检索数据

`HttpClient.GetAsync`方法用于将 GET 请求发送到 URI，指定 web 服务，然后接收响应从 web 服务，如下面的代码示例中所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/
  var uri = new Uri (string.Format (Constants.RestUrl, string.Empty));
  ...
  var response = await client.GetAsync (uri);
  if (response.IsSuccessStatusCode) {
      var content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

REST 服务发送的 HTTP 状态代码`HttpResponseMessage.IsSuccessStatusCode`属性，以指示 HTTP 请求是否成功。 对于此操作的 REST 服务响应，表示请求成功，所请求的信息包含在响应中发送 HTTP 状态代码 200 （正常）。

如果 HTTP 操作成功，读取响应的内容时，用于显示。 `HttpResponseMessage.Content`属性表示的 HTTP 响应的内容和`HttpContent.ReadAsStringAsync`方法以异步方式将 HTTP 内容写入字符串。 此内容然后转换为 JSON 从`List`的`TodoItem`实例。

### <a name="creating-data"></a>创建数据

`HttpClient.PostAsync`方法用于将 POST 请求发送到 URI，指定 web 服务，然后从 web 服务接收的响应，如下面的代码示例中所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/
  var uri = new Uri (string.Format (Constants.RestUrl, string.Empty));

  ...
  var json = JsonConvert.SerializeObject (item);
  var content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem) {
    response = await client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"             TodoItem successfully saved.");

  }
  ...
}
```

`TodoItem`实例转换为 JSON 负载将发送到 web 服务。 此负载然后嵌入在正文中的之前与发出的请求将发送到 web 服务的 HTTP 内容`PostAsync`方法。

REST 服务发送的 HTTP 状态代码`HttpResponseMessage.IsSuccessStatusCode`属性，以指示 HTTP 请求是否成功。 此操作的常见响应是：

- **201 （已创建）** – 请求导致已发送响应之前正在创建新资源。
- **400 （错误请求）** – 服务器不理解此请求。
- **409 （冲突）** – 请求可能不会执行因为服务器上有冲突。

### <a name="updating-data"></a>更新数据

`HttpClient.PutAsync`方法用于将 PUT 请求发送到 URI，指定 web 服务，然后接收响应从 web 服务，如下面的代码示例中所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await client.PutAsync (uri, content);
  ...
}
```
该操作的`PutAsync`方法等同于`PostAsync`用于在 web 服务中创建数据的方法。 但是，从 web 服务发送的可能响应与不同。

REST 服务发送的 HTTP 状态代码`HttpResponseMessage.IsSuccessStatusCode`属性，以指示 HTTP 请求是否成功。 此操作的常见响应是：

- **204 （无内容）** – 成功处理该请求和响应是有意留为空白。
- **400 （错误请求）** – 服务器不理解此请求。
- **404 （未找到）** – 服务器上不存在请求的资源。

### <a name="deleting-data"></a>删除数据

`HttpClient.DeleteAsync`方法用于将 DELETE 请求发送到 URI，指定 web 服务，然后接收响应从 web 服务，如下面的代码示例中所示：

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/{0}
  var uri = new Uri (string.Format (Constants.RestUrl, id));
  ...
  var response = await client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"             TodoItem successfully deleted.");
  }
  ...
}
```

REST 服务发送的 HTTP 状态代码`HttpResponseMessage.IsSuccessStatusCode`属性，以指示 HTTP 请求是否成功。 此操作的常见响应是：

- **204 （无内容）** – 成功处理该请求和响应是有意留为空白。
- **400 （错误请求）** – 服务器不理解此请求。
- **404 （未找到）** – 服务器上不存在请求的资源。

## <a name="summary"></a>摘要

这篇文章学习了如何使用 rest 样式 web 服务从 Xamarin.Forms 应用程序，使用`HttpClient`类。 REST 的简单性已帮助使其用于访问移动应用程序中的 web 服务的主要方法。


## <a name="related-links"></a>相关链接

- [为本机移动应用程序创建后端服务](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
