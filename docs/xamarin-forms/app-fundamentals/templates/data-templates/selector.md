---
title: 创建 Xamarin.Forms DataTemplateSelector
description: 本文演示如何创建和使用 DataTemplateSelector，可以用于选择在运行时根据数据绑定属性的值的 DataTemplate。
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: a72777c7e51e96a8e123ecd85ad0aa24fc60fc6c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994512"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>创建 Xamarin.Forms DataTemplateSelector

_DataTemplateSelector 可以用于选择在运行时根据数据绑定属性的值的 DataTemplate。这使多个 DataTemplates 以应用于相同类型的对象，若要自定义的特定对象的外观。本文演示如何创建和使用 DataTemplateSelector。_

数据模板选择器启用方案，例如[ `ListView` ](xref:Xamarin.Forms.ListView)绑定到对象的集合，其中在每个对象的外观的`ListView`可以在运行时中返回的数据模板选择器选择特定[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。

## <a name="creating-a-datatemplateselector"></a>创建 DataTemplateSelector

通过创建继承的类实现一个数据模板选择器[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)。 `OnSelectTemplate`方法然后重写以返回特定[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，下面的代码示例中所示：

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

`OnSelectTemplate`方法返回值的基础的相应模板`DateOfBirth`属性。 要返回的模板是的值`ValidTemplate`属性或`InvalidTemplate`属性，使用时设置`PersonDataTemplateSelector`。

数据模板选择器类的实例可以然后赋给 Xamarin.Forms 控件属性如[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)。 有关有效的属性的列表，请参阅[创建 DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md)。

### <a name="limitations"></a>限制

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 实例具有以下限制：

- `DataTemplateSelector`子类必须始终会返回相同的数据的同一个模板，如果多次查询。
- `DataTemplateSelector`子类不能返回另一个`DataTemplateSelector`子类。
- `DataTemplateSelector`子类不能返回的新实例`DataTemplate`在每次调用。 相反，必须返回相同的实例。 如果不这样做会造成内存泄漏，将禁用虚拟化。
- 在 Android 上，可以每个不超过 20 个不同的数据模板`ListView`。

## <a name="consuming-a-datatemplateselector-in-xaml"></a>使用 XAML 中 DataTemplateSelector

在 XAML，`PersonDataTemplateSelector`可以实例化通过声明为资源，如下面的代码示例中所示：

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

此页面级[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)定义了两个[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)实例和一个`PersonDataTemplateSelector`实例。 `PersonDataTemplateSelector`实例设置其`ValidTemplate`和`InvalidTemplate`属性设置为相应`DataTemplate`实例使用`StaticResource`标记扩展。 请注意，当资源页中定义[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，它们也可以在控件级别或应用程序级别定义。

`PersonDataTemplateSelector`实例使用的将其分配给[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)属性，如下面的代码示例中所示：

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

在运行时， [ `ListView` ](xref:Xamarin.Forms.ListView)调用`PersonDataTemplateSelector.OnSelectTemplate`方法为每个调用传递数据对象作为基础集合中项`item`参数。 [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)返回该方法然后应用于该对象。

以下屏幕截图显示的结果[ `ListView` ](xref:Xamarin.Forms.ListView)应用`PersonDataTemplateSelector`到基础集合中每个对象：

![](selector-images/data-template-selector.png "使用数据模板选择器的 ListView")

任何`Person`对象，它具有`DateOfBirth`属性值大于或等于 1980年中显示为绿色，使用剩余的对象显示为红色。

## <a name="consuming-a-datatemplateselector-in-cnum"></a>使用 C 中 DataTemplateSelector&num;

在 C# 中，`PersonDataTemplateSelector`可实例化和分配给[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)属性，如下面的代码示例中所示：

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

`PersonDataTemplateSelector`实例设置其`ValidTemplate`并`InvalidTemplate`属性设置为相应[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)创建的实例`SetupDataTemplates`方法。 在运行时， [ `ListView` ](xref:Xamarin.Forms.ListView)调用`PersonDataTemplateSelector.OnSelectTemplate`方法为每个调用传递数据对象作为基础集合中项`item`参数。 `DataTemplate`返回该方法然后应用于该对象。

## <a name="summary"></a>总结

本文演示了如何创建和使用[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)。 一个`DataTemplateSelector`可用于选择[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)在运行时根据数据绑定属性的值。 这使多个`DataTemplate`实例应用于相同类型的对象，若要自定义的特定对象的外观。


## <a name="related-links"></a>相关链接

- [数据模板选择器 （示例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
