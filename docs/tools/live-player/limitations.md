---
title: 限制
description: Xamarin 实时播放器的一些限制
ms.topic: article
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 08/04/2017
ms.openlocfilehash: 71d37b79c962123bb5adf6f999e713f68086166c
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="limitations"></a>限制

![预览功能](~/media/shared/preview.png)

## <a name="device-requirements"></a>设备要求
Xamarin 实时播放器应用程序支持以下设备：

# <a name="androidtabandroid"></a>[Android](#tab/android)

- Android 4.2 或更高版本。
- ARM-v7a，ARM v8a、 ARM64 v8a，x86 或 x86_64 处理器。

# <a name="iostabios"></a>[iOS](#tab/ios)

- iOS 9.0 或更高版本。
- ARM64 处理器。

-----

## <a name="limitations"></a>限制

有一些限制可以运行实时的 Xamarin Player，包括以下各项的事情上：

### <a name="xamarinforms"></a>Xamarin.Forms
- 不支持自定义呈现器。
- 不支持效果。
- 不支持使用自定义可绑定属性的自定义控件。
- 不支持嵌入的资源 (即。 在 PCL 中嵌入图像或其他资源)。
- 第三方 MVVM 框架不支持 (ie。Prism、 Mvvm 跨、 Mvvm Light，等等。）。
- 不支持在 iOS 上的资产目录。

### <a name="other-project-types"></a>其他项目类型
- 实时播放器不用于本机 Android 或 iOS 项目 （Android XML 或情节提要用于用户界面）。

### <a name="misc"></a>杂项
- 反射对有限的支持 （当前影响某些常用的 NuGets，SQLite 和 Json.NET 等）。 其他 NuGets 可能仍受支持。
- 某些系统类不能重写 （例如，不能实现子类）。
- （但是它已配置为常见的操作，如照片库 access） 的实时的 Xamarin Player 应用中不能处理需要设置某些平台功能。
- 忽略自定义的目标和生成步骤。 例如，工具如 Fody、 Retit、 AutoFac，和 AutoMapper 不能合并。
- 在 Android 上不支持 F # 项目并在 iOS 上的支持有限
- 可能不支持使用自定义的泛型类和接口的高级的方案。

任何其他问题请报告上[bugzilla](https://aka.ms/live-player-report-issue)。


## <a name="related-links"></a>相关链接

- [疑难解答](~/tools/live-player/troubleshooting.md)
- [安装](~/tools/live-player/install.md)
