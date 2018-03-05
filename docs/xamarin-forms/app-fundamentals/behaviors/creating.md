---
title: Xamarin.Forms Behaviors
description: "Xamarin.Forms 行为由派生自的行为或行为<T>类。 本文演示如何创建和使用 Xamarin.Forms 行为。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: c70e4c9ec49b48c3bf6ecc6a4944d992f8ae930a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinforms-behaviors"></a>Xamarin.Forms Behaviors

_Xamarin.Forms 行为由派生自的行为或行为<T>类。本文演示如何创建和使用 Xamarin.Forms 行为。_

## <a name="overview"></a>概述

用于创建 Xamarin.Forms 行为的过程如下所示：

1. 创建继承自的类[ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)或[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)类，其中`T`是行为将应用到该控件的类型。
1. 重写[ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)方法来执行任何所需的安装程序。
1. 重写[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法来执行任何所需的清理。
1. 实现的行为的核心功能。

这将导致下面的代码示例中所示的结构：

```csharp
public class CustomBehavior : Behavior<View>
{
    protected override void OnAttachedTo (View bindable)
    {
        base.OnAttachedTo (bindable);
        // Perform setup
    }

    protected override void OnDetachingFrom (View bindable)
    {
        base.OnDetachingFrom (bindable);
        // Perform clean up
    }

    // Behavior implementation
}
```

[ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)行为附加到控件后立即触发方法。 此方法接收对控件到它附加，并可用来注册事件处理程序或执行其他安装程序将支持行为功能所需的引用。 例如，你可以订阅到上一个控件的事件。 然后将该事件的事件处理程序中实现的行为功能。

[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法引发时从控件中移除的行为。 此方法接收对它可以向其附加，并用于执行任何所需的清理控件的引用。 例如，你无法取消订阅从上一个控件，用于防止内存泄漏的事件。

通过将其附加到也可以使用行为[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)相应的控件的集合。

## <a name="creating-a-xamarinforms-behavior"></a>创建 Xamarin.Forms 行为

示例应用程序演示`NumericValidationBehavior`，其中突出显示到用户输入的值[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)如果它不控制为红色， `double`。 在下面的代码示例演示的行为：

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    protected override void OnAttachedTo(Entry entry)
    {
        entry.TextChanged += OnEntryTextChanged;
        base.OnAttachedTo(entry);
    }

    protected override void OnDetachingFrom(Entry entry)
    {
        entry.TextChanged -= OnEntryTextChanged;
        base.OnDetachingFrom(entry);
    }

    void OnEntryTextChanged(object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

`NumericValidationBehavior`派生自[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)类，其中`T`是[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)。 [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)方法注册的事件处理程序[ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/)事件，与[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法取消注册`TextChanged`事件，以防止内存泄漏。 提供的行为的核心功能是`OnEntryTextChanged`方法，将到用户输入的值分析`Entry`，并将设置[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/)属性为红色，如果该值不`double`。

> [!NOTE]
> **请注意**: Xamarin.Forms 不会设置`BindingContext`的行为，因为可以共享行为，并将其应用于通过样式的多个控件。

## <a name="consuming-a-xamarinforms-behavior"></a>使用 Xamarin.Forms 行为

Xamarin.Forms 中的每个控件具有[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)集合，其中一个或多个行为可向添加，如下面的 XAML 代码示例中所示：

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

等效于[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) C# 中所示下面的代码示例：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

在运行时行为将根据行为实现响应对与该控件的交互。 以下屏幕截图演示对无效输入进行响应的行为：

[ ![](creating-images/screenshots-sml.png "示例应用程序与 Xamarin.Forms 行为")](creating-images/screenshots.png "示例应用程序与 Xamarin.Forms 行为")

> [!NOTE]
> **请注意**： 行为针对特定的控件类型 （或超类可以应用于很多控件） 编写的并且它们仅应添加到兼容的控件。 尝试将行为附加到不兼容的控件将导致引发异常。

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>使用 Xamarin.Forms 行为的方式

显式或隐式样式也可以使用行为。 但是，创建设置样式[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)控件的属性不能，因为该属性是只读的。 解决方案是将附加的属性添加到行为类，用于控制添加和删除行为。 过程如下所示：

1. 将附加的属性添加到可用于控制的添加或删除附加到的行为将控件的行为的行为类。 确保附加的属性，注册`propertyChanged`属性的值更改时将执行的委托。
1. 创建`static`getter 和 setter 附加属性。
1. 实现中的逻辑`propertyChanged`委托来添加和删除行为。

下面的代码示例演示一个附加的属性，用于控制添加和删除`NumericValidationBehavior`:

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached ("AttachBehavior", typeof(bool), typeof(NumericValidationBehavior), false, propertyChanged: OnAttachBehaviorChanged);

    public static bool GetAttachBehavior (BindableObject view)
    {
        return (bool)view.GetValue (AttachBehaviorProperty);
    }

    public static void SetAttachBehavior (BindableObject view, bool value)
    {
        view.SetValue (AttachBehaviorProperty, value);
    }

    static void OnAttachBehaviorChanged (BindableObject view, object oldValue, object newValue)
    {
        var entry = view as Entry;
        if (entry == null) {
            return;
        }

        bool attachBehavior = (bool)newValue;
        if (attachBehavior) {
            entry.Behaviors.Add (new NumericValidationBehavior ());
        } else {
            var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
            if (toRemove != null) {
                entry.Behaviors.Remove (toRemove);
            }
        }
    }
    ...
}
```

`NumericValidationBehavior`类包含附加的属性名为`AttachBehavior`与`static`getter 和 setter，它控制的添加或删除到它将附加到控件的行为。 此附加属性寄存器`OnAttachBehaviorChanged`属性的值更改时将执行的方法。 此方法添加或删除到控件中，基于值的行为`AttachBehavior`附加属性。

下面的代码示例演示*显式*样式为`NumericValidationBehavior`使用`AttachBehavior`附加属性，以及其可应用于附加[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件：

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)可应用于[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)通过设置其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性`Style`实例使用`StaticResource`标记扩展，如下面的代码示例中所示：

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

> [!NOTE]
> **请注意**： 尽管您可以添加的行为，使进行设置或查询在 XAML 中，如果你执行的创建行为的可绑定属性具有状态它们不应共享中的控件之间`Style`中`ResourceDictionary`。

### <a name="removing-a-behavior-from-a-control"></a>删除从控件的行为

[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法引发行为会删除通过控件，并且用于执行任何所需的清理，例如取消订阅事件，以防止内存泄漏。 但是，行为不会隐式删除从控件除非控件的[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)通过修改集合`Remove`或`Clear`方法。 下面的代码示例演示了如何从一个控件中移除特定行为`Behaviors`集合：

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

或者，该控件的[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)可以清除集合，如下面的代码示例中所示：

```csharp
entry.Behaviors.Clear();
```

此外，请注意，行为不会隐式删除从控件时从导航堆栈中弹出页。 相反，它们之前，必须显式删除传出作用域的页。

## <a name="summary"></a>摘要

这篇文章演示了如何创建和使用 Xamarin.Forms 行为。 Xamarin.Forms 行为由派生自[ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)或[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)类。


## <a name="related-links"></a>相关链接

- [Xamarin.Forms 行为 （示例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehavior/)
- [Xamarin.Forms 行为应用了样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehaviorstyle/)
- [Behavior](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [行为<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
