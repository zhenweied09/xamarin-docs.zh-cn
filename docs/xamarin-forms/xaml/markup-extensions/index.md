---
title: XAML 标记扩展
description: 扩展属性设置从的 XAML 源的范围
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: b81bc4b31edd1d8b8f5f43f97885c38e889dd32c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xaml-markup-extensions"></a>XAML 标记扩展

XAML 标记扩展帮助通过允许要从文本字符串以外的源设置的元素属性扩展的功能和灵活性的 XAML。

例如，通常设置`Color`属性`BoxView`如下所示：

```xaml
<BoxView Color="Blue" />
```

或者，你可以将其设置为十六进制的 RGB 颜色值：

```xaml
<BoxView Color="#FF0080" />
```

在任一情况下，文本字符串设置为`Color`属性转换为`Color`值[ `ColorTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColorTypeConverter/)类。

您可能希望改为设置`Color`属性中使用的资源字典存储的值从你已创建的类的静态属性的值或从类型的属性`Color`在页上，另一个元素或从构造分隔色调、 饱和度和亮度值。

所有这些选项都可能使用 XAML 标记扩展。 但不允许短语"标记扩展"让你： XAML 标记扩展是*不*to XML 扩展。 即使使用 XAML 标记扩展，XAML 始终是合法的 XML。 

标记扩展的实际上只是不同的方式来表示一个元素的属性。 XAML 标记扩展是通常可以通过将括在大括号中的属性设置识别：

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

在大括号中的任何属性设置为*始终*XAML 标记扩展。 但是，如上所示，将 XAML 标记扩展也可以引用而不使用大括号。

本文分为两个部分：

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[使用 XAML 标记扩展](consuming.md)  

使用 Xamarin.Forms 中定义的 XAML 标记扩展。

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[创建 XAML 标记扩展](creating.md) 

编写你自己自定义的 XAML 标记扩展。



## <a name="related-links"></a>相关链接

- [标记扩展 （示例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [从 Xamarin.Forms 簿 XAML 标记扩展章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [动态样式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
