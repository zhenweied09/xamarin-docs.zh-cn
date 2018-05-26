---
title: iOS 平台细节
description: 平台细节，可以使用提供功能，仅在特定平台上，而无需实现自定义呈现器或效应。 本文演示如何使用 iOS 平台的细节，它们构建于 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/23/2018
ms.openlocfilehash: cc6cb282565e08f7ce4401e5317fba518a74a8f3
ms.sourcegitcommit: 4f646dc5c51db975b2936169547d625c78a22b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2018
---
# <a name="ios-platform-specifics"></a>iOS 平台细节

_平台细节，可以使用提供功能，仅在特定平台上，而无需实现自定义呈现器或效应。本文演示如何使用 iOS 平台的细节，它们构建于 Xamarin.Forms。_

在 iOS 上 Xamarin.Forms 包含以下平台的详细信息：

- 模糊对任何支持[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。 有关详细信息，请参阅[应用模糊](#blur)。
- 控制是否将页标题显示为页导航栏中的大型标题。 有关详细信息，请参阅[显示大标题](#large_title)。
- 确保该页面内容位于而言是安全的所有 iOS 设备的屏幕区域。 有关详细信息，请参阅[启用安全区域布局指南](#safe_area_layout)。
- 半透明的导航栏。 有关详细信息，请参阅[进行导航栏半透明](#translucent_navigation_bar)。
- 控制是否状态栏文本颜色上[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)调整以匹配导航栏的亮度。 有关详细信息，请参阅[调整状态栏文本颜色模式](#status_bar_color_mode)。
- 确保输入文本适合[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)通过调整字体大小。 有关详细信息，请参阅[调整项的字体大小](#adjust_font_size)。
- 控制中的项选择时[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)。 有关详细信息，请参阅[控制选取器项选择](#picker_update_mode)。
- 在上设置状态条可见性[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)。 有关详细信息，请参阅[页上设置状态条可见性](#set_status_bar_visibility)。
- 控制是否[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)处理触摸手势或将其传递给其内容。 有关详细信息，请参阅[延迟收尾 ScrollView 的内容工作](#delay_content_touches)。
- 在上设置的分隔符样式[ `ListView` ](xref:Xamarin.Forms.ListView)。 有关详细信息，请参阅[设置 ListView 分隔符样式](#listview-separatorstyle)。

<a name="blur" />

## <a name="applying-blur"></a>应用模糊

此特定于平台的使用进行模糊处理其下分层的内容和设置在 XAML 中由[ `VisualElement.BlurEffect` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty/)附加属性的值写入[ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/)枚举：

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

`BoxView.On<iOS>`方法指定此特定于平台的将仅在 iOS 上运行。 [ `VisualElement.UseBlurEffect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/)方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空间，用于将模糊效果应用与[ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/)枚举提供四个值： [ `None` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None/)， [ `ExtraLight` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight/)， [ `Light` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light/)，和[ `Dark` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark/)。

结果是，指定[ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/)应用于[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)实例，哪些模糊[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)分层其下：

![](ios-images/blur-effect.png "模糊影响特定于平台")

<a name="large_title" />

## <a name="displaying-large-titles"></a>显示大标题

此特定于平台的用于显示页标题为导航栏中，对于使用 iOS 11 或更高版本的设备上的大型标题。 大标题左对齐和使用较大的字体，并将转换为标准标题用户开始滚动内容，以便有效地使用屏幕的实际空间。 但是，在横向打印标题将返回到导航栏来优化内容布局的中心。 设置使用在 XAML 中`NavigationPage.PrefersLargeTitles`附加到属性`boolean`值：

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

`NavigationPage.On<iOS>`方法指定此特定于平台的将仅在 iOS 上运行。 `NavigationPage.SetPrefersLargeTitle`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空间，控制是否启用大标题。

假设上启用了大标题[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)，导航堆栈中的所有页面将都显示大标题。 此行为可以重写在页上通过设置`Page.LargeTitleDisplay`附加属性的值写入`LargeTitleDisplayMode`枚举：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

或者，可以从使用 fluent API 通过 C# 重写页的行为：

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

`Page.On<iOS>`方法指定此特定于平台的将仅在 iOS 上运行。 `Page.SetLargeTitleDisplay`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空间上的控件的大标题行为[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，与`LargeTitleDisplayMode`提供三个可能的枚举值：

- `Always` – 强制导航栏和字体大小以使用大型的格式。
- `Automatic` – 相同的样式 （大或小） 用作导航堆栈中的上一项。
- `Never` – 强制使用常规、 小格式导航栏。

此外，`SetLargeTitleDisplay`方法可以用于切换的枚举值，通过调用`LargeTitleDisplay`方法，返回当前`LargeTitleDisplayMode`:

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

结果是，指定`LargeTitleDisplayMode`应用于[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，它可以控制大标题行为：

![](ios-images/large-title.png "模糊影响特定于平台")

<a name="safe_area_layout" />

## <a name="enabling-the-safe-area-layout-guide"></a>启用安全区域布局指南

此特定于平台的用于确保页面内容位于而言是安全的所有使用 iOS 11 和更高版本的设备的屏幕区域。 具体而言，它将有助于确保不通过舍入的设备角、 家庭指示器或传感器机架 X 在 iPhone 上的剪切该内容。设置使用在 XAML 中`Page.UseSafeArea`附加到属性`boolean`值：

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

`Page.On<iOS>`方法指定此特定于平台的将仅在 iOS 上运行。 `Page.SetUseSafeArea`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空间，控制是否启用安全区域布局指南。

结果为页面内容可以位于的区域，则可以对所有 Iphone 安全的屏幕：

[![](ios-images/safe-area-layout.png "安全区域布局指南")](ios-images/safe-area-layout-large.png#lightbox "安全区域布局指南")

> [!NOTE]
> 由 Apple 定义的安全区域使用 Xamarin.Forms 中，来设置[ `Page.Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/)属性，并且将重写此属性的任何以前已设置的值。

可以通过检索自定义安全区域其[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)值与`Page.SafeAreaInsets`方法从[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空间。 然后可以作为修改它所需和重新分配给`Padding`页构造函数中的属性或[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/)重写：

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

<a name="translucent_navigation_bar" />

## <a name="making-the-navigation-bar-translucent"></a>使导航栏半透明

此特定于平台的可用于更改的透明度导航栏中，并设置在 XAML 中由[ `NavigationPage.IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty/)附加到属性`boolean`值：

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

`NavigationPage.On<iOS>`方法指定此特定于平台的将仅在 iOS 上运行。 [ `NavigationPage.EnableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/)方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空间，用于使半透明的导航栏。 此外， [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage/)类`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空间还具有[ `DisableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/)还原到其默认状态，导航栏的方法和一个[ `SetIsNavigationBarTranslucent`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/System.Boolean/)方法用于通过调用切换的导航栏透明度[ `IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/)方法：

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

结果是，可以更改导航栏的透明度：

![](ios-images/translucent-navigation-bar.png "半透明的导航栏特定于平台")

<a name="status_bar_color_mode" />

## <a name="adjusting-the-status-bar-text-color-mode"></a>调整文本颜色模式下状态栏

此特定于平台的控件是否状态栏文本颜色上[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)调整以匹配导航栏的亮度。 设置使用在 XAML 中[ `NavigationPage.StatusBarTextColorMode` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty/)附加属性的值写入[ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/)枚举：

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

`NavigationPage.On<iOS>`方法指定此特定于平台的将仅在 iOS 上运行。 [ `NavigationPage.SetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/)方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空间，控件是否状态栏文本颜色上[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)调整以匹配导航栏中，亮度与[ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/)枚举提供两个可能值：

- [`DoNotAdjust`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust/) – 指示不应调整状态栏文本颜色。
- [`MatchNavigationBarTextLuminosity`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity/) – 表示导航栏的亮度应与状态栏文本颜色匹配。

此外， [ `GetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/)方法可以用于检索的当前值[ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/)枚举应用于[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/).

结果是，在状态栏上的文本颜色[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)可以调整以匹配导航栏的亮度。 在此示例中，状态栏文本颜色更改为用户切换[ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)和[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)页[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

![](ios-images/status-bar-text-color-mode.png "状态栏文本颜色模式的特定于平台")

<a name="adjust_font_size" />

## <a name="adjusting-the-font-size-of-an-entry"></a>调整项的字体大小

此特定于平台的用于缩放的字体大小[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)以确保所的文本放在控件。 设置使用在 XAML 中[ `Entry.AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty/)附加到属性`boolean`值：

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

`Entry.On<iOS>`方法指定此特定于平台的将仅在 iOS 上运行。 [ `Entry.EnableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/)方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空间，用于扩展所的文本，以确保它符合的字体大小[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/). 此外， [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry/)类`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空间还具有[ `DisableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/)禁用此平台特定的方法和一个[ `SetAdjustsFontSizeToFitWidth`](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/System.Boolean/)方法用于切换通过调用缩放的字体大小[ `AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/)方法：

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

结果是的字号[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)进行缩放，以确保所的文本放在控件：

![](ios-images/entry-font-size.png "调整条目字体大小特定于平台的")

<a name="picker_update_mode" />

## <a name="controlling-picker-item-selection"></a>控制选取器项选择

此特定于平台的控件中的项选择时[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)，允许用户指定的项选择内容仅出现时浏览项在控件中，或一次只能**完成**按下按钮。 设置使用在 XAML 中`Picker.UpdateMode`附加属性的值写入`UpdateMode`枚举：

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

`Picker.On<iOS>`方法指定此特定于平台的将仅在 iOS 上运行。 `Picker.SetUpdateMode`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空间，用于控制项选择发生时，与`UpdateMode`枚举提供两个可能值：

- `Immediately` – 根据用户浏览中的项，则会发生项选择[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)。 这是 Xamarin.Forms 中的默认行为。
- `WhenFinished` – 用户已按后，才会出现项选择**完成**按钮[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)。

此外，`SetUpdateMode`方法可以用于切换的枚举值，通过调用`UpdateMode`方法，返回当前`UpdateMode`:

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

结果是，指定`UpdateMode`应用于[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)，它可以控制项选择发生时：

[![](ios-images/picker-updatemode.png "选取器 UpdateMode 特定于平台的")](ios-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Plaform-Specific")

<a name="set_status_bar_visibility" />

## <a name="setting-the-status-bar-visibility-on-a-page"></a>设置状态栏页面上的可见性

此特定于平台的用于上设置的可见性状态栏[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，它包括能够控制如何状态栏进入或离开`Page`。 设置使用在 XAML 中`Page.PrefersStatusBarHidden`附加属性的值写入`StatusBarHiddenMode`枚举，和 （可选）`Page.PreferredStatusBarUpdateAnimation`附加属性的值写入`UIStatusBarAnimation`枚举：

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

`Page.On<iOS>`方法指定此特定于平台的将仅在 iOS 上运行。 `Page.SetPrefersStatusBarHidden`方法，请在`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空间，用于在上设置的可见性状态栏[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)通过指定之一`StatusBarHiddenMode`枚举值： `Default`， `True`或`False`。 `StatusBarHiddenMode.True`和`StatusBarHiddenMode.False`值设置而不考虑设备方向，状态栏可见性和`StatusBarHiddenMode.Default`值将隐藏垂直 compact 环境中的状态栏。

结果是，在上的状态栏的可见性[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)可以设置：

![](ios-images/hide-status-bar.png "平台特有的状态栏可见性")

> [!NOTE]
> 上[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)，指定`StatusBarHiddenMode`枚举值也将更新所有的子页面上的状态栏。 在所有其他[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-派生类型，指定的`StatusBarHiddenMode`枚举值将仅更新在当前页面上的状态栏。

`Page.SetPreferredStatusBarUpdateAnimation`方法用于设置状态栏如何进入或离开[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)通过指定之一`UIStatusBarAnimation`枚举值： `None`， `Fade`，或`Slide`。 如果`Fade`或`Slide`指定的枚举值，0.25 第二个动画将作为状态栏进入或离开执行`Page`。

<a name="delay_content_touches" />

## <a name="delaying-content-touches-in-a-scrollview"></a>在 ScrollView 的延迟内容收尾工作

触摸手势开始中时触发隐式计时器[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)在 iOS 上和`ScrollView`根据在计时器范围内的用户操作，是否应处理笔势或将其传递给其内容的决定。 默认情况下，iOS`ScrollView`延迟内容收尾工作，但这可能会问题导致在某些情况下使用`ScrollView`时它应不入选笔势的内容。 因此，此特定于平台的控件是否`ScrollView`处理触摸手势或将其传递给其内容。 设置使用在 XAML 中`ScrollView.ShouldDelayContentTouches`附加到属性`boolean`值：

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

`ScrollView.On<iOS>`方法指定此特定于平台的将仅在 iOS 上运行。 `ScrollView.SetShouldDelayContentTouches`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空间，是否用于控制[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)处理触摸手势或将其传递给其内容。 此外，`SetShouldDelayContentTouches`方法可以用于切换通过调用延迟内容收尾工作`ShouldDelayContentTouches`方法以返回是否推迟内容收尾工作：

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

结果是， [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)可以禁用延迟接收内容收尾工作，因此，在这种情况下[ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)接收笔势而不是[ `Detail`](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)页[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

[![](ios-images/scrollview-delay-content-touches.png "ScrollView 延迟内容涉及特定于平台的")](ios-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Plaform-Specific")

<a name="listview-separatorstyle" />

## <a name="setting-the-separator-style-on-a-listview"></a>在 ListView 上设置的分隔符样式

此特定于平台的控制是否中单元格之间的分隔符[ `ListView` ](xref:Xamarin.Forms.ListView)使用的整个宽度`ListView`。 设置使用在 XAML 中[ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty)附加属性的值写入[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)枚举：

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

`ListView.On<iOS>`方法指定此特定于平台的将仅在 iOS 上运行。 [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle))方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制是否之间的分隔符中的单元格[ `ListView` ](xref:Xamarin.Forms.ListView)使用完整宽度`ListView`，与[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)枚举提供两个可能值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) – 指示默认 iOS 分隔符行为。 这是 Xamarin.Forms 中的默认行为。
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) – 指示分隔符将取自的一个边`ListView`相互。

结果是，指定[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)值应用于[ `ListView` ](xref:Xamarin.Forms.ListView)，它可以控制的单元格之间的分隔符的宽度：

![](ios-images/listview-separatorstyle.png "ListView SeparatorStyle 特定于平台的")

> [!NOTE]
> 一旦分隔符样式设置为`FullWidth`，不能将其改回`Default`在运行时。

## <a name="summary"></a>总结

这篇文章演示了如何使用 iOS 平台的细节，它们构建于 Xamarin.Forms。 平台细节，可以使用提供功能，仅在特定平台上，而无需实现自定义呈现器或效应。


## <a name="related-links"></a>相关链接

- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)
