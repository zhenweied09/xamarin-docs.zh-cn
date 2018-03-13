---
title: "搜索和主屏幕小组件增强功能"
description: "本文介绍如何 Apple iOS 10 中的小组件系统对所做的增强功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 7ca863b92d8d7af46f4ce18f5d088347b9ca04ee
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="search-and-home-screen-widget-enhancements"></a>搜索和主屏幕小组件增强功能

_本文介绍如何 Apple iOS 10 中的小组件系统对所做的增强功能。_


Apple 引入了对小组件系统以确保小组件显示 10 锁定屏幕新的 iOS 存在任何背景上很好多项增强。 此外，小组件现在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)属性，允许开发人员描述了多少内容是否可用并允许用户可以展开和折叠内容。

小组件 （也称为今天扩展） 是一种特殊类型的 iOS 扩展版本显示少量的有用信息或公开及时的特定于应用的功能。 例如，新闻应用具有小组件显示顶部的标题，并且日历应用提供两个不同的小组件： 一个用于显示当前的事件，另一个用于显示即将到来的事件。

小组件可高度自定义，并且可能包含 UI 元素，例如文本、 图像、 按钮等。此外，开发人员可以进一步自定义其小组件的布局。

[![](widgets-images/widgets01.png "示例小组件")](widgets-images/widgets01.png#lightbox)

有两个主要位置，用户可以查看并与其交互的应用的小组件：

- **搜索屏幕**-用户可以添加小组件他们发现对其搜索屏幕最有用。 搜索屏幕可通过轻扫家庭和锁定屏幕上的权限。
- **在主页屏幕**-从主页屏幕上，用户可以使用 3D Touch 通过将压力应用于应用程序的图标打开快速操作列表。 将快速操作列表上方显示应用的小组件。 请参阅我们[简介 3D Touch](~/ios/platform/3d-touch.md)文档以了解更多信息。

## <a name="widgets-developer-suggestions"></a>小组件开发人员建议

理想情况下，开发人员应始终尝试并设计用户将想要添加到其搜索屏幕的小组件。 为此，Apple 具有以下建议：

- **创建出色，方便查看体验**-用户的需要来提供简短，方便查看的状态更新的信息或允许它们快地执行简单任务的小组件。 这使得提供正确数量的信息和交互性一个必不可少。 只要有可能，允许用户一次点击执行某个给定的任务。 此外，由于平移或滚动，则不支持小组件，这将需要考虑到在小组件的设计。
- **快速显示内容**的小组件设计为方便查看，因此用户从来不必等待要加载后显示的小组件内容。 本地以便在后台加载全新的内容时，它们可以显示新内容，小组件应缓存其内容。
- **提供相应的填充和边距**的小组件应永远不会查找拥挤，因此避免扩展与边缘的小组件的视图的内容。 应始终有多个边缘和内容之间的像素宽边距。 Apple 此外，还推荐使用应用程序的图标，显示顶部的小组件中，作为对齐方式的指导。 如果该小组件显示网格布局，确保没有正确填充在网格中的项之间，并重试限制到四个最大值的项的数目。
- **使用自适应布局**的小组件的宽度将差异取决于在运行的设备和设备的方向。 如果 Collapsed （默认值） 或 （不支持所有小组件） 的扩展状态中显示的小组件的高度也差异取决于。 折叠小组件具有大致两个半标准 iOS 表行的高度。 开发人员可以为展开的小组件请求大小，但它理想情况下应小于屏幕的高度。 处于折叠状态，该小组件应显示唯一的基本、 独立的信息。 当扩展，该小组件应显示增强了处于折叠状态中显示的主内容的补充信息。 快速操作列表中显示的小组件将只能处于折叠状态。
- **不小组件的背景的自定义**的小组件显示系统提供的较小的、 和经过模糊处理背景上。 这样做是为了提高小组件之间的一致性并提高其内容的可读性。 避免使用与小组件背景图像，因为它无法使用用户的锁定和主页屏幕壁纸冲突。
- **使用系统字体中黑色或深灰色**-当在小组件，系统字体效果最佳中显示文本。 字体应为要针对较小的、 和经过模糊处理的小组件背景突出的黑色或暗灰色颜色。
- **提供应用程序访问时相应**的小组件的应始终从其应用单独操作，但是，如果需要更深入的功能，则小组件应该能够启动应用程序来查看或编辑特定的信息。 只需永远不会包含"打开应用"按钮，允许用户点击内容本身和永远不会打开的第三方应用程序。
- **选择清除、 简洁的小组件名称**-对小组件的内容始终显示应用程序的图标和小组件的名称。 Apple 提供的建议使用其主小组件的应用程序的名称和任何其他它提供的清晰、 简洁名称。 当提供自定义的小组件标题时，它们应具有应用程序的名称 （例如地图附近、 地图餐馆，等等。） 作为前缀。
- **通知身份验证时将值添加**-如果其他功能或信息可仅当用户进行身份验证和签名，向用户显示此。 例如，持续一段时间共享我说出"登录到簿持续一段时间"的应用。
- **选择一个快速操作列表构件**-如果应用程序提供了多个小组件，开发人员应选择一个要呈现时用户将显示通过将压力应用于使用 3D Touch 应用程序的图标快速操作列表。

有关使用小组件的更多详细信息，请参阅我们[扩展简介](~/ios/platform/extensions.md)，[简介 3D Touch](~/ios/platform/3d-touch.md)文档和 Apple 的[应用程序扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html).

## <a name="working-with-vibrancy"></a>使用活力

活力可确保小组件的文本保持清晰时小组件的光模糊 （系统所提供的） 的背景上呈现。 在 iOS 10 之前的开发人员将使用[NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect)的小组件的活力。 例如:

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

这已在 iOS 10 否决，并应替换为使用[WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect)或[WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)。 例如:

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>使用折叠和展开小组件

新到 iOS 10，小组件现在包含[NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode)属性，允许开发人员描述了多少内容是否可用并允许用户可以展开和折叠内容。

最初显示小组件，它将处于折叠状态。 折叠小组件具有大致两个半标准 iOS 表行的高度。 开发人员可以为展开的小组件请求大小，但它理想情况下应小于屏幕的高度。 

处于折叠状态，该小组件应显示唯一的基本、 独立的信息。 当扩展，该小组件应显示增强了处于折叠状态中显示的主内容的补充信息。 例如，天气应用显示当前的天气情况折叠时，并将添加预测展开时每小时。

快速操作列表中显示的小组件将只能处于折叠状态。 如果应用程序提供了多个小组件，开发人员应选择一个要呈现时用户将显示通过将压力应用于使用 3D Touch 应用程序的图标快速操作列表。

下面的示例是简单今天的扩展名 （小组件） 处理的折叠和展开状态：

```csharp
using System;
using NotificationCenter;
using Foundation;
using UIKit;
using CoreGraphics;

namespace MonkeyAbout
{
    public partial class TodayViewController : UIViewController, INCWidgetProviding
    {
        protected TodayViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Tell widget it can be expanded
            ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);

            // Get the maximum size
            var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
        }

        [Export ("widgetPerformUpdateWithCompletionHandler:")]
        public void WidgetPerformUpdate (Action<NCUpdateResult> completionHandler)
        {
            // Take action based on the display mode
            switch (ExtensionContext.GetWidgetActiveDisplayMode()) {
            case NCWidgetDisplayMode.Compact:
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                Content.Text = "Gorilla!!!!";
                break;
            }

            // Report results
            // If an error is encoutered, use NCUpdateResultFailed
            // If there's no update required, use NCUpdateResultNoData
            // If there's an update, use NCUpdateResultNewData
            completionHandler (NCUpdateResult.NewData);
        }

        [Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
        public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
        {
            // Take action based on the display mode
            switch (activeDisplayMode) {
            case NCWidgetDisplayMode.Compact:
                PreferredContentSize = maxSize;
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                PreferredContentSize = new CGSize (0, 200);
                Content.Text = "Gorilla!!!!";
                break;
            }
        }

    }
}
```

了解详细信息中的小组件显示模式的特定代码。 为此小组件支持的扩展状态的情况下通知系统，它使用：

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

若要获取小组件的当前显示模式，它使用：

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

若要获取的最大大小为折叠或扩展状态，它使用：

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

然后，若要处理更改的状态 （显示模式），使用：

```csharp
[Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
{
    // Take action based on the display mode
    switch (activeDisplayMode) {
    case NCWidgetDisplayMode.Compact:
        PreferredContentSize = maxSize;
        Content.Text = "Let's Monkey About!";
        break;
    case NCWidgetDisplayMode.Expanded:
        PreferredContentSize = new CGSize (0, 200);
        Content.Text = "Gorilla!!!!";
        break;
    }
}
```

除了设置为每个状态 （折叠或展开） 请求的大小，它还会更新以匹配新的大小所显示的内容。

## <a name="summary"></a>摘要

这篇文章具有涵盖 Apple iOS 10 中的小组件系统对所做的增强功能，并显示如何在 Xamarin.iOS 中实现它们。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [扩展简介](~/ios/platform/extensions.md)
- [3D Touch 简介](~/ios/platform/3d-touch.md)
- [应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
