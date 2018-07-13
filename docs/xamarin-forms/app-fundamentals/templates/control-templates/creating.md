---
title: 创建 ControlTemplate
description: 可以在应用程序级别或页级别定义控件模板。 本文演示如何创建和使用控件模板。
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: b83668f6836b1d5d98f67592bf3e2b01e7319edc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998177"
---
# <a name="creating-a-controltemplate"></a>创建 ControlTemplate

_可以在应用程序级别或页级别定义控件模板。本文演示如何创建和使用控件模板。_

## <a name="creating-a-controltemplate-in-xaml"></a>在 XAML 中创建 ControlTemplate

若要定义[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)应用程序级别[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)必须添加到`App`类。 默认情况下，所有从模板创建的 Xamarin.Forms 应用程序使用**应用程序**类，以实现[ `Application` ](xref:Xamarin.Forms.Application)子类。 若要声明`ControlTemplate`在应用程序级别，在应用程序的`ResourceDictionary`使用 XAML，默认**应用**类必须替换 XAML**应用**类和关联的代码隐藏，作为下面的代码示例所示：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.App">
    <Application.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                <Grid>
                    ...
                    <BoxView ... />
                    <Label Text="Control Template Demo App"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                    <ContentPresenter ... />
                    <BoxView Color="Teal" ... />
                    <Label Text="(c) Xamarin 2016"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                </Grid>
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

每个[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)实例创建为一个可重用对象[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。  这通过为每个声明提供一个唯一`x:Key`属性，为其提供中的描述性键`ResourceDictionary`。

下面的代码示例显示了关联`App`隐藏代码：

```csharp
public partial class App : Application
{
  public App ()
  {
    InitializeComponent ();
    MainPage = new HomePage ();
  }
}
```

设置以及[ `MainPage` ](xref:Xamarin.Forms.Application.MainPage)属性，还必须调用代码隐藏`InitializeComponent`方法来加载和分析相关联的 XAML。

下面的代码示例演示[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)应用`TealTemplate`到[ `ContentView` ](xref:Xamarin.Forms.ContentView):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0"
                 ControlTemplate="{StaticResource TealTemplate}">
        <StackLayout VerticalOptions="CenterAndExpand">
            <Label Text="Welcome to the app!" HorizontalOptions="Center" />
            <Button Text="Change Theme" Clicked="OnButtonClicked" />
        </StackLayout>
    </ContentView>
</ContentPage>
```

`TealTemplate`分配给[ `ContentView.ControlTemplate` ](xref:Xamarin.Forms.TemplatedView.ControlTemplate)属性使用`StaticResource`标记扩展。 [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content)属性设置为[ `StackLayout` ](xref:Xamarin.Forms.StackLayout) ，用于定义要在上显示的内容[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)。 此内容将显示的[ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter)中包含`TealTemplate`。 这会导致下面的屏幕截图中所示的外观：

![](creating-images/teal-theme.png "青色控件模板")

### <a name="re-theming-an-application-at-runtime"></a>Re 主题设置在运行时的应用程序

单击**更改主题**按钮执行`OnButtonClicked`方法，在下面的代码示例所示：

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

此方法替换活动[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)实例使用的替代方案`ControlTemplate`实例，从而导致以下屏幕截图：

![](creating-images/aqua-theme.png "浅绿色控件模板")

> [!NOTE]
> 上`ContentPage`，则`Content`可以将属性分配和`ControlTemplate`还可以设置属性。 在这种情况，如果`ControlTemplate`包含`ContentPresenter`实例，分配到的内容`Content`属性将显示由`ContentPresenter`内`ControlTemplate`。

### <a name="setting-a-controltemplate-with-a-style"></a>设置样式的 ControlTemplate

一个[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)还可以通过应用[ `Style` ](xref:Xamarin.Forms.Style)以进一步展开主题功能。 这可以通过创建实现*隐式*或*显式*样式中的目标视图[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，以及设置`ControlTemplate`目标属性在中查看[ `Style` ](xref:Xamarin.Forms.Style)实例。 下面的代码示例演示*隐式*已添加到应用程序级别的样式[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

因为[ `Style` ](xref:Xamarin.Forms.Style)实例*隐式*，它将应用于所有`ContentView`中应用程序的实例。 因此，它不再需要设置[ `ContentView.ControlTemplate` ](xref:Xamarin.Forms.TemplatedView.ControlTemplate)属性，如下面的代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

### <a name="creating-a-controltemplate-at-page-level"></a>在页面级别创建 ControlTemplate

除了创建之外[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)在应用程序级别的实例，则可以也在创建这些页面级别中，如下面的代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                ...
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
        ...
    </ContentView>
</ContentPage>
```

添加时[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)级别的页[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)添加到[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)，然后`ControlTemplate`将包括实例在`ResourceDictionary`。

## <a name="creating-a-controltemplate-in-c35"></a>在 C 中创建 ControlTemplate&#35;

若要定义[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)应用程序级别`class`必须创建，它表示`ControlTemplate`。 类应派生自[布局](~/xamarin-forms/user-interface/layouts/index.md)使用模板，如下面的代码示例中所示：

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var contentPresenter = new ContentPresenter ();
    Children.Add (contentPresenter, 0, 1);
    Grid.SetColumnSpan (contentPresenter, 2);
    ...
  }
}

class AquaTemplate : Grid
{
  ...
}
```

`AquaTemplate`类等同于`TealTemplate`类，用于不同的颜色相似，只不过[ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color)并[ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor)属性。

下面的代码示例演示[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)应用`TealTemplate`到[ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
public class HomePageCS : ContentPage
{
  ...
  ControlTemplate tealTemplate = new ControlTemplate (typeof(TealTemplate));
  ControlTemplate aquaTemplate = new ControlTemplate (typeof(AquaTemplate));

  public HomePageCS ()
  {
    var button = new Button { Text = "Change Theme" };
    var contentView = new ContentView {
      Padding = new Thickness (0, 20, 0, 0),
      Content = new StackLayout {
        VerticalOptions = LayoutOptions.CenterAndExpand,
        Children = {
          new Label { Text = "Welcome to the app!", HorizontalOptions = LayoutOptions.Center },
          button
        }
      },
      ControlTemplate = tealTemplate
    };
    ...
    Content = contentView;
  }
}
```

[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)通过指定定义控件模板中的类的类型创建实例`ControlTemplate`构造函数。

[ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content)属性设置为[ `StackLayout` ](xref:Xamarin.Forms.StackLayout) ，用于定义要在上显示的内容[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)。 此内容将显示的[ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter)中包含`TealTemplate`。 所述的相同机制以前用于更改处于运行时主题`AquaTheme`。

## <a name="summary"></a>总结

本文演示了如何创建和使用控件模板。 可以在应用程序级别或页级别定义控件模板。


## <a name="related-links"></a>相关链接

- [样式](~/xamarin-forms/user-interface/styles/index.md)
- [简单主题 （示例）](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
