---
title: 从 Xamarin.Forms ControlTemplate 绑定
description: 模板绑定允许对数据的控件模板中的控件绑定到公共属性，使控件模板来轻松地更改中的控件上的属性值。 本文演示如何使用模板绑定控件模板中执行数据绑定。
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 86de2ad6009365b3debbe1a2310651002b023219
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995557"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>从 Xamarin.Forms ControlTemplate 绑定

_模板绑定允许对数据的控件模板中的控件绑定到公共属性，使控件模板来轻松地更改中的控件上的属性值。本文演示如何使用模板绑定控件模板中执行数据绑定。_

一个[ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)用于将控件模板中的控件的属性绑定到可绑定的属性的父级上*目标*拥有控件模板的视图。 例如，不同于定义显示的文本[ `Label` ](xref:Xamarin.Forms.Label)实例内[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)，您可以使用模板绑定来绑定[ `Label.Text`](xref:Xamarin.Forms.Label.Text)属性设置为可绑定属性用于定义要显示的文本。

一个[ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)类似于现有[ `Binding` ](xref:Xamarin.Forms.Binding)，只不过*源*的`TemplateBinding`始终自动设置为父级的*目标*拥有控件模板的视图。 但是，请注意，使用`TemplateBinding`之外[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)不受支持。

## <a name="creating-a-templatebinding-in-xaml"></a>在 XAML 中创建 TemplateBinding

在 XAML 中， [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)使用创建[ `TemplateBinding` ](xref:Xamarin.Forms.Xaml.TemplateBindingExtension)标记扩展，如下面的代码示例中所示：

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

而不是设置[ `Label.Text` ](xref:Xamarin.Forms.Label.Text)静态文本的属性，属性可以使用模板绑定绑定到可绑定属性的父级上*目标*视图拥有[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). 但请注意模板绑定绑定到`Parent.HeaderText`并`Parent.FooterText`，而非`HeaderText`和`FooterText`。 这是因为在此示例中，可绑定属性定义上的祖父*目标*查看，而不是父项，如下面的代码示例中所示：

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

*源*模板的绑定始终自动设置为父级的*目标*拥有控件模板，此处为视图[ `ContentView` ](xref:Xamarin.Forms.ContentView)实例。 绑定使用的模板[ `Parent` ](xref:Xamarin.Forms.Element.Parent)属性返回的父元素`ContentView`实例，这是[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)实例。 因此，使用[ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)中[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)要绑定到`Parent.HeaderText`并`Parent.FooterText`定位定义的可绑定属性`ContentPage`，作为以下代码示例所示：

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

这会导致下面的屏幕截图中所示的外观：

![](template-binding-images/teal-theme.png "使用模板绑定的深青色控件模板")

## <a name="creating-a-templatebinding-in-c35"></a>在 C 中创建 TemplateBinding&#35;

在 C# 中， [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)通过创建`TemplateBinding`构造函数，如下面的代码示例中所示：

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

而不是设置[ `Label.Text` ](xref:Xamarin.Forms.Label.Text)静态文本的属性，属性可以使用模板绑定绑定到可绑定属性的父级上*目标*视图拥有[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). 使用创建的模板绑定[ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))方法中，指定[ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)实例作为第二个参数。 请注意，模板绑定绑定到`Parent.HeaderText`并`Parent.FooterText`，因为可绑定属性定义上的祖父级*目标*查看，而不是父项，如下面的代码示例中所示：

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

可绑定属性上定义`ContentPage`，如前面所述。

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>绑定到 ViewModel 属性 BindableProperty

如前面所述， [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)将在控件模板中的控件的属性绑定到可绑定的属性的父级上*目标*拥有控件模板的视图。 反过来，这些可绑定属性可以绑定到 Viewmodel 中的属性。

下面的代码示例在 ViewModel 上定义两个属性：

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

`HeaderText`和`FooterText`ViewModel 属性可以绑定到，如下面的 XAML 代码示例中所示：

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

`HeaderText`并`FooterText`可绑定属性绑定到`HomePageViewModel.HeaderText`并`HomePageViewModel.FooterText`属性，由于设置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)实例`HomePageViewModel`类。 总体而言，这会导致控件中的属性[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)要绑定到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)上实例[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)，又将其绑定到ViewModel 属性。

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

## <a name="summary"></a>总结

使用模板绑定控件模板中执行数据绑定演示这篇文章。 模板绑定允许对数据的控件模板中的控件绑定到公共属性，使控件模板来轻松地更改中的控件上的属性值。



## <a name="related-links"></a>相关链接

- [数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [使用模板绑定 （示例） 的简单主题](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)
- [使用模板绑定和 ViewModel （示例） 的简单主题](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)
