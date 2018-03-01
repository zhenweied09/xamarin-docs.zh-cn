---
title: "章 25 的摘要。 页类型"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: bbe960357d9180df90a4423d6acfdf3f869d1b77
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-25-page-varieties"></a>章 25 的摘要。 页类型

到目前为止，你已了解两个类派生自`Page`:`ContentPage`和`NavigationPage`。 这一章介绍其他两个：

- [`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) 管理这两页、 master 和详细信息
- [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) 管理访问通过选项卡的多个子页

这些页面类型提供更高级的导航选项比`NavagationPage`中所述[24 章。页导航](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md)。

## <a name="master-and-detail"></a>Master 和详细信息

[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)定义的类型的两个属性`Page`: [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)和[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)。 设置每个到这些属性通常`ContentPage`。 `MasterDetailPage`显示，并这些两个页之间切换。

有两种基本的方法，这些两个页之间切换：

- *拆分*其中 master 和详细信息在并行
- *popover*的详细信息页涵盖或部分覆盖主位置页上

有几个变体*popover*方法 (*幻灯片*，*重叠*，和*交换*)，但它们通常是平台依赖。 你可以设置[ `MasterDetailBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/)属性`MasterDetailPage`指向成员的[ `MasterBehavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterBehavior/)枚举：

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Default/)
- [`Split`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Split/)
- [`SplitOnLandscape`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.SplitOnLandscape/)
- [`SplitOnPortrait`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.SplitOnPortrait/)
- [`Popover`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Popover/)

但是，此属性不起在手机上。 手机始终具有 popover 行为。 仅平板电脑和桌面的 windows 可以拆分行为。

### <a name="exploring-the-behaviors"></a>浏览行为

[ **MasterDetailBehaviors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors)示例允许你能够试验不同的设备上的默认行为。 程序包含两个单独`ContentPage`衍生产品的 master 和详细信息 (与`Title`上都设置的属性)，以及派生自的另一个类`MasterDetailPage`，将它们合并。 详细信息页括在`NavigationPage`因为 UWP 程序而无需它不会起作用。

Windows 8.1 和 Windows Phone 8.1 平台需要位图设置为`Icon`主控页的属性。

### <a name="back-to-school"></a>回学校

[ **SchoolAndDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail)示例采用稍有不同的方法，构造此程序以显示学生的[ **SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)库。

`Master`和`Detail`属性定义中的可视化树[SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml)文件，派生自`MasterDetailPage`。 这种安排允许数据绑定之间的 master 和详细信息页设置。

此外将设置 XAML 文件[ `IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/)属性`MasterDetailPage`到`True`。 这将导致主页后，可以显示在启动;默认情况下显示的详细信息页。 [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs)文件中设置`IsPresented`到`false`时从选择项`ListView`母版页中。 然后显示的详细信息页：

[![三重的学校和详细信息的屏幕截图](images/ch25fg09-small.png "从 MasterDetailPage 的详细信息页")](images/ch25fg09-large.png "从 MasterDetailPage 的详细信息页")

### <a name="your-own-user-interface"></a>用户界面

Xamarin.Forms 提供用户界面用于 master 和详细信息视图之间切换，尽管你可以提供你自己。 若要这样做：

- 设置[ `IsGestureEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsGestureEnabled/)属性`false`禁用轻扫
- 重写[ `ShouldShowToolbarButton` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton()/)方法，并且返回`false`隐藏在 Windows 8.1 和 Windows Phone 8.1 上的工具栏按钮。

你必须提供一种方式来切换 master 和详细信息页中，如所示[ **ColorsDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails)示例。

[ **MasterDetailTaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps)示例演示了如何在另一个方法使用`TapGestureRecognizer`的 master 和详细信息页上。

## <a name="tabbedpage"></a>TabbedPage

[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)是你可以使用选项卡之间切换的页的集合。 它派生自`MultiPage<Page>`并定义没有公共属性或自己的方法。 [`MultiPage<T>`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/)但是，未定义属性：

- [`Children`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/) 类型的属性 `IList<T>`

填充该`Children`与页的对象的集合。

另一种方法允许你定义`TabbedPage`子级非常类似`ListView`使用自动生成的选项卡式的页这两个属性：

- [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/) 类型 `IEnumerable`
- [`ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/) 类型 `DataTemplate`

但是，这种方法很好地在 iOS 时无效集合包含多个项。

`MultiPage<T>` 定义让你跟踪当前查看的页的另外两个属性：

- [`CurrentPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.CurrentPage/) 类型的`T`，以引用页面
- [`SelectedItem`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.SelectedItem/) 类型的`Object`，以引用中的对象`ItemsSource`集合

`MultiPage<T>` 此外定义了两个事件：

- [`PagesChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.MultiPage%3CT%3E.PagesChanged/) 当`ItemsSource`集合更改
- [`CurrentPageChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.MultiPage%3CT%3E.CurrentPageChanged/) 查看的页的更改时

### <a name="discrete-tab-pages"></a>离散的选项卡页

[ **DiscreteTabbedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors)示例由三个不同的方式显示颜色的三个选项卡式页组成。 每个选项卡`ContentPage`派生，，然后`TabbedPage`派生[DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml)合并三个页面。

将出现在每个页`TabbedPage`、`Title`属性需要在选项卡中指定的文本，并且 Apple 应用商店要求一个图标，也可用于因此`Icon`为 iOS 设置属性：

[![三重的离散选项卡式颜色的屏幕截图](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png "TabbedPage")

[ **StudentNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes)示例有列出所有学生的主页。 当点击一名学生时，这会定位到`TabbedPage`派生， [ `StudentNotesDataPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml)，包含三个`ContentPage`对象在其可视化树中，一种允许该学生输入的一些注意事项。

### <a name="using-an-itemtemplate"></a>使用 ItemTemplate

[ **MultiTabbedColor** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors)示例使用[ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs)类[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。 [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml)文件中设置`DataTemplate`属性`TabbedPage`为一个可视化树开头`ContentPage`，包含绑定到的属性`NamedColor`（包括一种绑定到`Title`属性)。

但是，这是在 iOS 上有问题。 可以显示只有几个项，并且没有为其提供图标没有好办法。



## <a name="related-links"></a>相关链接

- [章 25 的全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [章 25 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [主-详细信息页](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [选项卡式的页面](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
