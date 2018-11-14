---
title: Android 平台特定信息
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文演示如何使用 Android 平台特定信息的内置于 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: 3249a9706ba96ec3690a3a3a6b80a5eb261625e4
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527269"
---
# <a name="android-platform-specifics"></a>Android 平台特定信息

_平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。本文演示如何使用 Android 平台特定信息的内置于 Xamarin.Forms。_

## <a name="visualelements"></a>VisualElements

在 Android 上，Xamarin.Forms 视图、 页面和布局提供了以下特定于平台的功能：

- 控制 Z 顺序的可视元素来确定绘制顺序。 有关详细信息，请参阅[控制可视元素提升的](#elevation)。
- 禁用上受支持的旧颜色模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[禁用旧式颜色模式](#legacy-color-mode)。

<a name="elevation" />

### <a name="controlling-the-elevation-of-visual-elements"></a>控制可视元素的提升

此特定于平台的是用于控制提升或 Z 顺序，对应用程序的可视元素的面向 API 21 或更高版本。 一个可视元素提升确定其绘制顺序，使用具有更高版本的 Z 值 occluding 具有较低的 Z 值的可视元素的可视元素。 设置使用在 XAML`VisualElement.Elevation`附加属性设置为`boolean`值：

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

`Button.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 `VisualElement.SetElevation`方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于设置为一个可以为 null 的可视元素的权限提升`float`。 此外，`VisualElement.GetElevation`方法可以用于检索一个可视元素的提升值。

结果是，以便具有更高版本的 Z 值的可视元素遮蔽具有较低的 Z 值的可视元素，可以控制可视元素的提升。 因此，在此示例中第二个[ `Button` ](xref:Xamarin.Forms.Button)上方呈现[ `BoxView` ](xref:Xamarin.Forms.BoxView)因为它具有较大的提升值：

![](android-images/elevation.png)

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>禁用旧式颜色模式

某些 Xamarin.Forms 视图功能旧颜色模式。 在此模式下，当[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)视图的属性设置为`false`，视图将重写由具有已禁用状态的默认本机颜色用户设置的颜色。 有关向后兼容性，这种旧颜色模式保持不受支持视图的默认行为。

此特定于平台的禁用此旧颜色模式，以便对视图由用户设置的颜色保持，即使禁用的视图。 设置使用在 XAML [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty)附加到属性`false`:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于控制是否将禁用旧颜色模式。 此外， [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement}))方法可以用于返回是否禁用旧颜色模式。

结果是，可以禁用旧版颜色模式，以便对视图由用户设置的颜色甚至保持禁用视图时：

![](android-images/legacy-color-mode-disabled.png "旧颜色模式已禁用")

> [!NOTE]
> 设置时[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)旧颜色模式被完全忽略上一个视图。 可视状态的详细信息，请参阅[Xamarin.Forms 视觉状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="views"></a>视图

在 Android 上，为 Xamarin.Forms 视图提供以下特定于平台的功能：

- 使用默认填充边距和阴影的 Android 按钮的值。 有关详细信息，请参阅[使用 Android 按钮](#button-padding-shadow)。
- 输入的法编辑器为设置选项的软键盘[ `Entry` ](xref:Xamarin.Forms.Entry)。 有关详细信息，请参阅[设置条目输入法编辑器选项](#entry-imeoptions)。
- 启用快速滚动[ `ListView` ](xref:Xamarin.Forms.ListView)的详细信息，请参阅[ListView 中启用快速滚动](#fastscroll)。
- 控制是否[ `WebView` ](xref:Xamarin.Forms.WebView)可以显示混合的内容。 有关详细信息，请参阅[中的启用混合内容 WebView](#webview-mixed-content)。

<a name="button-padding-shadow" />

### <a name="using-android-buttons"></a>使用 Android 按钮

此特定于平台的控制 Xamarin.Forms 按钮使用的默认填充边距和阴影的 Android 按钮的值。 设置使用在 XAML [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty)并[ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty)附加属性设置为`boolean`值：

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

`Button.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 [ `Button.SetUseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean))并[`Button.SetUseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean))方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，是用于控制是否 Xamarin.Forms 按钮使用默认值填充和阴影的 Android 按钮的值。 此外， [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button}))并[ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button}))方法可以用于返回按钮是否使用默认值分别填充值和默认卷影值。

结果是 Xamarin.Forms 按钮，可以使用默认填充和 Android 的按钮的卷影值：

![](android-images/button-padding-and-shadow.png "旧颜色模式已禁用")

请注意，在上述每个屏幕截图[ `Button` ](xref:Xamarin.Forms.Button)具有相同的定义，不同之处在于右侧`Button`使用默认填充边距和阴影的 Android 按钮的值。

<a name="entry-imeoptions" />

### <a name="setting-entry-input-method-editor-options"></a>设置条目输入法编辑器选项

此特定于平台的设置输入的法编辑器 (IME) 的软键盘选项[ `Entry` ](xref:Xamarin.Forms.Entry)。 这包括在角的软键盘和与之间的交互来设置用户操作按钮`Entry`。 设置使用在 XAML [ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty)附加属性的值为[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)枚举：

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

`Entry.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags))方法，在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于设置的软键盘输入的法操作选项[ `Entry` ](xref:Xamarin.Forms.Entry)，与[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)枚举提供以下值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) -指示没有特定操作的键是必需的并且基础控件将生成其自己如果它可以。 这将是`Next`或`Done`。
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) -指示没有操作项会使可用。
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) – 指示操作键将执行"转到"操作，它们使到文本的目标用户类型。
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) – 指示操作键执行"搜索"操作，它们使用户的搜索文本结果具有类型化。
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) – 指示操作键将执行一个"发送"操作，将文本传递到其目标。
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) – 指示操作键将执行"下一步"操作，使用户为将接受文本的下一个字段。
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) – 指示操作键将执行一个"done"的操作，关闭软键盘。
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) – 指示操作键将执行"上一个"操作，使用户将接受文本的上一个字段。
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – 若要选择操作选项的掩码。
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) – 指示，拼写检查器将既不了解从用户，也不提供根据用户以前已如何键入更正建议。
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) – 指示在 UI 不应处于全屏。
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) -指示对于提取的文本将不显示 UI。
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) -指示没有 UI，将显示用于自定义操作。

结果是，指定[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)值将应用于软键盘[ `Entry` ](xref:Xamarin.Forms.Entry)，用于设置输入的法编辑器选项：

[![条目输入方法编辑器平台专属](android-images/entry-imeoptions.png "条目输入方法编辑器平台特定")](android-images/entry-imeoptions-large.png#lightbox "条目输入方法编辑器特定于平台的")

<a name="fastscroll" />

### <a name="enabling-fast-scrolling-in-a-listview"></a>启用快速滚动 ListView

此特定于平台的用于启用通过中的数据快速滚动[ `ListView` ](xref:Xamarin.Forms.ListView)。 设置使用在 XAML`ListView.IsFastScrollEnabled`附加属性设置为`boolean`值：

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

`ListView.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 `ListView.SetIsFastScrollEnabled`方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于启用通过中的数据快速滚动[ `ListView` ](xref:Xamarin.Forms.ListView)。 此外，`SetIsFastScrollEnabled`方法可用于切换通过调用快速滚动`IsFastScrollEnabled`方法以返回指示是否启用快速滚动：

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

结果是通过中的数据的快速滚动[ `ListView` ](xref:Xamarin.Forms.ListView)可以启用，这将更改滚动块的大小：

[![](android-images/fastscroll.png "ListView FastScroll 平台专属")](android-images/fastscroll-large.png#lightbox "ListView FastScroll 特定于平台的")

<a name="webview-mixed-content" />

### <a name="enabling-mixed-content-in-a-webview"></a>启用 web 视图中的混合的内容

此特定于平台的控件是否[ `WebView` ](xref:Xamarin.Forms.WebView)可以显示混合内容中的应用程序面向 API 21 或更高版本。 混合的内容是的内容的最初已加载，通过 HTTPS 连接，但这会通过 HTTP 连接加载资源 （如图像、 音频、 视频、 样式表、 脚本）。 设置使用在 XAML [ `WebView.MixedContentMode` ](x:ref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty)附加属性的值为[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)枚举：

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

`WebView.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling))方法，在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于控制是否可以显示混合的内容，与[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)提供三个可能值的枚举：

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) – 指示[ `WebView` ](xref:Xamarin.Forms.WebView)将允许从 HTTP 源加载内容的 HTTPS 原点。
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) – 指示[ `WebView` ](xref:Xamarin.Forms.WebView)将不允许 HTTPS origin 从 HTTP 源加载内容。
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) – 指示[ `WebView` ](xref:Xamarin.Forms.WebView)将尝试与最新的设备 web 浏览器的方法兼容。 可能允许某些 HTTP 内容加载的 HTTPS origin 和其他类型的内容将被阻止。 阻止或允许的内容的类型可能会随每个操作系统版本。

结果是，指定[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)值应用于[ `WebView` ](xref:Xamarin.Forms.WebView)，它可以控制是否可以显示混合的内容：

[![WebView 混合内容处理特定于平台](android-images/webview-mixedcontent.png "WebView 混合内容处理特定于平台")](android-images/webview-mixedcontent-large.png#lightbox "WebView 混合内容处理特定于平台")

## <a name="pages"></a>Pages

在 Android 上，为 Xamarin.Forms 页面提供以下特定于平台的功能：

- 设置导航栏的高度[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 有关详细信息，请参阅[NavigationPage 上设置导航栏高度](#navigationpage-barheight)。
- 中的页面中导航时禁用过渡动画[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 有关详细信息，请参阅[TabbedPage 中禁用页面切换动画](#tabbedpage-transition-animations)。
- 启用在页面间轻扫[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 有关详细信息，请参阅[启用轻扫页面之间中您不要将 TabbedPage](#enable_swipe_paging)。
- 在设置工具栏位置和颜色[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 有关详细信息，请参阅[设置 TabbedPage 工具栏位置和颜色](#tabbedpage-toolbar)。

<a name="navigationpage-barheight" />

### <a name="setting-the-navigation-bar-height-on-a-navigationpage"></a>设置导航栏上 NavigationPage 的高度

此特定于平台的设置在导航栏的高度[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 设置使用在 XAML [ `NavigationPage.BarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty)可绑定属性设置为一个整数值：

```xaml
<NavigationPage ...
                xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
                android:NavigationPage.BarHeight="450">
    ...
</NavigationPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

public class AndroidNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public AndroidNavigationPageCS()
    {
        On<Android>().SetBarHeight(450);
    }
}
```

`NavigationPage.On<Android>`方法指定仅将在应用程序兼容性 Android 上运行此特定于平台的。 [ `NavigationPage.SetBarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.SetBarHeight(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.NavigationPage},System.Int32))方法，在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)命名空间，用于设置在导航栏的高度[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 此外， [ `NavigationPage.GetBarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.GetBarHeight(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.NavigationPage}))方法可用于返回在导航栏的高度`NavigationPage`。

结果是，在导航栏的高度[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)可以设置：

![](android-images/navigationpage-barheight.png "NavigationPage 导航栏高度")

<a name="tabbedpage-transition-animations" />

### <a name="disabling-page-transition-animations-in-a-tabbedpage"></a>您不要将 TabbedPage 中禁用页面切换动画

此特定于平台的用于时的页面之间导航，或者以编程方式或在使用选项卡栏中，禁用过渡动画[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 设置使用在 XAML`TabbedPage.IsSmoothScrollEnabled`可绑定属性设置为`false`:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

`TabbedPage.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 `TabbedPage.SetIsSmoothScrollEnabled`方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于控制是否在页之间导航时，将显示过渡动画[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 此外，`TabbedPage`类中`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`命名空间还具有以下方法：

- `IsSmoothScrollEnabled`它用于检索是否之间导航页中，将显示过渡动画`TabbedPage`。
- `EnableSmoothScroll`用于启用过渡动画中的页面之间导航时`TabbedPage`。
- `DisableSmoothScroll`用于在页面之间导航时禁用过渡动画`TabbedPage`。

<a name="enable_swipe_paging" />

### <a name="enabling-swiping-between-pages-in-a-tabbedpage"></a>启用在您不要将 TabbedPage 页面间轻扫

此特定于平台的用于启用与中的页面水平手指笔势轻扫[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 设置使用在 XAML [ `TabbedPage.IsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty)附加到属性`boolean`值：

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

`TabbedPage.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 [ `TabbedPage.SetIsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled(Xamarin.Forms.BindableObject,System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于启用中的页面轻扫[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 此外，`TabbedPage`类中`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`命名空间也具有[ `EnableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage}))方法，使此特定于平台的和一个[ `DisableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage}))禁用的方法此特定于平台的。 [ `TabbedPage.OffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty)附加属性，以及[ `SetOffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit(Xamarin.Forms.BindableObject,System.Int32))方法，用于设置应保留在当前页的任何一侧上空闲状态中的页面数。

结果是通过显示的页通过该轻扫分页[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)已启用：

![](android-images/tabbedpage-swipe.png)

<a name="tabbedpage-toolbar" />

### <a name="setting-tabbedpage-toolbar-placement-and-color"></a>设置 TabbedPage 工具栏位置和颜色

这些平台特定信息用于上设置的位置和颜色的工具栏[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 它们由在 XAML 中设置[ `TabbedPage.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty)附加属性的值为[ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement)枚举，以及[ `TabbedPage.BarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty)和[ `TabbedPage.BarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty)附加到的属性[ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

或者，因此，可以使用从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

`TabbedPage.On<Android>`方法指定仅将在 Android 上运行这些平台特定信息。 [ `TabbedPage.SetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement))方法，在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于上设置工具栏位置[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，与[`ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement)枚举提供以下值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default) – 指示工具栏位于页面上的默认位置。 这是页的在手机上，页面顶部和底部上其他设备的惯用语言。
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top) – 指示工具栏被放在页面顶部。
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom) – 指示工具栏放置在页面的底部。

此外， [ `TabbedPage.SetBarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color))并[ `TabbedPage.SetBarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color))方法用于分别设置工具栏项和选定的工具栏项的颜色。

> [!NOTE]
> [ `GetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage}))， [ `GetBarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage}))，以及[ `GetBarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage}))方法可用于检索位置和颜色[ `TabbedPage`](xref:Xamarin.Forms.TabbedPage)工具栏。

结果是，可以在设置工具栏位置、 工具栏项的颜色和所选的工具栏项的颜色[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

![](android-images/tabbedpage-toolbar-placement.png)

## <a name="application"></a>应用程序

在 Android 上，以下特定于平台的功能提供适用于 Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application)类：

- 设置屏幕键盘的操作模式。 有关详细信息，请参阅[软键盘输入模式设置](#soft_input_mode)。
- 禁用[ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing)并[ `Appearing` ](xref:Xamarin.Forms.Page.Appearing)页生命周期事件上暂停和继续分别使用 AppCompat 的应用程序。 有关详细信息，请参阅[禁用消失并使其不显示页面生命周期事件](#disable_lifecycle_events)。

<a name="soft_input_mode" />

### <a name="setting-the-soft-keyboard-input-mode"></a>设置屏幕键盘输入的模式

此特定于平台的用于设置屏幕键盘输入区域中，运行模式和设置在 XAML 中由[ `Application.WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty)附加属性的值为[ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)枚举：

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

`Application.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 [ `Application.UseWindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust))方法，在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于设置屏幕键盘输入的区域的运行模式，与[ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)枚举提供两个值： [ `Pan` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan)并[ `Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize)。 `Pan`值使用[ `AdjustPan` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/)调整选项，当输入的控件具有焦点时都不会调整窗口大小。 相反，以便当前焦点不会因软键盘遮住素数窗口的内容。 `Resize`值使用[ `AdjustResize` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/)输入的控件具有焦点，屏幕键盘的腾出空间，请调整窗口的大小调整选项。

结果是软键盘输入的输入的控件具有焦点时，可以设置运行模式的区域：

[![](android-images/pan-resize.png "软键盘操作模式的特定于平台")](android-images/pan-resize-large.png#lightbox "操作模式下特定于平台的屏幕键盘")

<a name="disable_lifecycle_events" />

### <a name="disabling-the-disappearing-and-appearing-page-lifecycle-events"></a>禁用消失和显示页面生命周期事件

此特定于平台的用来禁用[ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing)并[ `Appearing` ](xref:Xamarin.Forms.Page.Appearing)上应用程序的页面事件暂停和继续分别使用 AppCompat 的应用程序。 此外，它包含控件的功能是否软键盘显示在恢复，如果它显示了上暂停，前提是软键盘的操作模式设置为[ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize)。

> [!NOTE]
> 请注意默认情况下启用这些事件是以保留现有的应用程序依赖于事件的行为。 禁用这些事件将使匹配预 AppCompat 事件周期 AppCompat 事件周期。

此特定于平台的可供在 XAML 中设置[ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty)， [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty)，以及[ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) 到附加属性`boolean`值：

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

`Application.Current.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)命名空间，用于启用或禁用激发[ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing)页面事件，当应用程序进入背景。 [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean))方法用于启用或禁用激发[ `Appearing` ](xref:Xamarin.Forms.Page.Appearing)页面事件时，如果应用程序将继续在后台从。 [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean))方法用于控制是否软键盘显示在恢复，如果它已暂停，显示提供的软键盘的操作模式设置为[ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

结果是， [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing)并[ `Appearing` ](xref:Xamarin.Forms.Page.Appearing)页面事件不会触发应用程序暂停和恢复，并且，如果软键盘是后显示应用程序已暂停，它还会显示该应用程序恢复运行时：

[![](android-images/keyboard-on-resume.png "平台特有的生命周期事件")](android-images/keyboard-on-resume-large.png#lightbox "生命周期事件特定于平台的")

## <a name="summary"></a>总结

本文演示了如何使用 Android 平台特定信息的内置于 Xamarin.Forms。 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。

## <a name="related-links"></a>相关链接

- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
