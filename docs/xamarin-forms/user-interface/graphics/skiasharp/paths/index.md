---
title: SkiaSharp 线和路径
description: 此文介绍了如何使用 SkiaSharp 绘制 Xamarin.Forms 应用程序中的行和图形路径，此示例代码进行了演示。
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9febfabb7b44b1ec09abda4b352691b37565cb48
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615130"
---
# <a name="skiasharp-lines-and-paths"></a>SkiaSharp 线和路径

_使用 SkiaSharp 绘制线条和图形路径_

[上一节](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)所示的 SkiaSharp`SKCanvas`类包括几种方法来绘制矩形、 椭圆和圆角的矩形。 此部分和更高版本的部分介绍各种类连接与创建和呈现*图形路径*。

图形路径是到绘制的直线和曲线 SkiaSharp 中的最通用的方法。 本部分介绍如何使用`SKPath`对象绘制的直线，并使用一系列小直线，直线 (称为*折线*) 若要绘制的您可以通过数学方式定义曲线。 更高版本的部分将讨论各种类型的支持的曲线`SKPath`。

在本部分中的所有示例程序都显示在标题下**线和路径**中的主页[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程序，然后在[**路径**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths)解决方案的文件夹。

## <a name="lines-and-stroke-capslinesmd"></a>[线和笔划大写字母](lines.md)

了解如何使用 SkiaSharp 绘制不同笔划大写字母的线条。

## <a name="path-basicspathsmd"></a>[路径基础知识](paths.md)

了解 SkiaSharp SKPath 对象组合的直线和曲线。

## <a name="the-path-fill-typesfill-typesmd"></a>[路径填充类型](fill-types.md)

发现不同的效果可能使用 SkiaSharp 路径填充类型。

## <a name="polylines-and-parametric-equationspolylinesmd"></a>[折线和参数等式](polylines.md)

使用 SkiaSharp 来呈现可以定义与参数方程确定任何行。

## <a name="dots-and-dashesdotsmd"></a>[点和短划线](dots.md)

主中 SkiaSharp 绘制点线和虚线线条的复杂性。

## <a name="finger-paintingfinger-paintmd"></a>[手指绘图](finger-paint.md)

使用手指在画布上绘制。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
