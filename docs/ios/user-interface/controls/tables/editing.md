---
title: 编辑使用 Xamarin.iOS 的表
description: 本文档介绍如何编辑 Xamarin.iOS 中的表。 它讨论轻扫可删除、 编辑模式，以及行插入。
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 28ebf1157a1bfc9f7bd910fd11365b29cecb9529
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789985"
---
# <a name="editing-tables-with-xamarinios"></a>编辑使用 Xamarin.iOS 的表

通过重写中的方法启用表的编辑功能`UITableViewSource`子类。 最简单的编辑行为是可以使用单个方法重写实现轻扫删除手势。
可以通过编辑模式中的表实现更复杂的编辑 （包括移动行）。

## <a name="swipe-to-delete"></a>轻扫删除

轻扫以删除功能是在用户期望的 iOS 中的自然笔势。 

 [![](editing-images/image10.png "轻扫到删除的示例")](editing-images/image10.png#lightbox)

有三个方法重写，会影响要显示的轻扫笔势**删除**单元中的按钮：

-   **CommitEditingStyle** – 表源将检测此方法被重写，并且会自动启用轻扫删除笔势。 该方法的实现应调用`DeleteRows`上`UITableView`导致要消失，并且还可以从您的模型 （例如，一个数组，字典或数据库） 删除基础数据的单元格。 
-   **CanEditRow** – 如果 CommitEditingStyle 被重写，所有行都被都认为是可编辑。 如果此方法实现，并返回 false （对于某些特定的行，或者为所有行） 然后轻扫删除手势将不能在该单元格中。 
-   **TitleForDeleteConfirmation** – 根据需要指定的文本**删除**按钮。 如果未实现此方法将"删除"按钮文本。 


在实现这些方法`TableSource`类如下所示：

```csharp
public override void CommitEditingStyle (UITableView tableView, UITableViewCellEditingStyle editingStyle, Foundation.NSIndexPath indexPath)
{
    switch (editingStyle) {
        case UITableViewCellEditingStyle.Delete:
            // remove the item from the underlying data source
            tableItems.RemoveAt(indexPath.Row);
            // delete the row from the table
            tableView.DeleteRows (new NSIndexPath[] { indexPath }, UITableViewRowAnimation.Fade);
            break;
        case UITableViewCellEditingStyle.None:
            Console.WriteLine ("CommitEditingStyle:None called");
            break;
    }
}
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override string TitleForDeleteConfirmation (UITableView tableView, NSIndexPath indexPath)
{   // Optional - default text is 'Delete'
    return "Trash (" + tableItems[indexPath.Row].SubHeading + ")";
}
```

对于本例`UITableViewSource`已更新为使用`List<TableItem>`（而不字符串数组） 为数据源，因为它支持添加和删除项集合中。


## <a name="edit-mode"></a>编辑模式

当表处于编辑模式时用户在每个行，这将显示删除按钮时接触上看到红色停止小组件。 表还显示一个句柄图标，以指示可以拖动行进行更改的顺序。
**TableEditMode**示例实现这些功能，如所示。

 [![](editing-images/image11.png "TableEditMode 示例实现这些功能，如所示")](editing-images/image11.png#lightbox)

上有多种不同方法`UITableViewSource`影响表的编辑模式行为：

-   **CanEditRow** – 是否可以编辑每个行。 返回 false 可禁止轻扫到 delete 和删除处于编辑模式。 
-   **CanMoveRow** – 返回为真启用移动句柄或 false 将禁止移动。 
-   **EditingStyleForRow** – 如果表处于编辑模式，此方法的返回值确定是否该单元格显示红色删除图标或绿色添加图标。 返回`UITableViewCellEditingStyle.None`如果行不应为可编辑。 
-   **MoveRow** – 行，以便可以修改基础数据结构，以显示表中匹配的数据移动时调用。 


前三个方法的实现是一个相对较直截了当 – 除非你想要使用`indexPath`若要更改的特定行的行为，只需进行硬编码返回值为整个表。

```csharp
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you don't allow re-ordering
}
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    return UITableViewCellEditingStyle.Delete; // this example doesn't support Insert
}
```

`MoveRow`实现是稍微有些复杂，因为它需要 alter 基础的数据结构，以匹配新的顺序。 由于数据作为实现`List`下面的代码中删除的数据项在其原位置，并将其插入到新位置。 如果数据已存储在一个 SQLite 数据库表具有一个顺序列 （例如），此方法将改为需要执行一些 SQL 操作以重新排序的列中的数字。

```csharp
public override void MoveRow (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    var item = tableItems[sourceIndexPath.Row];
    var deleteAt = sourceIndexPath.Row;
    var insertAt = destinationIndexPath.Row;
    
    // are we inserting 
    if (destinationIndexPath.Row < sourceIndexPath.Row) {
        // add one to where we delete, because we're increasing the index by inserting
        deleteAt += 1;
    } else {
        // add one to where we insert, because we haven't deleted the original yet
        insertAt += 1;
    }
    tableItems.Insert (insertAt, item);
    tableItems.RemoveAt (deleteAt);
}
```

最后，若要获取表到编辑模式，则**编辑**按钮需要调用`SetEditing`如下所示

```csharp
table.SetEditing (true, true);
```

完成用户和编辑，**完成**按钮关闭应编辑模式：

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>行插入编辑样式

从表中的行插入是一种常见用户界面 – 标准 iOS 应用中的主要示例是**编辑联系人**屏幕。 此屏幕截图中显示的行插入功能的工作原理 – 在编辑模式下没有附加行 （单击） 时将其他行插入到数据。 编辑完成时，临时 **（添加新）** 该行将被移除。

 [![](editing-images/image12.png "编辑完成后，新添加的临时删除行")](editing-images/image12.png#lightbox)

上有多种不同方法`UITableViewSource`影响表的编辑模式行为。 这些方法具有在示例代码中实现了，如下所示：

-   **EditingStyleForRow** – 返回`UITableViewCellEditingStyle.Delete`包含数据，并返回的行`UITableViewCellEditingStyle.Insert`的最后一行 （这将添加专门用于充当插入按钮）。 
-   **CustomizeMoveTarget** – 虽然用户要移动的单元格此可选方法的返回值可以重写其选择的位置。 这意味着可以防止删除中某些位置 – 例如防止任何行后移动此示例的单元格 **（添加新）** 行。 
-   **CanMoveRow** – 返回为真启用移动句柄或 false 将禁止移动。 在示例中，最后一行有隐藏因为它是到服务器作为插入按钮仅移动句柄。 


我们还添加了两个自定义方法以添加 'insert' 行，然后删除它再次在不再需要时。 从调用**编辑**和**完成**按钮：

-   **WillBeginTableEditing** – 当**编辑**按钮接触它调用`SetEditing`表置于编辑模式。 这会触发其中我们显示 WillBeginTableEditing 方法 **（添加新）** 要充当 '插入 button' 的表结尾处添加一行。 
-   **DidFinishTableEditing** – 接触完成按钮时`SetEditing`会再次调用关闭编辑模式。 示例代码用于删除 **（添加新）** 不再需要从表在编辑时的行。 


示例文件中实现这些方法重写**TableEditModeAdd/Code/TableSource.cs**:

```csharp
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    if (tableView.Editing) {
        if (indexPath.Row == tableView.NumberOfRowsInSection (0) - 1)
            return UITableViewCellEditingStyle.Insert;
        else
            return UITableViewCellEditingStyle.Delete;
    } else // not in editing mode, enable swipe-to-delete for all rows
        return UITableViewCellEditingStyle.Delete;
}
public override NSIndexPath CustomizeMoveTarget (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath proposedIndexPath)
{
    var numRows = tableView.NumberOfRowsInSection (0) - 1; // less the (add new) one
    if (proposedIndexPath.Row >= numRows)
        return NSIndexPath.FromRowSection(numRows - 1, 0);
    else
        return proposedIndexPath;
} 
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return indexPath.Row < tableView.NumberOfRowsInSection (0) - 1;
}
```

这两个自定义方法用于添加和删除 **（添加新）** 启用或禁用行在表的编辑模式时：

```csharp
public void WillBeginTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // insert the 'ADD NEW' row at the end of table display
    tableView.InsertRows (new NSIndexPath[] { 
            NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0), 0) 
        }, UITableViewRowAnimation.Fade);
    // create a new item and add it to our underlying data (it is not intended to be permanent)
    tableItems.Add (new TableItem ("(add new)"));
    tableView.EndUpdates (); // applies the changes
}
public void DidFinishTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // remove our 'ADD NEW' row from the underlying data
    tableItems.RemoveAt ((int)tableView.NumberOfRowsInSection (0) - 1); // zero based :)
    // remove the row from the table display
    tableView.DeleteRows (new NSIndexPath[] { NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0) - 1, 0) }, UITableViewRowAnimation.Fade);
    tableView.EndUpdates (); // applies the changes
}
```

最后，此代码实例化**编辑**和**完成**按钮，与启用或禁用编辑模式时要对其接触的 lambda:

```csharp
done = new UIBarButtonItem(UIBarButtonSystemItem.Done, (s,e)=>{
    table.SetEditing (false, true);
    NavigationItem.RightBarButtonItem = edit;
    tableSource.DidFinishTableEditing(table);
});
edit = new UIBarButtonItem(UIBarButtonSystemItem.Edit, (s,e)=>{
    if (table.Editing)
        table.SetEditing (false, true); // if we've half-swiped a row
    tableSource.WillBeginTableEditing(table);
    table.SetEditing (true, true);
    NavigationItem.LeftBarButtonItem = null;
    NavigationItem.RightBarButtonItem = done;
});
```

不过，你可以使用，不非常频繁使用此行插入 UI 模式`UITableView.BeginUpdates`和`EndUpdates`方法要进行动画处理的插入或删除任何表中的单元格。 使用这些方法的规则是返回值的差`RowsInSection`之间`BeginUpdates`和`EndUpdates`调用必须与匹配的单元格添加/删除而 net 数目`InsertRows`和`DeleteRows`方法。 如果不更改基础数据源，以便匹配插入/删除表视图将会出错。


## <a name="related-links"></a>相关链接

- [WorkingWithTables （示例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
