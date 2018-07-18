---
title: ListView 数据源
description: 此文章介绍了如何填充数据，使用 Xamarin.Forms ListView 以及如何使用 ListView 的数据绑定。
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 17c353844a7ddc808e5d9f0632434472913170a4
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995201"
---
# <a name="listview-data-sources"></a>ListView 数据源

ListView 用于显示数据的列表。 我们将了解有关填充 ListView 与数据和如何我们可以绑定到选定的项。

- **[设置 ItemsSource](#ItemsSource)**  &ndash;使用简单列表或数组。
- **[数据绑定](#Data_Binding)** &ndash;模型和 ListView 之间建立关系。 绑定适合于 MVVM 模式。

## <a name="itemssource"></a>ItemsSource
使用数据填充 ListView`ItemsSource`属性，它可以接受任何集合实现`IEnumerable`。 最简单的方法来填充`ListView`，需使用一个字符串数组：

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

上述方法将填充`ListView`与字符串的列表。 默认情况下`ListView`将调用`ToString`并显示在结果`TextCell`每个行。 若要自定义数据的显示方式，请参阅[单元格的外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

因为`ItemsSource`已发送到一个数组中的内容不会更新为基础的列表或数组更改。 如果你想要自动更新，因为添加、 删除和更改的基础列表中的项时 ListView，您将需要使用`ObservableCollection`。 [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) 在中定义`System.Collections.ObjectModel`和类似于`List`，只不过它可以通知`ListView`的任何更改：

```csharp
ObservableCollection<Employees> employeeList = new ObservableCollection<Employess>();
listView.ItemsSource = employeeList;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employeeList.Add(new Employee(){ DisplayName="Mr. Mono"});
```

<a name="Data_Binding" />

## <a name="data-binding"></a>数据绑定
数据绑定是将用户界面对象的属性绑定到某些 CLR 对象，如在 ViewModel 中的类的属性"粘合"。 数据绑定很有用，因为它简化了用户界面的开发，通过替换大量繁琐样板代码。

数据绑定的工作原理是在其绑定的值更改时保持对象的同步。 而无需编写事件处理程序，每次控件的值更改时，可以建立绑定，并在 ViewModel 中启用绑定。

数据绑定的详细信息，请参阅[数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)这四个是一部分[Xamarin.Forms XAML 基础知识文章系列](~/xamarin-forms/xaml/xaml-basics/index.md)。

### <a name="binding-cells"></a>单元格绑定
单元格 （和单元格的子项） 的属性可以绑定到对象中的属性`ItemsSource`。 例如，ListView 可以用于显示图像的员工的列表。

Employee 类：

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

以下代码片段演示`ListView`绑定到的员工列表：

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

尽管它可能已绑定在 XAML 中，请注意的绑定是在代码中为简单起见，安装程序。

XAML 的上一位定义`ContentPage`，其中包含`ListView`。 数据源`ListView`通过设置`ItemsSource`属性。 中的每一行的布局`ItemsSource`中定义`ListView.ItemTemplate`元素。

下面是结果：

![](data-and-databinding-images/bound-data.png "使用数据绑定的 ListView")

### <a name="binding-selecteditem"></a>绑定 SelectedItem

通常你会想要绑定到的所选的项`ListView`，而不是不是使用事件处理程序的更改进行响应。 若要执行此操作在 XAML 中，将绑定`SelectedItem`属性：

```xaml
<ListView x:Name="listView"
 SelectedItem="{Binding Source={x:Reference SomeLabel},
 Path=Text}">
 …
</ListView>
```

假设`listView`的`ItemsSource`是一个字符串，列表`SomeLabel`将具有其 text 属性绑定到`SelectedItem`。



## <a name="related-links"></a>相关链接

- [两个双向绑定 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [1.4 的发行说明](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 的发行说明](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
