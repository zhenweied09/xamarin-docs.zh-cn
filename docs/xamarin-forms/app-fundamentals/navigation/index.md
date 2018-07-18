---
title: Xamarin.Forms 导航
description: 本指南介绍如何在 Xamarin.Forms 应用中执行导航。 Xamarin.Forms 提供了多种不同的页导航体验，取决于所使用的页类型。
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 202f044ebd7dd5b110b94d2aa60eeb7151150607
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994723"
---
# <a name="xamarinforms-navigation"></a>Xamarin.Forms 导航

_Xamarin.Forms 提供了多种不同的页导航体验，取决于所使用的页类型。_

![](images/page-types.png "Xamarin.Forms 页类型")

## <a name="hierarchical-navigationhierarchicalmd"></a>[分层导航](hierarchical.md)

[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 类提供分层导航体验，用户可以随心所欲地向前或向后导航页面。 此类将导航实现为 [`Page`](xref:Xamarin.Forms.Page) 对象的后进先出 (LIFO) 堆栈。

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

Xamarin.Forms [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)包含具有内容载入的详细信息区域的每个选项卡的选项卡和较大的详细信息区域的列表。

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

Xamarin.Forms [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)是一个页面，用户可以向从左到右轻扫来导航页面的内容，例如库。

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

Xamarin.Forms [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)是可管理两个页面的相关信息 – 主页面，其中的项，并显示主页面的项目的详细信息的详细信息页面的页面。

## <a name="modal-pagesmodalmd"></a>[模式页](modal.md)

Xamarin.Forms 还提供支持模式页面。 模式页面鼓励用户完成独立任务，在完成或取消该任务之前，不允许导航离开该任务。

## <a name="displaying-pop-upspop-upsmd"></a>[显示弹出窗口](pop-ups.md)

Xamarin.Forms 提供了两个弹出注册类似于用户界面元素： 警报和操作工作表。 要求用户简单的问题和来指导用户完成任务，可以使用这些界面元素。
