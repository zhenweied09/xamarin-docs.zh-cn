---
title: SkiaSharp 行和路径
description: 使用 SkiaSharp 绘制行和图形路径
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: f7c0fcc87253b1d5ae9d612c52ef3a32c5790c5e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="skiasharp-lines-and-paths"></a>SkiaSharp 行和路径

_使用 SkiaSharp 绘制行和图形路径_

[上一节](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)演示，SkiaSharp`SKCanvas`类包括几种方法来绘制矩形、 椭圆和圆角的矩形。 此部分和更高版本的各节涵盖创建和呈现连接的各个类*图形路径*。

图形路径是绘制直线和曲线中 SkiaSharp 最通用的方法。 本部分介绍如何使用`SKPath`对象绘制的直线，而是使用小的直线的集合 (称为*折线*) 绘制可以在数学上定义的曲线。 更高版本的部分将讨论各种类型的支持的曲线`SKPath`。

本部分中的所有示例程序都显示在标题下**行和路径**的主页中[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程序，然后在[**路径**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Paths)的解决方案的文件夹。

## <a name="lines-and-stroke-capslinesmd"></a>[线和笔划大写字母](lines.md)

了解如何使用 SkiaSharp 绘制具有不同的描边顶端的行。

## <a name="path-basicspathsmd"></a>[路径基础知识](paths.md)

浏览组合直线和曲线的 SkiaSharp SKPath 对象。

## <a name="the-path-fill-typesfill-typesmd"></a>[路径填充类型](fill-types.md)

发现可能具有 SkiaSharp 路径填充类型不同的效果。

## <a name="polylines-and-parametric-equationspolylinesmd"></a>[折线和参数等式](polylines.md)

使用 SkiaSharp 来呈现可以定义为上面带有参数的公式任何行。

## <a name="dots-and-dashesdotsmd"></a>[点和短划线](dots.md)

主 SkiaSharp 中的绘图线和虚线的复杂性。

## <a name="finger-paintingfinger-paintmd"></a>[手指绘图](finger-paint.md)

使用您的手指在画布上绘制。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
