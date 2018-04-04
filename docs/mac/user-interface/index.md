---
title: macOS 用户界面
description: 此文章链接到指南描述各种 macOS UI 控件。
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/27/2018
ms.openlocfilehash: d40faa29f2fe278377bf4eae42a032f3dc9086ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="macos-user-interface"></a>macOS 用户界面

_此文章链接到指南描述各种 macOS UI 控件。_

在使用 C# 和.NET Xamarin.Mac 应用程序中，可以访问到同一个用户界面控件的开发人员*Objective C*和*Xcode*未。 因为 Xamarin.Mac 与 Xcode 直接集成，你可以使用 Xcode 的_接口生成器_来创建和维护你的用户界面 （或根据需要在 C# 代码中直接创建它们）。

下面列出的指南提供有关使用 macOS UI 中的元素 Xamarin.Mac 应用程序的详细的信息。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在每篇文章中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，因为它还说明了`Register`和`Export`用于网络-最多 C# 类 OBJECTIVE-C 的对象和 UI 元素的属性。

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

本文介绍如何使用 windows 和 Xamarin.Mac 应用程序中的面板。 它涉及创建和维护 windows 和 Xcode 和接口生成器中，加载 windows 中的面板和.storyboard 或.xib 文件中的面板、 使用 windows，以及对 C# 代码中的 windows 做出响应。

## <a name="dialogsmacuser-interfacedialogmd"></a>[对话框](~/mac/user-interface/dialog.md)

本文介绍如何使用对话框和 Xamarin.Mac 应用程序中的模式窗口。 它涵盖创建和维护模式窗口 Xcode 和接口生成器中，使用标准对话框，并显示和响应到 C# 代码中的窗口中。

## <a name="alertsmacuser-interfacealertmd"></a>[警报](~/mac/user-interface/alert.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的警报。 它涉及创建和显示 C# 代码中的警报以及对警报作出响应。

## <a name="menusmacuser-interfacemenumd"></a>[菜单](~/mac/user-interface/menu.md)

在 Mac 应用程序的用户界面; 各个部分中使用菜单从屏幕到弹出菜单和可以出现在窗口中的任意位置的上下文菜单顶部的应用程序的主菜单。 菜单是不可或缺的组成部分 Mac 应用程序的用户体验。 本文介绍如何使用 Cocoa Xamarin.Mac 应用程序中的菜单。

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[标准控件](~/mac/user-interface/standard-controls.md)

使用标准的 AppKit 控件，如按钮、 标签、 文本字段、 复选框和 Xamarin.Mac 应用程序中的分段的控件。 此指南介绍了如何将它们添加到 Xcode 的接口生成器中的用户界面设计、 将其公开到代码中通过插座和操作，以及使用 C# 代码中的 AppKit 控件。

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[工具栏](~/mac/user-interface/toolbar.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的工具栏。 它涵盖了创建和维护 Xcode 和接口生成器中的工具栏如何公开对代码使用插座和操作、 启用和禁用工具栏项和最后响应在 C# 代码中的工具栏项的工具栏项。

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[表视图](~/mac/user-interface/table-view.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的表视图。 它涵盖了创建和维护 Xcode 和接口生成器中的表视图如何公开表视图的项使用的容器和操作的代码，填充表视图，以及对 C# 代码中的表视图项的响应。

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[大纲视图](~/mac/user-interface/outline-view.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的大纲视图。 它涵盖了创建和维护 Xcode 和接口生成器中的大纲视图如何公开大纲视图中的项使用插座和操作的代码，填充大纲视图，以及响应概述查看 C# 代码中的项。

## <a name="source-listsmacuser-interfacesource-listmd"></a>[源列表](~/mac/user-interface/source-list.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的源列表。 它涵盖了创建和维护 Xcode 和接口生成器中的源列表如何公开到代码使用插座和操作、 填充源列表，以及对 C# 代码中的源列表项的响应的源列表项。

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[集合视图](~/mac/user-interface/collection-view.md)

本文介绍如何使用 Xamarin.Mac 应用程序中的集合视图。 它涵盖了创建和维护 Xcode 和接口生成器中的集合视图如何公开集合视图的项使用的容器和操作的代码，填充集合视图，以及对 C# 代码中的集合视图的响应。

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[创建自定义控件](~/mac/user-interface/custom-controls.md)

本文介绍如何创建自定义用户界面控件 (通过继承`NSControl`)、 绘制的控件，自定义接口和创建可与 Xcode 的接口生成器使用的自定义操作。

## <a name="mac-samples-gallery"></a>Mac 示例库

我们还建议先了解一下[Mac 示例库](https://developer.xamarin.com/samples/mac/all/)。 它包括大量可帮助你快速地获取起作用所 Xamarin.Mac 项目的已准备可用的代码。

## <a name="related-links"></a>相关的链接

- [macOS 人工界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
