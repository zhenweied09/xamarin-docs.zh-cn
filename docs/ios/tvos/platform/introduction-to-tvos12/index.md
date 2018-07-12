---
title: TvOS 12 简介
description: 本文档提供了当前提供的 C# 绑定高级别中的哪些 Xamarin 的预览版本的 tvOS 12 的新增和更新功能的概述。
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 03841306ba54e511dbf2f2b86a7c17e9f4669bcd
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38847556"
---
# <a name="introduction-to-tvos-12"></a>TvOS 12 简介

![预览](~/media/shared/preview.png)

> [!WARNING]
> Xamarin 的 tvOS 12 支持目前处于预览状态，它可能包含 bug，这意味着不是功能完备，并可能会更改。 将它用于仅试验。

> [!NOTE]
> - 审阅[入门](~/ios/platform/introduction-to-ios12/get-started.md)指南说明如何开始构建 iOS 12 和 tvOS 12 应用使用 Xamarin。
> - 有关详细信息，请阅读[发行说明](https://releases.xamarin.com/preview-release-xcode-10-beta/)Xamarin 的预览版本。

本文档提供的新的和更新 tvOS 概述 12 的 Xamarin 预览版版本目前提供的 C# 绑定的功能。

## <a name="password-autofill"></a>密码自动填充

使用 tvOS 12，用户可以使用其 iOS 设备中登录到 tvOS 应用一次点击。 启用此选项通过组合使用`UITextContentType`使用情况，以指定用户名和密码字段，关联的域来建立 iOS 应用程序和 tvOS 应用和首选的焦点环境，可以选择要在用户接收焦点的项之间的关系提供用户名和密码。

## <a name="focus-engine-enhancements"></a>焦点引擎增强功能

tvOS 12 允许所有应用程序，无论它们的呈现方式，与焦点引擎进行交互。 通过使用 Siri 远程用户的交互，焦点引擎可与任何应用程序选择一个项、 在可能的焦点发生变动，提示和自然地更新焦点。 这通过 UIKit 的自定义应用程序中启用`IUIFocusItemContainer`接口，`UIFocusMovementHint`类，`IUIFocusItemScrollableContainer`接口，其他相关的类和方法。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS – Apple 开发人员 (Apple)](https://developer.apple.com/tvos/)
- [什么是 tvOS 12 (Apple) （视频） 中的新增功能](https://developer.apple.com/videos/play/wwdc2018/208/)
- [电视 (Apple)](https://www.apple.com/tv/)
- Xamarin 预览版[发行说明](https://releases.xamarin.com/preview-release-xcode-10-beta/)
