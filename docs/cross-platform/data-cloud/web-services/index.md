---
title: 介绍了 Web 服务
description: 本指南演示如何使用另一个 web 服务技术。 涵盖的主题包括与 REST 服务、 SOAP 服务和 Windows Communication Foundation 服务通信。
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: ad18382a7143c7b1cc6bbecb3867c042512eb562
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-web-services"></a>介绍了 Web 服务

_本指南演示如何使用另一个 web 服务技术。涵盖的主题包括与 REST 服务、 SOAP 服务和 Windows Communication Foundation 服务通信。_

为了正常运行，许多移动应用程序都依赖于云中，并因此将 web 服务集成到移动应用程序是一个常用方案。 Xamarin 平台支持使用另一个 web 服务技术，包括内置和第三方为使用基于 Rest、 ASMX 和 Windows Communication Foundation (WCF) 服务的支持。

本文讨论以下主题：

- [REST 服务](#rest)
- [ASP.Net Web Services (ASMX)](#asmx)
- [WCF 服务](#wcf)

对于使用 Xamarin.Forms 的客户，有使用上述每种技术中的完整示例[Xamarin.Forms Web 服务](~/xamarin-forms/data-cloud/index.md)文档。

> [!IMPORTANT]
> 在 iOS 9 中，应用程序传输安全 (ATS) 强制实施安全连接之间 internet 资源 （如应用程序的后端服务器） 和应用程序，从而防止意外泄露的敏感信息。
> 由于默认情况下，生成的 ios 9 应用中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，则会失败并出现异常。

你可以选择退出的 ATS 如果不能使用`HTTPS`协议和安全的 internet 资源的通信。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用传输安全](~/ios/app-fundamentals/ats.md)。



<a name="rest" />

## <a name="rest"></a>REST

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

REST 的简单性已帮助使其用于访问移动应用程序中的 web 服务的主要方法。

## <a name="consuming-rest-services"></a>使用 REST 服务

有大量库和可用于使用 REST 服务的类和以下各小节讨论它们。 有关使用 REST 服务的详细信息，请参阅[使用 RESTful Web 服务](~/xamarin-forms/data-cloud/consuming/rest.md)。

### <a name="httpclient"></a>HttpClient

[Microsoft HTTP 客户端库](https://www.nuget.org/packages/Microsoft.Net.Http)提供`HttpClient`类，该类用于发送和接收通过 HTTP 请求。 它提供用于发送 HTTP 请求和从 URI 标识的资源的接收 HTTP 响应的功能。 每个请求将作为异步操作发送。 有关异步操作的详细信息，请参阅[异步支持概述](~/cross-platform/platform/async.md)。

`HttpResponseMessage`类表示进行 HTTP 请求之后，从 web 服务收到 HTTP 响应消息。 它包含有关响应，包括状态代码、 标头和正文的信息。 `HttpContent`类表示的 HTTP 正文和内容标头，如`Content-Type`和`Content-Encoding`。 可以使用任一读取内容`ReadAs`方法，如`ReadAsStringAsync`和`ReadAsByteArrayAsync`根据数据的格式。

有关详细信息`HttpClient`类，请参阅[创建 HTTPClient 对象](~/xamarin-forms/data-cloud/consuming/rest.md)。

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

调用 web 服务用于`HTTPWebRequest`涉及：

-  为特定 URI 创建请求实例。
-  在请求实例上设置各种 HTTP 属性。
-  检索`HttpWebResponse`请求。
-  读取响应数据。

例如，下面的代码从美国检索数据国家/地区的故障排除库 web 服务：

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
request.ContentType = "application/json";
request.Method = "GET";

using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
  if (response.StatusCode != HttpStatusCode.OK)
     Console.Out.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
  using (StreamReader reader = new StreamReader(response.GetResponseStream()))
  {
               var content = reader.ReadToEnd();
               if(string.IsNullOrWhiteSpace(content)) {
                       Console.Out.WriteLine("Response contained empty body...");
               }
               else {
                       Console.Out.WriteLine("Response Body: \r\n {0}", content);
               }

               Assert.NotNull(content);
  }
}
```

上面的示例创建`HttpWebRequest`这将返回 JSON 格式数据。 返回的数据`HttpWebResponse`，从中`StreamReader`可以获得要读取的数据。

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

使用 REST 服务的另一种方法使用[RestSharp](http://restsharp.org/)库。 RestSharp 封装 HTTP 请求，包括用于检索结果作为原始字符串内容或反序列化的 C# 对象的支持。 例如，下面的代码在美国太平洋时区中发出了请求国家/地区的故障排除库 web 服务，并检索结果为 JSON 格式字符串：

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` 是一个方法，将会从原始的 JSON 字符串`RestSharp.RestResponse.Content`属性并将其转换为 C# 对象。 反序列化从 web 服务返回的数据将更高版本在本文中讨论。

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

除了单声道基中可用的类类库 (BCL)，如`HttpWebRequest`，和第三方 C# 库，如 RestSharp，特定于平台的类也已可用于使用 web 服务。 例如，在 iOS、`NSUrlConnection`和`NSMutableUrlRequest`可以使用类。

下面的代码示例演示如何调用美国国家/地区的故障排除库 web 服务使用 iOS 类：

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
       NSUrlRequestCachePolicy.ReloadRevalidatingCacheData, 20);
request["Accept"] = "application/json";

var connectionDelegate = new RxTermNSURLConnectionDelegate();
var connection = new NSUrlConnection(request, connectionDelegate);
connection.Start();

public class RxTermNSURLConnectionDelegate : NSUrlConnectionDelegate
{
       StringBuilder _ResponseBuilder;
       public bool IsFinishedLoading { get; set; }
       public string ResponseContent { get; set; }

       public RxTermNSURLConnectionDelegate()
               : base()
       {
               _ResponseBuilder = new StringBuilder();
       }

       public override void ReceivedData(NSUrlConnection connection, NSData data)
       {
               if(data != null) {
                       _ResponseBuilder.Append(data.ToString());
               }
       }
       public override void FinishedLoading(NSUrlConnection connection)
       {
               IsFinishedLoading = true;
               ResponseContent = _ResponseBuilder.ToString();
       }
}
```

通常情况下，为使用 web 服务的特定于平台的类应限于正在将本机代码移植到 C# 的方案。 如果可能，web 服务访问代码应为可移植，以便它可以是共享的跨平台。

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

用于调用 web 服务的另一个选项是[服务堆栈](http://www.servicestack.net/)库。 例如，下面的代码演示如何使用服务堆栈`IServiceClient.GetAsync`发出服务请求的方法：

```csharp
client.GetAsync<CustomersResponse>("",
          (response) => {
               foreach(var c in response.Customers) {
                       Console.WriteLine(c.CompanyName);
               }
       },
       (response, ex) => {
               Console.WriteLine(ex.Message);
       });
```

> [!IMPORTANT]
> 虽然工具 ServiceStack 和 RestSharp 可以轻松调用和使用 REST 服务，但是有时重要使用 XML 或不符合标准的 JSON _DataContract_序列化约定。 如有必要，调用请求并处理相应的序列化显式使用下面讨论的 ServiceStack.Text 库。


<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>使用 rest 样式的数据

RESTful web 服务通常使用 JSON 消息来将数据返回到客户端。 JSON 是基于文本的数据交换格式生成压缩的负载，这会导致降低的带宽要求时发送数据。 在此部分中，将检查以使用 JSON 和纯旧 XML (POX) 的 RESTful 响应的机制。

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

Xamarin 平台随附了现成的 json 支持。 通过使用`JsonObject`，可以检索结果，如下面的代码示例中所示：

```csharp
var obj = JsonObject.Parse(json);
var properties = obj["rxtermsProperties"];
term.BrandName = properties["brandName"];
term.DisplayName = properties["displayName"];
term.Synonym = properties["synonym"];
term.FullName = properties["fullName"];
term.FullGenericName = properties["fullGenericName"];
term.Strength = properties["strength"];
```

但是，务必请注意，`System.Json`工具将整个数据加载到内存。

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

[NewtonSoft JSON.NET 库](http://www.newtonsoft.com/json)是用于序列化和反序列化 JSON 消息的常用的库。 下面的代码示例演示如何使用 JSON.NET 反序列化到 C# 对象的 JSON 消息：

```csharp
var term = new RxTerm();
var properties = JObject.Parse(json)["rxtermsProperties"];
term.BrandName = properties["brandName"].Value<string>();
term.DisplayName = properties["displayName"].Value<string>();
term.Synonym = properties["synonym"].Value<string>();;
term.FullName = properties["fullName"].Value<string>();;
term.FullGenericName = properties["fullGenericName"].Value<string>();;
term.Strength = properties["strength"].Value<string>();
term.RxCUI = properties["rxcui"].Value<string>();
```

<a name="Using_ServiceStack.Text" />

### <a name="servicestacktext"></a>ServiceStack.Text

ServiceStack.Text 是用于处理与 ServiceStack 库的 JSON 序列化库。 下面的代码示例演示如何分析 JSON 使用`ServiceStack.Text.JsonObject`:

```csharp
var result = JsonObject.Parse(json).Object("rxtermsProperties")
       .ConvertTo(x => new RxTerm {
               BrandName = x.Get("brandName"),
               DisplayName = x.Get("displayName"),
               Synonym = x.Get("synonym"),
               FullName = x.Get("fullName"),
               FullGenericName = x.Get("fullGenericName"),
               Strength = x.Get("strength"),
               RxTermDoseForm = x.Get("rxtermsDoseForm"),
               Route = x.Get("route"),
               RxCUI = x.Get("rxcui"),
               RxNormDoseForm = x.Get("rxnormDoseForm"),
       });
```

<a name="Using_System.Xml.Linq" />

### <a name="systemxmllinq"></a>System.Xml.Linq

发生时使用基于 XML 的 REST web 服务，LINQ to XML 可分析的 XML 并填充 C# 对象内联，如下面的代码示例中所示：

```csharp
var doc = XDocument.Parse(xml);
var result = doc.Root.Descendants("rxtermsProperties")
.Select(x=> new RxTerm()
       {
               BrandName = x.Element("brandName").Value,
               DisplayName = x.Element("displayName").Value,
               Synonym = x.Element("synonym").Value,
               FullName = x.Element("fullName").Value,
               FullGenericName = x.Element("fullGenericName").Value,
               //bind more here...
               RxCUI = x.Element("rxcui").Value,
       });
```

<a name="asmx" />

## <a name="aspnet-web-service-asmx"></a>ASP.NET Web 服务 (ASMX)

ASMX 能够生成使用简单对象访问协议 (SOAP) 发送消息的 web 服务。 SOAP 是一个独立于平台的独立于语言的协议用于构建和访问 web 服务。 ASMX 服务的使用者不需要知道任何有关平台、 对象模型或用于实现服务的编程语言。 它们只需了解如何发送和接收 SOAP 消息。

SOAP 消息是 XML 文档包含以下元素：

- 名为的根元素*信封*标识 XML 文档作为 SOAP 消息。
- 一个可选*标头*包含特定于应用程序的信息，例如身份验证数据的元素。 如果*标头*存在元素，则它必须是第一个子元素*信封*元素。
- 必需*正文*包含适用于接收方的 SOAP 消息的元素。
- 一个可选*错误*元素，用于指示错误消息。 如果*错误*元素存在，它必须是子元素的*正文*元素。

SOAP 可以对许多传输协议，包括 HTTP、 SMTP、 TCP 和 UDP 进行操作。 但是，ASMX 服务可以仅通过 HTTP 操作。 Xamarin 平台支持标准 SOAP 1.1 实现通过 HTTP，并且这会包含对许多标准 ASMX 服务配置的支持。

### <a name="generating-a-proxy"></a>生成代理

A*代理*必须生成要使用 ASMX 服务，它允许应用程序连接到服务。 代理是通过使用以定义的方法和关联的服务配置的服务元数据构造的。 此元数据公开为生成的 web 服务的 Web 服务描述语言 (WSDL) 文档。 使用适用于 Mac 的 Visual Studio 或 Visual Studio 将 web 服务的 web 引用添加到特定于平台的项目生成代理。

Web 服务 URL 可以是托管远程源或本地文件系统资源可通过访问`file:///`路径前缀，例如：

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "Web 服务 URL 可以是托管远程源或可通过文件路径前缀访问的本地文件系统资源")](images/add-webreference-dialog.png#lightbox)

这将在项目的 Web 或服务引用文件夹中生成代理。 由于生成代理代码，它不应修改。

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>手动将代理添加到项目

如果必须使用兼容的工具生成的现有代理服务器，包括你的项目的一部分时，可以使用此输出。 在适用于 Mac 的 Visual Studio，使用**添加文件...** 将代理添加的菜单选项。 此外，这需要*System.Web.Services.dll*使用显式引用**添加引用...** 对话框。

### <a name="consuming-the-proxy"></a>使用代理

生成的代理类提供用于使用 web 服务使用的异步编程模型 (APM) 设计模式的方法。 在此模式中异步操作实现这两个方法名为*BeginOperationName*和*EndOperationName*，其开始和结束异步操作。

*BeginOperationName*方法开始异步操作并返回一个对象，实现`IAsyncResult`接口。 在调用*BeginOperationName*，应用程序可以继续在调用的线程上执行指令，同时异步操作在有线程池线程上的发生。

每次调用*BeginOperationName*，应用程序还应调用*EndOperationName*来获取该操作的结果。 返回值*EndOperationName*同步 web 服务方法返回的类型相同。 下面的代码示例演示了此示例：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

任务并行库 (TPL) 可以简化通过封装在同一个异步操作来使用的 APM begin/end 方法对的过程`Task`对象。 此封装提供的多个重载的`Task.Factory.FromAsync`方法。 此方法创建`Task`执行`TodoService.EndGetTodoItems`方法一次`TodoService.BeginGetTodoItems`方法完成时，与`null`表示没有数据传递到参数`BeginGetTodoItems`委托。 最后，值`TaskCreationOptions`枚举指定应使用的创建和执行的任务的默认行为。

APM 有关的详细信息，请参阅[异步编程模型](https://msdn.microsoft.com/en-us/library/ms228963(v=vs.110).aspx)和[TPL 和传统.NET Framework 异步编程](https://msdn.microsoft.com/en-us/library/dd997423(v=vs.110).aspx)MSDN 上。

有关使用 ASMX 服务的详细信息，请参阅[使用 ASP.NET Web 服务 (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)。

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF 是 Microsoft 的统一的框架，用于构建面向服务的应用程序。 它允许开发人员生成安全、 可靠、 事务处理，且可互操作的分布式应用程序。

WCF 描述了与各种不同的协定包括以下服务：

- **数据协定**– 定义构成一条消息中的内容的基础的数据结构。
- **消息协定搭配**– 撰写从现有数据协定的消息。
- **错误协定**– 允许指定的自定义 SOAP 错误。
- **服务协定**– 指定服务支持的操作和消息所需的每个操作与之进行交互。 它们还指定可以与每个服务操作相关联的任何自定义错误行为。

ASP.NET Web 服务 (ASMX) 和 WCF，之间的差异，但是务必了解 WCF 支持相同的功能，提供的 ASMX 服务 – 通过 HTTP 的 SOAP 消息。

一般情况下，Xamarin 平台支持相同的客户端将一部分附带 Silverlight 运行时的 WCF。 这包括 WCF 的最常见的编码和协议实现 — 通过 HTTP 文本编码 SOAP 消息传输协议使用`BasicHttpBinding`类。 此外，WCF 支持需要仅在一个用于生成代理的 Windows 环境中可用的工具的用法。

有关使用 Xamarin 平台来使用 WCF 的详细信息的 web 服务与`BasicHttpBinding`类，请参阅[演练-使用 WCF](walkthrough-working-with-wcf.md)。

### <a name="generating-a-proxy"></a>生成代理

A*代理*必须生成要使用 WCF 服务，它允许应用程序连接到服务。 代理是通过使用以定义的方法和关联的服务配置的服务元数据构造的。 由 web 服务生成 Web 服务描述语言 (WSDL) 文档形式公开此元数据。 可以使用在 Visual Studio 2017 Microsoft WCF Web 服务引用提供程序将 web 服务的服务引用添加到.NET 标准库生成代理。

创建在 Visual Studio 2017 中使用 Microsoft WCF Web 服务引用提供程序的代理的替代方法是使用 ServiceModel 元数据实用工具 (svcutil.exe)。 有关详细信息，请参阅[ServiceModel 元数据实用工具 (Svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)。

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>配置代理服务器

配置生成的代理将通常采用两个配置参数 （具体取决于 SOAP 1.1/ASMX 或 WCF） 在初始化期间：`EndpointAddress`和/或关联的绑定信息，如下面的示例中所示：

```csharp
var binding = new BasicHttpBinding () {
       Name= "basicHttpBinding",
       MaxReceivedMessageSize = 67108864,
};

binding.ReaderQuotas = new System.Xml.XmlDictionaryReaderQuotas() {
       MaxArrayLength = 2147483646,
       MaxStringContentLength = 5242880,
};

var timeout = new TimeSpan(0,1,0);
binding.SendTimeout= timeout;
binding.OpenTimeout = timeout;
binding.ReceiveTimeout = timeout;

client = new Service1Client (binding, new EndpointAddress ("http://192.168.1.100/Service1.svc"));
```

绑定用于指定传输、 编码和协议详细信息所需的应用程序和服务相互通信。 `BasicHttpBinding`指定，将通过 HTTP 传输协议发送文本编码 SOAP 消息。 指定终结点地址启用应用程序连接到 WCF 服务的不同实例，前提是有多个已发布的实例。

### <a name="consuming-the-proxy"></a>使用代理

生成的代理类提供用于使用使用异步编程模型 (APM) 设计模式的 web 服务方法。 在此模式中，异步操作实现这两个方法名为*BeginOperationName*和*EndOperationName*，其开始和结束异步操作。

*BeginOperationName*方法开始异步操作并返回一个对象，实现`IAsyncResult`接口。 在调用*BeginOperationName*，应用程序可以继续在调用的线程上执行指令，同时异步操作在有线程池线程上的发生。

每次调用*BeginOperationName*，应用程序还应调用*EndOperationName*来获取该操作的结果。 返回值*EndOperationName*同步 web 服务方法返回的类型相同。 下面的代码示例演示了此示例：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

任务并行库 (TPL) 可以简化通过封装在同一个异步操作来使用的 APM begin/end 方法对的过程`Task`对象。 此封装提供的多个重载的`Task.Factory.FromAsync`方法。 此方法创建`Task`执行`TodoServiceClient.EndGetTodoItems`方法一次`TodoServiceClient.BeginGetTodoItems`方法完成时，与`null`表示没有数据传递到参数`BeginGetTodoItems`委托。 最后，值`TaskCreationOptions`枚举指定应使用的创建和执行的任务的默认行为。

APM 有关的详细信息，请参阅[异步编程模型](https://msdn.microsoft.com/en-us/library/ms228963(v=vs.110).aspx)和[TPL 和传统.NET Framework 异步编程](https://msdn.microsoft.com/en-us/library/dd997423(v=vs.110).aspx)MSDN 上。

有关使用 WCF 服务的详细信息，请参阅[使用 Windows Communication Foundation (WCF) Web 服务](~/xamarin-forms/data-cloud/consuming/wcf.md)。

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>使用传输安全

WCF 服务可能会使用传输级安全，以防出现截获的消息。 Xamarin 平台支持采用使用 SSL 的传输级安全的绑定。 但是，有时可能无法在其中堆栈可能需要验证的证书，这会导致意外行为。 验证可以通过注册重写`ServerCertificateValidationCallback`委托之前调用服务，如下面的代码示例中所示：

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

这将保持时忽略服务器端证书验证的传输加密。 但是，此方法将有效地忽略与证书关联的信任问题和可能不合适。 有关详细信息，请参阅[恭敬使用受信任的根](http://www.mono-project.com/UsingTrustedRootsRespectfully)上[mono project.com](http://www.mono-project.com)。

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>使用客户端凭据安全

WCF 服务还可能要求使用凭据进行身份验证的服务客户端。 Xamarin 平台不支持 Ws-security 协议，允许客户端在 SOAP 消息信封内发送凭据。 但是，Xamarin 平台支持能够向服务器发送 HTTP 基本身份验证凭据，通过指定相应`ClientCredentialType`:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

然后，可以指定基本身份验证凭据：

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

在上面的示例中，如果你收到消息"用尽 trampolines 类型 0"则可以增加类型 0 trampolines 数加上`–aot “trampolines={number of trampolines}”`到生成的自变量。 有关详细信息，请参阅[故障排除](~/ios/troubleshooting/troubleshooting.md#trampolines)。

详细了解 HTTP 基本身份验证，但在 REST web 服务的上下文，请参阅[RESTful Web 服务进行身份验证](~/xamarin-forms/data-cloud/authentication/rest.md)。

## <a name="summary"></a>总结

本指南演示了如何使用另一个 web 服务技术。 涵盖的主题包括与 REST 服务、 SOAP 服务和 Windows Communication Foundation 服务通信。

## <a name="related-links"></a>相关链接

- [WebServices 示例](https://developer.xamarin.com/samples/mobile/WebServices/WebServiceSamples/)
- [Xamarin.Forms 中的 web 服务](~/xamarin-forms/data-cloud/index.md)
- [ServiceModel 元数据实用工具 (svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](http://msdn.microsoft.com/en-us/library/system.servicemodel.basichttpbinding.aspx)
