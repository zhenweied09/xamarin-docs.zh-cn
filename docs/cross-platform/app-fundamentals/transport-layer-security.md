---
title: 传输层安全性 (TLS)
description: 为 Android、 iOS 和 Mac 上的 Xamarin 项目启用 TLS 1.2
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/10/2017
ms.openlocfilehash: 8b2d0288248f2468e6976ad4f7c46255690116c0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="transport-layer-security-tls"></a>传输层安全性 (TLS)

_为 Android、 iOS 和 Mac 上的 Xamarin 项目启用 TLS 1.2_

使用最新版本的[_传输层安全性_(TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)确保应用程序网络通讯都是安全非常重要。

> [!NOTE]
> Xamarin 释放自[年 2 月 2017年](https://releases.xamarin.com/stable-release-cycle-9/)默认情况下在新项目中使用 TLS 1.2。

TLS 1.2 支持现已在提供:

* Mono 4.8 (包括[TLS 1.2 支持](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support))
* Xamarin.iOS
* Xamarin.Mac
* Xamarin.Android （需要 Android 5.0 或更高版本）

项目必须引用**System.Net.Http**程序集。 

## <a name="updating-to-tls-12"></a>更新到 TLS 1.2

本部分介绍了某些 Xamarin 项目中的网络的配置选项，因此可以更新你_现有_应用可以充分利用更安全的协议。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

这些设置可在**项目选项 > Android 选项**，然后单击**高级**按钮： 

[![在 Visual Studio 中配置 HttpClient 和 TLS](transport-layer-security-images/properties-vs-sml.png)](transport-layer-security-images/properties-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
这些设置可在**项目属性 > 生成选项 > 高级**选项卡：

[![在 Xamarin Studio 和 Visual Studio 中配置 HttpClient 和 TLS，适用于 Mac](transport-layer-security-images/properties-xs-sml.png)](transport-layer-security-images/properties-xs.png#lightbox)

-----


### <a name="httpclient-implementation"></a>HttpClient 实现

Xamarin 开发人员始终都可以在其代码中使用的本机网络类，但是没有还由一个选项，确定哪些网络堆栈`HttpClient`类。 这提供了一个熟悉的.NET API 具有本机平台的速度和安全优势。

选项为：

- **托管的堆栈**– Mono 提供的网络功能，或
- **本机堆栈**– 各种网络 （Android、 iOS 或 macOS） 的基础平台提供的 Api。

托管的堆栈提供了与现有的.NET 代码的兼容性的最高级别，但它可以速度要慢，并导致较大的可执行文件大小。

本机选项可以更快和具有更佳的安全性 （包括 TLS 1.2），但可能无法提供的所有功能和选项`HttpClient`类。


### <a name="ssltls-implementation"></a>SSL/TLS 实现

项目选项还可让你选择哪一种 SSL/TLS 实现以支持：

- **Mono/托管**– 在 Android 上的 TLS 1.1、 TLS 1.0 在 iOS 和 macOS 上。
- **本机**– 在 Android、 iOS 和 macOS 上的 TLS 1.2。

新的 Xamarin 项目默认为支持 TLS 1.2 （该建议的所有项目） 的本机实现，但是你可以返回到托管代码中，如果切换所需的兼容性原因。

> [!IMPORTANT]
> **Mono/托管**中将删除选项[将来的版本](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/)。
>
> 建议使用本机选项。

## <a name="platform-specific-details"></a>特定于平台的详细信息

上述摘要说明 HttpClient 和 SSL/TLS 实现 Xamarin 项目中的项目级别设置。 HttpClient 实现也可以设置动态在代码中，并在 iOS 上有两个本机选项可供选择。

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS 和 Mac**](~/cross-platform/macios/http-stack.md)


## <a name="summary"></a>总结

只要有可能，应用程序应使用传输层安全 (TLS) 1.2。
新应用现在默认为此配置，但你可能需要更新现有应用程序根据本文中的说明中的设置。

## <a name="related-links"></a>相关链接

- [应用传输安全性](~/ios/app-fundamentals/ats.md)
- [Xamarin.Android Environment](~/android/deploy-test/environment.md)
- [Xamarin 周期 9 (自 2017 年 2 月)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Mono 4.8 发行说明-TLS 1.2 支持](http://www.mono-project.com/docs/about-monohttps://developer.xamarin.com/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient、 HttpClientHandler 和 WebRequestHandler 所述](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/en-us/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/en-us/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/en-us/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/en-us/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/en-us/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/en-us/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](http://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](https://developer.xamarin.com/api/type/CoreFoundation.CFNetwork/)
- [Foundation.NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)
- [System.Net.WebRequest](https://msdn.microsoft.com/en-us/library/system.net.webrequest(v=vs.110).aspx)
- [HTTP 客户端 （示例）](https://developer.xamarin.com/samples/monotouch/HttpClient/)
