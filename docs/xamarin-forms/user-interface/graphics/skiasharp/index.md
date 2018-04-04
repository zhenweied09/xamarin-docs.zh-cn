---
title: 使用 Xamarin.Forms 中 SkiaSharp
description: 用于在 Xamarin.Forms 应用程序的二维图形 SkiaSharp
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 09/11/2017
ms.openlocfilehash: e3444411c80ecaee105cce7c10f7bec7583dc2a5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="using-skiasharp-in-xamarinforms"></a>使用 Xamarin.Forms 中 SkiaSharp

_用于在 Xamarin.Forms 应用程序的二维图形 SkiaSharp_

SkiaSharp 是二维图形系统 for.NET 和 C# 由 Google 产品中广泛使用的开放源代码 Skia 图形引擎提供支持。 可以在 Xamarin.Forms 应用程序使用 SkiaSharp 绘制二维矢量图形、 位图和文本。 请参阅[2D 绘图](~/graphics-games/skiasharp/index.md)SkiaSharp 库有关的更多常规信息和一些其他教程的指南。

本指南假定你熟悉 Xamarin.Forms 编程。

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Xamarin.Forms 的网络研讨会： SkiaSharp**

## <a name="skiasharp-preliminaries"></a>SkiaSharp 初步操作

Xamarin.Forms 的 SkiaSharp 打包为 NuGet 包。 已为 Mac 在 Visual Studio 或 Visual Studio 创建 Xamarin.Forms 解决方案之后，可以使用 NuGet package manager 要搜索**SkiaSharp.Views.Forms**包并将其添加到你的解决方案。 如果选中**引用**部分的每个项目添加 SkiaSharp 后，你可以看到，各种**SkiaSharp**库已添加到每个解决方案中的项目。

Xamarin.Forms 应用程序针对 iOS 的如果使用项目属性页更改为 iOS 8.0 的最小的部署目标。

在使用 SkiaSharp 任何 C# 页中，你将需要包括`using`指令[ `SkiaSharp` ](https://developer.xamarin.com/api/namespace/SkiaSharp/)命名空间，其中包含所有 SkiaSharp 类、 结构和将在图形中使用的枚举编程。 你还需要`using`指令[ `SkiaSharp.Views.Forms` ](https://developer.xamarin.com/api/namespace/SkiaSharp.Views.Forms/)特定于 Xamarin.Forms 类的命名空间。 这是一个多较小命名空间，最重要的类为[ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/)。 此类派生自 Xamarin.Forms`View`类并承载 SkiaSharp 图形输出。

> [!IMPORTANT]
> `SkiaSharp.Views.Forms`命名空间还包含`SKGLView`派生自的类`View`但使用 OpenGL 呈现图形。 为了简单起见，本指南会使自身限制到`SKCanvasView`，但使用`SKGLView`相反，它是非常相似。

## <a name="skiasharp-drawing-basicsbasicsindexmd"></a>[SkiaSharp 绘制基础知识](basics/index.md)

某些与 SkiaSharp 可以绘制的最简单的图形图是圆形、 椭圆和矩形。 在显示这些数字，你将学习如何 SkiaSharp 坐标、 大小和颜色。

## <a name="skiasharp-lines-and-pathspathsindexmd"></a>[SkiaSharp 线和路径](paths/index.md)

图形路径是一系列连接的直线和曲线。 路径可描边，填充，和/或文件名。 本主题包含线条图形，包括笔画末端和联接，和短划线和虚线，但缺少曲线几何图形的停止的许多的方面。

## <a name="skiasharp-transformstransformsindexmd"></a>[SkiaSharp 转换](transforms/index.md)

转换允许图形对象统一转换、 缩放、 旋转或倾斜。 本文还介绍用于创建非仿射转换和将转换应用到路径，你就可以使用标准的 3-3 转换矩阵。

## <a name="skiasharp-curves-and-pathscurvesindexmd"></a>[SkiaSharp 曲线和路径](curves/index.md)

路径浏览会继续进行将曲线添加到路径对象，并利用功能强大的路径中的其他功能。 你将看到简洁的文本字符串中，你就可以指定整个路径、 如何使用路径的作用，以及如何深入探究路径内部结构。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [SkiaSharp 与 Xamarin.Forms 网络研讨会 （视频）](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
