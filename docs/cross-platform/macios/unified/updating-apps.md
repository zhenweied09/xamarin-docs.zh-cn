---
title: "更新现有应用到统一的 API"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: b1b6338494b9be98e677cf9d338410eae759feb8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="updating-existing-apps-to-the-unified-api"></a>更新现有应用到统一的 API

> [!IMPORTANT]
> **经典的配置文件弃用：** Xamarin.iOS 中添加新的平台时我们开始逐渐否决从经典的配置文件 (monotouch.dll) 的功能。 例如，已删除非 NRC （新 ref 计数） 选项。 NRC 始终已为所有统一应用程序启用 （即非 NRC 永远不会是一个选项），并且有任何已知的问题。 将来的版本将删除作为垃圾回收器使用 Boehm 的选项。 这也是永远不会对统一应用程序可用的选项。 完全删除的经典支持用于下一步回退 Xamarin.iOS 10.0 的发布计划。




## <a name="how-to-update-your-apps"></a>如何更新你的应用程序

Xamarin 大学免费提供的视频对**升级到 iOS 统一 API**。 请访问[Xamarin 大学闪电讲座](http://university.xamarin.com/lightninglectures)要监视 ！

[ ![](updating-apps-images/xamu-video-sml.png "Xamarin 大学")](http://university.xamarin.com/lightninglectures)

有三个步骤以更新你的应用程序：

1. 修复任何编译器警告中的现有代码，特别是那些与弃用的 Api。

2. 使用内置于 Visual Studio for Mac 迁移工具将更新你的项目文件和命名空间。

3. 修复剩余相关到新的编译器错误[64 类型](~/cross-platform/macios/nativetypes.md)和[其他 Api](~/cross-platform/macios/unified/index.md#deprecated-typos)更改的。 签出[这些提示](~/cross-platform/macios/unified/updating-tips.md)有关其他信息可能是必需的手动更新。

有可用于每个产品，可帮助你更新你的应用到的统一 API 和 64 位支持的特定参考线：

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Xamarin.iOS 应用程序](~/cross-platform/macios/unified/updating-ios-apps.md)

可以将现有的 Xamarin.iOS 应用程序更新为使用 for mac。 内置于 Visual Studio 的自动化的迁移工具统一的 API 一些其他修复然后可能是必需的中, 所述[这些说明](~/cross-platform/macios/unified/updating-ios-apps.md)和[提示](~/cross-platform/macios/unified/updating-tips.md)。

###  <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Xamarin.Mac 应用](~/cross-platform/macios/unified/updating-mac-apps.md)

可以将现有 Xamarin.Mac 应用更新为使用自动的迁移工具 for mac。 内置于 Visual Studio 统一的 API 一些其他修复然后可能是必需的中, 所述[这些说明](~/cross-platform/macios/unified/updating-mac-apps.md)和[提示](~/cross-platform/macios/unified/updating-tips.md)。

###  <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Xamarin.Forms 应用](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

按照这些说明来更新现有 Xamarin.Forms 解决方案与使用统一的 API 将 iOS 项目。 统一的 API 支持仅位于 Xamarin.Forms 1.3 和更高版本，因此[说明](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)还介绍如何更新到版本 1.3 Xamarin.Forms 应用程序。 这些[提示](~/cross-platform/macios/unified/updating-tips.md)可能帮助更新自定义呈现器或依赖关系服务中的任何本机 iOS 代码。

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[使用跨平台应用中的本机类型](~/cross-platform/macios/nativetypes.md)

本文介绍如何使用新的 iOS 统一 API 本机类型 （nint、 nuint、 nfloat） 在跨平台应用程序代码与非 iOS 设备，例如 Android 或 Windows Phone 操作系统的共享位置。 它可让你应在何时使用本机类型的了解，并提供到新的类型必须用于跨平台代码的情况下的几个可能的解决方案。

## <a name="update-bindings-to-the-unified-api"></a>更新绑定到统一的 API

已创建绑定到 Objective C 库的客户将需要更新绑定项目以反映基础 API （其中有些类型现在将是 64 位） 中的更改。
按照这些说明[更新现有绑定项目以支持统一 API](~/cross-platform/macios/unified/update-binding.md)。




## <a name="related-links"></a>相关链接

- [更新 iOS 应用程序](~/cross-platform/macios/unified/updating-ios-apps.md)
- [更新 Mac 应用程序](~/cross-platform/macios/unified/updating-mac-apps.md)
- [更新 Xamarin.Forms 应用](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新绑定](~/cross-platform/macios/unified/update-binding.md)
- [更新提示](~/cross-platform/macios/unified/updating-tips.md)
- [经典 vs 统一的 API 差异](http://developer.xamarin.comhttps://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
