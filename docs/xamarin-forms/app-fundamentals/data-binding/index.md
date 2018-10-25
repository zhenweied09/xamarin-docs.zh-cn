---
title: Xamarin.Forms 数据绑定
description: 数据绑定是链接的两个对象的属性，使一个属性中的更改会自动反映在另一个属性的技术。 数据绑定是模型-视图-视图模型 (MVVM) 应用程序体系结构的重要组成部分。
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2018
ms.openlocfilehash: def97ab77781c7a7156d4c4178097184614f3e8b
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "35240348"
---
# <a name="xamarinforms-data-binding"></a>Xamarin.Forms 数据绑定

_数据绑定是链接的两个对象的属性，使一个属性中的更改会自动反映在另一个属性的技术。数据绑定是模型-视图-视图模型 (MVVM) 应用程序体系结构的重要组成部分。_

## <a name="the-data-linking-problem"></a>数据链接问题

Xamarin.Forms 应用程序包含一个或多个页面，其中每个通常包含多个名为的用户界面对象*视图*。 该程序的首要任务之一是保持同步，这些视图并来跟踪不同的值或它们所代表的选择。 视图通常从基础数据源，代表值和用户操作这些视图，可以更改该数据。 基础数据视图更改时，必须反映该更改，同样，当基础数据更改时，所做的更改必须反映在视图中。

若要成功地处理此作业，必须在这些视图或基础数据中的更改的通知程序。 常见的解决方案是定义发生更改时发出信号的事件。 事件处理程序随后进行安装，这些更改的通知。 它通过将数据从一个对象传输到另一个响应。 但是，当存在多个视图时，必须也有很多事件处理程序，并获取涉及大量的代码。

## <a name="the-data-binding-solution"></a>数据绑定解决方案

数据绑定自动执行此作业，并将事件处理程序呈现不必要。 （这些事件是仍有必要，但是，因为数据绑定基础结构使用它们。）在代码或 XAML，可以实现数据绑定，但它们是在它们有助于减少代码隐藏文件的大小的 XAML 中更常见。 通过将声明性代码或标记替换为事件处理程序中的程序代码，该应用程序是简化和阐明了。

数据绑定中涉及的两个对象之一几乎始终是派生的元素`View`和窗体页面的可视界面的一部分。 另一个对象是：

- 另一个`View`派生，通常在同一页上。
- 代码文件中的对象。

在如中的演示程序[ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)示例，两个数据绑定`View`派生类通常显示为的清晰和简洁的目的。 但是，相同的原则可以应用于之间的数据绑定`View`和其他对象。 当使用模型-视图-视图模型 (MVVM) 体系结构生成应用程序时，具有基础数据的类通常称为 ViewModel。

在以下一系列文章中探讨了数据绑定：

## <a name="basic-bindingsbasic-bindingsmd"></a>[基本绑定](basic-bindings.md)

了解数据绑定目标和源之间的差异并查看代码和 XAML 中的简单数据绑定。

## <a name="binding-modebinding-modemd"></a>[绑定模式](binding-mode.md)

了解如何绑定模式，可以控制两个对象之间的数据流。

## <a name="string-formattingstring-formattingmd"></a>[字符串格式设置](string-formatting.md)

使用数据绑定进行格式化和显示为字符串的对象。

## <a name="binding-pathbinding-pathmd"></a>[绑定路径](binding-path.md)

深入了解`Path`数据绑定来访问子属性和集合成员的属性。

## <a name="binding-value-convertersconvertersmd"></a>[绑定值转换器](converters.md)

绑定值转换器用于变更数据绑定中的值。

## <a name="binding-fallbacksbinding-fallbacksmd"></a>[绑定回退](binding-fallbacks.md)

使数据绑定通过定义要使用在绑定过程失败时的回退值更稳健。

## <a name="the-command-interfacecommandingmd"></a>[命令界面](commanding.md)

实现`Command`具有数据绑定属性。

## <a name="compiled-bindingscompiled-bindingsmd"></a>[已编译的绑定](compiled-bindings.md)

使用已编译的绑定，以提高数据绑定性能。

## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [数据绑定 Xamarin.Forms 书籍章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)
