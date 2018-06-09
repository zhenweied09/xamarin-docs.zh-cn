---
title: Xamarin.Forms 中的动画
description: Xamarin.Forms 包括其自己非常简单用于创建简单的动画时也是足够通用，若要创建复杂的动画的动画基础结构。
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 5bc04f638168a10266c20e278481fc0c513afe48
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245021"
---
# <a name="animation-in-xamarinforms"></a>Xamarin.Forms 中的动画

_Xamarin.Forms 包括其自己非常简单用于创建简单的动画时也是足够通用，若要创建复杂的动画的动画基础结构。_

Xamarin.Forms 动画类针对不同的可视元素的属性的渐进式属性从一个值更改到另一段时间内的一个典型的动画。 请注意，Xamarin.Forms 动画类没有 XAML 接口。 但是，可以归纳动画[行为](~/xamarin-forms/app-fundamentals/behaviors/index.md)，然后从 XAML 引用。

## <a name="simple-animationssimplemd"></a>[简单动画](simple.md)

[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)类提供扩展方法可用于构造旋转、 缩放、 平移和淡入淡出的简单动画[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)实例。 本文演示如何创建和取消使用动画`ViewExtensions`类。

## <a name="easing-functionseasingmd"></a>[缓动函数](easing.md)

Xamarin.Forms 包括[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)类，该类允许你指定传输函数，用于控制如何动画加快或减慢，因为它们正在运行。 本文演示如何使用预定义的缓动函数，以及如何创建自定义缓动函数。

## <a name="custom-animationscustommd"></a>[自定义动画](custom.md)

[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)类是所有 Xamarin.Forms 动画，与中的扩展方法的构建块[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)类创建一个或多个`Animation`对象。 本文演示如何使用`Animation`类创建和取消动画、 同步多个动画，然后创建自定义不由现有动画方法进行动画处理的属性进行动画处理的动画。
