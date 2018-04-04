---
title: 可重用 EffectBehavior
description: 行为是将效果添加到控件中，删除的样板影响处理从代码隐藏文件的代码很有用的方法。 本文演示如何使用 Xamarin.Forms 行为添加到控件的效果。
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: a1612d1e87f0e05c859babd93fd03ac9a5736b47
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="reusable-effectbehavior"></a>可重用 EffectBehavior

_行为是将效果添加到控件中，删除的样板影响处理从代码隐藏文件的代码很有用的方法。本文演示如何使用 Xamarin.Forms 行为添加到控件的效果。_

## <a name="overview"></a>概述

`EffectBehavior`类是添加的可重用 Xamarin.Forms 自定义行为[ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)控制何时行为附加到控件，并删除实例`Effect`实例时的行为是从控件中分离。

以下行为属性必须设置为使用行为：

- **组**– 的值[ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/)影响该类的属性。
- **名称**– 的值[ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/)影响该类的属性。

有关效果的详细信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

## <a name="creating-the-behavior"></a>创建行为

`EffectBehavior`类派生自[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)类，其中`T`是[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)。 这意味着，`EffectBehavior`类可以附加到任何 Xamarin.Forms 控件。

### <a name="implementing-bindable-properties"></a>实现可绑定属性

`EffectBehavior`类定义了两个[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)实例，用于添加[ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)控制何时行为附加到控件。 这些属性下面的代码示例所示：

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

当`EffectBehavior`使用时，`Group`属性应设置为的值[ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/)效果的属性。 此外，`Name`属性应设置为的值[ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/)影响该类的属性。

### <a name="implementing-the-overrides"></a>实现重写

`EffectBehavior`类重写[ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)和[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)类，如下面的代码中所示示例：

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

[ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)方法通过调用执行安装程序`AddEffect`方法，并在附加控件中作为参数传递。 [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法通过调用执行清理`RemoveEffect`方法，并在附加控件中作为参数传递。

### <a name="implementing-the-behavior-functionality"></a>实现行为功能

行为旨在将添加[ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)中定义`Group`和`Name`属性控制何时行为附加到控件，并删除`Effect`时的行为是从控件中分离。 核心行为功能下面的代码示例所示：

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

`AddEffect`方法执行以响应`EffectBehavior`要附加到控件，并且它接收作为参数附加的控件。 然后，该方法将检索到的效果添加到控件的[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。 `RemoveEffect`方法执行以响应`EffectBehavior`从一个控件，和它要分离接收作为参数附加的控件。 方法然后从控件的删除效果[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。

`GetEffect`方法使用[ `Effect.Resolve` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.Resolve/p/System.String/)方法来检索[ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)。 效果是通过串联所在`Group`和`Name`属性值。 如果平台不提供效果，`Effect.Resolve`方法将返回一个非`null`值。

## <a name="consuming-the-behavior"></a>使用行为

`EffectBehavior`类可以附加到[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)集合的控件，如下面的 XAML 代码示例中所示：

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

`Group`和`Name`的行为的属性设置的值为[ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/)和[ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/)中每个特定于平台的影响类的属性项目。

在运行时，行为附加到[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控件，`Xamarin.LabelShadowEffect`将添加到控件的[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。 这会导致正在添加到显示的文本阴影`Label`控制，如以下屏幕截图中所示：

![](effect-behavior-images/screenshots.png "与 EffectsBehavior 示例应用程序")

使用此行为来添加和移除效果从控件的优点是，可以从代码隐藏文件中删除的样板影响处理代码。

## <a name="summary"></a>总结

这篇文章演示了使用行为来向控件添加效果。 `EffectBehavior`类是添加的可重用 Xamarin.Forms 自定义行为[ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)控制何时行为附加到控件，并删除实例`Effect`实例时的行为是从控件中分离。


## <a name="related-links"></a>相关链接

- [效果](~/xamarin-forms/app-fundamentals/effects/index.md)
- [影响行为 （示例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [Behavior](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [行为<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
