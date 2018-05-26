---
title: Android 平台的详细信息
description: 平台细节，可以使用提供功能，仅在特定平台上，而无需实现自定义呈现器或效应。 本文演示如何使用 Android 平台的详细信息，它们构建于 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/23/2018
ms.openlocfilehash: 8d7ec3f2f64fdb8be903fd13bd72bcf545265a3d
ms.sourcegitcommit: 4f646dc5c51db975b2936169547d625c78a22b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2018
---
# <a name="android-platform-specifics"></a>Android 平台的详细信息

_平台细节，可以使用提供功能，仅在特定平台上，而无需实现自定义呈现器或效应。本文演示如何使用 Android 平台的详细信息，它们构建于 Xamarin.Forms。_

在 Android 上，Xamarin.Forms 包含以下平台的详细信息：

- 设置软键盘的操作模式。 有关详细信息，请参阅[软键盘输入模式设置](#soft_input_mode)。
- 启用快速滚动[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)详细信息，请参阅[在 ListView 中启用快速滚动](#fastscroll)。
- 启用在页面间轻扫[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)。 有关详细信息，请参阅[启用轻扫之间中的页 TabbedPage](#enable_swipe_paging)。
- 控制 Z 顺序的可视元素以确定绘制顺序。 有关详细信息，请参阅[控制可视元素提升的](#elevation)。
- 禁用[ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)和[ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)页上暂停的生命周期事件和继续分别使用 AppCompat 的应用程序。 有关详细信息，请参阅[禁用消失和显示页面生命周期事件](#disable_lifecycle_events)。
- 控制是否[ `WebView` ](xref:Xamarin.Forms.WebView)可以显示混合的内容。 有关详细信息，请参阅[启用混合内容中的 WebView](#webview-mixed-content)。
- 输入的法编辑器为设置选项的软键盘[ `Entry` ](xref:Xamarin.Forms.Entry)。 有关详细信息，请参阅[设置条目输入法编辑器选项](#entry-imeoptions)。

<a name="soft_input_mode" />

## <a name="setting-the-soft-keyboard-input-mode"></a>软键盘输入的模式设置

此特定于平台的可用于设置软键盘输入区域的运行模式，并设置在 XAML 中由[ `Application.WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty/)附加属性的值写入[ `WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/)枚举：

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

`Application.On<Android>`方法指定此特定于平台的将仅在 Android 上运行。 [ `Application.UseWindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/)方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)命名空间，用于设置软键盘输入的区域的运行模式， [ `WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/)提供两个值的枚举： [ `Pan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan/)和[ `Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/)。 `Pan`值使用[ `AdjustPan` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/)调整选项，当输入的控件具有焦点时都不调整窗口大小。 相反，窗口中的内容被平移以便当前焦点不软键盘被遮盖。 `Resize`值使用[ `AdjustResize` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/)调整选项，当输入的控件具有焦点，以便腾出空间供软键盘时调整窗口的大小。

结果为软键盘输入的输入的控件具有焦点时，可以设置运行模式的区域：

[![](android-images/pan-resize.png "运行模式平台特有的软键盘")](android-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Plaform-Specific")

<a name="fastscroll" />

## <a name="enabling-fast-scrolling-in-a-listview"></a>启用在 ListView 中的快速滚动

此特定于平台的用于启用通过中的数据的快速滚动[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。 设置使用在 XAML 中`ListView.IsFastScrollEnabled`附加到属性`boolean`值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

`ListView.On<Android>`方法指定此特定于平台的将仅在 Android 上运行。 `ListView.SetIsFastScrollEnabled`方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)命名空间，用于启用通过中的数据的快速滚动[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。 此外，`SetIsFastScrollEnabled`方法可以用于切换快速滚动通过调用`IsFastScrollEnabled`方法以返回是否启用快速滚动：

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

结果是通过中的数据该快速滚动[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)可以启用、 其更改滚动块的大小：

[![](android-images/fastscroll.png "ListView FastScroll 特定于平台的")](android-images/fastscroll-large.png#lightbox "ListView FastScroll Plaform-Specific")

<a name="enable_swipe_paging" />

## <a name="enabling-swiping-between-pages-in-a-tabbedpage"></a>启用在 TabbedPage 页面间轻扫

此特定于平台的用于启用与中的页面之间的水平手指笔势轻扫[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)。 设置使用在 XAML 中[ `TabbedPage.IsSwipePagingEnabled` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty/)附加到属性`boolean`值：

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

`TabbedPage.On<Android>`方法指定此特定于平台的将仅在 Android 上运行。 [ `TabbedPage.SetIsSwipePagingEnabled` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled/p/Xamarin.Forms.BindableObject/System.Boolean/)方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)使用命名空间，以便在页面间轻扫[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)。 此外，`TabbedPage`类`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`命名空间还具有[ `EnableSwipePaging` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/)使此平台特定的方法和一个[ `DisableSwipePaging` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/)禁用的方法此特定于平台的。 [ `TabbedPage.OffscreenPageLimit` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty/)附加属性，和[ `SetOffscreenPageLimit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit/p/Xamarin.Forms.BindableObject/System.Int32/)方法，用于设置应保留在当前页的任何一侧空闲状态的页面数。

结果是通过网页显示由该轻扫分页[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)启用：

![](android-images/tabbedpage-swipe.png)

<a name="elevation" />

## <a name="controlling-the-elevation-of-visual-elements"></a>控制可视元素提升

此特定于平台的是用于控制提升或 Z 顺序，对应用程序的可视元素面向 API 21 或更高版本。 可视元素提升确定其绘制顺序，与有较大 Z 值 occluding 带较低的 Z 值的可视元素的可视元素。 设置使用在 XAML 中`VisualElement.Elevation`附加到属性`boolean`值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             Title="Elevation">
    <StackLayout>
        <Grid>
            <Button Text="Button Beneath BoxView" />
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>        
        <Grid Margin="0,20,0,0">
            <Button Text="Button Above BoxView - Click Me" android:VisualElement.Elevation="10"/>
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

public class AndroidElevationPageCS : ContentPage
{
    public AndroidElevationPageCS()
    {
        ...
        var aboveButton = new Button { Text = "Button Above BoxView - Click Me" };
        aboveButton.On<Android>().SetElevation(10);

        Content = new StackLayout
        {
            Children =
            {
                new Grid
                {
                    Children =
                    {
                        new Button { Text = "Button Beneath BoxView" },
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                },
                new Grid
                {
                    Margin = new Thickness(0,20,0,0),
                    Children =
                    {
                        aboveButton,
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                }
            }
        };
    }
}
```

`Button.On<Android>`方法指定此特定于平台的将仅在 Android 上运行。 `VisualElement.SetElevation`方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)命名空间，用于设置为一个可以为 null 的可视元素提升`float`。 此外，`VisualElement.GetElevation`方法可以用于检索可视元素的提升值。

结果是，以便具有更高版本的 Z 值的可视化元素遮蔽带较低的 Z 值的可视元素，可以控制的可视元素提升。 因此，在此示例中第二个[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)上面呈现[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)因为它具有更高版本的提升值：

![](android-images/elevation.png)

<a name="disable_lifecycle_events" />

## <a name="disabling-the-disappearing-and-appearing-page-lifecycle-events"></a>禁用消失和显示页面生命周期事件

此特定于平台的用来禁用[ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)和[ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)上应用程序的页面事件暂停和继续分别使用 AppCompat 的应用程序。 此外，它包含控件的功能是否软键盘显示恢复时，如果它显示了在暂停，前提是软键盘的运行模式设置为[ `WindowSoftInputModeAdjust.Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/)。

> [!NOTE]
> 请注意默认情况下启用这些事件是要保留现有的应用程序依赖于事件的行为。 禁用这些事件会使匹配前 AppCompat 事件周期 AppCompat 事件周期。

此特定于平台的可供在 XAML 中设置[ `Application.SendDisappearingEventOnPause` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty/)， [ `Application.SendAppearingEventOnResume` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty/)，和[ `Application.ShouldPreserveKeyboardOnResume` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty/)附加到的属性`boolean`值：

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

`Application.Current.On<Android>`方法指定此特定于平台的将仅在 Android 上运行。 [ `Application.SendDisappearingEventOnPause` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/)方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/)命名空间，用于启用或禁用激发[ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)页事件，当应用程序进入背景。 [ `Application.SendAppearingEventOnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/)方法用于启用或禁用激发[ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)页事件，当应用程序将继续从后台时。 [ `Application.ShouldPreserveKeyboardOnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/)使用方法控制是否软键盘显示恢复时，它显示在暂停，如果提供的软键盘的运行模式设置为[ `WindowSoftInputModeAdjust.Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/).

结果是， [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)和[ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/)和页面事件不会在应用程序暂停激发事件并分别恢复，如果软键盘已显示时应用程序已暂停，它也会显示应用程序恢复时：

[![](android-images/keyboard-on-resume.png "生命周期事件特定于平台的")](android-images/keyboard-on-resume-large.png#lightbox "生命周期事件特定于平台的")

<a name="webview-mixed-content" />

## <a name="enabling-mixed-content-in-a-webview"></a>启用 WebView 中的混合的内容

此特定于平台的控件是否[ `WebView` ](xref:Xamarin.Forms.WebView)可以显示混合内容中应用程序面向 API 21 或更高版本。 混合的内容是的内容的最初已加载，通过 HTTPS 连接，但这会将资源 （如图像、 音频、 视频、 样式表、 脚本） 加载通过 HTTP 连接。 设置使用在 XAML 中[ `WebView.MixedContentMode` ](x:ref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty)附加属性的值写入[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)枚举：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

`WebView.On<Android>`方法指定此特定于平台的将仅在 Android 上运行。 [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling))方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于控制是否可以显示混合的内容，与[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)提供三个可能值的枚举：

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) – 指示[ `WebView` ](xref:Xamarin.Forms.WebView)将允许 HTTPS origin，以便从 HTTP 源加载内容。
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) – 指示[ `WebView` ](xref:Xamarin.Forms.WebView)将不允许 HTTPS origin，以便从 HTTP 源加载内容。
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) – 指示[ `WebView` ](xref:Xamarin.Forms.WebView)将尝试使用的最新的设备 web 浏览器的方法兼容。 可能允许某些 HTTP 内容加载的 HTTPS origin 和其他类型的内容将被阻止。 类型的内容被阻止或允许可能随每个操作系统版本发生变化。

结果是，指定[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)值应用于[ `WebView` ](xref:Xamarin.Forms.WebView)，它可以控制是否可以显示混合的内容：

[![WebView 混合内容处理特定于平台](android-images/webview-mixedcontent.png "WebView 混合内容处理特定于平台")](android-images/webview-mixedcontent-large.png#lightbox "WebView 混合内容处理特定于平台")

<a name="entry-imeoptions" />

## <a name="setting-entry-input-method-editor-options"></a>设置项输入法编辑器选项

此特定于平台的设置输入的法编辑器 (IME) 的选项的软键盘[ `Entry` ](xref:Xamarin.Forms.Entry)。 这包括在右下角的软键盘，以及与的交互中设置用户操作按钮`Entry`。 设置使用在 XAML 中[ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty)附加属性的值写入[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)枚举：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

`Entry.On<Android>`方法指定此特定于平台的将仅在 Android 上运行。 [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags))方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于设置的软键盘输入的法操作选项[ `Entry` ](xref:Xamarin.Forms.Entry)，与[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)枚举提供以下值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) – 指示没有特定的操作的密钥是必需的并且基础控件将生成其自己如果它可以。
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) – 指示注意将进行任何操作键可用。
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) – 指示操作键将执行"转到"操作，它们采用到的目标文本的用户类型。
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) – 指示操作键执行"搜索"运算，它们使用户能够搜索文本的结果具有类型化。
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) – 指示操作键将执行"发送"操作，将文本传送到其目标。
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) – 指示操作键将执行一个"下一步"的操作，将用户发送到将接受文本的下一个字段。
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) – 指示操作键将执行一个"完成"的操作，关闭软键盘。
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) – 指示操作键将执行一个"早期/之前"的操作，将用户发送到上一个将接受文本的字段。
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – 掩码选择操作选项。
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) – 指示，拼写检查器将既不了解从用户，也不建议基于用户以前已如何键入要更正的内容。
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) – 指示 UI 应不会全屏。
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) – 指示提取的文本将不显示任何 UI。
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) – 指示没有用户界面将显示的自定义操作。

结果是，指定[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)值应用于软键盘[ `Entry` ](xref:Xamarin.Forms.Entry)，这会设置输入的法编辑器选项：

[![条目输入方法编辑器特定于平台的](android-images/entry-imeoptions.png "条目输入方法编辑器特定于平台的")](android-images/entry-imeoptions-large.png#lightbox "条目输入方法编辑器特定于平台的")

## <a name="summary"></a>总结

这篇文章演示了如何使用 Android 平台的详细信息，它们构建于 Xamarin.Forms。 平台细节，可以使用提供功能，仅在特定平台上，而无需实现自定义呈现器或效应。

## <a name="related-links"></a>相关链接

- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)
- [AndroidSpecific.AppCompat](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/)
