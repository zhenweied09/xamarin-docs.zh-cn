---
title: "疑难解答"
description: "本文提供使用 tvOS 10 Xamarin.tvOS 应用中的多个故障排除提示。"
ms.topic: article
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d45b9ed04d3ae4815d408d82068e588a2cbcc6f8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshooting"></a>疑难解答

_本文提供使用 tvOS 10 Xamarin.tvOS 应用中的多个故障排除提示。_

以下部分列出使用 tvOS 10 Xamarin.tvOS 与这些问题的解决方案时可能发生的一些已知的问题：

- [App Store](#App-Store)
- [二进制兼容性](#Binary-Compatibility)
- [CFNetwork HTTP Protocol](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [CoreImage](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>应用商店

已知问题：

 - 时沙盒环境中测试应用内购买，身份验证对话框中可能出现两次。
 - 当使用沙盒环境中承载的内容测试应用内购买，每次应用程序置于前台，直到内容下载完成时，将出现密码对话框。

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>二进制兼容性

已知问题：

 - 调用`NSObject.ValueForKey`将`null`密钥将导致异常。
 - 在调用时，按名称引用字体`UIFont.WithName`将导致崩溃。
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

`CIImageProcessor` API 现在支持任意输入的图像计数。 `CIImageProcessor` 将删除 tvOS 10 beta 1 中所包含的 API。

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

提交操作后，`UserInfo`属性`NSUserActivity`对象可能为空。 显式调用`BecomeCurrent`NSUserActivity 作为当前的解决方法的对象。

<a name="UIKit" />

## <a name="uikit"></a>UIKit

已知问题：

 - 更改为的背景外观`UINavigationBar`，`UITabBar`或`UIToolBar`可能会导致布局处理过程中，若要解决的新外观。 尝试修改的内部这些外观`LayoutSubviews`， `UpdateConstraints`，`WillLayoutSubviews`或`DidUpdateSubviews`事件可能会导致无限布局循环。
 - 在调用 tvOS 10，`RemoveGestureRecognizer`方法`UIView`对象显式取消任何正在进行中笔势识别器。
 - 提供的视图控制器现在可能会影响状态栏的外观。
 - tvOS 10 要求开发人员调用`base.AwakeFromNib`时子类化`UIViewController`和重写`AwakeFromNib`方法。
 - 使用自定义应用`UIView`的子类，可重写`LayoutSubviews`脏页之前调用的布局和`base.LayoutSubviews`可能会触发 tvOS 10 中的无限布局循环。
 - 方向特定或 flippable 映像资产都是翻转时分配给`UIButton`对象。





## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [什么是 tvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
