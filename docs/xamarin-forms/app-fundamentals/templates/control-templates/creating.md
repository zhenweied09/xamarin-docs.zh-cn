---
title: 创建 ControlTemplate
description: 可以在应用程序级别或页级别定义控件模板。 本文演示如何创建和使用控件模板。
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 214f967ccc07a7c952ba87927c34c3ab32623391
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848273"
---
# <a name="creating-a-controltemplate"></a>创建 ControlTemplate

_可以在应用程序级别或页级别定义控件模板。本文演示如何创建和使用控件模板。_

## <a name="creating-a-controltemplate-in-xaml"></a>在 XAML 中创建 ControlTemplate

若要定义[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)应用程序级别[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)必须添加到`App`类。 默认情况下，从模板创建的所有 Xamarin.Forms 应用程序都使用**应用**类，以实现[ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)子类。 若要声明`ControlTemplate`在应用程序级别，在应用程序的`ResourceDictionary`使用 XAML，默认值**应用**类必须替换 XAML**应用**类和关联的代码隐藏，为下面的代码示例所示：

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

每个[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)作为可重用对象中创建实例[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。  这通过为每个声明提供一个唯一`x:Key`属性，它提供描述性项`ResourceDictionary`。

下面的代码示例演示关联`App`隐藏代码：

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

设置以及[ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/)属性，还必须调用代码隐藏`InitializeComponent`加载并分析关联的 XAML 方法。

下面的代码示例演示[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)应用`TealTemplate`到[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

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

`TealTemplate`分配给[ `ContentView.ControlTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TemplatedView.ControlTemplate/)属性使用`StaticResource`标记扩展。 [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/)属性设置为[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)定义上显示的内容[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)。 此内容将显示的[ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)中包含`TealTemplate`。 这将导致以下屏幕截图中所示的外观：

![](creating-images/teal-theme.png "青色控制模板")

### <a name="re-theming-an-application-at-runtime"></a>重新进行主题定位在运行时应用程序

单击**更改主题**按钮执行`OnButtonClicked`方法，下面的代码示例中所示：

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

此方法将替换活动[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)实例与另一种`ControlTemplate`实例，导致下面的屏幕快照：

![](creating-images/aqua-theme.png "浅绿色控件模板")

> [!NOTE]
> 上`ContentPage`、`Content`属性可以分配与`ControlTemplate`还可以设置属性。 在这种情况，如果`ControlTemplate`包含`ContentPresenter`实例分配给的内容`Content`属性将由`ContentPresenter`内`ControlTemplate`。

### <a name="setting-a-controltemplate-with-a-style"></a>设置使用样式 ControlTemplate

A [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)还可以通过应用[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)以进一步展开主题功能。 这可以通过创建*隐式*或*显式*样式中的目标视图[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，以及设置`ControlTemplate`目标属性在中查看[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)实例。 下面的代码示例演示*隐式*已添加到应用程序级别的样式[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/):

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

因为[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)实例是*隐式*，它将应用于所有`ContentView`应用程序中的实例。 因此，它不再需要设置[ `ContentView.ControlTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TemplatedView.ControlTemplate/)属性，如下面的代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

### <a name="creating-a-controltemplate-at-page-level"></a>在页级别创建 ControlTemplate

除了创建[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)应用程序级别的实例，它们也可将创建在页级别中，如下面的代码示例中所示：

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

添加时[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)级别的页[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)添加到[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)，，然后`ControlTemplate`实例是否包含在`ResourceDictionary`。

## <a name="creating-a-controltemplate-in-c35"></a>在 C 中创建 ControlTemplate&#35;

若要定义[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)应用程序级别`class`必须创建表示的`ControlTemplate`。 类应派生自[布局](~/xamarin-forms/user-interface/layouts/index.md)正在用于模板，如下面的代码示例中所示：

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

`AquaTemplate`类等同于`TealTemplate`类，用于不同的颜色相似，只不过[ `BoxView.Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/)和[ `Label.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/)属性。

下面的代码示例演示[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)应用`TealTemplate`到[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

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

[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)通过指定在定义控件模板的类的类型创建实例`ControlTemplate`构造函数。

[ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/)属性设置为[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)定义上显示的内容[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)。 此内容将显示的[ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)中包含`TealTemplate`。 所述的相同机制以前用于更改运行时在主题`AquaTheme`。

## <a name="summary"></a>总结

这篇文章演示了如何创建和使用控件模板。 可以在应用程序级别或页级别定义控件模板。


## <a name="related-links"></a>相关链接

- [样式](~/xamarin-forms/user-interface/styles/index.md)
- [简单主题 （示例）](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentPresenter](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)
- [ContentView](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
