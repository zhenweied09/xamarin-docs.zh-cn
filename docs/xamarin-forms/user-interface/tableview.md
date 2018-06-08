---
title: TableView
description: 提供滚动菜单、 设置和输入的窗体。
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 312472fdfae65bc62b76f4295a13760236dededc
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847652"
---
# <a name="tableview"></a>TableView

[TableView](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)是用于显示数据或选择的可滚动列表视图其中有不共享相同的模板的行。 与不同[ListView](~/xamarin-forms/user-interface/listview/index.md)，TableView 没有这一概念`ItemsSource`，因此作为子级必须手动添加项。

本指南由以下部分组成：

- **[用例](#Use_Cases)** &ndash;何时使用 TableView 而不是 ListView 或自定义视图。
- **[TableView 结构](#TableView_Structure)** &ndash; TableView 中所需的视图层次结构。
- **[TableView 外观](#TableView_Appearance)** &ndash; TableView 的自定义选项。
- **[内置的单元格](#Built-In_Cells)** &ndash;内置的单元格选项，包括[EntryCell](#EntryCell)和[SwitchCell](#SwitchCell)。
- **[自定义单元格](#Custom_Cells)** &ndash;如何使你自己的自定义单元格。

![](tableview-images/tableview-all-sml.png "TableView 示例")

<a name="Use_Cases" />

## <a name="use-cases"></a>用例

TableView 非常有用：

- 提供设置的列表
- 收集数据在表单中，或
- 显示呈现的数据以不同的方式从行到行 （例如数字、 百分比和映像）。

TableView 处理滚动和布局具吸引力的部分，常见的需要上述方案中的行。 `TableView`控件使用每个平台的基础等效视图时可用，创建拥有的每个平台的本机外观。

<a name="TableView_Structure" />

## <a name="tableview-structure"></a>TableView 结构

中的元素`TableView`归纳为部分。 根目录下的`TableView`是`TableRoot`，这是父表与一个或多个`TableSections`:

```csharp
Content = new TableView {
    Root = new TableRoot {
        new TableSection...
    },
    Intent = TableIntent.Settings
};
```

每个`TableSection`组成一个标题和一个或多个 ViewCells。 在这里我们看到`TableSection`的`Title`属性设置为 *"环"* 构造函数中：

```csharp
var section = new TableSection ("Ring") { //TableSection constructor takes title as an optional parameter
    new SwitchCell {Text = "New Voice Mail"},
    new SwitchCell {Text = "New Mail", On = true}
};
```

若要完成的布局相同，如上所述在 XAML 中：

```xaml
<TableView Intent="Settings">
    <TableRoot>
        <TableSection Title="Ring">
            <SwitchCell Text="New Voice Mail" />
      <SwitchCell Text="New Mail" On="true" />
        </TableSection>
    </TableRoot>
</TableView>
```

<a name="TableView_Appearance" />

## <a name="tableview-appearance"></a>TableView 外观

TableView 公开`Intent`属性，它是以下选项的枚举：

- **数据**&ndash;用于显示数据条目时。 请注意， [ListView](~/xamarin-forms/user-interface/listview/index.md)可能是更好的选择对滚动数据的列表。
- **窗体**&ndash;以便在 TableView 充当窗体时使用。
- **菜单**&ndash;以便在提供的选择菜单时使用。
- **设置**&ndash;供使用时显示的配置设置的列表。

`TableIntent`选择可能会影响如何`TableView`将出现在每个平台。 即使有不清除差异，它是一种最佳做法来选择`TableIntent`与最匹配你想要使用表。

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>内置的单元格

Xamarin.Forms 附带了内置单元格，收集和显示信息。 ListView 和 TableView 可以使用所有的相同单元格中，但以下是最为相关的 TableView 方案：

- **SwitchCell** &ndash;演示和捕获 true/false 状态，以及文本标签。
- **EntryCell** &ndash;演示和捕获文本。

请参阅[ListView 单元格的外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)有关的详细说明[TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell)和[ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell)。

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell
[`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/) 是控件用于提供和捕获开/关或`true` / `false`状态。

SwitchCells 有一个行待编辑文本和一个打开/关闭属性。 这两种属性是可绑定。

- `Text` &ndash; 要交换机旁边显示的文本。
- `On` &ndash; 是否交换机被显示为 on 或 off。

请注意，`SwitchCell`公开`OnChanged`事件，从而可以响应的单元格的状态更改。

![](tableview-images/switch-cell.png "SwitchCell 示例")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell
[`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/) 如果需要显示用户可以编辑的文本数据时十分有用。 `EntryCell`s 提供可以自定义以下属性：

- `Keyboard` &ndash; 若要编辑时显示键盘。 有数字值、 电子邮件、 电话号码等之类的内容的选项。[请参阅 API 文档](http://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/)。
- `Label` &ndash; 要文本输入字段右侧显示的标签文本。
- `LabelColor` &ndash; 标签文本的颜色。
- `Placeholder` &ndash; 当它为 null 或空时显示在输入字段的文本。 当文本输入开始时，此文本将消失。
- `Text` &ndash; 输入字段中的文本。
- `HorizontalTextAlignment` &ndash; 文本的水平对齐方式。 可以对齐 center，左侧或右侧。 [请参阅 API 文档](http://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/)。

请注意，`EntryCell`公开`Completed`事件，当用户点击完成键盘上编辑文本时激发。

![](tableview-images/entry-cell.png "EntryCell 示例")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>自定义单元格
当内置的单元格不够用时，可以使用自定义单元格为捕获适合您的应用程序的方法中的数据和呈现。 例如，你可能希望将显示滑块，以允许用户选择的映像不透明度。

所有自定义单元格必须派生自[ `ViewCell` ](http://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)，类型使用内置的单元格的所有相同的基类。

这是自定义单元格的一个示例：

![](tableview-images/custom-cell.png "自定义单元格示例")

### <a name="xaml"></a>XAML
以下是要创建上述布局的 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="DemoTableView.TablePage" Title="TableView">
    <ContentPage.Content>
        <TableView Intent="Settings">
            <TableRoot>
                <TableSection Title="Getting Started">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <Image Source="bulb.png" />
                            <Label Text="left"
                              TextColor="#f35e20" />
                            <Label Text="right"
                              HorizontalOptions="EndAndExpand"
                              TextColor="#503026" />
                        </StackLayout>
                    </ViewCell>
                </TableSection>
            </TableRoot>
        </TableView>
    </ContentPage.Content>
</ContentPage>

```

上面的 XAML 执行大量操作。 让我们将分解：

- 下的根元素`TableView`是`TableRoot`。
- 没有`TableSection`立即下方`TableRoot`。
- `ViewCell`直接下节定义。 与不同`ListView`，`TableView`不需要该自定义 （或任何） 中定义单元格`ItemTemplate`。
- StackLayout 用于管理自定义单元格的布局。 这里可以使用任何布局。

### <a name="cnum"></a>C&num;

因为`TableView`工作方式与静态数据或手动更改的数据，它不具有项模板的概念。 相反，自定义单元格可以手动创建并放入表。 请注意创建自定义的技术单元格，继承自`ViewCell`，然后将其添加到`TableView`您这类将内置的单元格，则也支持。
下面是 c# 代码来完成上述布局：

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() {Source = "bulb.png"});
layout.Children.Add (new Label() {
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label () {
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot () {
    new TableSection("Getting Started") {
        new ViewCell() {View = layout}
    }
};

Content = table;
```

上面的 C# 正在执行大量。 让我们将分解：

- 下的根元素`TableView`是`TableRoot`。
- 没有`TableSection`立即下方`TableRoot`。
- `ViewCell`直接下节定义。 与不同`ListView`，`TableView`不需要该自定义 （或任何） 中定义单元格`ItemTemplate`。
- StackLayout 用于管理自定义单元格的布局。 这里可以使用任何布局。

请注意，永远不会定义自定义单元格的类。 相反，`ViewCell`的视图属性设置为的特定实例`ViewCell`。



## <a name="related-links"></a>相关链接

- [TableView （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)
