---
title: Xamarin.Forms 单元格
description: Xamarin.Forms 单元格可以添加到 Listview 和 TableViews。 本文列出了 Xamarin.Forms 中包含的单元格。
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 0947027b43eacd0bac269ebf7a779746e0d22866
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243351"
---
# <a name="xamarinforms-cells"></a>Xamarin.Forms 单元格

_Xamarin.Forms 单元格可以添加到 Listview 和 TableViews。_

A*单元格*是一个专用的元素，用于在表中的项，描述列表中的每个项的呈现方式。 [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/)类派生自[ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/)，从中[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/)还派生。 单元格本身不是一个可见元素;它是用于创建可视元素的模板。

`Cell` 以独占方式与使用[ `ListView` ](views.md#listView)和[ `TableView` ](views.md#tableView)控件。 若要了解如何使用和自定义单元格，请参阅[ `ListView` ](~/xamarin-forms/user-interface/listview/index.md)和[ `TableView` ](~/xamarin-forms/user-interface/tableview.md)文档。

## <a name="cells"></a>单元格

Xamarin.Forms 还支持以下的单元格类型：

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| A [ `TextCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell)显示一个或两个文本字符串。 设置[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Text/)属性和 （可选） [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Detail/)到这些文本字符串的属性。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell) / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![TextCell 示例](cells-images/TextCell.png "TextCell 示例")](cells-images/TextCell-Large.png#lightbox "TextCell 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| [ `ImageCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell)显示相同的信息[ `TextCell` ](#textCell)但包括与设置的位图[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/)属性。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell) / [指南](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![ImageCell 示例](cells-images/ImageCell.png "ImageCell 示例")](cells-images/ImageCell-Large.png#lightbox "ImageCell 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| [ `SwitchCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell)包含文本设置[ `Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.SwitchCellText/)属性和打开/关闭开关使用布尔值最初设置[ `On` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SwitchCell.On/)属性。 处理[ `OnChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.SwitchCell.OnChanged/)事件时通知`On`属性更改。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell) / [指南](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![SwitchCell 示例](cells-images/SwitchCell.png "SwitchCell 示例")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| [ `EntryCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell)定义[ `Label` ](https://developer.xamarin.com/api/property/Xamarin.Forms.EntryCell.Label/)属性，用于标识该单元格和单个行中的可编辑文本[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.EntryCell.Text/)属性。 处理[ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.EntryCell.Completed/)时用户已完成文本输入通知的事件。<br /><br />[API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell) / [指南](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![EntryCell 示例](cells-images/EntryCell.png "EntryCell 示例")](cells-images/EntryCell-Large.png#lightbox "EntryCell 示例")<br />[C# 代码，此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |


## <a name="related-links"></a>相关链接

- [Xamarin.Forms 简介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Xamarin.Forms FormsGallery 示例](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms API 文档](https://developer.xamarin.com/api/root/Xamarin.Forms/)
