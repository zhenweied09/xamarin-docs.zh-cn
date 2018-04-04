---
title: 第 10 章的摘要。 XAML 标记扩展
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 3223bc20448bd8354e84dc67ee64a8dc435f7667
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>第 10 章的摘要。 XAML 标记扩展

通常情况下，XAML 分析器将任何字符串作为属性值设置为基于标准转换的基本的.NET 数据类型，属性的类型的转换或[ `TypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverter/)派生附加到属性或具有其类型[`TypeConverterAttribute`](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverterAttribute/).

但有时却很方便地设置属性，从不同源，例如，一个字典或的静态属性或字段的值中的项或从某种类型的计算。

这是个作业的*XAML 标记扩展*。 不管名称如何，XAML 标记扩展是*不*to XML 扩展。 XAML 始终是合法的 XML。

## <a name="the-code-infrastructure"></a>代码基础结构

XAML 标记扩展是一个类，实现[ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/)接口。 此类通常有单词`Extension`在其名称的末尾但通常会显示在 XAML 中使用不带该后缀。

下面的 XAML 标记扩展支持 XAML 的所有实现：

- `x:Static` 支持 [`StaticExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/)
- `x:Reference` 支持 [`ReferenceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/)
- `x:Type` 支持 [`TypeExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/)
- `x:Null` 支持 [`NullExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/)
- `x:Array` 支持 [`ArrayExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/)

这些四个 XAML 标记扩展支持很多 XAML，包括 Xamarin.Forms 的实现：

- `StaticResource` 支持 [`StaticResourceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticResourceExtension/)
- `DynamicResource` 支持 [`DynamicResourceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.DynamicResourceExtension/)
- `Binding` 支持[ `BindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/)&mdash;中所述[章 16。数据绑定](#chapter16)
- `TemplateBinding` 支持[ `TemplateBindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TemplateBindingExtension/)&mdash;未涵盖书中

其他的 XAML 标记扩展包含在门户中的 Xamarin.Forms [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/):

- [`ConstraintExpression`](https://developer.xamarin.com/api/type/Xamarin.Forms.ConstraintExpression/)&mdash;未涵盖书中

## <a name="accessing-static-members"></a>访问静态成员

使用[ `x:Static` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/)元素将属性设置为公共的静态属性、 字段或枚举成员的值。 设置[ `Member` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.StaticExtension.Member/)静态成员的属性。 通常更轻松地指定`x:Static`和在大括号中的成员名称。 名称`Member`属性不需要将包括，只是该成员本身。 此常见语法所示[ **SharedStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics)示例。 在中定义的静态字段本身[ `AppConstants` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs)类。 此技术，可建立通过计划使用常量。

与其他 XML 命名空间声明，你可以引用的公共静态属性、 字段或枚举成员在.NET framework 中，定义中所示[ **SystemStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics)示例.

## <a name="resource-dictionaries"></a>资源字典

`VisualElement`类定义一个名为属性[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) ，你可以设置为类型的对象[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。 在 XAML 中，你可以将项存储在此字典并确定它们与`x:Key`属性。 在对项的所有引用之间共享资源字典中存储的项。

### <a name="staticresource-for-most-purposes"></a>否则，在大多数情况

在大多数情况下将使用[ `StaticResource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticResourceExtension/)标记扩展，可从资源字典中，引用项目，如所示： [ **ResourceSharing** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing)示例. 你可以使用`StaticResourceExtension`元素或`StaticResource`大括号内：

[![三重的资源共享的屏幕截图](images/ch10fg03-small.png "资源共享")](images/ch10fg03-large.png#lightbox "资源共享")

不要混淆`x:Static`标记扩展和`StaticResource`标记扩展。

### <a name="a-tree-of-dictionaries"></a>字典的一个树

当 XAML 分析器遇到`StaticResource`，它开始搜索匹配的键的可视化树向上并随后查找`ResourceDictionary`中应用程序的`App`类。 这允许更深入地可视化树中重写的可视化树中较高的资源字典中的资源字典中的项。 此进行了演示[ **ResourceTrees** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees)示例。

### <a name="dynamicresource-for-special-purposes"></a>用于特殊用途的 DynamicResource

`StaticResource`标记扩展会导致要可视化树生成期间时从字典中检索的项`InitializeComponent`调用。 一种替代方法`StaticResource`是[ `DynamicResource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.DynamicResourceExtension/)，其维护字典键的链接和项引用的密钥的更改时，更新目标。

之间的差异`StaticResource`和`DynamicResource`中演示[ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)示例。

设置的属性`DynamicResource`必须由可绑定属性中所述[第 11 章可绑定的基础结构](chapter11.md)。

## <a name="lesser-used-markup-extensions"></a>使用较小的标记扩展

使用[ `x:Null` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/)标记扩展以将属性设置为`null`。

使用[ `x:Type` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/)标记扩展以将属性设置为.NET`Type`对象。

使用[ `x:Array` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/)以定义一个数组。 通过设置中指定的数组成员的类型 [`Type`] 属性设置为`x:Type`标记扩展。

## <a name="a-custom-markup-extension"></a>自定义标记扩展

你可以通过编写实现的类来创建你自己的 XAML 标记扩展[ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/)与[ `ProvideValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue/p/System.IServiceProvider/)方法。

[ `HslColorExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs)类可满足这些要求。 它会创建类型的值`Color`的名为的属性的值为基础的`H`， `S`， `L`，和`A`。 此类是一个名为的 Xamarin.Forms 库中的第一个项[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)构建并使用本书之中。

[ **CustomExtensionDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo)示例演示如何以引用此库，并使用自定义标记扩展。



## <a name="related-links"></a>相关链接

- [第 10 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [第 10 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
