---
title: "Xamarin.Forms 单元格"
description: "Xamarin.Forms 单元格可以添加到 Listview 和 TableViews。"
ms.topic: article
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 509ecc509754bba544115c140e619f634bd64eae
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-cells"></a>Xamarin.Forms 单元格

_Xamarin.Forms 单元格可以添加到 Listview 和 TableViews。_

<style>.tableimg {最大宽度： 无 ！ 重要;}</style>

## <a name="cells"></a>单元格

单元格是一个专用的元素，该元素用于在表中的项，并且描述应如何绘制列表中的每个项。 单元格派生自[ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/)，从它 VisualElement 还派生。 单元格不是可视元素，但是它只描述用于创建可视元素的模板。 [`Cell`](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) 对于所有 Xamarin.Forms 单元格提供基本类和功能。 单元格都设计为要添加到元素[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)或[ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)控件。

若要了解如何使用和自定义单元格，请参阅[ListView](~/xamarin-forms/user-interface/listview/index.md)和[TableView](~/xamarin-forms/user-interface/tableview.md)文档。

<table align="center" border="1" cellpadding="1" cellspacing="1">
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
  </thead>
  <tbody>
    <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/">EntryCell</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a>与一个标签和单行文本输入字段。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EntryDemoPage.cs"><img src="cells-images/EntryCell.png" title="EntryCell 示例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/">SwitchCell</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a>标签，然后打开/关闭开关。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SwitchCellDemoPage.cs"><img src="cells-images/SwitchCell.png" title="SwitchCell 示例" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">TextCell</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a>主和辅助文本。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TextCellDemoPage.cs"><img src="cells-images/TextCell.png" title="TextCell 示例" class="tableimg">
    </a></td>
  </tr>
      <tr>
    <td>
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/">ImageCell</a>
    </td>
    <td valign="top">
A<a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">文本单元格</a>还包括映像。
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ImageCellDemoPage.cs"><img src="cells-images/ImageCell.png" title="ImageCell 示例" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>相关链接

- [Xamarin.Forms 简介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms 库 （示例）](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms API 文档](https://developer.xamarin.com/api/namespace/Xamarin.Forms/)
