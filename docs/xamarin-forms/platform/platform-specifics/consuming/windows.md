---
title: Windows 平台的详细信息
description: 平台细节，可以使用提供功能，仅在特定平台上，而无需实现自定义呈现器或效应。 本文演示如何使用 Windows 平台的详细信息，它们构建于 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: 7299de658a3491928e9bbeaa4dd192a8e95c435e
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732796"
---
# <a name="windows-platform-specifics"></a>Windows 平台的详细信息

_平台细节，可以使用提供功能，仅在特定平台上，而无需实现自定义呈现器或效应。本文演示如何使用 Windows 平台的详细信息，它们构建于 Xamarin.Forms。_

在通用 Windows 平台 (UWP)，Xamarin.Forms 包含以下平台的详细信息：

- 设置工具栏放置选项。 有关详细信息，请参阅[更改工具栏放置](#toolbar_placement)。
- 折叠[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)导航栏。 有关详细信息，请参阅[折叠 MasterDetailPage 导航栏](#collapsable_navigation_bar)。
- 启用[ `WebView` ](xref:Xamarin.Forms.WebView)在 UWP 消息对话框显示 JavaScript 警报。 有关详细信息，请参阅[显示 JavaScript 警报](#webview-javascript-alert)。
- 启用[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)与拼写检查引擎进行交互。 有关详细信息，请参阅[启用 SearchBar 拼写检查](#searchbar-spellcheck)。
- 检测文本中的内容从读取顺序[ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，和[ `Label` ](xref:Xamarin.Forms.Label)实例。 有关详细信息，请参阅[检测从内容的阅读顺序](#inputview-readingorder)。
- 禁用上支持的旧配色模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[禁用旧配色模式](#legacy-color-mode)。
- 启用中的点击手势支持[ `ListView` ](xref:Xamarin.Forms.ListView)。 有关详细信息，请参阅[在 ListView 中启用点击手势支持](#listview-selectionmode)。

<a name="toolbar_placement" />

## <a name="changing-the-toolbar-placement"></a>更改工具栏放置

此特定于平台的用于上更改工具栏的放置[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，和设置在 XAML 中由[ `Page.ToolbarPlacement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty/)附加属性的值写入[`ToolbarPlacement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/)枚举：

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

`Page.On<Windows>`方法指定此特定于平台的将仅在 Windows 上运行。 [ `Page.SetToolbarPlacement` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page}/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/)方法，请在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)命名空间，用于设置工具栏放置， [ `ToolbarPlacement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/)枚举提供三个值： [ `Default` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default/)， [ `Top` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top/)，和[ `Bottom` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom/)。

结果是，指定的工具栏放置将应用到[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)实例：

[![](windows-images/toolbar-placement.png "工具栏放置特定于平台的")](windows-images/toolbar-placement-large.png#lightbox "工具栏放置特定于平台的")

<a name="collapsable_navigation_bar" />

## <a name="collapsing-a-masterdetailpage-navigation-bar"></a>折叠 MasterDetailPage 导航栏

此特定于平台的用于折叠导航栏上[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)，和设置在 XAML 中由[ `MasterDetailPage.CollapseStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty/)和[ `MasterDetailPage.CollapsedPaneWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty/)附加属性：

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

`MasterDetailPage.On<Windows>`方法指定此特定于平台的将仅在 Windows 上运行。 [ `Page.SetCollapseStyle` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage}/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/)方法，请在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)命名空间，用于与指定的折叠样式， [ `CollapseStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/)枚举提供两个值： [ `Full` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full/)和[ `Partial` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial/)。 [ `MasterDetailPage.CollapsedPaneWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage}/System.Double/)方法用于指定部分折叠的导航栏的宽度。

结果是，指定[ `CollapseStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/)应用于[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)实例，而且还指定的宽度：

[![](windows-images/collapsed-navigation-bar.png "折叠导航栏平台特有")](windows-images/collapsed-navigation-bar-large.png#lightbox "折叠导航栏特定于平台")

<a name="webview-javascript-alert" />

## <a name="displaying-javascript-alerts"></a>显示 JavaScript 警报

此特定于平台的启用[ `WebView` ](xref:Xamarin.Forms.WebView)在 UWP 消息对话框显示 JavaScript 警报。 设置使用在 XAML 中[ `WebView.IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty)附加到属性`boolean`值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var webView = new Xamarin.Forms.WebView
{
  Source = new HtmlWebViewSource
  {
    Html = @"<html><body><button onclick=""window.alert('Hello World from JavaScript');"">Click Me</button></body></html>"
  }
};
webView.On<Windows>().SetIsJavaScriptAlertEnabled(true);
```

`WebView.On<Windows>`方法指定仅将在通用 Windows 平台上运行此特定于平台的。 [ `WebView.SetIsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean))方法，请在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，用于控制是否已启用 JavaScript 警报。 此外，`WebView.SetIsJavaScriptAlertEnabled`方法可以用于通过调用切换 JavaScript 警报[ `IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*)方法以返回是否已启用：

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

结果是可以在 UWP 消息对话框显示 JavaScript 警报：

![WebView JavaScript 警报特定于平台的](windows-images/webview-javascript-alert.png "WebView JavaScript 警报特定于平台的")

<a name="searchbar-spellcheck" />

## <a name="enabling-searchbar-spell-check"></a>启用 SearchBar 拼写检查

此特定于平台的启用[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)与拼写检查引擎进行交互。 设置使用在 XAML 中[ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty)附加到属性`boolean`值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

`SearchBar.On<Windows>`方法指定仅将在通用 Windows 平台上运行此特定于平台的。 [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean))方法，请在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，开启和关闭的拼写检查器。 此外，`SearchBar.SetIsSpellCheckEnabled`方法可以用于通过调用切换拼写检查器[ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar}))方法以返回指示是否启用拼写检查器：

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

结果是输入到该文本[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)可以拼写检查，使用不正确的拼写指定给用户：

![SearchBar 拼写检查特定于平台的](windows-images/searchbar-spellcheck.png "SearchBar 拼写检查特定于平台的")

> [!NOTE]
> `SearchBar`类[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间还具有[ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*)和[ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*)可用来启用和禁用的方法上的拼写检查器`SearchBar`分别。

<a name="inputview-readingorder" />

## <a name="detecting-reading-order-from-content"></a>检测阅读顺序从内容

此特定于平台的启用双向中文本的阅读顺序 （从左到右还是从右到左） [ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，和[ `Label` ](xref:Xamarin.Forms.Label)动态检测到的实例。 设置使用在 XAML 中[ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (有关`Entry`和`Editor`实例) 或[ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty)附加到属性`boolean`值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

`Editor.On<Windows>`方法指定仅将在通用 Windows 平台上运行此特定于平台的。 [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean))方法，请在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，用于控制是否从内容中检测到的阅读顺序[ `InputView` ](xref:Xamarin.Forms.InputView). 此外，`InputView.SetDetectReadingOrderFromContent`方法可以用于切换是否的阅读顺序从内容检测到通过调用[ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView}))方法以返回当前值：

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

结果是， [ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，和[ `Label` ](xref:Xamarin.Forms.Label)实例可以具有动态检测到其内容的阅读顺序：

[![检测从内容特定于平台的阅读顺序的 InputView](windows-images/editor-readingorder.png "InputView 检测从内容特定于平台的阅读顺序")](windows-images/editor-readingorder-large.png#lightbox "InputView 检测从的阅读顺序特定于平台的内容")

> [!NOTE]
> 与设置不同[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性时，检测的阅读顺序从其文本内容将不会影响视图中的文本的对齐方式的视图的逻辑。 相反，它将调整在其中的双向文本块进行布局的顺序。

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>禁用旧配色模式

Xamarin.Forms 视图的某些功能旧配色模式。 在此模式下，当[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)视图的属性设置为`false`，查看将覆盖由具有已禁用状态的默认本机颜色用户设置的颜色。 向后兼容性，这种旧颜色模式保持受支持的视图的默认行为。

此特定于平台的禁用旧颜色此模式下，以便用户在视图上设置的颜色保持，即使禁用的视图。 设置使用在 XAML 中[ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty)附加到属性`false`:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Editor Text="Enter text here"
                TextColor="Blue"
                BackgroundColor="Bisque"
                windows:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Windows>`方法指定此特定于平台的将仅在 Windows 上运行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean))方法，请在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，用于控制是否禁用旧配色模式。 此外， [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))方法可以用于返回是否禁用旧配色模式。

结果是，则可以禁用旧配色模式，以便用户在视图上设置的颜色甚至保持禁用视图时：

![](windows-images/legacy-color-mode-disabled.png "旧颜色模式被禁用")

> [!NOTE]
> 设置时[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)在视图中，完全忽略旧配色模式。 可视状态有关的详细信息，请参阅[Xamarin.Forms 视觉状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

<a name="listview-selectionmode" />

## <a name="enabling-tap-gesture-support-in-a-listview"></a>启用在 ListView 中的点击手势支持

在通用 Windows 平台上，默认情况下，Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView)使用本机`ItemClick`事件能够响应交互，而不是本机`Tapped`事件。 这提供了可访问性功能，以便 Windows 讲述人和键盘可以与交互`ListView`。 但是，它还会呈现在任何点击手势`ListView`无法运行。

此特定于平台的控件是否中的项[ `ListView` ](xref:Xamarin.Forms.ListView)可以响应点击手势，并因此是否本机`ListView`激发`ItemClick`或`Tapped`事件。 设置使用在 XAML 中[ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty)附加属性的值写入[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)枚举：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

`ListView.On<Windows>`方法指定仅将在通用 Windows 平台上运行此特定于平台的。 [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode))方法，请在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，是否用于控制中的项[ `ListView` ](xref:Xamarin.Forms.ListView)可以响应与点击手势，[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)枚举提供两个可能值：

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) – 指示`ListView`将激发本机`ItemClick`事件来处理交互，并因此提供可访问性功能。 因此，Windows 讲述人和键盘可以与交互`ListView`。 但是，在项`ListView`无法响应点击手势。 这是默认行为`ListView`通用 Windows 平台上的实例。
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) – 指示`ListView`将激发本机`Tapped`事件以处理交互。 因此中的项`ListView`可以响应点击手势。 但是，没有任何可访问性功能，因此 Windows 讲述人和键盘不能与交互`ListView`。

> [!NOTE]
> `Accessible`和`Inaccessible`选择模式是互斥的并且将需要选择之间访问[ `ListView` ](xref:Xamarin.Forms.ListView)或`ListView`可响应的点击手势。

此外， [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView}))方法可以用于返回当前[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)。

结果是，指定[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)应用于[ `ListView` ](xref:Xamarin.Forms.ListView)，哪些控件是否中的项`ListView`可以响应点击手势，并因此是否本机`ListView`激发`ItemClick`或`Tapped`事件。

## <a name="summary"></a>总结

这篇文章演示了如何使用 Windows 平台的详细信息，它们构建于 Xamarin.Forms。 平台细节，可以使用提供功能，仅在特定平台上，而无需实现自定义呈现器或效应。

## <a name="related-links"></a>相关链接

- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)
