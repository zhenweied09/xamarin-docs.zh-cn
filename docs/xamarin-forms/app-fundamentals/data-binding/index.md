---
title: Xamarin.Forms 数据绑定
description: 数据绑定是链接的两个对象的属性，以便在一个属性的更改会自动反映在另一个属性的技术。 数据绑定是模型-视图-视图模型 (MVVM) 应用程序体系结构的组成部分。
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: a5ea5dcb5b108da52634f131fd36a91ba82f7da4
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240348"
---
# <a name="xamarinforms-data-binding"></a>Xamarin.Forms 数据绑定

_数据绑定是链接的两个对象的属性，以便在一个属性的更改会自动反映在另一个属性的技术。数据绑定是模型-视图-视图模型 (MVVM) 应用程序体系结构的组成部分。_

## <a name="the-data-linking-problem"></a>数据链接问题

Xamarin.Forms 应用程序包含一个或多个页，其中每个通常包含多个用户界面对象调用*视图*。 程序的主要任务之一是来保持同步，这些视图，并能够跟踪不同的值或它们表示的选择。 视图通常从基础数据源，表示值和用户操作这些视图以更改该数据。 当视图发生更改时，基础数据必须反映该更改，并且与此类似，当基础数据更改时，所做的更改必须反映在视图中。

若要成功处理此作业，该程序必须接收通知这些视图或基础数据中的更改。 常见的解决方案是定义发生更改时发出信号的事件。 事件处理程序可以然后安装这些更改的通知。 它通过将数据从一个对象转移到另一个进行响应。 但是，当存在许多视图时，还必须许多事件处理程序，并且涉及大量的代码获取。

## <a name="the-data-binding-solution"></a>数据绑定解决方案

数据绑定自动执行此作业，并呈现不必要的事件处理程序。 （事件均仍有必要，但是，因为数据绑定基础结构使用它们。）在代码中或在 XAML 中，可以实现数据绑定，但它们可以在 XAML 中它们有助于减少代码隐藏文件的大小得更加常见。 通过将事件处理程序中的程序代码替换为声明性代码或标记，应用程序简化，并且阐明了。

数据绑定中涉及的两个对象之一几乎始终是派生自元素`View`和窗体页的可视界面的一部分。 另一个对象是：

- 另一个`View`派生，通常在同一页上。
- 一个代码文件中的对象。

在如演示程序[ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)示例，两个之间的数据绑定`View`衍生产品通常为了清楚起见和简易性显示。 但是，相同的原则可应用于之间的数据绑定`View`和其他对象。 当使用模型-视图-视图模型 (MVVM) 体系结构生成应用程序时，类的基础数据通常称为视图模型。

数据绑定在以下一系列文章中讨论：

## <a name="basic-bindingsbasic-bindingsmd"></a>[基本绑定](basic-bindings.md)

了解数据绑定目标和源之间的差异，并查看代码和 XAML 中的简单数据绑定。

## <a name="binding-modebinding-modemd"></a>[绑定模式](binding-mode.md)

发现绑定模式可以如何控制两个对象之间的数据流。

## <a name="string-formattingstring-formattingmd"></a>[字符串格式设置](string-formatting.md)

使用数据绑定进行格式化和显示为字符串的对象。

## <a name="binding-pathbinding-pathmd"></a>[绑定路径](binding-path.md)

了解更深入到`Path`数据绑定来访问子属性和集合成员的属性。

## <a name="binding-value-convertersconvertersmd"></a>[绑定值转换器](converters.md)

绑定值转换器用于变更数据绑定中的值。

## <a name="the-command-interfacecommandingmd"></a>[命令界面](commanding.md)

实现`Command`具有数据绑定属性。



## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [从 Xamarin.Forms 簿的数据绑定章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)
