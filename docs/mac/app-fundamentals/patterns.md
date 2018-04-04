---
title: 常见的模式和惯例
description: 本文档介绍模型-视图-控制器模式、 数据源和委托模式和协议。
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/17/2016
ms.openlocfilehash: cf499c555ddbefbdb5a7fae3ae8929a17e0d0cd7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="common-patterns-and-idioms"></a>常见的模式和惯例

在 Apple Api 通过 C# 公开整个某些习语和模式提出反复。 如果你有使用 Xamarin.iOS 编程经验，它们看起来可能熟悉。 文档将通常引用这些模式和习语重复，因此具有清楚地了解它们将帮助你了解你找到的文档。

## <a name="mvc---model-view-controller"></a>MVC 的模型视图控制器

模型视图控制器或简称，MVC 是整个 Cocoa 找到非常通用模式。 详细的讨论已超出本文档范围内，但简言之，它是一种构造为组件的应用程序：

- **模型**对象表示基础数据被查看和操作 （如通讯簿中的地址）
- **视图**对象处理屏幕上的给定对象的绘制和处理用户交互 （在屏幕上显示地址的文本字段）
- **控制器**对象处理模型和视图之间的交互。 它们推送模型更改"最多"以更新视图，然后从该视图按"下"更改，当用户在 UI 中进行更改。

如果你熟悉 MVVM （模型视图视图模型） 从 WPF 等其他库，控制器视图模型类似，但通常更紧密地绑定到特定的 UI 元素。

更多详细信息可在此处找到：

- [Apple 的网站上的学习 MVC](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Objective C 中的模型视图控制器](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [使用 Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>数据源 / 委托 / 子类化

Cocoa 中另一种很常见的模式涉及到提供给 UI 元素的数据和对用户交互的作出反应。 使用`NSTableView`作为示例，你需要以某种方式为每个行提供数据，某些设置的 UI 元素表示的行，某些组以响应用户交互，并可能一定的自定义的行为。 数据源和委托模式使您能够大多数情况下处理而无需采用子类化`NSTableView`自己。

- `DataSource`属性分配的自定义子类的实例`NSTableViewDataSource`调用该方法可使用数据填充该表 (通过`GetRowCount`和`GetObjectValue`)。

- `Delegate`属性分配的自定义子类的实例`NSTableViewDelegate`该给定的模型对象提供的视图 (通过`GetViewForItem`) 并处理用户界面交互 (通过`DidClickTableColumn`，`MouseDownInHeaderOfTableColumn`等)。

在某些情况下，你将想要自定义委托或数据源中提供的挂钩超出一种方法中的控件和直接可以子类化视图。 但是请注意，在许多情况下，重写默认行为将然后要求你自己处理所有该行为 （自定义选择行为可能要求你自己实现所有所选内容行为）。

在 Xamarin.iOS，一些 Api，如`UITableView`使用一个属性，实现委托和数据源扩展 (`UITableViewSource`)。 若要解决单个的 C# 类只能有一个基类，和协议我们提供的常见限制它这是通过基类，这些类。

有关使用 Xamarin.Mac 应用程序中的表视图的详细信息，请参阅我们[表视图](~/mac/user-interface/table-view.md)文档。

## <a name="protocols"></a>协议

Objective C 中的协议可以与 C# 中的接口相比，并且在许多情况下可用于在类似情况下。 例如`NSTableView`上例中，委托和数据源是实际协议。 Xamarin.Mac 公开这些基类，这些类为使用可以重写虚拟方法。 C# 接口和 Objective C 协议的主要区别是，一种协议中的某些方法可能是可选的实现。 你将需要查看的文档和/或 API 以确定什么是可选的定义。

详细信息，请参阅我们[委托、 协议和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)文档。



## <a name="related-links"></a>相关链接

- [表视图](~/mac/user-interface/table-view.md)
- [使用 windows](~/mac/user-interface/window.md)
- [委托、 协议和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
