---
title: 可重用 EffectBehavior
description: 行为是将效果添加到控件时，删除处理代码中的代码隐藏文件的样板效果很有用的方法。 本文演示如何使用 Xamarin.Forms 行为添加到控件的效果。
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 1ce7eda6f556041cbffc3793b00e8e2cba44d3d0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995776"
---
# <a name="reusable-effectbehavior"></a>可重用 EffectBehavior

_行为是将效果添加到控件时，删除处理代码中的代码隐藏文件的样板效果很有用的方法。本文演示如何使用 Xamarin.Forms 行为添加到控件的效果。_

## <a name="overview"></a>概述

`EffectBehavior`类是一个可重用的 Xamarin.Forms 自定义行为，将添加[ `Effect` ](xref:Xamarin.Forms.Effect)实例与控件时的行为附加到控件，并且删除`Effect`实例时的行为是从控件中分离。

以下行为属性必须设置为使用行为：

- **组**– 的值[ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute)影响类的属性。
- **名称**– 的值[ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute)影响类的属性。

有关效果的详细信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

## <a name="creating-the-behavior"></a>创建行为

`EffectBehavior`类派生自[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)类，其中`T`是[ `View` ](xref:Xamarin.Forms.View)。 这意味着，`EffectBehavior`类可以附加到任何 Xamarin.Forms 控件。

### <a name="implementing-bindable-properties"></a>实现可绑定属性

`EffectBehavior`类定义了两个[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)实例，用于添加[ `Effect` ](xref:Xamarin.Forms.Effect)到控件时行为附加到控件。 这些属性下面的代码示例所示：

```csharp
public class EffectBehavior : Behavior<View>
{
  public static readonly BindableProperty GroupProperty =
    BindableProperty.Create ("Group", typeof(string), typeof(EffectBehavior), null);
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(EffectBehavior), null);

  public string Group {
    get { return (string)GetValue (GroupProperty); }
    set { SetValue (GroupProperty, value); }
  }

  public string Name {
    get { return(string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }
  ...
}
```

当`EffectBehavior`用`Group`属性应设置为的值[ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute)效果的属性。 此外，`Name`属性应设置为值[ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute)影响类的属性。

### <a name="implementing-the-overrides"></a>实现重写

`EffectBehavior`类将重写[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))并[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)类，如以下代码所示示例：

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  protected override void OnAttachedTo (BindableObject bindable)
  {
    base.OnAttachedTo (bindable);
    AddEffect (bindable as View);
  }

  protected override void OnDetachingFrom (BindableObject bindable)
  {
    RemoveEffect (bindable as View);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))方法执行安装程序通过调用`AddEffect`方法，在附加的控件作为参数传递。 [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法通过调用执行清理`RemoveEffect`方法，在附加的控件作为参数传递。

### <a name="implementing-the-behavior-functionality"></a>实现行为功能

行为的目的是要添加[ `Effect` ](xref:Xamarin.Forms.Effect)中定义`Group`并`Name`属性，以控制时的行为附加到控件，并且删除`Effect`时的行为是从控件中分离。 核心行为功能下面的代码示例所示：

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  void AddEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Add (GetEffect ());
    }
  }

  void RemoveEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Remove (GetEffect ());
    }
  }

  Effect GetEffect ()
  {
    if (!string.IsNullOrWhiteSpace (Group) && !string.IsNullOrWhiteSpace (Name)) {
      return Effect.Resolve (string.Format ("{0}.{1}", Group, Name));
    }
    return null;
  }
}
```

`AddEffect`方法执行响应`EffectBehavior`附加到一个控件，并接收作为参数的附加的控件。 该方法然后将检索到的效果添加到控件的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。 `RemoveEffect`方法执行响应`EffectBehavior`正在分离从一个控件，并接收作为参数的附加的控件。 方法然后从控件的删除效果[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。

`GetEffect`方法使用[ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String))方法来检索[ `Effect` ](xref:Xamarin.Forms.Effect)。 效果是通过串联所在`Group`和`Name`属性值。 如果一个平台不提供效果`Effect.Resolve`方法将返回一个非`null`值。

## <a name="consuming-the-behavior"></a>使用行为

`EffectBehavior`类可以附加到[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors)集合的一个控件，如以下 XAML 代码示例所示：

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Behaviors>
    <local:EffectBehavior Group="Xamarin" Name="LabelShadowEffect" />
  </Label.Behaviors>
</Label>
```

以下代码示例显示相应的 C# 代码：

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};
label.Behaviors.Add (new EffectBehavior {
  Group = "Xamarin",
  Name = "LabelShadowEffect"
});
```

`Group`并`Name`行为的属性设置为的值[ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute)并[ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute)中每个特定于平台的影响类的属性项目。

在运行时，行为附加到时[ `Label` ](xref:Xamarin.Forms.Label)控件，`Xamarin.LabelShadowEffect`将添加到控件的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。 这会导致要添加到显示的文本阴影`Label`控制，如以下屏幕截图中所示：

![](effect-behavior-images/screenshots.png "包含 EffectsBehavior 示例应用程序")

使用此行为来添加和删除效果从控件的优点是样板效果处理代码可从代码隐藏文件。

## <a name="summary"></a>总结

本文演示了使用行为添加到控件的效果。 `EffectBehavior`类是一个可重用的 Xamarin.Forms 自定义行为，将添加[ `Effect` ](xref:Xamarin.Forms.Effect)实例与控件时的行为附加到控件，并且删除`Effect`实例时的行为是从控件中分离。


## <a name="related-links"></a>相关链接

- [效果](~/xamarin-forms/app-fundamentals/effects/index.md)
- [影响行为 （示例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [行为](xref:Xamarin.Forms.Behavior)
- [行为<T>](xref:Xamarin.Forms.Behavior`1)
