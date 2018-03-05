---
title: "ControlTemplate 绑定"
description: "模板绑定都允许对数据的控件模板中的控件绑定到公共属性启用的控件模板来轻松地更改中的控件上的属性值。 本文演示如何使用模板绑定从控件模板执行数据绑定。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 5b330c448a135cbcf8fc2745debc48924e29c103
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="binding-from-a-controltemplate"></a>ControlTemplate 绑定

_模板绑定都允许对数据的控件模板中的控件绑定到公共属性启用的控件模板来轻松地更改中的控件上的属性值。本文演示如何使用模板绑定从控件模板执行数据绑定。_

A [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)用于将控件模板中的控件的属性绑定到的父级的可绑定属性*目标*拥有控件模板的视图。 例如，而定义显示的文本不是[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例内[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)，你无法使用模板绑定绑定[ `Label.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)定义要显示的文本的可绑定属性的属性。

A [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)类似于现有[ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/)，只不过*源*的`TemplateBinding`始终自动设置到的父级*目标*拥有控件模板的视图。 但是，请注意，使用`TemplateBinding`之外[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)不支持。

## <a name="creating-a-templatebinding-in-xaml"></a>在 XAML 中创建 TemplateBinding

在 XAML 中， [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)使用创建[ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TemplateBindingExtension/)标记扩展，如下面的代码示例中所示：

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

而不是设置[ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)为静态文本的属性，属性可以用模板绑定来绑定到可绑定属性的父*目标*拥有视图[`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/). 但请注意，模板绑定将绑定到`Parent.HeaderText`和`Parent.FooterText`，而不是`HeaderText`和`FooterText`。 这是因为在此示例中，可绑定属性定义上的祖父*目标*查看，而不是父，如下面的代码示例中所示：

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

*源*的模板绑定始终自动设置到的父级*目标*拥有控件模板中，下面是视图[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)实例。 绑定使用的模板[ `Parent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Parent/)属性返回的父元素`ContentView`实例，即[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)实例。 因此，使用[ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)中[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)要绑定到`Parent.HeaderText`和`Parent.FooterText`定位定义的可绑定属性`ContentPage`，作为下面的代码示例所示：

```csharp
public static readonly BindableProperty HeaderTextProperty =
  BindableProperty.Create ("HeaderText", typeof(string), typeof(HomePage), "Control Template Demo App");
public static readonly BindableProperty FooterTextProperty =
  BindableProperty.Create ("FooterText", typeof(string), typeof(HomePage), "(c) Xamarin 2016");

public string HeaderText {
  get { return (string)GetValue (HeaderTextProperty); }
}

public string FooterText {
  get { return (string)GetValue (FooterTextProperty); }
}
```

这将导致以下屏幕截图中所示的外观：

![](template-binding-images/teal-theme.png "使用模板绑定的青色控件模板")

## <a name="creating-a-templatebinding-in-c35"></a>在 c&#35; 中创建 TemplateBinding

在 C# 中， [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)通过创建`TemplateBinding`构造函数，如下面的代码示例中所示：

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var topLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    topLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.HeaderText"));
    ...
    var bottomLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    bottomLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.FooterText"));
    ...
  }
}
```

而不是设置[ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)为静态文本的属性，属性可以用模板绑定来绑定到可绑定属性的父*目标*拥有视图[`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/). 通过创建模板绑定[ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)方法，指定[ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)实例作为第二个参数。 请注意，模板绑定将绑定到`Parent.HeaderText`和`Parent.FooterText`，因为可绑定属性定义上的祖父*目标*查看，而不是父，如下面的代码示例中所示：

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    Content = new ContentView {
      ControlTemplate = tealTemplate,
      Content = new StackLayout {
        ...
      },
      ...
    };
    ...
  }
}
```

在上定义的可绑定属性`ContentPage`，如前面所述。

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>绑定 BindableProperty 到视图模型属性

如前面所述， [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)将控件模板中的控件的属性绑定到的父级的可绑定属性*目标*拥有控件模板的视图。 反过来，这些可绑定属性可绑定到 Viewmodel 中的属性。

下面的代码示例定义视图模型上的两个属性：

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

`HeaderText`和`FooterText`ViewModel 属性可绑定到，如下面的 XAML 代码示例中所示：

```xaml
<ContentPage xmlns:local="clr-namespace:SimpleTheme;assembly=SimpleTheme"
             HeaderText="{Binding HeaderText}" FooterText="{Binding FooterText}" ...>
    <ContentPage.BindingContext>
        <local:HomePageViewModel />
    </ContentPage.BindingContext>
    <ContentView ControlTemplate="{StaticResource TealTemplate}" ...>
    ...
    </ContentView>
</ContentPage>
```

`HeaderText`和`FooterText`可绑定属性绑定到`HomePageViewModel.HeaderText`和`HomePageViewModel.FooterText`属性，因为设置[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)到实例`HomePageViewModel`类。 总体上而言，这会导致中的控件属性[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)要绑定到[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)实例[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)，这反过来将绑定到视图模型属性。

以下代码示例显示相应的 C# 代码：

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    BindingContext = new HomePageViewModel ();
    this.SetBinding (HeaderTextProperty, "HeaderText");
    this.SetBinding (FooterTextProperty, "FooterText");
    ...
  }
}
```

有关数据绑定到 Viewmodel 的详细信息，请参阅[从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="summary"></a>摘要

演示使用模板绑定进行数据绑定控件模板从这篇文章。 模板绑定都允许对数据的控件模板中的控件绑定到公共属性启用的控件模板来轻松地更改中的控件上的属性值。



## <a name="related-links"></a>相关链接

- [数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [与模板绑定 （示例） 的简单主题](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)
- [使用模板绑定和视图模型 （示例） 的简单主题](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentView](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
