---
title: "源列表"
description: "本文介绍如何使用 Xamarin.Mac 应用程序中的源列表。 它介绍创建和维护 Xcode 和接口生成器中的源列表和 C# 代码中与它们进行交互。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 1cc74fb30e59ecd5f6be3cf3e1c84f60cd5ca0a6
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="source-lists"></a>源列表

_本文介绍如何使用 Xamarin.Mac 应用程序中的源列表。它介绍创建和维护 Xcode 和接口生成器中的源列表和 C# 代码中与它们进行交互。_

在使用 C# 和.NET Xamarin.Mac 应用程序中，你有权访问相同源列出的开发人员使用*Objective C*和*Xcode*未。 因为 Xamarin.Mac 与 Xcode 直接集成，你可以使用 Xcode 的_接口生成器_来创建和维护您的源列表 （或根据需要在 C# 代码中直接创建它们）。

源列表是操作的用于显示，如在查找工具或 iTunes 侧栏的源的大纲视图的特殊类型。

[ ![](source-list-images/source05.png "示例源列表")](source-list-images/source05.png)

在本文中，我们将介绍使用源列表 Xamarin.Mac 应用程序中的基础知识。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在本文中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于网络-最多 C# 类 OBJECTIVE-C 的对象和 UI 元素。

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-source-lists"></a>源列表简介

如上面所述，源列表是操作的一种特殊类型的大纲视图用于显示，如在查找工具或 iTunes 侧栏的源。 源列表是表的一种允许用户类型展开或折叠的分层数据行。 与不同的表视图中，源列表中的项不在平面列表中，其组织的层次结构，如文件和在硬盘上的文件夹中。 如果源列表中的项包含其他项，其可展开或折叠的用户。

源列表是专门样式的大纲视图 (`NSOutlineView`)，后者本身是位于表视图的子类 (`NSTableView`)，并在这种情况下，从其父类继承其大部分行为。 因此，许多操作支持的大纲视图中，也支持源列表。 Xamarin.Mac 应用程序具有这些功能的控制权，可以配置源列表的参数 （无论是在代码还是接口生成器） 以允许或禁止某些操作。

源列表不会存储它自己的数据，而是依赖数据源 (`NSOutlineViewDataSource`) 提供的行和所需，根据需要逐列。

可以通过提供的子类大纲视图委托的自定义源列表的行为 (`NSOutlineViewDelegate`) 若要支持大纲类型以选择功能，项选择和编辑、 自定义跟踪和中的单个项目的自定义视图。

由于源列表共享大部分它的行为和功能的表视图，大纲视图，你可能希望通过我们[表视图](~/mac/user-interface/table-view.md)和[大纲视图](~/mac/user-interface/outline-view.md)文档，然后再继续此文章。

<a name="Working_with_Source_Lists" />

## <a name="working-with-source-lists"></a>使用源列表

源列表是操作的用于显示，如在查找工具或 iTunes 侧栏的源的大纲视图的特殊类型。 大纲视图与我们在接口生成器中定义了我们的源列表之前，让我们创建后备类在 Xamarin.Mac 中。

首先，让我们创建一个新`SourceListItem`类以保存数据源列表。 在**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...**选择**常规** > **空类**，输入`SourceListItem`为**名称**单击**新建**按钮：

[ ![](source-list-images/source01.png "添加空类")](source-list-images/source01.png)

请`SourceListItem.cs`文件外观如下所示： 

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListItem: NSObject, IEnumerator, IEnumerable
    {
        #region Private Properties
        private string _title;
        private NSImage _icon;
        private string _tag;
        private List<SourceListItem> _items = new List<SourceListItem> ();
        #endregion

        #region Computed Properties
        public string Title {
            get { return _title; }
            set { _title = value; }
        }

        public NSImage Icon {
            get { return _icon; }
            set { _icon = value; }
        }

        public string Tag {
            get { return _tag; }
            set { _tag=value; }
        }
        #endregion

        #region Indexer
        public SourceListItem this[int index]
        {
            get
            {
                return _items[index];
            }

            set
            {
                _items[index] = value;
            }
        }

        public int Count {
            get { return _items.Count; }
        }

        public bool HasChildren {
            get { return (Count > 0); }
        }
        #endregion

        #region Enumerable Routines
        private int _position = -1;

        public IEnumerator GetEnumerator()
        {
            _position = -1;
            return (IEnumerator)this;
        }

        public bool MoveNext()
        {
            _position++;
            return (_position < _items.Count);
        }

        public void Reset()
        {_position = -1;}

        public object Current
        {
            get 
            { 
                try 
                {
                    return _items[_position];
                }

                catch (IndexOutOfRangeException)
                {
                    throw new InvalidOperationException();
                }
            }
        }
        #endregion

        #region Constructors
        public SourceListItem ()
        {
        }

        public SourceListItem (string title)
        {
            // Initialize
            this._title = title;
        }

        public SourceListItem (string title, string icon)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
        }

        public SourceListItem (string title, string icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
        }

        public SourceListItem (string title, NSImage icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon, string tag)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
        }

        public SourceListItem (string title, NSImage icon, string tag, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
            this.Clicked = clicked;
        }
        #endregion

        #region Public Methods
        public void AddItem(SourceListItem item) {
            _items.Add (item);
        }

        public void AddItem(string title) {
            _items.Add (new SourceListItem (title));
        }

        public void AddItem(string title, string icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, string icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, NSImage icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon, string tag) {
            _items.Add (new SourceListItem (title, icon, tag));
        }

        public void AddItem(string title, NSImage icon, string tag, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, tag, clicked));
        }

        public void Insert(int n, SourceListItem item) {
            _items.Insert (n, item);
        }

        public void RemoveItem(SourceListItem item) {
            _items.Remove (item);
        }

        public void RemoveItem(int n) {
            _items.RemoveAt (n);
        }

        public void Clear() {
            _items.Clear ();
        }
        #endregion

        #region Events
        public delegate void ClickedDelegate();
        public event ClickedDelegate Clicked;

        internal void RaiseClickedEvent() {
            // Inform caller
            if (this.Clicked != null)
                this.Clicked ();
        }
        #endregion
    }
}
```

在**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...**选择**常规** > **空类**，输入`SourceListDataSource`为**名称**单击**新建**按钮。 请`SourceListDataSource.cs`文件外观如下所示：

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDataSource : NSOutlineViewDataSource
    {
        #region Private Variables
        private SourceListView _controller;
        #endregion

        #region Public Variables
        public List<SourceListItem> Items = new List<SourceListItem>();
        #endregion

        #region Constructors
        public SourceListDataSource (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Properties
        public override nint GetChildrenCount (NSOutlineView outlineView, Foundation.NSObject item)
        {
            if (item == null) {
                return Items.Count;
            } else {
                return ((SourceListItem)item).Count;
            }
        }

        public override bool ItemExpandable (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, Foundation.NSObject item)
        {
            if (item == null) {
                return Items [(int)childIndex];
            } else {
                return ((SourceListItem)item) [(int)childIndex]; 
            }
        }

        public override NSObject GetObjectValue (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            return new NSString (((SourceListItem)item).Title);
        }
        #endregion

        #region Internal Methods
        internal SourceListItem ItemForRow(int row) {
            int index = 0;

            // Look at each group
            foreach (SourceListItem item in Items) {
                // Is the row inside this group?
                if (row >= index && row <= (index + item.Count)) {
                    return item [row - index - 1];
                }

                // Move index
                index += item.Count + 1;
            }

            // Not found 
            return null;
        }
        #endregion
    }
}
```

这将为我们的源列表中提供数据。

在**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...**选择**常规** > **空类**，输入`SourceListDelegate`为**名称**单击**新建**按钮。 请`SourceListDelegate.cs`文件外观如下所示：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDelegate : NSOutlineViewDelegate
    {
        #region Private variables
        private SourceListView _controller;
        #endregion

        #region Constructors
        public SourceListDelegate (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Methods
        public override bool ShouldEditTableColumn (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            return false;
        }

        public override NSCell GetCell (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            nint row = outlineView.RowForItem (item);
            return tableColumn.DataCellForRow (row);
        }

        public override bool IsGroupItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            NSTableCellView view = null;

            // Is this a group item?
            if (((SourceListItem)item).HasChildren) {
                view = (NSTableCellView)outlineView.MakeView ("HeaderCell", this);
            } else {
                view = (NSTableCellView)outlineView.MakeView ("DataCell", this);
                view.ImageView.Image = ((SourceListItem)item).Icon;
            }

            // Initialize view
            view.TextField.StringValue = ((SourceListItem)item).Title;

            // Return new view
            return view;
        }

        public override bool ShouldSelectItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return (outlineView.GetParent (item) != null);
        }

        public override void SelectionDidChange (NSNotification notification)
        {
            NSIndexSet selectedIndexes = _controller.SelectedRows;

            // More than one item selected?
            if (selectedIndexes.Count > 1) {
                // Not handling this case
            } else {
                // Grab the item
                var item = _controller.Data.ItemForRow ((int)selectedIndexes.FirstIndex);

                // Was an item found?
                if (item != null) {
                    // Fire the clicked event for the item
                    item.RaiseClickedEvent ();

                    // Inform caller of selection
                    _controller.RaiseItemSelected (item);
                }
            }
        }
        #endregion
    }
}
```

这将提供我们的源列表的行为。

最后，在**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...**选择**常规** > **空类**，输入`SourceListView`为**名称**单击**新建**按钮。 请`SourceListView.cs`文件外观如下所示：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }
        
        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

这将创建的自定义、 可重复使用子类`NSOutlineView`(`SourceListView`)，我们可以使用任何 Xamarin.Mac 应用程序中，我们使驱动器源列表。

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode" />

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>创建和维护在 Xcode 中的源列表

现在，让我们设计界面生成器中我们源列表。 双击`Main.storyboard`文件以打开进行编辑接口生成器中并拖动拆分视图从**库检查器**、 将其添加到视图控制器和将其设置为与视图中调整大小**约束编辑器**:

[ ![](source-list-images/source00.png "编辑约束")](source-list-images/source00.png)

接下来，将从源列表拖**库检查器**、 将其添加到左侧的拆分视图和将其设置为与视图中调整大小**约束编辑器**:

[ ![](source-list-images/source02.png "编辑约束")](source-list-images/source02.png)

接下来，切换到**标识视图**，选择源列表中，并更改它的**类**到`SourceListView`:

[ ![](source-list-images/source03.png "设置的类名称")](source-list-images/source03.png)

最后，创建**Outlet**为名为我们的源列表`SourceList`中`ViewController.h`文件：

[ ![](source-list-images/source04.png "配置上的电源插座")](source-list-images/source04.png)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

<a name="Populating the Source List" />

## <a name="populating-the-source-list"></a>填充源列表

让我们编辑`RotationWindow.cs`Mac 的 Visual Studio 中的文件并使其的`AwakeFromNib`方法外观如下所示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Populate source list
    SourceList.Initialize ();

    var library = new SourceListItem ("Library");
    library.AddItem ("Venues", "house.png", () => {
        Console.WriteLine("Venue Selected");
    });
    library.AddItem ("Singers", "group.png");
    library.AddItem ("Genre", "cards.png");
    library.AddItem ("Publishers", "box.png");
    library.AddItem ("Artist", "person.png");
    library.AddItem ("Music", "album.png");
    SourceList.AddItem (library);

    // Add Rotation 
    var rotation = new SourceListItem ("Rotation"); 
    rotation.AddItem ("View Rotation", "redo.png");
    SourceList.AddItem (rotation);

    // Add Kiosks
    var kiosks = new SourceListItem ("Kiosks");
    kiosks.AddItem ("Sign-in Station 1", "imac");
    kiosks.AddItem ("Sign-in Station 2", "ipad");
    SourceList.AddItem (kiosks);

    // Display side list
    SourceList.ReloadData ();
    SourceList.ExpandItem (null, true);

}
```

`Initialize ()`方法需要针对我们的源列表调用**Outlet** _之前_任何项添加到它。 每个组的项，我们将创建一个父项，然后向该组项中添加子项。 然后添加到源列表的集合中每个组`SourceList.AddItem (...)`。 最后两个行的源列表中加载数据，并展开所有组：

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

最后，编辑`AppDelegate.cs`文件并进行`DidFinishLaunching`方法外观如下所示：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

如果我们运行我们的应用程序时，以下将显示：

[ ![](source-list-images/source05.png "运行示例应用程序")](source-list-images/source05.png)

<a name="Summary" />

## <a name="summary"></a>摘要

本文已详细的介绍了在使用源列表 Xamarin.Mac 应用程序中。 我们已了解如何创建和维护在 Xcode 的接口生成器中列出的源以及如何在 C# 代码中使用源列出。

## <a name="related-links"></a>相关链接

- [MacOutlines （示例）](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [表视图](~/mac/user-interface/table-view.md)
- [大纲视图](~/mac/user-interface/outline-view.md)
- [OS X 用户界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [介绍概述视图](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
