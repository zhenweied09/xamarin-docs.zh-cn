---
title: 拖放
description: 实现拖放适用于 iOS 11
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/05/2016
ms.openlocfilehash: fa6fcb2c4f5f17011307b31e4644889d066b71a9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="drag-and-drop"></a>拖放

_实现拖放适用于 iOS 11_

iOS 11 包括拖放支持在 iPad 上的应用程序之间复制数据和。 用户可以选择并拖动所有类型的内容，应用定位并排，或者通过拖动将在应用程序图标就会触发此应用以打开并允许数据被删除：

![从拖放示例自定义应用到说明应用程序](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> 拖放是仅在 iPhone 上的相同应用程序内可用。

和请考虑支持拖放的操作任何位置可以创建或编辑内容：

- 文本控件支持拖放对于生成针对 iOS 11 中，而无需任何其他工作的所有应用程序。
- 表视图和集合视图在 iOS 11，简化添加拖放行为中包括增强功能。
- 可以进行任何其他视图，来支持拖动和删除与其他自定义项。

当添加的拖放支持向你的应用时，你可以提供不同级别的内容的保真度;例如，你可能会提供的带格式的文本和纯文本版本的数据，以便接收应用程序可以选择最适合拖动目标上的。 还有可能能够自定义拖可视化效果，并还能够启用在一次拖动多个项。

## <a name="drag-and-drop-with-text-controls"></a>使用文本控件拖放

`UITextView` 和`UITextField`自动支持所选的文本输出，拖放文本中的内容。

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>拖放与 UITableView

`UITableView` 具有内置的处理拖放与表行，要求只有几个方法，以使该默认行为的交互。

有所涉及的两个接口：

- `IUITableViewDragDelegate` – 当在表视图中启动拖时包信息。
- `IUITableViewDropDelegate` – 处理信息时禁止删除尝试和完成。

在[DragAndDropTableView 示例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)这些两个接口的实现上`UITableViewController`类，以及委托和数据源。 它们分配在`ViewDidLoad`方法：

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

这两个接口所需的最小代码方法说明如下。

### <a name="table-view-drag-delegate"></a>表视图拖动委托

唯一方法_必需_为了支持在表视图中拖动行是`GetItemsForBeginningDragSession`。 如果用户开始拖动某一行，将调用此方法。

实现如下所示。 它检索拖动行与关联的数据、 进行编码，并配置`NSItemProvider`用于确定应用程序将如何处理该操作的"放置"部分 (例如，是否它们可以处理的数据类型， `PlainText`，在示例中):

```csharp
public UIDragItem[] GetItemsForBeginningDragSession (UITableView tableView,
  IUIDragSession session, NSIndexPath indexPath)
{
  // gets the 'information' to be dragged
  var placeName = model.PlaceNames[indexPath.Row];
  // convert to NSData representation
  var data = NSData.FromString(placeName, NSStringEncoding.UTF8);
  // create an NSItemProvider to describe the data
  var itemProvider = new NSItemProvider();
  itemProvider.RegisterDataRepresentation(UTType.PlainText,
                                NSItemProviderRepresentationVisibility.All,
                                (completion) =>
  {
    completion(data, null);
    return null;
  });
  // wrap in a UIDragItem
  return new UIDragItem[] { new UIDragItem(itemProvider) };
}
```

可以实现自定义的拖动行为，如提供多个数据表示形式，可以在目标应用程序中利用执行哪些拖委托上有许多可选方法 (如格式化文本作为以及纯文本或向量和位图版本中的图形）。 你还可以提供自定义数据表示形式时中拖放在相同的应用内使用。

### <a name="table-view-drop-delegate"></a>表视图放委托

拖动操作发生在表视图中，或其上完成时调用放委托的方法。 所需的方法确定是否允许数据被删除，并且如果在放置操作完成执行哪些操作：

- `CanHandleDropSession` – 虽然拖动正在进行，并且可能会丢弃该应用程序，此方法可确定是否允许要拖动的数据会删除。
- `DropSessionDidUpdate` – 拖动正在进行时，此方法被调用以确定应采取的操作。 从拖到表视图、 拖动会话中，以及可能的索引路径的信息所有可用来确定的行为和向用户提供的视觉反馈。
- `PerformDrop` – 当用户完成下拉 （通过提起其手指），此方法提取正在拖动的数据，并修改该表格视图以将数据添加在新行 （或行）。

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` 指示表视图是否可以接受要拖动的数据。 在此代码段中，`CanLoadObjects`用于确认此表视图可以接受字符串数据。

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

`DropSessionDidUpdate`拖放操作正在进行，以向用户提供可视提示时重复调用方法。

在下面的代码，`HasActiveDrag`用于确定是否当前的表视图中产生该操作。 如果是这样，只有单个行允许移动。
如果拖动是来自另一个源，将指示复制操作：

```csharp
public UITableViewDropProposal DropSessionDidUpdate(UITableView tableView, IUIDropSession session, NSIndexPath destinationIndexPath)
{
  // The UIDropOperation.Move operation is available only for dragging within a single app.
  if (tableView.HasActiveDrag)
  {
    if (session.Items.Length > 1)
    {
        return new UITableViewDropProposal(UIDropOperation.Cancel);
    } else {
        return new UITableViewDropProposal(UIDropOperation.Move, UITableViewDropIntent.InsertAtDestinationIndexPath);
    }
  } else {
    return new UITableViewDropProposal(UIDropOperation.Copy, UITableViewDropIntent.InsertAtDestinationIndexPath);
  }
}
```

拖放操作可以是之一`Cancel`， `Move`，或`Copy`。

拖放意向可以以插入新行，或添加/追加到现有行的数据。

#### <a name="performdrop"></a>PerformDrop

`PerformDrop`用户完成此操作，并且修改以反映放置的数据的表视图和数据源时调用方法。

```csharp
public void PerformDrop(UITableView tableView, IUITableViewDropCoordinator coordinator)
{
  NSIndexPath indexPath, destinationIndexPath;
  if (coordinator.DestinationIndexPath != null)
  {
    indexPath = coordinator.DestinationIndexPath;
    destinationIndexPath = indexPath;
  }
  else
  {
    // Get last index path of table view
    var section = tableView.NumberOfSections() - 1;
    var row = tableView.NumberOfRowsInSection(section);
    destinationIndexPath = NSIndexPath.FromRowSection(row, section);
  }
  coordinator.Session.LoadObjects(typeof(NSString), (items) =>
  {
    // Consume drag items
    List<string> stringItems = new List<string>();
    foreach (var i in items)
    {
      var q = NSString.FromHandle(i.Handle);
      stringItems.Add(q.ToString());
    }
    var indexPaths = new List<NSIndexPath>();
    for (var j = 0; j < stringItems.Count; j++)
    {
      var indexPath1 = NSIndexPath.FromRowSection(destinationIndexPath.Row + j, destinationIndexPath.Section);
      model.AddItem(stringItems[j], indexPath1.Row);
      indexPaths.Add(indexPath1);
    }
    tableView.InsertRows(indexPaths.ToArray(), UITableViewRowAnimation.Automatic);
  });
}
```

可以添加附加代码来以异步方式加载大型数据的对象。

### <a name="testing-drag-and-drop"></a>测试的拖放

你必须使用 iPad 测试[示例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)。
打开与另一个应用程序 （例如注释） 一起示例并将行和文本拖动它们之间：

![正在进行拖动操作的屏幕截图](drag-and-drop-images/01-sml.png)


## <a name="related-links"></a>相关链接

- [拖放人机接口指南 (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [拖放表视图示例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)
- [拖放集合视图示例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
- [引入拖放 (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/203/)
- [使用集合和表视图 (WWDC) （视频） 拖放](https://developer.apple.com/videos/play/wwdc2017/223/)
