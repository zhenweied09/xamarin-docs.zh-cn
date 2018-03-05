---
title: "应用程序传输安全"
description: "应用程序传输安全性 (ATS) 强制实施 internet 资源 （如应用程序的后端服务器） 和你的应用程序之间的安全连接。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/13/2017
ms.openlocfilehash: 60858e05e222725f05eb67bd7aaa4e56d2ff3880
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="app-transport-security"></a>应用程序传输安全

_应用程序传输安全性 (ATS) 强制实施 internet 资源 （如应用程序的后端服务器） 和你的应用程序之间的安全连接。_

本文将介绍应用程序传输安全性 iOS 9 应用强制实施的安全更改和[这为你的 Xamarin.iOS 项目意味着](#xamarinsupport)，它将涵盖[ATS 配置选项](#config)和它将讲解如何[选择退出的 ATS](#optout) ATS 必要情况。 因为默认情况下启用了 ATS，任何不安全的 internet 连接将引发 iOS 9 应用中的异常，（除非已显式允许它）。


## <a name="about-app-transport-security"></a>有关应用程序传输安全

如上面所述，ATS 可确保所有 internet 通信，在 iOS 9 和 OS X El Capitan 都符合以确保连接安全的最佳实践，从而防止意外泄露的敏感信息直接通过你的应用程序或它是一个库使用。

对于现有应用程序，实现`HTTPS`协议尽可能。 对于新的 Xamarin.iOS 应用程序，应使用`HTTPS`以独占方式与 internet 资源通信时。 此外，必须使用向前保密 TLS 版本 1.2 加密高级 API 通信。

建立与任何连接[NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)， [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/)或[NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/)将默认情况下，在为 iOS 9 和 OS X 10.11 (El Capitan) 构建的应用程序中使用 ATS。

## <a name="default-ats-behavior"></a>默认 ATS 行为

由于默认情况下，在生成适用于 iOS 9 和 OS X 10.11 (El Capitan) 使用的所有连接的应用程序中启用了 ATS [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)， [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/)或[NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/)依据ATS 安全要求。 如果你的连接不满足这些要求，则会失败并出现异常。

### <a name="ats-connection-requirements"></a>ATS 连接要求

ATS 将强制执行所有 internet 连接的以下要求：

- 所有连接密码必须都使用向前保密。 请参阅下面的接受密码的列表。
- 传输层安全 (TLS) 协议必须为 1.2 或更高版本。
- 至少 SHA256 指纹为 2048 位或更高版本的 RSA 密钥，或 256 位或更高版本的椭圆曲线 (ECC) 密钥使用必须用于所有的证书。

同样，由于默认情况下，在 iOS 9 中启用了 ATS，任何尝试进行不满足这些要求的连接将导致引发异常。 

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>ATS 兼容密码

通过安全的 internet 通信的 ATS 接受以下向前保密的密码类型：

- `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA`
- `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`

有关使用 iOS internet 通信类的详细信息，请参阅 Apple 的[NSURLConnection 类引用](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755)， [CFURL 引用](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206)或[NSURLSession 类引用](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435).

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>在 Xamarin.iOS 中支持 ATS

因为 ATS 启用在 iOS 9 和 OS X El Capitan，默认情况下，如果你的 Xamarin.iOS 应用程序或任何库或正在使用的服务建立连接到 internet，你将需要采取某项操作，或你的连接将导致引发异常。

对于现有应用程序，Apple 建议你支持`HTTPS`协议越早越好。 如果你是无法，因为你正在连接的第三方不支持的 web 服务`HTTPS`或如果支持`HTTPS`是不现实，你可以选择退出的 ATS。 请参阅[Opting 打卡的 ATS](#Opting-Out-of-ATS)下面部分以了解更多详细信息。

对于新的 Xamarin.iOS 应用程序，应使用`HTTPS`以独占方式与 internet 资源通信时。 同样，可能有 （例如使用第三方 web 服务） 的情况下这不是可能，其中你将需要选择退出的 ATS。

此外，ATS 强制实施高级 API 通信进行加密 TLS 版本 1.2 使用向前保密。 请参阅[ATS 连接要求](#ATS-Connection-Requirements)和[ATS 兼容密码](#ATS-Compatible-Ciphers)上面部分的更多详细信息。

虽然你可能不熟悉 TLS ([传输层安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security)) 它是到 SSL 的后继版本 ([安全套接字层](https://en.wikipedia.org/wiki/Transport_Layer_Security)) 和提供的加密协议上实施安全的集合网络连接。

TLS 级别控制要使用的 web 服务，因此在应用程序的控制之外。 同时`HttpClient`和`ModernHttpClient`应自动使用 TLS 加密服务器支持的最高级别。

根据服务器时，您交谈 （尤其是如果它是第三方服务），你可能需要禁用向前保密或选择较低的 TLS 级别。 请参阅[配置 ATS 选项](#Configuring-ATS-Options)下面部分以了解更多详细信息。

> [!IMPORTANT]
> **注意：**应用传输安全不适用于 Xamarin 应用程序使用**托管 HTTPClient 实现**。 它适用于连接使用 CFNetwork **HTTPClient 实现**或**NSURLSession HTTPClient 实现**仅。

### <a name="setting-the-httpclient-implementation"></a>设置 HTTPClient 实现

若要设置 HTTPClient 实现使用的 iOS 应用程序，请双击**项目**中**解决方案资源管理器**以打开**项目选项**。 导航到**iOS 生成**，然后选择所需的客户端类型下**HttpClient 实现**下拉列表中：

![](ats-images/client01.png "设置 iOS 生成选项")


#### <a name="managed-handler"></a>托管处理程序

托管处理程序是完全托管的 HttpClient 并的处理程序已运送与以前版本的 Xamarin.iOS 是默认处理程序。

专业人员：

- 它是随 Microsoft.NET 和较旧版本的 Xamarin 兼容。

缺点：

- 它是未完全与集成 iOS （例如它被限制为 TLS 1.0）。
- 它是通常比本机 Api 要慢得多。
- 它需要更多托管的代码，并创建更大的应用。

#### <a name="cfnetwork-handler"></a>CFNetwork 处理程序

CFNetwork 基于处理程序基于本机`CFNetwork`framework。

专业人员：

- 使用更好的性能和较小的可执行文件大小的本机 API。
- 添加了对较新的标准，如 TLS 1.2 支持。

缺点：

- 需要 iOS 6 或更高版本。
- WatchOS 不可用。
- 某些 HttpClient 功能和选项将不可用。

#### <a name="nsurlsession-handler"></a>NSUrlSession Handler

NSUrlSession 基于处理程序基于本机`NSUrlSession`API。

专业人员：

- 使用更好的性能和较小的可执行文件大小的本机 API。
- 添加了对较新的标准，如 TLS 1.2 支持。

缺点：

- 需要 iOS 7 或更高版本。
- 某些 HttpClient 功能和选项将不可用。 

## <a name="diagnosing-ats-issues"></a>诊断 ATS 问题

时尝试连接到 internet，直接或从 web 视图在 iOS 9 中，可能会在窗体中出现错误：

> 应用程序传输安全已阻止明文形式 HTTP (http://www.-the-blocked-domain.com) 资源负载，因为它是不安全。 可以通过应用的 Info.plist 文件配置临时例外。

在 iOS9，应用程序传输安全 (ATS) 强制实施 internet 资源 （如应用程序的后端服务器） 和你的应用程序之间的安全连接。 此外，ATS 需要通信使用`HTTPS`协议和高级 API 通信进行加密 TLS 版本 1.2 使用向前保密。

由于默认情况下，在生成适用于 iOS 9 和 OS X 10.11 (El Capitan) 使用的所有连接的应用程序中启用了 ATS `NSURLConnection`，`CFURL`或`NSURLSession`都将遵循 ATS 安全要求。 如果你的连接不满足这些要求，则会失败并出现异常。

此外提供了 Apple [TLSTool 示例应用程序](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2)可以编译 （或 （可选） 将转换为 Xamarin 和 C#），用于诊断 ATS/TLS 问题。 请参阅[Opting 打卡的 ATS](#Opting-Out_of_ATS)节如何解决此问题的信息。


<a name="config" />

## <a name="configuring-ats-options"></a>配置 ATS 选项

你可以通过在您的应用程序中设置特定的键的值配置的几项 ATS 功能**Info.plist**文件。 以下项是可用于控制 ATS (_缩进以显示它们嵌套_):

```csharp
NSAppTransportSecurity
    NSAllowsArbitraryLoads
    NSAllowsArbitraryLoadsInWebContent
    NSExceptionDomains
    <domain-name-for-exception-as-string>
        NSExceptionMinimumTLSVersion
        NSExceptionRequiresForwardSecrecy
        NSExceptionAllowsInsecureHTTPLoads
        NSRequiresCertificateTransparency
        NSIncludesSubdomains
        NSThirdPartyExceptionMinimumTLSVersion
        NSThirdPartyExceptionRequiresForwardSecrecy
        NSThirdPartyExceptionAllowsInsecureHTTPLoads
```

每个密钥都有以下类型和含义：

- **NSAppTransportSecurity** (`Dictionary`)-包含所有设置键和值 ATS。
- **NSAllowsArbitraryLoads** (`Boolean`)-如果`YES`ATS 将被禁用的任何域**不**列入`NSExceptionDomains`。 对于列出的域，将使用指定的安全设置。
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`)-如果`YES`将允许 web 页时为应用程序的其余部分仍启用 Apple 传输安全 (ATS) 保护正确加载。
- **NSExceptionDomains** (`Dictionary`)-的域的集合，以及 ATS 应使用为给定的域的安全设置。
- **< Domain-name-for-exception-as-string >** (`Dictionary`) 的异常 （如的给定域的集合 `www.xamarin.com`）格式模式中出现的位置生成。
- **NSExceptionMinimumTLSVersion** (`String`)-为最小 TLS 版本`TLSv1.0`，`TLSv1.1`或`TLSv1.2`（这是默认值）。
- **NSExceptionRequiresForwardSecrecy** (`Boolean`)-如果`NO`域不需要使用密码，正向安全。 默认值为 `YES`。
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`)-如果`NO`（默认值） 与此域的所有通信都必须在`HTTPS`协议。
- **NSRequiresCertificateTransparency** (`Boolean`)-如果`YES`域的安全套接字层 (SSL) 必须包含有效的透明数据。 默认值为 `NO`。
- **NSIncludesSubdomains** (`Boolean`)-如果`YES`这些设置重写此域的所有子域。 默认值为 `NO`。
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`)-使用域时在开发人员控制以外的第三方服务的 TLS 版本。
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`)-如果`YES`第三方域需要向前保密。
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`)-如果`YES`ATS 将允许与第三方域不安全的通信。

<a name="optout" />

### <a name="opting-out-of-ats"></a>选择向外的 ATS

虽然 Apple 强烈建议使用`HTTPS`协议和到 internet 的安全通信基于信息，可能这并不总是可行的时间。 例如，如果你要与第三方 web 服务通信，或使用 internet 应用程序中传递广告。

如果你的 Xamarin.iOS 应用程序必须对不安全的域进行请求，以下更改到应用程序的**Info.plist**文件将禁用 ATS 强制实施为给定的域的安全默认值：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>www.the-domain-name.com</key>
        <dict>
            <key>NSExceptionMinimumTLSVersion</key>
            <string>TLSv1.0</string>
            <key>NSExceptionRequiresForwardSecrecy</key>
            <false/>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSIncludesSubdomains</key>
            <true/>
        </dict>
    </dict>
</dict>
```

在适用于 Mac 的 Visual Studio，双击`Info.plist`文件中**解决方案资源管理器**，切换到**源**查看和添加上述密钥：

[ ![](ats-images/ats01.png "Info.plist 文件的源视图")](ats-images/ats01.png)


如果你的应用需要加载和显示来自不安全站点的 web 内容时，将以下代码添加到应用程序的**Info.plist**文件，以便网页时的其余部分仍启用 Apple 传输安全 (ATS) 保护正确加载应用程序：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

（可选） 可以进行以下更改到应用程序的**Info.plist**文件要彻底禁用 ATS，为所有域和 internet 通信：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

在适用于 Mac 的 Visual Studio，双击`Info.plist`文件中**解决方案资源管理器**，切换到**源**查看和添加上述密钥：

[ ![](ats-images/ats02.png "Info.plist 文件的源视图")](ats-images/ats02.png)

> [!IMPORTANT]
> **注意：**如果你的应用程序需要连接到不安全的网站，您应该**始终**异常使用输入域`NSExceptionDomains`而不是关闭 ATS 完全使用`NSAllowsArbitraryLoads`。 `NSAllowsArbitraryLoads` 仅应在极端紧急情况下使用。




同样，禁用 ATS 应_仅_用作最后一招，如果切换到安全的连接是不可用或不切实际。

<a name="Summary" />

## <a name="summary"></a>摘要

这篇文章已引入应用程序传输安全 (ATS)，并描述它会强制实施与 internet 的安全通信的方式。 首先，我们介绍 ATS 需要在 iOS 9 上运行一个 Xamarin.iOS 应用程序的更改。 然后，我们介绍控制 ATS 功能和选项。 最后，我们介绍了选择退出 ATS Xamarin.iOS 应用程序中。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
