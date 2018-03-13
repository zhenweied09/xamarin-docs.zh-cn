---
title: "边距和填充"
description: "边距和填充属性控制布局时的行为元素呈现用户界面中。 这篇文章演示了两个属性，以及如何设置它们之间的差别。"
ms.topic: article
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 461430ba27b5d6008338019e5feaebed7b09d4cb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="margin-and-padding"></a>边距和填充

_边距和填充属性控制布局时的行为元素呈现用户界面中。这篇文章演示了两个属性，以及如何设置它们之间的差别。_

## <a name="overview"></a>概述

边距和填充是相关的布局概念：

- [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)属性表示一个元素和其相邻元素之间的距离，用于控制元素的呈现位置和与其相邻元素的呈现位置。 `Margin` 可对指定值[布局](~/xamarin-forms/user-interface/controls/layouts.md)和[视图](~/xamarin-forms/user-interface/controls/views.md)类。
- [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)属性表示一个元素及其子元素之间的距离，并可用于将控件从其自己的内容。 `Padding` 可对指定值[布局](~/xamarin-forms/user-interface/controls/layouts.md)类。

下图阐释了这两个概念：

[![](margin-and-padding-images/margins-and-padding-sml.png "边距和填充概念")](margin-and-padding-images/margins-and-padding.png#lightbox "边距和填充概念")

请注意， [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)值相加。 因此，如果两个相邻元素指定边距为 20 像素，元素之间的距离将是 40 个像素。 此外，边距和填充是累加式的同时应用时，即元素与任何内容之间的距离将是边距和填充。

## <a name="specifying-a-thickness"></a>指定粗细

[ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)和[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)属性的类型都是[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)。 有三种可能的创建时`Thickness`结构：

- 创建[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)结构定义的单个统一值。 单个值应用于左侧、 顶部、 右侧和底部均带有的元素。
- 创建[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)水平和垂直值所定义的结构。 所需应用到左侧和右侧的元素，与所需应用于元素的顶部和底部旁边的垂直值进行水平值。
- 创建[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)由适用于在左侧、 顶部、 右侧和底部均带有的元素的四个非重复值的结构。

下面的 XAML 代码示例显示所有三种可能：

```xaml
<StackLayout Padding="0,20,0,0">
  <Label Text="Xamarin.Forms" Margin="20" />
  <Label Text="Xamarin.iOS" Margin="10, 15" />
  <Label Text="Xamarin.Android" Margin="0, 20, 15, 5" />
</StackLayout>
```

以下代码示例显示相应的 C# 代码：

```csharp
var stackLayout = new StackLayout {
  Padding = new Thickness(0,20,0,0),
  Children = {
    new Label { Text = "Xamarin.Forms", Margin = new Thickness (20) },
    new Label { Text = "Xamarin.iOS", Margin = new Thickness (10, 25) },
    new Label { Text = "Xamarin.Android", Margin = new Thickness (0, 20, 15, 5) }
  }
};
```

> [!NOTE]
> `Thickness` 通常裁剪或拉伸过度内容，值可以是负数。

## <a name="summary"></a>摘要

本文演示之间的差异[ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)和[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)属性，以及如何设置它们。 元素呈现用户界面中时，控件布局行为的属性。


## <a name="related-links"></a>相关链接

- [Margin](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)
- [填充](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)
- [粗细](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)
