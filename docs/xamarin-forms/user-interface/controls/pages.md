---
title: Xamarin.Forms Pages
description: "Xamarin.Forms 页表示跨平台移动应用程序屏幕。"
ms.topic: article
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 35822dbbb7d5694e7f1f0a3f35f10df404206af9
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-pages"></a>Xamarin.Forms Pages

_Xamarin.Forms 页表示跨平台移动应用程序屏幕。_

<style>.tableimg {最大宽度： 无 ！ 重要;}</style>

## <a name="pages"></a>页数

[ `Page` ](http://iosapi.xamarin.com/?link=T%3aXamarin.Forms.Page)类是占用大部分或全部屏幕，并且包含单个子一个可见元素。 A`Xamarin.Forms.Page`表示在 iOS 中的视图控制器或 Windows Phone 中的页。 在 Android 上每个页面将采用类似活动屏幕但是 Xamarin.Forms 页面是*不*活动。

 [ ![](pages-images/pages-sml.png "Xamarin.Forms 页类型")](pages-images/pages.png "Xamarin.Forms 页类型")

<br clear="all" />

Xamarin.Forms 支持：

<table align="center" border="1" cellpadding="1" cellspacing="1">
  <tr>
  <thead>
    <th>
      <strong>类型</strong>
    </th>
    <th>
      <strong>说明</strong>
    </th>
    <th style="min-width:400px">
      <strong>屏幕快照</strong>
    </th>
  </thead></tr>
  <tbody>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a>
    </td>
    <td align="center" valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">内容页</a>显示单个<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">视图</a>，通常类似于容器<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/">StackLayout</a>或<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/">ScrollView</a>。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ContentPageDemoPage.cs"><img src="pages-images/ContentPage.png" title="内容页示例" class="tableimg">
    </a></td>
  </tr><tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/">MasterDetailPage</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">页</a>管理的信息的两个窗格。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/MasterDetailPageDemoPage.cs"><img src="pages-images/MasterDetailPage.png" title="MasterDetailPage 示例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/">NavigationPage</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">页</a>管理导航和其他页面一堆的用户体验。  
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/NavigationPageDemoPage.cs"><img src="pages-images/NavigationPage.png" title="NavigationPage 示例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/">TabbedPage</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">页</a>这样子级之间的导航页，请使用选项卡。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TabbedPageDemoPage.cs"><img src="pages-images/TabbedPage.png" title="TabbedPage 示例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/">TemplatedPage</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">页</a>显示使用控件模板，和类的基类的全屏幕内容<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">内容页</a>。
    </td>
    <td valign="top">
    <a href="https://github.com/xamarin/xamarin-forms-samples/tree/master/Templates/ControlTemplates/"><img src="pages-images/TemplatedPage.png" title="TemplatedPage 示例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/">CarouselPage</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">页</a>允许子页，如库之间的滑动手势。
    </td>
    <td valign="top">
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CarouselPageDemoPage.cs"><img src="pages-images/CarouselPage.png" title="CarouselPage 示例" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>相关链接

- [Xamarin.Forms 简介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms 库 （示例）](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Xamarin.Forms API 文档](http://iosapi.xamarin.com/?link=N%3aXamarin.Forms)
