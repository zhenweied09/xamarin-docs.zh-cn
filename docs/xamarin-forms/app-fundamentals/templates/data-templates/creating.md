---
title: 创建 DataTemplate
description: 数据模板可以内联，创建的 ResourceDictionary 中或从自定义的类型或相应 Xamarin.Forms 单元格类型。 本文探讨了每种技术。
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: cd4266fb8e7d68a9f93bd169ca70c6a0f516a357
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-datatemplate"></a>创建 DataTemplate

_数据模板可以内联，创建的 ResourceDictionary 中或从自定义的类型或相应 Xamarin.Forms 单元格类型。本文探讨了每种技术。_

有关的常见使用方案[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)所显示数据中的对象的集合从[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。 在每个单元格的数据的外观[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)可以通过设置管理[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)属性[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)。 有多种方法可以用于实现此目的：

- [创建内联 DataTemplate](#inline)。
- [使用类型创建 DataTemplate](#type)。
- [创建作为资源 DataTemplate](#resource)。

无论正在使用的技术，结果是，在每个单元格的外观[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)由定义[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)，如以下屏幕截图中所示：

![](creating-images/data-template-appearance.png "使用 DataTemplate 的 ListView")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>创建内联数据模板

[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)属性可以设置为内联[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)。 如果没有无需重复使用在其他位置的数据模板，则应使用的内联模板，这是一个位于相应的控件属性的直接子级。 中指定的元素`DataTemplate`定义每个单元格的外观，如下面的 XAML 代码示例中所示：

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

内联的子[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)必须的或从派生，请键入[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)。 在布局`ViewCell`此处由[ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)。 `Grid`包含三种[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例该绑定其[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)属性设置为适当的属性的每个`Person`集合中的对象。

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

在 C# 中，内联[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)创建使用指定的构造函数重载`Func`自变量。

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>使用类型创建 DataTemplate

[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)属性还可以设置为[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)从单元格类型创建。 此方法的优点是在整个应用程序的多个数据模板可以重用由单元格类型定义的外观。 下面的 XAML 代码演示此方法的一个示例：

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

在这里， [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)属性设置为[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)从定义的单元格外观的自定义类型创建。 自定义的类型必须派生自类型[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)，下面的代码示例中所示：

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

在[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)，布局由此处[ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)。 `Grid`包含三种[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例该绑定其[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)属性设置为适当的属性的每个`Person`集合中的对象。

等效的 C# 代码将显示在下面的示例：

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

在 C# 中， [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)创建使用指定的单元格类型作为自变量的构造函数重载。 单元格类型必须派生自类型[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)，下面的代码示例中所示：

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
> 请注意，Xamarin.Forms 还包含可以用于显示中的简单数据的单元格类型[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)单元格。 有关详细信息，请参阅[单元格的外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>为资源创建 DataTemplate

数据模板也可以创建为可重用对象[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。 这通过为每个声明提供一个唯一`x:Key`属性，它提供描述性项`ResourceDictionary`，下面的 XAML 代码示例中所示：

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

[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)分配给[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)属性使用`StaticResource`标记扩展。 请注意，当`DataTemplate`中页面的定义[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，也可以在控件级别或应用程序级别定义。

下面的代码示例演示 C# 中的等效页：

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

[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)添加到[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)使用[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/)方法，它指定`Key`用于的字符串引用`DataTemplate`检索它时。

## <a name="summary"></a>总结

本文介绍了如何从自定义类型，或在创建数据模板，内联， [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。 如果没有无需重复使用在其他位置的数据模板，则应使用内联模板。 或者，可以通过定义它作为自定义类型，或控制级别页级或应用程序级别资源重复使用数据模板。


## <a name="related-links"></a>相关链接

- [单元格外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [数据模板 （示例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [数据模板](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)
