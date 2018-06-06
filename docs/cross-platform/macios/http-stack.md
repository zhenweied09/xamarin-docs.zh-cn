---
title: HttpClient 和 iOS/macOS 的 SSL/TLS 实现选择器
description: HttpClient 堆栈和 SSL/TLS 实现选择器确定将由 Xamarin iOS、 tvOS 或 macOS 应用程序的 HttpClient 和 SSL/TLS 实现。
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 9de2c97933bd33111a751be51e06dffe09794f15
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782263"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>HttpClient 和 iOS/macOS 的 SSL/TLS 实现选择器

**HttpClient 实现选择器**xamarin.ios，Xamarin.tvOS 和 Xamarin.Mac 控制其`HttpClient`实现来使用。 您可以切换到使用 iOS、 tvOS 或 macOS 本机传输实现 (`NSUrlSession`或`CFNetwork`，取决于操作系统)。 好的结果是 TLS 1.2 支持、 较小二进制文件，并更快地下载;其缺点在于，它需要事件循环正在运行要执行的异步操作。

项目必须引用**System.Net.Http**程序集。

> [!WARNING]
> **年 4 月，2018年**– 由于增强的安全性要求，包括 PCI 法规遵从性，主要云提供程序和 web 服务器应停止支持 TLS 版本早于 1.2。  在以前版本的 Visual Studio 默认使用 TLS 的较旧版本中创建 Xamarin 项目。
>
> 为了确保你的应用程序继续使用这些服务器和服务，**应更新与 Xamarin 项目`NSUrlSession`设置下面所示，然后重新生成并重新部署你的应用**到你的用户。

<a name="Selecting-a-HttpClient-Stack" />

### <a name="selecting-a-httpclient-stack"></a>选择 HttpClient 堆栈

若要调整`HttpClient`正由您的应用程序：

1. 双击**项目名称**中**解决方案资源管理器**若要打开项目选项。
2. 切换到**生成**为你的项目的设置 (例如， **iOS 生成**Xamarin.iOS 应用程序)。
3. 从**HttpClient 实现**下拉列表中，选择`HttpClient`类型作为以下项之一： **NSUrlSession** （推荐）， **CFNetwork**，或**管理**。

[![从托管、 CFNetwork 或 NSUrlSession 选择 HttpClient 实现](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> TLS 1.2 支持`NSUrlSession`建议选项。

<a name="NSUrlSession" />

### <a name="nsurlsession"></a>NSUrlSession

`NSURLSession`-基于处理程序基于本机`NSURLSession`framework 位于 iOS 7 和更高版本。 
**这是建议的设置。**

#### <a name="pros"></a>专业人员

- 它使用更好的性能和较小的可执行文件大小的本机 Api。
- 有关最新的标准，如 TLS 1.2 的支持。

#### <a name="cons"></a>Cons

- 需要 iOS 7 或更高版本。
- 某些`HttpClient`功能/选项将不可用。

<a name="CFNetwork" />

### <a name="cfnetwork"></a>CFNetwork

`CFNetwork`-基于处理程序基于本机`CFNetwork`framework 位于 iOS 6 和更高版本。

#### <a name="pros"></a>专业人员

- 它使用更好的性能和较小的可执行文件大小的本机 Api。
- 支持较新的标准，如 TLS 1.2。

#### <a name="cons"></a>Cons

- 需要 iOS 6 或更高版本。
- 在 watchOS 上不可用。
- 某些 HttpClient 功能/选项将不可用。

<a name="Managed" />

### <a name="managed"></a>Managed

托管处理程序的完全托管的 HttpClient 处理程序已使用以前版本的 Xamarin 返还。

#### <a name="pros"></a>专业人员

- 它具有的最兼容的功能集与 Microsoft.NET 和 Xamarin 的较旧版本。

#### <a name="cons"></a>Cons

- 它与 Apple Os 中不能完全集成，并仅限于 TLS 1.0。 它不能以连接到安全 web 服务器或在将来的云服务。
- 它通常要慢得多等加密在比本机 Api。
- 它要求更托管的代码，从而创建更大的应用程序可分发。

### <a name="programmatically-setting-the-httpmessagehandler"></a>以编程方式设置 HttpMessageHandler

除了上面所示项目范围的配置，你还可以实例化`HttpClient`和插入所需`HttpMessageHandler`通过构造函数，如这些代码段中所示：

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

这样便能以使用其他`HttpMessageHandler`从中已声明**项目选项**对话框。

<a name="New-SSL-TLS-implementation-build-option" />
<a name="Selecting-a-SSL-TLS-implementation" />
<a name="Apple-TLS" />

## <a name="ssltls-implementation"></a>SSL/TLS 实现

SSL （安全套接字层） 和及其后继，TLS （传输层安全性），提供对 HTTP 和通过其他网络连接支持`System.Net.Security.SslStream`。 Xamarin.iOS、 Xamarin.tvOS 或 Xamarin.Mac 的`System.Net.Security.SslStream`实现将调用 Apple 的本机 SSL/TLS 实现而不是使用由 Mono 提供的托管的实现。 Apple 的本机实现支持 TLS 1.2。

<a name="App-Transport-Security" />

## <a name="app-transport-security"></a>应用程序传输安全

Apple 的_应用传输安全_(ATS) 强制实施 internet 资源 （如应用程序的后端服务器） 和你的应用程序之间的安全连接。 ATS 可确保所有 internet 通信都符合以确保连接安全的最佳实践，从而防止意外泄露的敏感信息直接通过你的应用程序或正在使用的库。

由于默认情况下，在为 iOS 9、 tvOS 9 和 OS X 10.11 (El Capitan) 生成的应用程序中启用了 ATS 和更新版本中，使用的所有连接`NSUrlConnection`，`CFUrl`或`NSUrlSession`都将遵循 ATS 安全要求。 如果你的连接不能满足这些要求，则会失败并出现异常。

根据你的 HttpClient 堆栈和 SSL/TLS 实现选择，你可能需要对你的应用能正常使用 ATS 进行修改。

若要了解有关 ATS 的详细信息，请参阅我们[应用传输安全指南](~/ios/app-fundamentals/ats.md)。

## <a name="known-issues"></a>已知问题

本部分将介绍在 Xamarin.iOS 的 TLS 支持的已知的问题。

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>未能加载，出现错误"请求的值 AppleTLS 找不到"项目

Xamarin.iOS 9.8 引入包含某些新设置 **.csproj** Xamarin.iOS 应用程序文件。 使用较旧版本的 Xamarin.iOS 打开项目时，这些更改可能会导致问题。 下面的屏幕截图演示可能会显示在此方案中的错误消息：

![尝试加载的项目，时出错的屏幕截图请求值旧找不到](http-stack-images/tlserror-xs.png)

此错误引起的简介`MtouchTlsProvider`将设置为 Xamarin.iOS 9.8 中的项目文件。 如果它不是可以来更新到 Xamarin.iOS 9.8 （或更高版本），解决方法是手动编辑 **.csproj**文件应用程序中，删除`MtouchTlsprovider`元素，然后将保存已更改的项目文件。

下面的代码段演示了什么`MtouchTlsProvider`设置可能看起来像内 **.csproj**文件：

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>相关链接

- [传输层安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [应用传输安全性](~/ios/app-fundamentals/ats.md)
