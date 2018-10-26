---
title: iOS 平台特定信息
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文演示如何使用 iOS 平台特定信息的内置于 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: 69f754db0fd9661fb317f43c7cda546b0b510265
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119396"
---
# <a name="ios-platform-specifics"></a>iOS 平台特定信息

_平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。本文演示如何使用 iOS 平台特定信息的内置于 Xamarin.Forms。_

## <a name="visualelements"></a>VisualElements

在 iOS 上，Xamarin.Forms 视图、 页面和布局提供了以下特定于平台的功能：

- 模糊处理的任何支持[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[应用模糊](#blur)。
- 禁用上受支持的旧颜色模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[禁用旧式颜色模式](#legacy-color-mode)。
- 在启用投影[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[启用投影](#drop-shadow)。

<a name="blur" />

### <a name="applying-blur"></a>应用模糊

此特定于平台的使用进行模糊处理框架，下面的内容和设置在 XAML 中由[ `VisualElement.BlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty)附加属性的值为[ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)枚举：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
  ...
  <AbsoluteLayout HorizontalOptions="Center">
      <Image Source="monkeyface.png" />
      <BoxView x:Name="boxView" ios:VisualElement.BlurEffect="ExtraLight" HeightRequest="300" WidthRequest="300" />
  </AbsoluteLayout>
  ...
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

`BoxView.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `VisualElement.UseBlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于将模糊效果应用与[ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)提供四个枚举值： [ `None` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None)， [ `ExtraLight` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight)， [ `Light` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light)，并[ `Dark` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark)。

结果是，指定[ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)应用于[ `BoxView` ](xref:Xamarin.Forms.BoxView)实例的模糊化[ `Image` ](xref:Xamarin.Forms.Image)框架下面：

![](ios-images/blur-effect.png "模糊效果特定于平台")

> [!NOTE]
> 添加到的模糊效果时[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)，仍将由接收触摸事件`VisualElement`。

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>禁用旧式颜色模式

某些 Xamarin.Forms 视图功能旧颜色模式。 在此模式下，当[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)视图的属性设置为`false`，视图将重写由具有已禁用状态的默认本机颜色用户设置的颜色。 有关向后兼容性，这种旧颜色模式保持不受支持视图的默认行为。

此特定于平台的禁用此旧颜色模式，以便对视图由用户设置的颜色保持，即使禁用的视图。 设置使用在 XAML [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty)附加到属性`false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制是否将禁用旧颜色模式。 此外， [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))方法可以用于返回是否禁用旧颜色模式。

结果是，可以禁用旧版颜色模式，以便对视图由用户设置的颜色甚至保持禁用视图时：

![](ios-images/legacy-color-mode-disabled.png "旧颜色模式已禁用")

> [!NOTE]
> 设置时[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)旧颜色模式被完全忽略上一个视图。 可视状态的详细信息，请参阅[Xamarin.Forms 视觉状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

<a name="drop-shadow" />

### <a name="enabling-a-drop-shadow"></a>启用投影

此特定于平台的用于上启用投影[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 设置使用在 XAML [ `VisualElement.IsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty)附加到属性`true`，以及许多其他可选的附加控制投影的属性：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <BoxView ...
                 ios:VisualElement.IsShadowEnabled="true"
                 ios:VisualElement.ShadowColor="Purple"
                 ios:VisualElement.ShadowOpacity="0.7"
                 ios:VisualElement.ShadowRadius="12">
            <ios:VisualElement.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </ios:VisualElement.ShadowOffset>
         </BoxView>
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var boxView = new BoxView { Color = Color.Aqua, WidthRequest = 100, HeightRequest = 100 };
boxView.On<iOS>()
       .SetIsShadowEnabled(true)
       .SetShadowColor(Color.Purple)
       .SetShadowOffset(new Size(10,10))
       .SetShadowOpacity(0.7)
       .SetShadowRadius(12);
```

`VisualElement.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `VisualElement.SetIsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsShadowEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制是否在启用投影`VisualElement`。 此外，可以调用以下方法来控制投影：

- [`SetShadowColor`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.Color)) -设置投影的颜色。 默认颜色[ `Color.Default` ](xref:Xamarin.Forms.Color.Default*)。
- [`SetShadowOffset`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.Size)) -设置投影的偏移量。 偏移量更改阴影被强制转换，并指定为方向[ `Size` ](xref:Xamarin.Forms.Size)值。 `Size`正在向左 （负值） 或向右 （正值） 的距离的第一个值和第二个值被更高版本的距离 （负值） 或下方 （正值） 结构的值以与设备无关单位表示. 此属性的默认值为 （0.0，0.0），这会导致卷影被强制转换涉及的每个方面`VisualElement`。
- [`SetShadowOpacity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowOpacity(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Double)) – 要在范围 0.0 （透明） 到 1.0 （不透明） 的值设置投影的不透明度。 默认不透明度值为 0.5。
- [`SetShadowRadius`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowRadius(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Double)) – 设置用于呈现阴影的模糊半径。 默认半径值为 10.0。

> [!NOTE]
> 可以通过调用查询投影的状态[ `GetIsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsShadowEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))， [ `GetShadowColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))， [ `GetShadowOffset` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))， [ `GetShadowOpacity` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowOpacity(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))，并[ `GetShadowRadius` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowRadius(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))方法。

结果是，可以在启用投影[ `VisualElement` ](xref:Xamarin.Forms.VisualElement):

![](ios-images/drop-shadow.png "已启用投影")

## <a name="views"></a>视图

在 iOS 上，为 Xamarin.Forms 视图提供以下特定于平台的功能：

- 确保输入的文本适合[ `Entry` ](xref:Xamarin.Forms.Entry)通过调整字体大小。 有关详细信息，请参阅[调整项的字体大小](#adjust_font_size)。
- 在中设置的游标颜色[ `Entry` ](xref:Xamarin.Forms.Entry)。 有关详细信息，请参阅[设置条目游标颜色](#entry-cursorcolor)。
- 设置分隔符样式[ `ListView` ](xref:Xamarin.Forms.ListView)。 有关详细信息，请参阅[ListView 设置分隔符样式](#listview-separatorstyle)。
- 控制当项选择发生在[ `Picker` ](xref:Xamarin.Forms.Picker)。 有关详细信息，请参阅[控制选取器项选择](#picker_update_mode)。
- 启用[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)属性可以通过点击对一个位置上设置[ `Slider` ](xref:Xamarin.Forms.Slider)栏中，而不是按无需将`Slider`thumb。 有关详细信息，请参阅[启用上点击移动到滑块](#slider-updateontap)。

<a name="adjust_font_size" />

### <a name="adjusting-the-font-size-of-an-entry"></a>调整项的字体大小

此特定于平台的用于缩放的字体大小[ `Entry` ](xref:Xamarin.Forms.Entry)以确保适合所的文本的控件。 设置使用在 XAML [ `Entry.AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty)附加到属性`boolean`值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

`Entry.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `Entry.EnableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于调整所要确保它符合的文本的字号[ `Entry` ](xref:Xamarin.Forms.Entry). 此外， [ `Entry` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry)类`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空间还具有[ `DisableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}))禁用此平台特定的方法和一个[ `SetAdjustsFontSizeToFitWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},System.Boolean))方法可用于切换字体大小缩放通过调用该方法[ `AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}))方法：

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

结果是的字号[ `Entry` ](xref:Xamarin.Forms.Entry)进行缩放，以确保所的文本放在控件：

![](ios-images/entry-font-size.png "调整项的字体大小特定于平台的")

<a name="entry-cursorcolor" />

### <a name="setting-the-entry-cursor-color"></a>设置条目光标颜色

此特定于平台的设置中的光标颜色[ `Entry` ](xref:Xamarin.Forms.Entry)为指定的颜色。 设置使用在 XAML [ `Entry.CursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.CursorColorProperty)可绑定属性设置为[ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry ... ios:Entry.CursorColor="LimeGreen" />
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var entry = new Xamarin.Forms.Entry();
entry.On<iOS>().SetCursorColor(Color.LimeGreen);
```

`Entry.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `Entry.SetCursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetCursorColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},Xamarin.Forms.Color))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间中，将光标颜色设置为指定[ `Color` ](xref:Xamarin.Forms.Color)。 此外， [ `Entry.GetCursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.GetCursorColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}))方法可以用于检索当前游标颜色。

结果是，中的光标颜色[ `Entry` ](xref:Xamarin.Forms.Entry)可以设置为特定[ `Color` ](xref:Xamarin.Forms.Color):

![](ios-images/entry-cursorcolor.png "条目光标颜色")

<a name="listview-separatorstyle" />

### <a name="setting-the-separator-style-on-a-listview"></a>设置 ListView 的分隔符样式

此特定于平台的控制是否中单元格之间的分隔符[ `ListView` ](xref:Xamarin.Forms.ListView)使用的完整宽度`ListView`。 设置使用在 XAML [ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty)附加属性的值为[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)枚举：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

`ListView.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制是否之间的分隔符中的单元格[ `ListView` ](xref:Xamarin.Forms.ListView)使用完整宽度`ListView`，使用[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)枚举提供两个可能值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) – 指示默认 iOS 分隔符行为。 这是在 Xamarin.Forms 中的默认行为。
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) – 指示分隔符将来自某条边的`ListView`到其他。

结果是，指定[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)值应用于[ `ListView` ](xref:Xamarin.Forms.ListView)，它可以控制单元格之间的分隔符的宽度：

![](ios-images/listview-separatorstyle.png "ListView SeparatorStyle 特定于平台的")

> [!NOTE]
> 一旦分隔符样式设置为`FullWidth`，则不能更改回`Default`在运行时。

<a name="picker_update_mode" />

### <a name="controlling-picker-item-selection"></a>控制选取器项选择

此特定于平台的控件项选择中的发生时[ `Picker` ](xref:Xamarin.Forms.Picker)，这样就允许用户指定的项选择发生时浏览项在控件中，还是仅后**完成**按下按钮。 设置使用在 XAML`Picker.UpdateMode`附加属性的值为`UpdateMode`枚举：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`Picker.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `Picker.SetUpdateMode`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制项选择的发生时间、 与`UpdateMode`枚举提供两个可能值：

- `Immediately` – 项选择会出现在用户浏览中的项[ `Picker` ](xref:Xamarin.Forms.Picker)。 这是在 Xamarin.Forms 中的默认行为。
- `WhenFinished` – 项选择仅发生后用户已按**完成**按钮[ `Picker` ](xref:Xamarin.Forms.Picker)。

此外，`SetUpdateMode`方法可用于切换的枚举值通过调用`UpdateMode`方法，返回当前`UpdateMode`:

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

结果是，指定`UpdateMode`应用于[ `Picker` ](xref:Xamarin.Forms.Picker)，它可以控制项选择发生时：

[![](ios-images/picker-updatemode.png "选取器 UpdateMode 特定于平台的")](ios-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Plaform-Specific")

<a name="slider-updateontap" />

### <a name="enabling-a-slider-thumb-to-move-on-tap"></a>启用移动上点击的滑块

此特定于平台的支持[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)属性可以通过点击对一个位置上设置[ `Slider` ](xref:Xamarin.Forms.Slider)栏中，而不是按无需将`Slider`thumb。 设置使用在 XAML [ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty)可绑定属性设置为`true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

`Slider.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `Slider.SetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.SetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制是否在点击`Slider`条将设置[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)属性。 此外， [ `Slider.GetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.GetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider}))方法可用于返回是否上点击`Slider`条将设置`Slider.Value`属性。

结果是，在点击[ `Slider` ](xref:Xamarin.Forms.Slider)栏可以移动`Slider`thumb 和设置[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)属性：

![](ios-images/slider-updateontap.png "在启用了点击滑块更新")

## <a name="pages"></a>Pages

在 iOS 上，为 Xamarin.Forms 页面提供以下特定于平台的功能：

- 在隐藏导航栏分隔符[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 有关详细信息，请参阅[隐藏导航栏分隔符在 NavigationPage](#navigationpage-hideseparatorbar)。
- 控制是否半透明的导航栏。 有关详细信息，请参阅[进行导航栏半透明](#translucent_navigation_bar)。
- 控制是否状态栏文本的颜色上[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)调整以匹配导航栏的亮度。 有关详细信息，请参阅[调整状态栏文本颜色模式](#status_bar_color_mode)。
- 控制是否将页标题显示为页导航栏中的大型标题。 有关详细信息，请参阅[显示大标题](#large_title)。
- 设置状态条可见性[ `Page` ](xref:Xamarin.Forms.Page)。 有关详细信息，请参阅[页上设置状态条可见性](#set_status_bar_visibility)。
- 确保该页面内容位于上是安全的所有 iOS 设备的屏幕区域。 有关详细信息，请参阅[启用安全区域布局指南](#safe_area_layout)。
- 在 iPad 上设置模式页面演示文稿的样式。 有关详细信息，请参阅[在 iPad 上设置模式页面演示文稿样式](#modal-page-presentation-style)。

<a name="navigationpage-hideseparatorbar" />

### <a name="hiding-the-navigation-bar-separator-on-a-navigationpage"></a>隐藏导航栏上 NavigationPage 的分隔条

此特定于平台的隐藏的分隔线和位于底部的导航栏的卷影[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 设置使用在 XAML [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty)可绑定属性设置为`false`:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;

public class iOSTitleViewNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public iOSTitleViewNavigationPageCS()
    {
        On<iOS>().SetHideNavigationBarSeparator(true);
    }
}
```

`NavigationPage.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `NavigationPage.SetHideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetHideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制是否隐藏导航栏分隔符。 此外， [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}))方法可以用于返回是否隐藏导航栏分隔符。

结果是，在导航栏分隔符[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)可以隐藏：

![](ios-images/navigationpage-hideseparatorbar.png "NavigationPage 导航栏中隐藏")

<a name="translucent_navigation_bar" />

### <a name="making-the-navigation-bar-translucent"></a>使导航栏半透明

此特定于平台的用于更改导航栏中，透明度和消耗在 XAML 中的设置[ `NavigationPage.IsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty)附加到属性`boolean`值：

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

`NavigationPage.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `NavigationPage.EnableTranslucentNavigationBar` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于使导航栏半透明。 此外， [ `NavigationPage` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage)类`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空间还具有[ `DisableTranslucentNavigationBar` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}))还原为其默认状态，在导航栏的方法和一个[ `SetIsNavigationBarTranslucent`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean))方法用于通过调用切换导航栏透明度[ `IsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}))方法：

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

结果是，可以更改导航栏的透明度：

![](ios-images/translucent-navigation-bar.png "半透明导航栏中特定于平台")

<a name="status_bar_color_mode" />

### <a name="adjusting-the-status-bar-text-color-mode"></a>调整状态栏文本颜色模式

此特定于平台的控件是否状态栏文本的颜色上[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)调整以匹配导航栏的亮度。 设置使用在 XAML [ `NavigationPage.StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty)附加属性的值为[ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)枚举：

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

`NavigationPage.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `NavigationPage.SetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间中，控件是否状态栏文本的颜色上[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)调整以匹配亮度的导航栏中，使用[ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)枚举提供两个可能值：

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) – 指示不应调整状态栏文本颜色。
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) – 表示文本颜色状态栏应匹配导航栏的亮度。

此外， [ `GetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}))方法可用于检索的当前值[ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)枚举应用于[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage).

结果是，在状态栏上的文本颜色[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)可以进行调整以匹配导航栏的亮度。 在此示例中，状态栏文本颜色更改为用户切换[ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)并[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)页[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

![](ios-images/status-bar-text-color-mode.png "状态栏文本颜色模式特定于平台的")

<a name="large_title" />

### <a name="displaying-large-titles"></a>显示大标题

此特定于平台的用于导航栏中，对于使用 iOS 11 或更高版本的设备上的大型标题显示的页面标题。 大型标题左对齐和使用的较大的图标，并将转换为标准标题当用户开始滚动的内容，以便有效地使用屏幕空间。 但是，在横向方向，标题将返回到导航栏来优化内容布局的中心。 设置使用在 XAML`NavigationPage.PrefersLargeTitles`附加属性设置为`boolean`值：

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

或者可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

`NavigationPage.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `NavigationPage.SetPrefersLargeTitle`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间中，控制是否启用大标题。

前提是大标题上启用[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)，在导航堆栈中的所有页面将都显示大标题。 可以通过设置页上写此行为`Page.LargeTitleDisplay`附加属性的值为`LargeTitleDisplayMode`枚举：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

或者，可以从使用 fluent API 通过 C# 重写页面行为：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

`Page.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `Page.SetLargeTitleDisplay`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，在控制大标题行为[ `Page` ](xref:Xamarin.Forms.Page)，与`LargeTitleDisplayMode`提供三个可能的枚举值：

- `Always` – 强制的导航栏和字体大小，以使用较大的格式。
- `Automatic` – 相同的样式 （大或小） 用作导航堆栈中的上一项。
- `Never` – 强制使用正则、 小型格式导航栏。

此外，`SetLargeTitleDisplay`方法可用于切换的枚举值通过调用`LargeTitleDisplay`方法，返回当前`LargeTitleDisplayMode`:

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

结果是，指定`LargeTitleDisplayMode`应用于[ `Page` ](xref:Xamarin.Forms.Page)，它可以控制大标题行为：

![](ios-images/large-title.png "模糊效果特定于平台")

<a name="set_status_bar_visibility" />

### <a name="setting-the-status-bar-visibility-on-a-page"></a>设置状态栏页上的可见性

此特定于平台的用于上设置的可见性状态栏[ `Page` ](xref:Xamarin.Forms.Page)，并包括能够控制状态栏如何进入或离开`Page`。 设置使用在 XAML 中`Page.PrefersStatusBarHidden`附加属性设置为值`StatusBarHiddenMode`枚举，并选择性地`Page.PreferredStatusBarUpdateAnimation`附加属性的值为`UIStatusBarAnimation`枚举：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

`Page.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `Page.SetPrefersStatusBarHidden`方法，请在`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空间，用于上设置的可见性状态栏[ `Page` ](xref:Xamarin.Forms.Page)通过指定之一`StatusBarHiddenMode`枚举值： `Default`， `True`或`False`。 `StatusBarHiddenMode.True`并`StatusBarHiddenMode.False`值设置而不考虑设备方向状态栏可见性和`StatusBarHiddenMode.Default`值将隐藏垂直 compact 环境中的状态栏。

结果是，在状态栏的可见性[ `Page` ](xref:Xamarin.Forms.Page)可以设置：

![](ios-images/hide-status-bar.png "平台特有的状态栏的可见性")

> [!NOTE]
> 上[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，指定`StatusBarHiddenMode`枚举值还将更新所有的子页面上的状态栏。 所有其他[ `Page`](xref:Xamarin.Forms.Page)的派生类型，指定的`StatusBarHiddenMode`枚举值只会更新当前页面上的状态栏。

`Page.SetPreferredStatusBarUpdateAnimation`方法用于设置状态栏如何进入或离开[ `Page` ](xref:Xamarin.Forms.Page)通过指定之一`UIStatusBarAnimation`枚举值： `None`， `Fade`，或`Slide`。 如果`Fade`或`Slide`指定枚举值，0.25 第二个动画执行如状态栏进入或离开`Page`。

<a name="safe_area_layout" />

### <a name="enabling-the-safe-area-layout-guide"></a>启用安全区域布局指南

此特定于平台的用于确保页面内容定位在屏幕上，则可以使用 iOS 11 和更高版本的所有设备的安全区域上。 具体而言，这将有助于确保该内容未剪辑的舍入的设备角部、 家庭的指示符或在 iPhone X 上的传感器底座。设置使用在 XAML`Page.UseSafeArea`附加属性设置为`boolean`值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

`Page.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `Page.SetUseSafeArea`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间中，控制是否启用安全区域布局指南。

结果是屏幕的页面内容可置于是屏幕的安全的所有 Iphone 区域：

[![](ios-images/safe-area-layout.png "安全区域布局指南")](ios-images/safe-area-layout-large.png#lightbox "安全区域布局指南")

> [!NOTE]
> 定义由 Apple 的安全区域在 Xamarin.Forms 中用来设置[ `Page.Padding` ](xref:Xamarin.Forms.Page.Padding)属性，并且将重写此属性的任何以前已设置的值。

可以通过检索自定义安全区域及其[ `Thickness` ](xref:Xamarin.Forms.Thickness)值替换`Page.SafeAreaInsets`方法从[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间。 它然后可修改为所需和重新分配给`Padding`该页的构造函数中的属性或[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)重写：

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

<a name="modal-page-presentation-style" />

### <a name="setting-the-modal-page-presentation-style-on-an-ipad"></a>在 iPad 上设置模式页面演示文稿样式

此特定于平台的用于在 iPad 上设置的模式页面演示文稿样式。 设置使用在 XAML`Page.ModalPresentationStyle`可绑定属性设置为`UIModalPresentationStyle`枚举值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

`Page.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `Page.SetModalPresentationStyle`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于上设置模式的演示文稿样式[ `Page` ](xref:Xamarin.Forms.Page)通过指定以下项之一`UIModalPresentationStyle`枚举值：

- `FullScreen`用于设置模式的演示文稿样式以覆盖整个屏幕。 默认情况下，使用此演示文稿样式显示模式页面。
- `FormSheet`用于设置模式的演示文稿样式上居中且小于屏幕。

此外，`GetModalPresentationStyle`方法可以用于检索的当前值`UIModalPresentationStyle`应用于枚举[ `Page` ](xref:Xamarin.Forms.Page)。

结果是，在模式的演示文稿样式[ `Page` ](xref:Xamarin.Forms.Page)可以设置：

[![](ios-images/modal-presentation-style-small.png "在 iPad 上的模式演示风格")](ios-images/modal-presentation-style-large.png#lightbox "iPad 上的模式的演示文稿样式")

> [!NOTE]
> 使用此特定于平台的设置模式的演示文稿样式的页面必须使用模式导航。 有关详细信息，请参阅[Xamarin.Forms 模式页面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

## <a name="layouts"></a>布局

在 iOS 上，以下特定于平台的功能用于 Xamarin.Forms 布局：

- 控制是否[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)处理触摸手势或将其传递给其内容。 有关详细信息，请参阅[延迟内容收尾工作了在 ScrollView](#delay_content_touches)。

<a name="delay_content_touches" />

### <a name="delaying-content-touches-in-a-scrollview"></a>ScrollView 中的延迟内容收尾工作

触摸手势中开始时触发的隐式计时器[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)在 iOS 上和`ScrollView`决定是否应处理手势，也可以将其传递给其内容基于计时器的范围内中的用户操作。 默认情况下，iOS`ScrollView`延迟内容的收尾工作了，但这可能会问题导致在某些情况下使用`ScrollView`不应获胜手势的内容。 因此，此特定于平台的控件是否`ScrollView`处理触摸手势或将其传递给其内容。 设置使用在 XAML`ScrollView.ShouldDelayContentTouches`附加属性设置为`boolean`值：

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

`ScrollView.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `ScrollView.SetShouldDelayContentTouches`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，是否用于控制[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)处理触摸手势或将其传递给其内容。 此外，`SetShouldDelayContentTouches`方法可用于切换通过调用延迟内容收尾工作了`ShouldDelayContentTouches`方法以返回是否延迟内容收尾工作了：

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

结果是， [ `ScrollView` ](xref:Xamarin.Forms.ScrollView)可以禁用延迟接收内容收尾工作了，因此，在这种情况下[ `Slider` ](xref:Xamarin.Forms.Slider)接收手势而不是[ `Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)页的[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

[![](ios-images/scrollview-delay-content-touches.png "ScrollView 延迟内容涉及特定于平台的")](ios-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Plaform-Specific")

## <a name="application"></a>应用程序

在 iOS 上，以下特定于平台的功能提供适用于 Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application)类：

- 启用[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)中滚动视图来捕获和共享平移手势与滚动视图。 有关详细信息，请参阅[启用同时进行的平移手势识别](#simultaneous-pan-gesture)。

<a name="simultaneous-pan-gesture" />

### <a name="enabling-simultaneous-pan-gesture-recognition"></a>启用同时进行的平移手势识别

当[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)附加到内部滚动视图中，所有平移手势捕获的一个视图，`PanGestureRecognizer`并不会传递给滚动视图。 因此，将无法再滚动滚动视图。

此特定于平台的使`PanGestureRecognizer`中滚动视图来捕获和共享平移手势与滚动视图。 设置使用在 XAML [ `Application.PanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty)附加到属性`true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

`Application.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.SetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制是否在滚动视图中的平移手势识别程序将捕获平移手势，或捕获并共享平移手势与滚动视图。 此外， [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.GetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application}))方法可用于返回是否与包含的滚动视图共享平移手势[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)。

因此，对于此特定于平台的启用，当[ `ListView` ](xref:Xamarin.Forms.ListView)包含[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)，这两个`ListView`和`PanGestureRecognizer`将收到平移手势和对其进行处理。 但是，对于此特定于平台的禁用状态，当`ListView`包含`PanGestureRecognizer`，则`PanGestureRecognizer`将捕获平移手势并处理它，并`ListView`不会收到平移手势。

## <a name="summary"></a>总结

本文演示了如何使用 iOS 平台特定信息的内置于 Xamarin.Forms。 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。

## <a name="related-links"></a>相关链接

- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
