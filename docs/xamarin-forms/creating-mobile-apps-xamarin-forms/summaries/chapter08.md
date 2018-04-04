---
title: 第 8 章的摘要。 代码和协调的 XAML
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: ffd2d508e99508309ec07c6bc65c8d716427bdff
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>第 8 章的摘要。 代码和协调的 XAML

此章节介绍 XAML 更深入地，以及特别代码和 XAML 交互的方式。

## <a name="passing-arguments"></a>传递自变量

在一般情况下，在 XAML 中实例化的类必须具有公共的无参数构造函数;生成的对象初始化通过属性设置。 但是，有两种方法可以实例化和初始化对象。

尽管这些是通用的技术，它们主要用于与 MVVM 查看模型。

### <a name="constructors-with-arguments"></a>带自变量的构造函数

[ **ParameteredConstructorDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo)示例演示如何使用`x:Arguments`标记指定构造函数自变量。 必须由，该值指示参数的类型的元素标记分隔这些自变量。 为了基本的.NET 数据类型，提供了以下标记：

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

### <a name="can-i-call-methods-from-xaml"></a>可以从 XAML 调用方法？

[ **FactoryMethodDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo)示例演示如何使用`x:FactoryMethod`元素，用于指定调用以创建对象的工厂方法。 此类的工厂方法必须是公共的且静态的并且它必须创建在其中定义的类型的对象。 (例如[ `Color.FromRgb` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Double/System.Double/System.Double/)) 方法限定，因为它是公用的且静态，并返回类型的值`Color`。)工厂方法的自变量内指定`x:Arguments`标记。

## <a name="the-xname-attribute"></a>X:name 特性

`x:Name`属性允许在要为指定的名称 XAML 中实例化的对象。 这些名称的规则都是与 C# 变量名相同。 以下的返回`InitializeComponent`的构造函数中调用，代码隐藏文件可以引用这些访问相应的 XAML 元素的名称。 名称被实际转换 XAML 分析器到生成的分部类中的私有字段。

[ **XamlClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock)示例演示如何使用`x:Name`若要允许保留两个代码隐藏文件`Label`在更新的当前日期和时间的 XAML 中定义的元素。

相同的名称不能用于同一页面上的多个元素。 这是一个特定的问题，如果你使用`OnPlatform`创建并行命名对象，为每个平台。 [ **PlatformSpecificLabele** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels)示例演示如何更好的方法来这样做些什么。

## <a name="custom-xaml-based-views"></a>自定义的基于 XAML 的视图

有多种方法，以避免重复的 XAML 中的标记。 一个常用的方法是创建一个新的基于 XAML 的该类派生自[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)。 此技术在中演示[ **ColorViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList)示例。 `ColorView`类派生自`ContentView`显示具有特定颜色和其名称，请同时`ColorViewListPage`类派生自`ContentPage`照常和显式创建的 17 实例`ColorView`。

访问`ColorView`在 XAML 中的类需要另一个 XML 命名空间声明，通常名为`local`同一程序集中的类。

## <a name="events-and-handlers"></a>事件和处理程序

可以将事件分配给事件处理程序在 XAML 中，但必须在代码隐藏文件中实现事件处理程序本身。 [ **XamlKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad)演示如何构建一个键盘用户界面，在 XAML 中的以及如何实现`Clicked`代码隐藏文件中的处理程序。

## <a name="tap-gestures"></a>点击手势

任何`View`对象可以获取触摸屏输入，并根据该输入生成事件。 `View`类定义[ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/)可以包含派生自的类的一个或多个实例的集合属性[ `GestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)。

[ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)生成[ `Tapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.TapGestureRecognizer.Tapped/)事件。 [ **MonkeyTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap)程序演示如何将附加`TapGestureRecognizer`的对象添加到四个`BoxView`元素来创建冒充游戏：

[![三重屏幕截图的 monkey tap](images/ch08fg07-small.png "模仿游戏")](images/ch08fg07-large.png#lightbox "模仿游戏")

但是**MonkeyTap**程序真正需要声音。 (请参阅[下一章](chapter09.md)。)



## <a name="related-links"></a>相关链接

- [第 8 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [第 8 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [第 8 章 F # 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
