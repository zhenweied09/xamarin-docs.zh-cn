---
title: "附加属性"
description: "附加的属性是可绑定属性，在一个类中定义但附加到其他对象的特殊类型可识别作为特性的 XAML 中包含的类和句点分隔属性名称。 这篇文章提供附加属性的简介，并演示如何创建和使用它们。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/02/2016
ms.openlocfilehash: 7112812c843ccbcd6c24ea028deae3c09851b03d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="attached-properties"></a>附加属性

_附加的属性是可绑定属性，在一个类中定义但附加到其他对象的特殊类型可识别作为特性的 XAML 中包含的类和句点分隔属性名称。这篇文章提供附加属性的简介，并演示如何创建和使用它们。_

## <a name="overview"></a>概述

附加属性启用要为其自己的类未定义的属性分配值的对象。 例如，子元素可以使用附加属性以通知它们将在用户界面中显示其父元素。 [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)控件允许的行和列的子级可通过设置指定`Grid.Row`和`Grid.Column`附加属性。 `Grid.Row` 和`Grid.Column`是附加的属性，因为它们都设置元素的子级的上`Grid`，而不是在`Grid`本身。

可绑定属性应实现为在以下情况中的附加属性：

- 需要具有用于类可用的属性设置机制以外时定义的类。
- 当类表示一种服务，需要与其他类轻松地集成。

有关可绑定属性的详细信息，请参阅[可绑定属性](~/xamarin-forms/xaml/bindable-properties.md)。

## <a name="creating-and-consuming-an-attached-property"></a>创建和使用附加的属性

用于创建附加的属性的过程如下所示：

1. 创建[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)实例之一[ `CreateAttached` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateAttached/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法重载。
1. 提供`static` `Get` *PropertyName*和`Set` *PropertyName*附加属性访问器方法。

### <a name="creating-a-property"></a>创建的属性

在创建时使用的附加的属性上其他类型，其中创建属性的类不需要派生自[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)。 但是，*目标*属性访问器应为，类型或派生自[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)。

可以通过声明创建附加的属性`public static readonly`类型的属性[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)。 可绑定属性应设置为返回的值之一的[ `BindableProperty.CreateAttached` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateAttached/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法重载。 声明应在其所属的类，但在任何成员定义之外的正文。

下面的代码演示附加属性的示例：

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

这将创建附加的属性名为`HasShadow`，类型的`bool`。 该属性属于`ShadowEffect`类，并提供默认值为`false`。 附加属性的命名约定是附加的属性标识符必须匹配中指定的属性名称`CreateAttached`方法，使用"属性"追加到它。 因此，在上面的示例中，附加的属性标识符是`HasShadowProperty`。

有关创建可绑定属性，包括可以在创建期间，指定的参数的详细信息请参阅[创建和使用可绑定属性](~/xamarin-forms/xaml/bindable-properties.md#consuming-bindable-property)。

### <a name="creating-accessors"></a>创建访问器

静态`Get` *PropertyName*和`Set` *PropertyName*方法都需要作为附加属性的访问器，否则属性系统将不能使用附加属性。 `Get` *PropertyName*访问器应符合以下签名：

```csharp
public static valueType GetPropertyName(BindableObject target)
```

`Get` *PropertyName*访问器应返回包含值的相应`BindableProperty`字段附加属性。 这可以通过调用实现[ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/)方法、 传入要获取的值，依据的可绑定的属性标识符，然后强制转换为所需的类型生成的值。

`Set` *PropertyName*访问器应符合以下签名：

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

`Set` *PropertyName*访问器应设置相应的值`BindableProperty`字段附加属性。 这可以通过调用实现[ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法，同时传入在其上设置值和要设置的值的可绑定的属性标识符。

对于这两个访问器而言，*目标*对象应为，类型或派生自[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)。

下面的代码示例演示的访问器`HasShadow`附加属性：

```csharp
public static bool GetHasShadow (BindableObject view)
{
  return (bool)view.GetValue (HasShadowProperty);
}

public static void SetHasShadow (BindableObject view, bool value)
{
  view.SetValue (HasShadowProperty, value);
}
```

### <a name="consuming-an-attached-property"></a>使用附加的属性

一旦创建附加的属性后，可以从 XAML 或代码使用它。 在 XAML 中，这被通过使用，该值指示公共语言运行时 (CLR) 命名空间名称和 （可选） 程序集名称的命名空间声明中声明的带有前缀的命名空间。 有关详细信息，请参阅[XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)。

下面的代码示例演示了包含一个附加的属性，将在相同的程序集中引用的自定义类型的应用程序代码中定义的自定义类型的 XAML 命名空间：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

时将特定控件，附加的属性设置为以下 XAML 代码示例所示，然后使用命名空间声明：

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

以下代码示例显示相应的 C# 代码：

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consuming-an-attached-property-with-a-style"></a>使用一种样式的附加的的属性

由样式之前，还可以将附加的属性添加到控件。 下面的 XAML 代码示例演示*显式*使用的样式`HasShadow`附加属性，可以应用于[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控件：

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)可应用于[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)通过设置其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性`Style`实例使用`StaticResource`标记扩展，如下面的代码示例中所示：

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

## <a name="advanced-scenarios"></a>高级的方案

在创建附加的属性时，有了多个可选参数，可设置为启用高级附加的属性的方案。 这包括检测属性更改、 验证属性值和强制属性值。 有关详细信息，请参阅[高级方案](~/xamarin-forms/xaml/bindable-properties.md#advanced)。

## <a name="summary"></a>摘要

这篇文章提供附加属性的简介，并演示了如何创建和使用它们。 附加的属性是一种特殊类型的可绑定属性，但对其他对象已附加和可识别在 XAML 中的某个类中定义为包含一个类和句点分隔的属性名称的属性。


## <a name="related-links"></a>相关链接

- [可绑定属性](~/xamarin-forms/xaml/bindable-properties.md)
- [XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)
- [投影效果 （示例）](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
- [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)
