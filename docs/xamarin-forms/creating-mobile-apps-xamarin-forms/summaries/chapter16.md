---
title: 章 16 的摘要。 数据绑定
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 0f200e0c482402813ac7051255dd7c27da93d6dc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-16-data-binding"></a>章 16 的摘要。 数据绑定

程序员通常会发现自己编写事件处理程序检测到一个对象的属性已更改，并使用它在另一个对象的属性的值更改。 此过程可以使用的技术自动*数据绑定*。 数据绑定通常在 XAML 中定义，并且成为定义的用户界面的一部分。

通常，这些数据绑定连接到基础数据的用户界面对象。 这是一种技术，在更探讨[**第 18 章。MVVM**](chapter18.md). 但是，数据绑定也可以连接两个或多个用户界面元素。 大多数早期本章中的数据绑定的示例演示此技术。

## <a name="binding-basics"></a>绑定基础知识

在数据绑定中涉及多个属性、 方法和类：

- [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/)类派生自[ `BindingBase` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingBase/)并封装许多特征的数据绑定
- [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)属性定义由[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)类
- [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)方法也由定义[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)类
- [ `BindableObjectExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObjectExtensions/)类定义三个其他`SetBinding`方法

以下两个类的绑定支持 XAML 标记扩展：

- [`BindingExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) 支持`Binding`标记扩展
- [`ReferenceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/) 支持`x:Reference`标记扩展

数据绑定涉及两个接口：

- [`INotifyPropertyChanged`](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) 在`System.ComponentModel`命名空间是用于实现的属性发生更改时发出的通知
- [`IValueConverter`](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) 用于定义将值从一种类型转换为另一个数据绑定中的较小类

数据绑定连接同一个对象或 （通常） 两个不同的对象的两个属性。 这两个属性被称为*源*和*目标*。 通常，源属性中的更改会导致在目标属性中，发生的更改，但有时方向变为相反方向。 而不考虑：

- *目标*必须由支持属性 [`BindableProperty`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- *源*属性通常是成员的实现的类 [`INotifyPropertyChanged`](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/)

一个类以实现`INotifyPropertyChanged`激发[ `PropertyChanged` ](https://developer.xamarin.com/api/event/System.ComponentModel.INotifyPropertyChanged.PropertyChanged/)属性更改值时的事件。 `BindableObject` 实现`INotifyPropertyChanged`和自动触发`PropertyChanged`事件属性作为后盾`BindableProperty`更改值，但你可以编写您自己的类实现`INotifyPropertyChanged`而不用从派生`BindableObject`。

## <a name="code-and-xaml"></a>代码和 XAML

[ **OpacityBindingCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode)示例演示如何在代码中设置数据绑定：

- 源是`Value`属性 `Slider`
- 目标是`Opacity`属性 `Label`

通过设置连接两个对象`BindingContext`的`Label`对象传递给`Slider`对象。 这两个属性连接通过调用[ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObjectExtensions.SetBinding/p/Xamarin.Forms.BindableObject/Xamarin.Forms.BindableProperty/System.String/)上的扩展方法`Label`引用`OpacityProperty`可绑定的属性和`Value`属性`Slider`表示为字符串。

操作`Slider`，则可能导致`Label`的视图内外淡入。

[ **OpacityBindingXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml)是使用在 XAML 中设置数据绑定相同的程序。 `BindingContext`的`Label`设置为`x:Reference`标记扩展引用`Slider`，和`Opacity`属性`Label`设置为`Binding`具有标记扩展其[ `Path`](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/)属性引用`Value`属性`Slider`。

## <a name="source-and-bindingcontext"></a>源和 BindingContext

[ **BindingSourceCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode)示例演示在代码中的另一种方法。 A`Binding`对象通过将创建[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Source/)属性`Slider`对象和[ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/)为"Value"的属性。 [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)方法`BindableObject`然后调用`Label`对象。

[ `Binding`构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Binding.Binding/p/System.String/Xamarin.Forms.BindingMode/Xamarin.Forms.IValueConverter/System.Object/System.String/System.Object/)可能还使用以定义`Binding`对象。

[ **BindingSourceXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml)示例演示在 XAML 中的可比较的技术。 `Opacity`属性`Label`设置为`Binding`具有标记扩展[ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/)设置为`Value`属性和[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Source/)设置为嵌入`x:Reference`标记扩展。

总之，有两种方式来引用绑定源对象：

- 通过`BindingContext`目标属性
- 通过`Source`属性`Binding`对象本身

如果同时指定了，第二个优先。 利用`BindingContext`是传播在可视化树。 这是*非常*方便如果多个目标属性绑定到相同的源对象。

[ **WebViewDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo)程序演示了使用此方法[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)元素。 两个`Button`的导航元素向后和向前继承`BindingContext`从引用其父`WebView`。 `IsEnabled`的两个按钮的属性然后具有简单`Binding`目标按钮的标记扩展`IsEnabled`属性基于的设置[ `CanGoBack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.CanGoBack/)和[ `CanGoForward`](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.CanGoForward/)的只读属性`WebView`。

## <a name="the-binding-mode"></a>绑定模式

设置[ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.Mode/)属性`Binding`指向成员的[ `BindingMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingMode/)枚举：

- [`OneWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWay/) 以便源属性中的更改会影响目标
- [`OneWayToSource`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWayToSource/) 以便中的目标属性的更改会影响源
- [`TwoWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/) 以便在源和目标的更改会影响每个其他
- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.Default/) 若要使用[ `DefaultBindingMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableProperty.DefaultBindingMode/)时指定目标`BindableProperty`已创建。 如果未指定，默认值是`OneWay`正常的可绑定属性和`OneWayToSource`只读可绑定属性。

很可能通常是 MVVM 方案中的数据绑定的目标的属性具有`DefaultBindingMode`的`TwoWay`。 这些是：

- `Value` 属性`Slider`和 `Stepper`
- `IsToggled` 属性 `Switch`
- `Text` 属性`Entry`， `Editor`，和 `SearchBar`
- `Date` 属性 `DatePicker`
- `Time` 属性 `TimePicker`

[ **BindingModes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes)示例演示在目标是数据绑定的四个绑定模式`FontSize`属性`Label`的源是`Value`属性`Slider`。 这允许每台`Slider`来控制相应的字体大小`Label`。 但是`Slider`不初始化元素，因为`DefaultBindingMode`的`FontSize`属性是`OneWay`。

[ **ReverseBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding)示例会将绑定设置上`Value`属性`Slider`引用`FontSize`每个属性`Label`。 这似乎是向后，但它可更好地在初始化字词`Slider`元素因为`Value`属性`Slider`具有`DefaultBindingMode`的`TwoWay`。

[![三重的反向绑定的屏幕截图](images/ch16fg06-small.png "反向绑定")](images/ch16fg06-large.png#lightbox "反向绑定")

这是类似于绑定 MVVM 中的定义方式，你将使用此类型的绑定频繁。

## <a name="string-formatting"></a>字符串格式设置

当目标属性属于类型`string`，你可以使用[ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.StringFormat/)属性定义的`BindingBase`将源转换为`string`。 设置`StringFormat`.NET 格式设置字符串，你将使用静态属性[ `String.Format` ](https://developer.xamarin.com/api/member/System.String.Format/p/System.String/System.Object/)格式显示对象。 在使用此格式设置字符串在标记扩展时外, 侧它单引号以便大括号不会被误认为嵌入的标记扩展。

[ **ShowViewValues** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues)示例演示如何使用`StringFormat`在 XAML 中。

[ **WhatSizeBindings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings)示例演示如何通过绑定到显示页的大小`Width`和`Height`属性`ContentPage`。

## <a name="why-is-it-called-path"></a>为什么它称为"Path"？

[ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/)属性`Binding`因此称作，因为它可以是属性和索引器用句点分隔的一系列。 [ **BindingPathDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos)示例显示几个示例。

## <a name="binding-value-converters"></a>绑定值转换器

不同类型的绑定源和目标属性时，你可以使用的绑定转换器的类型之间进行转换。 这是一个类以实现[ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/)接口，并包含两个方法： [ `Convert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IValueConverter.Convert/p/System.Object/System.Type/System.Object/System.Globalization.CultureInfo/)将源转换到目标，和[ `ConvertBack` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IValueConverter.ConvertBack/p/System.Object/System.Type/System.Object/System.Globalization.CultureInfo/)若要将目标转换到源。

[ `IntToBoolConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库是一个示例，用于转换`int`到`bool`。 演示通过[ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler)示例中，后者只允许`Button`如果至少一个字符键入`Entry`。

[ `BoolToStringConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs)类将`bool`到`string`并定义两个属性以指定应为返回什么文本`false`和`true`值。
[ `BoolToColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs)类似。 [ **SwitchText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText)示例演示了如何使用这些两个转换器用基于的不同颜色显示不同的文本`Switch`设置。

泛型[ `BoolToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs)可以替换`BoolToStringConverter`和`BoolToColorConverter`和作为通用`bool`-到-对象转换器的任何类型。

## <a name="bindings-and-custom-views"></a>绑定和自定义视图

你可以简化使用数据绑定的自定义控件。 [ `NewCheckBox.cs` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs)代码文件定义`Text`， `TextColor`， `FontSize`， `FontAttributes`，和`IsChecked`属性，但没有在所有有关控件的视觉对象的逻辑。
改为[ `NewCheckBox.cs.xaml` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml)文件包含通过数据绑定控件的视觉对象的所有标记上`Label`元素基于在代码隐藏文件中定义的属性。

[ **NewCheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo)示例演示如何`NewCheckBox`自定义控件。



## <a name="related-links"></a>相关链接

- [章 16 的全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [章 16 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
