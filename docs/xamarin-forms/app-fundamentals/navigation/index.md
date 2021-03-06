---
title: Xamarin.Forms 导航
description: 本指南介绍如何在 Xamarin.Forms 应用中执行导航。 Xamarin.Forms 提供多种不同的页面导航体验，具体取决于所使用的页面类型。
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 202f044ebd7dd5b110b94d2aa60eeb7151150607
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994723"
---
# <a name="xamarinforms-navigation"></a>Xamarin.Forms 导航

Xamarin.Forms 提供多种不同的页面导航体验，具体取决于所使用的页面类型。

![](images/page-types.png "Xamarin.Forms 页面类型")

## <a name="hierarchical-navigationhierarchicalmd"></a>[分层导航](hierarchical.md)

[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 类提供分层导航体验，用户可以随心所欲地向前或向后导航页面。 此类将导航实现为 [`Page`](xref:Xamarin.Forms.Page) 对象的后进先出 (LIFO) 堆栈。

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 包含一系列选项卡和较大的详细信息区域，其中每个选项卡将内容加载到详细信息区域。

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

Xamarin.Forms [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 页面就像一个库，用户可从一侧轻扫到另一侧以浏览内容页面。

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

Xamarin.Forms [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 页面用于管理两个相关信息页，其中一个为显示项目的母版页，另一个为详细信息页，显示母版页上各项目的详细信息。

## <a name="modal-pagesmodalmd"></a>[模式页](modal.md)

Xamarin.Forms 还支持模式页。 模式页面鼓励用户完成独立任务，在完成或取消该任务之前，不允许导航离开该任务。

## <a name="displaying-pop-upspop-upsmd"></a>[显示弹出窗口](pop-ups.md)

Xamarin.Forms 提供了两个弹出式用户界面元素：警报和操作工作表。 这些界面元素可用于向用户询问简单问题并指导用户完成任务。
