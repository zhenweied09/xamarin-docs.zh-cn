---
title: "限制"
description: "Xamarin 实时播放器的一些限制"
ms.topic: article
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 08/04/2017
ms.openlocfilehash: d551c0a82fb8f970ce5a00ec9e64ac7f49c81a44
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="limitations"></a>限制

![预览功能](~/media/shared/preview.png)

## <a name="device-requirements"></a>设备要求
Xamarin 实时播放器应用程序支持以下设备：

### <a name="ios"></a>iOS
- iOS 9.0 或更高版本。
- ARM64 处理器。
- 检查[应用商店](https://itunes.apple.com/us/app/xamarin-live-player/id1228841832?mt=8)有关受支持的设备的列表。

### <a name="android"></a>Android
- Android 4.2 或更高版本。
- ARM-v7a，ARM v8a、 ARM64 v8a，x86 或 x86_64 处理器。


## <a name="limitations"></a>限制

有一些限制可以运行实时的 Xamarin Player，包括以下各项的事情上：

### <a name="android"></a>Android
- 当前不支持 android 用户界面设计为具有 AXML 文件。

### <a name="ios"></a>iOS
- 不支持某些 iOS 情节提要功能。
- 不支持 iOS XIB 文件。

### <a name="xamarinforms"></a>Xamarin.Forms
- 不支持自定义呈现器。
- 不支持效果。
- 不支持使用自定义可绑定属性的自定义控件。
- 不支持嵌入的资源 (即。 在 PCL 中嵌入图像或其他资源)。

### <a name="misc"></a>杂项
- 反射对有限的支持 （当前影响某些常用的 NuGets，SQLite 和 Json.NET 等）。 其他 NuGets 仍受支持。
- 某些系统类不能重写 （例如，不能实现子类）。
- （但是它已配置为常见的操作，如照片库 access） 的实时的 Xamarin Player 应用中不能处理需要设置某些平台功能。
- 忽略自定义的目标和生成步骤。 例如，不能合并 Fody 之类的工具。

> [!WARNING]
> **注意：**实时的 Xamarin Player 并不适用于 Xamarin Studio

任何其他问题请报告上[bugzilla](https://aka.ms/live-player-report-issue)。


## <a name="related-links"></a>相关链接

- [疑难解答](~/tools/live-player/troubleshooting.md)
- [安装](~/tools/live-player/install.md)
