---
title: 章 12 的摘要。 样式
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: bb26c5d93bc9945ad43ed62d7feba2bc851e510e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-12-styles"></a>章 12 的摘要。 样式

Xamarin.Forms，这在样式允许多个视图来共享属性设置的集合。 这减少了标记，并使维护一致的直观主题。

样式几乎始终定义并在标记中使用。 类型的对象[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)资源字典中实例化并将设置为[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)的可视元素使用属性`StaticResource`或`DyanamicResource`标记扩展。

## <a name="the-basic-style"></a>基本的样式

A`Style`要求其[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)设置为适用于的视觉对象的类型。 当`Style`实例化在资源字典中 （如很常见） 它还要求`x:Key`属性。

`Style`有类型的内容属性[ `Setters` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Setters/)，这是一套[ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)对象。 每个`Setter`将相关联[ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/)与[ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/)。

在 XAML 中`Property`设置为 CLR 属性的名称 (如`Text`属性`Button`) 但样式的属性必须由可绑定属性。 此外，必须指示的类中定义属性`TargetType`设置，或通过此类继承。

你可以指定`Value`设置使用的属性元素`<Setter.Value>`。 这允许您将`Value`到在文本字符串中，或无法表示的对象`OnPlatform`对象，或对象实例化使用`x:Arguments`或`x:FactoryMethod`。 `Value`属性还可以设置与`StaticResource`到字典中的另一个项的表达式。

[ **BasicStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle)程序演示基本语法，并演示如何引用`Style`与`StaticResource`标记扩展：

[![三重屏幕快照的基本样式](images/ch12fg01-small.png "基本样式")](images/ch12fg01-large.png#lightbox "基本样式")

`Style`对象和在中创建任何对象`Style`对象作为`Value`设置引用的所有视图之间共享`Style`。 `Style`不能包含任何内容不能共享，如`View`派生。

事件处理程序不能设置`Style`。 `GestureRecognizers`属性不能在中设置`Style`因为它不支持可绑定属性。

## <a name="styles-in-code"></a>在代码中的样式

虽然不常见，但可以实例化和初始化`Style`代码中的对象。 说明了这一点通过[ **BasicStyleCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode)示例。

## <a name="style-inheritance"></a>样式继承

`Style` 具有[ `BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/)属性可设置为`StaticResource`引用另一种样式的标记扩展。 这允许进行继承自上一个样式，以及添加或替换属性设置的样式。 [ **StyleInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance)示例演示这一操作。

如果`Style2`基于`Style1`、`TargetType`的`Style2`必须是相同`Style1`或派生自`Style1`。 在其中的资源字典`Style1`存储必须为相同的资源字典`Style2`或更高版本的可视化树中的资源字典。

## <a name="implicit-styles"></a>隐式样式

如果`Style`在资源字典不具有`x:Key`属性设置，它自动分配有一个字典键与`Style`对象变为*隐式样式*。 视图不`Style`设置，并且其类型与匹配`TargetType`完全会发现该样式为[ **ImplicitStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle)示例演示如何。

隐式样式可派生自此`Style`与`x:Key`设置，但不是采用相反的方法。 不能显式引用隐式样式。

你可以实现三种类型的样式具有层次结构和`BasedOn`:

- 从上定义的样式`Application`和`Page`下布局的可视化树中较低级别上定义的样式。
- 如为基类，这些类定义的样式从`VisualElement`和`View`对为特定的类定义的样式。
- 从具有显式字典键对隐式样式的样式。

这些层次结构所示[ **StyleHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy)示例。

## <a name="dynamic-styles"></a>动态样式

可以通过引用的资源字典中的样式`DynamicResource`而非`StaticResource`。 这使得样式*动态样式*。 如果该样式将被替换将在资源字典中的另一种样式为相同的键，引用与该样式的视图`DynamicResource`自动更改。 此外，将导致与指定的键的字典条目没有`StaticResource`以引发异常但未`DynamicResource`。

你可以使用此方法动态更改样式或作为主题[ **DynamicStyles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles)示例演示如何。

但是，不能设置`BasedOn`属性`DynamicResource`构成扩展因为`BasedOn`不支持可绑定属性。 若要动态派生样式，请不要设置`BasedOn`。 与此相反，设置[ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/)你想要派生自的样式的字典键的属性。 [ **DynamicStylesInheritance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh)示例演示这种方法。

## <a name="device-styles"></a>设备样式

[ `Device.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)嵌套的类定义使用六个样式的 12 个静态只读字段`TargetType`的`Label`可用于常见的文本用法类型。

6 个这些字段的类型是`Style`您可以将直接为`Style`在代码中的属性：

- [`BodyStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/)
- [`TitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyle/)
- [`SubtitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyle/)
- [`CaptionStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyle/)
- [`ListItemTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyle/)
- [`ListItemDetailTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyle/)

其他六个字段属于类型`string`和可用作动态样式的字典键：

- [`BodyStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyleKey/) 等于"BodyStyle"
- [`TitleStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyleKey/) 等于"TitleStyle"
- [`SubtitleStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyleKey/) 等于"SubtitleStyle"
- [`CaptionStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyleKey/) 等于"CaptionStyle"
- [`ListItemTextStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyleKey/) equal to "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyleKey/) equal to "ListItemDetailTextStyle"

这些样式所示[ **DeviceStylesList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList)示例。



## <a name="related-links"></a>相关链接

- [第 12 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [第 12 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [样式](~/xamarin-forms/user-interface/styles/index.md)
