---
title: 创建 Xamarin.Forms DataTemplate
description: 数据模板可以以内联方式创建的 ResourceDictionary、 中或从自定义类型或适当的 Xamarin.Forms 单元格类型。 本文探讨了每种技术。
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 63f9bf82bc8e637aced1afa5d5699ac1e8dc3f8c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994610"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>创建 Xamarin.Forms DataTemplate

_数据模板可以以内联方式创建的 ResourceDictionary、 中或从自定义类型或适当的 Xamarin.Forms 单元格类型。本文探讨了每种技术。_

有关的常见使用方案[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)显示在对象的集合中的数据[ `ListView` ](xref:Xamarin.Forms.ListView)。 在每个单元格的数据的外观[ `ListView` ](xref:Xamarin.Forms.ListView)可以设置管理[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)属性设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。 有许多可以用于实现此目的的方法：

- [创建内联 DataTemplate](#inline)。
- [使用一种类型创建 DataTemplate](#type)。
- [创建为资源 DataTemplate](#resource)。

无论所使用的技术，其结果是，在每个单元格的外观[ `ListView` ](xref:Xamarin.Forms.ListView)由定义[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，如以下屏幕截图所示：

![](creating-images/data-template-appearance.png "使用 DataTemplate 的 ListView")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>创建内联 DataTemplate

[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)属性可以设置为内联[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。 如果不需要重复利用其他位置的数据模板，应使用一个内联模板，这是指位于作为相应的控件属性的直接子级。 中指定的元素`DataTemplate`定义每个单元格的外观，如下面的 XAML 代码示例中所示：

```xaml
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
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Label Text="{Binding Name}" FontAttributes="Bold" />
                    <Label Grid.Column="1" Text="{Binding Age}" />
                    <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

内嵌元素的子[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)必须是的或从派生，键入[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)。 内的布局`ViewCell`此处由[ `Grid` ](xref:Xamarin.Forms.Grid)。 `Grid`包含三种[ `Label` ](xref:Xamarin.Forms.Label)实例的绑定及其[ `Text` ](xref:Xamarin.Forms.Label.Text)属性设置为相应的属性的每个`Person`集合中的对象。

以下代码示例显示相应的 C# 代码：

```csharp
public class WithDataTemplatePageCS : ContentPage
{
    public WithDataTemplatePageCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        var personDataTemplate = new DataTemplate(() =>
        {
            var grid = new Grid();
            ...
            var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
            var ageLabel = new Label();
            var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

            nameLabel.SetBinding(Label.TextProperty, "Name");
            ageLabel.SetBinding(Label.TextProperty, "Age");
            locationLabel.SetBinding(Label.TextProperty, "Location");

            grid.Children.Add(nameLabel);
            grid.Children.Add(ageLabel, 1, 0);
            grid.Children.Add(locationLabel, 2, 0);

            return new ViewCell { View = grid };
        });

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemsSource = people, ItemTemplate = personDataTemplate, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

在 C# 中，内联[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)创建使用指定的构造函数重载`Func`参数。

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>使用一种类型创建 DataTemplate

[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)属性也设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)创建从单元格类型。 此方法的优点是整个应用程序的多个数据模板可以重用单元格类型所定义的外观。 下面的 XAML 代码演示此方法的一个示例：

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
                    ...
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <local:PersonCell />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

在这里， [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)属性设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)创建自定义单元格的外观的自定义类型。 自定义的类型必须派生自类型[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，下面的代码示例中所示：

```xaml
<ViewCell xmlns="http://xamarin.com/schemas/2014/forms"
          xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
          x:Class="DataTemplates.PersonCell">
     <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.2*" />
            <ColumnDefinition Width="0.3*" />
        </Grid.ColumnDefinitions>
        <Label Text="{Binding Name}" FontAttributes="Bold" />
        <Label Grid.Column="1" Text="{Binding Age}" />
        <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
    </Grid>
</ViewCell>
```

内[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，通过此处管理布局[ `Grid` ](xref:Xamarin.Forms.Grid)。 `Grid`包含三种[ `Label` ](xref:Xamarin.Forms.Label)实例的绑定及其[ `Text` ](xref:Xamarin.Forms.Label.Text)属性设置为相应的属性的每个`Person`集合中的对象。

等效的 C# 代码如以下示例所示：

```csharp
public class WithDataTemplatePageFromTypeCS : ContentPage
{
    public WithDataTemplatePageFromTypeCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemTemplate = new DataTemplate(typeof(PersonCellCS)), ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

在 C# 中， [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)创建使用指定的单元格类型作为参数的构造函数重载。 单元格类型必须派生自类型[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，下面的代码示例中所示：

```csharp
public class PersonCellCS : ViewCell
{
    public PersonCellCS()
    {
        var grid = new Grid();
        ...
        var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        var ageLabel = new Label();
        var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

        nameLabel.SetBinding(Label.TextProperty, "Name");
        ageLabel.SetBinding(Label.TextProperty, "Age");
        locationLabel.SetBinding(Label.TextProperty, "Location");

        grid.Children.Add(nameLabel);
        grid.Children.Add(ageLabel, 1, 0);
        grid.Children.Add(locationLabel, 2, 0);

        View = grid;
    }
}
```

> [!NOTE]
> 请注意，Xamarin.Forms 还包括可用于显示简单的数据中的单元格类型[ `ListView` ](xref:Xamarin.Forms.ListView)单元格。 有关详细信息，请参阅[单元格的外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>DataTemplate 创建为资源

数据模板也可以创建可重用对象作为[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 这通过为每个声明提供一个唯一`x:Key`属性，为其提供中的描述性键`ResourceDictionary`，如以下 XAML 代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="personTemplate">
                 <ViewCell>
                    <Grid>
                        ...
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        ...
        <ListView ItemTemplate="{StaticResource personTemplate}" Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)分配给[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)属性使用`StaticResource`标记扩展。 请注意，当`DataTemplate`页中定义[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，也可以在控件级别或应用程序级别定义。

下面的代码示例显示了 C# 中的等效页：

```csharp
public class WithDataTemplatePageCS : ContentPage
{
  public WithDataTemplatePageCS ()
  {
    ...
    var personDataTemplate = new DataTemplate (() => {
      var grid = new Grid ();
      ...
      return new ViewCell { View = grid };
    });

    Resources = new ResourceDictionary ();
    Resources.Add ("personTemplate", personDataTemplate);

    Content = new StackLayout {
      Margin = new Thickness(20),
      Children = {
        ...
        new ListView { ItemTemplate = (DataTemplate)Resources ["personTemplate"], ItemsSource = people };
      }
    };
  }
}
```

[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)添加到[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)使用[ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object))方法，后者指定`Key`用于字符串引用`DataTemplate`时检索它。

## <a name="summary"></a>总结

本文介绍了如何从自定义类型，或在创建数据模板、 内联[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 如果不需要重复利用其他位置的数据模板，应使用内联模板。 或者，可以通过定义它作为自定义的类型，或作为控件级别页级别或应用程序级别资源重复使用数据模板。


## <a name="related-links"></a>相关链接

- [单元格外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [数据模板 （示例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [数据模板](xref:Xamarin.Forms.DataTemplate)
