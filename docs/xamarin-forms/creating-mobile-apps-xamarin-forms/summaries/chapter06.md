---
title: "第 6 章的摘要。 按钮单击事件"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 44dbb4d2cdc573e721bb772fdd05d392c90b746a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-6-button-clicks"></a>第 6 章的摘要。 按钮单击事件

[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)是允许用户启动命令的视图。 A`Button`文本由标识 (和 （可选） 一个图像中所示[章 13，位图](chapter13.md))。 因此，`Button`定义许多与相同的属性`Label`:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Text/)
- [`FontFamily`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontFamily/)
- [`FontSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontSize/)
- [`FontAttributes`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontAttributes/)
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.TextColor/)

`Button` 此外定义了三个属性控制其边框的外观，但这些属性和其相互独立的支持是特定于平台：

- [`BorderColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderColor/) 类型 `Color`
- [`BorderWidth`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderWidth/) 类型 `Double`
- [`BorderRadius`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderRadius/) 类型 `Double`

`Button` 此外会继承的所有属性`VisualElement`和`View`，包括`BackgroundColor`， `HorizontalOptions`，和`VerticalOptions`。

## <a name="processing-the-click"></a>处理单击

`Button`类定义[ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/)当用户点击时触发的事件`Button`。 `Click`处理程序的类型`EventHandler`。 第一个参数是`Button`对象生成的事件; 第二个参数是`EventArgs`提供任何附加信息的对象。

[ **ButtonLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger)示例演示简单`Clicked`处理。

## <a name="sharing-button-clicks"></a>共享按钮单击

多个`Button`视图可以共享同一个`Clicked`处理程序，但处理程序通常需要确定哪些`Button`负责特定事件。 一种方法是存储的各种`Button`作为字段并检查哪一个已触发的事件处理程序中的对象。

[ **TwoButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons)示例演示这种方法。 该程序还演示如何设置[ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/)属性`Button`到`false`时按`Button`将不再有效。 禁用一个`Button`不会生成`Clicked`事件。

## <a name="anonymous-event-handlers"></a>匿名事件处理程序

可以定义`Clicked`作为匿名 lambda 函数的处理程序作为[ **ButtonLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas)示例演示如何。 但是，不能无一些混乱反射代码共享匿名的处理程序。

## <a name="distinguishing-views-with-ids"></a>具有 Id 的可分辨视图

多个`Button`还可以通过设置地区分对象[ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/)属性或[ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/)属性`string`。 此属性定义由`Element`但它不在 Xamarin.Forms 内使用。 它旨在仅由应用程序使用。

[ **SimplestKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad)示例为数字键盘上的所有 10 个数字密钥使用相同的事件处理程序，并区分它们与`StyleId`属性：

[![最简单的小键盘的三个屏幕快照](images/ch06fg04-small.png "计算器")](images/ch06fg04-large.png "计算器")

## <a name="saving-transient-data"></a>保存暂时性数据

许多应用程序需要将数据保存在程序终止时，程序重新启动时重新加载该数据。 [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)类定义帮助程序保存和还原暂时性数据的多个成员：

- [ `Properties` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Properties/)属性是与一个字典`string`密钥和`object`项。 字典的内容自动保存在应用程序在程序终止之前的本地存储并在程序启动时重新加载。
- `Application`类定义该程序的标准的三个受保护的虚拟方法`App`类重写： [ `OnStart` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnStart()/)， [ `OnSleep` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnSleep()/)，和[ `OnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnResume()/). 这些是指*应用程序生命周期*事件。
- [ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/)方法将保存的字典的内容。

不需要调用`SavePropertiesAsync`。 字典的内容自动保存在程序终止之前，并在程序启动之前检索。 在程序测试以保存数据，如果此程序发生故障期间很有用。

也很有用是：

- [`Application.Current`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Current/)返回当前的静态属性`Application`对象，你可以使用它以获取`Properties`字典。

第一步是确定你想要在程序终止时持久保存的页面上的所有变量。 如果你知道这些变量在其中更改的所有位置，你可以轻松将其添加到`Properties`字典在该点。 在页面的构造函数，可以设置从变量`Properties`字典如果该键存在。

较大程序可能需要处理应用程序生命周期事件。 最重要的是`OnSleep`方法。 对此方法的调用指示该程序已离开前台。 用户已按可能**主页**按钮在设备上，或显示所有应用程序，或者是否正在关闭手机。 调用`OnSleep`是唯一的通知，程序将会接收程序终止前。 该程序应利用此机会来确保`Properties`字典是最新。

调用`OnResume`指示该程序未终止以下上次调用`OnSleep`但现在再次运行时在前台。 程序可能趁此机会刷新 internet 连接 （例如）。

调用`OnStart`在程序启动期间发生。 不需要等待，直到此方法调用访问`Properties`字典因为内容都已还原时`App`调用构造函数。

[ **PersistentKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad)示例是非常类似于**SimplestKeypad**只不过该程序使用`OnSleep`替代以保存当前的键盘输入，并页构造函数来还原该数据。



## <a name="related-links"></a>相关链接

- [第 6 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [第 6 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [第 6 章 F # 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
