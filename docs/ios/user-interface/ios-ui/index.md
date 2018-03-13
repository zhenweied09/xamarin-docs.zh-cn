---
title: "在 iOS 中的用户界面"
description: "介绍如何使用在 Xamarin.iOS 应用程序中的 iOS 用户界面。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: f456b54180d50cfc4b6b98ed8f3d4118c8397b37
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="user-interface-in-ios"></a>在 iOS 中的用户界面

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[外观 API](introduction-to-the-appearance-api.md)

iOS 允许用户界面控件，为主题使用 UIAppearance Api 的许多可视的特性。

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[创建用户界面对象](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Apple 组相关功能集成到等同于 Xamarin.iOS 命名空间的"框架"的部分。 `UIKit` 是包含适用于 iOS 的所有用户界面控件的命名空间。

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[布局选项](~/ios/user-interface/ios-ui/layout-options.md)

有两种不同机制，用于控制布局视图进行调整大小或旋转时： 自动调整大小和自动布局。

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[提供 Haptic 反馈](~/ios/user-interface/ios-ui/haptic-feedback.md)

本文介绍如何在 iOS 10 和如何在 Xamarin.iOS 中实现它们中可用的 haptic 反馈的新类型。

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[使用 UI 线程](~/ios/user-interface/ios-ui/ui-thread.md)

你的代码仅应更改用户界面控件从主要 （或 UI） 线程。 在不同线程 （如回调或后台线程） 发生的任何 UI 更新不可能获取其呈现到屏幕，或甚至可能导致崩溃。




