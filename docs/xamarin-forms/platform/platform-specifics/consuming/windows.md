---
title: Windows 平台特定信息
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文演示如何使用 Windows 平台特定信息的内置于 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 39922ffaa144892c87d271e9b24524dc0a0a941a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061915"
---
# <a name="windows-platform-specifics"></a>Windows 平台特定信息

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

_平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。本文演示如何使用 Windows 平台特定信息的内置于 Xamarin.Forms。_

## <a name="visualelements"></a>VisualElements

通用 Windows 平台上的以下特定于平台的功能提供 Xamarin.Forms 视图、 页面和布局：

- 设置的访问密钥[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[设置 VisualElement 访问密钥](#visualelement-accesskeys)。
- 禁用上受支持的旧颜色模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[禁用旧式颜色模式](#legacy-color-mode)。

<a name="visualelement-accesskeys" />

### <a name="setting-visualelement-access-keys"></a>设置 VisualElement 访问密钥

访问键都是通过为用户可以快速导航并进行交互以直观的方式提供通过触摸键盘而不是通过应用程序的可见 UI 改进的可用性和可访问性的通用 Windows 平台上的应用程序的键盘快捷方式或鼠标。 它们是 Alt 键和一个或多个字母数字，通常按下键按顺序的组合。 使用单个字母数字字符的访问密钥会自动支持键盘快捷方式。

访问键提示浮动徽章控件，其中包含访问密钥旁边显示。 每个访问键提示包含激活关联的控件的字母数字键。 当用户按下 Alt 键时，显示访问键提示。

此特定于平台的用来指定的访问密钥[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 设置使用在 XAML [ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty)为字母数字值，并根据需要设置附加属性[ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty)附加属性设置为值为[ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement)枚举[ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty)附加属性设置为`double`，并[ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty)附加属性设置为`double`:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <ContentPage Title="Page 1"
                 windows:VisualElement.AccessKey="1">
        <StackLayout Margin="20">
            ...
            <Switch windows:VisualElement.AccessKey="A" />
            <Entry Placeholder="Enter text here"
                   windows:VisualElement.AccessKey="B" />
            ...
            <Button Text="Access key F, placement top with offsets"
                    Margin="20"
                    Clicked="OnButtonClicked"
                    windows:VisualElement.AccessKey="F"
                    windows:VisualElement.AccessKeyPlacement="Top"
                    windows:VisualElement.AccessKeyHorizontalOffset="20"
                    windows:VisualElement.AccessKeyVerticalOffset="20" />
            ...
        </StackLayout>
    </ContentPage>
    ...
</TabbedPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var page = new ContentPage { Title = "Page 1" };
page.On<Windows>().SetAccessKey("1");

var switchView = new Switch();
switchView.On<Windows>().SetAccessKey("A");
var entry = new Entry { Placeholder = "Enter text here" };
entry.On<Windows>().SetAccessKey("B");
...

var button4 = new Button { Text = "Access key F, placement top with offsets", Margin = new Thickness(20) };
button4.Clicked += OnButtonClicked;
button4.On<Windows>()
    .SetAccessKey("F")
    .SetAccessKeyPlacement(AccessKeyPlacement.Top)
    .SetAccessKeyHorizontalOffset(20)
    .SetAccessKeyVerticalOffset(20);
...
```

`VisualElement.On<Windows>`方法指定仅将在通用 Windows 平台上运行此特定于平台的。 [ `VisualElement.SetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.String))方法，在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，用于设置的访问密钥值`VisualElement`。 [ `VisualElement.SetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},Xamarin.Forms.AccessKeyPlacement))方法中，（可选） 指定的位置，用于访问键提示，显示与[ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement)枚举提供以下可能值：

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) – 指示访问键提示放置将由操作系统。
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) – 指示访问键提示将出现上面的上边缘`VisualElement`。
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) – 指示访问键提示将显示下面的下边缘`VisualElement`。
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) – 指示访问键提示将显示右侧的右边缘的`VisualElement`。
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) – 指示访问键提示将显示的左边缘的左侧`VisualElement`。
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) – 指示，访问键提示将显示为叠加的中心`VisualElement`。

> [!NOTE]
> 通常情况下， [ `Auto` ](xref:Xamarin.Forms.AccessKeyPlacement.Auto)键提示放置已足够，其中包括对自适应用户界面的支持。

[ `VisualElement.SetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double))并[ `VisualElement.SetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double))方法可用于访问键提示位置的更精细的控制。 参数`SetAccessKeyHorizontalOffset`方法指示如何得移动访问键提示向左或向右、 和的参数`SetAccessKeyVerticalOffset`方法指示如何得来向上或向下移动访问键提示。

>[!NOTE]
> 访问密钥放置设置时，不能设置访问键提示的偏移量`Auto`。

此外， [ `GetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))， [ `GetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))， [ `GetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))，并[ `GetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))可以使用方法若要检索所需的访问密钥值和它的位置。

结果是访问键提示，可以显示任何旁边[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)实例定义的访问密钥，通过按 Alt 键：

![VisualElement 访问密钥特定于平台的](windows-images/visualelement-accesskeys.png "VisualElement 访问密钥特定于平台的")

当用户激活通过按 Alt 键，然后按访问访问密钥，密钥的默认操作为`VisualElement`将执行。 例如，当用户激活的访问密钥上[ `Switch` ](xref:Xamarin.Forms.Switch)，则`Switch`处于切换状态。 当用户在激活的访问密钥[ `Entry` ](xref:Xamarin.Forms.Entry)，则`Entry`获得焦点。 当用户在激活的访问密钥[ `Button` ](xref:Xamarin.Forms.Button)，事件处理程序[ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)执行事件。

有关访问密钥的详细信息，请参阅[访问密钥](/windows/uwp/design/input/access-keys#key-tip-positioning)。

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>禁用旧式颜色模式

某些 Xamarin.Forms 视图功能旧颜色模式。 在此模式下，当[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)视图的属性设置为`false`，视图将重写由具有已禁用状态的默认本机颜色用户设置的颜色。 有关向后兼容性，这种旧颜色模式保持不受支持视图的默认行为。

此特定于平台的禁用此旧颜色模式，以便对视图由用户设置的颜色保持，即使禁用的视图。 设置使用在 XAML [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty)附加到属性`false`:

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

`VisualElement.On<Windows>`方法指定仅将在 Windows 上运行此特定于平台的。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，用于控制是否将禁用旧颜色模式。 此外， [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))方法可以用于返回是否禁用旧颜色模式。

结果是，可以禁用旧版颜色模式，以便对视图由用户设置的颜色甚至保持禁用视图时：

![](windows-images/legacy-color-mode-disabled.png "旧颜色模式已禁用")

> [!NOTE]
> 设置时[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)旧颜色模式被完全忽略上一个视图。 可视状态的详细信息，请参阅[Xamarin.Forms 视觉状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="views"></a>Views

在通用 Windows 平台 (UWP)，以下特定于平台的功能用于 Xamarin.Forms 视图：

- 检测文本中的内容从读取顺序[ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，以及[ `Label` ](xref:Xamarin.Forms.Label)实例。 有关详细信息，请参阅[从内容检测的阅读顺序](#inputview-readingorder)。
- 启用中的点击手势支持[ `ListView` ](xref:Xamarin.Forms.ListView)。 有关详细信息，请参阅[ListView 中启用点击手势支持](#listview-selectionmode)。
- 启用[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)与拼写检查引擎进行交互。 有关详细信息，请参阅[启用 SearchBar 拼写检查](#searchbar-spellcheck)。
- 启用[ `WebView` ](xref:Xamarin.Forms.WebView) UWP 消息对话框中显示 JavaScript 警报。 有关详细信息，请参阅[显示 JavaScript 警报](#webview-javascript-alert)。

<a name="inputview-readingorder" />

### <a name="detecting-reading-order-from-content"></a>检测内容从阅读顺序

此特定于平台的支持双向文本中的阅读顺序 （从左到右还是从右到左） [ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，以及[ `Label` ](xref:Xamarin.Forms.Label)动态检测到的实例。 设置使用在 XAML [ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (对于`Entry`并`Editor`实例) 或[ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty)附加属性设置为`boolean`值：

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

`Editor.On<Windows>`方法指定仅将在通用 Windows 平台上运行此特定于平台的。 [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，用于控制是否从内容中检测到的阅读顺序[ `InputView` ](xref:Xamarin.Forms.InputView). 此外，`InputView.SetDetectReadingOrderFromContent`方法可用于切换阅读顺序从内容检测到通过调用是否[ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView}))方法返回的当前值：

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

结果是， [ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，以及[ `Label` ](xref:Xamarin.Forms.Label)实例可能拥有动态检测到其内容的阅读顺序：

[![检测的阅读顺序从内容特定于平台的 InputView](windows-images/editor-readingorder.png "InputView 检测从内容特定于平台的阅读顺序")](windows-images/editor-readingorder-large.png#lightbox "InputView 检测从阅读顺序特定于平台的内容")

> [!NOTE]
> 与设置不同[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性中，检测的阅读顺序从其文本内容将不会影响视图中的文本的对齐方式的视图的逻辑。 相反，它将调整在其中放置的双向文本块的顺序。

<a name="listview-selectionmode" />

### <a name="enabling-tap-gesture-support-in-a-listview"></a>启用在 ListView 中的点击手势支持

在通用 Windows 平台上，默认情况下，Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView)使用本机`ItemClick`事件响应交互，而不是本机`Tapped`事件。 这提供了可访问性功能，以便 Windows 讲述人和键盘可以与交互`ListView`。 但是，它还会呈现在任何点击手势`ListView`不可操作。

此特定于平台的控件是否中的项[ `ListView` ](xref:Xamarin.Forms.ListView)可响应点击手势，并因此是否本机`ListView`激发`ItemClick`或`Tapped`事件。 设置使用在 XAML [ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty)附加属性的值为[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)枚举：

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

`ListView.On<Windows>`方法指定仅将在通用 Windows 平台上运行此特定于平台的。 [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode))方法，在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，是否用于控制中的项[ `ListView` ](xref:Xamarin.Forms.ListView)可响应的点击手势，[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)枚举提供两个可能值：

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) – 指示`ListView`将触发本机`ItemClick`事件处理的交互，并因此提供可访问性功能。 因此，Windows 讲述人和键盘可以与交互`ListView`。 但是中的项`ListView`无法响应点击手势。 这是默认行为`ListView`通用 Windows 平台上的实例。
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) – 指示`ListView`将触发本机`Tapped`事件用于处理的交互。 因此中的项`ListView`可响应点击手势。 但是，没有可访问性功能，因此 Windows 讲述人和键盘不能与交互`ListView`。

> [!NOTE]
> `Accessible`并`Inaccessible`选择模式是互斥的并且将需要选择可访问[ `ListView` ](xref:Xamarin.Forms.ListView)或`ListView`可响应的点击手势。

此外， [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView}))方法可用于返回当前[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)。

结果是，指定[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)应用于[ `ListView` ](xref:Xamarin.Forms.ListView)，哪些控件是否中的项`ListView`可响应点击手势，并因此是否本机`ListView`激发`ItemClick`或`Tapped`事件。

<a name="searchbar-spellcheck" />

### <a name="enabling-searchbar-spell-check"></a>启用 SearchBar 拼写检查

此特定于平台的支持[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)与拼写检查引擎进行交互。 设置使用在 XAML [ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty)附加到属性`boolean`值：

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

`SearchBar.On<Windows>`方法指定仅将在通用 Windows 平台上运行此特定于平台的。 [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，用于拼写检查器打开和关闭。 此外，`SearchBar.SetIsSpellCheckEnabled`方法可用于切换的拼写检查器通过调用[ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar}))方法以返回指示是否启用拼写检查器：

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

结果是输入到该文本[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)可以进行拼写检查，使用不正确的拼写指定给用户：

![SearchBar 拼写检查平台专属](windows-images/searchbar-spellcheck.png "SearchBar 拼写检查特定于平台的")

> [!NOTE]
> `SearchBar`类中[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间还具有[ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*)并[ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*)方法可用于启用和禁用上的拼写检查器`SearchBar`分别。

<a name="webview-javascript-alert" />

### <a name="displaying-javascript-alerts"></a>显示 JavaScript 警报

此特定于平台的支持[ `WebView` ](xref:Xamarin.Forms.WebView) UWP 消息对话框中显示 JavaScript 警报。 设置使用在 XAML [ `WebView.IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty)附加到属性`boolean`值：

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

`WebView.On<Windows>`方法指定仅将在通用 Windows 平台上运行此特定于平台的。 [ `WebView.SetIsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，用于控制是否启用了 JavaScript 警报。 此外，`WebView.SetIsJavaScriptAlertEnabled`方法可用于通过调用切换 JavaScript 警报[ `IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*)方法以返回是否已启用：

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

结果是，可以 JavaScript 警报显示在 UWP 消息对话框：

![Web 视图 JavaScript 警报平台专属](windows-images/webview-javascript-alert.png "web 视图 JavaScript 警报特定于平台的")

## <a name="pages"></a>Pages

在通用 Windows 平台上，为 Xamarin.Forms 页面提供以下特定于平台的功能：

- 折叠[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)导航栏。 有关详细信息，请参阅[折叠 MasterDetailPage 导航栏](#collapsable_navigation_bar)。
- 设置工具栏的放置选项。 有关详细信息，请参阅[更改页面工具栏放置](#toolbar_placement)。
- 启用页面图标上显示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)工具栏。 有关详细信息，请参阅[TabbedPage 上启用图标](#tabbedpage-icons)。

<a name="collapsable_navigation_bar" />

### <a name="collapsing-a-masterdetailpage-navigation-bar"></a>折叠 MasterDetailPage 导航栏

此特定于平台的用于折叠导航栏上[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)，并设置在 XAML 中由[ `MasterDetailPage.CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty)并[ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty)附加属性：

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

`MasterDetailPage.On<Windows>`方法指定仅将在 Windows 上运行此特定于平台的。 [ `Page.SetCollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle))方法，在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，用于指定折叠样式[ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle)枚举提供两个值： [ `Full` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full)并[ `Partial` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial)。 [ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},System.Double))方法用于指定部分折叠的导航栏的宽度。

结果是，指定[ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle)应用于[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)实例，而且还指定的宽度：

[![](windows-images/collapsed-navigation-bar.png "折叠导航栏中特定于平台")](windows-images/collapsed-navigation-bar-large.png#lightbox "折叠导航栏中特定于平台")

<a name="toolbar_placement" />

### <a name="changing-the-page-toolbar-placement"></a>更改页工具栏位置

此特定于平台的用于上更改工具栏的放置[ `Page` ](xref:Xamarin.Forms.Page)，并设置在 XAML 中由[ `Page.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty)附加属性的值为[`ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement)枚举：

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

`Page.On<Windows>`方法指定仅将在 Windows 上运行此特定于平台的。 [ `Page.SetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement))方法，在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，用于通过设置工具栏位置[ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement)枚举提供三个值： [ `Default` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default)， [ `Top` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top)，以及[ `Bottom` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom)。

结果是，为应用指定的工具栏放置[ `Page` ](xref:Xamarin.Forms.Page)实例：

[![](windows-images/toolbar-placement.png "工具栏位置平台专属")](windows-images/toolbar-placement-large.png#lightbox "工具栏放置特定于平台的")

<a name="tabbedpage-icons" />

### <a name="enabling-icons-on-a-tabbedpage"></a>启用您不要将 TabbedPage 上的图标

此特定于平台的支持页面图标上显示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)工具栏中，并提供的功能，还可以选择指定图标的大小。 设置使用在 XAML [ `TabbedPage.HeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty)附加到的属性`true`，并根据需要设置[ `TabbedPage.HeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty)附加属性设置为[ `Size`](xref:Xamarin.Forms.Size)值：

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:TabbedPage.HeaderIconsEnabled="true">
    <windows:TabbedPage.HeaderIconsSize>
        <Size>
            <x:Arguments>
                <x:Double>24</x:Double>
                <x:Double>24</x:Double>
            </x:Arguments>
        </Size>
    </windows:TabbedPage.HeaderIconsSize>
    <ContentPage Title="Todo" Icon="todo.png">
        ...
    </ContentPage>
    <ContentPage Title="Reminders" Icon="reminders.png">
        ...
    </ContentPage>
    <ContentPage Title="Contacts" Icon="contacts.png">
        ...
    </ContentPage>
</TabbedPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

public class WindowsTabbedPageIconsCS : Xamarin.Forms.TabbedPage
{
  public WindowsTabbedPageIconsCS()
    {
    On<Windows>().SetHeaderIconsEnabled(true);
    On<Windows>().SetHeaderIconsSize(new Size(24, 24));

    Children.Add(new ContentPage { Title = "Todo", Icon = "todo.png" });
    Children.Add(new ContentPage { Title = "Reminders", Icon = "reminders.png" });
    Children.Add(new ContentPage { Title = "Contacts", Icon = "contacts.png" });
  }
}
```

`TabbedPage.On<Windows>`方法指定仅将在通用 Windows 平台上运行此特定于平台的。 [ `TabbedPage.SetHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，用于启用或禁用标头的图标。 [ `TabbedPage.SetHeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsSize(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},Xamarin.Forms.Size))方法 （可选） 指定使用的标头图标大小[ `Size` ](xref:Xamarin.Forms.Size)值。

此外，`TabbedPage`类中`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`命名空间也具有[ `EnableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*)方法，使标头图标[ `DisableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*)禁用标头图标的方法和[ `IsHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*)方法，它返回`boolean`值，该值指示是否启用了标头图标。

结果是图标可以显示在该页[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)工具栏上，根据需要设置为所需大小的图标大小：

![TabbedPage 图标启用特定于平台的](windows-images/tabbedpage-icons.png "TabbedPage 图标启用特定于平台的")

## <a name="summary"></a>总结

本文演示了如何使用 Windows 平台特定信息的内置于 Xamarin.Forms。 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。

## <a name="related-links"></a>相关链接

- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
