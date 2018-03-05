---
title: "控件"
description: "Xamarin.iOS 公开 Apple 提供的所有本机用户界面对象。 它们轻松地添加到使用 iOS 设计器中，Xcode 的接口生成器的 Xamarin.iOS 应用程序或以编程方式。 无论选择哪种方法，Xamarin.iOS 公开所有用户界面对象属性和 C# 中的方法。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: d661cf873baad43a51b40fb59fecd5bc298bcac4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="controls"></a>控件

_Xamarin.iOS 公开 Apple 提供的所有本机用户界面对象。它们轻松地添加到使用 iOS 设计器中，Xcode 的接口生成器的 Xamarin.iOS 应用程序或以编程方式。无论选择哪种方法，Xamarin.iOS 公开所有用户界面对象属性和 C# 中的方法。_

本文档将介绍一些最常见的 iOS 用户界面控件以及如何使用它们。

## <a name="alertsalertsmd"></a>[警报](alerts.md)

从开始 iOS 8，UIAlertController 具有已完成的被替换的 UIActionSheet 和 UIAlertView 两种现已弃用。

## <a name="buttonsbuttonsmd"></a>[按钮](buttons.md)

UIButton 类用于表示在 iOS 屏幕中的按钮的各种不同的样式。 本部分介绍使用按钮在 iOS 中的不同选项。

## <a name="collection-viewsuicollectionviewmd"></a>[集合视图](uicollectionview.md)

中可用的集合视图`UICollectionView`类中，是在 iOS 6 中新引入使用布局在屏幕上提供多个项的概念。 向提供数据的模式`UICollectionView`创建项目并相同委派和数据源模式通常用于 iOS 开发这些项按照与之交互。

## <a name="imagesimagemd"></a>[图像](image.md)

将映像添加到你的应用程序需要两个步骤： 首先，将图像添加到你的项目;然后，添加控件和代码以在屏幕上显示它们。 请参阅[处理映像](~/ios/app-fundamentals/images-icons/index.md)文章更详细的映像在 Xamarin.iOS 中处理的覆盖率。

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[手动相机控件](intro-to-manual-camera-controls.md)

通过提供的手动相机控件`AVFoundation Framework`在 iOS 8 中，允许移动应用程序需要完全控制的 iOS 设备的照相机。 此级别细化的控制可以用于创建专业级别的照相机应用程序和通过采取静止图像或视频时调整相机的参数提供艺术家组合。

## <a name="mapsios-mapsindexmd"></a>[地图](ios-maps/index.md)

地图是所有现代移动操作系统中的共同功能。 iOS 提供映射支持本机通过映射工具包框架。 使用地图工具包应用程序可以轻松地添加丰富、 交互式的地图。 在有许多种情况下，例如添加批注，以将标记上一个代码图，位置和覆盖的任意形状的图形中，可以自定义这些映射。 Map 工具包甚至具有用于显示设备的当前位置的内置支持。

## <a name="labelslabelsmd"></a>[标签](labels.md)

`UILabel`控件用于显示单个和多行，读取纯文本。

## <a name="pickers-and-date-pickerspickermd"></a>[选取和日期选取器](picker.md)

选取器控件显示包含正在突出显示的选定值的值的可滚动列表的类似的滚轮控件。 用户旋转鼠标滚轮以选择他们希望的选项。

一个特定用户案例的选取以设置日期和/或时间。 若要为此 Apple 提供已创建自定义调用 UIDatePicker UIPickerView 类的子类。

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[进度和活动指示器](progress-activity-indicator.md)

iOS 提供两种主要方法，以指示应用程序中的进度： 活动指示器 (包括特定_网络_活动指示器) 和进度栏。

## <a name="search-barssearchbarmd"></a>[搜索栏](searchbar.md)

UISearchBar 用于搜索的值列表。 

## <a name="sliders-steppers-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[滑块、 分档器和分段的控件](slider-switch-segmented-controls.md)

滑块控件允许针对简单选择的范围内的数字值。 iOS 使用`UISwitch`为 boolean 类型的输入，可能由其他平台上的单选按钮。 分段控件是有序的方式，以允许用户与少量的选项进行交互。

## <a name="stack-viewuistackviewmd"></a>[堆栈视图](uistackview.md)

堆栈视图控件 (`UIStackView`) 利用动态响应 iOS 设备的方向和屏幕大小自动布局和大小类来管理水平或垂直，某些子视图的堆栈的电源。

## <a name="tables-and-cellstablesindexmd"></a>[表和单元格](tables/index.md)

他部分介绍用于创建和显示表的类，然后提供如何在 Xamarin.iOS 中使用它们的示例。 它将介绍对于表，自定义布局，实现编辑和 Xamarin iOS 设计器用于以可视方式设计表使用的默认外观。 有时显示显然是行 （如音乐应用程序） 和其他情况下很难识别 table 控件 （如编辑联系人应用或在消息应用程序中的对话中） 的列表。

## <a name="text-inputtext-inputmd"></a>[文本输入](text-input.md)

接受用户文本输入实现的`UITextField`单行输入和多行可编辑文本的 UITextView。 你可以将任一这些控件拖到屏幕，然后双击以设置的初始文本。

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[选项卡栏和选项卡栏控制器](creating-tabbed-applications.md)

使用 UITabBarController 类生成 iOS 应用程序使用 tab 键导航用户界面。 在本文中我们将演练如何设置的选项卡式应用程序包含若干个控制器和视图。 然后，我们将查看如何加载 UITabBarController 时它不是根控制器，如在登录屏幕后。

## <a name="web-viewsuiwebviewmd"></a>[Web 视图](uiwebview.md)

在本文中，我们将探讨每个由 Apple 提供的三个 Web 视图： `UIWebView`， `WKWebview`，和`SFSafariViewController`、 其相似性和差异，和如何使用它们。

## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
