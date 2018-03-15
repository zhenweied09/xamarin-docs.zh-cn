---
title: Platform-Specifics
description: "平台细节，可以使用提供功能，仅在特定平台上，而无需实现自定义呈现器或效应。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/17/2017
ms.openlocfilehash: e68b13cc7f673d607022751a1f1038fd057a90d8
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2018
---
# <a name="platform-specifics"></a>Platform-Specifics

_平台细节，可以使用提供功能，仅在特定平台上，而无需实现自定义呈现器或效应。_

以下的特定于平台的功能已内置到 Xamarin.Forms 中：

|iOS|Android|Windows|
|--- |--- |--- |
|[VisualElement.BlurEffect](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#blur)|[Application.WindowSoftInputModeAdjust](~/xamarin-forms/platform/platform-specifics/consuming/android.md#soft_input_mode)|[Page.ToolbarPlacement](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#toolbar_placement)|
|[NavigationPage.PrefersLargeTitles](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#large_title)|[ListView.IsFastScrollEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#fastscroll)|[MasterDetailPage.CollapsedPaneWidth 和 MasterDetailPage.CollapseStyle](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#collapsable_navigation_bar)|
|[Page.UseSafeArea](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#safe_area_layout)|[TabbedPage.IsSwipePagingEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#enable_swipe_paging)|
|[NavigationPage.IsNavigationBarTranslucent](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#translucent_navigation_bar)|[Elevation.Elevation](~/xamarin-forms/platform/platform-specifics/consuming/android.md#elevation)|
|[NavigationPage.StatusBarTextColorMode](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#status_bar_color_mode)|[Application.SendDisappearingEventOnPause、 Application.SendAppearingEventOnResume 和 Application.ShouldPreserveKeyboardOnResume](~/xamarin-forms/platform/platform-specifics/consuming/android.md#disable_lifecycle_events)|
|[Entry.AdjustsFontSizeToFitWidth](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#adjust_font_size)|
|[Picker.UpdateMode](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode)|
|[Page.PrefersStatusBarHidden 和 Page.PreferredStatusBarUpdateAnimation](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#set_status_bar_visibility)|
|[ScrollView.ShouldDelayContentTouches](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#delay_content_touches)|

使用特定于平台的 XAML 中，通过代码或通过 fluent API 的过程如下所示：

1. 添加`xmlns`声明或`using`指令[ `Xamarin.Forms.PlatformConfiguration` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration/)命名空间。
1. 添加`xmlns`声明或`using`针对包含特定于平台的功能的命名空间的指令：
    1. 在 iOS 中，这是[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)命名空间。
    1. 在 Android 上，这是[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)命名空间。 对于 Android AppCompat，这是[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/)命名空间。
    1. 在通用 Windows 平台上，这是[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)命名空间。
1. 从 XAML 中，或使用的代码应用特定于平台的`On<T>`fluent API。 值`T`可以是[ `iOS` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOS/)， [ `Android` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.Android/)，或[ `Windows` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.Windows/)从类型[ `Xamarin.Forms.PlatformConfiguration`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration/)命名空间。

> [!NOTE]
> 请注意，尝试使用特定于平台的是不可用的平台上不会导致错误。 相反，代码将不特定于平台的应用的情况下执行。

通过使用平台特定信息`On<T>`fluent 代码 API 返回[ `IPlatformElementConfiguration` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IPlatformElementConfiguration%3CTPlatform,TElement%3E/)对象。 这样，要调用与方法级联对同一个对象的多个平台的细节。

有关平台细节的详细信息，请参阅[使用平台特定信息](~/xamarin-forms/platform/platform-specifics/consuming/index.md)和[创建平台特定信息](~/xamarin-forms/platform/platform-specifics/creating.md)。


## <a name="related-links"></a>相关链接

- [使用平台特定信息](~/xamarin-forms/platform/platform-specifics/consuming/index.md)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [PlatformConfiguration](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration/)
