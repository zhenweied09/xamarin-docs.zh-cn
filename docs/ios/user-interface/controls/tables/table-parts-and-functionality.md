---
title: "表部件和功能"
ms.topic: article
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: fece27db2945ee7142296ec0872f395c127e318e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="table-parts-and-functionality"></a>表部件和功能

UITableView 可以分组或普通样式，并且包含以下部分：

-  [部分标题](#Section_Header)
-  [单元格](#Cells)（或行，如果需要）
-  [部分页脚](#Section_Footer)
-  [索引](#Index)
-  [编辑模式](#Edit_Features)(包括轻扫以删除和拖动手柄以更改行顺序) 

这些屏幕快照显示的部分行、 页眉、 页脚、 编辑控件和索引的显示方式。

 [![](table-parts-and-functionality-images/image1a.png "这些屏幕快照显示的部分行、 页眉、 页脚、 编辑控件和索引的显示方式")](table-parts-and-functionality-images/image1a.png#lightbox)

下面更详细地描述了三个部分：

<a name="Section_Header" />

## <a name="section-header"></a>部分标题

单元格可以根据需要进行划分为部分、 标记为使用自定义标头，和/或标有页脚。 标头可以设置具有字符串值，也可以提供自定义视图，以便允许对不同的布局和样式。

<a name="Cells" />

## <a name="cells"></a>单元格

单元格是表的主要用户界面元素。 当正确实现，则单元格为内存效率供重复使用。 有四个内置的单元格样式，并且你可以创建你自己自定义单元格-在代码中，或在设计器中使用情节提要时。

<a name="Section_Footer"/>

## <a name="section-footer"></a>部分页脚

可选节页脚可用设置的字符串值，或可以提供自定义视图，以便允许对不同的布局和样式。 可以单独设置部分页眉和页脚。

<a name="Index" />

## <a name="index"></a>索引

索引显示为下表的右边缘的字符的条带。
接触或拖动对索引可以加快对滚动到表的该部分。 索引是可选的但建议，以便导航较长的列表。 索引通常不用于分组样式。

<a name="Edit_Features" />

## <a name="editing-mode"></a>编辑模式

有几个不同的编辑功能：

- 若要删除单个单元格的轻扫。
- 进入编辑模式，以显示每个行上的删除按钮 
- 进入编辑模式，以显示重新排序的句柄。 
- 插入 （具有动画） 的新单元格。

此文档的其余部分演示如何实现使用 Xamarin.iOS 的所有这些 UITableView 功能。


## <a name="classes-overview"></a>类概述

用于显示表视图的主类如下所示：

[![](table-parts-and-functionality-images/classdiagram.png "此处显示用于显示表视图的主类")](table-parts-and-functionality-images/classdiagram.png#lightbox)

下面描述了每个类的用途：

- **UITableView** – 一个包含滚动容器中的单元格的集合视图。 表视图通常使用整个屏幕 iphone 版应用中，但可能存在 iPad 上更大的视图的一部分 （或出现在 popover）。 
- **UITableViewCell** – 表示在表视图中的单个单元格 （或行） 的视图。 有四个内置的单元格类型和可以同时在 C# 中或使用 iOS 设计器创建自定义单元格。 
- **UITableViewSource** – Xamarin.iOS 独占抽象提供要显示的表，包括行计数、 返回每一行的单元格视图、 处理行选择和许多其他可选功能所需的所有方法的类。 你*必须*子类此列表来获得 UITableView 工作。 
- **NSIndexPath** – 唯一标识表中的位置的单元格包含行和部分属性。 
- **UITableViewController** – 准备就绪，可以使用 UIViewController 作为其视图且可通过 TableView 属性访问具有 UITableView 硬编码。 
- **UIViewController** – 如果表不占用整个屏幕可以添加到其框架与任何 UIViewController UITableView 适当地设置。 

UITableViewSource 替换下面的两个类，该类在 Xamarin.iOS 中仍然可用但不是正常情况下必需：

- **UITableViewDataSource** – 在为抽象类的 Xamarin.iOS 中建模的 OBJECTIVE-C 的协议。 必须子类化以表的视图提供有关每个单元格，以及有关页眉、 页脚和数量的行和表中的部分的信息。 
- **UITableViewDelegate** – 在为类的 Xamarin.iOS 中建模的 OBJECTIVE-C 的协议。 处理所选内容，编辑功能和其他可选的表的功能。 

在本文档示例都使用 UITableViewSource，忽略这两个类。 因为在 Apple 的文档中找到任何 OBJECTIVE-C 的示例将它们，引用，以便它可用于了解它们执行的操作 （以及你可以改为使用 Xamarin.iOS 的 UITableViewSource），它们是此处提及。

## <a name="related-links"></a>相关链接

- [WorkingWithTables （示例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
