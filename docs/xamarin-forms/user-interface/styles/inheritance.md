---
title: "样式继承"
description: "样式可以继承其他样式来减少重复并启用重用。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 482358b0ccbedf926cd9182065a1eb8f4c12683c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="style-inheritance"></a>样式继承

_样式可以继承其他样式来减少重复并启用重用。_

## <a name="style-inheritance-in-xaml"></a>在 XAML 中的样式继承

通过设置执行样式继承[ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/)到一个现有的属性[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)。 在 XAML 中，这实现通过设置`BasedOn`属性`StaticResource`引用以前创建的标记扩展`Style`。 在 C# 中，情况下，这实现通过设置`BasedOn`属性`Style`实例。

继承自基样式的样式可以包括[ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)实例对于新属性，或者将其用于重写基样式中的样式。 此外，相同的类型或派生自基样式的目标类型的类型，必须指定目标继承自基样式的样式。 例如，如果基样式目标[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)情况下，可以面向基于基样式的样式`View`实例或派生自的类型`View`类，如[ `Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)和[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)实例。

下面的代码演示*显式*在 XAML 页面中的样式继承：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
            </Style>
            <Style x:Key="labelStyle" TargetType="Label"
                   BasedOn="{StaticResource baseStyle}">
                ...
                <Setter Property="TextColor" Value="Teal" />
            </Style>
            <Style x:Key="buttonStyle" TargetType="Button"
                   BasedOn="{StaticResource baseStyle}">
                <Setter Property="BorderColor" Value="Lime" />
                ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelStyle}" />
            ...
            <Button Text="So is the button"
                    Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

`baseStyle`目标[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)实例，并设置[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)和[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性。 `baseStyle`不直接在任何控件上设置。 相反，`labelStyle`和`buttonStyle`从它继承，设置其他可绑定属性值。 `labelStyle`和`buttonStyle`然后应用于[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例和[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)实例，通过设置其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性。 这将导致以下屏幕截图中所示的外观：

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png)

> [!NOTE]
> **请注意**： 隐式样式可以派生自显式样式，但不能从隐式样式派生显式样式。

### <a name="respecting-the-inheritance-chain"></a>遵从继承链

一种样式只能继承自样式同一级别或更高版本，视图层次结构中。 这表示：

- 应用程序级别资源只能继承自其他应用程序级别资源。
- 页级别资源可以继承其他页级别资源和应用程序级别资源。
- 控制级别资源可以从应用程序级别资源、 页级别资源和其他控件级别资源继承。

在下面的代码示例演示了此继承链：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelStyle" TargetType="Label" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                    <Style x:Key="buttonStyle" TargetType="Button" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此示例中，`labelStyle`和`buttonStyle`是控件级别的资源，而`baseStyle`是一个页级别资源。 但是，尽管`labelStyle`和`buttonStyle`继承`baseStyle`，不可能`baseStyle`要从其继承`labelStyle`或`buttonStyle`，因为视图层次结构中其各自的位置。

## <a name="style-inheritance-in-c35"></a>使用 c&#35; 样式继承

等效的 C# 页上，其中[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)实例分配给[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)所需的控件的属性下面的代码示例中所示：

```csharp
public class StyleInheritancePageCS : ContentPage
{
    public StyleInheritancePageCS ()
    {
        var baseStyle = new Style (typeof(View)) {
            Setters = {
                new Setter {
                    Property = View.HorizontalOptionsProperty, Value = LayoutOptions.Center },
                ...
            }
        };

        var labelStyle = new Style (typeof(Label)) {
            BasedOn = baseStyle,
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Teal }
            }
        };

        var buttonStyle = new Style (typeof(Button)) {
            BasedOn = baseStyle,
            Setters = {
                new Setter { Property = Button.BorderColorProperty, Value = Color.Lime },
                ...
            }
        };
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelStyle },
                ...
                new Button { Text = "So is the button", Style = buttonStyle }
            }
        };
    }
}
```

`baseStyle`目标[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)实例，并设置[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)和[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性。 `baseStyle`不直接在任何控件上设置。 相反，`labelStyle`和`buttonStyle`从它继承，设置其他可绑定属性值。 `labelStyle`和`buttonStyle`然后应用于[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例和[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)实例，通过设置其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性。

## <a name="summary"></a>摘要

样式可以继承其他样式来减少重复并启用重用。 通过设置执行样式继承[ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/)到一个现有的属性[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)。


## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [使用样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [样式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
