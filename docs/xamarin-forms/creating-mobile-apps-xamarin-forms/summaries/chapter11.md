---
title: "第 11 章的摘要。 可绑定的基础结构"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 3e3cfb55f7b96751979d14b489e892bc07817780
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>第 11 章的摘要。 可绑定的基础结构

每个 C# 程序员是熟悉 C#*属性*。 属性包含*设置*访问器和/或一个*获取*访问器。 它们通常称为*CLR 属性*为公共语言运行时。

Xamarin.Forms 定义名为增强的属性定义*可绑定属性*包装[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)类，并且支持[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)类。 这些类是相关但非常不同：`BindableProperty`用于定义的属性，然后重试。`BindableObject`类似`object`，因为它是定义可绑定属性的类的基类。

## <a name="the-xamarinforms-class-hierarchy"></a>Xamarin.Forms 类层次结构

[ **ClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy)示例使用反射显示 Xamarin.Forms 类层次结构，并演示所扮演的重要角色`BindableObject`此层次结构中。 `BindableObject` 派生自`Object`并且到父类[ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/)从中[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)派生。 这是到父类[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)和[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)，即到父类[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/):

[![共享的类层次结构的三个屏幕快照](images/ch11fg01-small.png "类层次结构共享")](images/ch11fg01-large.png "类层次结构共享")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>到 BindableObject 和 BindableProperty 扫视

在派生类中`BindableObject`许多 CLR 属性据说受"的支持"可绑定属性。 例如， [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)属性`Label`类是 CLR 属性，但`Label`类还定义了名为的公共静态只读字段[ `TextProperty` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextProperty/)的类型`BindableProperty`。

应用程序可以设置或获取`Text`属性`Label`通常情况下，或应用程序可以设置`Text`通过调用[ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法由定义`BindableObject`与`Label.TextProperty`自变量。 同样，应用程序可以获得的值`Text`属性通过调用[ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/)方法，再次使用`Label.TextProperty`自变量。 说明了这一点通过[ **PropertySettings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings)示例。

事实上， `Text` CLR 属性完全实现使用`SetValue`和`GetValue`由定义方法`BindableObject`结合`Label.TextProperty`静态属性。

`BindableObject` 和`BindableProperty`提供支持：

- 给定属性默认值
- 存储其当前值
- 提供用于验证属性值的机制
- 维护的单个类中的相关属性之间的一致性
- 响应属性更改
- 在属性将要发生更改或已更改时触发通知
- 支持数据绑定
- 支持样式
- 支持动态资源

每当一个属性，由可绑定属性更改，支持`BindableObject`激发[ `PropertyChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.PropertyChanged/)标识已更改的属性的事件。 属性设置为相同的值时，不激发此事件。

由可绑定属性，以及某些 Xamarin.Forms 类与 #x 2014; 不支持某些属性如`Span`& #x 2014; 不派生自`BindableObject`。 仅派生自的类`BindableObject`可以支持可绑定属性，因为`BindableObject`定义`SetValue`和`GetValue`方法。

因为`Span`不是派生自`BindableObject`，无其属性和 #x 2014年; 如`Text`& #x 2014; 由可绑定属性。 正因如此`DynamicResource`上设置`Text`属性`Span`引发的异常[ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)示例前一章。 [ **DynamicVsStaticCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode)示例演示如何在代码中设置动态资源[ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/p/Xamarin.Forms.BindableProperty/System.String/)方法由定义`Element`。 第一个参数是类型的对象`BindableProperty`。

同样， [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)方法由定义`BindableObject`具有类型的第一个参数`BindableProperty`。

## <a name="defining-bindable-properties"></a>定义可绑定属性

你可以定义自己可绑定的属性，使用静态[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法或略有较短者[ `BindableProperty.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.Create/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/)重载来创建类型的静态只读字段`BindableProperty`。

此进行了演示[ `AltLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。 类派生自`Label`并且允许你以磅为单位指定字体大小。 中所演示[ **PointSizedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText)示例。

四个自变量的`BindableProperty.Create`是必需的方法：

- `propertyName`： 属性 （与 CLR 属性名称相同） 的文本名称
- `returnType`: CLR 属性的类型
- `declaringType`： 声明属性的类的类型
- `defaultValue`： 该属性的默认值

因为`defaultValue`属于类型`object`，编译器必须能够确定默认值的类型。 例如，如果`returnType`是`double`、`defaultValue`应设置为 0.0，而不是只是 0，类似或类型不匹配将触发在运行时异常。

它也是很常见的可绑定的属性以包含：

- `propertyChanged`： 当属性值更改时调用的静态方法。 第一个参数是更改其属性类的实例。

其他参数`BindableProperty.Create`一些不太常见：

- `defaultBindingMode`： 用于数据绑定 (如中所述[**章 16。数据绑定**](chapter16.md))
- `validateValue`： 回调来检查有效的值
- `propertyChanging`： 指示即将更改属性时的回调
- `coerceValue`： 要强制转换为其他值将值的回调
- `defaultValueCreate`： 回调来创建不在类 （例如，集合） 的实例之间共享的默认值

### <a name="the-read-only-bindable-property"></a>只读可绑定属性

可绑定的属性可以是只读的。 创建只读可绑定属性需要调用静态方法[ `BindableProperty.CreateReadOnly` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateReadOnly/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)或更短[ `BindableProperty.CreateReadOnly` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateReadOnly/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/)定义私有静态只读字段的类型[ `BindablePropertyKey`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindablePropertyKey/).

然后，定义的 CLR 属性`set`访问器中作为`private`调用[ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindablePropertyKey/System.Object/)重载`BindablePropertyKey`对象。 这会阻止在类外部设置属性。

此进行了演示[ `CountedLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs)中使用类[ **BaskervillesCount** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount)示例。



## <a name="related-links"></a>相关链接

- [第 11 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [第 11 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
