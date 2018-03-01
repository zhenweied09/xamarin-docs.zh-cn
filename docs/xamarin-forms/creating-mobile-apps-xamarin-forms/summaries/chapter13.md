---
title: "第 13 章的摘要。 位图"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 2e511f2ebf75b065469a9051ee5797ac58c147f3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-13-bitmaps"></a>第 13 章的摘要。 位图

Xamarin.Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)会显示位图，元素。 所有 Xamarin.Forms 平台都支持 JPEG、 PNG、 GIF、 和 BMP 文件格式。

Xamarin.Forms 中的位图有四个不同来源：

- 通过指定 URL 的 web
- 为常见的可移植类库中的资源嵌入
- 作为资源嵌入在平台应用程序项目中
- 从任何位置可由.NET 引用`Stream`对象，包括 `MemoryStream`

特定于平台的平台项目中的位图资源时，在 PCL 中的位图资源是独立于平台的。

通过将设置指定位图[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/)属性`Image`类型的对象到[ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/)，一个带有三个派生的抽象类：

- [`UriImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) 用于通过基于 web 访问位图`Uri`对象设置为其[ `Uri` ](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.Uri/)属性
- [`FileImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/) 用于访问存储在平台应用程序项目中的位图基于文件夹和文件路径设置为上其[ `File` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FileImageSource.File/)属性
- [`StreamImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.StreamImageSource/) 用于加载位图使用.NET`Stream`通过返回指定对象`Stream`从`Func`设置为其[ `Stream` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StreamImageSource.Stream/)属性

或者 （和更常见） 可以使用以下的静态方法`ImageSource`类，将返回的所有`ImageSource`对象：

- [`ImageSource.FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) 用于通过基于 web 访问位图`Uri`对象
- [`ImageSource.FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) 用于访问存储在 PCL 中，应用程序中嵌入的资源为位图或[ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/System.Type/)或[ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/System.Reflection.Assembly/)访问另一个源程序集中的位图
- [`ImageSource.FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) 从程序访问位图平台应用程序项目
- [`ImageSource.FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) 用于加载位图基于`Stream`对象

没有类等效项的`Image.FromResource`方法。 `UriImageSource`类很有用，如果您需要缓存进行控制。 `FileImageSource`类可在 XAML 中。 `StreamImageSource` 可用于异步加载`Stream`对象，而`ImageSource.FromStream`是同步的。

## <a name="platform-independent-bitmaps"></a>独立于平台的位图

[ **WebBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode)项目加载位图通过 web 使用`ImageSource.FromUri`。 `Image`元素设置为`Content`属性`ContentPage`，因此它被约束为页的大小。 而不考虑位图的大小，约束`Image`元素扩展到其容器的大小和在其最大大小显示位图`Image`同时保持位图的纵横比的元素。 区域的`Image`以后可以具有颜色位图[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)。

[ **WebBitmapXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml)示例类似，但只需设置`Source`到 URL 的属性。 通过处理收缩转换[ `ImageSourceConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSourceConverter/)类。

### <a name="fit-and-fill"></a>配合和填充

你可以控制如何通过设置拉伸位图[ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/)属性`Image`的以下成员之一[ `Aspect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Aspect/)枚举：

- [`AspectFit`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFit/)： 遵循纵横比 （默认值）
- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.Fill/)： 填充区域，不遵从纵横比
- [`AspectFill`](https://developer.xamarin.com/api/type/Xamarin.Forms.Aspect.AspectFill/)： 填充区域，但会遵守纵横比，通过裁剪位图的一部分来实现

### <a name="embedded-resources"></a>嵌入的资源

你可以将位图文件添加到 PCL，或在 PCL 中的文件夹。 为其提供**生成操作**的**EmbeddedResource**。 [ **ResourceBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode)示例演示如何使用`ImageSource.FromResource`加载文件。 传递给方法的资源名称包含程序集名称后, 跟句点后, 跟可选的文件夹名称和句点后, 跟文件名。

程序集`VerticalOptions`和`HorizontalOptions`属性`Image`到`LayoutOptions.Center`，这使得`Image`不受约束的元素。 `Image`和位图是相同的大小：

- 在 iOS 和 Android，`Image`是位图的像素大小。 没有位图像素和屏幕像素之间的一对一映射。
- Windows 运行时在平台上，`Image`是独立于设备的单元中的位图的像素大小。 在大多数设备上每个位图像素占用多个屏幕像素。

[ **StackedBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap)示例将`Image`中垂直`StackLayout`在 XAML 中。 名为标记扩展[ `ImageResourceExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs)有助于引用 XAML 中嵌入的资源。 此类仅从它的程序集加载资源所在，因此它不能放在库中。

### <a name="more-on-sizing"></a>调整大小的详细信息

通常是在所有平台之间一致地大小位图到可取的。
动手试验**StackedBitmap**，你可以设置`WidthRequest`上`Image`中垂直元素`StackLayout`使大小一致之间平台，但你只能减少使用这种技术的大小。

你还可以设置`HeightRequest`使图像大小一致的平台上，但约束位图的宽度将限制此技术的通用性。 中垂直图像`StackLayout`，`HeightRequest`应当避免。

最佳方法是开头的位图宽于的 phone 宽度独立于设备的单位并设置`WidthRequest`到独立于设备的单位所需的宽度。 此进行了演示[ **DeviceIndBitmapSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize)示例。

[ **MadTeaParty** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty)显示 Lewis 卡罗尔的第 7 章*的 Wonderland Alice 的冒险*了通过 John Tenniel 原始的图示：

[![Mad tea 方的三个屏幕截图](images/ch13fg16-small.png "Mad Hatters Tea 方该书的文本")](images/ch13fg16-large.png "Mad Hatters Tea 方该书的文本")

### <a name="browsing-and-waiting"></a>浏览和等待

[ **ImageBrowser** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser)示例允许用户浏览 Xamarin 网站上存储的库存映像。 它使用的是.NET`WebRequest`类，以下载 JSON 文件的位图的列表。

该程序使用[ `ActivityIndicator` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/)以指示内容进行的操作。 如加载每个位图，只读的[ `IsLoading` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.IsLoading/)属性`Image`是`true`。 `IsLoading`属性支持可绑定属性，因此`PropertyChanged`该属性变化时触发事件。 程序将处理程序附加到此事件，并使用的当前设置`IsLoaded`设置[ `IsRunning` ](https://api/property/Xamarin.Forms.ActivityIndicator.IsRunning/)属性`ActivityIndicator`。

## <a name="streaming-bitmaps"></a>流式处理位图

`ImageSource.FromStream`方法创建`ImageSource`基于.NET `Stream`。 必须向此方法传递`Func`返回的对象`Stream`对象。

### <a name="accessing-the-streams"></a>访问流

[ **BitmapStreams** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams)示例演示如何使用`ImaageSource.FromStream`方法来加载位图存储作为嵌入资源，并在 web 负载位图。

### <a name="generating-bitmaps-at-run-time"></a>在运行时生成位图

所有 Xamarin.Forms 平台都支持未压缩的 BMP 文件格式，这很容易构造在代码中，然后将存储在`MemoryStream`。 此方法允许在运行时，算法创建位图实现的一样[ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)类**Xamrin.FormsBook.Toolkit**库。

"经营" [ **DiyGradientBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap)示例演示如何使用`BmpMaker`若要创建具有渐变图像的位图。

## <a name="platform-specific-bitmaps"></a>特定于平台的位图

所有 Xamarin.Forms 平台都允许将位图存储在平台应用程序程序集。 检索由 Xamarin.Forms 应用程序，这些平台位图属于类型[ `FileImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/)。 可将其用于：

- [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Icon/)属性 [`MenuItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/)
- [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/)属性 [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/)
- [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)属性 `Button`

平台程序集已包含位图的图标和初始屏幕：

- 在 iOS 项目中，在**资源**文件夹
- 在 Android 项目中的子文件夹**资源**文件夹
- 在 Windows 项目中，在**资产**文件夹 （尽管 Windows 平台到该文件夹不限制位图）

[ **PlatformBitmaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps)示例使用代码以显示指定平台应用程序项目中的一个图标。

### <a name="bitmap-resolutions"></a>位图的解决方法

所有平台都允许存储的不同设备的解决方法的位图图像的多个版本。 在运行时，正确的版本被加载基于屏幕设备解析。

在 iOS 上这些位图的区别在于在文件名上后缀：

- 160 DPI 设备 （为与设备无关的单位 1 个像素） 没有后缀
- @2x后缀 320 DPI 设备 （DIU 到 2 的像素为单位）
- @3x后缀 480 DPI 设备 （到 DIU 3 的像素为单位）

预期要显示为一个英寸方块的位图将三个版本中存在:

- 在 160 像素的正方形 MyImage.jpg
- MyImage@2x.jpg 宽度为 320 像素正方形
- MyImage@3x.jpg 在 480 像素的正方形

程序将引用此位图作为 MyImage.jpg，但在基于屏幕的分辨率的运行时检索的正确版本。 时不受约束，将始终在 160 设备无关的单位来呈现位图。

对于 Android，位图存储在各个子文件夹的**资源**文件夹：

- 可绘制-ldpi (低 DPI) 为 120 DPI 设备 （到 DIU 0.75 的像素为单位）
- 可绘制-mdpi （中等规模） 为 160 DPI 设备 （DIU 到 1 个像素）
- 可绘制的 hdpi （高） 为 240 DPI 设备 （DIU 到 1.5 的像素为单位）
- 可绘制-xhdpi （额外高） 为 320 DPI 设备 （DIU 到 2 的像素为单位）
- 可绘制-xxhdpi （额外额外高） 为 480 DPI 设备 （到 DIU 3 的像素为单位）
- 可绘制-xxxhdpi （三个额外高） 为 640 DPI 设备 （DIU 到 4 的像素为单位）

对于用于在一个正方形英寸呈现位图，各种版本的位图将具有相同名称但不同的大小，并存储在这些文件夹：

- 可绘制-ldpi/MyImage.jpg 在 120 像素正方形
- 可绘制-mdpi/MyImage.jpg 在 160 像素的正方形
- 可绘制的 hdpi/MyImage.jpg 在 240 像素的正方形
- 可绘制-xhdpi/MyImage.jpg 宽度为 320 像素正方形
- 可绘制-xxhdpi/MyImage.jpg 在 480 像素的正方形
- 可绘制-xxxhdpi/MyImage.jpg 在 640 像素的正方形

将始终在 160 设备无关的单位来呈现位图。 （标准 Xamarin.Forms 解决方案模板只包括 hdpi、 xhdpi 和 xxhdpi 文件夹。）

Windows 运行时项目支持位图命名方案组成一个比例因子以像素为单位，每个独立于设备的单位为百分比，例如：

- 宽度为 320 像素正方形的 MyImage.scale 200.jpg

仅某些百分比都有效。 本书的示例程序包含仅与映像**缩放 200**后缀，但当前 Xamarin.Forms 解决方案模板包括**缩放 100**，**缩放 125**，**缩放 150**，和**缩放-400**。 

将位图添加到平台项目中，当**生成操作**应为：

- iOS: **BundleResource**
- Android: **AndroidResource**
- Windows 运行时：**内容**

[ **ImageTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap)示例创建两个按钮类似对象组成`Image`元素`TapGestureRecognizer`安装。 它旨在这些对象是一个英寸正方形。 `Source`属性`Image`使用设置`OnPlatform`和`On`对象引用不同的平台上的文件名。 位图图像包括号指示其像素大小，以便你可以看到哪些大小位图是检索和呈现。

### <a name="toolbars-and-their-icons"></a>工具栏和其图标

特定于平台的位图的主要用途之一是 Xamarin.Forms 工具栏上，通过添加构造[ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/)对象添加到[ `ToolbarItems` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.ToolbarItems/)集合由定义`Page`. `ToobarItem` 派生自[ `MenuItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/)从它继承某些属性。

最重要`ToolbarItem`属性：

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Text/) 根据平台可能会出现的文本和 `Order`
- [`Icon`](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) 类型的`FileImageSource`可能会出现因平台而异的映像和 `Order`
- [`Order`](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Order/) 类型的[ `ToolbarItemOrder` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItemOrder/)，一个包含三个成员的枚举的[ `Default` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Default/)， [ `Primary` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Primary/)，和[ `Secondary` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ToolbarItemOrder.Secondary/).

数`Primary`应限制为三个或四个项。 应包括`Text`设置的所有项。 对于大多数平台，仅`Primary`项需要`Icon`，但 Windows 8.1 需要`Icon`的所有项。 图标应为 32 设备无关的单位正方形。 `FileImageSource`类型表示它们是特定于平台的。

`ToolbarItem`激发[ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.MenuItem.Clicked/)事件时分流，非常类似于`Button`。 `ToolbarItem` 此外支持[ `Command` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Command/)和[ `CommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.CommandParameter/)通常与 MVVM 一起使用的属性。 (请参阅[第 18 章、 MVVM](chapter18.md))。

IOS 和 Android 要求显示工具栏的页面是[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)或页导航到`NavigationPage`。 [ **ToolbarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo)程序集`MainPage`属性其`App`类到[`NavigationPage`构造函数](https://developer.xamarin.com/api/constructor/Xamarin.Forms.NavigationPage.NavigationPage/p/Xamarin.Forms.Page/)与`ContentPage`自变量，并演示了工具栏的构造和事件处理程序。

### <a name="button-images"></a>按钮图像

你还可以使用特定于平台的位图设置[ `Image` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Image/)属性`Button`32 独立于设备的单元方形，如所示的位图[ **ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage)示例。



## <a name="related-links"></a>相关链接

- [13 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [13 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [使用图像](~/xamarin-forms/user-interface/images.md)
