---
title: Xamarin.Forms 导航
description: 本指南说明如何在 Xamarin.Forms 应用中执行导航。 Xamarin.Forms 提供了多种不同的页导航体验，根据正在使用的页类型。
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 90aedee42af7ed1788110e832fb3b435d870ee77
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241951"
---
# <a name="xamarinforms-navigation"></a>Xamarin.Forms 导航

_Xamarin.Forms 提供了多种不同的页导航体验，根据正在使用的页类型。_

![](images/page-types.png "Xamarin.Forms 页类型")

## <a name="hierarchical-navigationhierarchicalmd"></a>[分层导航](hierarchical.md)

[`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 类提供分层导航体验，用户可以随心所欲地向前或向后导航页面。 此类将导航实现为 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 对象的后进先出 (LIFO) 堆栈。

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

Xamarin.Forms [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)的选项卡和更大的详细信息区域，列表包含带有加载到详细信息区域的内容每个选项卡。

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

Xamarin.Forms [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)是一个页，用户可以将从左到右轻扫网页的内容，如库间导航。

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

Xamarin.Forms [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)是管理两个页的相关信息 – 主控页显示项和在主页上显示项目的详细信息的详细信息页的页。

## <a name="modal-pagesmodalmd"></a>[模式页](modal.md)

Xamarin.Forms 还提供对模式的页的支持。 模式页面鼓励用户完成独立任务，在完成或取消该任务之前，不允许导航离开该任务。

## <a name="displaying-pop-upspop-upsmd"></a>[显示弹出窗口](pop-ups.md)

Xamarin.Forms 提供了两个高类似 pop 的用户界面元素： 警报和操作表。 可以使用这些界面元素，询问简单的问题的用户并以指导用户完成任务。
