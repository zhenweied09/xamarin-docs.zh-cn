---
title: Xamarin.Forms TableView
description: 此文章介绍了如何使用 Xamarin.Forms TableView 类在应用程序中显示滚动菜单、 设置和输入窗体。
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/04/2018
ms.openlocfilehash: 0561c2a08aa96755d6d6caa9a3c683a5ce83ae10
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053981"
---
# <a name="xamarinforms-tableview"></a>Xamarin.Forms TableView

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)

[TableView](xref:Xamarin.Forms.TableView)是用于显示数据或选择的可滚动列表视图的行不共享同一个模板。 与不同[ListView](~/xamarin-forms/user-interface/listview/index.md)，TableView 不具有这一概念`ItemsSource`，因此，作为子级必须手动添加的项。

本指南是由以下部分组成：

- **[用例](#Use_Cases)** &ndash;何时使用 TableView 而不是 ListView 或自定义视图。
- **[TableView 结构](#TableView_Structure)** &ndash; TableView 中所需的视图层次结构。
- **[TableView 外观](#TableView_Appearance)** &ndash; TableView 的自定义选项。
- **[内置的单元格](#Built-In_Cells)** &ndash;内置的单元格选项，包括[EntryCell](#EntryCell)并[SwitchCell](#SwitchCell)。
- **[自定义单元格](#Custom_Cells)** &ndash;如何使您自己的自定义单元格。

![](tableview-images/tableview-all-sml.png "TableView 示例")

<a name="Use_Cases" />

## <a name="use-cases"></a>用例

TableView 当以下情况下非常有用：

- 显示一系列的设置，
- 在窗体中收集数据或
- 显示呈现的数据以不同的方式从行与行 （例如数字、 百分比和图像）。

TableView 处理滚动和布局中极具吸引力的部分中，针对上述情形，通常需要的行。 `TableView`控件使用每个平台的基础等效的视图时可用，创建每个平台的本机外观。

<a name="TableView_Structure" />

## <a name="tableview-structure"></a>TableView 结构

中的元素`TableView`划分为部分。 根目录`TableView`是`TableRoot`，这是一个或多个父级`TableSections`:

```csharp
Content = new TableView {
    Root = new TableRoot {
        new TableSection...
    },
    Intent = TableIntent.Settings
};
```

每个`TableSection`标题和一个或多个 ViewCells 组成。 这里我们可以看到`TableSection`的`Title`属性设置为 *"环"* 构造函数中：

```csharp
var section = new TableSection ("Ring") { //TableSection constructor takes title as an optional parameter
    new SwitchCell {Text = "New Voice Mail"},
    new SwitchCell {Text = "New Mail", On = true}
};
```

若要完成如上所示在 XAML 中相同的布局：

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

TableView 公开`Intent`属性，它是一个枚举，下列选项：

- **数据**&ndash;用于显示数据条目时。 请注意， [ListView](~/xamarin-forms/user-interface/listview/index.md)可能是更好的选择进行滚动数据的列表。
- **窗体** &ndash; TableView 充当窗体时使用。
- **菜单**&ndash;供使用时显示选择的菜单。
- **设置**&ndash;供使用时显示的配置设置列表。

`TableIntent`选择可能会影响如何`TableView`显示每个平台上。 即使有不清除差异，它是最佳选择`TableIntent`的最匹配你想要使用表。

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>内置的单元格

Xamarin.Forms 提供了内置的单元格，以收集和显示信息。 尽管 ListView 和 TableView 可以使用所有相同的单元格，但以下是最适用于 TableView 方案：

- **SwitchCell** &ndash;演示和捕获的 true/false 状态，以及文本标签。
- **EntryCell** &ndash;演示和捕获的文本。

请参阅[ListView 单元格的外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)有关的详细说明[TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell)并[ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell)。

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell
[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 是要用于显示和捕获的控件打开/关闭或`true` / `false`状态。

SwitchCells 具有待编辑文本和打开/关闭属性的一个行。 这些属性都可绑定。

- `Text` &ndash; 若要切换旁边显示的文本。
- `On` &ndash; 此开关是否显示为 on 或 off。

请注意，`SwitchCell`公开`OnChanged`事件，从而可以对该单元格的状态变化作出响应。

![](tableview-images/switch-cell.png "SwitchCell 示例")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell
[`EntryCell`](xref:Xamarin.Forms.EntryCell) 如果需要显示的用户可以编辑的文本数据时非常有用。 `EntryCell`s 提供可自定义的以下属性：

- `Keyboard` &ndash; 若要编辑时显示键盘。 有数字值、 电子邮件、 电话号码等之类的内容的选项。[请参阅 API 文档](xref:Xamarin.Forms.Keyboard)。
- `Label` &ndash; 要在文本输入字段右侧显示的标签文本。
- `LabelColor` &ndash; 标签文本的颜色。
- `Placeholder` &ndash; 它为 null 或空时在输入字段中显示的文本。 文本输入开始时，此文本将消失。
- `Text` &ndash; 在输入字段中的文本。
- `HorizontalTextAlignment` &ndash; 文本的水平对齐方式。 可以在中心，左侧或右侧对齐。 [请参阅 API 文档](xref:Xamarin.Forms.TextAlignment)。

请注意，`EntryCell`公开`Completed`事件，如果用户点击完成键盘上编辑文本时则会激发该事件。

![](tableview-images/entry-cell.png "EntryCell 示例")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>自定义单元格
当内置的单元格仍不足时，可以使用自定义单元格能够并捕获有关您的应用程序有意义的方式中的数据。 例如，您可能希望显示滑块，以允许用户选择的图像的不透明度。

自定义的所有单元格必须派生自[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，所有内置的单元格类型使用相同的基类。

这是自定义单元格的一个示例：

![](tableview-images/custom-cell.png "自定义单元格示例")

### <a name="xaml"></a>XAML
若要创建上述布局 XAML 如下所示：

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

上述 XAML 执行大量操作。 让我们深入细致地：

- 根元素下的`TableView`是`TableRoot`。
- 没有`TableSection`立即下方`TableRoot`。
- `ViewCell`直接下节定义。 与不同`ListView`，`TableView`不需要该自定义 （或者任何） 中定义的单元格`ItemTemplate`。
- 在 StackLayout 用于管理自定义单元格的布局。 无法在此处使用任何布局。

### <a name="cnum"></a>C&num;

因为`TableView`工作方式与静态数据或手动更改的数据，它没有项模板的概念。 相反，自定义单元格可以手动创建并放入表。 请注意技术创建自定义单元格的继承`ViewCell`，然后将其添加到`TableView`像您一样将内置的单元格，还支持。
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

上面的 C# 执行大量。 让我们深入细致地：

- 根元素下的`TableView`是`TableRoot`。
- 没有`TableSection`立即下方`TableRoot`。
- `ViewCell`直接下节定义。 与不同`ListView`，`TableView`不需要该自定义 （或者任何） 中定义的单元格`ItemTemplate`。
- 在 StackLayout 用于管理自定义单元格的布局。 无法在此处使用任何布局。

请注意，永远不会定义一个类，用于自定义单元格。 相反，`ViewCell`的视图属性设置的特定实例`ViewCell`。

## <a name="row-height"></a>行高

[ `TableView` ](xref:Xamarin.Forms.TableView)类具有可用于更改单元格的行的高度的两个属性：

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) – 设置为每个行的高度`int`。
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) – 如果行具有不同高度设置为`true`。 请注意，当此属性设置为`true`，行高度自动计算，并通过 Xamarin.Forms 应用。

当中某个单元格中的内容的高度[ `TableView` ](xref:Xamarin.Forms.TableView)更改行高度隐式更新 Android 和通用 Windows 平台 (UWP) 上。 但是，在 iOS 上它必须强制通过设置更新[ `HasUnevenRows` ](xref:Xamarin.Forms.TableView.HasUnevenRows)属性设置为`true`并通过调用[ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法。

下面的 XAML 示例演示[ `TableView` ](xref:Xamarin.Forms.TableView) ，其中包含[ `ViewCell` ](xref:Xamarin.Forms.ViewCell):

```xaml
<ContentPage ...>
    <TableView ...
               HasUnevenRows="true">
        <TableRoot>
            ...
            <TableSection ...>
                ...
                <ViewCell x:Name="_viewCell"
                          Tapped="OnViewCellTapped">
                    <Grid Margin="15,0">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Label Text="Tap this cell." />
                        <Label x:Name="_target"
                               Grid.Row="1"
                               Text="The cell has changed size."
                               IsVisible="false" />
                    </Grid>
                </ViewCell>
            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

当[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)点击，`OnViewCellTapped`执行事件处理程序：

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

`OnViewCellTapped`事件处理程序显示或隐藏第二个[ `Label` ](xref:Xamarin.Forms.Label)中[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，并通过调用将显式更新该单元格的大小[ `Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法。

以下屏幕截图显示之前在点击后的单元格：

![](tableview-images/cell-beforeresize.png "调整大小前的 ViewCell")

下面的屏幕截图显示在点击后的单元格：

![](tableview-images/cell-afterresize.png "调整大小后的 ViewCell")

> [!IMPORTANT]
> 如果过度使用此功能，则强可能导致性能下降。

## <a name="related-links"></a>相关链接

- [TableView （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)
