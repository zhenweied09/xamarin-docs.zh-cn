---
title: "填充包含数据的表"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: fb0e4341d8d8ad0719f35c691add9bad1d3f85a8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="populating-a-table-with-data"></a>填充包含数据的表

若要将行添加到`UITableView`你需要实现`UITableViewSource`子类并重写查看表的方法调用来填充本身。

本指南涵盖：

- 子类化 UITableViewSource
- 单元格重用
- 添加索引
- 添加页眉和页脚


<a name="Subclassing_UITableViewSource" />

## <a name="subclassing-uitableviewsource"></a>子类化 UITableViewSource

A`UITableViewSource`子类分配到每个`UITableView`。 表视图查询源类，以确定如何呈现本身 （例如，所需的行数和每个行，如果不同于默认的高度）。 最重要的是，源提供了使用数据填充每个单元格视图。

有只有两个必需的方法使显示数据的表所需：

-   **RowsInSection** – 返回[ `nint` ](http://developer.xamarin.com/guides/cross-platform/macios/nativetypes/)表应显示的数据的总行数的计数。
-   **GetCell** – 返回`UITableCellView`填充传递给方法的相应行索引的数据。


BasicTable 示例文件**TableSource.cs**具有的最简单的可能实现`UITableViewSource`。 在以下代码段中，你可以看到它接受要在表中显示的字符串数组并返回包含每个字符串的默认单元格样式：

```csharp
public class TableSource : UITableViewSource {

        string[] TableItems;
        string CellIdentifier = "TableCell";

        public TableSource (string[] items)
        {
            TableItems = items;
        }

        public override nint RowsInSection (UITableView tableview, nint section)
        {
            return TableItems.Length;
        }

        public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewCell cell = tableView.DequeueReusableCell (CellIdentifier);
            string item = TableItems[indexPath.Row];

            //---- if there are no cells to reuse, create a new one
            if (cell == null)
            { cell = new UITableViewCell (UITableViewCellStyle.Default, CellIdentifier); }

            cell.TextLabel.Text = item;

            return cell;
        }
}
```

A`UITableViewSource`可使用任何数据结构，从简单的字符串数组 （如本示例中所示） 对列表 <> 或其他集合。 实现`UITableViewSource`方法隔离基础数据结构中的表。

若要使用此子类，可创建以构造源，然后将其分配到的实例的字符串数组`UITableView`:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    table = new UITableView(View.Bounds); // defaults to Plain style
    string[] tableItems = new string[] {"Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers"};
    table.Source = new TableSource(tableItems);
    Add (table);
}
```

生成的表类似如下所示：

 [ ![](populating-a-table-with-data-images/image3.png "运行示例表")](populating-a-table-with-data-images/image3.png)

大多数表允许用户接触的行选择它并执行一些其他操作 （如播放一首歌曲，或调用一个联系人，或显示另一个屏幕）。 若要实现此目的，有几个我们需要执行的操作。 首先，让我们创建显示一条消息，当用户单击行上通过添加以下 AlertController`RowSelected`方法：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

接下来，创建视图控制器的实例：

```csharp
HomeScreen owner;
```
将一个构造函数添加到你 UITableViewSource 类，它将作为参数的视图控制器，并将其保存在字段中：

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```
修改其中创建 UITableViewSource 类传递的 ViewDidLoad 方法`this`引用：

```csharp
table.Source = new TableSource(tableItems, this);
```
最后，签入你`RowSelected`方法中，调用`PresentViewController`上缓存的字段：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```


现在用户可以触摸行并将会出现警告：



 [ ![](populating-a-table-with-data-images/image4.png "行所选的警报")](populating-a-table-with-data-images/image4.png)


## <a name="cell-reuse"></a>单元格重用

在此示例中有仅六个项，因此不没有所需的任何单元格重用。 在显示时数百或数千个行，但是，它将会浪费的内存来创建数百或数千个`UITableViewCell`对象时一次只有几适合于屏幕。

若要避免这种情况下，当单元格从其视图放置在队列中的重复使用屏幕上消失。 当用户滚动时，表调用`GetCell`以请求新视图以显示 – 若要重用现有的单元格 （即当前未显示） 只需调用`DequeueReusableCell`方法。 如果可用以供重复使用它将返回的单元格，否则为将返回 null，并且你的代码必须创建新的单元格实例。

此代码段中的代码示例中演示的模式：

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

`cellIdentifier`能有效地创建单独的队列，为不同类型的单元格。 在此示例中的所有单元格查找相同以便只有一台硬编码使用标识符。 如果没有不同类型的单元格它们应每个具有不同的标识符字符串，当它们实例化和会重复使用队列在请求时。

### <a name="cell-reuse-in-ios-6"></a>在 iOS 6 + 中的单元格重用

iOS 6 添加类似于一个引入与集合视图的单元格重用模式。 尽管上面所示的现有重用模式仍支持向后兼容性，此新模式是更可取的如它无需对单元格的 null 检查。

与新模式应用程序注册的单元格类或 xib 用于通过调用`RegisterClassForCellReuse`或`RegisterNibForCellReuse`控制器的构造函数中。 然后，当取消排队单元格在`GetCell`方法，只需调用`DequeueReusableCell`传递注册单元格类或 xib 和索引路径的标识符。

例如，下面的代码在 UITableViewController 中注册的自定义单元格类：

```csharp
public class MyTableViewController : UITableViewController
{
  static NSString MyCellId = new NSString ("MyCellId");

  public MyTableViewController ()
  {
    TableView.RegisterClassForCellReuse (typeof(MyCell), MyCellId);
  }
  ...
}
```

与注册 MyCell 类，该单元格可以取消排队中`GetCell`方法`UITableViewSource`而无需额外 null 检查，如图所示下面：

```csharp
class MyTableSource : UITableViewSource
{
  public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
  {
    // if cell is not available in reuse pool, iOS will create one automatically
    // no need to do null check and create cell manually
    var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

    // do whatever you need to with cell, such as assigning properties, etc.

    return cell;
  }
}
```

请注意，在使用新的重复使用模式与自定义单元格类时，您需要实现构造函数采用`IntPtr`，如下面的代码段中所示，否则 Objective C 将无法构造单元格类的实例：

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

你可以看到在上文所述的主题的示例**BasicTable**示例链接到此文章。

<a name="Adding_an_Index" />

## <a name="adding-an-index"></a>添加索引

索引，从而帮助用户滚动较长的列表，通常，尽管你可以编制索引按字母顺序排列要按任何条件。 **BasicTableIndex**示例从要演示索引的文件加载较多长的项列表。 在索引中的每个项对应于表节。

 [ ![](populating-a-table-with-data-images/image5.png "索引显示")](populating-a-table-with-data-images/image5.png)

若要支持 sections 后面的表的数据需要进行分组，因此 BasicTableIndex 示例创建`Dictionary<>`从每个项的第一个字母用作字典键的字符串的数组：

```csharp
indexedTableItems = new Dictionary<string, List<string>>();
foreach (var t in items) {
    if (indexedTableItems.ContainsKey (t[0].ToString ())) {
        indexedTableItems[t[0].ToString ()].Add(t);
    } else {
        indexedTableItems.Add (t[0].ToString (), new List<string>() {t});
    }
}
keys = indexedTableItems.Keys.ToArray ();
```

`UITableViewSource`子类然后需要以下方法添加或修改，以便使用`Dictionary<>`:

-   **NumberOfSections** – 此方法是可选的默认情况下此表假定一个部分。 显示索引时此方法应返回索引 (例如，26 如果索引包含英语字母表中的所有字母) 中的项的数目。
-   **RowsInSection** – 给定的部分中返回的行数。
-   **SectionIndexTitles** – 返回的将用来显示索引的字符串数组。 示例代码返回字母的数组。


示例文件中的更新的方法**BasicTableIndex/TableSource.cs**如下所示：

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return keys.Length;
}
public override nint RowsInSection (UITableView tableview, nint section)
{
    return indexedTableItems[keys[section]].Count;
}
public override string[] SectionIndexTitles (UITableView tableView)
{
    return keys;
}
```

索引仅通常用于普通表样式。


<a name="Adding_Headers_and_Footers" />

## <a name="adding-headers-and-footers"></a>添加页眉和页脚

页眉和页脚可以用于以可视方式组表中的行。 所需的数据结构是非常类似于添加索引 –`Dictionary<>`的确非常有效。 除了使用字母表组单元格，此示例将一组由 botanical 类型蔬菜。
输出如下所示：

 [ ![](populating-a-table-with-data-images/image6.png "示例页眉和页脚")](populating-a-table-with-data-images/image6.png)

若要显示页眉和页脚`UITableViewSource`子类需要这些附加方法：

-   **TitleForHeader** – 返回要用作标头的文本
-   **TitleForFooter** – 返回要用作页脚的文本。


示例文件中的更新的方法**BasicTableHeaderFooter/Code/TableSource.cs**如下所示：

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    return keys[section];
}
public override string TitleForFooter (UITableView tableView, nint section)
{
    return indexedTableItems[keys[section]].Count + " items";
}
```

你可以进一步自定义页眉和页脚的视图的外观对象、 使用`GetViewForHeader`和`GetViewForFooter`方法重写上`UITableViewSource`。


## <a name="related-links"></a>相关链接

- [WorkingWithTables （示例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
