---
title: "HttpClient 堆栈和适用于 Android 的 SSL/TLS 实现选择器"
description: "HttpClient 堆栈和 SSL/TLS 实现选择器确定将由您的 Xamarin.Android 应用程序的 HttpClient 和 SSL/TLS 实现。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: bcb6f033c7fad76a17a7a5aa82f48a76b1ae501d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>HttpClient 堆栈和适用于 Android 的 SSL/TLS 实现选择器

_HttpClient 堆栈和 SSL/TLS 实现选择器确定将由您的 Xamarin.Android 应用程序的 HttpClient 和 SSL/TLS 实现。_

## <a name="overview"></a>概述

Xamarin.Android 提供两个将控制 Android 应用程序的 TLS 设置的组合框。 一个组合框将确定哪些`HttpMessageHandler`实例化时将使用`HttpClient`对象，而其他标识 web 请求时将使用哪个 TLS 实现。

> [!NOTE]
> **注意：**项目必须引用**System.Net.Http**程序集。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Xamarin.Android 项目的项目选项中找到 HttpClient 堆栈的设置。 单击**Android 选项**选项卡上，然后单击**高级选项**按钮。 这将显示**高级 Android 选项**对话框该对话框具有两个组合框、 HttpClient 实现和 SSL/TLS 实现：


[ ![Android 的 visual Studio 选项](http-stack-images/tls07-vs-sml.png)](http-stack-images/tls07-vs.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

HttpClient 堆栈的设置项目选项中找到 Xamarin.Android 项目。 单击**生成 > Android 生成**设置，然后单击**常规**选项卡：

[ ![Visual Studio for Mac Android 选项](http-stack-images/tls07-xs-sml.png)](http-stack-images/tls07-xs.png)


-----

## <a name="httpclient-stack-selector"></a>HttpClient 堆栈选择器

此项目选项控制这`HttpMessageHandler`实现将使用每次`HttpClient`实例化对象。 默认情况下，这是托管`HttpClientHandler`。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![Visual Studio 中的 android HttpClient 实现组合框](http-stack-images/tls04-vs-sml.png)](http-stack-images/tls04-vs.png) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![适用于 Mac 的 Visual Studio 中的 android HttpClient 实现组合框](http-stack-images/tls04-xs.png )

-----

### <a name="managed-httpclienthandler"></a>托管 (HttpClientHandler)

托管处理程序是随 Xamarin.Android 旧版完全托管的 HttpClient 处理程序。

#### <a name="pros"></a>专业人员：

- 它是最兼容 （功能） 与 MS.NET 和 Xamarin 的较旧版本。

#### <a name="cons"></a>缺点：

- 它是未完全集成与操作系统 （如。 限制为 TLS 1.0）。
- 它是通常要慢得多 （如。 加密） 比本机 API。
- 它要求更托管的代码，创建更大的应用程序。

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler 是的新处理程序委托给本机 Java/OS 代码，而不是在托管代码中实现的所有内容。

#### <a name="pros"></a>专业人员：

- 用于更好的性能和较小的可执行文件大小的本机 API。
- 支持最新的标准，例如。 TLS 1.2。

#### <a name="cons"></a>缺点：

- 需要 Android 5.0 或更高版本。
- 某些 HttpClient 功能/选项将不可用。


### <a name="programatically-using-androidclienthandler"></a>以编程方式使用 `AndroidClientHandler`

`Xamarin.Android.Net.AndroidClientHandler`是`HttpMessageHandler`专门针对 Xamarin.Android 的实现。 此类的实例将使用本机`java.net.URLConnection`实现所有的 HTTP 连接。 从理论上讲，这将使 HTTP 性能和较小 APK 大小的增加。

此代码片段演示了如何为单个实例的显式`HttpClient`类：

```csharp
// Android 5.0 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
>  **请注意**： 基础的 Android 设备必须支持 TLS 1.2 (ie。Android 5.0 及更高版本)


## <a name="ssltls-implementation-build-option"></a>SSL/TLS 实现生成选项

此项目选项控制哪些基础 TLS 库将使用所有 web 请求，同时`HttpClient`和`WebRequest`。 默认情况下，TLS 1.2 处于选定状态：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![Visual Studio 中实现 TLS/SSL 组合框](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![适用于 Mac 的 Visual Studio 中实现 TLS/SSL 组合框](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png)

-----

例如:

```csharp
var client = new HttpClient();
```

如果 HttpClient 实现设置为**托管**且 TLS 实现被设置为**本机 TLS 1.2 +**，则`client`对象将自动使用的托管`HttpClientHandler`和TLS 1.2 （BoringSSL 库提供） 用于其 HTTP 请求。

但是，如果**HttpClient 实现**设置为`AndroidHttpClient`，然后所有`HttpClient`对象将使用的基础的 Java 类`java.net.URLConnection`和将不受**实现TLS/SSL**值。 `WebRequest` 对象将使用 BoringSSL 库。

## <a name="other-ways-to-control-ssltls-configuration"></a>其他方法来控制 SSL/TLS 配置

有三种方法 Xamarin.Android 应用程序可以控制的 TLS 设置：

1. 在项目选项中选择 HttpClient 实现和默认 TLS 库。
2. 以编程方式使用`Xamarin.Android.Net.AndroidClientHandler`。
3. 环境变量声明 （可选）。

三个选项的推荐的方法是使用 Xamarin.Android 项目选项来声明默认`HttpMessageHandler`和整个应用程序的 TLS。 然后，如果有必要，以编程方式进行实例化`Xamarin.Android.Net.AndroidClientHandler`对象。
上面介绍了这些选项。

第三个选项&ndash;使用环境变量&ndash;如下所述。

### <a name="declare-environment-variables"></a>声明环境变量

有两个与 Xamarin.Android 中的 TLS 的使用相关的环境变量：

-   `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; 此环境变量声明默认`HttpMessageHandler`应用程序将使用。 例如:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

-   `XA_TLS_PROVIDER` &ndash; 此环境变量将声明其中 TLS 库将使用，或者`btls`， `legacy`，或`default`（这是与省略此变量相同）：

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

通过添加设置此环境变量_环境文件_到项目。 环境文件是具有生成操作的 Unix 格式的纯文本文件**AndroidEnvironment**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Visual Studio 中的 AndroidEnvironment 生成操作的屏幕截图。](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![AndroidEnvironment 的屏幕快照生成操作在 Visual Studio for mac。](http-stack-images/tls03-xs.png)

-----

请参阅[Xamarin.Android 环境](~/android/deploy-test/environment.md)有关环境变量和 Xamarin.Android 的更多详细信息的指南。


## <a name="related-links"></a>相关链接

- [传输层安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
