---
title: 可绑定属性
description: 本文介绍可绑定属性，并演示如何创建和使用它们。
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/02/2016
ms.openlocfilehash: 5e39e8eb3d7ffb3ed33ea2a585d8d367302e9baa
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245971"
---
# <a name="bindable-properties"></a>可绑定属性

_Xamarin.Forms 中的公共语言运行时 (CLR) 属性的功能扩展可绑定属性。可绑定属性是属性的一种特殊类型，其中通过 Xamarin.Forms 属性系统跟踪属性的值。本文介绍可绑定属性，并演示如何创建和使用它们。_

## <a name="overview"></a>概述

可绑定的属性扩展通过将数据备份具有的属性的 CLR 属性功能[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)类型，而不是支持与字段属性。 可绑定属性的用途是提供支持数据绑定、 样式、 模板的属性系统，并通过父-子关系的值设置。 此外，可绑定属性可以提供默认值，属性值和监视属性更改的回调的验证。

属性应至少实施为可绑定属性，以支持一个或多个以下功能：

- 充当有效*目标*的数据绑定属性。
- 通过将属性设置[样式](~/xamarin-forms/user-interface/styles/index.md)。
- 提供用于不同的默认值为属性的类型的默认属性值。
- 验证属性的值。
- 监视属性更改。

Xamarin.Forms 可绑定属性的示例包括[ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)， [ `Button.BorderRadius` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderRadius/)，和[ `StackLayout.Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/)。 每个可绑定属性都具有对应的`public static readonly`类型的属性[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)同一类上公开的且可绑定属性的标识符。 例如，对应可绑定属性的标识符为`Label.Text`属性是[ `Label.TextProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Label.TextProperty/)。

<a name="consuming-bindable-property" />

## <a name="creating-and-consuming-a-bindable-property"></a>创建和使用可绑定属性

用于创建可绑定属性的过程如下所示：

1. 创建[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)实例之一[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法重载。
1. 定义属性的访问器[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)实例。

请注意，所有[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)必须在 UI 线程上创建实例。 这意味着在 UI 线程运行的代码可以获取或设置可绑定属性的值。 但是，`BindableProperty`通过封送到 UI 线程情况下，实例可以访问来自其他线程[ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/)方法。

### <a name="creating-a-property"></a>创建的属性

若要创建`BindableProperty`实例，包含的类必须派生自[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)类。 但是，`BindableObject`类是高类层次结构中，因此类的大部分用于用户界面功能支持可绑定属性。

可以通过声明创建可绑定属性`public static readonly`类型的属性[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)。 可绑定属性应设置为返回的值之一的[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法重载。 声明应在主体中[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)派生类，但在任何成员定义的外部。

创建时，则必须指定至少一个标识符[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)，以及以下参数：

- 名称[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)。
- 属性的类型。
- 所属对象的类型。
- 属性的默认值。 这可确保该属性总是返回特定的默认值，已取消设置，并且它可以不同于属性的类型的默认值时。 默认值将为还原时[ `ClearValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.ClearValue/p/Xamarin.Forms.BindableProperty/)方法调用的可绑定属性。

下面的代码演示可绑定属性，与某个标识符及四个必需参数的值的示例：

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

这将创建[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)实例名为`EventName`，类型的`string`。 该属性属于`EventToCommandBehavior`类，并提供默认值为`null`。 可绑定属性的命名约定是可绑定属性标识符必须匹配中指定的属性名称`Create`方法，使用"属性"追加到它。 因此，在上面的示例中，可绑定属性标识符是`EventNameProperty`。

（可选） 创建时[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)实例，以下可以指定参数：

- 绑定模式中。 这用于指定属性值更改将在其中进行传播的方向。 在默认绑定模式中，更改将传播从*源*到*目标*。
- 当设置了属性值时将调用一个验证委托。 有关详细信息，请参阅[验证回调](#validation)。
- 属性更改属性值更改时将调用的委托。 有关详细信息，请参阅[检测属性更改](#propertychanges)。
- 更改属性值将更改时将调用的委托的一个属性。 此委托具有与属性更改后委托相同的签名。
- 属性值更改时将调用一个强制值委托。 有关详细信息，请参阅[Coerce 值回调](#coerce)。
- A`Func`用于初始化默认属性值。 有关详细信息，请参阅[使用 Func 创建默认值](#defaultfunc)。

### <a name="creating-accessors"></a>创建访问器

属性访问器需要使用属性语法访问可绑定属性。 `Get`访问器应返回相应的可绑定属性中包含的值。 这可以通过调用实现[ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/)方法、 传入要获取的值，依据的可绑定的属性标识符，然后将结果转换到所需的类型。 `Set`访问器应设置相应的可绑定属性的值。 这可以通过调用实现[ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法，同时传入在其上设置值和要设置的值的可绑定的属性标识符。

下面的代码示例演示的访问器`EventName`可绑定的属性：

```csharp
public string EventName {
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

### <a name="consuming-a-bindable-property"></a>使用可绑定属性

一旦创建可绑定属性后，可以从 XAML 或代码使用它。 在 XAML 中，这被通过使用，该值指示 CLR 命名空间名称和 （可选） 程序集名称的命名空间声明中声明的带有前缀的命名空间。 有关详细信息，请参阅[XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)。

下面的代码示例演示了包含可绑定属性，它将在相同的程序集中引用的自定义类型的应用程序代码中定义的自定义类型的 XAML 命名空间：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

设置时使用命名空间声明`EventName`可绑定属性，详见以下 XAML 代码示例：

```xaml
<ListView ...>
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

以下代码示例显示相应的 C# 代码：

```csharp
var listView = new ListView ();
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  ...
});
```

<a name="advanced" />

## <a name="advanced-scenarios"></a>高级的方案

在创建时[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)实例时，有了多个可选参数，可设置为启用高级可绑定属性方案。 本部分探讨这些方案。

<a name="propertychanges" />

### <a name="detecting-property-changes"></a>检测属性更改

A`static`属性更改回叫方法可由指定注册与可绑定属性`propertyChanged`参数[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法。 可绑定属性的值更改时，将调用的指定的回调方法。

下面的代码示例演示如何`EventName`可绑定属性寄存器`OnEventNameChanged`作为属性更改回调方法的方法：

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create (
    "EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
...

static void OnEventNameChanged (BindableObject bindable, object oldValue, object newValue)
{
  // Property changed implementation goes here
}
```

在属性更改回调方法中， [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)参数用于表示在所属类的实例已报告某项更改以及这两个值`object`参数表示的新旧值可绑定属性。

<a name="validation" />

### <a name="validation-callbacks"></a>验证回叫

A`static`验证回调方法可以通过指定注册与可绑定属性`validateValue`参数[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法。 设置可绑定属性的值时，将调用的指定的回调方法。

下面的代码示例演示如何`Angle`可绑定属性寄存器`IsValidValue`作为验证回调方法的方法：

```csharp
public static readonly BindableProperty AngleProperty =
  BindableProperty.Create ("Angle", typeof(double), typeof(HomePage), 0.0, validateValue: IsValidValue);
...

static bool IsValidValue (BindableObject view, object value)
{
  double result;
  bool isDouble = double.TryParse (value.ToString (), out result);
  return (result >= 0 && result <= 360);
}
```

验证回调提供一个值，并应返回`true`值是否有效的属性，否则`false`。 如果验证回调返回，则将引发异常`false`，应由开发人员。 可绑定属性设置时，验证回调方法的一个典型用途约束整数或双精度型值的值。 例如，`IsValidValue`方法检查该属性值将`double`介于 0 至 360。

<a name="coerce" />

### <a name="coerce-value-callbacks"></a>强制值回调

A `static` coerce 值可由指定的可绑定属性为注册回调方法`coerceValue`参数[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法。 可绑定属性的值更改时，将调用的指定的回调方法。

将强制回调用于强制可绑定属性的重新评估，该属性的值更改时的值。 例如，可以使用强制值回调，以确保一个可绑定属性的值不是大于另一个可绑定属性的值。

下面的代码示例演示如何`Angle`可绑定属性寄存器`CoerceAngle`作为强制值回调方法的方法：

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle) {
    input = homePage.MaximumAngle;
  }

  return input;
}
```

`CoerceAngle`方法检查的值`MaximumAngle`属性，并且如果`Angle`属性值大于它、 它强制转换为值`MaximumAngle`属性值。

<a name="defaultfunc" />

### <a name="creating-a-default-value-with-a-func"></a>使用 Func 创建默认值

A`Func`可以用于初始化默认值的可绑定的属性，如下面的代码示例中所示：

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

`defaultValueCreator`参数设置为`Func`，它调用[ `Device.GetNamedSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/System.Type/)方法以返回`double`表示字体上所用的命名的大小[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)本机平台上。

## <a name="summary"></a>总结

本文提供可绑定属性的简介，并演示了如何创建和使用它们。 可绑定属性是属性的一种特殊类型，其中通过 Xamarin.Forms 属性系统跟踪属性的值。


## <a name="related-links"></a>相关链接

- [XAML 命名空间](~/xamarin-forms/xaml/namespaces.md)
- [事件到，命令行为 （示例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [验证回调 （示例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/validationcallback/)
- [Coerce 值回调 （示例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/coercevaluecallback/)
- [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)
