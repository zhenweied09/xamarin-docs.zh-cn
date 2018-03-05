---
title: "自定义呈现器"
description: "使用目标平台，允许 Xamarin.Forms 应用程序保留了适当的外观和感觉，为每个平台的本机控件呈现 Xamarin.Forms 用户界面。 自定义呈现器，开发人员可以重写此过程以自定义外观和每个平台上的 Xamarin.Forms 控件的行为。"
ms.topic: article
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: a92da0320addf1569c25ed05873aa11a198b1daa
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="custom-renderers"></a>自定义呈现器

_使用目标平台，允许 Xamarin.Forms 应用程序保留了适当的外观和感觉，为每个平台的本机控件呈现 Xamarin.Forms 用户界面。自定义呈现器，开发人员可以重写此过程以自定义外观和每个平台上的 Xamarin.Forms 控件的行为。_

## <a name="introduction-to-custom-renderersintroductionmd"></a>[自定义呈现器简介](introduction.md)

自定义呈现器提供了一个功能强大的方法，用于自定义外观和 Xamarin.Forms 控件的行为。 它们可以用于小样式更改或复杂的特定于平台的布局和行为自定义项。 本文介绍自定义呈现器，并概述了用于创建自定义呈现器的过程。

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[呈现器基类，这些类和本机控件](renderers.md)

Xamarin.Forms 中的每个控件具有随附的呈现器针对每个平台创建的本机控件的实例。 本文列出的呈现器和实现每个 Xamarin.Forms 页、 布局、 视图和单元格的本机控件类。

## <a name="customizing-an-entryentrymd"></a>[自定义项](entry.md)

Xamarin.Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件允许单个行要编辑的文本。 本文演示如何创建自定义呈现器`Entry`控件，使开发人员能够重写默认本机呈现与自己特定于平台的自定义项。

## <a name="customizing-a-contentpagecontentpagemd"></a>[自定义 ContentPage](contentpage.md)

A [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)是一种显示单个视图，且占用大部分屏幕的可视元素。 本文演示如何创建自定义呈现器`ContentPage`页上，使开发人员能够重写默认本机呈现与自己特定于平台的自定义项。

## <a name="customizing-a-mapmapindexmd"></a>[自定义地图](map/index.md)

Xamarin.Forms.Maps 提供跨平台抽象显示为用户可使用每个平台上的 Api 以提供快速且熟悉映射体验本机地图的地图。 本主题演示如何创建自定义呈现器为`Map`控件，使开发人员能够重写默认本机呈现与自己特定于平台的自定义项。

## <a name="customizing-a-listviewlistviewmd"></a>[自定义 ListView](listview.md)

Xamarin.Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)是作为垂直列表中显示的数据集合的视图。 本文演示如何创建自定义呈现器，用于封装特定于平台的列表控件和本机单元格的布局，允许更好地控制本机列表控件的性能。

## <a name="customizing-a-viewcellviewcellmd"></a>[自定义 ViewCell](viewcell.md)

Xamarin.Forms [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)可以添加到的单元格[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)或[ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)，其中包含的开发人员定义的视图。 本文演示如何创建自定义呈现器`ViewCell`Xamarin.Forms 内承载`ListView`控件。 这将停止被反复调用期间的 Xamarin.Forms 布局计算`ListView`滚动。

## <a name="implementing-a-viewviewmd"></a>[实现视图](view.md)

Xamarin.Forms 自定义用户界面控件应派生自[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)类，该类用于放置布局和在屏幕上的控件。 本文演示如何创建自定义呈现器的 Xamarin.Forms 自定义控件，用于显示来自设备的照相机的预览视频流。

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[实现 HybridWebView](hybridwebview.md)

本文演示如何创建自定义呈现器`HybridWebView`自定义控件，其演示了如何增强特定于平台的 web 控件，若要从 JavaScript 中允许 C# 代码调用。

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[实现视频播放器](video-player/index.md)

这篇文章演示如何编写呈现器来实现一个自定义`VideoPlayer`可以播放从 web、 作为应用程序资源嵌入的视频或在用户的设备上的视频库中存储的视频的视频的控件。 演示了几种方法，包括实现方法和只读的可绑定属性。 


## <a name="related-links"></a>相关链接

- [效果](~/xamarin-forms/app-fundamentals/effects/index.md)
- [自定义呈现器 （Xamarin 大学视频）](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
- [自定义呈现器 （Xamarin 大学视频） 示例](http://bit.ly/xf-customrenderer)
