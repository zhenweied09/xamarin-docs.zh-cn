---
title: "介绍"
description: "Xamarin.Forms 数据模板提供受支持的控件上定义数据的表示形式的功能。 本文介绍数据模板，检查它们是所必需的原因。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: e7c1a8b233538b7ad40a18e44747bef672210516
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="introduction"></a>介绍

_Xamarin.Forms 数据模板提供受支持的控件上定义数据的表示形式的功能。本文介绍数据模板，检查它们是所必需的原因。_

请考虑[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)显示的集合`Person`对象。 下面的代码示例演示的定义`Person`类：

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

`Person`类定义`Name`， `Age`，和`Location`属性时可以设置`Person`创建对象。 [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)用于显示的集合`Person`对象，如下面的 XAML 代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataTemplates"
             ...>
    <StackLayout Margin="20">
        ...
        <ListView Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    <local:Person Name="John" Age="37" Location="USA" />
                    <local:Person Name="Tom" Age="42" Location="UK" />
                    <local:Person Name="Lucas" Age="29" Location="Germany" />
                    <local:Person Name="Tariq" Age="39" Location="UK" />
                    <local:Person Name="Jane" Age="30" Location="USA" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

项被添加到[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)在 XAML 中初始化[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/)从数组的属性`Person`实例。

> [!NOTE]
> 请注意，`x:Array`元素需要`Type`，该值指示数组中的项的类型的属性。

在下面的代码示例初始化显示等效的 C# 页[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/)属性`List`的`Person`实例：

```csharp
public WithoutDataTemplatePageCS()
{
    ...
    var people = new List<Person>
    {
        new Person { Name = "Steve", Age = 21, Location = "USA" },
        new Person { Name = "John", Age = 37, Location = "USA" },
        new Person { Name = "Tom", Age = 42, Location = "UK" },
        new Person { Name = "Lucas", Age = 29, Location = "Germany" },
        new Person { Name = "Tariq", Age = 39, Location = "UK" },
        new Person { Name = "Jane", Age = 30, Location = "USA" }
    };

    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children = {
            ...
            new ListView { ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
        }
    };
}
```

[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)调用`ToString`时显示的对象集合中。 因为没有任何`Person.ToString`重写，`ToString`返回的每个对象的类型名称，如以下屏幕截图中所示：

![](introduction-images/no-data-template.png "不使用数据模板的 ListView")

`Person`对象可以重写`ToString`方法以显示有意义的数据，如下面的代码示例中所示：

```csharp
public class Person
{
  ...
  public override string ToString ()
  {
    return Name;
  }
}
```

这会导致[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)显示`Person.Name`在集合中，如以下屏幕截图中所示的每个对象的属性值：

![](introduction-images/override-tostring.png "使用数据模板的 ListView")

`Person.ToString`重写可能会返回一个包含的格式化的字符串`Name`， `Age`，和`Location`属性。 但是，此方法提供仅对消息数据的每个项的外观有限的控制。 为更大的灵活性， [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)可以创建用于定义数据的外观。

## <a name="creating-a-datatemplate"></a>创建 DataTemplate

A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)可用于指定外观的数据，并通常使用数据绑定来显示数据。 其常见使用方案不显示数据，集合中的对象时[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。 例如，当`ListView`绑定到的集合`Person`对象，`ListView.ItemTemplate`属性将设置为`DataTemplate`，它定义每个外观`Person`对象在`ListView`。 `DataTemplate`将包含将绑定到每个属性值的元素`Person`对象。 若要深入了解数据绑定，请参阅[数据绑定基本知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)可以用作以下属性值：

- [`ListView.HeaderTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.HeaderTemplate/)
- [`ListView.FooterTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.FooterTemplate/)
- [`ListView.GroupHeaderTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupHeaderTemplate/)
- [`ItemsView.ItemTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)由继承[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。
- [`MultiPage.ItemTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/)由继承[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)， [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)，和[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)。

> [!NOTE]
> 请注意，虽然[ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)使使用[ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/)对象，它不使用[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)。 这是因为直接在上始终设置数据绑定`Cell`对象。

A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)如上面列出的属性的直接子级称为放置*内联模板*。 或者，`DataTemplate`可以定义为控件级别、 页级或应用程序级别资源。 选择定义的位置[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)可以使用它的影响：

- A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)定义在控件级别只能应用到控件。
- A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)定义在页级别可以应用于页面上的多个有效的控件。
- A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)在应用程序级别定义可以在整个应用程序应用于有效控件。

数据模板视图层次结构中较低级别优先于它们共享时，更高版本定义向上`x:Key`属性。 例如，页级数据模板，将被替代的应用程序级数据模板和控制级别数据模板或内联数据模板将替代页面级别数据模板。


## <a name="related-links"></a>相关链接

- [单元格外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [数据模板 （示例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [数据模板](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)
