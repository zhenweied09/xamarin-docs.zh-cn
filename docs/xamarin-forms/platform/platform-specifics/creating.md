---
title: 创建平台细节
description: 供应商可以使用效果创建其自己的平台特定信息。 一种效果提供与特定的功能，则通过特定于平台的公开。 结果是完成的 XAML，并通过 fluent 代码 API 可以更轻松地消耗的效果。 本文演示如何公开通过特定于平台的效果。
ms.prod: xamarin
ms.assetid: 0D0E6274-6EF2-4D40-BB77-3D8E53BCD24B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/23/2016
ms.openlocfilehash: dcd22dd0d4e281245a1f6598d9a58d24a97b1f20
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848143"
---
# <a name="creating-platform-specifics"></a>创建平台细节

_供应商可以使用效果创建其自己的平台特定信息。一种效果提供与特定的功能，则通过特定于平台的公开。结果是完成的 XAML，并通过 fluent 代码 API 可以更轻松地消耗的效果。本文演示如何公开通过特定于平台的效果。_

## <a name="overview"></a>概述

用于创建特定于平台的过程如下所示：

1. 实现作为效果的特定功能。 有关详细信息，请参阅[创建效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。
1. 创建将公开效果的特定于平台的类。 有关详细信息，请参阅[创建特定于平台的类](#creating)。
1. 在特定于平台的类中，实现要允许特定于平台的通过 XAML 使用的附加的属性。 有关详细信息，请参阅[添加附加属性](#attached_property)。
1. 在特定于平台的类中，实现了扩展方法，以允许特定于平台的使用通过 fluent 代码 API。 有关详细信息，请参阅[添加扩展方法](#extension_methods)。
1. 修改的效果实现，以便当已为的效果相同的平台上调用特定于平台的效果才适用。 有关详细信息，请参阅[创建效果](#creating_the_effect)。

公开特定于平台的效果的结果是，将可以更轻松地使用通过 XAML 和 fluent 代码 API。

> [!NOTE]
> 它被提出供应商将使用此方法来创建其自己平台的详细信息，以便于用户的消耗。 尽管用户可以选择创建其自己的平台的详细信息，但应该指出它需要比创建和使用效果的更多代码。

示例应用程序演示`Shadow`特定于平台的添加到显示的文本的阴影[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控件：

![](creating-images/screenshots.png "隐藏特定于平台的")

本示例应用程序实现`Shadow`特定于平台的每个平台，以便于了解上。 但是，除了每个特定于平台的效果实现，卷影类的实现是为每个平台大致相同。 因此，本指南重点介绍的卷影类和关联的影响单个平台的实现。

有关效果的详细信息，请参阅[使用效果自定义控件](~/xamarin-forms/app-fundamentals/effects/index.md)。

<a name="creating" />

## <a name="creating-a-platform-specific-class"></a>创建特定于平台的类

特定于平台的创建为`public static`类：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

以下各节讨论的实现`Shadow`特定于平台的和关联的效果。

<a name="attached_property" />

### <a name="adding-an-attached-property"></a>添加附加的属性

必须将附加的属性添加到`Shadow`特定于平台的表示允许通过 XAML 使用：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        const string EffectName = "MyCompany.LabelShadowEffect";

        public static readonly BindableProperty IsShadowedProperty =
            BindableProperty.CreateAttached("IsShadowed",
                                            typeof(bool),
                                            typeof(Shadow),
                                            false,
                                            propertyChanged: OnIsShadowedPropertyChanged);

        public static bool GetIsShadowed(BindableObject element)
        {
            return (bool)element.GetValue(IsShadowedProperty);
        }

        public static void SetIsShadowed(BindableObject element, bool value)
        {
            element.SetValue(IsShadowedProperty, value);
        }

        ...

        static void OnIsShadowedPropertyChanged(BindableObject element, object oldValue, object newValue)
        {
            if ((bool)newValue)
            {
                AttachEffect(element as FormsElement);
            }
            else
            {
                DetachEffect(element as FormsElement);
            }
        }

        static void AttachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || controller.EffectIsAttached(EffectName))
            {
                return;
            }
            element.Effects.Add(Effect.Resolve(EffectName));
        }

        static void DetachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || !controller.EffectIsAttached(EffectName))
            {
                return;
            }

            var toRemove = element.Effects.FirstOrDefault(e => e.ResolveId == Effect.Resolve(EffectName).ResolveId);
            if (toRemove != null)
            {
                element.Effects.Remove(toRemove);
            }
        }
    }
}
```

`IsShadowed`附加的属性用于将添加`MyCompany.LabelShadowEffect`影响，并将其删除从控件的`Shadow`类附加到。 此附加属性寄存器`OnIsShadowedPropertyChanged`属性的值更改时将执行的方法。 反过来，此方法调用`AttachEffect`或`DetachEffect`方法来添加或删除效果基于值`IsShadowed`附加属性。 添加到或从控件中移除通过修改控件的效果[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。

> [!NOTE]
> 请注意，被指定为串联的解析组名称和的效果实现指定的唯一标识符的值解析效果。 有关详细信息，请参阅[创建效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。

有关附加属性的详细信息，请参阅[附加属性](~/xamarin-forms/xaml/attached-properties.md)。

<a name="extension_methods" />

### <a name="adding-extension-methods"></a>添加扩展方法

扩展方法必须添加到`Shadow`特定于平台的表示允许通过 fluent 代码 API 的使用：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        ...
        public static bool IsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config)
        {
            return GetIsShadowed(config.Element);
        }

        public static IPlatformElementConfiguration<iOS, FormsElement> SetIsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config, bool value)
        {
            SetIsShadowed(config.Element, value);
            return config;
        }
        ...
    }
}
```

`IsShadowed`和`SetIsShadowed`扩展方法调用 get 和 set 访问器为`IsShadowed`附加属性，分别。 每个扩展方法的操作上`IPlatformElementConfiguration<iOS, FormsElement>`类型，指定可以对调用特定于平台的[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)从 iOS 的实例。

<a name="creating_the_effect" />

### <a name="creating-the-effect"></a>创建效果

`Shadow`特定于平台的添加`MyCompany.LabelShadowEffect`到[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)，并将其删除。 下面的代码示例演示`LabelShadowEffect`实现针对 iOS 项目：

```csharp
[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace ShadowPlatformSpecific.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            UpdateShadow();
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (args.PropertyName == Shadow.IsShadowedProperty.PropertyName)
            {
                UpdateShadow();
            }
        }

        void UpdateShadow()
        {
            try
            {
                if (((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.CornerRadius = 5;
                    Control.Layer.ShadowColor = UIColor.Black.CGColor;
                    Control.Layer.ShadowOffset = new CGSize(5, 5);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
                else if (!((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.ShadowOpacity = 0;
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`UpdateShadow`方法设置`Control.Layer`属性以创建卷影，提供`IsShadowed`附加的属性设置为`true`，并且假定`Shadow`特定于平台的已调用在相同的平台上，影响个的实现。 使用执行此检查`OnThisPlatform`方法。

如果`Shadow.IsShadowed`附加属性值更改在运行时，需要通过删除卷影响应将生效。 因此，重写的版本的`OnElementPropertyChanged`方法用于通过调用可绑定属性更改做出响应`UpdateShadow`方法。

有关创建效果的详细信息，请参阅[创建效果](~/xamarin-forms/app-fundamentals/effects/creating.md)和[传递作为附加属性的效果参数](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md)。

## <a name="consuming-a-platform-specific"></a>使用特定于平台的

`Shadow`设置在 XAML 中使用特定于平台的`Shadow.IsShadowed`附加到属性`boolean`值：

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

有关使用平台特定信息的详细信息，请参阅[使用平台特定信息](~/xamarin-forms/platform/platform-specifics/consuming/index.md)。

## <a name="summary"></a>总结

这篇文章演示了如何公开通过特定于平台的效果。 结果是完成的 XAML，并通过 fluent 代码 API 可以更轻松地消耗的效果。


## <a name="related-links"></a>相关链接

- [ShadowPlatformSpecific （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)
- [使用效果自定义控件](~/xamarin-forms/app-fundamentals/effects/index.md)
- [附加属性](~/xamarin-forms/xaml/attached-properties.md)
