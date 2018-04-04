---
title: 大纲视图
description: 本文介绍如何使用 Xamarin.Mac 应用程序中的大纲视图。 它介绍创建和维护 Xcode 和接口生成器中的大纲视图和以编程方式使用它们。
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 82cb3afadf7615fdd92476371e9ab80cd1228b02
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="outline-views"></a>大纲视图

_本文介绍如何使用 Xamarin.Mac 应用程序中的大纲视图。它介绍创建和维护 Xcode 和接口生成器中的大纲视图和以编程方式使用它们。_

在使用 C# 和.NET Xamarin.Mac 应用程序中，你有权访问相同大纲视图，开发人员使用*Objective C*和*Xcode*未。 因为 Xamarin.Mac 与 Xcode 直接集成，你可以使用 Xcode 的_接口生成器_来创建和维护大纲视图 （或根据需要在 C# 代码中直接创建它们）。

大纲视图是表的一种允许用户类型展开或折叠的分层数据行。 表视图类似，大纲视图与表示各个项和表示的那些项目的属性的列的行中显示一组相关项的数据。 与不同的表视图中，大纲视图中的项不在平面列表中，其组织的层次结构，如文件和在硬盘上的文件夹中。

[![](outline-view-images/populate03.png "运行示例应用程序")](outline-view-images/populate03.png#lightbox)

在本文中，我们将介绍使用 Xamarin.Mac 应用程序中的大纲视图的基础知识。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在本文中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于网络-最多 C# 类 OBJECTIVE-C 的对象和 UI 元素。

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>介绍概述视图

大纲视图是表的一种允许用户类型展开或折叠的分层数据行。 表视图类似，大纲视图与表示各个项和表示的那些项目的属性的列的行中显示一组相关项的数据。 与不同的表视图中，大纲视图中的项不在平面列表中，其组织的层次结构，如文件和在硬盘上的文件夹中。

如果大纲视图中的项包含其他项，它可以展开或折叠的用户。 可展开的项目将显示一个明显的三角形，用于指向右侧，当项处于折叠状态，并展开项时指向下。 单击泄露三角形会导致要展开或折叠的项。

大纲视图 (`NSOutlineView`) 是位于表视图的子类 (`NSTableView`)，并在这种情况下，从其父类继承其大部分行为。 因此，大纲视图也支持所支持的表视图中，选择行或列，例如通过拖动列标题，等等，调整列的许多操作。 Xamarin.Mac 应用程序具有这些功能的控制权，可以配置该大纲视图的参数 （无论是在代码还是接口生成器） 以允许或禁止某些操作。

大纲视图不会存储它自己的数据，而是依赖数据源 (`NSOutlineViewDataSource`) 提供的行和所需，根据需要逐列。

可以通过提供的子类大纲视图委托的自定义大纲视图的行为 (`NSOutlineViewDelegate`) 若要支持大纲列管理，键入选择功能、 行选择，编辑、 自定义跟踪和为各个自定义视图列和行。

由于与表视图，大纲视图共享大部分它的行为和功能，你可能希望通过我们[表视图](~/mac/user-interface/table-view.md)文档，然后再继续执行本文。

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>创建和维护在 Xcode 中的大纲视图

时创建新的 Xamarin.Mac Cocoa 应用程序时，默认情况下将获得标准保留为空，窗口。 在中定义此 windows`.storyboard`自动包括在项目中的文件。 若要编辑您的 windows 设计、 在**解决方案资源管理器**，双击`Main.storyboard`文件：

[![](outline-view-images/edit01.png "选择主情节提要")](outline-view-images/edit01.png#lightbox)

这将在 Xcode 的接口生成器中打开的窗口设计：

[![](outline-view-images/edit02.png "编辑在 Xcode 中 UI")](outline-view-images/edit02.png#lightbox)

类型`outline`到**库 Inspector 的**搜索框以使其更轻松地找到大纲视图控件：

[![](outline-view-images/edit03.png "从库中选择的大纲视图")](outline-view-images/edit03.png#lightbox)

大纲视图拖到中的视图控制器**界面编辑器**，使其填写的内容区域的视图控制器，并将其设置为其中缩小和随着中的窗口**约束编辑器**:

[![](outline-view-images/edit04.png "编辑约束")](outline-view-images/edit04.png#lightbox)

选择中的大纲视图**接口层次结构**和以下属性位于**属性检查器**:

[![](outline-view-images/edit05.png "属性检查器")](outline-view-images/edit05.png#lightbox)

- **概述列**的表列中显示分层数据。
- **自动保存大纲列**-如果`true`，大纲列将自动保存和还原应用程序运行之间。
- **缩进**-要缩进下已展开的项目列的量。
- **缩进遵循单元格**-如果`true`，缩进标记将缩进以及单元格。
- **自动保存展开项**-如果`true`，将自动保存和还原应用程序运行之间的项的展开/折叠状态。
- **内容模式**-允许你使用任一视图 (`NSView`) 或单元格 (`NSCell`) 若要显示的行和列中的数据。 从开始 macOS 10.7，应使用视图。
- **浮动对行进行分组**-如果`true`，表视图将绘制分组单元格，就像它们浮动。
- **列**-定义显示的列数。
- **标头**-如果`true`，这些列将具有标题。
- **重新排序**-如果`true`，用户将能够将对表中的列重新排序。
- **调整大小**-如果`true`，用户将能够拖动列标题来调整列的大小。
- **调整列大小**-控制如何表将自动调整列的大小。
- **突出显示**-选择单元格时，将使用的突出显示的表类型的控件。
- **备用行**-如果`true`、 曾经其他行，将有不同的背景色。
- **水平网格**-选择的水平绘制单元格之间的边框类型。
- **垂直网格**-选择的垂直绘制的单元格之间的边框类型。
- **网格颜色**-设置单元格边框颜色。
- **后台**-设置单元格背景色。
- **选择**-可用于控制如何，用户可以在与表中选择单元格：
    - **多个**-如果`true`，用户可以选择多个行和列。
    - **列**-如果`true`，用户可以选择列。
    - **键入 Select** -如果`true`，用户可以键入要选择的行的字符。
    - **空**-如果`true`，用户无需选择行或列，表允许未选择任何内容根本。
- **自动保存**-下保存表格式是自动的名称。
- **列信息**-如果`true`的顺序和列的宽度将自动保存。
- **分行符**-选择单元格处理分行符的方式。
- **将截断最后一个可见行**-如果`true`，该单元格将被截断数据能否不适合它的边界内。

> [!IMPORTANT]
> 除非你要保留旧 Xamarin.Mac 应用程序，`NSView`应通过使用基于的大纲视图`NSCell`基于表视图。 `NSCell` 被视为旧和可能不支持今后。

选择中的表列**接口层次结构**和以下属性位于**属性检查器**:

[![](outline-view-images/edit06.png "属性检查器")](outline-view-images/edit06.png#lightbox)

- **标题**-设置列的标题。
- **对齐**-在单元格内设置文本的对齐方式。
- **标题字体**-选择单元格的标头文本的字体。
- **排序关键字**-是用于对列中的数据进行排序的键。 将用户无法对此列进行排序的情况下留空。
- **选择器**-是**操作**用于执行排序。 将用户无法对此列进行排序的情况下留空。
- **顺序**-为列数据的排序顺序。
- **调整大小**-选择的调整大小的列类型。
- **可编辑**-如果`true`，用户可以编辑单元格基于表的单元格。
- **隐藏**-如果`true`，隐藏列。

你可以通过它的句柄 （垂直居中右侧列的） 左或向右拖动来调整列的大小。

让我们在我们的表视图中选择每个列，并为第一列**标题**的`Product`和第二个`Details`。

选择一个表格单元格视图 (`NSTableViewCell`) 中**接口层次结构**和以下属性位于**属性检查器**:

[![](outline-view-images/edit07.png "属性检查器")](outline-view-images/edit07.png#lightbox)

这些是所有标准视图的属性。 此外可以调整此列此处行的大小。

选择表视图单元格 (默认情况下，这是`NSTextField`) 中**接口层次结构**和以下属性位于**属性检查器**:

[![](outline-view-images/edit08.png "属性检查器")](outline-view-images/edit08.png#lightbox)

您必须要在此处设置的标准文本字段的所有属性。 默认情况下，标准的文本字段用于显示列中的某个单元格数据。

选择一个表格单元格视图 (`NSTableFieldCell`) 中**接口层次结构**和以下属性位于**属性检查器**:

[![](outline-view-images/edit09.png "属性检查器")](outline-view-images/edit09.png#lightbox)

最重要的设置如下：

- **布局**-选择此列中的单元格的布局方式如何。
- **使用单个行模式**-如果`true`，该单元格是否限制为单个行。
- **第一个运行时布局宽度**-如果`true`，该单元格将首选宽度设置为其 （手动或自动） 时它将显示第一次运行应用程序。
- **操作**-控制编辑**操作**发送单元格。
- **行为**-定义单元格是否可选择或可编辑。
- **格式文本**-如果`true`，该单元格可以显示格式和样式的文本。
- **撤消**-如果`true`，对它的单元格不承担责任撤消行为。

选择表单元格视图 (`NSTableFieldCell`) 中的表列底部**接口层次结构**:

[![](outline-view-images/edit11.png "选择表单元格视图")](outline-view-images/edit10.png#lightbox)

这使您可以编辑作为的基础的表单元格视图_模式_为给定的列创建的所有单元格。

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>添加操作和容器

只需像任何其他 Cocoa UI 控件，我们需要公开我们大纲视图并且会列和 C# 代码使用单元格**操作**和**Outlet** （基于所需的功能）。

该过程是我们想要公开任何大纲视图元素相同的：

1. 切换到**助手编辑器中**并确保`ViewController.h`选择文件： 

    [![](outline-view-images/edit11.png "选择正确的.h 文件")](outline-view-images/edit11.png#lightbox)
2. 选择从该大纲视图**接口层次结构**、 单击和拖动到`ViewController.h`文件。
3. 创建**Outlet**大纲视图中调用的`ProductOutline`: 

    [![](outline-view-images/edit13.png "配置上的电源插座")](outline-view-images/edit13.png#lightbox)
4. 创建**Outlet**对于表列也被称为`ProductColumn`和`DetailsColumn`: 

    [![](outline-view-images/edit14.png "配置上的电源插座")](outline-view-images/edit14.png#lightbox)
5. 保存更改并返回到 Visual Studio for Mac 与 Xcode 同步。

接下来，我们将编写的代码显示概要中的一些数据时运行该应用程序。

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>填充该大纲视图

与我们的大纲视图在接口生成器中设计和通过公开**Outlet**，接下来我们需要创建的 C# 代码来填充它。

首先，让我们创建一个新`Product`类来保存单个行和组的子产品的信息。 在**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...**选择**常规** > **空类**，输入`Product`为**名称**单击**新建**按钮：

[![](outline-view-images/populate01.png "创建一个空的类")](outline-view-images/populate01.png#lightbox)

请`Product.cs`文件外观如下所示：

```csharp
using System;
using Foundation;
using System.Collections.Generic;

namespace MacOutlines
{
    public class Product : NSObject
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Computed Properties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        public bool IsProductGroup {
            get { return (Products.Count > 0); }
        }
        #endregion

        #region Constructors
        public Product ()
        {
        }

        public Product (string title, string description)
        {
            this.Title = title;
            this.Description = description;
        }
        #endregion
    }
}
```

接下来，我们需要创建的一个子类`NSOutlineDataSource`为所请求的我们大纲提供数据。 在**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...**选择**常规** > **空类**，输入`ProductOutlineDataSource`为**名称**单击**新建**按钮。

编辑`ProductTableDataSource.cs`文件并使其如下所示：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDataSource : NSOutlineViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductOutlineDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetChildrenCount (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products.Count;
            } else {
                return ((Product)item).Products.Count;
            }

        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, NSObject item)
        {
            if (item == null) {
                return Products [childIndex];
            } else {
                return ((Product)item).Products [childIndex];
            }
                
        }

        public override bool ItemExpandable (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products [0].IsProductGroup;
            } else {
                return ((Product)item).IsProductGroup;
            }
        
        }
        #endregion
    }
}
```

此类已存储为我们的大纲视图项和替代`GetChildrenCount`以返回表中的行数。 `GetChild` （如请求的大纲视图） 中返回一个特定的父级或子级项和`ItemExpandable`作为父或子元素定义指定的项。

最后，我们需要创建的一个子类`NSOutlineDelegate`以提供我们大纲的行为。 在**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...**选择**常规** > **空类**，输入`ProductOutlineDelegate`为**名称**单击**新建**按钮。

编辑`ProductOutlineDelegate.cs`文件并使其如下所示：

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDelegate : NSOutlineViewDelegate
    {
        #region Constants 
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductOutlineDataSource DataSource;
        #endregion

        #region Constructors
        public ProductOutlineDelegate (ProductOutlineDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)outlineView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Cast item
            var product = item as Product;

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = product.Title;
                break;
            case "Details":
                view.StringValue = product.Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

当我们创建的实例`ProductOutlineDelegate`，我们还传递的实例中`ProductOutlineDataSource`大纲提供数据。 `GetView`方法负责返回视图 （数据） 以显示给定列和行的单元格。 如果可能，将重用现有视图以显示该单元格，如果不是必须创建一个新视图。

若要填充大纲，让我们编辑`MainWindow.cs`文件并进行`AwakeFromNib`方法外观如下所示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create data source and populate
    var DataSource = new ProductOutlineDataSource ();

    var Vegetables = new Product ("Vegetables", "Greens and Other Produce");
    Vegetables.Products.Add (new Product ("Cabbage", "Brassica oleracea - Leaves, axillary buds, stems, flowerheads"));
    Vegetables.Products.Add (new Product ("Turnip", "Brassica rapa - Tubers, leaves"));
    Vegetables.Products.Add (new Product ("Radish", "Raphanus sativus - Roots, leaves, seed pods, seed oil, sprouting"));
    Vegetables.Products.Add (new Product ("Carrot", "Daucus carota - Root tubers"));
    DataSource.Products.Add (Vegetables);

    var Fruits = new Product ("Fruits", "Fruit is a part of a flowering plant that derives from specific tissues of the flower");
    Fruits.Products.Add (new Product ("Grape", "True Berry"));
    Fruits.Products.Add (new Product ("Cucumber", "Pepo"));
    Fruits.Products.Add (new Product ("Orange", "Hesperidium"));
    Fruits.Products.Add (new Product ("Blackberry", "Aggregate fruit"));
    DataSource.Products.Add (Fruits);

    var Meats = new Product ("Meats", "Lean Cuts");
    Meats.Products.Add (new Product ("Beef", "Cow"));
    Meats.Products.Add (new Product ("Pork", "Pig"));
    Meats.Products.Add (new Product ("Veal", "Young Cow"));
    DataSource.Products.Add (Meats);

    // Populate the outline
    ProductOutline.DataSource = DataSource;
    ProductOutline.Delegate = new ProductOutlineDelegate (DataSource);

}
```

如果我们运行应用程序时，显示以下信息：

[![](outline-view-images/populate02.png "在折叠的视图")](outline-view-images/populate02.png#lightbox)

如果我们展开大纲视图中的节点，它将查找如下所示：

[![](outline-view-images/populate03.png "已展开的视图")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>按列排序

让我们允许用户通过单击列标题在大纲中的数据进行排序。 首先，双击`Main.storyboard`文件以打开它以在接口生成器中编辑。 选择`Product`列中，输入`Title`为**排序键**，`compare:`为**选择器**和选择`Ascending`为**顺序**:

[![](outline-view-images/sort01.png "设置排序键的顺序")](outline-view-images/sort01.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

现在让我们编辑`ProductOutlineDataSource.cs`文件并添加以下方法：

```csharp
public void Sort(string key, bool ascending) {

    // Take action based on key
    switch (key) {
    case "Title":
        if (ascending) {
            Products.Sort ((x, y) => x.Title.CompareTo (y.Title));
        } else {
            Products.Sort ((x, y) => -1 * x.Title.CompareTo (y.Title));
        }
        break;
    }
}

public override void SortDescriptorsChanged (NSOutlineView outlineView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    outlineView.ReloadData ();
}
```

`Sort`方法使我们能够基于数据源中的数据排序给定`Product`类字段按升序或降序。 重写`SortDescriptorsChanged`每次使用单击列标题时，将调用方法。 将传递**密钥**我们在接口生成器和该列的排序顺序中设置的值。

如果我们运行该应用程序，并单击列标题中，将按该列排序行：

[![](outline-view-images/sort02.png "已排序的输出的示例")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>行选择

如果你想要允许用户选择单个行中，双击`Main.storyboard`文件以打开它以在接口生成器中编辑。 选择中的大纲视图**接口层次结构**并取消选中**多个**中的复选框**属性检查器**:

[![](outline-view-images/select01.png "属性检查器")](outline-view-images/select01.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。


接下来，编辑`ProductOutlineDelegate.cs`文件并添加以下方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

这将允许用户在该大纲视图中选择任何单个行。 返回`false`为`ShouldSelectItem`的任一项，你不希望用户能够选择或`false`对于每个项，如果你不希望用户能够选择任何项。

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>多个行选择

如果你想要允许用户选择多个行中，双击`Main.storyboard`文件以打开它以在接口生成器中编辑。 选择中的大纲视图**接口层次结构**并检查**多个**中的复选框**属性检查器**:

[![](outline-view-images/select02.png "属性检查器")](outline-view-images/select02.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。


接下来，编辑`ProductOutlineDelegate.cs`文件并添加以下方法：

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

这将允许用户在该大纲视图中选择任何单个行。 返回`false`为`ShouldSelectRow`的任一项，你不希望用户能够选择或`false`对于每个项，如果你不希望用户能够选择任何项。

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>要选择的行类型

如果你想要允许用户与选定的大纲视图键入字符，然后选择第一行具有该字符，则双击`Main.storyboard`文件以打开它以在接口生成器中编辑。 选择中的大纲视图**接口层次结构**并检查**类型选择**中的复选框**属性检查器**:

[![](outline-view-images/type01.png "编辑的行类型")](outline-view-images/type01.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

现在让我们编辑`ProductOutlineDelegate.cs`文件并添加以下方法：

```csharp
public override NSObject GetNextTypeSelectMatch (NSOutlineView outlineView, NSObject startItem, NSObject endItem, string searchString)
{
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains (searchString)) {
            return product;
        }
    }

    // Not found
    return null;
}
```

`GetNextTypeSelectMatch`方法采用给定`searchString`并返回第一个项`Product`中它的包含该字符串`Title`。

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>对列重新排序

如果你想要允许用户将对在大纲视图中的列重新排序，请双击`Main.storyboard`文件以打开它以在接口生成器中编辑。 选择中的大纲视图**接口层次结构**并检查**Reordering**中的复选框**属性检查器**:

[![](outline-view-images/reorder01.png "属性检查器")](outline-view-images/reorder01.png#lightbox)

如果我们为提供的值**自动保存**属性并检查**列信息**字段中，我们对表的布局进行任何更改将自动为我们保存和还原应用程序的下一次运行。

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

现在让我们编辑`ProductOutlineDelegate.cs`文件并添加以下方法：

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

`ShouldReorder`方法应返回`true`它想要允许为任何列拖到重新排序`newColumnIndex`，否则返回`false`;

如果我们运行应用程序时，我们可以通过拖动围绕的列标题来对我们列重新排序：

[![](outline-view-images/reorder02.png "重新排序的列的示例")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>编辑单元格

如果你想要允许用户编辑给定的单元格的值，编辑`ProductOutlineDelegate.cs`文件并将更改`GetViewForItem`方法，如下所示：

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.StringValue;
            break;
        case "Details":
            prod.Description = view.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = product.Title;
        break;
    case "Details":
        view.StringValue = product.Description;
        break;
    }

    return view;
}
```

现在如果我们运行应用程序时，用户可以编辑的单元格的表视图中：

[![](outline-view-images/editing01.png "下面举例说明编辑单元格")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>在大纲视图中使用图像

若要包含图像，作为一部分中的单元格`NSOutlineView`，你将需要更改该大纲视图返回数据的方式`NSTableViewDelegate's``GetView`方法来使用`NSTableCellView`而不是典型`NSTextField`。 例如：

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

有关详细信息，请参阅[与大纲视图中使用映像](~/mac/app-fundamentals/image.md)一部分我们[使用映像](~/mac/app-fundamentals/image.md)文档。

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>数据绑定大纲视图

通过使用 Xamarin.Mac 应用程序中的键值对的编码和数据绑定技术，你可以显著缩短你需要编写和维护填充和使用 UI 元素的代码量。 你还可以进一步分离你的备份数据的好处 (_数据模型_) 从你前面结束用户界面 (_模型-视图-控制器_)，从而导致易于维护更灵活的应用程序设计。

键值对的编码 (KVC) 是用于间接访问的对象的属性，请使用键 （特殊格式字符串） 来标识而不是通过实例变量访问这些属性或访问器方法的机制 (`get/set`)。 通过实现 Xamarin.Mac 应用程序中的键值对的编码符合访问器，你可以访问键 / 值观察 (KVO)、 数据绑定、 核心数据、 Cocoa 绑定和 scriptability 等其他 macOS 功能。

有关详细信息，请参阅[大纲视图数据绑定](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding)一部分我们[数据绑定和键 / 值编码](~/mac/app-fundamentals/databinding.md)文档。

<a name="Summary" />

## <a name="summary"></a>总结

本文已了解使用 Xamarin.Mac 应用程序中的大纲视图的详细的信息。 我们看到不同的类型，并使用的大纲视图、 如何创建和维护在 Xcode 的接口生成器中的大纲视图以及如何在 C# 代码中使用大纲视图。

## <a name="related-links"></a>相关链接

- [MacOutlines （示例）](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [MacImages（示例）](https://developer.xamarin.com/samples/mac/MacImages/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [表视图](~/mac/user-interface/table-view.md)
- [源列表](~/mac/user-interface/source-list.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [介绍概述视图](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
