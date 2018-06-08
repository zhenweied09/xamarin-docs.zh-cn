---
title: 传递效果参数作为公共语言运行时属性
description: 公共语言运行时 (CLR) 属性可用来定义不响应运行时属性更改的效果参数。 本文演示如何使用 CLR 属性将参数传递给效果。
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: c85256803da137c850e502cfad917de703b161b5
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846469"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>传递效果参数作为公共语言运行时属性

_公共语言运行时 (CLR) 属性可用来定义不响应运行时属性更改的效果参数。本文演示如何使用 CLR 属性将参数传递给效果。_

用于创建不响应运行时属性更改的影响参数的过程如下所示：

1. 创建`public`类子类[ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)类。 `RoutingEffect`类表示对包装出是通常特定于平台的内部效果的独立于平台的影响。
1. 创建构造函数调用基类构造函数，传递中解析组名称，并指定每个特定于平台的影响类的唯一 ID 的串联。
1. 将属性添加到每个参数传递给该效果的类。

通过实例化效果时指定每个属性的值之前，然后可以将参数传递到效果。

示例应用程序演示`ShadowEffect`，它将阴影显示的文本到[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控件。 下图说明了示例应用程序，以及它们之间的关系中的每个项目的责任：

![](clr-properties-images/shadow-effect.png "卷影影响项目职责")

A [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控制上`HomePage`通过可自定义`LabelShadowEffect`每个特定于平台的项目中。 参数传递给每个`LabelShadowEffect`通过中的属性`ShadowEffect`类。 每个`LabelShadowEffect`类派生自`PlatformEffect`为每个平台的类。 这会导致正在添加到显示的文本阴影`Label`控制，如以下屏幕截图中所示：

![](clr-properties-images/screenshots.png "每个平台上的阴影效果")

## <a name="creating-effect-parameters"></a>创建影响参数

A`public`类子类[ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)类应创建以表示效果参数，如下面的代码示例中所示：

```csharp
public class ShadowEffect : RoutingEffect
{
  public float Radius { get; set; }

  public Color Color { get; set; }

  public float DistanceX { get; set; }

  public float DistanceY { get; set; }

  public ShadowEffect () : base ("MyCompany.LabelShadowEffect")
  {            
  }
}
```

`ShadowEffect`包含表示要传递到每个特定于平台的参数的四个属性`LabelShadowEffect`。 类构造函数调用基类构造函数中，传入参数包含的串联解析组名称，并指定每个特定于平台的影响类的唯一 ID。 因此的新实例的`MyCompany.LabelShadowEffect`将添加到控件的[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合时`ShadowEffect`实例化。

## <a name="consuming-the-effect"></a>使用效果

下面的 XAML 代码示例演示[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)到控件`ShadowEffect`附加：

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Effects>
    <local:ShadowEffect Radius="5" DistanceX="5" DistanceY="5">
      <local:ShadowEffect.Color>
        <OnPlatform x:TypeArguments="Color">
            <On Platform="iOS" Value="Black" />
            <On Platform="Android" Value="White" />
            <On Platform="UWP" Value="Red" />
        </OnPlatform>
      </local:ShadowEffect.Color>
    </local:ShadowEffect>
  </Label.Effects>
</Label>
```

等效于[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) C# 中所示下面的代码示例：

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};

Color color = Color.Default;
switch (Device.RuntimePlatform)
{
    case Device.iOS:
        color = Color.Black;
        break;
    case Device.Android:
        color = Color.White;
        break;
    case Device.UWP:
        color = Color.Red;
        break;
}

label.Effects.Add (new ShadowEffect {
  Radius = 5,
  Color = color,
  DistanceX = 5,
  DistanceY = 5
});
```

在这两个代码示例中，实例`ShadowEffect`之前要添加到控件的指定每个属性的值与实例化类[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。 请注意，`ShadowEffect.Color`属性使用特定于平台的颜色值。 有关详细信息，请参阅[设备类](~/xamarin-forms/platform/device.md)。

## <a name="creating-the-effect-on-each-platform"></a>创建每个平台上的效果

以下各节讨论的特定于平台的实现`LabelShadowEffect`类。

### <a name="ios-project"></a>iOS 项目

下面的代码示例演示`LabelShadowEffect`实现针对 iOS 项目：

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    Control.Layer.CornerRadius = effect.Radius;
                    Control.Layer.ShadowColor = effect.Color.ToCGColor ();
                    Control.Layer.ShadowOffset = new CGSize (effect.DistanceX, effect.DistanceY);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

`OnAttached`方法检索`ShadowEffect`实例，并设置`Control.Layer`属性设置为指定的属性值，以创建卷影。 此功能包装在`try` / `catch`阻止以防效果附加到控件不具有`Control.Layer`属性。 没有实现由`OnDetached`方法因为不是必需的任何清理。

### <a name="android-project"></a>Android 项目

下面的代码示例演示`LabelShadowEffect`实现针对 Android 项目：

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var control = Control as Android.Widget.TextView;
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    float radius = effect.Radius;
                    float distanceX = effect.DistanceX;
                    float distanceY = effect.DistanceY;
                    Android.Graphics.Color color = effect.Color.ToAndroid ();
                    control.SetShadowLayer (radius, distanceX, distanceY, color);
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

`OnAttached`方法检索`ShadowEffect`实例，并调用[ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/)方法，用于创建阴影使用指定的属性值。 此功能包装在`try` / `catch`阻止以防效果附加到控件不具有`Control.Layer`属性。 没有实现由`OnDetached`方法因为不是必需的任何清理。

### <a name="universal-windows-platform-project"></a>通用 Windows 平台项目

下面的代码示例演示`LabelShadowEffect`实现通用 Windows 平台 (UWP) 项目：

```csharp
[assembly: ResolutionGroupName ("Xamarin")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                    if (effect != null) {
                        var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;
                        var shadowLabel = new Label ();
                        shadowLabel.Text = textBlock.Text;
                        shadowLabel.FontAttributes = FontAttributes.Bold;
                        shadowLabel.HorizontalOptions = LayoutOptions.Center;
                        shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;
                        shadowLabel.TextColor = effect.Color;
                        shadowLabel.TranslationX = effect.DistanceX;
                        shadowLabel.TranslationY = effect.DistanceY;

                        ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                        shadowAdded = true;
                    }
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

通用 Windows 平台不提供投影效果，因此`LabelShadowEffect`实现这两个平台上的模拟一个通过添加第二个偏移量[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)落后于主`Label`。 `OnAttached`方法检索`ShadowEffect`实例时，创建新`Label`，和上设置某些布局属性`Label`。 然后，它创建卷影通过设置[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/)， [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)，和[ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)属性控制的颜色和位置`Label`. `shadowLabel`会插入落后于主偏移量`Label`。 此功能包装在`try` / `catch`阻止以防效果附加到控件不具有`Control.Layer`属性。 没有实现由`OnDetached`方法因为不是必需的任何清理。

## <a name="summary"></a>总结

本文演示了使用 CLR 属性将参数传递给效果。 可以使用 CLR 属性定义不响应运行时属性更改的效果参数。


## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)
- [PlatformEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/)
- [RoutingEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)
- [投影效果 （示例）](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
