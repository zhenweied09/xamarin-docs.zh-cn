---
title: 更新现有应用到统一的 API
description: 此文档链接到各种指南描述如何更新到统一 API 的 Xamarin 应用程序。 它讨论 Xamarin.iOS 应用程序，Xamarin.Mac 应用。 Xamarin.Forms 应用，在跨平台应用和绑定项目中的本机类型。
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 2d09be7b85980e5c5a8eb209dc1b4ff3136c34b3
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781626"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>更新现有应用到统一的 API

> [!IMPORTANT]
> Xamarin 经典 API 进行，以统一的 API，已弃用。 
> - Xamarin.iOS 支持经典 API (monotouch.dll) 的最后一个版本是 Xamarin.iOS 9.10。
> - Xamarin.Mac 仍支持经典 API，但它将不再更新。 因为它已被弃用，开发人员应将移动到统一 API 其应用程序。

## <a name="how-to-update-your-apps"></a>如何更新你的应用程序

Xamarin 大学免费提供的视频对**升级到 iOS 统一 API**。 请访问[Xamarin 大学闪电讲座](http://university.xamarin.com/lightninglectures)要监视 ！

[ ![](updating-apps-images/xamu-video-sml.png "Xamarin 大学")](http://university.xamarin.com/lightninglectures)

有三个步骤以更新你的应用程序：

1. 修复任何编译器警告中的现有代码，特别是那些与弃用的 Api。

2. 使用内置于 Visual Studio for Mac 迁移工具将更新你的项目文件和命名空间。

3. 修复剩余相关到新的编译器错误[64 类型](~/cross-platform/macios/nativetypes.md)和[其他 Api](~/cross-platform/macios/unified/overview.md#deprecated-typos)更改的。 签出[这些提示](~/cross-platform/macios/unified/updating-tips.md)有关其他信息可能是必需的手动更新。

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
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
