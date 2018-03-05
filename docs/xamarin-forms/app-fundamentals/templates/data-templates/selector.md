---
title: "创建 DataTemplateSelector"
description: "DataTemplateSelector 可用来选择 DataTemplate 在运行时基于的数据绑定属性的值。 这使多个 DataTemplates 要应用到相同类型的对象，自定义的特定对象的外观。 本文演示如何创建和使用 DataTemplateSelector。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 3fa2eb8ecac2015dc896a617a60d7c976411231a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-datatemplateselector"></a>创建 DataTemplateSelector

_DataTemplateSelector 可用来选择 DataTemplate 在运行时基于的数据绑定属性的值。这使多个 DataTemplates 要应用到相同类型的对象，自定义的特定对象的外观。本文演示如何创建和使用 DataTemplateSelector。_

数据模板选择器使方案如[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)绑定到集合的对象，其中在每个对象的外观的`ListView`可以在运行时中返回的数据模板选择器选择特定[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)。

## <a name="creating-a-datatemplateselector"></a>创建 DataTemplateSelector

通过创建继承自的类实现一个数据模板选择器[ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)。 `OnSelectTemplate`方法然后重写以返回特定[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)，下面的代码示例中所示：

```csharp
public class PersonDataTemplateSelector : DataTemplateSelector
{
  public DataTemplate ValidTemplate { get; set; }
  public DataTemplate InvalidTemplate { get; set; }

  protected override DataTemplate OnSelectTemplate (object item, BindableObject container)
  {
    return ((Person)item).DateOfBirth.Year >= 1980 ? ValidTemplate : InvalidTemplate;
  }
}
```

`OnSelectTemplate`方法返回适当的模板的值基于`DateOfBirth`属性。 要返回的模板是值`ValidTemplate`属性或`InvalidTemplate`属性，使用时设置`PersonDataTemplateSelector`。

数据模板选择器类的实例可以然后要分配给 Xamarin.Forms 控件属性如[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)。 有关有效的属性的列表，请参阅[创建 DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md)。

### <a name="limitations"></a>限制

[`DataTemplateSelector`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) 实例具有以下限制：

- `DataTemplateSelector`子类必须始终会返回相同的数据的同一个模板，当查询多次。
- `DataTemplateSelector`子类不能返回另一个`DataTemplateSelector`子类。
- `DataTemplateSelector`子类不能返回的新实例`DataTemplate`在每次调用。 相反，必须返回相同的实例。 如果不这样做将创建内存泄漏，并将禁用虚拟化。
- 在 Android 上，可以每个的不能超过 20 个不同的数据模板`ListView`。

## <a name="consuming-a-datatemplateselector-in-xaml"></a>使用 XAML 中 DataTemplateSelector

在 XAML 中，`PersonDataTemplateSelector`可以实例化通过声明为资源，如下面的代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Selector;assembly=Selector" x:Class="Selector.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="validPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <DataTemplate x:Key="invalidPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <local:PersonDataTemplateSelector x:Key="personDataTemplateSelector"
                ValidTemplate="{StaticResource validPersonTemplate}"
                InvalidTemplate="{StaticResource invalidPersonTemplate}" />
        </ResourceDictionary>
    </ContentPage.Resources>
  ...
</ContentPage>
```

此页面级别[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定义了两个[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)实例和一个`PersonDataTemplateSelector`实例。 `PersonDataTemplateSelector`实例集其`ValidTemplate`和`InvalidTemplate`属性设置为相应`DataTemplate`实例使用`StaticResource`标记扩展。 请注意，当资源页中定义[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，也可以在控件级别或应用程序级别定义它们。

`PersonDataTemplateSelector`实例由将其分配给[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)属性，如下面的代码示例中所示：

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

在运行时， [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)调用`PersonDataTemplateSelector.OnSelectTemplate`方法为每个具有将数据对象作为传递的调用的基础集合中的项`item`参数。 [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)返回方法然后应用于该对象。

以下屏幕快照显示的结果[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)应用`PersonDataTemplateSelector`对基础集合中每个对象：

![](selector-images/data-template-selector.png "使用数据模板选择器的 ListView")

任何`Person`具有对象`DateOfBirth`属性值大于或等于 1980年显示为绿色，与剩余的对象显示为红色。

## <a name="consuming-a-datatemplateselector-in-cnum"></a>使用在 C 中 DataTemplateSelector&num;

在 C# 中，`PersonDataTemplateSelector`可以实例化和分配给[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)属性，如下面的代码示例中所示：

```csharp
public class HomePageCS : ContentPage
{
  DataTemplate validTemplate;
  DataTemplate invalidTemplate;

  public HomePageCS ()
  {
    ...
    SetupDataTemplates ();
    var listView = new ListView {
      ItemsSource = people,
      ItemTemplate = new PersonDataTemplateSelector {
        ValidTemplate = validTemplate,
        InvalidTemplate = invalidTemplate }
    };

    Content = new StackLayout {
      Margin = new Thickness (20),
      Children = {
        ...
        listView
      }
    };
  }
  ...  
}
```

`PersonDataTemplateSelector`实例集其`ValidTemplate`和`InvalidTemplate`属性设置为相应[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)创建的实例`SetupDataTemplates`方法。 在运行时， [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)调用`PersonDataTemplateSelector.OnSelectTemplate`方法为每个具有将数据对象作为传递的调用的基础集合中的项`item`参数。 `DataTemplate`返回方法然后应用于该对象。

## <a name="summary"></a>摘要

本文演示了如何创建和使用[ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)。 A`DataTemplateSelector`可用来选择[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)在运行时基于的数据绑定属性的值。 这使多个`DataTemplate`实例应用于相同类型的对象，自定义的特定对象的外观。


## <a name="related-links"></a>相关链接

- [数据模板选择器 （示例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)
- [DataTemplateSelector](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)
