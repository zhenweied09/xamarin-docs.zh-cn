---
title: Xamarin.Forms 布局
description: Xamarin.Forms 布局用于组合成 visual 结构的用户界面控件。
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 0a3164dc0da7534e6bffc011ad2fdde894d6c74a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-layouts"></a>Xamarin.Forms 布局

_Xamarin.Forms 布局用于组合成 visual 结构的用户界面控件。_

[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout)和[ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) Xamarin.Forms 中的类是作为容器的视图和其他布局视图的专用子类型。 `Layout`类本身派生自[ `View` ](views.md)。 A`Layout`派生通常包含逻辑，以便在 Xamarin.Forms 应用程序中设置的位置和子元素的大小。

 [ ![](layouts-images/layouts-sml.png "Xamarin.Forms 布局类型")](layouts-images/layouts.png#lightbox "Xamarin.Forms 布局类型")

派生自的类`Layout`可以划分为两个类别：

## <a name="layouts-with-single-content"></a>具有单个内容布局

这些类派生自[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)，后者定义了[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)和[ `IsClippedToBounds` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.IsClippedToBounds/)属性。

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) 包含设置的单个子[ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/)属性。 `Content`属性可以设置为任何`View`派生，包括其他`Layout`衍生产品。 `ContentView` 主要用作一种结构元素和用作基类到[ `Frame` ](#frame)。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) | [![ContentView 示例](layouts-images/ContentView.png "ContentView 示例")](layouts-images/ContentView-Large.png#lightbox "ContentView 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Frame

|     |     |
| --- | --- |
| [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/)类派生自[ `ContentView` ](#contentView)并显示其子级矩形边框。 `Frame` 具有一个默认[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)值为 20，并还定义[ `OutlineColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.OutlineColor/)， [ `CornerRadius` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.CornerRadius/)，和[ `HasShadow` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.HasShadow/)属性。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/) | [![帧示例](layouts-images/Frame.png "帧示例")](layouts-images/Frame-Large.png#lightbox "框架示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) 它能够滚动其内容。 设置[ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Content/)属性设置为适合于屏幕查看或布局太大。 (的内容`ScrollView`非常通常[ `StackLayout` ](#stackLayout)。)设置[ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Orientation/)属性以指示是否滚动应为垂直，和 / 或水平。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) / [指南](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![ScrollView 示例](layouts-images/ScrollView.png "ScrollView 示例")](layouts-images/ScrollView-Large.png#lightbox "ScrollView 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/) 显示与控件模板的内容，而且是类的基类[ `ContentView` ](#contentView)。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/) / [指南](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![TemplatedView 示例](layouts-images/TemplatedView.png "TemplatedView 示例")](layouts-images/TemplatedView.png#lightbox "TemplatedView 示例") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) 为布局管理器中使用的模板化视图[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)标记显示内容的显示位置。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) / [指南](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![ContentPresenter 示例](layouts-images/ContentPresenter.png "ContentPresenter 示例")](layouts-images/ContentPresenter.png#lightbox "ContentPresenter 示例") |
|     |     |

## <a name="layouts-with-multiple-children"></a>具有多个子级的布局

这些类派生自[ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)。

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 在水平或垂直取决于堆栈中定位子元素[ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/)属性。 [ `Spacing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Spacing/)属性控制子级，之间的间距和的默认值为 6。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) / [指南](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)| [![StackLayout 示例](layouts-images/StackLayout.png "StackLayout 示例")](layouts-images/StackLayout-Large.png#lightbox "StackLayout 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) / [XAML 页]((https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml)) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grid

|     |     |
| --- | --- |
| [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) 在行和列的网格中定位及其子元素。 指示子表位置[附加属性](~/xamarin-forms/xaml/attached-properties.md) [ `Row` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowProperty/)， [ `Column` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnProperty/)， [ `RowSpan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowSpanProperty/)，和[ `ColumnSpan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnSpanProperty/)。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) / [指南](~/xamarin-forms/user-interface/layouts/grid.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![网格示例](layouts-images/Grid.png "网格示例")](layouts-images/Grid-Large.png#lightbox "网格示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) / [XAML 页]((https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml)) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) 在相对于其父级的特定位置定位子元素。 指示子表位置[附加属性](~/xamarin-forms/xaml/attached-properties.md) [ `LayoutBounds` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty/)和[ `LayoutFlags` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty/)。 `AbsoluteLayout`可用于进行动画处理的视图的位置。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) / [指南](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![AbsoluteLayout 示例](layouts-images/AbsoluteLayout.png "AbsoluteLayout 示例")](layouts-images/AbsoluteLayout-Large.png#lightbox "AbsoluteLayout 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayout.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayout.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/) 定位子元素相对于`RelativeLayout`本身或其同级。 指示子表位置[附加属性](~/xamarin-forms/xaml/attached-properties.md)，设置指向类型的对象[ `Constraint` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/)和[ `BoundsConstraint` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/)。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)/ [指南](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![RelativeLayout 示例](layouts-images/RelativeLayout.png "RelativeLayout 示例")](layouts-images/RelativeLayout-Large.png#lightbox "RelativeLayout 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) / [XAML 页]((https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml)) |
|     |     |

## <a name="related-links"></a>相关链接

- [Xamarin.Forms 简介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms FormsGallery 示例](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms API 文档](https://developer.xamarin.com/api/root/Xamarin.Forms/)
