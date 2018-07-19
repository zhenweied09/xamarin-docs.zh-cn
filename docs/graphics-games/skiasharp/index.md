---
title: 2D 绘制使用 SkiaSharp
description: 本文档提供跨平台 2D 的概述使用 SkiaSharp 绘制。 它链接到各种指南描述 SkiaSharp 和其各种 Api。
ms.prod: xamarin
ms.assetid: A8A61421-4544-422A-A7E0-9355C67DF21E
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: 0c8cbc14308c8c4131e5aaa2bcc0ddfa798af610
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130915"
---
# <a name="2d-drawing-with-skiasharp"></a>2D 绘制使用 SkiaSharp

SkiaSharp 进行二维图形提供了功能强大的 C# API。 由[Google 的 Skia 库](http://skia.org)，为提供支持 Google Chrome、 Firefox 和 Android 的图形堆栈的同一个库。

[![](images/ide-sml.png "SkiaSharp 提供功能强大的 C# API 进行二维图形")](images/ide.png#lightbox)

SkiaSharp 是可移植库，并附带提供方便地[跨平台 NuGet 包](https://www.nuget.org/packages/SkiaSharp)，并支持在初始状态下的以下平台： macOS、 Xamarin.Android、 Xamarin.iOS、 和 Windows 桌面。

## <a name="introduction-to-skiasharpgraphics-gamesskiasharpintroductionmd"></a>[SkiaSharp 简介](~/graphics-games/skiasharp/introduction.md)

SkiaSharp 和示例的核心概念的概述代码来呈现图形、 文本、 位图，并使用映像筛选器。

## <a name="skiasharp-tutorials-for-xamarinformsxamarin-formsuser-interfacegraphicsskiasharpindexmd"></a>[适用于 Xamarin.Forms 的 SkiaSharp 教程](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)

了解如何使用跨平台 Xamarin.Forms 中呈现的图形：

- [绘制基础知识](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)
  * [绘制一个简单的圆形](~/xamarin-forms/user-interface/graphics/skiasharp/basics/circle.md)
  * [与 Xamarin.Forms 集成](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)
  * [像素和设备无关的单位](~/xamarin-forms/user-interface/graphics/skiasharp/basics/pixels.md)
  * [基本动画](~/xamarin-forms/user-interface/graphics/skiasharp/basics/animation.md)
  * [将文本和图形集成](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)
  * [位图基础知识](~/xamarin-forms/user-interface/graphics/skiasharp/basics/bitmaps.md)
- [线和路径](~/xamarin-forms/user-interface/graphics/skiasharp/paths/index.md)
  * [线和笔划大写字母](~/xamarin-forms/user-interface/graphics/skiasharp/paths/lines.md)
  * [路径基础知识](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)
  * [路径填充类型](~/xamarin-forms/user-interface/graphics/skiasharp/paths/fill-types.md)
  * [折线和参数等式](~/xamarin-forms/user-interface/graphics/skiasharp/paths/polylines.md)
  * [点和短划线](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)
  * [手指绘图](~/xamarin-forms/user-interface/graphics/skiasharp/paths/finger-paint.md)
- [转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/index.md)
  * [翻译转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)
  * [缩放转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/scale.md)
  * [旋转转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)
  * [倾斜转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/skew.md)
  * [矩阵转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md)
  * [触摸操作](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)
  * [非仿射转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)
  * [3D 旋转](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)
- [曲线和路径](~/xamarin-forms/user-interface/graphics/skiasharp/curves/index.md)
  * [绘制弧线的三个方法](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)
  * [贝塞尔曲线的三种类型](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)
  * [SVG 路径数据](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)
  * [按路径和区域进行剪裁](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md)
  * [路径效果](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)
  * [路径和文本](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)
  * [路径信息和枚举](~/xamarin-forms/user-interface/graphics/skiasharp/curves/information.md)
- [位图](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/index.md)
  * [显示位图](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/displaying.md)
  * [创建和绘制位图上](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/drawing.md)
  * [裁剪位图](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/cropping.md)
  * [位图的分段的显示](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/segmented.md)
  * [将位图保存到文件](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/saving.md)
  * [访问位图像素位](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/pixel-bits.md)
  * [对位图进行动画处理](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/animating.md)

## <a name="platform-specific-notesgraphics-gamesskiasharpplatformmd"></a>[平台特定说明](~/graphics-games/skiasharp/platform.md)

此页介绍了不同的平台包括 iOS、 Android、 macOS 和 Windows 上的 SkiaSharp 的安装说明。

## <a name="api-documentationhttpsdeveloperxamarincomapinamespaceskiasharp"></a>[API 文档](https://developer.xamarin.com/api/namespace/SkiaSharp/)

您可以浏览[API 文档](https://developer.xamarin.com/api/namespace/SkiaSharp/)为 SkiaSharp 上我们的网站。

## <a name="work-in-progress"></a>正在进行的工作

SkiaSharp 是工作正在进行的我们正在与我们的社区共享。 尽管我们已绑定 Skia API 的重要组成部分，很多工作仍需完成。 我们使用稳定的 C API 显示 Skia，并且我们计划将继续参与 Skia api 提供完整的覆盖范围的 C 绑定到我们的工作。

若要帮助我们指导我们绑定的工作，请将保留的意见或建议为问题的 GitHub 存储库[ http://github.com/mono/SkiaSharp ](http://github.com/mono/SkiaSharp)。
