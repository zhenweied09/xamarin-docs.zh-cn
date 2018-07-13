---
title: Xamarin.Forms 数据模板
description: DataTemplate 用于支持的控件上指定数据的外观并通常将绑定到数据显示。
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 771ae22c3e28a4fce758bbfd6a3bd63bafb75e53
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994966"
---
# <a name="xamarinforms-data-templates"></a>Xamarin.Forms 数据模板

_DataTemplate 用于支持的控件上指定数据的外观并通常将绑定到数据显示。_

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

Xamarin.Forms 数据模板提供支持的控件上定义数据的表示形式的功能。 本文介绍数据模板，检查它们是必需的原因。

## <a name="creating-a-datatemplatecreatingmd"></a>[创建 DataTemplate](creating.md)

数据模板可以在中创建内联[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，或从自定义类型或适当的 Xamarin.Forms 单元格类型。 如果不需要重复利用其他位置的数据模板，应使用内联模板。 或者，可以通过定义它作为自定义的类型，或作为控件级别页级别或应用程序级别资源重复使用数据模板。

## <a name="creating-a-datatemplateselectorselectormd"></a>[创建 DataTemplateSelector](selector.md)

一个[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)可用于选择[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)在运行时根据数据绑定属性的值。 这使多个`DataTemplate`实例应用于相同类型的对象，若要自定义的特定对象的外观。 本文演示了如何创建和使用`DataTemplateSelector`。


## <a name="related-links"></a>相关链接

- [数据模板 （示例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
