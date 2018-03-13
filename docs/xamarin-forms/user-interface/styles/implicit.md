---
title: "隐式样式"
description: "隐式样式是指可供所有控件的同一 TargetType，而无需每个控件，以引用样式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: b96b306c882eb30aaf8c81604afb9b6a547d715b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="implicit-styles"></a>隐式样式

_隐式样式是指可供所有控件的同一 TargetType，而无需每个控件，以引用样式。_

## <a name="creating-an-implicit-style-in-xaml"></a>在 XAML 中创建隐式样式

若要声明[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)级别的页[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)必须添加到页，然后一个或多个`Style`声明可以包含在`ResourceDictionary`。 A`Style`进行*隐式*通过不指定`x:Key`属性。 样式然后将应用于匹配的可视元素`TargetType`完全匹配，但不适用于元素派生自`TargetType`值。

下面的代码示例演示*隐式*样式在 XAML 中声明中的页面`ResourceDictionary`，并且应用于该页面的[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)实例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定义单个*隐式*样式应用于页的[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)实例。 `Style`用于在一个黄色背景上显示蓝色文本，同时，将其他外观选项。 `Style`添加到页面的[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)而无需指定`x:Key`属性。 因此，`Style`适用于所有`Entry`隐式实例，因为它们匹配[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)属性`Style`完全。 但是，`Style`不应用于`CustomEntry`实例，即子类化`Entry`。 这将导致以下屏幕截图中所示的外观：

[![](implicit-images/implicit-styles.png "隐式样式示例")](implicit-images/implicit-styles-large.png#lightbox "隐式样式示例")

此外，第四个[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)重写[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)和[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/)属性隐式样式应用到不同`Color`值。

### <a name="creating-an-implicit-style-at-the-control-level"></a>在控件级别创建隐式样式

除了创建*隐式*页级别的样式，它们也可将创建的控制级别，如下面的代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此示例中，*隐式* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)分配给[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/)集合[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)控件。 *隐式*样式然后可以应用于控件及其子级。

有关创建应用程序中的样式信息[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，请参阅[全局样式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="creating-an-implicit-style-in-c35"></a>在 C 中创建隐式样式&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 可以将实例添加到页面的[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) C# 中通过创建新的集合[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，然后按添加`Style`实例到`ResourceDictionary`中, 所示下面的代码示例：

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

构造函数定义单个*隐式*样式应用于页的[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)实例。 `Style`用于在一个黄色背景上显示蓝色文本，同时，将其他外观选项。 `Style`添加到页面的[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)而无需指定`key`字符串。 因此，`Style`适用于所有`Entry`隐式实例，因为它们匹配[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)属性`Style`完全。 但是，`Style`不应用于`CustomEntry`实例，即子类化`Entry`。

## <a name="summary"></a>摘要

*隐式*样式是指由所有可视元素相同[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)，而无需每个控件，以引用样式。 A`Style`进行*隐式*通过不指定`x:Key`属性。 相反，`x:Key`属性将自动成为的值[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)属性。



## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [使用样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [样式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
