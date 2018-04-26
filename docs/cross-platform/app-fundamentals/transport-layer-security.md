---
title: 传输层安全性 (TLS) 1.2
description: 为 Android、 iOS 和 Mac 上的 Xamarin 项目启用 TLS 1.2
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 6205e8633ccdd2c1e568e7de8103c38eb9edbc2f
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2018
---
# <a name="transport-layer-security-tls-12"></a>传输层安全性 (TLS) 1.2

使用最新版本的[_传输层安全性_(TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)确保应用程序网络通讯都是安全非常重要。

> [!WARNING]
> **年 4 月，2018年**– 由于增强的安全性要求，包括 PCI 法规遵从性，主要云提供程序和 web 服务器应停止支持 TLS 版本早于 1.2。  在以前版本的 Visual Studio 默认使用 TLS 的较旧版本中创建 Xamarin 项目。
>
> 为了确保你的应用程序继续使用这些服务器和服务，**应更新 Xamarin 项目以使用的设置，然后重新生成并重新部署你的应用**到你的用户。

项目必须引用**System.Net.Http**程序集，如下所示进行配置。

## <a name="update-android-to-tls-12"></a>TLS 1.2 更新 Android

更新**HttpClient 实现**和**SSL/TLS 实现**用于启用 TLS 1.2 安全选项。

> [!NOTE]
> 需要 Android 5.0 或更高版本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

这些设置可在**项目属性 > Android 选项**，然后单击**高级**按钮：

[![在 Visual Studio 中配置 HttpClient 和 TLS](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

这些设置可在**项目选项 > 生成 > Android 生成**选项卡：

[![在 Visual Studio 中配置 HttpClient 和 TLS，适用于 Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-ios-to-tls-12"></a>TLS 1.2 更新 iOS

更新**HttpClient 实现**选项以启用 TSL 1.2 安全。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

此设置可在**项目属性 > iOS 生成**:

[![在 Visual Studio 中配置 HttpClient 和 TLS](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

此设置可在**项目选项 > 生成 > iOS 生成**选项卡：

[![在 Visual Studio 中为 Mac 配置 HttpClient](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-macos-to-tls-12-in-visual-studio-for-mac"></a>适用于 Mac 的 Visual Studio 中的 TLS 1.2 更新 macOS

更新**HttpClient 实现**选项**项目选项 > 生成 > Mac 生成**选项卡来启用 TSL 1.2 安全：

[![在 Visual Studio 中为 Mac 配置 HttpClient](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

## <a name="alternative-configuration-options"></a>备用配置选项

本部分讨论上面所示的 TLS 1.2 支持配置的替代方法。
应用程序开发人员只应考虑以下替代方法，如果他们了解以及使用不同级别的 TLS 支持的风险。

### <a name="httpclient-implementation"></a>HttpClient 实现

Xamarin 开发人员始终都可以在其代码中使用的本机网络类，但是没有还由一个选项，确定哪些网络堆栈`HttpClient`类。 这提供了一个熟悉的.NET API 具有本机平台的速度和安全优势。

选项为：

- **托管的堆栈**– Mono 提供的网络功能，或
- **本机堆栈**– 各种网络 （Android、 iOS 或 macOS） 的基础平台提供的 Api。

托管的堆栈提供了与现有的.NET 代码的兼容性的最高级别，但它可以速度要慢，并导致较大的可执行文件大小。

本机选项可以更快和具有更佳的安全性 （包括 TLS 1.2），但可能无法提供的所有功能和选项`HttpClient`类。

### <a name="ssltls-implementation-android"></a>SSL/TLS 实现 (Android)

Android 项目选项还可让你选择哪一种 SSL/TLS 实现以支持：

- **Mono/托管**– 在 Android 上的 TLS 1.1
- **本机**– 在 Android 上的 TLS 1.2。

新的 Xamarin 项目默认为支持 TLS 1.2 （该建议的所有项目） 的本机实现，但是你可以返回到托管代码中，如果切换所需的兼容性原因。

> [!IMPORTANT]
> **Mono/托管**选项已被[删除从 iOS 和 Mac](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/)项目选项。
>
> 在 iOS 和 Mac 平台上始终使用本机选项。

## <a name="platform-specific-details"></a>特定于平台的详细信息

上述摘要说明 HttpClient 和 SSL/TLS 实现 Xamarin 项目中的项目级别设置。 此外可以在代码中动态设置 HttpClient 实现。 有关详细信息这些特定于平台的指南，请参阅：

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS 和 Mac**](~/cross-platform/macios/http-stack.md)


## <a name="summary"></a>总结

只要有可能，应用程序应使用传输层安全 (TLS) 1.2。
应更新现有应用程序根据本文中中的说明中的设置，然后重新生成和重新部署到你的客户。

## <a name="related-links"></a>相关链接

- [应用传输安全性](~/ios/app-fundamentals/ats.md)
- [Xamarin.Android 环境](~/android/deploy-test/environment.md)
- [Xamarin 周期 9 (自 2017 年 2 月)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Mono 4.8 发行说明-TLS 1.2 支持](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient、 HttpClientHandler 和 WebRequestHandler 所述](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](http://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](https://developer.xamarin.com/api/type/CoreFoundation.CFNetwork/)
- [Foundation.NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [HTTP 客户端 （示例）](https://developer.xamarin.com/samples/monotouch/HttpClient/)
