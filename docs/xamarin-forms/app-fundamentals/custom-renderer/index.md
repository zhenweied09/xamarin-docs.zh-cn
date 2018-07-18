---
title: Xamarin.Forms 自定义呈现器
description: 自定义呈现器让开发人员重写每个平台，以自定义外观和行为的 Xamarin.Forms 控件上的本机控件呈现。
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: c7ae25688b2f8635a9a89318e0b307e58add7a5a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998733"
---
# <a name="xamarinforms-custom-renderers"></a>Xamarin.Forms 自定义呈现器

_使用目标平台，允许保留每个平台相应的外观和感觉的 Xamarin.Forms 应用程序的本机控件呈现 Xamarin.Forms 的用户界面。自定义呈现器让开发人员重写此过程以自定义外观和行为的每个平台上的 Xamarin.Forms 控件。_

## <a name="introduction-to-custom-renderersintroductionmd"></a>[自定义呈现器简介](introduction.md)

自定义呈现器为自定义外观和行为的 Xamarin.Forms 控件提供一种有效方法。 它们可用于小型样式更改或复杂的特定于平台的布局和行为自定义。 本文介绍了自定义呈现器，并概述了用于创建自定义呈现器的过程。

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[呈现器基类和本机控件](renderers.md)

每个 Xamarin.Forms 控件已创建的本机控件实例的每个平台随附的呈现器。 本文列出了呈现器和实现每个 Xamarin.Forms 页面、 布局、 视图和单元格的本机控件类。

## <a name="customizing-an-entryentrymd"></a>[自定义项](entry.md)

Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry)控件允许单个行的文本进行编辑。 本文演示如何创建自定义呈现器`Entry`控件，使开发人员能够重写其自己特定于平台的自定义的默认本机呈现。

## <a name="customizing-a-contentpagecontentpagemd"></a>[自定义 ContentPage](contentpage.md)

一个[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)是一个可显示一个视图，并占据屏幕的大部分可见元素。 本文演示如何创建自定义呈现器`ContentPage`页上，使开发人员能够重写其自己特定于平台的自定义的默认本机呈现。

## <a name="customizing-a-mapmapindexmd"></a>[自定义地图](map/index.md)

Xamarin.Forms.Maps 提供跨平台抽象，用于显示可使用本机 Api 每个平台上，以提供快速、 熟悉的映射体验的地图进行用户的映射。 本主题演示如何创建自定义呈现器为`Map`控件，使开发人员能够重写其自己特定于平台的自定义的默认本机呈现。

## <a name="customizing-a-listviewlistviewmd"></a>[自定义 ListView](listview.md)

Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView)是视图，以垂直列表的形式显示数据的集合。 本文演示如何创建自定义呈现器，用于封装特定于平台的列表控件和本机单元格的布局，允许更好地控制本机列表控制性能。

## <a name="customizing-a-viewcellviewcellmd"></a>[自定义 ViewCell](viewcell.md)

Xamarin.Forms [ `ViewCell` ](xref:Xamarin.Forms.ViewCell)是可以添加到一个单元格[ `ListView` ](xref:Xamarin.Forms.ListView)或者[ `TableView` ](xref:Xamarin.Forms.TableView)，其中包含开发人员定义的视图。 本文演示如何创建自定义呈现器`ViewCell`位于 Xamarin.Forms`ListView`控件。 这会阻止被重复调用期间的 Xamarin.Forms 布局计算`ListView`滚动。

## <a name="implementing-a-viewviewmd"></a>[实现视图](view.md)

Xamarin.Forms 自定义用户界面控件应派生自[ `View` ](xref:Xamarin.Forms.View)类，该类用于将布局和屏幕上的控件。 本文演示如何创建 Xamarin.Forms 自定义控件用于显示来自设备的摄像机的预览视频流的自定义呈现器。

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[实现 HybridWebView](hybridwebview.md)

本文演示如何创建自定义呈现器`HybridWebView`自定义控件，该示例演示了如何增强特定于平台的 web 控件，若要从 JavaScript 允许 C# 代码调用。

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[实现视频播放器](video-player/index.md)

本文介绍如何编写呈现器来实现一个自定义`VideoPlayer`可以播放视频从 web、 作为应用程序资源嵌入的视频或视频存储在用户的设备上的视频库中的控件。 演示了几种方法，包括实施方法和只读的可绑定属性。


## <a name="related-links"></a>相关链接

- [效果](~/xamarin-forms/app-fundamentals/effects/index.md)
- [自定义呈现器 （Xamarin University 视频）](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
- [自定义呈现器 （Xamarin University 视频） 示例](http://bit.ly/xf-customrenderer)
