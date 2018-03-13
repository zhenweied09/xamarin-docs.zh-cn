---
title: "第 7 章的摘要。 与代码的 XAML"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 1104f7576cabfed9988154f3b6a8beb429136fb3
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>第 7 章的摘要。 与代码的 XAML

Xamarin.Forms 支持基于 XML 的标记语言称为可扩展应用程序标记语言或 XAML （读作"zammel"）。 XAML 的另一种向 C# 和定义用户界面元素之间的绑定和基础数据中定义的 Xamarin.Forms 应用程序的用户界面的布局。

## <a name="properties-and-attributes"></a>属性和特性

Xamarin.Forms 类和结构变成 XML 元素，在 XAML 中，而这些类和结构的属性变得 XML 属性。 要实例化在 XAML 中，类通常必须具有公共无参数构造函数。 在 XAML 中设置任何属性必须具有公共`set`访问器。

有关的基本数据类型的属性 (`string`， `double`， `bool`，依此类推)，XAML 分析器使用标准`TryParse`方法将属性设置转换为这些类型。 XAML 分析器可以方便地处理枚举类型，并且它枚举成员的情况下组合的枚举类型会被标记为`Flags`属性。

若要帮助 XAML 分析器，更复杂的类型 （或这些类型的属性） 可以包含[ `TypeConverterAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverterAttribute/)标识派生自的类[ `TypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverter/)支持从转换对这些类型的字符串值。 例如， [ `ColorTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColorTypeConverter/)将转换到颜色名称和字符串，如"#rrggbb"`Color`值。

## <a name="property-element-syntax"></a>属性元素语法

在 XAML 中，类和从其创建的对象表示为 XML 元素。 这些被称为*对象元素*。 这些对象的大部分属性表示为 XML 属性。 这些应用程序称为*属性特性*。

有时属性必须设置为无法表示为一个简单的字符串的对象。 在这种情况下，XAML 支持调用的标记*属性元素*组成的类名称与句点分隔的属性名称。 然后，对象元素可以出现在属性元素标记的对。

## <a name="adding-a-xaml-page-to-your-project"></a>将 XAML 页添加到你的项目

首次创建，或者可以将 XAML 页添加到现有项目时，Xamarin.Forms 可移植类库可以包含一个 XAML 页。 在添加新项对话框，选择 XAML 页中，是指项或`ContentPage`和 XAML。 (不`ContentView`。)

创建两个文件： filename 扩展.xaml 的 XAML 文件和 C# 文件扩展名。 xaml.cs。 C# 文件通常称为*代码隐藏*的 XAML 文件。 代码隐藏文件是派生自的分部类定义`ContentPage`。 在生成时，分析 XAML 和另一个分部类定义生成相同的类。 此生成的类包含一个名为方法`InitializeComponent`从代码隐藏文件的构造函数调用。

在运行时，在结束`InitializeComponent`调用时，所有 XAML 文件的元素已实例化并初始化就像它们具有已在 C# 代码中创建。

XAML 文件中的根元素是`ContentPage`。 根标记包含至少两个 XML 命名空间声明，一个用于 Xamarin.Forms 元素，其他定义`x`元素和属性固有的所有 XAML 实现的前缀。 根标记还包含`x:Class`属性，指示的命名空间和派生自的类名称`ContentPage`。 这与代码隐藏文件中的命名空间和类名称相匹配。

通过以下方式演示 XAML 和代码的组合[ **CodePlusXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)示例。

## <a name="the-xaml-compiler"></a>XAML 编译器

Xamarin.Forms 具有 XAML 编译器，但其使用基于使用的是可选的[ `XamlCompilationAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/)。 如果尚未编译 XAML，在生成期间，分析 XAML 和 XAML 文件嵌入在 PCL 中，其中还进行分析，在运行时。 如果编译 XAML 后，生成过程将 XAML 转换为二进制格式，和运行时处理更有效。

## <a name="platform-specificity-in-the-xaml-file"></a>XAML 文件中的平台特征

在 XAML 中， [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)类可以用于选择依赖于平台的标记。 这是一个泛型类，必须要实例化的`x:TypeArguments`匹配的目标类型属性。 [ `OnIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnIdiom%3CT%3E/)类越来越少地是类似，但使用。

使用`OnPlatform`自书已发布以来已更改。 它最初具有名为的属性使用结合`iOS`， `Android`，和`WinPhone`。 现在用于子[ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/)对象。 设置[ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/)为一致的公共字符串属性`const`字段[ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)类。 设置[ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Value/)属性的值与一致`x:TypeArguments`属性`OnPlatform`标记。

`OnPlatform` 中所示[ **ScaryColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList)示例中，因此调用因为它包含块的几乎完全相同的 XAML。 此重复的标记存在提供的建议方法应可将其减少。

## <a name="the-content-property-attributes"></a>内容属性特性

某些属性元素频繁出现，如`<ContentPage.Content>`的根元素的标记`ContentPage`，或`<StackLayout.Children>`环绕的子级的标记`StackLayout`。

允许每个类来标识一个属性与[ `ContentPropertyAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPropertyAttribute/)类上。 此属性，则不需要的属性元素标记。 `ContentPage` 定义作为其内容属性`Content`，和`Layout<T>`(从其类`StackLayout`派生) 作为其内容属性定义`Children`。 这些属性元素标记不是必需的。

属性元素`Label`是`Text`。

## <a name="formatted-text"></a>带格式文本

[ **TextVariations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations)示例包含几个示例的设置`Text`和`FormattedText`属性`Label`。 在 XAML 中，`Span`对象显示为子级的`FormattedString`对象。

 如果将多行字符串设置为`Text`属性，行尾字符都转换为空格字符，但时多行字符串中显示的内容为保留的行尾字符`Label`或`Label.Text`标记：

 [![共享的文本变体的三个屏幕截图](images/ch07fg03-small.png "格式化文本变体")](images/ch07fg03-large.png#lightbox "格式化文本变体")



## <a name="related-links"></a>相关链接

- [第 7 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [第 7 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [第 7 章 F # 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
