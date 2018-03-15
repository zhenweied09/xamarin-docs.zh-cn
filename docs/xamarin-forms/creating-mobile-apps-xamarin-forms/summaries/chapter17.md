---
title: "第 17 章的摘要。 控制网格"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 4f76b1060ee8a672319683525470aee00e3db001
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>第 17 章的摘要。 控制网格

[ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)是一种强大的布局机制，它将为行和列的单元格及其子级。 与类似 HTML 不同`table`元素，`Grid`仅适用于的布局，而不是演示文稿的目的。

## <a name="the-basic-grid"></a>基本的网格

`Grid` 派生自[ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)，后者定义了[ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/)属性，`Grid`继承。 你可以填写此 XAML 或代码中的集合。

### <a name="the-grid-in-xaml"></a>在 XAML 中网格

定义`Grid`在 XAML 中通常开头填充[ `RowDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowDefinitions/)和[ `ColumnDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnDefinitions/)的集合`Grid`与[ `RowDefinition`](https://developer.xamarin.com/api/type/Xamarin.Forms.RowDefinition/)和[ `ColumnDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColumnDefinition/)对象。 这是如何建立的行数和列`Grid`，及其属性。

`RowDefinition` 具有[ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.RowDefinition.Height/)属性和`ColumnDefinition`具有[ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ColumnDefinition.Width/)属性，这两个类型[ `GridLength` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLength/)，一种结构。

在 XAML 中， [ `GridLengthTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLengthTypeConverter/)将转换到的简单文本字符串`GridLength`值。 在后台， [ `GridLength`构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.GridLength.GridLength/p/System.Double/Xamarin.Forms.GridUnitType/)创建`GridLength`值根据数量和类型的值[ `GridUnitType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridUnitType/)，一个包含三个成员的枚举：

- [`Absolute`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Absolute/) &mdash; 独立于设备的单位 （在 XAML 中的数字） 中指定的宽度或高度
- [`Auto`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Auto/) &mdash; 高度或宽度是根据单元格内容 （如在 XAML 中的"自动"） 的自动调整大小
- [`Star`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Star/) &mdash; 按比例分配剩余高度或宽度 (带数字"\*"，调用*星型*，在 XAML 中)

每个子`Grid`还必须被分配行和列 （显式或隐式）。 跨越行和列范围都是可选的。 这些指定使用附加的可绑定属性&mdash;属性定义的`Grid`但设置的子级`Grid`。 `Grid` 定义四个静态附加可绑定属性：

- [`RowProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowProperty/) &mdash; 从零开始的行;默认值为 0
- [`ColumnProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnProperty/) &mdash; 从零开始的列。默认值为 0
- [`RowSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowSpanProperty/) &mdash; 数的行子跨越;默认值为 1
- [`ColumnSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnSpanProperty/) &mdash; 数的列子跨越;默认值为 1

在代码中，程序可以使用八个静态方法来设置和获取这些值：

- [`Grid.SetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRow/p/Xamarin.Forms.BindableObject/System.Int32/) 和 [`Grid.GetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRow/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumn/p/Xamarin.Forms.BindableObject/System.Int32/) 和 [`Grid.GetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumn/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRowSpan/p/Xamarin.Forms.BindableObject/System.Int32/) 和 [`Grid.GetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRowSpan/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumnSpan/p/Xamarin.Forms.BindableObject/System.Int32/) 和 [`Grid.GetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumnSpan/p/Xamarin.Forms.BindableObject/)

在 XAML 中使用以下属性用于设置这些值：

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

[ **SimpleGridDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo)示例演示如何创建和初始化`Grid`在 XAML 中。

`Grid`继承[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)属性从`Layout`并定义提供的行和列之间的间距的两个附加属性：

- [`RowSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowSpacing/) 默认值为 6
- [`ColumnSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnSpacing/) 默认值为 6

`RowDefinitions`和`ColumnDefinitions`集合不是绝对必需的。 如果不存在，`Grid`创建行和列`Grid`子级，并将这些所有默认`GridLength`的"\*"（星号）。

### <a name="the-grid-in-code"></a>在代码中网格

[ **GridCodeDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo)示例演示如何创建并填充`Grid`在代码中。 你可以设置附加的属性，为每个孩子直接或间接通过调用其他`Add`等方法[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/)由定义[Grid.IGridList<T> ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid+IGridList%3CT%3E/)接口。

### <a name="the-grid-bar-chart"></a>网格条形图

[ **GridBarChart** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart)示例演示如何添加多个`BoxView`元素与`Grid`批量[ `AddHorizontal` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.AddHorizontal/p/System.Collections.Generic.IEnumerable%7BXamarin.Forms.View%7D/)方法。 默认情况下，这些`BoxView`元素具有相等宽度。 每个高度`BoxView`控制类似于条形图。

`Grid`中**GridBarChart**示例共享`AbsoluteLayout`与最初可见的父`Frame`。 该程序还会设置`TapGestureRecognizer`上每个`BoxView`使用`Frame`以显示有关分流条的信息。

### <a name="alignment-in-the-grid"></a>在网格中的对齐方式

[ **GridAlignment** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment)示例演示如何使用`VerticalOptions`和`HorizontalOptions`属性对齐内的子级`Grid`单元格。

[ **SpacingButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons)同样示例空间`Button`元素的中间`Grid`单元格。

### <a name="cell-dividers-and-borders"></a>单元格分隔条和边框

`Grid`不包括绘制单元格分隔线或边框的功能。 但是，你可以使你自己。

[ **GridCellDividers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers)演示了如何定义附加的行和列专门为精简`BoxView`元素来模拟分隔线。

[ **GridCellBorders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders)程序不会创建任何其他单元，但改为对齐`BoxView`模拟单元格边框每个单元格中的元素。

## <a name="almost-real-life-grid-examples"></a>几乎真实网格示例

[ **KeypadGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid)示例使用`Grid`以显示键盘：

[![键盘网格的三个屏幕截图](images/ch17fg12-small.png "键盘网格")](images/ch17fg12-large.png#lightbox "键盘网格")

### <a name="responding-to-orientation-changes"></a>对方向更改作出响应

`Grid`有助于结构的程序的方向更改进行响应。 [ **GridRgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders)示例演示纵向面向 phone 第二行和面向布局的手机上的第二列之间移动元素的方法。

程序初始化`Slider`一系列的 0 到 255 和使用数据绑定，以十六进制格式显示的滑块的值的元素。 因为`Slider`点和.NET 格式字符串设置为十六进制仅适用于整数，值为浮动[ `DoubleToIntConvert` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs)类[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库很有帮助。



## <a name="related-links"></a>相关链接

- [第 17 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [第 17 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [网格](~/xamarin-forms/user-interface/layouts/grid.md)
