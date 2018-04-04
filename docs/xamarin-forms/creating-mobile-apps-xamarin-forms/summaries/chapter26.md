---
title: 章 26 的摘要。 自定义布局
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 5d2dc3e809026a36d186c9a582fcd047f6be24fe
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-26-custom-layouts"></a>章 26 的摘要。 自定义布局

Xamarin.Forms 包括多个类派生自[ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/):

* `StackLayout`,
* `Grid`,
* `AbsoluteLayout` 和
* `RelativeLayout`。

本章介绍如何创建您自己的类派生自`Layout<View>`。

## <a name="an-overview-of-layout"></a>布局事件的概述

没有任何集中式的系统，可处理 Xamarin.Forms 布局。 每个元素都负责确定其自身的大小应该是什么，以及如何在特定区域内呈现自身。

### <a name="parents-and-children"></a>父项和子项

每个元素没有子级负责定位在自身这些子级。 它是应基于最终确定什么大小及其子级的父级的大小，它有可用，并且大小子想要。

### <a name="sizing-and-positioning"></a>大小调整和定位

布局开始与页面的可视化树的顶部，然后继续进行所有分支。 在布局中最重要的公共方法是[ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/)由定义`VisualElement`。 每个元素的父级的其他元素调用`Layout`为每个其子级的大小和位置相对于其自身的形式提供子[ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/)值。 这些`Layout`调用传播到可视化树。

调用`Layout`是所必需的元素出现在屏幕上，并导致以下的只读属性，以设置。 它们是与一致`Rectangle`传递给方法：

- [`Bounds`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Bounds/) 类型 `Rectangle`
- [`X`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.X/) 类型 `double`
- [`Y`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Y/) 类型 `double`
- [`Width`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) 类型 `double`
- [`Height`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) 类型 `double`

之前`Layout`调用，`Height`和`Width`具有模拟值&ndash;1。

调用`Layout`还会触发对以下受保护的方法的调用：

- [`SizeAllocated`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.SizeAllocated/p/System.Double/System.Double/)哪些调用
- [`OnSizeAllocated`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnSizeAllocated/p/System.Double/System.Double/)其中可以重写。

最后，激发以下事件：

- [`SizeChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.SizeChanged/)

`OnSizeAllocated`方法被重写通过`Page`和`Layout`，它们可以有子级的 Xamarin.Forms 中的仅有两个类。 重写的方法调用

- [`UpdateChildrenLayout`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.UpdateChildrenLayout()/) 有关`Page`衍生产品和[ `UpdateChildrenLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.UpdateChildrenLayout()/)为`Layout`衍生产品，调用
- [`LayoutChildren`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) 有关`Page`衍生产品和[ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/)为`Layout`衍生产品。

`LayoutChildren` 然后调用`Layout`为每个元素的子级。 如果至少一个子有一个新的`Bounds`设置，然后引发以下事件：

- [`LayoutChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.LayoutChanged/) 有关`Page`衍生产品和[ `LayoutChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Layout.LayoutChanged/)为`Layout`衍生产品

### <a name="constraints-and-size-requests"></a>约束和大小请求

有关`LayoutChildren`智能地调用`Layout`上所有子级，它必须知道*首选*或*需*大小的子级。 因此对的调用`Layout`通过调用通常前面为每个子级

- [`GetSizeRequest`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.GetSizeRequest/p/System.Double/System.Double/)

书已发布后，`GetSizeRequest`方法已弃用，替换为

- [`Measure`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)

`Measure`方法还包含[ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)属性并包含类型的自变量[ `MeasureFlag` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MeasureFlags/)，它具有两个成员：

- [`IncludeMargins`](https://developer.xamarin.com/api/field/Xamarin.Forms.MeasureFlags.IncludeMargins/)
- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.MeasureFlags.None/) 以便不包括边距

对于许多元素，`GetSizeRequest`或`Measure`其呈现器中获取的本机大小的元素。 这两种方法具有宽度和高度参数*约束*。 例如，`Label`将使用的宽度约束来确定如何包装多行文本。

同时`GetSizeRequest`和`Measure`返回类型的值[ `SizeRequest` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SizeRequest/)，它具有两个属性：

- [`Request`](https://developer.xamarin.com/api/property/Xamarin.Forms.SizeRequest.Request/) 类型 `Size`
- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.SizeRequest.Minimum/) 类型 `Size`

非常频繁这两个值都是相同的与`Minimum`值通常可以忽略。

`VisualElement` 此外定义了受保护的方法类似于`GetSizeRequest`从调用`GetSizeRequest`:

- [`OnSizeRequest`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnSizeRequest/p/System.Double/System.Double/) 返回`SizeRequest`值

该方法现在已弃用，并替换为：

- [`OnMeasure`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)

每个类都派生自`Layout`或`Layout<T>`必须重写`OnSizeRequest`或`OnMeasure`。 这是其中布局类将确定其自身的大小，它通过调用获取及其子级的大小通常基于`GetSizeRequest`或`Measure`子级上。 之前和之后调用`OnSizeRequest`或`OnMeasure`，`GetSizeRequest`或`Measure`进行调整基于以下属性：

- [`WidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)类型的`double`，影响`Request`属性 `SizeRequest`
- [`HeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) 类型的`double`，影响`Request`属性 `SizeRequest`
- [`MinimumWidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.MinimumWidthRequest/) 类型的`double`，影响`Minimum`属性 `SizeRequest`
- [`MinimumHeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.MinimumHeightRequest/) 类型的`double`，影响`Minimum`属性 `SizeRequest`

### <a name="infinite-constraints"></a>无限约束

约束自变量传递给`GetSizeRequest`(或`Measure`) 和`OnSizeRequest`(或`OnMeasure`) 可以是无限期 (即值`Double.PositiveInfinity`)。 但是，`SizeRequest`返回从这些方法不能包含无限的维度。

无限约束指示所请求的大小应反映元素的原始大小。 垂直`StackLayout`调用`GetSizeRequest`(或`Measure`) 上具有无限高度约束及其子级。 水平堆叠布局调用`GetSizeRequest`(或`Measure`) 上具有无限宽度约束及其子级。 `AbsoluteLayout`调用`GetSizeRequest`(或`Measure`) 上具有无限的宽度和高度约束及其子级。

### <a name="peeking-inside-the-process"></a>查看在过程

[ **ExploreChildSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes)显示约束和大小请求信息的简单布局。

## <a name="deriving-from-layoutview"></a>派生自布局<View>

自定义布局类派生自`Layout<View>`。 它具有以下两项职责：

- 重写`OnMeasure`调用`Measure`布局的所有子级上。 返回该版式本身的请求的大小
- 重写`LayoutChildren`调用`Layout`布局的所有子级上

`for`或`foreach`这些重写中的循环应跳过任何子其`IsVisible`属性设置为`false`。

调用`OnMeasure`不能保证。 `OnMeasure` 将不会调用如果布局的父负责管理布局的大小 （例如，填满页面布局）。 为此，`LayoutChildren`不能依赖于子大小期间获取`OnMeasure`调用。 通常，`LayoutChildren`本身必须调用`Measure`布局的子级上，也可以实现某种类型的缓存的逻辑 （稍后讨论） 的大小。

### <a name="an-easy-example"></a>一个简单示例

[ **VerticalStackDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo)示例包含的简化[ `VerticalStack` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs)类和其用法的演示。

### <a name="vertical-and-horizontal-positioning-simplified"></a>垂直和水平定位简化

一个作业，`VerticalStack`必须执行期间出现`LayoutChildren`重写。 该方法使用的子`HorizontalOptions`属性来确定如何在其槽内放置子`VerticalStack`。 你可以改为调用静态方法[ `Layout.LayoutChildIntoBoundingRect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/)。 此方法调用`Measure`上子并使用其`HorizontalOptions`和`VerticalOptions`属性来定位指定的矩形中的子级。

### <a name="invalidation"></a>失效

往往元素的属性中的更改会影响该元素在布局中的显示方式。 布局必须失效来触发新的布局。

`VisualElement` 定义受保护的方法[ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/)，通常称为属性更改处理程序的任何可绑定属性的更改影响的元素的大小。 `InvalidateMeasure`方法激发[ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/)事件。

`Layout`类定义一个名为的类似的受保护的方法[ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/)，后者`Layout`派生应调用会影响如何定位和大小及其子级的任何更改。

### <a name="some-rules-for-coding-layouts"></a>编码布局的一些规则

1. 属性定义`Layout<T>`衍生产品应由可绑定属性和属性更改处理程序应调用`InvalidateLayout`。

2. A`Layout<T>`定义附加的可绑定属性的派生应重写[ `OnAdded` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout%3CT%3E.OnAdded/p/T/)将属性更改的处理程序添加到其子和[ `OnRemoved` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout%3CT%3E.OnRemoved/p/T/)若要删除的处理程序。 该处理程序应检查这些中的更改附加的可绑定属性并通过调用响应`InvalidateLayout`。

3. A`Layout<T>`实现子大小的缓存的派生应重写`InvalidateLayout`和[ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/)并清除缓存，当调用这些方法。

### <a name="a-layout-with-properties"></a>一种布局有属性

[ `WrapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)假定所有子级都都相同大小，并将从某一行 （或列） 的子级包装到下一步。 它定义`Orientation`像属性`StackLayout`，和`ColumnSpacing`和`RowSpacing`属性，例如`Grid`，并且其缓存子大小。

[ **PhotoWrap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap)示例将`WrapLayout`中`ScrollView`用于显示股票照片。

### <a name="no-unconstrained-dimensions-allowed"></a>允许任何不受约束的维度 ！

[ `UniformGridLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库用于显示其自身的所有子级。 因此，它无法处理的不受约束的维度，并且如果遇到一个，则会引发异常。

[ **PhotoGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid)示例演示如何`UniformGridLayout`:

[![三重的照片网格的屏幕截图](images/ch26fg08-small.png "统一的网格布局")](images/ch26fg08-large.png#lightbox "统一的网格布局")

### <a name="overlapping-children"></a>重叠的子级

A`Layout<T>`派生可以重叠及其子级。 但是，在其顺序呈现子级`Children`集合，并且不在其中的顺序其`Layout`调用方法。

`Layout`类定义允许你将集合内的子两种方法：

- [`LowerChild`](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LowerChild/p/Xamarin.Forms.View/) 若要将子移动到集合的开头
- [`RaiseChild`](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.RaiseChild/p/Xamarin.Forms.View/) 若要将子移动到集合的末尾

对于重叠子级，在集合末尾的子级直观地显示在集合开头的子级之上。

[ `OverlapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库定义了一个附加的属性，以指示呈现顺序，因此允许之一其若要在其他之上显示的子级。 [ **StudentCardFile** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile)示例演示这一操作：

[![学生卡文件网格的三个屏幕截图](images/ch26fg10-small.png "重叠布局子级")](images/ch26fg10-large.png#lightbox "重叠布局子级")

### <a name="more-attached-bindable-properties"></a>连接可绑定属性

[ `CartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库定义附加的可绑定属性，以指定两个`Point`值和粗细值和操作`BoxView`元素类似于行。

[ **UnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube)示例使用该值来绘制三维多维数据集。

### <a name="layout-and-layoutto"></a>布局和 LayoutTo

A`Layout<T>`派生可以调用`LayoutTo`而非`Layout`要进行动画处理的布局。 [ `AnimatedCartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs)类执行此操作，与[ **AnimatedUnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube)示例演示它。



## <a name="related-links"></a>相关链接

- [章 26 全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [章 26 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)
