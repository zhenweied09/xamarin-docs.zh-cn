---
title: "SkiaSharp 转换"
description: "了解用于显示 SkiaSharp 图形的转换"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 06db59f5585671342ef7dbaa2d2cf14ec6830c41
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="skiasharp-transforms"></a>SkiaSharp 转换

_了解用于显示 SkiaSharp 图形的转换_

SkiaSharp 支持实现为的方法的传统图形转换[ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/)对象。 转换数学上，alter 坐标和你在中指定的大小`SKCanvas`绘制函数，如呈现的图形对象。 转换通常是用于绘制重复图形或动画方便的。 某些技术和 #x 2014;如旋转位图或文本和 #x 2014;不可能无需转换的使用。

SkiaSharp 转换支持以下操作：

- *转换*到 shift 坐标从一个位置复制到另一个
- *缩放*增加或减少坐标和大小
- *旋转*旋转围绕一个点的坐标
- *倾斜*移动协调水平或垂直，以便矩形成为一个平行四边形

这些被称为*仿射*转换。 仿射转换始终保留平行直线和永远不会导致要成为无限的坐标或大小。 正方形永远不会转换为的平行四边形以外的任何和圆形永远不会转换为一个椭圆之外的任何内容。

SkiaSharp 还支持非仿射转换 (也称为*投影*或*透视*转换) 基于标准的 3-3 转换矩阵。 非仿射转换允许方块以转换为任何凸四边形 （四个边图小于 180 度与所有的内部角度）。 非仿射转换可能导致坐标或大小成为无限的但它们是必需的三维效果。

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>SkiaSharp 和 Xamarin.Forms 转换之间的差异

Xamarin.Forms 还支持 SkiaSharp 相似的转换。 Xamarin.Forms [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)类定义以下转换属性：

- `TranslationX` 和 `TranslationY`
- `Scale`
- `Rotation`、`RotationX` 和 `RotationY`

`RotationX`和`RotationY`属性是创建近似三维效果的透视转换。

有几个 SkiaSharp 转换和 Xamarin.Forms 转换之间的重要区别：

第一个差异是 SkiaSharp 转换将应用于整个`SKCanvas`对象时 Xamarin.Forms 转换应用于单个`VisualElement`衍生产品。 (你可以将应用到 Xamarin.Forms 转换`SKCanvasView`对象本身，因为`SKCanvasView`派生自`VisualElement`，但在其中`SKCanvasView`，SkiaSkarp 转换应用。)

SkiaSharp 转换是相对于窗口左上角`SKCanvas`相对于窗口左上角的 Xamarin.Forms 转换时`VisualElement`它们将应用。 此区别便尤为重要应用缩放和旋转转换，因为这些转换始终是相对于某个特定点。

非常大的区别是 SKiaSharp 转换是*方法*Xamarin.Forms 转换时*属性*。 这是超出的语法差异语义的区别： SkiaSharp 转换 Xamarin.Forms 转换设置状态时执行操作。 SkiaSharp 转换应用于随后绘制的图形对象，但不适用于应用转换之前绘制的图形对象。 与此相反，Xamarin.Forms 转换适用于以前呈现元素的属性设置时，就会立即。 调用这些方法; 是累积 SkiaSharp 转换将属性设置与另一个值，将替换 Xamarin.Forms 转换。

本部分中的所有示例程序都显示在标题下**转换**的主页中[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)程序，然后在[**转换**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms)的解决方案的文件夹。

## <a name="the-translate-transformtranslatemd"></a>[翻译转换](translate.md)

了解如何使用转换变换移动 SkiaSharp 图形。

## <a name="the-scale-transformscalemd"></a>[缩放转换](scale.md)

发现 SkiaSharp 缩放变换的缩放到各种大小的对象。

## <a name="the-rotate-transformrotatemd"></a>[旋转转换](rotate.md)

浏览效果和动画，可以使用 SkiaSharp 旋转转换。

## <a name="the-skew-transformskewmd"></a>[倾斜转换](skew.md)

请参阅如何倾斜转换可以在 SkiaSharp 创建倾斜图形对象。

## <a name="matrix-transformsmatrixmd"></a>[矩阵转换](matrix.md)

深入了解 SkiaSharp 转换使用的通用转换矩阵。

## <a name="touch-manipulationstouchmd"></a>[触摸操作](touch.md)

使用矩阵转换来实现拖动、 缩放和旋转触摸操作。

## <a name="non-affine-transformsnon-affinemd"></a>[非仿射转换](non-affine.md)

超出了与非仿射转换效果 oridinary。

## <a name="3d-rotation3d-rotationmd"></a>[3D 旋转](3d-rotation.md)

使用非仿射转换轮换在三维空间中的 2D 对象。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
