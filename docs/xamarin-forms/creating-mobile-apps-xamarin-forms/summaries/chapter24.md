---
title: 第 24 章的摘要。 页导航
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 9d1a226a4532b745fddee28e943562fb51d34e65
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-24-page-navigation"></a>第 24 章的摘要。 页导航

许多应用程序包含多个用户在其中导航的页。 应用程序始终有*主要*页或*主*页上，并从那里用户导航至其他页面，在向后导航堆栈中维护。 中介绍了其他导航选项[**章 25。页上类型**](chapter25.md)。

## <a name="modal-pages-and-modeless-pages"></a>模式的页和无模式页

`VisualElement` 定义[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)类型的属性[ `INavigation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INavigation/)，其中包括以下两种方法，以导航到新页：

- [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/)
- [`PushModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync/p/Xamarin.Forms.Page/)

这两种方法接受`Page`实例作为自变量和返回`Task`对象。 以下两种方法向后定位到前一页：

- [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopAsync()/)
- [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/)

如果用户界面都有自己**回**按钮 （如 Android 和 Windows 手机执行操作），则它不是必需的应用程序以调用这些方法。

尽管这些方法可从任何`VisualElement`，通常从调用`Navigation`属性的当前`Page`实例。

当用户需要提供在返回到以前的页面之前页上的某些信息时，应用程序通常使用模式的页。 不模式页有时称为无模式或*分层*。 在页本身执行任何操作将它作为模式或无模式; 区分开来它由用来导航到它的方法相反控制。 若要跨所有平台，模式页必须为导航回前一页提供其自己的用户界面。

[ **ModelessAndModal** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal)示例让你能够浏览无模式和模式页之间的差异。 使用页导航任何应用程序必须将传递到其主页[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)构造函数，通常在程序的`App`类。 一个好处是可以是你不再需要设置`Padding`iOS 的页上。

你将会发现对于无模式的页，该页的[ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/)属性显示。 IOS、 Android 和 Windows 平板电脑和桌面平台所有提供的用户界面元素导航回前一页。 课程、 Android 和 Windows phone 设备具有一种标准**回**按钮以返回。

对于模式页，该页`Title`未显示，并提供没有用户界面元素返回到前一页。 尽管可以使用 Android 和 Windows phone 标准**回**按钮以返回到以前的页面中，在其他平台上的模式页面上必须提供其自己的机制，以返回。

### <a name="animated-page-transitions"></a>动画的网页转换

与第二个布尔参数设置为提供的各种的导航方法的备用版本`true`页转换，包括动画是否：

- [PushAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/System.Boolean/)
- [PushModalAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync/p/Xamarin.Forms.Page/System.Boolean/)
- [PopAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopAsync/p/System.Boolean/)
- [PopModalAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync/p/System.Boolean/)

但是，标准的页导航方法包括动画默认情况下，因此，这些是仅有价值的导航到在启动时的特定页 （如结束时的这一章所述） 或 （如所述提供你自己入口动画时[ **Chapter22。动画**](chapter22.md))。

### <a name="visual-and-functional-variations"></a>可视化和功能的变体

`NavigationPage` 包括实例化中的类时，可以设置的两种属性你`App`方法：

- [`BarBackgroundColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarBackgroundColor/)
- [`BarTextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarTextColor/)

`NavigationPage` 此外包括四个附加的可绑定属性影响在其设置特定页：

- [`SetHasBackButton`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetHasBackButton/p/Xamarin.Forms.Page/System.Boolean/) 和 [`GetHasBackButton`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetHasBackButton/p/Xamarin.Forms.Page/)
- [`SetHasNavigationBar`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetHasNavigationBar/p/Xamarin.Forms.BindableObject/System.Boolean/) 和 [`GetHasNavigationBar`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetHasNavigationBar/p/Xamarin.Forms.BindableObject/)
- [`SetBackButtonTitle`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetBackButtonTitle/p/Xamarin.Forms.BindableObject/System.String/) 和[ `GetBackButtonTitle` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetBackButtonTitle/p/Xamarin.Forms.BindableObject/)处理仅限 iOS
- [`SetTitleIcon`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetTitleIcon/p/Xamarin.Forms.BindableObject/Xamarin.Forms.FileImageSource/) 和[ `GetTitleIcon` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetTitleIcon/p/Xamarin.Forms.BindableObject/)在 iOS 和 Android 仅工作

### <a name="exploring-the-mechanics"></a>浏览机制

页导航方法是所有异步，应与`await`。 完成不表示页面导航已完成，但仅可安全检查的页导航堆栈。

当一页导航到另一个时，第一页通常获取调用其[ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing()/)方法和第二页获取调用其[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/)方法。 同样，当一页返回到另一个时，第一页获取调用其`OnDisappearing`方法和第二页通常获取调用其`OnAppearing`方法。 这些调用 （和导航时，将调用异步方法完成） 的顺序是取决于平台。 使用"一般"中的两个前面的语句字是由于 Android 模式页导航窗格中，这些方法调用不会发生。

此外，调用`OnAppearing`和`OnDisappearing`方法不一定表示页面导航。

`INavigation`接口包含两个集合属性，可用于检查导航堆栈：

- [`NavigationStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.NavigationStack/) 类型的`IReadOnlyList<Page>`无模式堆栈
- [`ModalStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) 类型的`IReadOnlyList<Page>`模式堆栈

它是最安全的方式访问从这些堆栈`Navigation`属性`NavigationPage`(应为`App`类的[ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/)属性)。 仅安全地检查这些堆栈后完成的异步页浏览方法。 [ `CurrentPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.CurrentPage/)属性`NavigationPage`不指示当前页是否当前页是一个模式的页，但指示改为无模式的最后一页。

[ **SinglePageNavigation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation)示例允许您浏览页面导航和堆栈中、 和页导航的合法类型：

- 无模式的页可以导航到另一个无模式的页或模式页
- 模式的页可以仅对另一个模式页导航

### <a name="enforcing-modality"></a>强制模式

需要从用户获取一些信息时，应用程序使用模式的页。 应禁止用户提供该信息之前返回到以前的页面。 在 iOS 上很容易地提供**回**按钮，然后仅在用户完成与页时，才启用它。 但是，对于 Android 和 Windows phone 设备，应用程序应重写[ `OnBackButtonPressed` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnBackButtonPressed()/)方法，并且返回`true`如果进行了处理程序**回**按钮本身，如下所示[ **ModalEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement)示例。

[ **MvvmEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement)示例演示此 MVVM 方案中的工作原理。

## <a name="navigation-variations"></a>导航变体

如果特定的模式页可以导航到多个时间，它应保留信息，以便用户可以编辑信息，而不是无需键入所有在试。 你可以保留模式页上，该特定实例来处理这但是 （特别是在 iOS) 更好的方法保留在视图模型中的信息。

### <a name="making-a-navigation-menu"></a>进行导航菜单

[ **ViewGalleryType** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType)示例演示如何使用`TableView`列出的菜单项。 每个项与`Type`特定的页对象。 选择该项目时，程序将实例化页，并导航到它。

[![三重的视图库类型的屏幕截图](images/ch24fg21-small.png "TableView 列出菜单项")](images/ch24fg21-large.png#lightbox "TableView 列出菜单项")

[ **ViewGalleryInst** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst)示例会稍有不同，则菜单包含的每个页，而不是类型的实例。 这可帮助保留每页中的信息，但所有页面必须实例都化在程序启动。

### <a name="manipulating-the-navigation-stack"></a>操作导航堆栈

[**StackManipulation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation)演示几个函数定义`INavigation`，可用于操作导航堆栈中的结构化的方式：

- [`RemovePage`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage/p/Xamarin.Forms.Page/)
- [`InsertPageBefore`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore/p/Xamarin.Forms.Page/Xamarin.Forms.Page/)
- [`PopToRootAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopToRootAsync()/) 和[ `PopToRootAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopToRootAsync/p/System.Boolean/)与可选的动画

### <a name="dynamic-page-generation"></a>生成动态页面

[ **BuildAPage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage)示例演示如何构建一个在运行时根据用户输入的页。

## <a name="patterns-of-data-transfer"></a>数据传输的模式

它通常是共享页面之间的数据所需&mdash;来传输数据到导航页中，和有关页后，可以将数据返回到调用它的页。 有几种方法执行此操作。

### <a name="constructor-arguments"></a>构造函数自变量

导航到新页，时，可以实例化具有一个构造函数参数，允许页后，可以初始化自身的页类。 [ **SchoolAndStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents)示例演示这一操作。 你也可用于导航页后，可以具有其`BindingContext`通过导航到它的页设置。

### <a name="properties-and-method-calls"></a>属性和方法调用

剩余的数据传输示例了解当一页导航到另一页的页和后之间传递信息的问题。 在这些讨论中，*主*页导航到*信息*页，然后必须将转移到的初始化的信息*信息*页。 *信息*页从用户获取其他信息，并传输到的信息*主*页。

*主*公共方法和属性中的，可以轻松地访问页*信息*页上，一旦它实例化该页面。 *信息*页还可以访问公共方法和属性中的*主*页上，但这可能会很棘手的选择的好时机。 [ **DateTransfer1** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1)示例执行此任务其`OnDisappearing`重写。 一个缺陷就是*信息*页需要了解的一种*主*页。

### <a name="messagingcenter"></a>MessagingCenter

Xamarin.Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)类提供了两个页来相互通信的另一种方法。 消息由文本字符串，并可能伴随任何对象。

想要接收来自特定类型的消息的程序必须订阅它们使用[ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender,TArgs%7D/p/System.Object/System.String/System.Action%7BTSender,TArgs%7D/TSender/)并指定一个回调函数。 它更高版本可以通过调用取消订阅[ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/)。 回调函数会收到具有指定名称通过发送发送从指定类型的任何消息[ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/)方法。

[ **DateTransfer2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2)程序演示如何传输数据使用的消息传送的中心，但这要求再次*信息*页知道的一种*主*页。

### <a name="events"></a>事件

事件是一个类，以将信息发送到另一个类，而不必知道该类的类型的经过方法。 在[ **DateTransfer3** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3)示例*信息*类定义准备信息时激发的事件。 但是，没有为任何方便的位置*主*页后，可以分离事件处理程序。

### <a name="the-app-class-intermediary"></a>应用程序类中介

[ **DateTransfer4** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4)示例演示如何访问属性中定义`App`两个类*主*页和*信息*页。 这是一个不错的解决方案，但下一节介绍更好的内容。

### <a name="switching-to-a-viewmodel"></a>切换到视图模型

信息允许使用 ViewModel*主*页和*信息*页后，可以共享信息类的实例。 此进行了演示[ **DateTransfer5** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5)示例。

### <a name="saving-and-restoring-page-state"></a>保存和还原页状态

`App`类中介或 ViewModel 方法非常适合应用程序必须保存信息，如果程序进入睡眠状态时*信息*页处于活动状态。 [ **DateTransfer6** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6)示例演示这一操作。

## <a name="saving-and-restoring-the-navigation-stack"></a>保存和还原导航堆栈

一般情况下，进入睡眠状态的多页程序应导航到同一页中，它在还原时。 这意味着，这样的程序应保存导航堆栈的内容。 本部分演示如何自动执行此过程中为此目的而设计的类。 此类还会调用以允许它们保存并还原其页面状态的各个页面。

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库定义一个名为的接口[ `IPersistantPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs)类可实现以保存并还原中项`Properties`字典。

[ `MultiPageRestorableApp` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs)类**Xamarin.FormsBook.Toolkit**库派生自`Application`。 然后可以派生你`App`类`MultiPageRestorableApp`并执行某些管理。

[ **StackRestoreDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo)演示如何使用`MultiPageRestorableApp`。

### <a name="something-like-a-real-life-app"></a>类似于现实应用

[ **NoteTaker** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker)示例还使用`MultiPageRestorableApp`，并允许的输入和编辑中保存的注释`Properties`字典。



## <a name="related-links"></a>相关链接

- [第 24 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [第 24 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [模式页](~/xamarin-forms/app-fundamentals/navigation/modal.md)
