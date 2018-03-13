---
title: "情节提要简介"
description: "本文提供与使用 Xamarin.Mac 应用中的情节提要的介绍。 其中介绍了如何使用 Storyboard 和 Xcode 的 Interface Builder 创建和维护应用 UI。"
ms.topic: article
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: ccee60b5d953987e858ef592d005cec9803b8b96
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="introduction-to-storyboards"></a>情节提要简介

_本文提供与使用 Xamarin.Mac 应用中的情节提要的介绍。它涵盖创建和维护应用程序的 UI 使用情节提要和 Xcode 的接口生成器。_

情节提要允许你开发用户界面的 Xamarin.Mac 应用不仅包括窗口定义和控件，但还包含不同的窗口之间的链接 (通过 segue) 和视图状态。

[![](images/intro01.png "示例在 Xcode 中的 UI")](images/intro01.png#lightbox)

本文将提供使用情节提要定义 Xamarin.Mac 应用的用户界面的说明。

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>情节提要有哪些？

通过使用情节提要，所有 Xamarin.Mac 应用程序的 UI 可以定义在其各个元素和用户界面之间导航的所有单一位置。 Xamarin.Mac 的情节提要中的情节提要非常类似的方式用于 Xamarin.iOS。 但是，它们包含一组不同的_Segue 类型_由于不同的接口惯例。

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>使用场景

如上面所述，情节提要定义所有分解为功能概述给定应用程序的 UI 其_查看控制器_。 在 Xcode 的接口生成器中，每个这些控制器居住在自己_场景_。

[![](images/intro02.png "示例视图控制器")](images/intro02.png#lightbox)

每个场景表示给定的视图和视图控制器对一组连接在 UI 中，因此显示它们之间的关系的每个场景的行 （称为 Segues）。 某些 Segues 定义如何一个视图控制器包含一个或多个子视图或视图控制器。 其他 Segues 定义 （如显示种 popover 或对话框中） 的视图控制器之间的转换。 

[![](images/intro03.png "示例 segue")](images/intro03.png#lightbox)

最重要需要注意的事项是每个 Segue 表示某种形式的应用程序的 UI 的给定元素之间的数据的流。

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>使用视图控制器

查看控制器定义给定的 Mac 应用中的信息的视图和数据模型，它提供该信息之间的关系。 情节提要中的每个顶级场景表示 Xamarin.Mac 应用代码中的一个视图控制器。

[![](images/intro04.png "示例落后视图控制器")](images/intro04.png#lightbox)

这种方式，每个视图控制器是自包含的可重用的配对的信息的可视表示形式 （视图） 和为控制该信息和呈现的逻辑。

可以在一个给定的场景中执行的所有操作将通常已处理的由人`.xib`文件： 

 - 位置 subviews 并控制 （如按钮和文本框）。
 - 定义元素的位置和自动布局约束。
 - 网络上卷的操作和 Outlet，若要公开到代码的 UI 元素。

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>使用 Segue

如上面所述，Segues 提供所有定义您的应用程序 UI 的场景之间的关系。 如果你熟悉在 iOS 中在情节提要中工作，你知道 Segue 的 iOS 通常定义全屏视图之间的转换。 这不同于 macOS，Segues 通常定义"包含"（其中一个场景是父级场景的子级） 时的不同而不同。

在 macOS，大多数应用程序往往组与其一起使用 UI 元素，例如拆分视图和选项卡的相同窗口中的视图。 与 iOS，其中视图需要打开和关闭屏幕转换，由于有限物理显示空间。

给定向包含 macOS 的趋势，在一些情况下其中_Segue 的演示文稿_使用，如模式窗口、 表视图和 Popovers。

当使用 Segue 的演示文稿，你可以重写`PrepareForSegue`演示以初始化和变量的父视图控制器方法并提供到要呈现的视图控制器的任何数据。

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>设计和运行时间

在设计时 (当在 Xcode 的接口生成器 ui 布局)，应用程序的 UI 的每个元素分解为构成项：

- **场景**-它是组成：
    - **查看控制器**-定义视图和支持它们的数据之间的关系。
    - **视图和子视图**-构成的用户界面的实际元素。
    - **包含 Segues** -定义之间场景的父-子关系。
- **演示文稿 Segues** -定义不同的演示文稿模式。 

通过以这种方式定义的每个元素，它允许每个元素延迟加载仅根据需要在运行时。 在 macOS，整个过程旨在使开发人员能够创建复杂、 灵活的支持代码，以使它们运行，裸机至少需要的用户界面同时尽可能正在为有效地利用系统资源。

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>情节提要快速入门

在[情节提要快速启动](~/mac/platform/storyboards/quickstart.md)指南中，我们将创建的简单 Xamarin.Mac 应用程序引入了使用情节提要创建用户界面的关键概念。 示例应用程序将包含的分割视图包含_内容区域_和_检查器区域_并它会显示一个简单的首选项对话框窗口。 我们将使用 Segues 将所有的用户界面元素绑定在一起。

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>使用情节提要

本部分介绍的深入详细信息[使用情节提要](~/mac/platform/storyboards/indepth.md)Xamarin.Mac 应用中。 我们需要深入探讨了场景以及它们如何查看控制器和视图组成。 然后，我们将看看如何与 Segues 一起绑定场景。 最后，我们将看看使用自定义 Segue 类型。 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>复杂的情节提要示例

有关使用情节提要 Xamarin.Mac 应用中的复杂的示例的示例，请参阅[SourceWriter 示例应用程序](https://developer.xamarin.com/samples/mac/SourceWriter/)。 SourceWriter 是一个非常简单的源代码编辑器，提供代码补全和简单语法突出显示支持。

SourceWriter 代码已经完全注释，且在可用时，提供了相关链接，链接涵盖了从关键技术或方法到 Xamarin.Mac 指南文档中的相关信息。

<a name="Summary" />

## <a name="summary"></a>摘要

本文已采取了快速了解一下 Xamarin.Mac 应用中使用情节提要。 我们已了解如何创建使用情节提要的新应用程序以及如何定义用户界面。 我们还已了解如何在不同的窗口之间导航和 segue 的使用的视图状态。


## <a name="related-links"></a>相关链接

- [Hello，Mac（示例）](https://developer.xamarin.com/samples/mac/Hello_Mac/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
