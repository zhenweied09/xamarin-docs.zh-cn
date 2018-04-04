---
title: 选取器
description: 选取器视图是用于从数据的列表中选择文本项的控件。
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 9889502b635997dbb5e2b79a7654bf1ff0c99861
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="picker"></a>选取器

_选取器视图是用于从数据的列表中选择文本项的控件。_

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)显示的项，用户可以从中选择的短列表。 但是， [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)首次显示时不显示任何数据。 相反，值其[ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/)属性显示为在 iOS 和 Android 平台上的占位符：

[![](images/picker-initial.png "初始选取器显示")](images/picker-initial-large.png#lightbox "初始选取器显示")

当[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)显示提升焦点，其数据和用户可以选择的项：

[![](images/picker-selection.png "选取器中选择项")](images/picker-selection-large.png#lightbox "选取器中选择项")

以下所选内容，所选的项显示通过[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/):

![](images/picker-after-selection.png "选定内容后选取器")

有两种技术来填充[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)数据：

- 设置[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)属性设置为要显示的数据。 这是推荐采用的方法，Xamarin.Forms 2.3.4 中引入。 有关详细信息，请参阅[设置选取器的 ItemsSource 属性](populating-itemssource.md)。
- 添加要向显示的数据[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合。 此方法已填充的原始过程[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)使用数据。 有关详细信息，请参阅[将数据添加到了选取器的项集合](populating-items.md)。


## <a name="related-links"></a>相关链接

- [选取器](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
