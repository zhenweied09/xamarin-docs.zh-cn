---
title: "章 27 的摘要。 自定义呈现器"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 0c1dc9ba5cf382551a1142110c68d16421db07e4
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="summary-of-chapter-27-custom-renderers"></a>章 27 的摘要。 自定义呈现器

如 Xamarin.Forms 元素`Button`呈现与封装在一个名为类特定于平台的按钮`ButtonRenderer`。  下面是[的 iOS 版本`ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs)、[的 Android 版本`ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)，和[Windows 运行时版本`ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.WinRT/ButtonRenderer.cs)。

这一章讨论如何编写你自己的呈现器，以创建映射到特定于平台的对象的自定义视图。

## <a name="the-complete-class-hierarchy"></a>完整类层次结构

有七个包含 Xamarin.Forms 特定于平台的代码的程序集。
您可以使用这些链接的 GitHub 上查看的源代码：

- [**Xamarin.Forms.Platform** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) （很小）
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.WinRT** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT) （大于的下一步的三个）
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)
- [**Xamarin.Forms.Platform.WinRT.Tablet**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT.Tablet)
- [**Xamarin.Forms.Platform.WinRT.Phone**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT.Phone)

[ **PlatformClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy)示例显示对正在执行平台有效的程序集的类层次结构。

你会注意到名为重要类`ViewRenderer`。 这是创建特定于平台的呈现器时，在从派生的类。 它存在于三个不同版本，因为它将关联到视图系统中的目标平台的不同而不同：

IOS [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L26)具有泛型自变量：

- `TView` 约束为 [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeView` 约束为 [`UIKit.UIView`](https://developer.xamarin.com/api/type/UIKit.UIView/)

Android [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L14)具有泛型自变量：

- `TView` 约束为 [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeView` 约束为 [`Android.Views.View`](https://developer.xamarin.com/api/type/Android.Views.View/)

Windows 运行时[ `ViewRenderer<TElement, TNativeElement>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.WinRT/ViewRenderer.cs#L12)了以不同方式命名泛型自变量：

- `TElement` 约束为 [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeElement` 约束为 [`Windows.UI.Xaml.FrameworkElement`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.aspx)

当编写这样的呈现器时，将派生的类从`View`，，然后编写多个`ViewRenderer`类、 一个针对每个受支持的平台。 每个特定于平台的实现将引用派生自指定为类型的本机类`TNativeView`或`TNativeElement`参数。

## <a name="hello-custom-renderers"></a>Hello，自定义呈现器 ！

[ **HelloRenderers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers)程序引用名为的自定义视图`HelloView`中其[ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs)类。

[ `HelloView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs)类包括在**HelloRenderers**项目，然后只需派生自`View`。

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs)类**HelloRenderers.iOS**项目派生自`ViewRenderer<HelloView, UILabel>`。 在`OnElementChanged`重写时，它会创建本机 iOS`UILabel`和调用`SetNativeControl`。

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs)类**HelloRenderers.Droid**项目派生自`ViewRenderer<HelloView, TextView>`。 在`OnElementChanged`重写时，它会创建 Android`TextView`和调用`SetNativeControl`。

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs)类**HelloRenderers.UWP**和其他 Windows 项目派生自`ViewRenderer<HelloView, TextBlock>`。 在`OnElementChanged`重写时，它可以创建窗口`TextBlock`和调用`SetNativeControl`。

所有`ViewRenderer`衍生产品包含`ExportRenderer`将相关联的程序集级别属性`HelloView`与特定的类`HelloViewRenderer`类。 这是 Xamarin.Forms 如何定位单个平台项目中的呈现器：

[![三重的 Hello 视图的屏幕截图](images/ch27fg02-small.png "自定义呈现器")](images/ch27fg02-large.png "自定义呈现器")

## <a name="renderers-and-properties"></a>呈现器和属性

下一组呈现器实现椭圆绘图，并位于中各项目的[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解决方案。

[ `EllipseView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs)类位于**Xamarin.FormsBook.Platform**平台。 此类是类似于`BoxView`并定义一个属性：`Color`类型的`Color`。

呈现器可以传输中设置的属性值`View`到通过重写的本机对象`OnElementPropertyChanged`呈现器中的方法。 在此方法中 （以及大部分呈现器中），两个属性有：

- `Element`Xamarin.Forms 元素
- `Control`本机视图或小组件或控件的对象

这些属性的类型由泛型参数`ViewRenderer`。 在此示例中，`Element`属于类型`EllipseView`。

`OnElementPropertyChanged`替代因此可以传输`Color`值`Element`于本机`Control`对象，可能与某些类型的转换。 三个呈现器是：

- iOS: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs)，它使用[ `EllipseUIView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs)椭圆的类。
- Android: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs)，它使用[ `EllipseDrawableView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs)椭圆的类。
- Windows 运行时： [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs)，这可以使用本机 Windows [ `Ellipse` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.ellipse.aspx)类。

[ **EllipseDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo)类显示多个这样的`EllipseView`对象：

[![椭圆演示的三个屏幕截图](images/ch27fg03-small.png "EllipseView 自定义呈现器")](images/ch27fg03-large.png "EllipseView 自定义呈现器")

[ **BouncingBall** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall)退回`EllipseView`关闭两边的屏幕。

## <a name="renderers-and-events"></a>呈现器和事件

它还有可能的呈现器间接生成事件。 [ `StepSlider` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs)类是类似于正常 Xamarin.Forms`Slider`但允许指定多个离散步骤之间`Minimum`和`Maximum`值。

三个呈现器是：

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- Windows 运行时： [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

呈现器对本机控件，更改的检测，然后调用`SetValueFromRenderer`，它引用中定义的可绑定属性`StepSlider`，这将导致更改`StepSlider`激发`ValueChanged`事件。

[ **StepSliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo)示例演示此新滑块。



## <a name="related-links"></a>相关链接

- [章 27 全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [章 27 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
