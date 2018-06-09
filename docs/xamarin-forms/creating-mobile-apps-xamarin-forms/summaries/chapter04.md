---
title: 第 4 章的摘要。 滚动堆栈
description: 使用 Xamarin.Forms 创建移动应用： 第 4 章的摘要。 滚动堆栈
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 1af3632d226ce894c1d856f665d6482f45d61f16
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241137"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>第 4 章的摘要。 滚动堆栈

本章主要用于引入的概念*布局*，这是类和技术 Xamarin.Forms 使用来组织页面上的多个视图的可视显示总体术语。

布局涉及多个类派生自[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)和[ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)。 本章重点[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)。

本章还引入了是[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)， [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/)，和[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)类。

## <a name="stacks-of-views"></a>视图的堆栈

[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 派生自`Layout<View>`和继承[ `Children` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)类型的属性`IList<View>`。 向此集合中，添加多个视图项和`StackLayout`水平或垂直堆栈中显示它们。

设置[ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/)属性`StackLayout`指向成员的[ `StackOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackOrientation/)枚举，任一[ `Vertical` ](https://developer.xamarin.com/api/field/Xamarin.Forms.StackOrientation.Vertical/)或[`Horizontal`](https://developer.xamarin.com/api/field/Xamarin.Forms.StackOrientation.Horizontal/). 默认值为 `Vertical`。

设置[ `Spacing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Spacing/)属性`StackLayout`到`double`要指定子级之间的间距的值。 默认值为 6。

在代码中，你可以将项添加到`Children`集合`StackLayout`中`for`或`foreach`循环中所示[ **ColorLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop)示例中，也可以初始化`Children`详见单个视图与列表集合[ **ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList)。 子级必须派生自`View`，但可以包括其他`StackLayout`对象。

## <a name="scrolling-content"></a>滚动的内容

如果`StackLayout`包含太多的子级显示在页中，您可以放入`StackLayout`中[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)以允许滚动。

设置[ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Content/)属性`ScrollView`到你想要向下滚动视图。 这通常是`StackLayout`，但它可以是任何视图。

设置[ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Orientation/)属性`ScrollView`指向成员的[ `ScrollOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/)属性， [ `Vertical` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Vertical/)， [ `Horizontal` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Horizontal/)，或[ `Both` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Both/)。 默认值为 `Vertical`。 如果内容`ScrollView`是`StackLayout`，两个方向应保持一致。

[ **ReflectedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors)示例演示如何使用`ScrollView`和`StackLayout`以显示可用的颜色。 此示例还演示如何使用.NET 反射来获取所有的公共静态属性和字段`Color`而无需显式列出它们的结构。

## <a name="the-expands-option"></a>展开此选项

当`StackLayout`堆栈其子项，每个子占用的总高度内特定槽`StackLayout`这对此子级的大小和的设置取决于其`HorizontalOptions`和`VerticalOptions`属性。 这些属性分配类型的值[ `LayoutOptions` ](http://developer.xamstage.com/api/type/Xamarin.Forms.LayoutOptions/)。

`LayoutOptions`结构定义两个属性：

- [`Alignment`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Alignment/) 枚举类型的[ `LayoutAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutAlignment/)具有四个成员[ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Start/)， [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Center/)， [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.End/)，和 [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Fill/)
- [`Expands`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/) 类型 `bool`

为方便起见，`LayoutOptions`结构还定义类型的八个静态只读字段`LayoutOptions`包含两个实例属性的所有组合：

- [`LayoutOptions.Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`LayoutOptions.Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`LayoutOptions.End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`LayoutOptions.StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`LayoutOptions.CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`LayoutOptions.EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

以下讨论涉及`StackLayout`默认垂直方向。 水平`StackLayout`类似。

对于垂直`StackLayout`、`HorizontalOptions`设置确定如何子水平放置的宽度内`StackLayout`。 `Alignment`设置`Start`， `Center`，或`End`使为水平方向不受约束的子。 子确定其自己的宽度，并将位于左、 居中或右侧`StackLayout`。 `Fill`选项使水平要约束的子和填充的宽度`StackLayout`。

对于垂直`StackLayout`、 每个子级是垂直不受约束和获取垂直槽具体取决于子级的高度，在这种情况下`VerticalOptions`设置无关。

如果垂直`StackLayout`本身是不受约束&mdash;即如果其`VerticalOptions`设置`Start`， `Center`，或`End`，然后的高度`StackLayout`是子级的总高度。

但是，如果垂直`StackLayout`垂直约束&mdash;如果其`VerticalOptions`设置`Fill`&mdash;然后的高度`StackLayout`将其容器，它可能大于总的高度及其所有子级的高度。 如果是这样，并且如果至少一个子有`VerticalOptions`设置，并`Expands`标志的`true`，然后中的额外空间`StackLayout`具有所有这些子项之间平均分配`Expands`标志的`true`。 子级的总高度然后等于的高度`StackLayout`，和`Alignment`属于`VerticalOptions`设置确定子如何垂直放置在其插槽中。

此进行了演示[ **VerticalOptionsDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo)示例。

## <a name="frame-and-boxview"></a>框架和字数

这两个矩形视图通常用于演示目的。

[ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/)视图显示另一个视图，可以如布局矩形边框`StackLayout`。 `Frame` 继承[ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/)属性从[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)设置为视图以显示在`Frame`。 `Frame`默认为透明。 设置以下三个属性，以自定义帧的外观：

- [ `OutlineColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.OutlineColor/)属性以使其可见。 是很常见设置`OutlineColor`到`Color.Accent`时你不知道基础的配色方案。
- [ `HasShadow` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.HasShadow/)属性可以设置为`true`在 iOS 设备上显示黑色阴影。
- 设置[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)属性`Thickness`值帧和帧之间留出空间的内容。 默认值为各边的 20 个单位。

`Frame`具有默认`HorizontalOptions`和`VerticalOptions`值`LayoutOptions.Fill`，这意味着，`Frame`将填充其容器。 与其他设置的大小`Frame`基于其内容的大小。

`Frame`中演示[ **FramedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText)示例。

[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)显示由指定的颜色的矩形区域其[ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/)属性。

如果`BoxView`约束 (其`HorizontalOptions`和`VerticalOptions`属性具有其默认设置`LayoutOptions.Fill`)，则`BoxView`为其填充的可用空间。 如果`BoxView`是不受约束 (与`HorizontalOptions`和`LayoutOptions`设置`Start`， `Center`，或`End`)，它具有 40 单元方形的默认维度。 A`BoxView`可以限制于一个维度和另一部分中不受约束。

通常情况下，你将设置[ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)和[ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/)属性`BoxView`以授予它具有特定大小。 说明了这一点[ **SizedBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView)示例。

你可以使用多个实例的`StackLayout`组合`BoxView`和几个`Label`实例`Frame`以显示具有特定颜色，并将这些视图中的每个`StackLayout`中`ScrollView`创建具吸引力列表中显示的颜色[ **ColorBlocks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks)示例：

[![颜色块的三个屏幕截图](images/ch04fg11-small.png "颜色列表")](images/ch04fg11-large.png#lightbox "列表的颜色")

## <a name="a-scrollview-in-a-stacklayout"></a>在 StackLayout ScrollView？

将`StackLayout`中`ScrollView`常见，但将`ScrollView`中`StackLayout`方法也有时很方便。 从理论上讲，这不应是可能因为垂直的子级`StackLayout`是垂直不受约束。 但`ScrollView`必须垂直约束。 必须为其提供一个特定的高度，以便它能够对滚动然后确定其子级的大小。

技巧就是为提供`ScrollView`子级`StackLayout``VerticalOptions`设置`FillAndExpand`。 此进行了演示[ **BlackCat** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat)示例。

**BlackCat**示例还演示如何定义和访问嵌入在可移植类库 (PCL) 的程序资源。 这还可以实现与共享资产项目 (SAPs)，但是该过程有点麻烦一些，作为[ **BlackCatSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap)示例演示如何。



## <a name="related-links"></a>相关链接

- [第 4 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [第 4 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [第 4 章 F # 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
