---
title: 第 5 部分 — 共享策略的实际代码
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6f6b88bf29e94a221b2ef58b3299348eb08d33fa
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2018
---
# <a name="part-5---practical-code-sharing-strategies"></a>第 5 部分 — 共享策略的实际代码

本部分提供了如何共享代码的常见应用程序方案的示例。



## <a name="data-layer"></a>数据层

数据层组成的存储引擎和方法来读取和写入信息。 出于性能、 灵活性和跨平台兼容性 SQLite 数据库引擎被建议 Xamarin 跨平台应用程序。
运行各种平台包括 Windows、 Android、 iOS 和 mac。


### <a name="sqlite"></a>SQLite

SQLite 是开放源代码数据库实现。 源和文档，请参阅[SQLite.org](http://www.sqlite.org/)。SQLite 支持是在每个移动平台上可用：

-  **iOS** – 内置于操作系统。
- **Android** – 内置于操作系统以来 Android 2.2 (API 级别 10)。
- **Windows** – 请参阅[SQLite for 通用 Windows 平台扩展](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936)。


即使使用数据库引擎在所有平台上可用时，访问数据库的本机方法是不同的。 这两种 iOS 和 Android 提供内置的 Api 从 Xamarin.iOS 或 Xamarin.Android 访问可用的 SQLite，但是使用本机 SDK 方法提供任何功能来共享代码 （而不是可能的 SQL 查询本身，假定它们存储为字符串）. 有关本机数据库功能搜索的详细信息`CoreData`在 iOS 或 Android 的`SQLiteOpenHelper`类; 这些选项不是跨平台由于它们是超出了本文的讨论范围。



### <a name="adonet"></a>ADO.NET

Xamarin.iOS 和 Xamarin.Android 支持`System.Data`和`Mono.Data.Sqlite`(请参阅 Xamarin.iOS[文档](~/ios/data-cloud/system.data.md)有关详细信息)。
使用这些命名空间，可编写适用于这两个平台的 ADO.NET 代码。 编辑要包括的项目的引用`System.Data.dll`和`Mono.Data.Sqlite.dll`并添加以下 using 语句到你的代码：

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

将工作，下面的示例代码：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "items.db3");
bool exists = File.Exists (dbPath);
if (!exists)
    SqliteConnection.CreateFile (dbPath);
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open ();
if (!exists) {
    // This is the first time the app has run and/or that we need the DB.
    // Copy a "template" DB from your assets, or programmatically create one like this:
    var commands = new[]{
        "CREATE TABLE [Items] (Key ntext, Value ntext);",
        "INSERT INTO [Items] ([Key], [Value]) VALUES ('sample', 'text')"
    };
    foreach (var command in commands) {
        using (var c = connection.CreateCommand ()) {
            c.CommandText = command;
            c.ExecuteNonQuery ();
        }
    }
}
// use `connection`... here, we'll just append the contents to a TextView
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT [Key], [Value] from [Items]";
    var r = contents.ExecuteReader ();
    while (r.Read ())
        Console.Write("\n\tKey={0}; Value={1}",
                r ["Key"].ToString (),
                r ["Value"].ToString ());
}
connection.Close ();
```

显然将跨不同的方法和类 （此示例中为仅用于演示目的） 分割的 ADO.NET 的实际实现。



### <a name="sqlite-net--cross-platform-orm"></a>SQLite NET – 跨平台 ORM

ORM （或对象关系映射器） 尝试简化在类中建模的数据的存储。 而不是手动编写 SQL 查询，该创建表或选择，手动从提取的插入和删除数据类的字段和属性，ORM 添加为你执行该代码的层。 使用反射来检查你的类的结构，ORM 可以自动创建表和匹配类并生成查询来读取和写入数据的列。 这允许应用程序代码只需发送和检索到 ORM、 负责实质上的所有 SQL 操作的对象实例。

SQLite NET 充当简单 ORM，您可以保存和检索 SQLite 中的类。 它会隐藏跨平台编译器指令和其他技巧相结合的 SQLite 访问的复杂性。

SQLite 的网络功能：

-  通过将属性添加到模型类定义的表。
-  数据库实例都由的一个子类`SQLiteConnection`，SQLite 的网络库中的主类。
-  数据可以插入、 查询和删除使用对象。 没有 SQL 语句是必需的 （尽管你可以编写 SQL 语句，如果需要）。
-  可以对 SQLite NET 返回的集合执行基本 Linq 查询。


源代码和文档 SQLite NET 位于[github 上的 SQLite Net](https://github.com/praeclarum/sqlite-net)并且已执行这两个案例研究中。 SQLite NET 代码的简单示例 (从*Tasky Pro*案例研究) 如下所示。

首先，`TodoItem`类使用属性来定义将数据库的主键字段：

```csharp
public class TodoItem : IBusinessEntity
{
    public TodoItem () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

这允许`TodoItem`表上创建替换为以下代码 （以及任何 SQL 语句） 行`SQLiteConnection`实例：

```csharp
CreateTable<TodoItem> ();
```

表中的数据还使用其他方法上操作`SQLiteConnection`（同样，不要求 SQL 语句）：

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

请参阅完整示例的案例研究源代码。



## <a name="file-access"></a>文件访问

文件访问是必然会成为任何应用程序的重要组成部分。 可能不属于应用程序包含的文件的常见示例：

-  SQLite 数据库文件。
-  用户生成 （文本、 图像、 声音、 视频） 的数据。
-  缓存 （图像、 html 或 PDF 文件） 下载的数据。




### <a name="systemio-direct-access"></a>System.IO 直接访问

Xamarin.iOS 和 Xamarin.Android 允许使用中的类的文件系统访问权限`System.IO`命名空间。

每个平台具有必须考虑的不同的访问限制：

-  iOS 应用程序具有严格限制的文件系统访问权限的沙箱中运行。 Apple 进一步决定如何指定备份的某些位置 （和其他不） 应使用文件系统。 请参阅[使用了文件系统在 Xamarin.iOS](~/ios/app-fundamentals/file-system.md)更多详细信息的指南。
-  Android 还限制对相关应用程序，某些特定目录的访问，但它还支持外部媒体 （如。 SD 卡） 和访问共享的数据。
-  Windows Phone 8 (Silverlight) 不允许直接文件访问 – 仅可以使用操作文件`IsolatedStorage`。
-  Windows 8.1 WinRT 和 Windows 10 UWP 项目仅提供了通过异步文件操作`Windows.Storage`Api，它不同于其他平台。

#### <a name="example-for-ios-and-android"></a>适用于 iOS 和 Android 的示例

写入和读取文本文件的普通示例所示。
使用`Environment.GetFolderPath`允许相同的代码在 iOS 和 Android，其中的每个返回有效的目录基于其文件系统约定上运行。

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.ReadAllText (filePath));
```

请参阅 Xamarin.iOS[使用文件系统](~/ios/app-fundamentals/file-system.md)有关特定于 iOS 的文件系统功能的详细信息的文档。 编写跨平台文件访问代码时，请记住，某些文件系统是区分大小写，并且具有不同的目录分隔符。 很好的做法对于文件名始终使用相同的大小写和`Path.Combine()`方法构造文件或目录路径时。



### <a name="windowsstorage-for-windows-8-and-windows-10"></a>对于 Windows 8 和 Windows 10 的 Windows.Storage

*具有 Xamarin.Forms 创建移动应用*[簿](https://developer.xamarin.com/r/xamarin-forms/book/)
[章 20。Async 和文件 I/O](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)包括[Windows 8.1 和 Windows 10 的示例](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20)。

使用[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)可以读取和文件在使用支持的 Api 这些平台上的文件：

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

请参阅[章节](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)有关详细信息。


<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>在 Windows Phone 7 和 8 (Silverlight) 上的独立的存储

独立的存储是用于保存和加载跨所有的 iOS、 Android 和较旧的 Windows Phone 平台的文件的公共 API。

它是默认的机制用于在 Windows Phone 中 Xamarin.iOS 和 Xamarin.Android 以允许公共文件访问代码，要写入已实现的 (Silverlight) 中的文件访问。 `System.IO.IsolatedStorage`可以跨所有三个平台中引用类[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)。

请参阅[独立存储概述为 Windows Phone](http://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx)有关详细信息。

独立存储 Api 中均不提供[可移植类库](~/cross-platform/app-fundamentals/pcl.md)。 对于 PCL 一个替代方法是[PCLStorage NuGet](https://pclstorage.codeplex.com/)



### <a name="cross-platform-file-access-in-pcls"></a>在 Pcl 中的跨平台文件访问

另外，还有 PCL 兼容 Nuget – [PCLStorage](https://www.nuget.org/packages/PCLStorage/) – Xamarin 支持的平台和最新的 Windows Api 的该设施跨平台文件访问权限。


## <a name="network-operations"></a>网络操作

大多数移动应用程序将有网络连接的组件，例如：

-  下载图像、 视频和音频 （如。 缩略图、 照片、 音乐）。
-  下载文档 （如。 HTML、 PDF)。
-  正在上载用户数据 （例如照片或文本）。
-  访问 web 服务或第三方 （包括 SOAP、 XML 或 JSON） 的 Api。


.NET Framework 提供几个不同的类，用于访问网络资源： `HttpClient`， `WebClient`，和`HttpWebRequest`。

### <a name="httpclient"></a>HttpClient

`HttpClient`类`System.Net.Http`命名空间可用于 Xamarin.iOS 和 Xamarin.Android，大多数 Windows 平台。 没有[Microsoft HTTP 客户端库 Nuget](https://www.nuget.org/packages/Microsoft.Net.Http/)可用来将此 API 引入可移植类库 （和 Windows Phone 8 Silverlight）。

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

`WebClient`类提供一个简单的 API，以从远程服务器中检索远程数据。

通用 Windows Platofrm 操作*必须*是异步的即使 Xamarin.iOS 和 Xamarin.Android 支持同步操作 （但可以不在后台线程上）。

简单的异步代码`WebClient`操作：

```csharp
var webClient = new WebClient ();
webClient.DownloadStringCompleted += (sender, e) =>
{
    var resultString = e.Result;
    // do something with downloaded string, do UI interaction on main thread
};
webClient.Encoding = System.Text.Encoding.UTF8;
webClient.DownloadStringAsync (new Uri ("http://some-server.com/file.xml"));
```

 `WebClient` 此外具有`DownloadFileCompleted`和`DownloadFileAsync`来检索二进制数据。

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>HttpWebRequest

`HttpWebRequest` 提供多个自定义项比`WebClient`并因此需要更多代码以使用。

同步一个简单的代码`HttpWebRequest`操作：

```csharp
var request = HttpWebRequest.Create(@"http://some-server.com/file.xml ");
request.ContentType = "text/xml";
request.Method = "GET";
using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
    if (response.StatusCode != HttpStatusCode.OK)
        Console.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
    using (StreamReader reader = new StreamReader(response.GetResponseStream()))
    {
        var content = reader.ReadToEnd();
        // do something with downloaded string, do UI interaction on main thread
    }
}
```

没有示例，请参见我们[Web 服务文档](~/cross-platform/data-cloud/web-services/index.md)。

 <a name="Reachability" />


### <a name="reachability"></a>可访问性

各种网络条件从快速 Wi-fi 或 4g 连接到不佳接待和慢速边缘数据链接下运行移动设备。 因此，很好的做法检测网络是否可用，因此，哪种类型的网络是否可用，然后再尝试连接到远程服务器。

移动应用程序可能需要在这些情况下的操作包括：

-  如果网络不可用，告知用户。 如果它们已手动将其禁用 （如。 飞行模式或关闭 Wi-fi） 然后它们可以解决问题。
-  如果连接为 3g，应用程序的行为可能不同 （例如，Apple 不允许应用程序超过 20 Mb 要下载超过 3 的 G）。 应用程序可以使用此信息来警告用户过多的下载时间检索大型文件时。
-  即使可用的网络，很好的做法在启动其他请求之前验证与目标服务器的连接。 这将重复可防止应用程序的网络操作超时，并且还允许信息更丰富的错误消息显示给用户。


没有[Xamarin.iOS 示例](https://github.com/xamarin/monotouch-samples/tree/master/ReachabilitySample)可用 (基于 Apple 的[可访问性的示例代码](http://developer.apple.com/library/ios/#samplecode/Reachability/Introduction/Intro.html)) 以帮助检测网络可用性。


## <a name="webservices"></a>WebServices

在查看我们的文档[使用 Web 服务](~/cross-platform/data-cloud/web-services/index.md)，其中介绍了访问其余部分，使用 Xamarin.iOS 的 SOAP 和 WCF 终结点。 它可以向现有产生 web 服务请求并分析的响应，但是有一些库若要简化此要简单得多，包括 Azure、 RestSharp 和 ServiceStack。 即使基本 WCF 操作可以访问 Xamarin 应用程序。

### <a name="azure"></a>Azure

Microsoft Azure 是一个云平台，提供各种移动应用，包括数据存储和同步和推送通知服务。

请访问[azure.microsoft.com](https://azure.microsoft.com/)以免费试用。

### <a name="restsharp"></a>RestSharp

RestSharp 是一个.NET 库，可以包括在移动应用程序能够提供 REST 客户端，它简化了对 web 服务的访问。 通过提供一个简单的 API 来请求数据和分析 REST 响应，它可帮助。 RestSharp 很有用

[RestSharp 网站](http://restsharp.org/)包含[文档](https://github.com/restsharp/RestSharp/wiki)如何实现使用 RestSharp 的 REST 客户端。
RestSharp 提供有关 Xamarin.iOS 和 Xamarin.Android 示例[github](https://github.com/restsharp/RestSharp/)。

另外，还有中的 Xamarin.iOS 代码片段我们[Web 服务文档](~/cross-platform/data-cloud/web-services/index.md)。

 <a name="ServiceStack" />


### <a name="servicestack"></a>ServiceStack

与 RestSharp，不同 ServiceStack 是这两个服务器端解决方案来承载 web 服务，以及在移动应用程序访问这些服务可实现一个客户端库。

[ServiceStack 网站](http://servicestack.net/)到文档和代码示例说明了项目和链接的用途。 示例包括 web 服务，以及可以访问它的各种客户端应用程序的完整服务器端实现。

没有[Xamarin.iOS 示例](http://www.servicestack.net/monotouch/remote-info/)ServiceStack 网站中的代码片段上我们[Web 服务文档](~/cross-platform/data-cloud/web-services/index.md)。


### <a name="wcf"></a>WCF

Xamarin 工具可帮助你使用一些 Windows Communication Foundation (WCF) 服务。 一般情况下，Xamarin 支持相同的客户端将一部分附带 Silverlight 运行时的 WCF。 这包括最常见编码和协议的 WCF 的实现： 通过 HTTP 文本编码 SOAP 消息传输协议使用`BasicHttpBinding`。

由于大小和复杂性的 WCF 框架中，可能会将由 Xamarin 的客户端子集域不支持的范围之外的当前和将来的服务实现。 此外，WCF 支持需要仅在一个用于生成代理的 Windows 环境中可用的工具的用法。

 <a name="Threading" />


## <a name="threading"></a>线程

对于移动应用程序的应用程序响应能力至关重要 – 用户预期应用程序以加载并快速执行。 将停止接受用户输入将显示指示损坏应用程序，因此很重要不占用很长时间阻塞调用，例如网络请求或慢速本地操作 （如解压缩文件） 的 UI 线程冻结屏幕。 特别是在启动过程不应包含长时间运行的任务 – 所有移动平台将终止的应用程序所用时间过长加载。

这意味着你的用户界面应实现进度指示器或快速显示，否则为可用 UI 和异步任务来执行后台操作。 执行后台任务需要的使用的线程，这意味着后台任务需要一种方法，以便向主线程以指示进度或当他们已完成。

 <a name="Parallel_Task_Library" />


### <a name="parallel-task-library"></a>任务并行库

使用并行任务库创建的任务可以以异步方式运行，并返回其调用线程，使其用于触发而不阻止的用户界面的长时间运行的操作非常有用。

简单的并行任务操作可能如下所示：

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

该键`TaskScheduler.FromCurrentSynchronizationContext()`其将重复使用的调用方法的线程 SynchronizationContext.Current (此处运行的主线程`MainThreadMethod`) 作为一种方法，封送回调用给该线程。 这意味着如果 UI 线程上调用方法时，它将运行`ContinueWith`回 UI 线程上的操作。

如果代码从其他线程启动任务，请使用以下模式来创建对 UI 线程的引用，并且任务可以仍调用返回它：

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />


### <a name="invoking-on-the-ui-thread"></a>在 UI 线程上调用

对于不使用并行任务库的代码，每个平台都有自己的语法为回 UI 线程封送处理操作：

-  **iOS** – `owner.BeginInvokeOnMainThread(new NSAction(action))`
-  **Android** – `owner.RunOnUiThread(action)`
-  **Xamarin.Forms** – `Device.BeginInvokeOnMainThread(action)`
-  **Windows** – `Deployment.Current.Dispatcher.BeginInvoke(action)`



IOS 和 Android 语法需要一个 'context' 类成为可用，这意味着该代码需要将此对象传递到任何需要在 UI 线程上的回调的方法。

若要在共享代码中进行用户界面线程调用，请按照[IDispatchOnUIThread 示例](http://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net)(由[ @follesoe ](http://jonas.follesoe.no/))。 声明和计划`IDispatchOnUIThread`接口中的共享代码，然后实现的特定于平台的类，如下所示：

```csharp
// program to the interface in shared code
public interface IDispatchOnUIThread {
    void Invoke (Action action);
}
// iOS
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly NSObject owner;
    public DispatchAdapter (NSObject owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.BeginInvokeOnMainThread(new NSAction(action));
    }
}
// Android
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly Activity owner;
    public DispatchAdapter (Activity owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.RunOnUiThread(action);
    }
}
// WP7
public class DispatchAdapter : IDispatchOnUIThread {
    public void Invoke (Action action) {
        Deployment.Current.Dispatcher.BeginInvoke(action);
    }
}
```

Xamarin.Forms 开发人员应使用[ `Device.BeginInvokeOnMainThread` ](~/xamarin-forms/platform/device.md#Device_BeginInvokeOnMainThread) （共享项目或 PCL） 的公共代码中。

 <a name="Platform_and_Device_Capabilities_and_Degradation" />


## <a name="platform-and-device-capabilities-and-degradation"></a>平台和设备功能和性能降低

进一步平台功能文档中给出的处理不同的功能的特定示例。 它涉及到检测不同的功能以及如何适当降级应用程序提供良好的用户体验，即使应用程序无法运行，其全部功能。
