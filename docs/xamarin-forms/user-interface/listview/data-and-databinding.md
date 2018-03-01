---
title: "ListView 数据源"
description: "了解如何填充你的数据的 ListView。"
ms.topic: article
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 78659aff0b6b4e6401bec96a55935c141d1199f1
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="listview-data-sources"></a>ListView 数据源

ListView 用于显示数据的列表。 我们将了解如何填充数据，并且我们到选定的项可以将的绑定与 ListView。

- **[设置 ItemsSource](#ItemsSource)**  &ndash;使用简单列表或数组。
- **[数据绑定](#Data_Binding)** &ndash;模型和列表视图之间建立关系。 绑定是 MVVM 模式的理想选择。

## <a name="itemssource"></a>ItemsSource
ListView 填充数据使用`ItemsSource`属性，可以接受任何集合实现`IEnumerable`。 最简单的方法来填充`ListView`涉及使用一个字符串数组：

```csharp
var listView = new ListView();
listView.ItemsSource = new string[]{
  "mono",
  "monodroid",
  "monotouch",
  "monorail",
  "monodevelop",
  "monotone",
  "monopoly",
  "monomodal",
  "mononucleosis"
};

//monochrome will not appear in the list because it was added
//after the list was populated.
listView.ItemsSource.Add("monochrome");
```

![](data-and-databinding-images/itemssource-simple.png "ListView 显示的字符串列表")

上面的方法将填充`ListView`的字符串的列表。 默认情况下，`ListView`将调用`ToString`并显示在结果`TextCell`每一行。 若要自定义如何显示数据，请参阅[单元格的外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

因为`ItemsSource`已发送到一个数组中的内容不会更新为基础的列表或数组更改。 如果你想要自动更新，如添加、 删除和更改基础列表中项的 ListView，你将需要使用`ObservableCollection`。 [`ObservableCollection`](https://developer.xamarin.com/api/type/System.Collections.ObjectModel.ObservableCollection%3CT%3E/) 在中定义`System.Collections.ObjectModel`和则类似`List`，只不过它可以通知`ListView`的任何更改：

```csharp
ObservableCollection<Employees> employeeList = new ObservableCollection<Employess>();
listView.ItemsSource = employeeList;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employeeList.Add(new Employee(){ DisplayName="Mr. Mono"});
```

<a name="Data_Binding" />

## <a name="data-binding"></a>数据绑定
数据绑定是将用户界面对象的属性绑定到某个 CLR 对象，如你视图模型中的类的属性"粘附"。 数据绑定很有用，因为它通过替换大量无聊样板文件代码简化了开发用户界面。

数据绑定的工作原理是对象保留为同步的因为其绑定的值更改。 而无需编写事件处理程序，每次将控件的值更改时，你将建立绑定，并在你 ViewModel 启用绑定。

有关数据绑定的详细信息，请参阅[数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)这四个是部分[Xamarin.Forms XAML 基础知识文章系列](~/xamarin-forms/xaml/xaml-basics/index.md)。

### <a name="binding-cells"></a>单元格绑定
单元格 （和单元格的子级） 的属性可以绑定到中的对象的属性`ItemsSource`。 例如，ListView 无法用于提供使用映像的员工的列表。

Employee 类中：

```csharp
public class Employee{
    public string DisplayName {get; set;}
}
```

`ObservableCollection<Employee>` 创建并设置为`ListView`的`ItemsSource`:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
public EmployeeListPage()
{
  //defined in XAML to follow
  EmployeeView.ItemsSource = employees;
  ...
}
```

列表中填充了数据：

```csharp
public EmployeeListPage()
{
  ...
  employees.Add(new Employee{ DisplayName="Rob Finnerty"});
  employees.Add(new Employee{ DisplayName="Bill Wrestler"});
  employees.Add(new Employee{ DisplayName="Dr. Geri-Beth Hooper"});
  employees.Add(new Employee{ DisplayName="Dr. Keith Joyce-Purdy"});
  employees.Add(new Employee{ DisplayName="Sheri Spruce"});
  employees.Add(new Employee{ DisplayName="Burt Indybrick"});
}
```

下面的代码段演示`ListView`绑定到的员工列表：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
Title="Employee List">
  <ListView x:Name="EmployeeView">
    <ListView.ItemTemplate>
      <DataTemplate>
        <TextCell Text="{Binding DisplayName}" />
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

尽管它可能已绑定在 XAML 中使用，请注意的绑定是在代码中为简单起见，安装程序。

XAML 的上一位定义`ContentPage`包含`ListView`。 数据源的`ListView`通过设置`ItemsSource`属性。 中的每一行的布局`ItemsSource`中定义`ListView.ItemTemplate`元素。

下面是结果：

![](data-and-databinding-images/bound-data.png "使用数据绑定的 ListView")

### <a name="binding-selecteditem"></a>绑定 SelectedItem

通常，你将需要将绑定到的选定项`ListView`，而不是使用事件处理程序的更改进行响应。 若要执行此操作在 XAML 中，将绑定`SelectedItem`属性：

```xaml
<ListView x:Name="listView"
 SelectedItem="{Binding Source={x:Reference SomeLabel},
 Path=Text}">
 …
</ListView>
```

假设`listView`的`ItemsSource`是列表的字符串，`SomeLabel`将具有绑定到其文本属性`SelectedItem`。



## <a name="related-links"></a>相关链接

- [双向绑定 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [1.4 的发行说明](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 的发行说明](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
