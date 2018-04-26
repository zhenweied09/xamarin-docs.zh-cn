---
title: HttpClient 堆栈和适用于 Android 的 SSL/TLS 实现选择器
description: HttpClient 堆栈和 SSL/TLS 实现选择器确定将由您的 Xamarin.Android 应用程序的 HttpClient 和 SSL/TLS 实现。
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 04/20/2018
ms.openlocfilehash: bedcf0603fffc9886155881f91972203104ba155
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2018
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>HttpClient 堆栈和适用于 Android 的 SSL/TLS 实现选择器

HttpClient 堆栈和 SSL/TLS 实现选择器确定将由您的 Xamarin.Android 应用程序的 HttpClient 和 SSL/TLS 实现。

项目必须引用**System.Net.Http**程序集。

> [!WARNING]
> **年 4 月，2018年**– 由于增强的安全性要求，包括 PCI 法规遵从性，主要云提供程序和 web 服务器应停止支持 TLS 版本早于 1.2。  在以前版本的 Visual Studio 默认使用 TLS 的较旧版本中创建 Xamarin 项目。
>
> 为了确保你的应用程序继续使用这些服务器和服务，**应更新与 Xamarin 项目`Android HttpClient`和`Native TLS 1.2`下面所示的设置，然后重新生成和重新部署你的应用**到你用户。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

正在 Xamarin.Android HttpClient 配置**项目选项 > Android 选项**，然后单击**高级选项**按钮。

这些是 TLS 1.2 支持的建议的设置：

[![Android 的 visual Studio 选项](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

正在 Xamarin.Android HttpClient 配置**项目选项 > 生成 > Android 生成**设置，然后单击**常规**选项卡。

这些是 TLS 1.2 支持的建议的设置：

[![Visual Studio for Mac Android 选项](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>备用配置选项

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler 是的新处理程序委托给本机 Java/OS 代码，而不是在托管代码中实现的所有内容。
**这是建议的选项。**

#### <a name="pros"></a>专业人员

- 用于更好的性能和较小的可执行文件大小的本机 API。
- 支持最新的标准，例如。 TLS 1.2。

#### <a name="cons"></a>Cons

- 需要 Android 5.0 或更高版本。
- 某些 HttpClient 功能/选项将不可用。

### <a name="managed-httpclienthandler"></a>托管 (HttpClientHandler)

托管处理程序是随 Xamarin.Android 旧版完全托管的 HttpClient 处理程序。

#### <a name="pros"></a>专业人员

- 它是最兼容 （功能） 与 MS.NET 和 Xamarin 的较旧版本。

#### <a name="cons"></a>Cons

- 它是未完全集成与操作系统 （如。 限制为 TLS 1.0）。
- 它是通常要慢得多 （如。 加密） 比本机 API。
- 它要求更托管的代码，创建更大的应用程序。



### <a name="choosing-a-handler"></a>选择一个处理程序

选择`AndroidClientHandler`和`HttpClientHandler`取决于你的应用程序的需求。 `AndroidClientHandler` 建议的最新的安全支持，例如。

-   你要求 TLS 1.2 + 支持。
-   你的应用所面向 Android 5.0 (API 21) 或更高版本。
-   你需要 TLS 1.2 + 支持`HttpClient`。
-   不需要支持 TLS 1.2 + `WebClient`。

`HttpClientHandler` 是一个不错的选择，如果你需要 TLS 1.2 + 支持，但必须支持早于 Android 5.0 的 Android 版本。 它也是一个不错的选择，如果你需要 TLS 1.2 + 支持`WebClient`。

从开始 Xamarin.Android 8.3`HttpClientHandler`默认为单调 SSL (`btls`) 为基础的 TLS 提供程序。 单调 SSL TLS 提供程序提供以下优势：

-   它支持 TLS 1.2。
-   它支持所有的 Android 版本。
-   它提供了两个 TLS 1.2 支持`HttpClient`和`WebClient`。

为基础 TLS 提供程序使用单调 SSL 的缺点是，它可以增加生成的 APK （它将添加大约 1 MB 的每个受支持的 ABI 的其他 APK 大小） 的大小。

从 Xamarin.Android 8.3 开始，默认的 TLS 提供程序单调 SSL (`btls`)。 如果您不想要使用单调 SSL，你可以通过设置还原到的历史的托管 SSL 实现`$(AndroidTlsProvider)`属性`legacy`(有关设置生成属性的详细信息，请参阅[生成过程](~/android/deploy-test/building-apps/build-process.md))。


### <a name="programatically-using-androidclienthandler"></a>以编程方式使用 `AndroidClientHandler`

`Xamarin.Android.Net.AndroidClientHandler`是`HttpMessageHandler`专门针对 Xamarin.Android 的实现。
此类的实例将使用本机`java.net.URLConnection`实现所有的 HTTP 连接。 从理论上讲，这将使 HTTP 性能和较小 APK 大小的增加。

此代码片段演示了如何为单个实例的显式`HttpClient`类：

```csharp
// Android 5.0 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> 基础的 Android 设备必须支持 TLS 1.2 (ie。Android 5.0 及更高版本)


## <a name="ssltls-implementation-build-option"></a>SSL/TLS 实现生成选项

此项目选项控制哪些基础 TLS 库将使用所有 web 请求，同时`HttpClient`和`WebRequest`。 默认情况下，TLS 1.2 处于选定状态：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Visual Studio 中实现 TLS/SSL 组合框](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![适用于 Mac 的 Visual Studio 中实现 TLS/SSL 组合框](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

例如：

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

三个选项的推荐的方法是使用 Xamarin.Android 项目选项来声明默认`HttpMessageHandler`和整个应用程序的 TLS。 然后，如果有必要，以编程方式进行实例化`Xamarin.Android.Net.AndroidClientHandler`对象。 上面介绍了这些选项。

第三个选项&ndash;使用环境变量&ndash;如下所述。

### <a name="declare-environment-variables"></a>声明环境变量

有两个与 Xamarin.Android 中的 TLS 的使用相关的环境变量：

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; 此环境变量声明默认`HttpMessageHandler`应用程序将使用。 例如：

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; 此环境变量将声明其中 TLS 库将使用，或者`btls`， `legacy`，或`default`（这是与省略此变量相同）：

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

通过添加设置此环境变量_环境文件_到项目。 环境文件是具有生成操作的 Unix 格式的纯文本文件**AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Visual Studio 中的 AndroidEnvironment 生成操作的屏幕截图。](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![AndroidEnvironment 的屏幕快照生成操作在 Visual Studio for mac。](http-stack-images/tls03-xs.png)

-----

请参阅[Xamarin.Android 环境](~/android/deploy-test/environment.md)有关环境变量和 Xamarin.Android 的更多详细信息的指南。


## <a name="related-links"></a>相关链接

- [传输层安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
