---
title: Xamarin Live Player 的限制
description: 本文档介绍了 Xamarin Live Player 的限制。 它讨论了设备要求，该功能适用于项目类型和其他杂项主题。
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
author: topgenorth
ms.author: toopge
ms.date: 03/29/2018
ms.openlocfilehash: ea71391382f9e1ecb80cbf5f2d5bf127e0d6d1be
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815277"
---
# <a name="limitations-of-xamarin-live-player"></a>Xamarin Live Player 的限制

![预览功能](~/media/shared/preview.png)

## <a name="device-requirements"></a>设备要求
Xamarin Live Player 应用支持以下设备：

# <a name="androidtabandroid"></a>[Android](#tab/android)

- Android 4.2 或更高版本。
- ARM-v7a，ARM-v8a、 ARM64-v8a，x86，或 x86_64 处理器。

# <a name="iostabios"></a>[iOS](#tab/ios)

- iOS 9.0 或更高版本。
- ARM64 处理器。

-----

## <a name="limitations"></a>限制

有一些限制可以运行 Xamarin Live Player，包括以下各项的事情上：

### <a name="xamarinforms"></a>Xamarin.Forms

- 不支持自定义呈现器。
- 不支持的效果。
- 不支持使用自定义的可绑定属性的自定义控件。
- 不支持嵌入的资源 (即。 在 PCL 中嵌入图像或其他资源)。
- 第三方 MVVM 框架不受支持 （即。Prism、 Mvvm 跨，Mvvm Light，等等）。
- 不支持在 iOS 上的资产目录。

### <a name="other-project-types"></a>其他项目类型

- 实时播放器不适合本机 Android 或 iOS 项目 （即使用用户界面的 Android XML 或情节提要）。

### <a name="misc"></a>杂项

- 有限的反射的支持 （目前会影响某些常用的 Nuget，SQLite 和 Json.NET 等）。 其他 Nuget 仍然受支持。
- 不能重写某些系统类 （例如，您不能实现子类）。
- 需要预配一些平台功能不能适用于 Xamarin Live Player 应用 （但是它已配置为像照片库访问的常见操作）。
- 忽略自定义目标和生成步骤。 例如，不能合并 Fody、 refit 时需、 AutoFac 和 AutoMapper 等工具。
- 在 Android 上不支持 F # 项目，有限的支持在 iOS 上
- 可能不支持使用自定义的泛型类和接口的高级的方案。

请在报告的任何其他问题[bugzilla](https://aka.ms/live-player-report-issue)。

## <a name="related-links"></a>相关链接

- [疑难解答](~/tools/live-player/troubleshooting.md)
- [安装](~/tools/live-player/install.md)
