---
title: "全局样式"
description: "样式可全局方法将它们添加到应用程序的资源字典。 这有助于避免能跨页或控件的重复样式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: dc728e5817a7d1d61d7745120bca7f05641ce76f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="global-styles"></a>全局样式

_样式可全局方法将它们添加到应用程序的资源字典。这有助于避免能跨页或控件的重复样式。_

## <a name="creating-a-global-style-in-xaml"></a>在 XAML 中创建全局样式

默认情况下，从模板创建的所有 Xamarin.Forms 应用程序都使用**应用**类，以实现[ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)子类。 若要声明[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)在应用程序级别，在应用程序的[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)使用 XAML，默认值**应用**类必须替换 XAML**应用**类和关联的代码隐藏。 有关详细信息，请参阅[使用 App 类](~/xamarin-forms/app-fundamentals/application-class.md)。

下面的代码示例演示[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)在应用程序级别声明：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.App">
    <Application.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BorderColor" Value="Lime" />
                <Setter Property="BorderRadius" Value="5" />
                <Setter Property="BorderWidth" Value="5" />
                <Setter Property="WidthRequest" Value="200" />
                <Setter Property="TextColor" Value="Teal" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

这[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定义单个*显式*样式， `buttonStyle`，它将使用设置的外观[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)实例。 但是，可以全局样式*显式*或*隐式*。

下面的代码示例演示 XAML 页应用`buttonStyle`到页面的[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)实例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

这将导致以下屏幕截图中所示的外观：

[![](application-images/application-styles-1.png "全局样式示例")](application-images/application-styles-1-large.png#lightbox "全局样式示例")

有关创建一页中的样式信息[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，请参阅[显式样式](~/xamarin-forms/user-interface/styles/explicit.md)和[隐式样式](~/xamarin-forms/user-interface/styles/implicit.md)。

### <a name="overriding-styles"></a>重写样式

视图层次结构中较低级别的样式优先于那些定义更高版本上。 例如，设置[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)设置[ `Button.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.TextColor/)到`Red`在应用程序级别将被替代设置的页级别样式`Button.TextColor`到`Green`. 同样，将在控件级别样式将页级别样式中重写。 此外，如果`Button.TextColor`设置直接上的控件属性，它将优先于任何样式。 在下面的代码示例演示了此优先：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                ...
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="buttonStyle" TargetType="Button">
                        ...
                        <Setter Property="TextColor" Value="Blue" />
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

原始`buttonStyle`、 在应用程序级别定义、 通过重写`buttonStyle`页级别定义的实例。 此外，来控制级别重写的页级别样式`buttonStyle`。 因此， [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)实例将以蓝色文本，显示，如以下屏幕截图中所示：

[![](application-images/application-styles-2.png "重写样式示例")](application-images/application-styles-2-large.png#lightbox "重写样式示例")

## <a name="creating-a-global-style-in-c35"></a>在 C 中创建全局样式&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 可以将实例添加到应用程序的[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) C# 中通过创建新的集合[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，然后按添加`Style`实例到`ResourceDictionary`，作为下面的代码示例所示：

```csharp
public class App : Application
{
    public App ()
    {
        var buttonStyle = new Style (typeof(Button)) {
            Setters = {
                ...
                new Setter { Property = Button.TextColorProperty,   Value = Color.Teal }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("buttonStyle", buttonStyle);
        ...
    }
    ...
}
```

构造函数定义单个*显式*样式用于将应用于[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)整个应用程序的实例。 *显式* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)实例添加到[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)使用[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/)方法，指定`key`字符串来引用`Style`实例。 `Style`实例可以然后应用到任何应用程序中的正确类型的控件。 但是，可以全局样式*显式*或*隐式*。

下面的代码示例演示 C# 页应用`buttonStyle`到页面的[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)实例：

```csharp
public class ApplicationStylesPageCS : ContentPage
{
    public ApplicationStylesPageCS ()
    {
        ...
        Content = new StackLayout {
            Children = {
                new Button { Text = "These buttons", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "are demonstrating", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "application styles", Style = (Style)Application.Current.Resources ["buttonStyle"]
                }
            }
        };
    }
}
```

`buttonStyle`应用于[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)实例通过设置其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性，并控制的外观`Button`实例。

## <a name="summary"></a>摘要

样式可提供全局通过将它们添加到应用程序的[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。 这有助于避免能跨页或控件的重复样式。



## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [使用样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [样式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
