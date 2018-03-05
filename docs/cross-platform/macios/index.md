---
title: "iOS 和 Mac"
description: "在本部分中，我们介绍了策略来共享你的 Xamarin.iOS 和 Xamarin.Mac 项目之间的代码。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: a5711b5ac8332ac518ba5b639f05de557229af6e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="ios-and-mac"></a>iOS 和 Mac

_在本部分中，我们介绍了策略来共享你的 Xamarin.iOS 和 Xamarin.Mac 项目之间的代码。_

## <a name="code-sharing"></a>代码共享

具有共享的最好办法的没有用户界面元素的元素的代码的 iOS 和 Mac 之间的代码是仍使用[可移植类库](~/cross-platform/app-fundamentals/pcl.md)。

必须执行一些用户界面工作的代码并且尚未，你想要共享，则应使用[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)使你能够将共享的单个项目中并让它使用 Mac 和 iOS 时引用编译的代码。

##  <a name="unified-apiunifiedindexmd"></a>[Unified API](unified/index.md)

对于 iOS 和 Mac 项目统一 API 框架中使用相同的命名空间，因此，同一个代码文件可用于在两个平台之间无缝的代码共享。 它还使 32 和 64 位版本。 统一 API 早期 2015年以来已模板默认和建议对于所有新项目-*仅*统一 API 项目可以提交到应用商店。

### <a name="classic-apis"></a>经典 Api

> [!NOTE]
> **经典的配置文件弃用：** Xamarin.iOS 中添加新的平台时我们开始逐渐否决从经典的配置文件 (monotouch.dll) 的功能。 例如，已删除非 NRC （新 ref 计数） 选项。 NRC 始终已为所有统一应用程序启用 （即非 NRC 永远不会是一个选项），并且有任何已知的问题。 将来的版本将删除作为垃圾回收器使用 Boehm 的选项。 这也是永远不会对统一应用程序可用的选项。 用于回退 2016 Xamarin.iOS 10.0 的发布计划的经典支持完全删除。

原始 （非统一） Xamarin.iOS 和 Xamarin.Mac Api 做出代码共享的难度本机框架有其中一个`MonoTouch.`或`MonoMac.`命名空间前缀。  我们提供一些空命名空间，使开发人员通过添加来共享代码`using`引用上同一文件中，但这样的 MonoMac 和 MonoTouch 命名空间的语句已少量极差。 经典 API 应仅将继续使用旧的应用中的内部分布式 （升级到统一 API 建议）。


### <a name="updating-from-classic-to-the-unified-api"></a>从经典更新到统一的 API

没有更新任何应用程序从经典到统一 API 的详细的说明。

## <a name="binding-objective-c-librariesbindingindexmd"></a>[绑定 Objective-C 库](binding/index.md)

Xamarin 让你通过绑定到你的应用使本机库。 本部分介绍：

- 绑定的工作原理
- 如何手动生成，你可以将 Objective C 代码引入 Xamarin，绑定项目和
- 如何使用我们**目标 Sharpie**工具以帮助自动执行的过程。

## <a name="native-referencesnative-referencesmd"></a>[本机引用](native-references.md)



##  <a name="macios-native-typesnativetypesmd"></a>[Mac/iOS 本机类型](nativetypes.md)

为了支持 32 和 64 位代码以透明方式从 C# 和 F #，我们引入了新的数据类型。   在此处了解有关它们的信息。

##  <a name="building-32-and-64-bit-apps32-and-64md"></a>[生成 32 位和 64 位应用程序](32-and-64.md)

您需要知道要支持 32 和 64 位应用程序。

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[使用跨平台应用中的本机类型](native-types-cross-platform.md)

本文介绍如何使用新的 iOS 统一 API 本机类型 (`nint`， `nuint`， `nfloat`) 在跨平台应用程序代码与非 iOS 设备，例如 Android 或 Windows Phone 操作系统的共享位置。
它可让你应在何时使用本机类型的了解，并提供到新的类型必须用于跨平台代码的情况下的几个可能的解决方案。


## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[HttpClient 堆栈和 SSL/TLS 实现选择器](http-stack.md)

新的 HttpClient 堆栈选择器控制要在 Xamarin.iOS、 Xamarin.tvOS 和 Xamarin.Mac 应用中使用哪些 HttpClient 实现。 你可以现在或切换到使用 iOS 的实现，tvOS 的 OS X 本机传输 (`NSUrlSession`或`CFNetwork`具体取决于操作系统)。

SSL （安全套接字层） 和及其后继，TLS （传输层安全性），提供对 HTTP 和通过其他网络连接支持`System.Net.Security.SslStream`。 新的 SSL/TLS 实现生成选项 Mono 自己 TLS 堆栈和由 Apple 的 TLS 堆栈存在于 Mac 和 iOS 提供支持的一个之间切换。
