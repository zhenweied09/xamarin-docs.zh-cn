---
title: 第 14 章的摘要。 绝对布局
description: 使用 Xamarin.Forms 创建移动应用： 章 14 的摘要。 绝对布局
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: b8fe8f8bb0ba3204a312f3d91bc59b8fa3f7c03a
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241398"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>第 14 章的摘要。 绝对布局

如`StackLayout`， [ `AbsoluteLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)派生自`Layout<View>`和继承`Children`属性。 `AbsoluteLayout` 实现一个要求程序员也要指定的位置的子级以及其大小 （可选） 的布局系统。 位置由相对于窗口左上角的子窗口左上角指定`AbsoluteLayout`以独立于设备的单位。 `AbsoluteLayout` 此外实现的成比例定位、 调整大小功能。

`AbsoluteLayout` 应被认为是仅当程序员可以施加的子级上大小时要使用的特殊用途布局系统 (例如，`BoxView`元素) 或当元素的大小并不会影响其他的子级的位置。 `HorizontalOptions`和`VerticalOptions`属性不起作用的子级上`AbsoluteLayout`。

本章还引入了的重要功能*附加可绑定属性*，可让某个类中定义的属性 (在这种情况下`AbsoluteLayout`) 要附加到另一个类 (的子级`AbsoluteLayout`)。

## <a name="absolutelayout-in-code"></a>在代码中的 AbsoluteLayout

你可以添加到子`Children`集合`AbsoluteLayout`使用标准[ `Add` ](https://developer.xamarin.com/api/member/System.Collections.Generic.ICollection%3CT%3E.Add/p/T/)方法，但`AbsoluteLayout`还提供了扩展[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Rectangle/Xamarin.Forms.AbsoluteLayoutFlags/)允许您指定的方法[ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/)。 另一个[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Point/)方法仅要求[ `Point` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Point/)，在这种情况下的子域是不受约束和调整自身大小。

你可以创建`Rectangle`值与[构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Rectangle.Rectangle/p/System.Double/System.Double/System.Double/System.Double/)需要四个值&mdash;，该值指示相对于其父级的子级的左上角的位置的前两个和，该值指示第二个第二个孩子的大小。 也可以使用[构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Rectangle.Rectangle/p/Xamarin.Forms.Point/Xamarin.Forms.Size/)需要`Point`和[ `Size` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Size/)值。

这些`Add`方法所示[ **AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo)，哪些位置`BoxView`元素使用`Rectangle`值，和一个`Label`使用刚刚元素`Point`值。

[ **ChessboardFixed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed)示例使用 32`BoxView`元素以创建棋盘模式。 该程序提供`BoxView`35 单元方形的硬编码的元素大小。 `AbsoluteLayout`具有其`HorizontalOptions`和`VerticalOptions`设置为`LayoutOptions.Center`，这将导致`AbsoluteLayout`能够 280 单元方形的总大小。

## <a name="attached-bindable-properties"></a>附加的可绑定属性

还有可能要设置的位置和 （可选） 的子级的大小`AbsoluteLayout`添加到之后`Children`集合使用的静态方法[ `AbsoluteLayout.SetLayoutBounds` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.SetLayoutBounds/p/Xamarin.Forms.BindableObject/Xamarin.Forms.Rectangle/)。 第一个参数是子;第二个是`Rectangle`对象。 你可以指定，子调整自身大小水平和/或垂直宽度和高度值设置为[ `AbsoluteLayout.AutoSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.AbsoluteLayout.AutoSize/)常量。

[ **ChessboardDynamic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic)示例将`AbsoluteLayout`中`ContentView`与`SizeChanged`处理程序以调用`AbsoluteLayout.SetLayoutBounds`以使它们尽可能大的所有子级上。  

附加的可绑定属性的`AbsoluteLayout`定义类型的静态只读字段`BindableProperty`名为[ `AbsoluteLayout.LayoutBoundsProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty/)。 静态`AbsoluteLayout.SetLayoutBounds`方法实现通过调用`SetValue`上与子`AbsoluteLayout.LayoutBoundsProperty`。 子包含在其中存储附加的可绑定属性，并且其值的字典。 在布局，`AbsoluteLayout`可以通过调用来获取该值[ `AbsoluteLayout.GetLayoutBounds` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.GetLayoutBounds/p/Xamarin.Forms.BindableObject/)，这通过实现`GetValue`调用。

## <a name="proportional-sizing-and-positioning"></a>按比例调整大小和定位

`AbsoluteLayout` 实现成比例的大小调整和定位功能。 类定义的第二个附加可绑定属性， [ `LayoutFlagsProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty/)，与相关的静态方法[ `AbsoluteLayout.SetLayoutFlags` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.SetLayoutFlags/p/Xamarin.Forms.BindableObject/Xamarin.Forms.AbsoluteLayoutFlags/)和[ `AbsoluteLayout.GetLayoutFlags` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.GetLayoutFlags/p/Xamarin.Forms.BindableObject/)。

自变量`AbsoluteLayout.SetLayoutFlags`和返回值的`AbsoluteLayout.GetLayoutFlags`是类型的值[ `AbsoluteLayoutFlags` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayoutFlags/)，枚举包含下列成员：

- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.None/) （等于 0）
- [`XProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.XProportional/) (1)
- [`YProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.YProportional/) (2)
- [`PositionProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional/) (3)
- [`WidthProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional/) (4)
- [`HeightProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional/) (8)
- [`SizeProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional/) (12)
- [`All`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.All/) (\xFFFFFFFF)

你可以将这些使用 C# 按位 OR 运算符合并。

这些标志设置后，某些属性`Rectangle`布局边界结构用于位置和大小子解释按比例。

当`WidthProportional`设置标志，`Width`值 1 表示，与相同的宽度，该子级则`AbsoluteLayout`。 类似的方法用于高度。

按比例定位将考虑在内的大小。 当`XProportional`设置标志，`X`属性`Rectangle`布局范围大小成正比。 值为 0 表示子级的左边缘将位于的左边缘`AbsoluteLayout`，但 1 表示此子级的右边缘将位于的右边缘的位置`AbsoluteLayout`，不超出的右边缘`AbsoluteLayout`为的方式可能 expect。 `X` 0.5 属性为中心水平在子`AbsoluteLayout`。

[ **ChessboardProportional** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional)示例演示如何使用成比例的大小调整和定位。

## <a name="working-with-proportional-coordinates"></a>使用成比例坐标

有时，很容易地将不同于如何在中实施定位成比例`AbsoluteLayout`。 你可能希望使用成比例坐标其中`X`1 属性针对的右边缘置于子级的左边的缘 （而不是右边缘） `AbsoluteLayout`。

此替代的定位方案可以调用"小数子 coordinates"。 你可以从小数子坐标转换为所需的布局边界`AbsoluteLayout`使用以下公式：

layoutBounds.X = (fractionalChildCoordinate.X / (1-layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1-layoutBounds.Height))

[ **ProportionalCoordinateCalc** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc)示例演示这一操作。

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout 和 XAML

你可以使用`AbsoluteLayout`在 XAML 中的子级上设置附加的可绑定属性和`AbsoluteLayout`使用的属性值`AbsoluteLayout.LayoutBounds`和`AbsoluteLayout.LayoutFlags`。 此进行了演示[ **AbsoluteXamlDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo)和[ **ChessboardXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml)示例。 后一种程序包含 32`BoxView`元素但使用隐式`Style`包括`AbsoluteLayout.LayoutFlags`属性保留到最少的标记。

类名、 一个点和属性名称组成的 XAML 中的特性是*始终*附加的可绑定属性。

## <a name="overlays"></a>叠加

你可以使用`AbsoluteLayout`构造*覆盖*，其中介绍了与其他控件，页面可能是为了防止用户与页上的正常控件进行交互。

[ **SimpleOverlay** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay)示例演示这种方法，并且还演示[ `ProgressBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/)，它会显示在程序完成向其的范围任务。

## <a name="some-fun"></a>一些有趣

[ **DotMatrixClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock)示例显示具有模拟的 5 全天候点矩阵显示的当前时间。 每个圆点是`BoxView`（有其中 228） 大小和上放置`AbsoluteLayout`。

[![三重的点矩阵时钟的屏幕截图](images/ch14fg08-small.png "点矩阵时钟")](images/ch14fg08-large.png#lightbox "点矩阵时钟")

[ **BouncingText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText)程序进行动画处理两个`Label`反弹在屏幕的水平和垂直的对象。



## <a name="related-links"></a>相关链接

- [章 14 的全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [章 14 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
