---
title: macOS Xamarin.Mac 开发人员的 Api
description: 本文档介绍如何读取 OBJECTIVE-C 的选择器以及如何在 Xamarin.Mac 应用中查找其相应的 C# 方法。
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/02/2017
ms.openlocfilehash: cceaa2f6e89b712be5929f7e978663d8c47f18c5
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791545"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>macOS Xamarin.Mac 开发人员的 Api

## <a name="overview"></a>概述

对于很多使用 Xamarin.Mac 开发时间，可以认为、 读取和编写在 C# 中，而与基础 Objective C Api 过多地考虑。 但是，有时你将需要从 Apple 中读取的 API 文档转换从堆栈溢出到解决方案的答案对于您的问题，或向现有示例进行比较。

## <a name="reading-enough-objective-c-to-be-dangerous"></a>读取足够 Objective C 要危险

它有时需要读取 OBJECTIVE-C 的定义或方法调用，将该转换为等效的 C# 方法。 让我们看一看 Objective C 函数定义，并将分解部分。 此方法 (*选择器*Objective C 中) 上可以找到`NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

声明可以左到右阅读：

- `-`前缀意味着它是一个实例 （非静态） 方法。 + 表示它是一个类 （静态） 方法
- `(BOOL)` 是返回类型 (C# 中的 bool)
- `canDragRowsWithIndexes` 是名称的第一部分。
- `(NSIndexSet *)rowIndexes` 是第一个参数，并与之具有键入。 第一个参数采用以下格式： `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` 为第二个参数、 类型。 第一个之后的每个参数为格式： `selectorPart:(Type) pararmName`
- 此消息选择器的完整名称是： `canDragRowsWithIndexes:atPoint:`。 请注意`:`末尾-很重要。
- 实际的 Xamarin.Mac C# 绑定是： `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

此选择器调用可以读取相同的方式：

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- 实例`v`时遇到其`canDragRowsWithIndexes:atPoint`选择器使用两个参数，调用`set`和`point`、 传入的。
- 在 C# 中，方法调用类似如下所示： `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>查找给定选择器的 C# 成员

现在，找到你需要调用的 OBJECTIVE-C 的选择器后下, 一步将，映射到等效的 C# 成员。 有四个方法，你可以尝试 (继续`NSTableView CanDragRows`示例):

1. 使用自动完成列表来快速扫描内容相同的名称。 是的一个实例，因为我们知道`NSTableView`可以键入：

    - `NSTableView x;`
    - `x.` [ctrl + 空间如果未显示列表）。
    - `CanDrag` [输入]
    - 右键单击的方法，请转到声明，以打开程序集浏览器，其中你可以比较`Export`属性设为问题的选择器

2. 搜索整个类绑定。 是的一个实例，因为我们知道`NSTableView`可以键入：

    - `NSTableView x;`
    - 右键单击`NSTableView`，请转到声明移到程序集浏览器
    - 搜索问题的选择器

3. 你可以使用[Xamarin.Mac API 联机文档](https://developer.xamarin.com/api/root/monomac-lib/)。

4. Miguel 提供 Xamarin.Mac Api 的"Rosetta 一成不变"视图[此处](http://tirania.org/tmp/rosetta.html)，您可以通过搜索给定的 api。 如果你的 API 不是 AppKit 或 macOS 特定，你可能会那里发现。

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
