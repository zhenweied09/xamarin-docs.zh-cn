---
title: 使用表和单元格
description: 显示 UITableView 使用 Xamarin.iOS 的数据
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/06/2016
ms.openlocfilehash: a1cda3632a75c7e462e763a34fdb5b586237b670
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-tables-and-cells"></a>使用表和单元格


本部分介绍用于创建和显示表的类，然后提供如何在 Xamarin.iOS 中使用它们的示例。 它将介绍对于表，自定义布局，实现编辑和 Xamarin iOS 设计器用于以可视方式设计表使用的默认外观。 有时显示显然是行 （如音乐应用程序） 和其他情况下很难识别 table 控件 （如编辑联系人应用或在消息应用程序中的对话中） 的列表。

对于那些从事具有 Xamarin.Android 的跨平台应用程序，UITableView 控件是类似于 Android 中的 ListView 类 （和 UITableViewSource 类是类似于 Android 的适配器类）。

这些文章需要全面了解了如何使用表，包括：

-   **表部件**– 简介和说明的可视元素`UITableView`控件。 
-   **在表中显示数据**– 演示如何创建和填充表，使用不同的表和单元格样式和通过回收单元格对象来避免内存问题。 
-   **高级用法**– 构建自定义单元格，并使用 UITableView 类的编辑功能。 
-   **以可视方式创建表**– 使用 for iOS Xamarin 设计器使用情节提要创建表驱动的接口。 


## <a name="contents"></a>内容

 [表部件&amp;功能](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [使用数据填充表](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [自定义表的外观](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [编辑](~/ios/user-interface/controls/tables/editing.md)
 
 [行操作](~/ios/user-interface/controls/tables/row-action.md)

 [在情节提要中创建表](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [自动调整行高](~/ios/user-interface/controls/tables/autosizing-row-height.md)


## <a name="related-links"></a>相关链接

- [WorkingWithTables （示例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables/)
- [情节提要 （示例） 中的表](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [情节提要简介](~/ios/user-interface/storyboards/index.md)
- [情节提要 TableView 原因之一](https://developer.xamarin.com/recipes/ios/general/storyboard/storyboard_a_tableview)
- [MonoTouch.Dialog 简介](~/ios/user-interface/monotouch.dialog/index.md)
- [在 Github 上的 TableEditing 示例](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [在 Github 上的 TableParts 示例](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [在 Github 上的 TableAndCellStyles 示例](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [UITableView 类引用](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [UITableViewCell 类引用](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
