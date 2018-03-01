---
title: "第 18 章的摘要。 MVVM"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: fa62ff952a4a8916a0c9603157d14948119d243d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-18-mvvm"></a>第 18 章的摘要。 MVVM

设计应用程序的最佳方式之一是通过将用户界面与基础代码，有时称为分离*业务逻辑*。 存在多种技术，但针对基于 XAML 的环境的一个被称为模型-视图-视图模型或 MVVM。

## <a name="mvvm-interrelationships"></a>MVVM 相互关系

MVVM 应用程序具有三个层：

- 该模型可提供基础数据，有时会通过文件或 web 访问
- 视图是用户界面或演示文稿层，在 XAML 中通常实现
- ViewModel 连接模型和视图

模型是未知的 ViewModel 和 ViewModel 是未知的视图。 这三个层通常将使用下列机制互相连接：

![视图、 ViewModel 和视图](images/ch18fg03.png "MVVM")

在许多较小的程序 （和更大的），通常该模型不存在或其功能集成到视图模型。

## <a name="viewmodels-and-data-binding"></a>Viewmodel 和数据绑定

若要参与数据绑定，ViewModel 必须能够 ViewModel 属性已更改时通知视图。 通过实现的 ViewModel 执行此[ `INotifyPropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/)接口中`System.ComponentModel`命名空间。 这是.NET 而不是 Xamarin.Forms 的一部分。 (通常 Viewmodel 尝试维护平台独立性。)

`INotifyPropertyChanged`接口声明一个事件名为[ `PropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) ，该值指示已更改的属性。

### <a name="a-viewmodel-clock"></a>ViewModel 时钟

[ `DateTimeViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)库定义的类型的属性`DateTime`更改基于计时器。 此类应实现`INotifyPropertyChanged`并激发`PropertyChanged`事件每当`DateTime`属性更改。

[ **MvvmClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock)示例实例化此视图模型，并使用数据绑定到 ViewModel 显示更新的日期和时间信息。

### <a name="interactive-properties-in-a-viewmodel"></a>视图模型中的交互式属性

视图模型中的属性可以是交互性更强，如所示： [ `SimpleMultiplierViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs)类，该类是一部分的[ **SimpleMultiplier** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier)示例。 数据绑定提供两个被乘数和乘数值`Slider`元素并显示与产品`Label`。 但是，你可以对在 XAML 中，未对视图模型或代码隐藏文件的后续更改此用户界面进行广泛的更改。

### <a name="a-color-viewmodel"></a>颜色 ViewModel

[ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)库集成 RGB 和 HSL 颜色模型。 中所演示[ **HslSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders)示例：

[![三重屏幕截图的 TK](images/ch18fg08-small.png "HSL 颜色模型")](images/ch18fg08-large.png "HSL 颜色模型")

### <a name="streamlining-the-viewmodel"></a>这样可简化视图模型

Viewmodel 中的代码可以简化通过定义`OnPropertyChanged`方法使用[ `CallerMemberName` ](https://developer.xamarin.com/api/type/System.Runtime.CompilerServices.CallerMemberNameAttribute/)属性，它可以自动通过获取调用的属性名称。 [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit)库执行此操作并提供基本类，用于 Viewmodel。

## <a name="the-command-interface"></a>命令界面

MVVM 配合数据绑定和数据绑定处理的属性，因此 MVVM 似乎是不足之处，处理时`Clicked`事件`Button`或`Tapped`事件`TapGestureRecognizer`。 若要允许 Viewmodel 来处理此类事件，Xamarin.Forms 支持*命令界面*。

命令界面表现在`Button`具有两个公共属性：

- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Command/) 类型的[ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/) (中定义`System.Windows.Input`命名空间)
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.CommandParameter/) 类型 `Object`

若要支持命令界面，ViewModel 必须定义类型的属性`ICommand`即然后数据绑定到`Command`属性`Button`。 `ICommand`接口声明两个方法和一个事件：

- [ `Execute` ](https://developer.xamarin.com/api/member/System.Windows.Input.ICommand.Execute/p/System.Object/)用类型自变量的方法 `object`
- A [ `CanExecute` ](https://developer.xamarin.com/api/member/System.Windows.Input.ICommand.CanExecute/p/System.Object/)用类型自变量的方法`object`，返回 `bool`
- A [ `CanExecuteChanged` ](https://developer.xamarin.com/api/event/System.Windows.Input.ICommand.CanExecuteChanged/)事件

视图模型设置类型的每个属性的内部，`ICommand`到实现的类的实例`ICommand`接口。 通过数据绑定，`Button`最初调用`CanExecute`方法，并禁用其自身，如果该方法返回`false`。 它还将设置的处理程序`CanExecuteChanged`事件并调用`CanExecute`每当激发该事件。 如果`Button`是启用，它将调用`Execute`方法每当`Button`单击。

你可能必须早 Xamarin.Forms，某些 Viewmodel，这些可能已支持命令界面。 为新 Viewmodel 应仅与 xamarin.forms 结合使用，提供了 Xamarin.Forms [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)类和一个[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/)类实现`ICommand`接口。 泛型类型是一种的自变量`Execute`和`CanExecute`方法。

### <a name="simple-method-executions"></a>简单的方法执行

[ **PowersOfThree** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree)示例演示如何使用视图模型中的命令界面。 [ `PowersViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs)类定义的类型的两个属性`ICommand`和还定义了将传递到最简单的两个私有属性[`Command`构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/)。 程序包含数据绑定到此 ViewModel 从`Command`的两个属性`Button`元素。

`Button`元素可以被轻松地替换为`TapGestureRecognizer`在 XAML 中进行任何代码更改的对象。

### <a name="a-calculator-almost"></a>一个计算器几乎

[ **AddingMachine** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine)示例使同时使用`Execute`和`CanExecute`方法`ICommand`。 它使用[ `AdderViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs)库。 视图模型包含六个属性的类型`ICommand`。 这些从初始化[`Command`构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/)和[`Command`构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/System.Func%7BSystem.Boolean%7D/)的`Command`和[`Command<T>`构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command%3CT%3E.Command%3CT%3E/p/System.Action%7BT%7D/System.Func%7BT,System.Boolean%7D/)`Command<T>`。 数字键添加机器的所有绑定到的属性使用初始化`Command<T>`，和一个`string`参数`Execute`和`CanExecute`标识特定密钥。

## <a name="viewmodels-and-the-application-lifecycle"></a>Viewmodel 和应用程序生命周期

`AdderViewModel`中使用**AddingMachine**示例还定义了名为的两个方法`SaveState`和`RestoreState`。 它进入睡眠状态以及它重新启动时，将从应用程序中调用这些方法。



## <a name="related-links"></a>相关链接

- [第 18 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [第 18 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
