---
title: 平台功能
description: 利用使用 Xamarin.Forms 的特定于平台的功能
ms.prod: xamarin
ms.assetid: 2C6CE42C-E380-4BB9-90CC-D0F4E60C4C03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/20/2017
ms.openlocfilehash: 454969d8a59128423d632452033b8ba84ee0bc98
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="platform-features"></a>平台功能

Xamarin.Forms 是可扩展的并允许你使用的特定于平台的功能合并[效果](~/xamarin-forms/app-fundamentals/effects/index.md)，[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)、 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)， [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)，和的详细信息。

## <a name="androidandroidindexmd"></a>[Android](android/index.md)

本指南介绍如何通过更新现有 Xamarin.Forms Android 应用程序实现材料设计。

## <a name="application-indexing-and-deep-linkingdeep-linkingmd"></a>[应用程序索引和深层链接](deep-linking.md)

应用程序索引允许将否则忘记后几个使用能够通过在搜索结果中显示相关的应用程序。 深层链接允许应用程序响应通过导航到从深层链接引用的页面通常包含应用程序数据的搜索结果。

## <a name="device-classdevicemd"></a>[设备分类](device.md)

如何使用`Device`类，以在共享的代码和用户界面 （包括使用 XAML） 中创建特定于平台的行为。 此外介绍了`BeginInvokeOnMainThread`在修改 UI 控件从后台线程时，这非常重要。

## <a name="iosiosindexmd"></a>[iOS](ios/index.md)

可以通过执行某些 iOS 样式**Info.plist**和`UIAppearance`API。 本指南包括有关如何将 iOS 9 功能包含在一个 Xamarin.Forms 解决方案，包括核心 Spotlight 搜索的 iOS 应用的示例。

## <a name="macmacmd"></a>[Mac](mac.md)

Xamarin.Forms 现在具有对 macOS 应用的预览支持。

## <a name="native-formsnative-formsmd"></a>[本机窗体](native-forms.md)

本机窗体允许 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-派生页将使用的本机 Xamarin.iOS 和 Xamarin.Android，通用 Windows 平台 (UWP) 项目。

## <a name="native-viewsnative-viewsindexmd"></a>[本机视图](native-views/index.md)

从 iOS、 Android 和通用 Windows 平台的本机视图可以从 Xamarin.Forms 直接引用。 可以在本机视图中设置属性和事件处理程序，并且它们可以与 Xamarin.Forms 视图交互。

## <a name="platform-specificsplatform-specificsindexmd"></a>[Platform-Specifics](platform-specifics/index.md)

平台细节，可以使用提供功能，仅在特定平台上，而无需自定义呈现器或效应。

## <a name="pluginspluginsmd"></a>[插件](plugins.md)

有许多丰富的开放源代码插件可在 Github、 Nuget 和 Xamarin 组件应用商店，以帮助扩展 Xamarin.Forms 应用上。

## <a name="windowswindowsindexmd"></a>[Windows](windows/index.md)

Xamarin.Forms 具有四种不同类型的 Windows 项目的支持：

* Windows Phone 8 Silverlight （原始 Windows 支持的平台 Xamarin.Forms），
* Windows Phone 8.1 (WinRT)
* Windows 8.1 (WinRT) 和
* 通用 Windows 平台 (Windows 10)。

本部分介绍，以及如何将它们添加到现有 Xamarin.Forms 解决方案之间的差异。
