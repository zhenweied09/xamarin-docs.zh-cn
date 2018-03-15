---
title: "章 21 的摘要。 转换"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: a40b4f00fd2a0dd3f61c4882a2ef25b8eb68a3a5
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-21-transforms"></a>章 21 的摘要。 转换

Xamarin.Forms 视图中的位置和大小由其父站点，通常是该屏幕上显示`Layout`或`Layout<View>`派生。 *转换*是一种 Xamarin.Forms 功能，可以修改该位置、 大小或甚至方向。

Xamarin.Forms 支持三种基本类型的转换：

- *转换*&mdash;水平或垂直移动元素
- *缩放*&mdash;更改元素的大小
- *旋转*&mdash;打开周围的点或轴元素

Xamarin.Forms 中的缩放是各向同性;统一它影响的宽度和高度。 同时在屏幕的和二维面上在三维空间中支持旋转。 没有任何时间差 （或巨大） 转换，并且未通用化的矩阵转换。

转换支持八个属性的类型与`double`由定义`VisualElement`类：

- [`TranslationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)
- [`TranslationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)
- [`Scale`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)
- [`Rotation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)
- [`RotationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/)
- [`RotationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/)
- [`AnchorX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)
- [`AnchorY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)

由可绑定属性，所有这些属性提供支持。 它们可以是数据绑定的目标和风格。 [**章 22。动画**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md)演示如何这些属性可以进行动画处理，但某些本章中的示例显示如何动态显示它们使用 Xamarin.Forms[计时器](~/xamarin-forms/platform/device.md#Device_StartTimer)。

转换仅如何呈现时，以及执行操作的元素的属性影响*不*影响如何在布局中识别的元素。

## <a name="the-translation-transform"></a>转换转换

非零值的[ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)和[ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)属性水平或垂直移动元素。

[ **TranslationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo)程序允许你尝试使用这些属性包含两个`Slider`控制元素`TranslationX`和`TranslationY`属性`Frame`. 转换还会影响的所有子级`Frame`。

### <a name="text-effects"></a>文本效果

转换属性的一个常见用途是，因而轻微弥补文本的呈现。 此进行了演示[ **TextOffsets** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets)示例：

[![文本的偏移量的三个屏幕截图](images/ch21fg03-small.png "文本偏移量")](images/ch21fg03-large.png#lightbox "文本偏移量")

另一个作用是呈现的多个副本`Label`以类似于三维块，如所示[ **BlockText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText)示例。

### <a name="jumps-and-animations"></a>跳转和动画

[ **ButtonJump** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump)示例使用转换来移动`Button`每当点击，但主要意图在于演示：`Button`接收位置的用户输入，其中呈现的按钮。

[ **ButtonGlide** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide)示例类似，但使用计时器要进行动画处理`Button`从到另一个点。

## <a name="the-scale-transform"></a>缩放变换

[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)转换可以增加或减少元素的呈现的大小。 默认值为 1。 值为 0 会导致元素不可见。 负值导致要似乎旋转 180 度的元素。 `Scale`属性不会影响`Width`或`Height`元素的属性。 这些值保持不变。

你可以用来试验`Scale`属性使用[ **SimpleScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo)示例。

[ **ButtonScaler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler)示例演示之间进行动画处理的差别`Scale`属性`Button`和动态显示`FontSize`属性。 `FontSize`属性影响如何`Button`布局; 在任何可察觉`Scale`属性却没有。

[ **ScaleToSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize)示例计算`Scale`属性应用于`Label`但价格仍在页中进行一样大的元素。

### <a name="anchoring-the-scale"></a>锚定刻度

在以前的三个样本进行缩放的元素具有所有增加或减少的大小相对于元素的中心。 换而言之，元素中增大或减小大小所有方向相同。 仅点在中心的元素保留在同一位置缩放时。

你可以更改通过设置缩放 center [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)和[ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)属性。 这些属性是相对于元素本身。 有关`AnchorX`、 左侧元素的引用值为 0 和 1 指右侧。 同样对于`AnchorY`、 0 是顶部，1 是底部。 这两个属性具有默认值为 0.5，它是的中心。

[ **AnchoredScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo)示例允许你能够试验`AnchorX`和`AnchorY`属性以及`Scale`属性。

在 iOS 中，使用非默认值`AnchorX`和`AnchorY`属性是与 phone 方向更改通常不兼容。

## <a name="the-rotation-transform"></a>旋转转换

[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)属性以度为单位指定的指示由定义的元素的点的顺时针旋转`AnchorX`和`AnchorY`。 [ **PlaneRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo)允许你能够试验这三个属性。

### <a name="rotated-text-effects"></a>旋转的文本效果

[ **BoxViewCircle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle)示例演示如何绘制使用 64 小旋转的圆形所需的数学学科`BoxView`元素。

[ **RotatedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText)示例显示多个`Label`具有相同的文本字符串的元素旋转以看起来像是分支。

[ **CircularText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText)示例显示似乎包装在一个圆周中的文本字符串。

### <a name="an-analog-clock"></a>是模拟时钟

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库包含[ `AnalogClockViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs)计算时钟手的角度的类。 若要避免在视图模型，类使用的平台依赖项`Task.Delay`而不是用于查找一个新的计时器`DateTime`值。

也包括在**Xamarin.FormsBook.Toolkit**是[ `SecondTickConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs)类，该类实现`IValueConverter`并提供要舍入到最接近的秒第二个角度。

[ **MinimalBoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock)使用三个轮换`BoxView`绘制是模拟时钟的元素。

[ **BoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock)使用`BoxView`用于更广泛的图形，包括刻度标记围绕表面的时钟，并从其结束为，旋转的小距离：

[![三重的字数时钟的屏幕截图](images/ch21fg17-small.png "模拟时钟表盘")](images/ch21fg17-large.png#lightbox "模拟时钟表盘")

此外[ `SecondBackEaseConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs)类**Xamarin.FormsBook.Toolkit**会导致第二个指针能够看上去像有点取回之前预先跳转，然后将移到其正确的位置。

### <a name="vertical-sliders"></a>垂直滑块？

[ **VerticalSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders)示例演示`Slider`元素可否旋转 90 度，并仍函数。 但是，很难放置这些旋转`Slider`元素因为它们仍在布局中显示为水平。

## <a name="3d-ish-rotations"></a>三维 ish 旋转

[ `RotationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/)属性似乎旋转绕 3D x 轴元素，以便似乎移入或离开查看器的顶部和底部的元素。 同样， [ `RotationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/)似乎旋转围绕 y 轴以便似乎移入或离开查看器的左侧和右侧的元素的元素。

`AnchorX`属性将影响`RotationY`但不是`RotationX`。 `AnchorY`属性将影响`RotationX`但不是`RotationY`。 你可以用来试验[ **ThreeDeeRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo)示例来浏览这些属性的交互。

左手隐含 Xamarin.Forms 3D 坐标系统。 如果你点左手递增 X 方向的食指协调 （右侧） 和中间手指递增 Y 方向协调 （向下），然后你在增加 Z 坐标 （带屏幕） 的方向的滚动块点。

此外，对于任何三个轴，如果你的递增值，方向点左侧拇指然后你指的曲线指示的方向旋转为正的旋转角度。



## <a name="related-links"></a>相关链接

- [章 21 全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [章 21 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
