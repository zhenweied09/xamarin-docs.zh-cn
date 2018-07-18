---
title: 创建一种效果
description: 效果简化控件的自定义项。 本文演示如何创建更改项控件的背景色，当控件获得焦点时的效果。
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: b29d83999724a35293882f7b9efc0158171c4fd2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998155"
---
# <a name="creating-an-effect"></a>创建一种效果

_效果简化控件的自定义项。本文演示如何创建更改项控件的背景色，当控件获得焦点时的效果。_

每个特定于平台的项目中创建有效的过程如下所示：

1. 创建一个子类`PlatformEffect`类。
1. 重写`OnAttached`方法和写入自定义控件的逻辑。
1. 重写`OnDetached`清理控件自定义，如果所需的方法和写入逻辑。
1. 添加[ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute)影响类的属性。 此属性设置的效果，阻止与其他具有相同名称的效果的碰撞公司宽命名空间。 请注意，此属性只能应用一次每个项目。
1. 添加[ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute)影响类的属性。 此属性用于通过 Xamarin.Forms，组名称，以及查找然后将其应用到控件的影响的唯一 ID 注册效果。 该属性采用两个参数 – 效果，并将用于定位的效果，然后将其应用到控件的唯一字符串的类型名称。

将其附加到相应的控件，然后可以使用效果。

> [!NOTE]
> 它是可选提供每个平台项目中的效果。 尝试使用一种效果，其中一个不注册时将返回一个非 null 值，不执行任何操作。

该示例应用程序演示`FocusEffect`时获得焦点，更改控件的背景色。 下图演示了示例应用程序，以及它们之间的关系的每个项目的职责：

![](creating-images/focus-effect.png "焦点效果项目职责")

[ `Entry` ](xref:Xamarin.Forms.Entry)对的 control 权限`HomePage`由自定义`FocusEffect`每个特定于平台的项目中的类。 每个`FocusEffect`类派生自`PlatformEffect`为每个平台的类。 这会导致`Entry`控制呈现具有特定于平台的背景色，这会更改时控件获得焦点，如以下屏幕截图中所示：

![](creating-images/screenshots-1.png "重点放在每个平台上的效果")
![](creating-images/screenshots-2.png "重点放在每个平台上的效果")

## <a name="creating-the-effect-on-each-platform"></a>创建每个平台上的效果

以下各节讨论的特定于平台的实现`FocusEffect`类。

## <a name="ios-project"></a>iOS 项目

下面的代码示例演示`FocusEffect`实现针对 iOS 项目：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.iOS
{
    public class FocusEffect : PlatformEffect
    {
        UIColor backgroundColor;

        protected override void OnAttached ()
        {
            try {
                Control.BackgroundColor = backgroundColor = UIColor.FromRGB (204, 153, 255);
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);

            try {
                if (args.PropertyName == "IsFocused") {
                    if (Control.BackgroundColor == backgroundColor) {
                        Control.BackgroundColor = UIColor.White;
                    } else {
                        Control.BackgroundColor = backgroundColor;
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`OnAttached`方法设置`BackgroundColor`淡紫色与该控件的属性`UIColor.FromRGB`方法，并将此颜色也存储在字段中。 此功能包装在`try` / `catch`阻止以防效果附加到的控件不具有`BackgroundColor`属性。 通过提供任何实现`OnDetached`方法因为任何清理不不需要。

`OnElementPropertyChanged`替代响应的 Xamarin.Forms 控件上的可绑定的属性更改。 当[ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused)属性更改`BackgroundColor`如果控件有焦点的控件属性更改为白色，否则它将更改为淡紫色。 此功能包装在`try` / `catch`阻止以防效果附加到的控件不具有`BackgroundColor`属性。

## <a name="android-project"></a>Android 项目

下面的代码示例演示`FocusEffect`实现针对 Android 项目：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.Droid
{
    public class FocusEffect : PlatformEffect
    {
        Android.Graphics.Color backgroundColor;

        protected override void OnAttached ()
        {
            try {
                backgroundColor = Android.Graphics.Color.LightGreen;
                Control.SetBackgroundColor (backgroundColor);

            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);
            try {
                if (args.PropertyName == "IsFocused") {
                    if (((Android.Graphics.Drawables.ColorDrawable)Control.Background).Color == backgroundColor) {
                        Control.SetBackgroundColor (Android.Graphics.Color.Black);
                    } else {
                        Control.SetBackgroundColor (backgroundColor);
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`OnAttached`方法调用`SetBackgroundColor`方法以设置要打造出色的控件的背景色绿色，并且还在字段中存储此颜色。 此功能包装在`try` / `catch`阻止以防效果附加到的控件不具有`SetBackgroundColor`属性。 通过提供任何实现`OnDetached`方法因为任何清理不不需要。

`OnElementPropertyChanged`替代响应的 Xamarin.Forms 控件上的可绑定的属性更改。 当[ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused)属性更改控件的背景色更改为白色，如果控件有焦点，否则它将更改为浅绿色。 此功能包装在`try` / `catch`阻止以防效果附加到的控件不具有`BackgroundColor`属性。

## <a name="universal-windows-platform-projects"></a>通用 Windows 平台项目

下面的代码示例演示`FocusEffect`实现通用 Windows 平台 (UWP) 项目：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.UWP
{
    public class FocusEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            try
            {
                (Control as Windows.UI.Xaml.Controls.Control).Background = new SolidColorBrush(Colors.Cyan);
                (Control as FormsTextBox).BackgroundFocusBrush = new SolidColorBrush(Colors.White);
            }
            catch (Exception ex)
            {
                Debug.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }
    }
}
```

`OnAttached`方法设置`Background`蓝绿色，并设置该控件的属性`BackgroundFocusBrush`属性设置为白色。 此功能包装在`try` / `catch`阻止以防效果附加到控件缺少这些属性。 通过提供任何实现`OnDetached`方法因为任何清理不不需要。

## <a name="consuming-the-effect"></a>使用效果

使用从 Xamarin.Forms.NET Standard 库或共享库项目的效果的过程如下所示：

1. 声明将由效果自定义控件。
1. 通过将其添加到控件的附加到该控件的效果[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。

> [!NOTE]
> 效果实例只能附加到单个控件。 因此，必须解决影响两次以在两个控件中使用它。

## <a name="consuming-the-effect-in-xaml"></a>使用 XAML 中的效果

以下 XAML 代码示例所示[ `Entry` ](xref:Xamarin.Forms.Entry)控件`FocusEffect`附加：

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

`FocusEffect` .NET Standard 库中的类支持中 XAML，效果消耗，并在下面的代码示例所示：

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ("MyCompany.FocusEffect")
    {
    }
}
```

`FocusEffect`类子类[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect)类，该类表示包装内部效果通常特定于平台的一个独立于平台的效果。 `FocusEffect`类会调用基类构造函数，传入参数包含的串联的解析组名称 (使用指定[ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute)效果类中的属性)，和的唯一 ID使用指定[ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute)效果类中的属性。 因此，当[ `Entry` ](xref:Xamarin.Forms.Entry)在运行时的新实例初始化`MyCompany.FocusEffect`添加到控件的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。

效果可以还附加到控件通过使用一种行为，或通过使用附加属性。 有关使用行为附加到控件影响的详细信息，请参阅[可重用 EffectBehavior](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md)。 有关使用附加的属性将影响附加到控件的详细信息，请参阅[效果传递参数](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md)。

## <a name="consuming-the-effect-in-cnum"></a>使用 C 中的效果&num;

等效于[ `Entry` ](xref:Xamarin.Forms.Entry) C# 中所示下面的代码示例：

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

`FocusEffect`附加到`Entry`通过将效果添加到控件的实例[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合，如下面的代码示例中所示：

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ("MyCompany.FocusEffect"));
  ...
}
```

[ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String))返回[ `Effect` ](xref:Xamarin.Forms.Effect)对于指定的名称，这是解析组名称的串联 (使用指定[ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute)效果类中的属性)，并使用指定的唯一 ID [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute)效果类中的属性。 如果一个平台不提供效果`Effect.Resolve`方法将返回一个非`null`值。

## <a name="summary"></a>总结

本文演示了如何创建更改的背景色的效果[ `Entry` ](xref:Xamarin.Forms.Entry)控制当控件获得焦点。


## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [背景颜色效果 （示例）](https://developer.xamarin.com/samples/xamarin-forms/effects/backgroundcoloreffect/)
- [焦点效果 （示例）](https://developer.xamarin.com/samples/xamarin-forms/effects/focuseffect/)
