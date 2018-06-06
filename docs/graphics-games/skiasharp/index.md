---
title: 与 SkiaSharp 2D 绘图
description: 本文档提供跨平台 2D 的概述使用 SkiaSharp 进行绘制。 它链接到各种指南描述 SkiaSharp 和其各种 Api。
ms.prod: xamarin
ms.assetid: A8A61421-4544-422A-A7E0-9355C67DF21E
author: charlespetzold
ms.author: chape
ms.date: 09/14/2017
ms.openlocfilehash: 962fe657f25976f9b5069f2d434e92f816d249ca
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783281"
---
# <a name="2d-drawing-with-skiasharp"></a>与 SkiaSharp 2D 绘图

SkiaSharp 为执行二维图形提供功能强大的 C# API。 通过打开[Google Skia 库](http://skia.org)，同一个库为提供技术支持 Google Chrome、 Firefox 和 Android 的图形堆栈。

[![](images/ide-sml.png "SkiaSharp 提供了一个功能强大的 C# API，用于执行二维图形")](images/ide.png#lightbox)

SkiaSharp 是可移植库，并作为方便地提供[跨平台 NuGet 包](https://www.nuget.org/packages/SkiaSharp)，并支持以下平台现成： macOS，Xamarin.Android、 Xamarin.iOS 和 Windows 桌面。

## <a name="introduction-to-skiasharpgraphics-gamesskiasharpintroductionmd"></a>[SkiaSharp 简介](~/graphics-games/skiasharp/introduction.md)

SkiaSharp 和示例的核心概念概述代码来呈现图形、 文本、 位图和使用映像筛选器。

## <a name="skiasharp-tutorials-for-xamarinformsxamarin-formsuser-interfacegraphicsskiasharpindexmd"></a>[有关 Xamarin.Forms SkiaSharp 教程](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)

了解如何使用跨平台呈现 Xamarin.Forms 中的图形：

- [绘制基础知识](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)
  * [绘制一个简单的圆形](~/xamarin-forms/user-interface/graphics/skiasharp/basics/circle.md)
  * [与 Xamarin.Forms 集成](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)
  * [像素和设备无关的单位](~/xamarin-forms/user-interface/graphics/skiasharp/basics/pixels.md)
  * [基本动画](~/xamarin-forms/user-interface/graphics/skiasharp/basics/animation.md)
  * [将文本和图形集成](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)
  * [位图基础知识](~/xamarin-forms/user-interface/graphics/skiasharp/basics/bitmaps.md)
- [行和路径](~/xamarin-forms/user-interface/graphics/skiasharp/paths/index.md)
  * [行和描边大写字母](~/xamarin-forms/user-interface/graphics/skiasharp/paths/lines.md)
  * [路径基础知识](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)
  * [路径的填充类型](~/xamarin-forms/user-interface/graphics/skiasharp/paths/fill-types.md)
  * [折线和参数的等式](~/xamarin-forms/user-interface/graphics/skiasharp/paths/polylines.md)
  * [点和短划线](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)
  * [手指绘图](~/xamarin-forms/user-interface/graphics/skiasharp/paths/finger-paint.md)
- [转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/index.md)
  * [转换变换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)
  * [缩放变换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/scale.md)
  * [旋转变换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)
  * [时间差转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/skew.md)
  * [矩阵转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md)
  * [触摸操作](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)
  * [非仿射转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)
  * [三维旋转](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)
- [曲线和路径](~/xamarin-forms/user-interface/graphics/skiasharp/curves/index.md)
  * [绘制弧线的三个方法](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)
  * [贝塞尔曲线的三种类型](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)
  * [SVG 路径数据](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)
  * [按路径和区域进行剪裁](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md)
  * [路径效果](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)
  * [路径和文本](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)
  * [路径信息和枚举](~/xamarin-forms/user-interface/graphics/skiasharp/curves/information.md)

## <a name="platform-specific-notesgraphics-gamesskiasharpplatformmd"></a>[平台特定说明](~/graphics-games/skiasharp/platform.md)

本页介绍在包括 iOS、 Android、 macOS 和 Windows 的不同平台上 SkiaSharp 的设置说明。

## <a name="api-documentationhttpsdeveloperxamarincomapinamespaceskiasharp"></a>[API 文档](https://developer.xamarin.com/api/namespace/SkiaSharp/)

你可以浏览[API 文档](https://developer.xamarin.com/api/namespace/SkiaSharp/)为 SkiaSharp 在我们的 web 站点上。

## <a name="work-in-progress"></a>正在进行的工作

SkiaSharp 是我们正在与我们的社区共享工作正在进行。 而我们具有绑定 Skia API 的重要部分，将剩余工作量进行。 我们使用 Skia，显示的稳定 C API，并且我们计划将继续参与到 Skia 来提供完整的覆盖范围提供给 Api 的 C 绑定我们工作。

若要帮助我们指南我们绑定工作，请为问题 GitHub 存储库上发表意见或建议[ http://github.com/mono/SkiaSharp ](http://github.com/mono/SkiaSharp)。
