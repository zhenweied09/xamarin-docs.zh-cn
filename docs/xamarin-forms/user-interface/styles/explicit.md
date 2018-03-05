---
title: "显式样式"
description: "显式样式是指通过设置其样式属性有选择地应用于这些控件。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 43a1a5ee6a8bd9d53f6fd44be935ae7573db6812
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="explicit-styles"></a>显式样式

_显式样式是指通过设置其样式属性有选择地应用于这些控件。_

## <a name="creating-an-explicit-style-in-xaml"></a>在 XAML 中创建显式样式

若要声明[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)级别的页[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)必须添加到页，然后一个或多个`Style`声明可以包含在`ResourceDictionary`。 A`Style`进行*显式*提供其声明，从而`x:Key`属性，其在提供描述性密钥`ResourceDictionary`。 *显式*样式必须然后将应用到特定的可视元素通过设置其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性。

下面的代码示例演示*显式*样式在 XAML 中声明中的页面`ResourceDictionary`和应用于页的[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="labelRedStyle" TargetType="Label">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
            <Style x:Key="labelGreenStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Green" />
            </Style>
            <Style x:Key="labelBlueStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelRedStyle}" />
            <Label Text="are demonstrating"
                   Style="{StaticResource labelGreenStyle}" />
            <Label Text="explicit styles,"
                   Style="{StaticResource labelBlueStyle}" />
            <Label Text="and an explicit style override"
                   Style="{StaticResource labelBlueStyle}"
                   TextColor="Teal" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定义三个*显式*样式应用于页的[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例。 每个`Style`用于显示文本以不同的颜色，同时，将字体大小和水平和垂直布局选项。 每个`Style`应用于不同`Label`通过设置其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性使用`StaticResource`标记扩展。 这将导致以下屏幕截图中所示的外观：

[![](explicit-images/explicit-styles.png "显式样式示例")](explicit-images/explicit-styles-large.png "显式样式示例")

此外，最终[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)具有[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)应用于它，但还将重写[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/)属性设置为不同`Color`值。

### <a name="creating-an-explicit-style-at-the-control-level"></a>在控件级别创建显式样式

除了创建*显式*页级别的样式，它们也可将创建的控制级别，如下面的代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelRedStyle" TargetType="Label">
                      ...
                    </Style>
                    ...
                </ResourceDictionary>
            </StackLayout.Resources>
            <Label Text="These labels" Style="{StaticResource labelRedStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此示例中，*显式* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)实例分配给[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/)集合[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)控件。 然后可以将样式应用于控件及其子级。

有关创建应用程序中的样式信息[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，请参阅[全局样式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="creating-an-explicit-style-in-c35"></a>在 c&#35; 中创建显式样式

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 可以将实例添加到页面的[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) C# 中通过创建新的集合[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，然后按添加`Style`实例到`ResourceDictionary`中, 所示下面的代码示例：

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red  }
            }
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Green }
            }
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Blue }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("labelRedStyle", labelRedStyle);
        Resources.Add ("labelGreenStyle", labelGreenStyle);
        Resources.Add ("labelBlueStyle", labelBlueStyle);
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels",
                            Style = (Style)Resources ["labelRedStyle"] },
                new Label { Text = "are demonstrating",
                            Style = (Style)Resources ["labelGreenStyle"] },
                new Label { Text = "explicit styles,",
                            Style = (Style)Resources ["labelBlueStyle"] },
                new Label { Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

构造函数定义三个*显式*样式应用于页的[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例。 每个*显式* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)添加到[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)使用[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/)方法，指定`key`字符串来引用`Style`实例。 每个`Style`应用于不同`Label`通过设置其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性。

但是，没有什么特别的用处使用[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)此处。 相反， [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)实例可以直接分配[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)的所需的可视元素的属性和`ResourceDictionary`可以删除，如在下面的示例所示代码示例：

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            ...
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            ...
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            ...
        };
        ...
        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelRedStyle },
                new Label { Text = "are demonstrating", Style = labelGreenStyle },
                new Label { Text = "explicit styles,", Style = labelBlueStyle },
                new Label { Text = "and an explicit style override", Style = labelBlueStyle,
                            TextColor = Color.Teal }
            }
        };
    }
}
```

构造函数定义三个*显式*样式应用于页的[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例。 每个`Style`用于显示文本以不同的颜色，同时，将字体大小和水平和垂直布局选项。 每个`Style`应用于不同`Label`通过设置其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性。 此外，最终`Label`具有`Style`应用于它，但还将重写`TextColor`为另一种属性`Color`值。

## <a name="summary"></a>摘要

A [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)进行*显式*提供其声明，从而`x:Key`属性，然后在然后有选择地将它应用到控件通过设置其[ `Style`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性。



## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [使用样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [样式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
