---
title: "用户界面"
description: "此文章链接到指南描述各种 macOS UI 控件。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/19/2016
ms.openlocfilehash: a8cb9488849dafc2cd720ecf59d654009a9ad781
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="user-interface"></a>用户界面

_此文章链接到指南描述各种 macOS UI 控件。_

在使用 C# 和.NET Xamarin.Mac 应用程序中，你有权访问相同用户界面控件，开发人员使用*Objective C*和*Xcode*未。 因为 Xamarin.Mac 与 Xcode 直接集成，你可以使用 Xcode 的_接口生成器_来创建和维护你的用户界面 （或根据需要在 C# 代码中直接创建它们）。 

下面列出的指南提供有关使用 macOS UI 中的元素 Xamarin.Mac 应用程序的详细的信息。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在每篇文章中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于网络-最多 C# 类 OBJECTIVE-C 的对象和 UI 元素。

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

本文介绍如何使用 Windows 和面板 Xamarin.Mac 应用程序中。 它涵盖了创建和维护 Windows 和面板在 Xcode 和接口生成器中，加载 Windows 和从面板`.storyboard`或`.xib`文件，使用 Windows 和 C# 代码中对 Windows 中做出响应。

## <a name="dialogsmacuser-interfacedialogmd"></a>[对话框](~/mac/user-interface/dialog.md)

本文介绍如何在 Xamarin.Mac 应用程序中的使用对话框和模式窗口。 其中包含如何在 Xcode 和 Interface Builder 中创建和维护模式窗口、使用标准对话框、显示以及响应 C# 代码中的窗口。

## <a name="alertsmacuser-interfacealertmd"></a>[警报](~/mac/user-interface/alert.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的警报。 其中包括通过 C# 代码创建和显示警告以及响应警告。

## <a name="menusmacuser-interfacemenumd"></a>[菜单](~/mac/user-interface/menu.md)

在 Mac 应用程序的用户界面; 各个部分中使用菜单从屏幕到可以出现在窗口中的任意位置的弹出和上下文菜单顶部的应用程序的主菜单。 菜单是构成 Mac 应用程序用户体验的一个组成部分。 本文介绍如何在 Xamarin.Mac 应用程序中使用 Cocoa 菜单。

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[标准控件](~/mac/user-interface/standard-controls.md)

使用如按钮、 标签、 文本字段、 复选框和分段控件 Xamarin.Mac 应用程序中的标准 AppKit 控件。 此指南介绍了如何将它们添加到 Xcode 的接口生成器中用户界面设计、 将其公开到通过插座和操作的代码以及使用 C# 代码中的 AppKit 控件。

 
## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[工具栏](~/mac/user-interface/toolbar.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的工具栏。 其中包含如何在 Xcode 和 Interface Builder 中创建和维护工具栏、如何使用输出口和操作对代码公开工具栏项、如何启用和禁用工具栏项以及响应 C# 代码中的工具栏项。

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[表视图](~/mac/user-interface/table-view.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的表视图。 它涵盖了创建和维护在 Xcode 和接口生成器中，表视图如何公开对代码使用插座和操作、 填充表视图和最后响应在 C# 代码中的表视图项的表视图项。

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[大纲视图](~/mac/user-interface/outline-view.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的大纲视图。 其中包含如何在 Xcode 和 Interface Builder 中创建和维护大纲视图、如何使用输出口和操作对代码公开大纲视图项、填充大纲项以及响应 C# 代码中的大纲视图项。

## <a name="source-listsmacuser-interfacesource-listmd"></a>[源列表](~/mac/user-interface/source-list.md)

本文介绍如何在 Xamarin.Mac 应用程序源列出的使用。 其中包含如何在 Xcode 和 Interface Builder 中创建和维护源列表、如何使用输出口和操作对代码公开源列表项、如何填充源列表项以及响应 C# 代码中的源列表项。

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[集合视图](~/mac/user-interface/collection-view.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的集合视图。 其中包含如何在 Xcode 和 Interface Builder 中创建和维护集合视图、如何使用操作对代码公开集合视图元素、如何填充集合视图以及响应 C# 代码中的集合视图。

## <a name="creating-custom-user-controlsmacuser-interfacecustom-controlsmd"></a>[创建自定义用户控件](~/mac/user-interface/custom-controls.md)

本文介绍如何创建自定义用户界面控件 (通过继承`NSControl`)、 绘制控件的自定义接口和创建可与 Xcode 的接口生成器使用的自定义操作。

## <a name="mac-samples-gallery"></a>Mac 示例库

我们还建议先了解一下[Mac 示例库](http://developer.xamarin.com/samples/mac/all/)，它包括大量可帮助你快速地获取起作用所 Xamarin.Mac 项目的已准备可用的代码。

## <a name="related-links"></a>相关链接

- [macOS 人工界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
