---
title: 使用 iOS 设计器构建用户界面
description: 本文档介绍如何使用 Xamarin 设计器中为 iOS 生成应用的用户界面与情节提要和.xib 文件。 它链接到文档的讨论工具的可用性、 其基本功能，可设计控件，并提供其用法的演练。
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/31/2018
ms.openlocfilehash: a931373a6abba3084af3c7aefcdddc903ad1b577
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209227"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>使用 iOS 设计器构建用户界面

_Xamarin 设计器中为 iOS 是可视化设计器中的适用于 iOS 情节提要和接口生成器格式适用于 Mac 和 Visual Studio 与 Visual Studio 完全集成。IOS 设计器维护的情节提要和.xib 格式中，与完全兼容，以便可以在适用于 Mac 的 Visual Studio 或 Visual Studio 除了 Xcode 的接口生成器中编辑文件。此外，适用于 iOS 的 Xamarin 设计器支持高级的功能，如在编辑器中的设计时呈现的自定义控件。_

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![iOS 适用于 Mac 的 Visual Studio 中的设计器](images/designer-vsmac-sml.png "iOS 设计器")](images/designer-vsmac.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![iOS 在 Visual Studio 中的设计器](images/designer-vs.png "iOS 设计器")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>可用性

Xamarin 设计器中为 iOS 是适用于 Mac 的 Visual Studio 中，在 Windows 上的 Visual Studio 2017 可用。

这些指南假定你熟悉中介绍的内容[Xamarin.iOS 入门指导](~/ios/get-started/index.md)。

## <a name="ios-designer-basicsintroductionmd"></a>[iOS 设计器基本知识](introduction.md)

本指南涵盖 Xamarin iOS 设计器的功能。 它介绍了设计器的基础知识，显示如何使用设计器控件进行布局以可视方式以及如何编辑属性。

## <a name="designable-controls-overviewios-designable-controls-overviewmd"></a>[可设计控件概述](ios-designable-controls-overview.md)

本指南查找深入地自定义控件，讨论如何创建和哪些要求必须满足设计图面上呈现。 此外，它演示如何调试使用可设计控件时可能发生的常见问题。

## <a name="walkthrough---using-custom-controls-with-ios-designerios-designable-controls-walkthroughmd"></a>[演练-与 iOS 设计器一起使用的自定义控件](ios-designable-controls-walkthrough.md)

本文提供了演示如何创建自定义控件和在 iOS 设计器中使用它的分步演练。 它演示如何使控件在设计器的工具箱中可用，因此它可以拖动/放置到一个视图。 此外，它演示如何实现控件，因此它正确呈现在设计时和运行时，以及如何创建可以在设计时设置的属性。

## <a name="auto-layout-with-the-xamarin-ios-designerdesigner-auto-layoutmd"></a>[使用 Xamarin iOS 设计器的自动数据布局](designer-auto-layout.md)

本指南介绍 iOS 自动布局和新约束工作流 iOS 设计器中可用。
