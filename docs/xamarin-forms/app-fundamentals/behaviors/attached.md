---
title: 附加的行为
description: 附加的行为是具有一个或多个附加属性的静态类。 本文演示如何创建和使用附加的行为。
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: af891ad1ff1389d5a48c6c47ba1914b8d4dfc20f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="attached-behaviors"></a>附加的行为

_附加的行为是具有一个或多个附加属性的静态类。本文演示如何创建和使用附加的行为。_

## <a name="overview"></a>概述

附加的属性是一种特殊类型的可绑定属性。 它们是一个类中定义，但附加到其他对象，并且它们是可以识别在 XAML 中作为包含的类和句点分隔的属性名称的属性。

附加的属性可以定义`propertyChanged`属性的值更改，如当属性设置在控件上时，将执行的委托。 当`propertyChanged`委托执行时，它已传递到在其将其正在附加的控件及其包含的属性的旧和新值的参数的引用。 此委托可以用于将新功能添加到该属性附加到通过操作传入，如下所示的引用的控件：

1. `propertyChanged`委托转换控件引用，即在收到作为[ `BindableObject`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)到行为的控件类型旨在增强。
1. `propertyChanged`委托修改控件属性，公开由控件，若要实现的核心行为功能的事件的控件或寄存器事件处理程序调用方法。

附加行为的一个问题是在定义`static`类，与`static`属性和方法。 这使得难以创建有状态的附加的行为。 此外，Xamarin.Forms 行为具有作为首选的方法与行为构造替代附加的行为。 有关各种 Xamarin.Forms 行为的详细信息，请参阅[Xamarin.Forms 行为](~/xamarin-forms/app-fundamentals/behaviors/creating.md)和[可重用行为](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。

## <a name="creating-an-attached-behavior"></a>创建一个附加的行为

示例应用程序演示`NumericValidationBehavior`，其中突出显示到用户输入的值[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)如果它不控制为红色， `double`。 在下面的代码示例演示的行为：

```csharp
public static class NumericValidationBehavior
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached (
            "AttachBehavior",
            typeof(bool),
            typeof(NumericValidationBehavior),
            false,
            propertyChanged:OnAttachBehaviorChanged);

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
            entry.TextChanged += OnEntryTextChanged;
        } else {
            entry.TextChanged -= OnEntryTextChanged;
        }
    }

    static void OnEntryTextChanged (object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

`NumericValidationBehavior`类包含附加的属性名为`AttachBehavior`与`static`getter 和 setter，它控制的添加或删除到它将附加到控件的行为。 此附加属性寄存器`OnAttachBehaviorChanged`属性的值更改时将执行的方法。 此方法注册或取消注册的事件处理程序[ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/)事件，基于值`AttachBehavior`附加属性。 提供的行为的核心功能是`OnEntryTextChanged`方法，将进入值分析[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)通过用户，并设置`TextColor`属性为红色，如果该值不`double`。

## <a name="consuming-an-attached-behavior"></a>使用附加的行为

`NumericValidationBehavior`类可供添加`AttachBehavior`附加到属性[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制，如下面的 XAML 代码示例中所示：

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

等效于[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) C# 中所示下面的代码示例：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

在运行时，行为将根据行为实现响应对与该控件的交互。 以下屏幕截图演示对无效输入进行响应的附加的行为：

[![](attached-images/screenshots-sml.png "示例应用程序与附加行为")](attached-images/screenshots.png#lightbox "示例与附加行为的应用程序")

> [!NOTE]
> 附加的行为针对特定的控件类型 （或超类可以应用于很多控件） 编写的并且它们仅应添加到兼容的控件。 尝试将行为附加到不兼容的控件将导致未知行为，并且依赖于行为实现。

### <a name="removing-an-attached-behavior-from-a-control"></a>从控件中删除附加的行为

`NumericValidationBehavior`通过设置情况下，类可以删除从控件`AttachBehavior`附加到属性`false`，如下面的 XAML 代码示例所示：

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

等效于[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) C# 中所示下面的代码示例：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

在运行时，`OnAttachBehaviorChanged`方法将时执行的值`AttachBehavior`附加的属性设置为`false`。 `OnAttachBehaviorChanged`方法将然后取消注册的事件处理程序[ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/)事件，确保不执行行为，因为与该控件交互用户。

## <a name="summary"></a>总结

这篇文章演示了如何创建和使用附加的行为。 附加的行为是`static`具有一个或多个附加属性的类。


## <a name="related-links"></a>相关链接

- [附加的行为 （示例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/attachednumericvalidationbehavior/)
