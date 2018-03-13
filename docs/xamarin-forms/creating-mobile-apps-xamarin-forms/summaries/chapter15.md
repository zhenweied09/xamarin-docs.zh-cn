---
title: "第 15 章的摘要。 交互式接口"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 37bbbf2b89048db175ddc1b3b1a24cea92b898a7
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>第 15 章的摘要。 交互式接口

此章节介绍八个`View`允许与用户交互的衍生产品。

## <a name="view-overview"></a>查看概述

Xamarin.Forms 包含 20 可实例化的类派生自`View`但不是`Layout`。 第六个已涉及的上一章节：

- `Label`: [**第 2 章。应用程序的剖析**](chapter02.md)
- `BoxView`: [**第 3 章。滚动堆栈**](chapter03.md)
- `Button`: [**第 6 章。按钮单击事件**](chapter06.md)
- `Image`: [**章 13。Bitmaps**](chapter13.md)
- `ActivityIndicator`: [**章 13。Bitmaps**](chapter13.md)
- `ProgressBar`: [ **14 章。AbsoluteLayout**](chapter14.md)

本章中的八个视图有效地允许用户与基本.NET 数据类型进行交互：

<table>
  <tr>
    <th>数据类型</th>
    <th>视图</th>
  </tr>
  <tr>
    <td>`Double`</td>
    <td
      <code><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/">Slider</a></code>,
      <code><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/">Stepper</a></code>
    </td>
  </tr>
  <tr>
    <td>`Boolean`</td>
    <td>
      <code><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/">Switch</a></code>
    </td>
  </tr>
  <tr>
    <td>`String`</td>
    <td>
      <code><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">Entry</a></code>, <code><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/">Editor</a></code>, <code><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/">SearchBar</a></code>
    </td>
  </tr>
  <tr>
    <td>`DateTime`</td>
    <td>
      <code><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/">DatePicker</a></code>, <code><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/">TimePicker</a></code>
    </td>
  </tr>
</table>

可以将这些视图作为视觉交互式表示形式的基础数据类型。 在下一章，更探讨这一概念[**章 16。数据绑定**](chapter16.md)。

以下章节中介绍的剩余的六个视图：

- `WebView`: [**章 16。数据绑定**](chapter16.md)
- `Picker`: [**第 19 章。集合视图**](chapter19.md)
- `ListView`: [**第 19 章。集合视图**](chapter19.md)
- `TableView`: [**第 19 章。集合视图**](chapter19.md)
- `Map`: [**章 28。位置和映射**](chapter28.md)
- `OpenGLView`： 并不涵盖在本书 （和 Windows 平台不支持）

## <a name="slider-and-stepper"></a>滑块和分档器

同时[ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)和[ `Stepper` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)允许用户从一系列选择的数字值。 `Slider`是一个连续范围时`Stepper`涉及离散值。

### <a name="slider-basics"></a>滑块基础知识

[ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)是一根水平线表示一系列的值从左侧的最小值到最右侧。 它定义三个公共属性：

- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Value/) 类型的`double`，默认值为 0
- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) 类型的`double`，默认值为 0
- [`Maximum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) 类型的`double`，默认值为 1

备份这些属性可绑定属性确保它们一致：

- 对于所有三个属性， [ `coerceValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+CoerceValueDelegate/)方法指定的可绑定的属性可确保`Value`之间`Minimum`和`Maximum`。
- [ `validateValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+ValidateValueDelegate/)方法`MinimumProperty`返回`false`如果`Minimum`大于或等于设置一个值为`Maximum`，以及类似的`MaximumProperty`。 返回`false`从`validateValue`方法使`ArgumentException`被引发。

`Slider` 激发[ `ValueChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Slider.ValueChanged/)事件和[ `ValueChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ValueChangedEventArgs/)自变量时`Value`属性更改，以编程方式或当用户操作`Slider`。

[ **SliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo)示例演示如何将简单的`Slider`。

### <a name="common-pitfalls"></a>常见的问题

在代码和在 XAML 中，`Minimum`和`Maximum`在你指定的顺序设置属性。 请务必初始化这些属性以便`Maximum`是否始终大于`Minimum`。 否则，将引发异常。

初始化`Slider`属性可能会导致`Value`属性可以更改和`ValueChanged`要激发的事件。 你应确保`Slider`事件处理程序不访问尚未尚未在页初始化期间创建的视图。

`ValueChanged`期间不会激发事件`Slider`初始化除非`Value`属性更改。 你可以调用`ValueChanged`直接从代码处理程序。

### <a name="slider-color-selection"></a>滑块颜色选择

[ **RgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders)程序包含三种`Slider`允许你以交互方式选择通过指定其 RGB 值的一种颜色的元素：

[![R G B 滑块的三个屏幕截图](images/ch15fg03-small.png "RGB 滑块")](images/ch15fg03-large.png#lightbox "RGB 滑块")

[ **TextFade** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade)示例使用两个`Slider`移动两个元素`Label`跨元素`AbsoluteLayout`和淡入淡出到另一个。

### <a name="the-stepper-difference"></a>分档器差异

[ `Stepper` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)定义相同的属性和事件作为`Slider`但`Maximum`属性初始化为 100 和`Stepper`定义的第四个属性：

- [`Increment`](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Increment/) 类型的`double`、 初始化为 1

您可以看到，`Stepper`包含两个按钮标记为**& #x 2013;**和 **+** 。 按**& #x 2013;**减少`Value`通过`Increment`到最小`Minimum`。 按 **+** 增加`Value`通过`Increment`到最大`Maximum`。

说明了这一点通过[ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo)示例。

## <a name="switch-and-checkbox"></a>交换机和复选框

[ `Switch` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)允许用户指定一个布尔值。

### <a name="switch-basics"></a>切换基础知识

您可以看到，`Switch`包含可关闭和打开打开切换。 类定义一个属性：

- [`IsToggled`](https://developer.xamarin.com/api/property/Xamarin.Forms.Switch.IsToggled/) 类型 `bool`

`Switch` 定义一个事件：

- [`Toggled`](https://developer.xamarin.com/api/event/Xamarin.Forms.Switch.Toggled/) 伴随[ `ToggledEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToggledEventArgs/)对象，当激发`IsToggled`属性更改。

[ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo)程序演示`Switch`。

### <a name="a-traditional-checkbox"></a>传统的复选框

一些开发人员可能希望更传统`CheckBox`到`Switch`。 [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库包含`CheckBox`派生自的类`ContentView`。 `CheckBox` 由实现[CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml)和[CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs)文件。 `CheckBox` 定义三个属性 (`Text`， `FontSize`，和`IsChecked`) 和一个`CheckedChanged`事件。

[ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo)示例演示了这`CheckBox`。

## <a name="typing-text"></a>键入文本

Xamarin.Forms 定义三个视图，让用户输入和编辑文本：

- [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) 为单个文本行
- [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) 为多行文本
- [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) 为单个供搜索的文本行。

`Entry` 和`Editor`派生自[ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/)，它派生自`View`。 `SearchBar` 直接从派生`View`。

### <a name="keyboard-and-focus"></a>键盘和焦点

在手机和平板电脑，而无需物理键盘上`Entry`， `Editor`，和`SearchBar`所有的元素会导致弹出的虚拟键盘。 在屏幕上的此键盘存在与相关输入焦点。 视图必须同时具有其[ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/)和[ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/)属性设置为`true`获取输入的焦点。

具有输入焦点涉及两种方法，一个只读属性，并且两个事件。 这些都被定义为通过`VisualElement`:

- [ `Focus` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Focus()/)方法尝试设置输入的焦点的元素，并返回`true`如果成功
- [ `Unfocus` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Unfocus()/)方法从元素中删除输入的焦点
- [ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/)只读属性指示是否元素具有输入焦点
- [ `Focused` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.Focused/)事件指示某个元素时获取输入的焦点
- [ `Unfocused` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.Unfocused/)事件指示某个元素时失去输入的焦点

### <a name="choosing-the-keyboard"></a>选择键盘

[ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/)从中类`Entry`和`Editor`派生定义仅一个属性：

- [`Keyboard`](https://developer.xamarin.com/api/property/Xamarin.Forms.InputView.Keyboard/) 类型 [`Keyboard`](https://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/)

此设置指示显示键盘的类型。 某些键盘进行了优化的 Uri 或数字。

`Keyboard`类允许定义与静态键盘[ `Keyboard.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Keyboard.Create/p/Xamarin.Forms.KeyboardFlags/)用类型自变量的方法[ `KeyboardFlags` ](https://developer.xamarin.com/api/type/Xamarin.Forms.KeyboardFlags/)，一个包含以下的位标志的枚举：

- `None` 设置为 0
- [`CapitalizeSentence`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.CapitalizeSentence/) 设置为 1
- [`Spellcheck`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.Spellcheck/) 设置为 2
- [`Suggestions`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.Suggestions/) 将设置为 4
- [`All`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.All/) 设置为 \xFFFFFFFF

使用多行时[ `Editor` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/)何时需要一段或多个文本调用`Keyboard.Create`是选择键盘的好方法。 为单线条[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)，以下静态只读属性的`Keyboard`非常有用：

- [`Default`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Default/)
- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Text/)
- [`Chat`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Chat/)
- [`Url`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Url/)
- [`Email`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Email/)
- [`Telephone`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Telephone/)
- [`Numeric`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Numeric/) 对于使用或不带小数点的正数。

[ `KeyboardTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.KeyboardTypeConverter/)允许在 XAML 中指定这些属性，如所示： [ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards)程序。

### <a name="entry-properties-and-events"></a>输入属性和事件

单行[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)定义以下属性：

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) 类型的`string`，在显示的文本 `Entry`
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/) 类型 `Color`
- [`FontFamily`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontFamily/) 类型 `string`
- [`FontSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontSize/) 类型 `double`
- [`FontAttributes`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontAttributes/) 类型 `FontAttributes`
- [`IsPassword`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.IsPassword/) 类型的`bool`，这将导致将要屏蔽的字符
- [`Placeholder`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Placeholder/) 类型的`string`，dimly 彩色中显示的文本`Entry`键入任何内容之前
- [`PlaceholderColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.PlaceholderColor/) 类型 `Color`

`Entry`还定义了两个事件：

- [`TextChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) 与[ `TextChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextChangedEventArgs/)对象，每当激发`Text`属性更改
- [`Completed`](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/)在用户已完成并关闭键盘时触发。 用户可以指示特定于平台的方式完成

[ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations)示例演示这两个事件。

### <a name="the-editor-difference"></a>编辑器差异

多行[ `Editor` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/)定义相同`Text`和`Font`属性作为`Entry`但不是包括其他属性。 `Editor` 此外定义了相同的两种属性视为`Entry`。

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes)是保存和还原的内容的自由格式说明拍摄程序`Editor`。

### <a name="the-searchbar"></a>SearchBar

[ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)不是派生自`InputView`，因此它不具有`Keyboard`属性。 但它具有所有`Text`， `Font`，和`Placeholder`属性，`Entry`定义。 此外，`SearchBar`定义其他三个属性：

- [`CancelButtonColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.CancelButtonColor/) 类型 `Color`
- [`SearchCommand`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommand/) 类型的[ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/)用于数据绑定和 MVVM
- [`SearchCommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommandParameter/) 类型的`Object`，以用于 `SearchCommand`

特定于平台的取消按钮擦除文本。 `SearchBar`还具有特定于平台的搜索按钮。 按这些按钮任一引发两个事件之一的`SearchBar`定义：

- [`TextChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.TextChanged/) 伴随[ `TextChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextChangedEventArgs/)对象
- [`SearchButtonPressed`](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.SearchButtonPressed/)

[ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo)示例演示如何`SearchBar`。

## <a name="date-and-time-selection"></a>日期和时间选择

[ `DatePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)和[ `TimePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/)视图实现允许用户指定的日期或时间的特定于平台的控件。

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) 定义四个属性：

- [`MinimumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) 类型的`DateTime`、 初始化为 1900 年 1 月 1 日
- [`MaximumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) 类型的`DateTime`、 初始化为 2100 年 12 月 31，
- [`Date`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) 类型的`DateTime`、 初始化为 `DateTime.Today`
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Format/) 类型的`string`、.NET 格式化字符串初始化为"d"，短日期模式中，从而导致类似"7/20/1969"在美国日期显示。

你可以设置`DateTime`属性在 XAML 中通过表达为属性元素的属性并使用区域性固定短日期格式 ("7/20/1969")。   

[ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates)示例计算的用户选择的两个日期之间的天数。

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>TimePicker （或者是 TimeSpanPicker？）

[`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) 定义两个属性和任何事件：

- [`Time`](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Time/) 类型`TimeSpan`而非`DateTime`，自午夜以来经过的指示的时间
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Format/) 类型的`string`、.NET 美国太平洋时区中的格式设置字符串初始化为"t"，导致类似"下午 1:45"时显示的短时间模式。

[ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer)程序演示如何使用`TimePicker`计时器指定时间。 当保持在前台，程序才有效。

**SetTimer**还演示了如何使用[ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/)方法`Page`以显示一个警报框。



## <a name="related-links"></a>相关链接

- [章 15 的全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [章 15 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [项](~/xamarin-forms/user-interface/text/entry.md)
- [编辑器](~/xamarin-forms/user-interface/text/editor.md)
