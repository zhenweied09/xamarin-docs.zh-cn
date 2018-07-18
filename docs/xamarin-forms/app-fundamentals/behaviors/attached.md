---
title: 附加的行为
description: 附加的行为是具有一个或多个附加属性的静态类。 本文演示如何创建和使用附加的行为。
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2c9bd9ad4e7572b9eae6f0073da8a2c8f1e7c9fc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995341"
---
# <a name="attached-behaviors"></a>附加的行为

_附加的行为是具有一个或多个附加属性的静态类。本文演示如何创建和使用附加的行为。_

## <a name="overview"></a>概述

附加的属性是一种特殊的可绑定属性。 它们是一个类中定义，但附加到其他对象，并且它们是可识别在 XAML 中作为包含的类和一个句点分隔的属性名称的属性。

可以定义附加的属性`propertyChanged`属性的值发生更改，例如当属性设置在控件上时将执行的委托。 当`propertyChanged`委托执行时，已通过对附加的控件在其上它正在和包含该属性的旧的和新值的参数的引用。 此委托可用于将新功能添加到该属性附加到通过操作中，按如下所示传递的引用的控件：

1. `propertyChanged`委托将强制转换控件引用，即作为接收[ `BindableObject`](xref:Xamarin.Forms.BindableObject)到的控件类型的行为是用于增强。
1. `propertyChanged`委托修改属性的控件的控件，来实现的核心行为功能公开的事件的控件或注册事件处理程序调用方法。

附加行为的一个问题是它们在定义`static`类，与`static`属性和方法。 这使得难以创建具有状态的附加的行为。 此外，Xamarin.Forms 行为已替换为行为构造的首选方法的附加的行为。 有关 Xamarin.Forms 行为的详细信息，请参阅[Xamarin.Forms 行为](~/xamarin-forms/app-fundamentals/behaviors/creating.md)并[可重用行为](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。

## <a name="creating-an-attached-behavior"></a>创建附加的行为

该示例应用程序演示`NumericValidationBehavior`，其中突出显示了通过到用户输入的值[ `Entry` ](xref:Xamarin.Forms.Entry)控件中以红色，如果不是`double`。 行为是在下面的代码示例所示：

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

`NumericValidationBehavior`类包含一个名为的附加的属性`AttachBehavior`与`static`getter 和 setter，它控制的添加或删除到要附加的控件的行为。 此附加属性寄存器`OnAttachBehaviorChanged`属性的值发生更改时将执行的方法。 此方法注册或取消注册的事件处理程序[ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged)事件，值的基础`AttachBehavior`附加属性。 提供的核心功能的行为`OnEntryTextChanged`方法，将值输入到分析[ `Entry` ](xref:Xamarin.Forms.Entry)的用户和组`TextColor`属性设置为红色，如果该值不`double`。

## <a name="consuming-an-attached-behavior"></a>使用附加的行为

`NumericValidationBehavior`类可供添加`AttachBehavior`附加到的属性[ `Entry` ](xref:Xamarin.Forms.Entry)控制，如以下 XAML 代码示例所示：

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

等效于[ `Entry` ](xref:Xamarin.Forms.Entry) C# 中所示下面的代码示例：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

在运行时，行为将根据行为实现响应与该控件的交互。 下面的屏幕截图演示了如何对无效输入进行响应的附加的行为：

[![](attached-images/screenshots-sml.png "示例应用程序与附加行为")](attached-images/screenshots.png#lightbox "示例使用附加行为的应用程序")

> [!NOTE]
> 附加的行为针对特定的控件类型 （或适用于许多控件的超类） 编写的它们只应添加到一个兼容的控件。 尝试将行为附加到不兼容的控件将导致未知行为，并取决于行为实现。

### <a name="removing-an-attached-behavior-from-a-control"></a>从控件中删除附加的行为

`NumericValidationBehavior`类可以通过设置控件中移除`AttachBehavior`附加属性设置为`false`，如以下 XAML 代码示例所示：

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

等效于[ `Entry` ](xref:Xamarin.Forms.Entry) C# 中所示下面的代码示例：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

在运行时，`OnAttachBehaviorChanged`方法将时执行的值`AttachBehavior`附加的属性设置为`false`。 `OnAttachBehaviorChanged`方法将然后取消注册的事件处理程序[ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged)事件，确保当用户与控件交互时，不会执行行为。

## <a name="summary"></a>总结

本文演示了如何创建和使用附加的行为。 附加的行为是`static`具有一个或多个附加属性的类。


## <a name="related-links"></a>相关链接

- [附加的行为 （示例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/attachednumericvalidationbehavior/)
