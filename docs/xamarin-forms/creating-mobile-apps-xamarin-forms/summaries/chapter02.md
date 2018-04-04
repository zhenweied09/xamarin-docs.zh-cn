---
title: 第 2 章的摘要。 应用程序的剖析
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: c206b124349614db7249609707bd22e8a4efe6d8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>第 2 章的摘要。 应用程序的剖析

在 Xamarin.Forms 应用中，占用的空间在屏幕上的对象称为*可视元素*、 通过封装[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)类。 可视元素可以拆分为对应这些类的三个类别：

- [页](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)
- [布局](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)
- [视图](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)

A`Page`派生占用整个屏幕或几乎整个屏幕。 通常，页的子级是`Layout`派生来组织子可视元素。 子级`Layout`可以是其他`Layout`类或`View`衍生产品 (通常称为*元素*)，这是熟悉如文本、 位图、 滑块、 按钮、 列表框中和等等的对象。

本章演示如何创建了应用程序将重点放在[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)，即`View`显示文本的派生。

## <a name="say-hello"></a>说你好

安装 Xamarin 平台，你可以创建一个新 Xamarin.Forms 解决方案在 Visual Studio 或 Visual Studio for mac。 [ **Hello** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello)解决方案使用可移植类库的通用代码。 它演示了在 Visual Studio 中创建不作任何修改的 Xamarin.Forms 解决方案。 解决方案包含六个项目 （其中两个不使用当前的 Xamarin.Forms 解决方案模板创建的最后一个）：

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello)，由其他项目共享的可移植类库 (PCL)
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid)，适用于 Android 的应用程序项目
- [**Hello.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS)，适用于 iOS 的应用程序项目
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP)，通用 Windows 平台 （Windows 10 和 Windows 10 移动版） 的应用程序项目
- [**Hello.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Windows)，Windows 8.1 的应用程序项目
- [**Hello.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.WinPhone)，Windows Phone 8.1 的应用程序项目

你可以进行任何这些应用程序项目启动项目，然后生成并在设备或模拟器上运行程序。

在很多程序 Xamarin.Forms，不会修改应用程序项目。 这些通常保持小的存根，只是为了启动程序。 大部分你焦点将可移植类库普遍适用于所有应用程序。

## <a name="inside-the-files"></a>文件内

显示的视觉对象**Hello**程序定义的构造函数中[ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs)类。 `App` 派生自 Xamarin.Forms 类[ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)。

**引用**部分**Hello** PCL 项目，包括以下 Xamarin.Forms 程序集：

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Platform**

**引用**的五个应用程序项目的部分包括适用于各个平台的其他程序集：

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.Tablet**
- **Xamarin.Forms.Platform.WinRT.Phone**

每个应用程序项目包含为对静态调用`Forms.Init`中的方法`Xamarin.Forms`命名空间。 此值将初始化 Xamarin.Forms 库。 不同版本的`Forms.Init`为每个平台定义。 可以在以下类中找到调用此方法：

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App`类，`OnLaunched`方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Windows 8.1: [ `App`类，`OnLaunched`方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/App.xaml.cs#L65)
- Windows Phone 8.1: [ `App`类，`OnLaunched`方法](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WinPhone/App.xaml.cs#L67)

此外，每个平台必须实例化`App`类在 PCL 中的位置。 对的调用中将发生这种情况`LoadApplication`为以下几类：

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)
- Windows 8.1： [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/MainPage.xaml.cs)
- Windows Phone 8.1: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WindowsPhone/MainPage.xaml.cs)

否则，这些应用程序项目是正常的"不执行任何操作"的程序。

## <a name="pcl-or-sap"></a>PCL 或 SAP？

可使用可移植类库 (PCL) 或共享资产项目 (SAP) 中的常见代码创建 Xamarin.Forms 解决方案。 若要创建的 SAP 解决方案，请在 Visual Studio 中选择共享选项。 [ **HelloSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap)解决方案演示 SAP 模板不作任何修改。

平台应用程序项目引用的类库项目中的代码的所有常见 PCL 方法捆绑包。 使用 SAP 方法时，常见的代码有效地存在于所有平台应用程序项目，在它们之间共享。

大多数 Xamarin.Forms 开发人员更喜欢 PCL 方法。 在本书中，大部分的解决方案是 PCL。 使用 SAP 的这些注意事项包括**Sap**中的项目名称后缀。

若要支持所有 Xamarin.Forms 平台，通过 PCL 中使用的.NET 版本必须满足以下平台：

- .NET Framework 4.5
- Windows 8
- Windows Phone 8.1
- Xamarin.Android
- Xamarin.iOS
- Xamarin.IOS （经典）

这称为计算机配置文件 111。

使用 SAP 方法共享项目中的代码可以通过使用 C# 预处理器指令执行的各种平台的不同代码 (`#if`，#`elif`，和`#endif`) 使用这些预定义标识符：

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`
- Windows 8.1： `WINDOWS_APP`
- Windows Phone 8.1: `WINDOWS_PHONE_APP`

在 PCL 可以确定在运行时，要在运行哪种平台，正如您将看到更高版本中这一章。

## <a name="labels-for-text"></a>标签文本

[ **Greetings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings)解决方案演示如何将添加一个新 C# 文件到**Greetings**项目。 此文件定义一个名为`GreetingsPage`派生自`ContentPage`。 在本书中，大多数项目包含单个`ContentPage`派生其名称是带有后缀项目的名称`Page`追加。

`GreetingsPage`构造函数实例化[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)视图，它是显示文本的 Xamarin.Forms 视图。 [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)属性设置为显示的文本`Label`。 此程序设置`Label`到`Content`属性`ContentPage`。 构造函数`App`然后实例化类`GreetingsPage`并将它设置为其`MainPage`属性。

在页面的左上角显示的文本。 在 iOS 上，这意味着它重叠页的状态栏。 有几种解决方案，此问题：

### <a name="solution-1-include-padding-on-the-page"></a>解决方案 1。 包括在页上的填充

设置[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/)页上的属性。 `Padding` 类型[ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)，具有四个属性的结构：

- [`Left`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Left/)
- [`Top`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Top/)
- [`Right`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Right/)
- [`Bottom`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Bottom/)

`Padding` 定义页面内的一个区域，其中排除内容。 这允许`Label`以避免覆盖 iOS 状态栏。

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>解决方案 2。 包括只适用于 iOS (仅适用于 SAP) 的填充

仅在使用 C# 预处理器指令的 SAP 的 iOS 设置的填充的属性。 此进行了演示[ **GreetingsSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap)解决方案。

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>解决方案 3。 包括只适用于 iOS （PCL 或 SAP） 的填充

在用于书籍，Xamarin.Forms 版`Padding`可以选择特定于 iOS 的 PCL 或 SAP 中的属性，使用[ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/)或[ `Device.OnPlatform<T>` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform%7BT%7D/p/T/T/T/)静态方法。 这些方法现已弃用

`Device.OnPlatform`方法用来运行特定于平台的代码，或者选择特定于平台的值。 在内部，它们使使用[ `Device.OS` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/)静态只读属性，返回的成员[ `TargetPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetPlatform/)枚举：

- [`iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.iOS/)
- [`Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Android/)
- [`Windows`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Windows/) Windows 8.1、 Windows Phone 8.1 和所有 UWP 设备。
- [`WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.WinPhone/)以前用于标识 Windows Phone 8.0 但是现在不使用
- [`Other`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Other/) 未使用

`Device.OnPlatform`方法，`Device.OS`属性，与`TargetPlatform`枚举都现在已弃用。 请改用[ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/)属性和比较`string`返回与以下的静态字段的值：

- [`iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/)字符串"iOS" 
- [`Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/)字符串"Android"
- [`UWP`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/)字符串"UWP"，以引用 Windows 运行时平台
- [`Windows`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Windows/)字符串"Windows"for Windows Runtime （Windows 8.1 和 Windows Phone 8.1）
- [`WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/)字符串"WinPhone"的 Windows Phone 8.0 

[ `Device.Idiom` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.Idiom/)相关静态只读属性。 这将返回的成员[ `TargetIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetIdiom/)，其中包括以下成员：

- [`Desktop`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Desktop/)
- [`Tablet`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Tablet/)
- [`Phone`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Phone/)
- [`Unsupported`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Unsupported/) 未使用

适用于 iOS 和 Android，之间截止`Tablet`和`Phone`是 600 单位纵向宽度。 对于 Windows 平台，`Desktop`指示在 Windows 10 下运行的 UWP 应用程序`Tablet`Windows 8.1 程序和`Phone`指示在 Windows 10 或 Windows Phone 8.1 应用程序下运行的 UWP 应用程序。

## <a name="solution-3a-set-margin-on-the-label"></a>解决方案 3a。 标签上的设置距

[ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)太迟引入属性要包括在簿中，但它也属于类型`Thickness`并可以将其设置上`Label`定义之外的计算中包含的视图的区域视图的布局。

`Padding`属性才是可在上找到[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)和[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)衍生产品。 `Margin`属性是可以在所有[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)衍生产品。

## <a name="solution-4-center-the-label-within-the-page"></a>方案 4。 中心页中的标签

您可以居中`Label`内`Page`（或将其放入其他八个位置之一） 通过设置[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)和[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性`Label`类型的值为[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)。 `LayoutOptions`结构定义两个属性：

- [ `Alignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Alignment/)类型的属性[ `LayoutAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutAlignment/)，一个包含四个成员的枚举： [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Start/)，这意味着向左或具体取决于顶部方向， [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Center/)， [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.End/)，这意味着右侧或底部，具体取决于方向，和[ `Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Fill/)。

- [ `Expands` ](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/)类型的属性`bool`。

通常不直接使用这些属性。 相反，这两个属性的组合提供的类型的八个静态只读属性`LayoutOptions`:

- [`LayoutOptions.Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`LayoutOptions.Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`LayoutOptions.End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`LayoutOptions.StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`LayoutOptions.CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`LayoutOptions.EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

`HorizontalOptions` 和`VerticalOptions`是在 Xamarin.Forms 布局中，最重要的属性和更详细地讨论了[**第 4 章。滚动堆栈**](chapter04.md)。

下面是结果与`HorizontalOptions`和`VerticalOptions`属性`Label`都设置为`LayoutOptions.Center`:

[![三重的 greetings 程序的屏幕截图](images/ch02fg05-small.png "水平和垂直居中标签")](images/ch02fg05-large.png#lightbox "水平和垂直居中标签")

## <a name="solution-5-center-the-text-within-the-label"></a>解决方案 5。 Center 在标签中的文本

也可以居中文本 （或将其放在页上的八个其他位置中） 通过设置[ `HorizontalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.HorizontalTextAlignment/)和[ `VerticalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/)属性`Label`指向成员的[ `TextAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/)枚举：

- [`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Start/)含义 left 或 top （具体取决于方向）
- [`Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/)
- [`End`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.End/)这意味着右侧或底部 （具体取决于方向）

这两个属性定义仅可由`Label`，而`HorizontalAlignment`和`VerticalAlignment`通过定义属性`View`并由所有继承`View`衍生产品。 下的视觉效果可能看上去相似，但它们有很大差异，如下一章所示。



## <a name="related-links"></a>相关链接

- [第 2 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [第 2 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [第 2 章 F # 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Getting Started with Xamarin.Forms](~/xamarin-forms/get-started/index.md)
