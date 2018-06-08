---
title: 选取器
description: 选取器视图是用于从数据的列表中选择文本项的控件。
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 7f0050351ca28d7f8afeb82a85e82e51d399824b
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847493"
---
# <a name="picker"></a>选取器

_选取器视图是用于从数据的列表中选择文本项的控件。_

Xamarin.Forms [ `Picker` ](xref:Xamarin.Forms.Picker)显示短列表的项，用户可以从中选择的项。 `Picker` 定义八个属性：

- [`Title`](xref:Xamarin.Forms.Picker.Title) 类型的`string`，使用默认`null`。
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 类型的`IList`，源列表的项以显示，默认值为`null`。
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 类型的`int`，所选的项，默认值为-1 的索引。
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 类型的`object`，所选的项目，默认为`null`。
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) 类型的[ `Color` ](xref:Xamarin.Forms.Color)，用来显示的文本，默认为颜色[ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/)。
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) 类型的[ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes)，使用默认[ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None)。
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) 类型的`string`，使用默认`null`。
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) 类型的`double`，其默认值为-1.0。

所有八个属性由[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着它们可以风格，并且这些属性可以将数据绑定的目标。 [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)和[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)属性具有的默认绑定模式[ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)，这意味着，它们可以是数据绑定的目标在使用的应用程序[模型-视图-视图模型 (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构。 有关设置字体属性的信息，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)。

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)首次显示时不显示任何数据。 相反，值其[ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/)属性显示为在 iOS 和 Android 平台上的占位符：

[![](images/picker-initial.png "初始选取器显示")](images/picker-initial-large.png#lightbox "初始选取器显示")

当[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)显示提升焦点，其数据和用户可以选择的项：

[![](images/picker-selection.png "选取器中选择项")](images/picker-selection-large.png#lightbox "选取器中选择项")

[ `Picker` ](xref:Xamarin.Forms.Picker)激发[ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件时用户选择一个项。 以下所选内容，所选的项显示通过`Picker`:

![](images/picker-after-selection.png "选定内容后选取器")

有两种技术来填充[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)数据：

- 设置[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)属性设置为要显示的数据。 这是推荐采用的方法，Xamarin.Forms 2.3.4 中引入。 有关详细信息，请参阅[设置选取器的 ItemsSource 属性](populating-itemssource.md)。
- 添加要向显示的数据[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合。 此方法已填充的原始过程[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)使用数据。 有关详细信息，请参阅[将数据添加到了选取器的项集合](populating-items.md)。

## <a name="related-links"></a>相关链接

- [选取器](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
