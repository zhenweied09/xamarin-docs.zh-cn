---
title: Xamarin.Forms 行为
description: Xamarin.Forms 行为创建的派生自的行为或行为<T>类。 本文演示如何创建和使用 Xamarin.Forms 行为。
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 7e057567ec0bb72e9bcc016d4a9fef3af78a3ea1
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998890"
---
# <a name="xamarinforms-behaviors"></a>Xamarin.Forms 行为

_Xamarin.Forms 行为创建的派生自的行为或行为<T>类。本文演示如何创建和使用 Xamarin.Forms 行为。_

## <a name="overview"></a>概述

创建 Xamarin.Forms 行为的过程如下所示：

1. 创建一个类继承自[ `Behavior` ](xref:Xamarin.Forms.Behavior)或[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)类，其中`T`是一种行为要应用到的控件。
1. 重写[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))方法来执行任何所需的设置。
1. 重写[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法来执行任何所需的清理。
1. 实现的核心功能的行为。

这会导致在下面的代码示例所示的结构：

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

[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))行为附加到控件后将立即激发方法。 此方法将接收到它已附加，并可用于注册事件处理程序或执行其他安装程序支持行为功能所需的控件的引用。 例如，您可以订阅一个控件中的事件。 然后将该事件在事件处理程序实现行为功能。

[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法在从控件中移除行为时被激发。 此方法将接收到它已附加，并用于执行任何所需的清理控件的引用。 例如，可以从上一个控件，以防止内存泄漏事件取消订阅。

然后可以通过将附加到已使用行为[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors)相应的控件的集合。

## <a name="creating-a-xamarinforms-behavior"></a>创建 Xamarin.Forms 行为

该示例应用程序演示`NumericValidationBehavior`，其中突出显示了通过到用户输入的值[ `Entry` ](xref:Xamarin.Forms.Entry)控件中以红色，如果不是`double`。 行为是在下面的代码示例所示：

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

`NumericValidationBehavior`派生自[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)类，其中`T`是[ `Entry` ](xref:Xamarin.Forms.Entry)。 [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))方法注册的事件处理程序[ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged)事件，与[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法取消注册`TextChanged`事件，以防止内存泄漏。 提供的核心功能的行为`OnEntryTextChanged`方法，将为用户输入的值解析`Entry`，并设置[ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor)属性设置为红色，如果该值不`double`。

> [!NOTE]
> Xamarin.Forms 不会设置`BindingContext`的行为，因为可以共享行为，并将其应用于多个控件通过样式。

## <a name="consuming-a-xamarinforms-behavior"></a>使用 Xamarin.Forms 行为

每个 Xamarin.Forms 控件具有[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) ，添加到集合的一个或多个行为可以如以下 XAML 代码示例所示：

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

等效于[ `Entry` ](xref:Xamarin.Forms.Entry) C# 中所示下面的代码示例：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

在运行时行为将根据行为实现响应与该控件的交互。 下面的屏幕截图演示了如何对无效输入进行响应的行为：

[![](creating-images/screenshots-sml.png "示例应用程序与 Xamarin.Forms 行为")](creating-images/screenshots.png#lightbox "示例应用程序与 Xamarin.Forms 行为")

> [!NOTE]
> 行为针对特定的控件类型 （或适用于许多控件的超类） 编写的它们只应添加到一个兼容的控件。 尝试将行为附加到不兼容的控件将导致引发异常。

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>使用 Xamarin.Forms 行为的方式

显式或隐式样式也可以使用行为。 但是，创建设置样式[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors)控件的属性不能，因为该属性是只读的。 解决方案是将附加的属性添加到控件添加和删除行为的行为类。 过程如下所示：

1. 将附加的属性添加到将用于控制添加或删除行为将附加的控件的行为的行为类。 请确保附加的属性注册`propertyChanged`属性的值发生更改时将执行的委托。
1. 创建`static`getter 和 setter 的附加属性。
1. 实现中的逻辑`propertyChanged`委托来添加和删除行为。

下面的代码示例显示了附加的属性，用于控制添加和删除`NumericValidationBehavior`:

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

`NumericValidationBehavior`类包含一个名为的附加的属性`AttachBehavior`与`static`getter 和 setter，它控制的添加或删除到要附加的控件的行为。 此附加属性寄存器`OnAttachBehaviorChanged`属性的值发生更改时将执行的方法。 此方法添加或删除到值的基础的控件行为`AttachBehavior`附加属性。

下面的代码示例演示*显式*的样式`NumericValidationBehavior`，它使用`AttachBehavior`附加属性，并将其应用于[ `Entry` ](xref:Xamarin.Forms.Entry)控件：

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

[ `Style` ](xref:Xamarin.Forms.Style)可应用于[ `Entry` ](xref:Xamarin.Forms.Entry)通过设置其[ `Style` ](xref:Xamarin.Forms.VisualElement.Style)属性设置为`Style`使用实例的步骤`StaticResource`标记扩展，如下面的代码示例中所示：

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

> [!NOTE]
> 虽然您可以添加可绑定属性设置或查询中 XAML，如果执行操作，创建行为的行为将状态它们不应共享中的控件之间`Style`在`ResourceDictionary`。

### <a name="removing-a-behavior-from-a-control"></a>从控件中删除行为

[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法触发行为已从一个控件，以及用于执行任何所需的清理如取消订阅事件，以防止内存泄漏。 但是，行为不会隐式删除从控件除非控件的[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors)通过修改集合`Remove`或`Clear`方法。 下面的代码示例演示了如何从控件的移除特定行为`Behaviors`集合：

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

或者，该控件的[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors)可以清除集合，如下面的代码示例中所示：

```csharp
entry.Behaviors.Clear();
```

此外，请注意，行为不会隐式从删除控件时从导航堆栈中弹出页。 相反，它们之前，必须显式删除超出作用域的页。

## <a name="summary"></a>总结

本文演示了如何创建和使用 Xamarin.Forms 行为。 Xamarin.Forms 行为创建的派生自[ `Behavior` ](xref:Xamarin.Forms.Behavior)或[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)类。


## <a name="related-links"></a>相关链接

- [Xamarin.Forms 行为 （示例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehavior/)
- [Xamarin.Forms 行为应用具有样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehaviorstyle/)
- [行为](xref:Xamarin.Forms.Behavior)
- [行为<T>](xref:Xamarin.Forms.Behavior`1)
