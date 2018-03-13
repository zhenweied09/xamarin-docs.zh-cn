---
title: "布局"
description: "在屏幕上的视图的布局。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 65030DA3-C7C1-4A02-B478-811073C39139
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: 0ede9bbb47f398a82d6eae5d827122f469ad6ea4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="layouts"></a>布局

Xamarin.Forms 具有几种布局和用于组织在屏幕上的内容的功能。 

> [!VIDEO https://youtube.com/embed/4HlLjTZQzjM]

**Xamarin.Forms 布局，也可由[Xamarin 大学](https://university.xamarin.com/)**

每个布局控件是下面所述，以及如何处理屏幕方向更改的详细信息：

* **[StackLayout](stack-layout.md)**  &ndash;用于线性，排列视图水平或垂直。 可以向左或向右的布局的中心对齐 StackLayout 中的视图。
* **[AbsoluteLayout](absolute-layout.md)**  &ndash;用于按设置坐标排列视图 （&） 在绝对值和比率方面的调整大小。 AbsoluteLayout 可用来进行分层视图，以及它们定位于左、 右或中心。
* **[RelativeLayout](relative-layout.md)**  &ndash;用于排列视图，通过设置相对于其父级的维度和位置的约束。
* **[网格](grid.md)** &ndash;用于排列在网格中的视图。 根据绝对值或比率，可以指定行和列。
* **[ScrollView](scroll-view.md)**  &ndash;用于提供滚动视图不能完全适合屏幕的界限。
* **[LayoutOptions](layout-options.md)**  &ndash;定义对齐方式和视图中，相对于其父级的扩展。
* **[输入透明度](#input_transparency)** &ndash;指定元素是否接收输入。
* **[边距和填充](margin-and-padding.md)** &ndash;演示如何控制布局行为元素呈现用户界面中时。
* **[设备方向](device-orientation.md)** &ndash;说明如何处理设备方向更改。
* **[在平板电脑和桌面设备上的布局](tablet.md)** &ndash;演示如何针对每个平台上的更大屏幕进行优化。
* **[创建自定义布局](custom.md)** &ndash;说明如何创建一个自定义布局的类。
* **[布局压缩](layout-compression.md)** &ndash;移除指定尝试提升页呈现性能的来自的可视化树的布局。

平台控件还可直接在具有 Xamarin.Forms 布局[**本机嵌入**](~/xamarin-forms/platform/native-views/index.md) （新 Xamarin.Forms 2.2 中），并且可以[**创建自定义布局**](custom.md)来满足特定要求。

下图直观显示布局控件：

[![](images/layouts-sml.png "Xamarin.Forms 布局")](images/layouts.png#lightbox "Xamarin.Forms 布局")

## <a name="choosing-the-right-layout"></a>选择右的布局

在你的应用程序中选择的布局可以帮助，或者会降低你为你正在创建具吸引力，也可用 Xamarin.Forms 应用。 花费一些时间来考虑每个布局工作可帮助你编写更简洁且更具伸缩性的 UI 代码。 屏幕可以具有不同的布局，以实现的特定设计的组合。

### <a name="stacklayoutstack-layoutmd"></a>[StackLayout](stack-layout.md)

`StackLayout`用于显示视图沿水平或垂直的一行。 位置和布局中的大小将根据视图的确定`HeightRequest`， `WidthRequest`，`HorizontalOptions`和`VerticalOptions`。 `StackLayout` 通常用作基布局，排列在屏幕上的其他布局。

有关何时示例`StackLayout`将是一个不错的选择，请考虑的应用程序需要显示一个按钮和具有左对齐的标签和右对齐按钮的标签。

```xaml
<StackLayout Orientation="Horizontal">
  <Label HorizontalOptions="StartAndExpand" Text="Label" />
  <Button HorizontalOptions="End" Text="Button" />
</StackLayout>
```

### <a name="absolutelayoutabsolute-layoutmd"></a>[AbsoluteLayout](absolute-layout.md)

`AbsoluteLayout`用于显示具有作为显式值或相对于布局的大小的值的大小和位置所指定的视图。 与不同`StackLayout`和`Grid`，`AbsoluteLayout`允许子视图重叠。 与不同`RelativeLayout`，`AbsoluteLayout`不可以放置屏幕之外的元素。

有关何时示例`AbsoluteLayout`将是一个不错的选择，请考虑的应用程序需要提供作为堆栈的对象的集合。 此选项通常显示当呈现唱片集的照片或歌曲。 下面的代码与旋转以提示超时和累积的内容的元素提供多行之中，的外观：

在 XAML 中：

```xaml
<AbsoluteLayout Padding="15">
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="30"
    Source="bottom.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="60"
    Source="middle.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
    Source="cover.png" />
</AbsoluteLayout>
```

请注意上述代码中的以下方面：

- 每个`Image`显示在同一位置 （中间的水平空间）
- `Padding`程序认为是`AbsoluteLayout`与`RelativeLayout`，表示忽略它。
- `AbsoluteLayout.LayoutFlags` 指定将如何解释布局边界。 在这种情况下`PositionProportional`，意味着这些坐标也会的布局，大小的比率，而大小将被解释为显式大小。
- `AbsoluteLayout.Layoutbounds` 这种顺序指定水平位置、 垂直位置、 宽度和高度。

### <a name="relativelayoutrelative-layoutmd"></a>[RelativeLayout](relative-layout.md)

`RelativeLayout`用于显示视图大小和位置指定为相对于布局或另一个视图的值的值。 相对的值不需要匹配他对应值相关的视图。 例如，很可能先设置视图的`Width`属性按比例到另一个视图`X`属性。

RelativeLayout 可以用于创建跨设备大小按比例缩放的 Ui。 下面的 XAML 实现与框的最顶层的角，在使用中 center 标志 flagpole 设计：

```xaml
<RelativeLayout HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
  <BoxView Color="Blue" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = 0}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Red" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .9}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Gray" WidthRequest="15" x:Name="pole"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .45}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = .25}" />
  <BoxView Color="Green"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
    RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.2, Constant=20}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

请注意上述代码中的以下方面：

- 位置和大小指定为约束。
- 名为 flagpole 以便的标志的 （绿框中的） 可以相对于 flagpole 集位置。
- 约束表达式具有`Factor`和`Constant`属性，用于定义为倍数的位置和大小 （或秒的小数部分） 的其他对象，以及常量的属性。 常量可以是负数。

### <a name="gridgridmd"></a>[网格](grid.md)

`Grid`适用于在行和列中显示元素。 请注意，网格不是表，因此它没有单元格、 页眉和页脚行或行与列之间的边框的概念。 一般情况下，不适合于显示表格格式数据网格。 对于使用，请考虑[ListView](~/xamarin-forms/user-interface/listview/index.md)或[TableView](~/xamarin-forms/user-interface/tableview.md)。

有关何时示例`Grid`是右的布局，以使用，请数字输入考虑的一个计算器。 一个计算器数字输入可能包括四个行和三个列，每个都有一个按钮。 下面的代码实现此设计：

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>

  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Button Text="1" Grid.Row="0" Grid.Column="0" />
  <Button Text="2" Grid.Row="0" Grid.Column="1" />
  <Button Text="3" Grid.Row="0" Grid.Column="2" />
  <Button Text="4" Grid.Row="1" Grid.Column="0" />
  <Button Text="5" Grid.Row="1" Grid.Column="1" />
  <Button Text="6" Grid.Row="1" Grid.Column="2" />
  <Button Text="7" Grid.Row="2" Grid.Column="0" />
  <Button Text="8" Grid.Row="2" Grid.Column="1" />
  <Button Text="9" Grid.Row="2" Grid.Column="2" />
  <Button Text="0" Grid.Row="3" Grid.Column="1" />
  <Button Text="&lt;-" Grid.Row="3" Grid.Column="2" />
</Grid>
```

请注意上述代码中的以下方面：

- 网格列显式指定和，不从内容推断。
- `Height` 和`Width`可将该值设置为星号，这意味着，网格会设置这些值以填充可用空间。
- 每个按钮的位置由指定`Grid.Row`  &  `Grid.Column`属性。

### <a name="layoutoptionslayout-optionsmd"></a>[LayoutOptions](layout-options.md)

[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)结构可以用于定义对齐方式和视图中，相对于其父级的扩展。

### <a name="margin-and-paddingmargin-and-paddingmd"></a>[边距和填充](margin-and-padding.md)

[ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)和[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)属性控制布局行为元素呈现用户界面中时。

<a name="input_transparency" />

### <a name="input-transparency"></a>输入的透明度

每个元素具有[ `InputTransparent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.InputTransparent/)属性，用于定义元素是否接收输入。 其默认值是`false`，确保元素接收输入。

当容器类，例如布局类，其值传输到子元素上设置此属性。 因此，设置[ `InputTransparent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.InputTransparent/)属性`true`布局类将导致不接收输入的布局中的元素。

### <a name="device-orientationdevice-orientationmd"></a>[设备方向](device-orientation.md)

Xamarin.Forms 和其内置的布局是能够处理更改设备方向。 请考虑您的应用程序将支持，以及如何将使哪些方向利用横向和纵向模式中提供的空间。

### <a name="layout-for-tablet-and-desktop-appstabletmd"></a>[对于平板电脑和桌面应用程序的布局](tablet.md)

iOS、 Android 和 Windows 平台所有支持更大的屏幕大小平板设备 （以及便携式计算机和适用于 Windows 的桌面）。 Xamarin.Forms 能够让你通过检测的设备类型和调整页面布局中，或使用完全不同的页完全适用于较大的屏幕来优化应用程序以较大的屏幕。

### <a name="creating-a-custom-layoutcustommd"></a>[创建自定义布局](custom.md)

Xamarin.Forms 定义四个布局类- [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)， [ `AbsoluteLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)， [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)，和[ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)，和每个不同的方式排列其子。 但是，有时由 Xamarin.Forms 进行必要的组织不使用的布局的页面内容提供。 本文介绍如何编写一个自定义布局的类，并演示了方向区分`WrapLayout`跨页上，水平排列其子，然后将包装对其他行的后续子级的显示的类。

### <a name="layout-compressionlayout-compressionmd"></a>[布局压缩](layout-compression.md)

布局压缩的可视化树中中删除指定的布局，以尝试提升页呈现性能。 这带来的性能优势因页面复杂性、要使用的操作系统版本以及运行应用的设备而异。 不过，在旧设备上实现的性能提升最大。

## <a name="making-your-choice"></a>做出选择

请注意，在大多数情况下，多个布局选项可用来实现您所需的设计。 当存在多个有效的选项时，请考虑哪种方法将是最简单的您的实际情况。
大多数设计无法实现与只是一种布局，因此作为嵌套布局需要来创建更复杂的设计。


## <a name="related-links"></a>相关链接

- [Apple 的人机接口指南](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG)
- [Android 设计网站](https://developer.android.com/design/index.html)
- [布局 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 示例 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
