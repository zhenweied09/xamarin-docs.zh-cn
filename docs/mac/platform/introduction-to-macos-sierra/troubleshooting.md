---
title: "疑难解答"
description: "本文提供使用 macOS Sierra Xamarin.Mac 应用中的多个故障排除提示。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/22/2016
ms.openlocfilehash: 739cffb2b5418e4fc52bd91f97f4123b01abf0c7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshooting"></a>疑难解答

_本文提供使用 macOS Sierra Xamarin.Mac 应用中的多个故障排除提示。_

以下各节列出使用 macOS Sierra Xamarin.mac 与这些问题的解决方案时可能发生的一些已知的问题：

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [二进制兼容性](#Binary-Compatibility)
- [CFNetwork HTTP Protocol](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [CoreImage](#CoreImage)
- [通知](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>应用商店

已知问题：

- 时沙盒环境中测试应用内购买，身份验证对话框中可能出现两次。
- 当使用沙盒环境中承载的内容测试应用内购买，每次应用程序置于前台，直到内容下载完成时，将出现密码对话框。

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

如果将新的支付卡添加到 Apple Pay 时输入一个不正确的到期日期或安全代码 （顺时针），则将终止设置过程的卡。

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>二进制兼容性

已知问题：

- 调用`NSObject.ValueForKey`将`null`密钥将导致异常。
- 同时`NSURLSession`和 NSURLConnection` no longer RC4 cipher suites during the TLS handshake for `http:// Url。
- 如果它们修改超级视图的几何图形中的应用可以挂起`ViewWillLayoutSubviews`或`LayoutSubviews`方法。
- 对于所有 SSL/TLS 连接，RC4 对称密码现在默认处于禁用状态。 此外，安全传输 API 不再支持 SSLv3 和建议应用程序停止使用 sha-1 和 3DES 加密越早越好。

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>CFNetwork HTTP 协议

`HTTPBodyStream`属性`NSMutableURLRequest`类必须设置为自以来的未打开流`NSURLConnection`和`NSURLSession`现在严格强制此要求。

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

长时间运行的操作将返回_"你无权保存文件。"_ 错误。

<a name="CoreImage" />

## <a name="coreimage"></a>CoreImage

`CIImageProcessor` API 现在支持任意输入的图像计数。 `CIImageProcessor` 将删除已包含在 macOS Sierra beta 1 的 API。

<a name="Notifications" />

## <a name="notifications"></a>通知

在使用通知内容扩展，视图控制器未被正确释放和达到扩展内存限制时，可能会导致崩溃。

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

提交操作后，`UserInfo`属性`NSUserActivity`对象可能为空。 显式调用`BecomeCurrent``NSUserActivity`对象作为当前的解决方法。

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation 需要一种安全的 (`https://`) URL 以在 iOS 10 和 macOS Sierra 以防止恶意使用位置数据上工作。







## <a name="related-links"></a>相关链接

- [Mac 示例](https://developer.xamarin.com/samples/mac/)
- [OS X 10.12 的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
