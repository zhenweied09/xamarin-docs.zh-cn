---
title: 创建效果
description: 效果简化控件的自定义项。 本文演示如何创建出当控件获得焦点时，更改输入控件的背景色的效果。
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: 773636cf879439477a6f71e44f13ae66b8f10ea8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="creating-an-effect"></a>创建效果

_效果简化控件的自定义项。本文演示如何创建出当控件获得焦点时，更改输入控件的背景色的效果。_

在每个特定于平台的项目中创建效果的过程如下所示：

1. 创建一个子类`PlatformEffect`类。
1. 重写`OnAttached`方法并写自定义控件的逻辑。
1. 重写`OnDetached`方法并写清理控件自定义情况，如果所需的逻辑。
1. 添加[ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/)效果类属性。 此属性设置的作用，阻止发生冲突具有相同名称的其他作用的公司宽命名空间。 请注意，此属性只能应用一次每个项目。
1. 添加[ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/)效果类属性。 此属性用于通过 Xamarin.Forms，这组名称，以及查找然后将其应用到控件的影响的唯一 ID 注册效果。 该属性采用两个参数 – 生效，并且可用于查找然后将其应用到控件的影响的唯一字符串的类型名称。

将其附加到相应的控件，然后可以使用效果。

> [!NOTE]
> 它是可选若要提供每个平台项目中的效果。 尝试在一个未注册时使用效果将返回一个非 null 值，不执行任何操作。

示例应用程序演示`FocusEffect`，当它获得焦点时更改控件的背景色。 下图说明了示例应用程序，以及它们之间的关系中的每个项目的责任：

![](creating-images/focus-effect.png "焦点影响项目职责")

[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制上`HomePage`通过可自定义`FocusEffect`每个特定于平台的项目中的类。 每个`FocusEffect`类派生自`PlatformEffect`为每个平台的类。 这会导致`Entry`控制呈现使用特定于平台的背景色，这会更改时控件获得焦点，如以下屏幕截图中所示：

![](creating-images/screenshots-1.png "重点放在每个平台上的效果")
![](creating-images/screenshots-2.png "集中每个平台上的效果")

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

`OnAttached`方法设置`BackgroundColor`浅色紫色与该控件的属性`UIColor.FromRGB`方法，并将该颜色也存储在字段中。 此功能包装在`try` / `catch`阻止以防效果附加到控件不具有`BackgroundColor`属性。 没有实现由`OnDetached`方法因为不是必需的任何清理。

`OnElementPropertyChanged`替代响应 Xamarin.Forms 控件上的可绑定的属性更改。 当[ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/)属性更改`BackgroundColor`如果控件有焦点的控件的属性更改为空白，否则它将更改为浅色紫色。 此功能包装在`try` / `catch`阻止以防效果附加到控件不具有`BackgroundColor`属性。

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

`OnAttached`方法调用`SetBackgroundColor`方法来设置要浅的控件的背景色绿色，并还在字段中存储该颜色。 此功能包装在`try` / `catch`阻止以防效果附加到控件不具有`SetBackgroundColor`属性。 没有实现由`OnDetached`方法因为不是必需的任何清理。

`OnElementPropertyChanged`替代响应 Xamarin.Forms 控件上的可绑定的属性更改。 当[ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/)属性发生更改，如果控件有焦点，更改控件的背景色为白色，否则它将更改为浅绿色。 此功能包装在`try` / `catch`阻止以防效果附加到控件不具有`BackgroundColor`属性。

## <a name="windows-phone--universal-windows-platform-projects"></a>Windows Phone 和通用 Windows 平台项目

下面的代码示例演示`FocusEffect`实现为 Windows Phone 和通用 Windows 平台 (UWP) 项目：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.WinRT;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.WinPhone81
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

`OnAttached`方法设置`Background`青色，并设置该控件的属性`BackgroundFocusBrush`为白色的属性。 此功能包装在`try` / `catch`阻止以防效果附加到控件缺少这些属性。 没有实现由`OnDetached`方法因为不是必需的任何清理。

## <a name="consuming-the-effect"></a>使用效果

使用从 Xamarin.Forms 可移植类库 (PCL) 或共享库项目的效果的过程如下所示：

1. 声明一个将自定义的效果。
1. 通过将其添加到控件的将影响附加到控件[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。

> [!NOTE]
> 仅可以将影响实例附加到单个控件。 因此，必须解决影响两次以在两个控件中使用它。

## <a name="consuming-the-effect-in-xaml"></a>使用 XAML 中的效果

下面的 XAML 代码示例演示[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)到控件`FocusEffect`附加：

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

`FocusEffect`在 PCL 中的类在 XAML 中，支持效果消耗，下面的代码示例所示：

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ("MyCompany.FocusEffect")
    {
    }
}
```

`FocusEffect`类子类[ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)类，该类表示对包装出是通常特定于平台的内部效果的独立于平台的影响。 `FocusEffect`类调用基类构造函数，传递参数包含解析组名称的串联中 (使用指定[ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/)效果类中的属性)，和的唯一 ID使用指定[ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/)效果类中的属性。 因此，当[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)在运行时的新实例初始化`MyCompany.FocusEffect`添加到控件的[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。

效果可以也会附加到控件通过使用行为，或通过使用附加属性。 有关使用行为附加到控件的效果的详细信息，请参阅[可重用 EffectBehavior](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md)。 有关将影响附加到控件，通过使用附加的属性的详细信息，请参阅[Passing Parameters to 效果](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md)。

## <a name="consuming-the-effect-in-cnum"></a>使用在 C 中的效果&num;

等效于[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) C# 中所示下面的代码示例：

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

`FocusEffect`附加到`Entry`通过将效果添加到控件的实例[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合，如下面的代码示例中所示：

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ("MyCompany.FocusEffect"));
  ...
}
```

[ `Effect.Resolve` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.Resolve/p/System.String/)返回[ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)即指定的名称解析组名称的串联 (使用指定[ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/)效果类中的属性)，并使用指定的唯一 ID [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/)效果类中的属性。 如果平台不提供效果，`Effect.Resolve`方法将返回一个非`null`值。

## <a name="summary"></a>总结

这篇文章演示了如何创建更改的背景色的效果[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件时控件获得焦点。


## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)
- [PlatformEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/)
- [背景颜色效果 （示例）](https://developer.xamarin.com/samples/xamarin-forms/effects/backgroundcoloreffect/)
- [焦点效果 （示例）](https://developer.xamarin.com/samples/xamarin-forms/effects/focuseffect/)
