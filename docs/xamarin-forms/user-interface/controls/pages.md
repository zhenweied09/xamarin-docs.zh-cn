---
title: Xamarin.Forms Pages
description: Xamarin.Forms 页表示跨平台移动应用程序屏幕。
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: bc1345bfaaf02464e2cf1ea0b3aceb28eb91e1d4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-pages"></a>Xamarin.Forms Pages

_Xamarin.Forms 页表示跨平台移动应用程序屏幕。_

如下所述的所有页类型都派生 Xamarin.Forms [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)类。 这些可视元素占用所有或大多数屏幕。 A`Page`对象所表示`ViewController`在 iOS 中和`Page`在通用 Windows 平台。 在 Android 上，每个页面采用如屏幕`Activity`，但是 Xamarin.Forms 页面是*不*`Activity`对象。

[ ![](pages-images/pages-sml.png "Xamarin.Forms 页类型")](pages-images/pages.png#lightbox "Xamarin.Forms 页类型")

## <a name="pages"></a>页数

Xamarin.Forms 还支持以下的页类型：

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     | 
| --- | --- | 
| [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 是页面的最简单也是最常见类型。 设置[ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentPage.Content/)到单个属性[ `View` ](views.md)对象，它是最常[ `Layout` ](layouts.md)如[ `StackLayout`](layouts.md#stackLayout)， [ `Grid` ](layouts.md#grid)，或[ `ScrollView` ](layouts.md#scrollView)。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) | [![内容页示例](pages-images/ContentPage.png "内容页示例")](pages-images/ContentPage-Large.png#lightbox "内容页示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     | 
| --- | --- | 
| A [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)管理的信息的两个窗格。 设置[ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)到页通常显示为列表或菜单的属性。 设置[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)到页上显示母版页中的某个选定的项的属性。 [ `IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/)属性控制 master 或详细信息页上是否可见。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) / [指南](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [示例](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/) | [![MasterDetailPage 示例](pages-images/MasterDetailPage.png "MasterDetailPage 示例")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml)与[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     | 
| --- | --- | 
| [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)管理在使用基于堆栈的体系结构的其他页面之间导航。 主页上的实例时应用程序中使用页导航，应传递给的构造函数`NavigationPage`对象。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) / [指南](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [示例 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)， [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)，和[3](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)  | [![NavigationPage 示例](pages-images/NavigationPage.png "NavigationPage 示例")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml)与[代码后面 =](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     | 
| --- | --- | 
| [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) 派生自抽象[ `MultiPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/)类，并允许使用选项卡页的各子之间的导航。 设置[ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/)属性页或集的集合[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/)到的数据对象的集合的属性和[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/)属性设置为[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)描述每个对象的直观表示方式。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) / [指南](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [示例 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)和[2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage) | [![TabbedPage 示例](pages-images/TabbedPage.png "TabbedPage 示例")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     | 
| --- | --- | 
| [`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) 派生自抽象[ `MultiPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/)类，并允许通过手指轻扫页的各子之间的导航。 设置[ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/)属性的集合[ `ContentPage` ](#contentPage)对象或一组[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/)到的数据对象的集合的属性和[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/)属性[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)描述每个对象的直观表示方式。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) / [指南](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [示例 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)和[2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/) | [![CarouselPage 示例](pages-images/CarouselPage.png "CarouselPage 示例")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     | 
| --- | --- | 
| [`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/) 显示使用控件模板，全屏幕内容，而且是类的基类[ `ContentPage` ](#contentPage)。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/) / [指南](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![TemplatedPage 示例](pages-images/TemplatedPage.png "TemplatedPage 示例")](pages-images/TemplatedPage.png "TemplatedPage 示例") |
|     |     |

## <a name="related-links"></a>相关链接

- [Xamarin.Forms 简介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms FormsGallery 示例](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms API 文档](https://developer.xamarin.com/api/root/Xamarin.Forms/)
