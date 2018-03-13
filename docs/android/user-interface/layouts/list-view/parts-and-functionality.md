---
title: "ListView 部件和功能"
ms.topic: article
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: 4a7947c40d80c0ff8cb35dab54a11907280335d9
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="listview-parts-and-functionality"></a>ListView 部件和功能


## <a name="overview"></a>概述

A`ListView`以下部分组成：

- **行**&ndash;列表中的数据的可见表示。

- **适配器**&ndash;将数据源绑定到列表视图的非视觉类。

- **快速滚动**&ndash;允许用户滚动列表的长度的句柄。

- **部分索引**&ndash;通过滚动浮动的用户界面元素以指示在列表中的当前行的位置的行。

这些屏幕截图使用基本`ListView`控件以显示快速滚动和部分索引的呈现方式：

[![使用普通的旧行的应用的屏幕快照快速滚动和部分索引](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

构成元素`ListView`下面更详细地描述：


## <a name="rows"></a>行

每行都有其自己`View`。 该视图可以是之一中定义的内置视图`Android.Resources`，或自定义视图。 每个行可以使用相同的视图布局或所有在可能不同。 使用内置的布局和其他说明如何定义自定义布局的本文档中有示例。


## <a name="adapter"></a>适配器

`ListView`控件要求`Adapter`提供格式化`View`每一行。 Android 有内置的适配器和视图，可以使用，也可以创建自定义类。


## <a name="fast-scrolling"></a>快速滚动

当`ListView`包含许多行的数据快速滚动可以启用以帮助用户导航到列表的任何部分。 （可选） 启用 （和自定义 API 级别 11 和更高版本），可以是快速滚动滚动条。


## <a name="section-index"></a>部分索引

时滚动较长的列表，可选的节索引提供反馈的用户列表的哪一部分它们当前正在查看。 仅针对长的列表，通常与结合快速滚动适当。


## <a name="classes-overview"></a>类概述

用于显示的主类`ListViews`如下所示：

[![UML 关系图说明 ListView 和关联的类之间的关系](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

下面描述了每个类的用途：

- **ListView** &ndash;显示一个可滚动行集合的用户界面元素。 在手机它通常将占用整个屏幕 (在这种情况下，`ListActivity`类可用于) 或它可能是在手机或平板电脑设备上更大的布局的一部分。

- **视图** &ndash; Android 中的一个视图可以是任何用户界面元素，但的上下文中`ListView`它需要`View`将提供给每个行。

- **BaseAdapter** &ndash;基类适配器实现，若要将绑定`ListView`到数据源。

- **基于 ArrayAdapter** &ndash;内置适配器类，将绑定到字符串数组`ListView`进行显示。 泛型`ArrayAdapter<T>`执行相同的操作对于其他类型。

- **CursorAdapter** &ndash;使用`CursorAdapter`或`SimpleCursorAdapter`以显示基于 SQLite 查询的数据。

本文档包含使用的简单示例`ArrayAdapter`以及需要的自定义实现的更复杂示例`BaseAdapter`或`CursorAdapter`。

