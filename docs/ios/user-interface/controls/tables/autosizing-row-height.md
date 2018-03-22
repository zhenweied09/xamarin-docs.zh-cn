---
title: "自动调整行高"
ms.topic: article
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: f1b35905d14086dcfc0cb749c8e4cc7de1608dd5
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="auto-sizing-row-height"></a>自动调整行高

从 iOS 8，Apple 增加能够创建表视图 (`UITableView`)，可以自动增加和减少基于其使用的内容自动布局、 大小类和约束大小某一给定行的高度。

iOS 11 添加了要自动展开的行的功能。 标头、 页脚和单元格可以现在自动调整根据其内容。 但是，如果在 iOS 设计器中，接口生成器中创建你的表，或如果它具有固定行高必须手动启用自动调整单元格，本指南中所述。

## <a name="cell-layout-in-the-ios-designer"></a>在 iOS 设计器中的单元格布局

打开你想要在 iOS 设计器中中的行的自动调整大小的表视图情节提要选择单元格的*原型*和设计的单元格的布局。 例如：

[![](autosizing-row-height-images/table01.png "该单元格的原型设计")](autosizing-row-height-images/table01.png#lightbox)

原型中的每个元素，将添加约束，以将元素保留在正确的位置，为旋转或不同的 iOS 设备的屏幕大小调整表视图的大小时。 例如，固定`Title`到顶部，左侧和右侧的单元格的*内容视图*:

[![](autosizing-row-height-images/table02.png "固定到顶端、 左侧和右侧的单元格的内容视图标题")](autosizing-row-height-images/table02.png#lightbox)

对于我们的示例表，小型`Label`(下`Title`) 是可以缩小和增大以增加或减少行高度的字段。 若要实现此效果，添加要固定左侧、 右侧、 顶部和底部的标签的以下约束：

[![](autosizing-row-height-images/table03.png "这些约束固定左侧、 右侧、 顶部和底部的标签")](autosizing-row-height-images/table03.png#lightbox)

现在，我们具有完全受约束的单元格中的元素，我们需要阐明应拉伸的元素。 若要执行此操作，将设置**内容拥抱优先级**和**内容压缩抵制优先级**根据需要在**布局**属性填充部分：

[![](autosizing-row-height-images/table03a.png "布局部分中的属性填充")](autosizing-row-height-images/table03a.png#lightbox)

将你想要展开此项可具有的元素设置**较低**拥抱优先级值和**较低**压缩抵制优先级值。

接下来，我们需要选择单元格原型，并为其提供一个唯一**标识符**:

[![](autosizing-row-height-images/table04.png "提供的唯一标识符的单元格原型")](autosizing-row-height-images/table04.png#lightbox)

对于本示例中， `GrowCell`。 我们在填充表时，我们将更高版本使用此值。

> [!IMPORTANT]
> 如果表中包含多个单元格类型 (**原型**)，你需要确保每个类型都有其自己唯一`Identifier`自动行调整大小，工作的。

对于我们的单元格原型的每个元素，分配**名称**以将其公开给 C# 代码。 例如：

[![](autosizing-row-height-images/table05.png "将分配一个名称以将其公开给 C# 代码")](autosizing-row-height-images/table05.png#lightbox)

接下来，添加的自定义类`UITableViewController`、`UITableView`和`UITableCell`（原型）。 例如： 

[![](autosizing-row-height-images/table06.png "UITableViewController、 UITableView 和 UITableCell 添加自定义类")](autosizing-row-height-images/table06.png#lightbox)

最后，若要确保所有预期的内容显示在我们的标签，设置**行**属性`0`:

[![](autosizing-row-height-images/table06.png "行属性设置为 0")](autosizing-row-height-images/table06a.png#lightbox)

定义用户界面时，让我们添加代码以启用自动行高度的调整大小。

## <a name="enabling-auto-resizing-height"></a>启用自动调整大小的高度

在我们的表视图的数据源 (`UITableViewDatasource`) 或源 (`UITableViewSource`)，当我们取消排队的单元格，我们需要使用`Identifier`我们在设计器中定义。 例如：

```csharp
public string CellID {
    get { return "GrowCell"; }
}
...

public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (CellID, indexPath) as GrowRowTableCell;
    var item = Items [indexPath.Row];

    // Setup
    cell.Image = UIImage.FromFile(item.ImageName);
    cell.Title = item.Title;
    cell.Description = item.Description;

    return cell;
}
```

默认情况下，将为自动调整大小行高度设置表视图。 若要确保此操作，请`RowHeight`属性应设置为`UITableView.AutomaticDimension`。 我们还需要设置`EstimatedRowHeight`属性中的我们`UITableViewController`。 例如：

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Initialize table
    TableView.DataSource = new GrowRowTableDataSource(this);
    TableView.Delegate = new GrowRowTableDelegate (this);
    TableView.RowHeight = UITableView.AutomaticDimension;
    TableView.EstimatedRowHeight = 40f;
    TableView.ReloadData ();
}
```

此估计值不一定是准确的只需粗略的估算值的平均的表视图中的每个行的高度。

使用此代码中的位置，当应用运行时，每一行将收缩并增长根据单元格原型中的最后一个标签的高度。 例如：

[![](autosizing-row-height-images/table07.png "运行示例表")](autosizing-row-height-images/table07.png#lightbox)


## <a name="related-links"></a>相关链接

- [GrowRowTable （示例）](https://developer.xamarin.com/samples/monotouch/GrowRowTable/)
