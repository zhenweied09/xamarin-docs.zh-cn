---
title: "简介样式"
description: "样式允许可视化元素，以自定义的外观。 样式为特定类型定义，并且包含有关该类型可用的属性的值。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 6b4d63637eae7c1719f3f6e525327d416ddff59d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-styles"></a>简介样式

_样式允许可视化元素，以自定义的外观。样式为特定类型定义，并且包含有关该类型可用的属性的值。_

Xamarin.Forms 应用程序通常包含多个具有相同的外观的控件。 例如，应用程序可能具有多个[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例具有相同的字体选项和布局选项，如下面的 XAML 代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="are not"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="using styles"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

下面的代码示例演示在 C# 中创建的等效页：

```csharp
public class NoStylesPageCS : ContentPage
{
    public NoStylesPageCS ()
    {
        Title = "No Styles";
        Icon = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label {
                    Text = "These labels",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "are not",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "using styles",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                }
            }
        };
    }
}
```

每个[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例具有相同的属性值，用于控制显示的文本的外观`Label`。 这将导致以下屏幕截图中所示的外观：

[![](introduction-images/no-styles.png "标签不带样式的外观")](introduction-images/no-styles-large.png "标签不带样式的外观")

设置每个控件的外观可能非常重复，而且容易出错。 相反，一种样式可以创建定义的外观，然后应用于所需的控件。

## <a name="creating-a-style"></a>创建样式

[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)类进行分组到一个对象，然后可以应用于多个可视元素实例属性值的集合。 这有助于减少重复标记，并允许应用程序外观的外观，若要更轻松地更改。

尽管主要为基于 XAML 的应用程序设计样式，但它们也可以创建在 C# 中：

- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 在 XAML 中创建的实例通常在中定义[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) ，分配给[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/)的控件的集合页上，或者提供至[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/)的应用程序的集合。
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 在页面的类中，或类可全局访问通常定义在 C# 中创建的实例。

选择定义的位置[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)可以使用它的影响：

- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 在控件级别定义的实例只能应用到控件和及其子级。
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 只能向页以及其子应用的页级别定义的实例。
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 可以在整个应用程序应用的应用程序级别定义的实例。

每个[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)实例包含一个或多个集合[ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)对象，每个`Setter`具有[ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/)和[`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/). `Property`是样式应用于的元素的可绑定属性的名称和`Value`是应用于属性的值。

每个[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)实例可以是*显式*，或*隐式*:

- *显式* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)实例定义通过指定[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)和`x:Key`值，并通过设置目标元素[`Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性`x:Key`引用。 有关详细信息*显式*样式，请参阅[显式样式](~/xamarin-forms/user-interface/styles/explicit.md)。
- *隐式* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)实例定义通过仅指定[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)。 `Style`实例将然后自动应用于该类型的所有元素。 请注意的该子类`TargetType`不自动具有`Style`应用。 有关详细信息*隐式*样式，请参阅[隐式样式](~/xamarin-forms/user-interface/styles/implicit.md)。

在创建时[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)、 [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)属性是始终必需的。 下面的代码示例演示*显式*样式 (请注意`x:Key`) 在 XAML 中创建：

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

要应用`Style`，目标对象必须是[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)匹配[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)属性值`Style`，下面的 XAML 代码示例中所示：

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

视图层次结构中较低级别的样式优先于那些定义更高版本上。 例如，设置[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)设置[ `Label.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/)到`Red`在应用程序级别将被替代设置的页级别样式`Label.TextColor`到`Green`. 同样，将在控件级别样式将页级别样式中重写。 此外，如果`Label.TextColor`设置直接上的控件属性，这将优先于任何样式。

此部分中的文章演示并说明如何创建并应用*显式*和*隐式*样式，如何创建全局样式，样式继承，如何在运行时，样式更改作出响应以及如何使用 Xamarin.Forms 中包含的内置样式。

> [!NOTE]
> **什么是 StyleId？**
>
> 低于 Xamarin.Forms 2.2 [ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/)属性用于标识用于在 UI 测试中，以及主题引擎，如 Pixate 中标识的应用程序中各个元素。 但是，引入了 Xamarin.Forms 2.2 [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/)属性，已取代[ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/)属性。 有关详细信息，请参阅[自动执行 Xamarin.Forms 测试与 Xamarin.UITest 以及测试云](~/xamarin-forms/deploy-test/uitest-and-test-cloud.md)。

## <a name="summary"></a>摘要

Xamarin.Forms 应用程序通常包含多个具有相同的外观的控件。 设置每个控件的外观可能非常重复，而且容易出错。 相反，样式可以创建自定义控件外观的分组和可用的控件类型的设置属性。


## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [样式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
