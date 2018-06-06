---
title: watchOS 3 故障排除
description: 使用 Xamarin 中 watchOS 3 时，本文档提供多个有用的故障排除提示。 提示关联的活动、 Apple Pay、 后台刷新、 NSURLConnection、 隐私和的详细信息。
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 0aca2c96533e17e4aeb2f57d38a87d39f700fb45
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791021"
---
# <a name="watchos-3-troubleshooting"></a>watchOS 3 故障排除

_本文提供使用 watchOS 3 Xamarin Apple Watch 应用中的多个故障排除提示。_

此页列出使用 Xamarin 和这些问题的解决方案使用 watchOS 3 时可能发生的一些已知的问题。

## <a name="activities"></a>活动

对于活动共享正常工作，所有配对的 Apple 监视项目必须运行 watchOS 3。

已知问题：

- 有时答复活动共享通知失败。
- 答复活动共享一条消息通知可能会失败。
- 上方活动共享通知消息的上下文文本会不正确。

## <a name="apple-pay"></a>Apple Pay

已知问题：

- 如果不正确的到期日期或顺时针代码输入提供在 Apple Pay，新的付款服务时达到**下一步**正在运行的进程时将崩溃。
- Apple Pay 应用内购买需 PIN 数量可能会崩溃。

## <a name="auto-mac-unlock"></a>自动 Mac 解锁

通过使用 watchOS 3 beta 2 （或更高版本） 和 macOS Sierra beta 2 （或更高版本），如果用户的 iCloud 帐户启用双因素身份验证，它们可以使用其 Apple Watch 自动解锁其 mac。

## <a name="background-refresh"></a>后台刷新

违反系统资源将导致 watchOS 3 应用程序崩溃与以下异常代码：

- **0xc51bad01** -应用程序占用太多 CPU 时间。
- **0xc51bad02** -应用程序占用过多挂钟时间。
- **0xc51bad03** -应用程序没有足够的运行时，若要完成当前的任务。

## <a name="clock"></a>Clock

从新安装 Apple Watch 应用的复杂性可能显示为空白。 重新启动 Apple Watch 若要解决此问题。

## <a name="connectivity"></a>连接

已知问题：

- watchOS 不会提示用户输入 Apple Watch 上的受保护的用户数据的访问权限。 在 iPhone 应用中监视应用使用数据之前授予访问权限。
- Apple Watch 可以进入其中的所有 WatchConnectivity 传输都失败的状态重新启动 Apple Watch 若要解决问题。

## <a name="notifications"></a>通知

如果媒体附件太大，它将显示在用户的 iPhone 但不是其 Apple Watch 上。

## <a name="nsurlconnection"></a>NSURLConnection

任何`NSURLConnection`使用较旧的 TLS 协议的连接将失败。 对于所有 SSL/TLS 连接，RC4 对称密码现在默认处于禁用状态。 此外，安全传输 API 不再支持 SSLv3 和建议应用程序停止使用 sha-1 和 3DES 加密越早越好。

截至 watchOS 3，SSL/TLS 连接安全性是严格强制实施由 Apple。 受影响的服务和应用都应更新 web 服务器以使用最新的 TLS 协议版本。

## <a name="nsurlsession"></a>NSURLSession

截至 watchOS 3，`HTTPBodyStream`属性`NSMutableURLRequest`类必须设置为自以来的未打开流`NSURLConnection`和`NSURLSession`现在严格强制此要求。

## <a name="privacy"></a>隐私

已知问题：

使用时`https://`Url 这两个`NSURLSession`和`NSURLConnection`TLS 握手过程中不再支持 RC4 密码套件。 可能生成以下错误代码之一：

- **-1200年或-98** -对于`NSURLErrorSecurityConnectionFailed`和 SecureTransport 错误。
- **-1200 [3:-9824]** -失败的 http 负载。
- **-1200**  -  `NSURLConnection`已完成，但错误。

截至 watchOS 3，SSL/TLS 连接安全性是严格强制实施由 Apple。 受影响的服务和应用都应更新 web 服务器以使用最新的 TLS 协议版本。 请参阅[NSURLConnection](#NSURLConnection)上面有关详细信息。

## <a name="snapshots"></a>快照

未采用新的 WatchKit 应用`HandelBackgroundTask`watchOS 3 中，API 将不再接收定期更新。 

## <a name="watchkit"></a>WatchKit

当应用程序进入 watchOS 停靠在后台，则将暂停 SpriteKit 和 SceneKit 场景。

## <a name="related-links"></a>相关链接

- [watchOS 示例](https://developer.xamarin.com/samples/watchos/all/)
- [什么是 watchOS 3 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
