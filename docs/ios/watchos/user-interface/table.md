---
title: "Table 控件"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 4887b9a42c5a855353b5a4e422559aafcdc68173
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="table-control"></a>Table 控件

WatchOS`WKInterfaceTable`控件比其 iOS 对应的方法要简单得多，但执行类似的角色。 它创建一个滚动列表的行，可以自定义布局，并且其响应触控事件。

![](table-images/table-list-sml.png "监视表列表") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>添加了一个表

拖动**表**到场景的控件。 默认情况下它将类似此 （显示未指定的单行布局）：

[ ![](table-images/add-table-sml.png "添加了一个表")](table-images/add-table.png)

为表的名称在**属性**填充的**名称**框中，以便可以在代码中引用。

## <a name="add-a-row-controller"></a>添加行控制器

该表自动包含单个行，表示由包含行控制器**组**默认情况下的控件。

若要设置**类**行控制器中，选择中的行**文档大纲**类中键入名称和**属性**填充：

[ ![](table-images/add-row-controller-sml.png "在属性键盘输入的类名称")](table-images/add-row-controller.png)

设置行的控制器类后，IDE 将在项目中创建相应的 C# 文件。 拖到行上的控件 （如标签），并给予他们名称以便它们可以在代码中引用。




## <a name="create-and-populate-rows"></a>创建并填充行

`SetNumberOfRows` 创建了行控制器类为每个行，使用`Identifier`选择正确的订阅。 如果你为行控制器指定自定义`Identifier`，更改**默认**在下面的代码代码段为你使用的标识符。 `RowController` *的每一行*时，将创建`SetNumberOfRows`称为和显示的表。

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> **请注意**： 表行起来似乎都是在 iOS 中没有被虚拟化。 尝试限制 （Apple 建议小于 20） 的行数。
创建行后, 需要填充每个单元格 (如`GetCell`像在 iOS 中)。 此代码段从[WatchTables 示例](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)更新每个行中的标签

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> **注意：**使用`SetNumberOfRows`，然后循环访问使用`GetRowController`导致要发送到手表的整个表。 后续视图的表中，如果你需要添加或删除特定行使用`InsertRowsAt`和`RemoveRowsAt`以提高性能。


## <a name="respond-to-taps"></a>响应分流

可以通过两种方式来响应行选择：

- 实现`DidSelectRow`方法在接口控制器上，或
- 在情节提要创建 segue 并实现`GetContextForSegue`如果你想行选择，以打开另一个场景。

### <a name="didselectrow"></a>DidSelectRow

若要以编程方式处理行选择，实现`DidSelectRow`方法。 若要打开新的场景，请使用`PushController`并传递场景的标识符和要使用的数据上下文：

```csharp
public override void DidSelectRow (WKInterfaceTable table, nint rowIndex)
{
    var rowData = rows [(int)rowIndex];
    Console.WriteLine ("Row selected:" + rowData);
    // if selection should open a new scene
    PushController ("secondInterface", rows[(int)rowIndex]);
}
```

### <a name="getcontextforsegue"></a>GetContextForSegue

将拖动 segue 情节提要从你的表行到另一个场景 (按住**控件**密钥拖动时)。
请务必选择 segue，并在中为该标识符**属性**pad (如`secondLevel`在下面的示例)。

在接口控制器中，实现`GetContextForSegue`方法并返回应提供的数据上下文 segue 进行呈现的场景。

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

此数据传递给目标情节提要场景其`Awake`方法。

## <a name="multiple-row-types"></a>多个行类型

默认情况下 table 控件具有你可以设计的单个行类型。 若要添加更多行模板使用**行**框中**属性**pad 创建更多行控制器：

![](table-images/prototype-rows1.png "设置原型行数")

设置**行**属性**3**将创建其他行占位符，您可以拖动到控件。 对于每一行，设置**类**名称中**属性**pad，确保创建行控制器类。

![](table-images/prototype-rows2.png "设计器中的原型行")

以不同的行类型使用表中填充`SetRowTypes`方法，以指定要用于表中的每一行的行控制器类型。 使用行的标识符来指定哪些行控制器应该用于每个行。

此数组中的元素数目应与匹配您希望在表中的行数：

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

当填充具有多个行控制器的表，将需要跟踪的哪种类型会因为填充 UI:

```csharp
for (var i = 0; i < rows.Count; i++) {
    if (i == 0) {
        var elementRow = (Type1RowController)myTable.GetRowController (i);
        // populate UI controls
    } else if (i == 3) {
        var elementRow = (Type2RowController)myTable.GetRowController (i);
        // populate UI controls
    } else {
        var elementRow = (DefaultRowController)myTable.GetRowController (i);
        // populate UI controls
    }
}
```


## <a name="vertical-detail-paging"></a>垂直的详细信息分页

watchOS 3 引入表的新功能： 能够滚动查看详细信息页与每个行，而无需返回到表并选择另一个行。 可通过向上和向下轻扫，或使用数字王冠滚动的详细信息屏幕。

![](table-images/table-scroll-sml.png "垂直的详细信息分页示例") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> **警告：**此功能目前仅提供通过编辑在 Xcode 接口生成器情节提要。

若要启用此功能，请选择`WKInterfaceTable`设计图面上，刻度上**垂直详细信息分页**选项：

![](table-images/vertical-detail-paging-sml.png "选择垂直详细信息分页选项")

作为[解释的 Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023)表导航必须使用 segue 以使用分页功能。 重新编写使用的任何现有代码`PushController`使用 segue 的相反。

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>附录： 行控制器代码示例

在设计器中创建行控制器时，IDE 将自动创建两个代码文件。 这些生成的文件中的代码下面显示了引用。

第一个将命名类，例如**RowController.cs**，如下所示：

```csharp
using System;
using Foundation;

namespace WatchTablesExtension
{
    public partial class RowController : NSObject
    {
        public RowController ()
        {
        }
    }
}
```

其他**。 designer.cs**文件是包含的容器和在设计器图面，如本示例具有一个创建的操作的分部类定义`WKInterfaceLabel`控件：

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using UIKit;

namespace WatchTables.OnWatchExtension
{
    [Register ("RowController")]
    partial class RowController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        public WatchKit.WKInterfaceLabel MyLabel { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (MyLabel != null) {
                MyLabel.Dispose ();
                MyLabel = null;
            }
        }
    }
}
```

插座和此声明的操作然后可以引用以代码-但是**。 designer.cs**不应直接编辑文件。



## <a name="related-links"></a>相关链接

- [WatchTables （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple 的表文档](https://developer.apple.com/reference/watchkit/wkinterfacetable)
