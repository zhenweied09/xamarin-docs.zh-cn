---
title: "第 19 章的摘要。 集合视图"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 73b3ec3e60a8fca5c48f515eab2cbb8359618dbb
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-19-collection-views"></a>第 19 章的摘要。 集合视图

Xamarin.Forms 定义保留集合并显示其元素的三个视图：

- [`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) 是相对较短字符串项的列表，它允许用户选择一个
- [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 通常是较长的项通常是相同类型的列表和格式设置，还允许用户选择一个
- [`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) 是一套*单元格*（通常为各种类型和外观） 若要显示的数据或管理用户输入

很常见的 MVVM 应用程序以使用`ListView`以显示可选择对象的集合。

## <a name="program-options-with-picker"></a>选取器的计划选项

[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)时你需要允许用户选择从相对较短列表的一个选项是一个不错的选择`string`项。

### <a name="the-picker-and-event-handling"></a>选取器和事件处理

[ **PickerDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo)示例演示如何使用 XAML 来设置`Picker` [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/)属性并添加`string`项到[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合。 当用户选择`Picker`，它显示中的项`Items`方式取决于平台的集合。

[ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/)事件表示用户选择某个项。 从零开始[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)属性然后指示所选的项。 如果未不选定任何项，`SelectedIndex`等于 &#x2013;1.

你还可以使用`SelectedIndex`初始化，所选的项，但它必须设置后`Items`填充集合。 在 XAML 中，这意味着，你可能将使用的属性元素来设置`SelectedIndex`。

### <a name="data-binding-the-picker"></a>数据绑定选取器

`SelectedIndex`属性由可绑定属性但`Items`不是，因此使用数据绑定与`Picker`会很困难。 一种解决方案是使用`Picker`结合[ `ObjectToIndexConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs)如中的一个[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。 [ **PickerBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding)演示这如何工作。

## <a name="rendering-data-with-listview"></a>与 ListView 呈现数据

[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)是派生自的唯一类[ `ItemsView<TVisual>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)它所继承[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/)和[ `ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/)属性。

`ItemsSource` 类型`IEnumerable`但`null`默认情况下并且必须显式初始化或通过数据绑定 （更常见） 设置到集合。 此集合中的项可以是任何类型。

`ListView` 定义[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/)属性已设置为中的项之一`ItemsSource`集合或`null`如果未不选定任何项。 `ListView` 激发[ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/)选择新项目时的事件。

### <a name="collections-and-selections"></a>集合和选择

[ **ListViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList)示例填充`ListView`17 个`Color`中值`List<Color>`集合。 项是可选但默认情况下会显示与其毫无吸引力`ToString`表示形式。 在本章中的几个示例演示如何修复该显示并使它根据需要为具吸引力。

### <a name="the-row-separator"></a>行分隔符

在 iOS 和 Android 的显示上, 一条细线分隔行。 你可以控制这些都可以通过[ `SeparatorVisibiliy` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SeparatorVisibility/)和[ `SeparatorColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SeparatorColor/)属性。 `SeparatorVisibility` 属性是类型[ `SeparatorVisbility` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SeparatorVisibility/)，一个包含两个成员的枚举：

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.SeparatorVisibility.Default/)默认设置
- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.SeparatorVisibility.None/)

### <a name="data-binding-the-selected-item"></a>数据绑定的选定的项

`SelectedItem`由可绑定的属性，支持属性，因此它可以是源或目标的数据绑定。 其默认`BindingMode`是`OneWayToSource`，但它通常是双向数据绑定，尤其是在 MVVM 方案的目标。 [ **ListViewArray** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray)示例演示此类型的绑定。

### <a name="the-observablecollection-difference"></a>ObservableCollection 差异

[ **ListViewLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger)示例集`ItemsSource`属性`ListView`到`List<DateTime>`集合，然后逐渐添加新`DateTime`到集合的对象每秒使用计时器。

但是，`ListView`不会自动更新本身因为`List<T>`集合没有一种通知机制，以指示当项被添加到或从集合中移除。

多更好类用于这种情况下是[ `ObservableCollection<T>` ](https://developer.xamarin.com/api/type/System.Collections.ObjectModel.ObservableCollection%3CT%3E/)中定义`System.Collections.ObjectModel`命名空间。 此类实现[ `INotifyCollectionChanged` ](https://developer.xamarin.com/api/type/System.Collections.Specialized.INotifyCollectionChanged/)接口，从而致使激发[ `CollectionChanged` ](https://developer.xamarin.com/api/event/System.Collections.ObjectModel.ObservableCollection%3CT%3E.CollectionChanged/)项已添加到或删除集合中的属性或在替换或在内移动时的事件集合中。 当`ListView`内部检测到类实现`INotifyCollectionChanged`已设置为其`ItemsSource`属性，它将附加的处理程序`CollectionChanged`事件并更新其显示当集合更改。

[ **ObservableLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger)示例演示如何使用`ObservableCollection`。

### <a name="templates-and-cells"></a>模板和单元格

默认情况下，`ListView`显示其使用每个项的集合中的项`ToString`方法。 更好的方法涉及定义模板，以显示项。

若要体验此功能，可以使用[ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。 此类定义一个静态`All`类型的属性`IList<NamedColor>`，该字符串包含其中有 141`NamedColor`对应的公共字段的对象`Color`结构。

[ **NaiveNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList)示例集`ItemsSource`的`ListView`至此`NamedColor.All`属性，但仅的完全限定类名`NamedColor`对象显示。

`ListView` 需要模板，以显示这些项。 在代码中，你可以设置[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/)属性定义`ItemsView<TVisual>`到[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)对象使用[`DataTemplate`构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Type/)，引用的派生[ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/)类。 `Cell` 具有五个衍生产品：

- [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) &#x2014;包含两个`Label`（从概念上讲） 的视图
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) &#x2014;将添加`Image`查看对 `TextCell`
- [`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/) &#x2014;包含`Entry`的视图 `Label`
- [`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/) &#x2014;包含`Switch`与 `Label`
- [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) &#x2014;可以是任何`View`（可能具有子级）

然后调用[ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DataTemplate.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)和[ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DataTemplate.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)上`DataTemplate`要将数值与相关联对象`Cell`属性，或者对其设置数据绑定`Cell`属性引用中的项的属性`ItemsSource`集合。 此进行了演示[ **TextCellListCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode)示例。

每个项显示的`ListView`、 从该模板后，构造一个小型的可视化树和此可视化树中的项和的元素的属性之间建立数据绑定。 你可以了解此过程通过安装处理程序[ `ItemAppearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemAppearing/)和[ `ItemDisappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemDisappearing/)的事件`ListView`，或使用备用[ `DataTemplate`构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Func%7BSystem.Object%7D/)使用必须创建项的可视化树中每次调用的函数。

[ **TextCellListXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml)完全在 XAML 中显示的功能相同的程序。 A`DataTemplate`标记设置为`ItemTemplate`属性`ListView`，，然后`TextCell`设置为`DataTemplate`。 到集合中项的属性的绑定设置直接在上[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Text/)和[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Detail/)属性`TextCell`。

### <a name="custom-cells"></a>自定义单元格

在 XAML 中就可以设置[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)到`DataTemplate`然后定义自定义可视化树作为[ `View` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ViewCell.View/)属性`ViewCell`。 (`View`作为内容属性的`ViewCell`因此`ViewCell.View`无需使用标记。)[ **CustomNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList)示例演示了这个方法：

[![三重的自定义名为颜色列表的屏幕截图](images/ch19fg11-small.png "自定义名为颜色列表")](images/ch19fg11-large.png#lightbox "自定义名为颜色列表")

获取适合所有平台大小可能会很棘手。 [ `RowHeight` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RowHeight/)属性很有用，但在某些情况下，你将需要采用[ `HasUnevenRows` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.HasUnevenRows/)属性，这是效率较低，但强制`ListView`调整行的大小。 对于 iOS 和 Android，你必须使用这两个属性之一来获取正确的行大小调整。

### <a name="grouping-the-listview-items"></a>对 ListView 项进行分组

`ListView` 支持对项进行分组和在这些组之间导航。 `ItemsSource`属性必须设置为是集合的集合： 对象的`ItemsSource`设置必须为实现`IEnumerable`，和每个项集合中的还必须实现`IEnumerable`。 每个组应包括两个属性： 的组和三个字母缩写的文本说明。

[ `NamedColorGroup` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) library 将创建七个组的`NamedColor`对象。 [ **ColorGroupList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList)示例演示如何将这些组用于[ `IsGroupingEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsGroupingEnabled/)属性`ListView`设置为`true`，和[ `GroupDisplayBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupDisplayBinding/)和[ `GroupShortNameBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupShortNameBinding/)属性绑定到每个组中的属性。

### <a name="custom-group-headers"></a>自定义组标头

可以创建自定义标头`ListView`通过将组`GroupDisplayBinding`具有属性[ `GroupHeaderTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupHeaderTemplate/)定义标头的模板。

### <a name="listview-and-interactivity"></a>ListView 和交互的能力

通常，应用程序获取与用户交互`ListView`通过附加的处理程序`ItemSelected`或[ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/)事件，或通过设置上的数据绑定`SelectedItem`属性。 但某些单元格类型 (`EntryCell`和`SwitchCell`) 允许用户交互，并且也可以创建自定义单元格该本身与用户交互。 [ **InteractiveListView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView)创建 100 个实例的[ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) ，并允许用户更改使用的三个每种颜色`Slider`元素。 该程序还使用[ `ColorToContrastColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs)中[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)。

## <a name="listview-and-mvvm"></a>ListView 和 MVVM

`ListView` MVVM 方案中扮演着重要角色。 每当`IEnumerable`视图模型中存在集合，它通常绑定到`ListView`。 此外，通常集合中的项实现`INotifyPropertyChanged`要将绑定具有在模板中的属性。

### <a name="a-collection-of-viewmodels"></a>一套 Viewmodel

若要浏览此操作，请[ **SchoolOfFineArts** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)库创建基于多个类[XML 数据文件](http://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml)和映像在此虚构学校虚构学生。

[ `Student` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs)类派生自[ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs)。 [ `StudentBody` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs)类是一套`Student`对象，并还派生自`ViewModelBase`。 [ `SchoolViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs)下载 XML 文件并将所有对象都组合。

[ **StudentList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList)程序使用`ImageCell`以显示学生和在其图像`ListView`:

[![三重的学生列表的屏幕截图](images/ch19fg18-small.png "学生列表")](images/ch19fg18-large.png#lightbox "学生列表")

[ **ListViewHeader** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader)示例添加[ `Header` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.Header/)属性，但该只出现在 Android 上。

### <a name="selection-and-the-binding-context"></a>选择和绑定上下文

[ **SelectedStudentDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail)程序绑定`BindingContext`的`StackLayout`到`SelectedItem`属性`ListView`。 这允许程序将显示有关所选学生详细的信息。

### <a name="context-menus"></a>上下文菜单

单元格可以定义一个采用特定于平台的方式实现的上下文菜单。 若要创建此菜单上，添加[ `MenuItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/)对象添加到[ `ContextActions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Cell.ContextActions/)属性`Cell`。

`MenuItem` 定义五个属性：

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Text/) 类型 `string`
- [`Icon`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Icon/) 类型 `FileImageSource`
- [`IsDestructive`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.IsDestructive/) 类型 `bool`
- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Command/) 类型 `ICommand`
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.CommandParameter/) 类型 `object`

`Command`和`CommandParameter`属性表示每个项 ViewModel 包含用于执行所需的菜单命令的方法。 在非 MVVM 方案中，`MenuItem`还定义[ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.MenuItem.Clicked/)事件。

[ **CellContextMenu** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu)演示这种方法。 `Command`每个属性`MenuItem`绑定到类型的属性`ICommand`中`Student`类。 设置`IsDestructive`属性`true`为`MenuItem`，移除或删除所选的对象。

### <a name="varying-the-visuals"></a>不同的视觉对象

有时，您将希望在视觉对象中的项的稍有不同`ListView`根据属性。 例如，当学生的评分点平均值低于 2.0，时才[ **ColorCodedStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents)示例以红色显示该学生的名称。
这通过使用的绑定值转换器，来实现[ `ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。

### <a name="refreshing-the-content"></a>正在刷新的内容

`ListView`支持手势刷新其数据。 程序，必须设置[ `IsPullToRefresh` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsPullToRefreshEnabled/)属性`true`要启用此功能。 `ListView`响应笔势通过设置其[ `IsRefreshing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsRefreshing/)属性`true`，并通过引发[ `Refreshing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.Refreshing/)事件和 （对于 MVVM 方案） 调用`Execute`方法其[ `RefreshCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RefreshCommand/)属性。

代码处理`Refresh`事件或`RefreshCommand`然后可能更新所显示的数据`ListView`和设置`IsRefreshing`回`false`。

[ **RssFeed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed)示例演示如何使用[ `RssFeedViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs)实现`RefreshCommand`和`IsRefreshing`数据绑定属性。

## <a name="the-tableview-and-its-intents"></a>TableView 和其意向

虽然`ListView`通常显示的相同类型的多个实例[ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)通常侧重于各种类型的多个属性提供的用户界面。 每个项都有其自己关联[ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/)派生，用于显示属性，或提供给它的用户界面。

### <a name="properties-and-hierarchies"></a>属性和层次结构

`TableView` 定义只需四个属性：

- [`Intent`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Intent/) 类型的[ `TableIntent` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableIntent/)，枚举
- [`Root`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Root/) 类型的[ `TableRoot`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/)的内容属性 `TableView`
- [`RowHeight`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.RowHeight/) 类型 `int`
- [`HasUnevenRows`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.HasUnevenRows/) 类型 `bool`

`TableIntent`枚举指示你想要使用`TableView`:

- [`Data`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Data/)
- [`Form`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Form/)
- [`Settings`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Settings/)
- [`Menu`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Menu/)

这些成员还建议的一些用途`TableView`。

定义表涉及多个其他类：

- [`TableSectionBase`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSectionBase/) 是一个抽象类派生自`BindableObject`并定义[ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TableSectionBase.Title/)属性

- [`TableSectionBase<T>`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSectionBase%3CT%3E/) 是一个抽象类派生自`TableSectionBase`并实现`IList<T>`和 `INotifyCollectionChanged`

- [`TableSection`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSection/) 派生自 `TableSectionBase<Cell>`

- [`TableRoot`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/) 派生自 `TableSectionBase<TableSection>`

简单地说，`TableView`具有`Root`属性设置为`TableRoot`对象，这是集合的`TableSection`对象，其中每个是一套`Cell`对象。 表具有多个部分中，并且每个部分具有多个单元格。 表本身可以有标题，并且每个部分可以标题。 尽管`TableView`利用`Cell`衍生产品，它不会进行使用`DataTemplate`。

### <a name="a-prosaic-form"></a>实际的窗体

[ **EntryForm** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm)示例定义[ `PersonalInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)视图模型，其中实例变为`BindingContext`的`TableView`。 每个`Cell`中派生其`TableSection`然后可以绑定到的属性`PersonalInformation`类。

### <a name="custom-cells"></a>自定义单元格

[ **ConditionalCells** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells)示例扩展**EntryForm**。 [ `ProgrammerInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)类包括布尔值属性，控制对两个附加属性的适用性。 有关这两个其他属性，该程序使用自定义`PickerCell`基于[PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml)和[PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。

尽管`IsEnabled`这两个属性`PickerCell`元素绑定到中的布尔属性`ProgrammerInformation`，此方法似乎不起作用，随后系统会提示下一个示例。

### <a name="conditional-sections"></a>条件节

[ **ConditionalSection** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection)示例放入了于在单独的布尔值项选择条件的两个项`TableSection`。 代码隐藏文件中删除此部分从`TableView`或将它返回基于的布尔属性。

### <a name="a-tableview-menu"></a>TableView 菜单

另一个用途`TableView`是一个菜单。 [ **MenuCommands** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands)示例演示一个菜单，让你将稍有移动`BoxView`在屏幕。



## <a name="related-links"></a>相关链接

- [第 19 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [第 19 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
