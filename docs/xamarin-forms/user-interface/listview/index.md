---
title: ListView
description: 美观、 交互式列表中展示你的数据。
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2015
ms.openlocfilehash: ddd779fc7eb1a10e74c68504367083ff0efcdfcd
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2018
---
# <a name="listview"></a>ListView

ListView 是提供数据的列表，尤其是需要滚动的冗长列表的视图。 本指南将演示如何使用 ListView:

1. **[数据源](data-and-databinding.md)** &ndash;填充数据，无论数据绑定与 ListView。
2. **[单元格外观](customizing-cell-appearance.md)** &ndash;自定义内置的单元格的外观或创建你自己的自定义单元格。
3. **[列出外观](customizing-list-appearance.md)** &ndash;自定义 ListView 的外观。 设置页眉和页脚、 启用组和更改的行的高度。
4. **[交互性](interactivity.md)** &ndash;处理分流和选择、 实现请求刷新，以及添加上下文的操作。
5. **[性能](performance.md)** &ndash;避免性能问题。

## <a name="use-cases"></a>用例
请确保 ListView 是用于你的需求的正确控件。 可以在其中显示的数据的可滚动列表任何情况下使用 ListView。 Listview 支持上下文操作和数据绑定。

不应与混淆 ListView [TableView](~/xamarin-forms/user-interface/tableview.md)。 TableView 控件会是更好的选择的选项或数据未绑定的列表。 例如，具有一组大多数预定义的选项，iOS 设置应用程序是更好地适合使用 TableView 比 ListView。

请注意，ListView 是最佳此外还适用于同类数据&ndash;，即所有的数据应为同一类型。 这是因为只有一个单元格类型可以用于在列表中每个行。 TableViews 可以支持多个单元格类型，因此它们是更好的选择时需要使用多种视图。


## <a name="components"></a>组件数
ListView 具有大量可用于执行每个平台的本机功能的组件。 下面描述了每个组件：

- **[页眉和页脚](customizing-list-appearance.md#Headers_and_Footers)** &ndash;文本或视图的开头和结尾的列表，在显示分开列表的数据。 页眉和页脚可以绑定到数据源独立 ListView 的数据源。
- **[组](customizing-list-appearance.md#Grouping)** &ndash; ListView 中的数据可以分组以便更易于导航。 组时通常是绑定的数据：

![](images/grouping-depth.png "与分组数据的 ListView")

- **[单元格](customizing-cell-appearance.md)** &ndash; ListView 中的数据也会出现在单元格。 每个单元格对应于数据行。 没有内置的单元格可供选择，或者你可以定义自己的自定义单元格。 内置和自定义单元可以是在 XAML 或代码中使用或定义。
  - **[内置](customizing-cell-appearance.md#Built_in_Cells)** &ndash;内置的单元格，尤其是 TextCell 和 ImageCell，可能会大幅提高性能，因为它们对应于每个平台上的本机控件。
    - **[TextCell](customizing-cell-appearance.md#TextCell)**  &ndash;显示的文本，并且可选择带有详细文本的字符串。 详细信息文本呈现为第二行中以强调文字颜色与较小的字体。
    - **[ImageCell](customizing-cell-appearance.md#ImageCell)**  &ndash;显示带文本的图像。 将显示为与左侧图像 TextCell。
  - **[自定义单元格](customizing-cell-appearance.md#customcells)** &ndash;自定义单元格非常，当你需要提供复杂的数据。 例如，自定义视图，无法用于提供歌曲，包括唱片集和艺术家的列表：

![](images/image-cell-default.png "与 ImageCells 的 ListView")

若要了解有关自定义在 ListView 中的单元格的详细信息，请参阅[自定义 ListView 单元格的外观](customizing-cell-appearance.md)。

## <a name="functionality"></a>功能
ListView 支持大量的交互样式，包括：

- **[请求刷新](interactivity.md#Pull_to_Refresh)** &ndash; ListView 支持每个平台上的刷新请求。
- **[上下文操作](interactivity.md#Context_Actions)** &ndash; ListView 在列表中的各个项上支持拍摄操作。 例如，你可以在 iOS 上实现轻扫到操作或长时间 tap 在 Android 上的操作。
- **[选择](interactivity.md#selectiontaps)** &ndash;可以侦听选择和取消点击行时采取操作。

![](images/context-default.png "使用上下文的操作的 ListView")

若要了解有关 ListView 交互功能的详细信息，请参阅[操作 （&) 与 ListView 交互的能力](interactivity.md)。


## <a name="related-links"></a>相关链接

- [使用与 ListView （示例）](https://developer.xamarin.com/samples/WorkingWithListview)
- [双向绑定 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [生成中单元格 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [自定义单元格 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [分组 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [自定义呈现器视图 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/WorkingWithListviewNative)
- [ListView 交互性 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [iOS 工作簿](https://developer.xamarin.com/workbooks/xamarin-forms/user-interface/listview/ListView1-ios.workbook)
- [Android 工作簿](https://developer.xamarin.com/workbooks/xamarin-forms/user-interface/listview/ListView1-android.workbook)
