---
title: Xamarin.Forms 中的缓动函数
description: Xamarin.Forms 中包括允许你指定的传输函数，用于控制如何动画加快或减慢，因为它们正在运行一个缓动类。 本文演示如何使用预定义的缓动函数，以及如何创建自定义缓动函数。
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 9398a1b9cf4e5f6fd18f2213a7cf55e9cbb93ef0
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243137"
---
# <a name="easing-functions-in-xamarinforms"></a>Xamarin.Forms 中的缓动函数

_Xamarin.Forms 中包括允许你指定的传输函数，用于控制如何动画加快或减慢，因为它们正在运行一个缓动类。本文演示如何使用预定义的缓动函数，以及如何创建自定义缓动函数。_


[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)类定义了多个可供动画的缓动函数：

- [ `BounceIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceIn/)缓动函数退回开头动画。
- [ `BounceOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceOut/)缓动函数退回末尾动画。
- [ `CubicIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicIn/)渐变缓动函数加快动画。
- [ `CubicInOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicInOut/)加速开始时，动画和减速末尾动画的缓动函数。
- [ `CubicOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicOut/)快速缓动函数减速的动画。
- [ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/)缓动函数使用常量的速度，并为默认值缓动函数。
- [ `SinIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinIn/)顺利缓动函数加快动画。
- [ `SinInOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinInOut/)顺利缓动函数加快开始时，动画并顺利减速末尾动画。
- [ `SinOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinOut/)顺利缓动函数减速的动画。
- [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/)缓动函数使动画结束时非常快速地加速。
- [ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/)缓动函数使动画快速减速结束时。

`In`和`Out`后缀指示是否动画，在结束时，或同时开头明显缓动函数提供的效果。

此外，可以创建自定义缓动函数。 有关详细信息，请参阅[自定义缓动函数](#customeasing)。

## <a name="consuming-an-easing-function"></a>使用缓动函数

中的动画扩展方法[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)类允许缓动函数指定为最终方法参数，如下面的代码示例中所示：

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

通过指定的动画的缓动函数，动画速度变得非线性，并生成缓动函数提供的效果。 创建动画时省略缓动函数使动画使用默认[ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/)缓动函数，这样会生成线性速度。

有关使用中的动画扩展方法的详细信息[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)类，请参阅[简单动画](~/xamarin-forms/user-interface/animation/simple.md)。 缓动函数还可供[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)类。 有关详细信息，请参阅[自定义动画](~/xamarin-forms/user-interface/animation/custom.md)。

<a name="customeasing" />

## <a name="custom-easing-functions"></a>自定义缓动函数

有三个主要方法来创建自定义缓动函数实现：

1. 创建一个带方法`double`自变量，并返回`double`结果。
1. 创建 `Func<double, double>`。
1. 指定为参数的缓动函数[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)构造函数。

在所有三种情况下，自定义缓动函数应返回 0 0 和 1 的自变量 1 的自变量。 但是，任何值可以返回自变量的值为 0 和 1 之间。 现在将依次介绍每种方法。

### <a name="custom-easing-method"></a>自定义缓动方法

自定义缓动函数可以定义为采用的方法`double`自变量，并返回`double`结果，如下面的代码示例中所示：

```csharp
await image.TranslateTo(0, 200, 2000, CustomEase);

double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}
```

`CustomEase`方法截断到 0、 0.2，0.4，0.6、 0.8 和 1 的值传入的值。 因此， [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)中离散跳转，而不是平稳转换实例。

### <a name="custom-easing-func"></a>自定义缓动 Func

自定义缓动函数也可以定义为`Func<double, double>`，如下面的代码示例所示：

```csharp
Func<double, double> CustomEase = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEase));
```

`CustomEase` `Func`表示一个缓动函数，启动关闭快速、 降低和反转过程中，然后反转课程试以加快快速结束时。 因此，虽然的总体移动[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)实例向下，它也会暂时反转过程中途动画。

### <a name="custom-easing-constructor"></a>自定义缓动构造函数

也可为的参数定义自定义缓动函数[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)构造函数，如下面的代码示例中所示：

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

自定义缓动函数指定的 lambda 函数参数为[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)构造函数，并使用`Math.Cos`方法来创建阻碍通过慢速放置效果`Math.Exp`方法。 因此， [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)实例会转换，以便看起来似乎是放到其最终的放置位置。

## <a name="summary"></a>总结

本文演示如何使用预定义的缓动函数，以及如何创建自定义缓动函数。 Xamarin.Forms 包括[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)类，该类允许你指定传输函数，用于控制如何动画加快或减慢，因为它们正在运行。



## <a name="related-links"></a>相关链接

- [异步支持概述](~/cross-platform/platform/async.md)
- [缓动函数 （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/easing/)
- [缓动](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)
- [ViewExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)
