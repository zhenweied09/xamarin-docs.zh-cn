---
title: 作为附加属性的效果参数传递
description: 附加的属性可用于定义响应的运行时属性更改的效果参数。 本文演示如何使用附加属性，以将参数传递给某个效果，请和更改在运行时参数。
ms.prod: xamarin
ms.assetid: DFCDCB9F-17DD-4117-BD53-B4FB206BB387
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 9483e424a74a88ce3f0eb49624bb5315551f2062
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996446"
---
# <a name="passing-effect-parameters-as-attached-properties"></a>作为附加属性的效果参数传递

_附加的属性可用于定义响应的运行时属性更改的效果参数。本文演示如何使用附加属性，以将参数传递给某个效果，请和更改在运行时参数。_

创建响应的运行时属性更改的效果参数的过程如下所示：

1. 创建`static`类，该类包含要传递给影响每个参数的附加的属性。
1. 将其他附加的属性添加到将用于控制添加或删除效果类将附加到的控件的类。 请确保此附加属性寄存器`propertyChanged`属性的值发生更改时将执行的委托。
1. 创建`static`getter 和 setter 为每个附加属性。
1. 实现中的逻辑`propertyChanged`委托来添加和删除效果。
1. 实现内部的嵌套的类`static`类，名为后的效果，其中子类`RoutingEffect`类。 对于构造函数中，调用基类构造函数，传入解析组名称，并已在每个特定于平台的效果类中指定的唯一 ID 的串联。

通过将附加的属性和属性值添加到相应的控件之前，然后可以将参数传递到效果。 此外，还可以指定新的附加的属性值在运行时更改参数。

> [!NOTE]
> 附加的属性是一种特殊的可绑定属性，但附加到其他对象，并可识别在 XAML 中的一个类中定义为包含的类和一个句点分隔的属性名称的属性。 有关详细信息，请参阅[附加属性](~/xamarin-forms/xaml/attached-properties.md)。

该示例应用程序演示`ShadowEffect`，它将到显示的文本阴影[ `Label` ](xref:Xamarin.Forms.Label)控件。 此外，可以在运行时更改阴影的颜色。 下图演示了示例应用程序，以及它们之间的关系的每个项目的职责：

![](attached-properties-images/shadow-effect.png "阴影效果项目职责")

一个[ `Label` ](xref:Xamarin.Forms.Label)对的 control 权限`HomePage`由自定义`LabelShadowEffect`每个特定于平台的项目中。 参数传递给每个`LabelShadowEffect`通过中的附加属性`ShadowEffect`类。 每个`LabelShadowEffect`类派生自`PlatformEffect`为每个平台的类。 这会导致要添加到显示的文本阴影`Label`控制，如以下屏幕截图中所示：

![](attached-properties-images/screenshots.png "每个平台上的阴影效果")

## <a name="creating-effect-parameters"></a>创建效果参数

一个`static`类应创建表示效果参数，如下面的代码示例中所示：

```csharp
public static class ShadowEffect
{
  public static readonly BindableProperty HasShadowProperty =
    BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false, propertyChanged: OnHasShadowChanged);
  public static readonly BindableProperty ColorProperty =
    BindableProperty.CreateAttached ("Color", typeof(Color), typeof(ShadowEffect), Color.Default);
  public static readonly BindableProperty RadiusProperty =
    BindableProperty.CreateAttached ("Radius", typeof(double), typeof(ShadowEffect), 1.0);
  public static readonly BindableProperty DistanceXProperty =
    BindableProperty.CreateAttached ("DistanceX", typeof(double), typeof(ShadowEffect), 0.0);
  public static readonly BindableProperty DistanceYProperty =
    BindableProperty.CreateAttached ("DistanceY", typeof(double), typeof(ShadowEffect), 0.0);

  public static bool GetHasShadow (BindableObject view)
  {
    return (bool)view.GetValue (HasShadowProperty);
  }

  public static void SetHasShadow (BindableObject view, bool value)
  {
    view.SetValue (HasShadowProperty, value);
  }
  ...

  static void OnHasShadowChanged (BindableObject bindable, object oldValue, object newValue)
  {
    var view = bindable as View;
    if (view == null) {
      return;
    }

    bool hasShadow = (bool)newValue;
    if (hasShadow) {
      view.Effects.Add (new LabelShadowEffect ());
    } else {
      var toRemove = view.Effects.FirstOrDefault (e => e is LabelShadowEffect);
      if (toRemove != null) {
        view.Effects.Remove (toRemove);
      }
    }
  }

  class LabelShadowEffect : RoutingEffect
  {
    public LabelShadowEffect () : base ("MyCompany.LabelShadowEffect")
    {
    }
  }
}
```

`ShadowEffect`包含五个附加的属性，则使用`static`getter 和 setter 为每个附加属性。 这些属性的四个表示参数传递给每个特定于平台的`LabelShadowEffect`。 `ShadowEffect`类还定义了`HasShadow`附加属性，用于控制添加或删除到该控件的效果，`ShadowEffect`类附加到。 此附加属性寄存器`OnHasShadowChanged`属性的值发生更改时将执行的方法。 此方法添加或删除值的基础的效果`HasShadow`附加属性。

嵌套`LabelShadowEffect`类，其中子类[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect)类，支持效果添加和删除。 `RoutingEffect`类表示包装内部效果通常特定于平台的一个独立于平台的效果。 这简化了效果删除过程，因为没有编译时访问类型信息特定于平台的效果。 `LabelShadowEffect`构造函数调用基类构造函数，传入参数包含解析组名称，并已在每个特定于平台的效果类中指定的唯一 ID 的串联。 这样效果添加和删除在`OnHasShadowChanged`方法，按如下所示：

- **影响加法**– 的新实例`LabelShadowEffect`添加到控件的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。 这将替换使用[ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String))方法来添加效果。
- **影响删除**– 的第一个实例`LabelShadowEffect`在该控件的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)检索集合并将其删除。

## <a name="consuming-the-effect"></a>使用效果

每个平台专属`LabelShadowEffect`可供添加到的附加的属性[ `Label` ](xref:Xamarin.Forms.Label)控制，如以下 XAML 代码示例所示：

```xaml
<Label Text="Label Shadow Effect" ...
       local:ShadowEffect.HasShadow="true" local:ShadowEffect.Radius="5"
       local:ShadowEffect.DistanceX="5" local:ShadowEffect.DistanceY="5">
  <local:ShadowEffect.Color>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="Black" />
        <On Platform="Android" Value="White" />
        <On Platform="UWP" Value="Red" />
    </OnPlatform>
  </local:ShadowEffect.Color>
</Label>
```

等效于[ `Label` ](xref:Xamarin.Forms.Label) C# 中所示下面的代码示例：

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

ShadowEffect.SetHasShadow (label, true);
ShadowEffect.SetRadius (label, 5);
ShadowEffect.SetDistanceX (label, 5);
ShadowEffect.SetDistanceY (label, 5);
ShadowEffect.SetColor (label, color));
```

设置`ShadowEffect.HasShadow`附加属性设置为`true`执行`ShadowEffect.OnHasShadowChanged`方法添加或删除`LabelShadowEffect`到[ `Label` ](xref:Xamarin.Forms.Label)控件。 在这两个代码示例中，`ShadowEffect.Color`附加的属性提供特定于平台的颜色值。 有关详细信息，请参阅[设备类](~/xamarin-forms/platform/device.md)。

此外， [ `Button` ](xref:Xamarin.Forms.Button)允许要在运行时更改的阴影颜色。 当`Button`单击时，以下代码更改通过设置颜色阴影`ShadowEffect.Color`附加属性：

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>使用样式的效果

一种样式也可以使用可供添加到控件的附加的属性的效果。 以下 XAML 代码示例所示*显式*阴影效果，可以应用于样式[ `Label` ](xref:Xamarin.Forms.Label)控件：

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="True" />
    <Setter Property="local:ShadowEffect.Radius" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceX" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceY" Value="5" />
  </Style.Setters>
</Style>
```

[ `Style` ](xref:Xamarin.Forms.Style)可应用于[ `Label` ](xref:Xamarin.Forms.Label)通过设置其[ `Style` ](xref:Xamarin.Forms.VisualElement.Style)属性设置为`Style`实例使用`StaticResource`标记扩展，如下面的代码示例中所示：

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

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
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                Control.Layer.ShadowOpacity = 1.0f;
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateRadius ()
        {
            Control.Layer.CornerRadius = (nfloat)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            Control.Layer.ShadowColor = ShadowEffect.GetColor (Element).ToCGColor ();
        }

        void UpdateOffset ()
        {
            Control.Layer.ShadowOffset = new CGSize (
                (double)ShadowEffect.GetDistanceX (Element),
                (double)ShadowEffect.GetDistanceY (Element));
        }
    }
```

`OnAttached`方法调用的方法，检索附加的属性的值使用`ShadowEffect`getter，并将其设置`Control.Layer`用于创建阴影的属性值的属性。 此功能包装在`try` / `catch`阻止以防效果附加到该控件不具有`Control.Layer`属性。 通过提供任何实现`OnDetached`方法因为任何清理不不需要。

#### <a name="responding-to-property-changes"></a>响应属性更改

如果任一`ShadowEffect`附加在运行时，需要通过显示所做的更改来响应该效果的属性值更改。 重写的版本`OnElementPropertyChanged`方法，在特定于平台的效果类中，是对可绑定的属性更改进行响应的位置，如下面的代码示例中所示：

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

`OnElementPropertyChanged`方法更新 radius、 颜色或阴影的偏移量提供的相应`ShadowEffect`附加的属性值已更改。 始终应进行检查更改的属性，如可以多次调用此重写。

### <a name="android-project"></a>Android 项目

下面的代码示例演示`LabelShadowEffect`实现针对 Android 项目：

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        Android.Widget.TextView control;
        Android.Graphics.Color color;
        float radius, distanceX, distanceY;

        protected override void OnAttached ()
        {
            try {
                control = Control as Android.Widget.TextView;
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                UpdateControl ();
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateControl ()
        {
            if (control != null) {
                control.SetShadowLayer (radius, distanceX, distanceY, color);
            }
        }

        void UpdateRadius ()
        {
            radius = (float)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            color = ShadowEffect.GetColor (Element).ToAndroid ();
        }

        void UpdateOffset ()
        {
            distanceX = (float)ShadowEffect.GetDistanceX (Element);
            distanceY = (float)ShadowEffect.GetDistanceY (Element);
        }
    }
```

`OnAttached`方法调用的方法，检索附加的属性的值使用`ShadowEffect`getter，并调用一个方法，调用[ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/)方法来创建阴影使用属性值。 此功能包装在`try` / `catch`阻止以防效果附加到该控件不具有`Control.Layer`属性。 通过提供任何实现`OnDetached`方法因为任何清理不不需要。

#### <a name="responding-to-property-changes"></a>响应属性更改

如果任一`ShadowEffect`附加在运行时，需要通过显示所做的更改来响应该效果的属性值更改。 重写的版本`OnElementPropertyChanged`方法，在特定于平台的效果类中，是对可绑定的属性更改进行响应的位置，如下面的代码示例中所示：

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
      UpdateControl ();
    }
  }
  ...
}
```

`OnElementPropertyChanged`方法更新 radius、 颜色或阴影的偏移量提供的相应`ShadowEffect`附加的属性值已更改。 始终应进行检查更改的属性，如可以多次调用此重写。

### <a name="universal-windows-platform-project"></a>通用 Windows 平台项目

下面的代码示例演示`LabelShadowEffect`实现通用 Windows 平台 (UWP) 项目：

```csharp
[assembly: ResolutionGroupName ("MyCompany")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        Label shadowLabel;
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;

                    shadowLabel = new Label ();
                    shadowLabel.Text = textBlock.Text;
                    shadowLabel.FontAttributes = FontAttributes.Bold;
                    shadowLabel.HorizontalOptions = LayoutOptions.Center;
                    shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;

                    UpdateColor ();
                    UpdateOffset ();

                    ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                    shadowAdded = true;
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateColor ()
        {
            shadowLabel.TextColor = ShadowEffect.GetColor (Element);
        }

        void UpdateOffset ()
        {
            shadowLabel.TranslationX = ShadowEffect.GetDistanceX (Element);
            shadowLabel.TranslationY = ShadowEffect.GetDistanceY (Element);
        }
    }
}
```

通用 Windows 平台不提供阴影效果，因此`LabelShadowEffect`两个平台上的实现模拟一个通过添加第二个偏移量[ `Label` ](xref:Xamarin.Forms.Label)落后于主要`Label`。 `OnAttached`方法创建的新`Label`，并对设置某些布局属性`Label`。 然后，它调用检索使用的附加的属性值的方法`ShadowEffect`getter，并通过设置创建卷影[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)， [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)，并且[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY)属性来控制的颜色和位置`Label`。 `shadowLabel`然后插入偏移量落后于主要`Label`。 此功能包装在`try` / `catch`阻止以防效果附加到该控件不具有`Control.Layer`属性。 通过提供任何实现`OnDetached`方法因为任何清理不不需要。

#### <a name="responding-to-property-changes"></a>响应属性更改

如果任一`ShadowEffect`附加在运行时，需要通过显示所做的更改来响应该效果的属性值更改。 重写的版本`OnElementPropertyChanged`方法，在特定于平台的效果类中，是对可绑定的属性更改进行响应的位置，如下面的代码示例中所示：

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
                      args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

`OnElementPropertyChanged`方法将更新的颜色或阴影的偏移量提供的相应`ShadowEffect`附加的属性值已更改。 始终应进行检查更改的属性，如可以多次调用此重写。

## <a name="summary"></a>总结

本文演示了使用附加属性，以将参数传递给某个效果，请和更改在运行时参数。 附加的属性可用于定义响应的运行时属性更改的效果参数。


## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [阴影效果 （示例）](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffectruntimechange/)
