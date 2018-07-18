---
title: iOS 7 用户界面概述
description: iOS 7 引入了大量的用户界面将会更改。 本文重点介绍某些较大的更改，在控件的可视外观和中支持的新设计的 Api。
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 3f5ea8abd41e718f9ac947c5acb290dffe400ddd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30781920"
---
# <a name="ios-7-user-interface-overview"></a>iOS 7 用户界面概述

_iOS 7 引入了大量的用户界面将会更改。本文重点介绍某些较大的更改，在控件的可视外观和中支持的新设计的 Api。_

iOS 7 通过 chrome 重点内容。 在 iOS 7 中的用户界面元素去加重 chrome 通过删除属性，如收集到无关的边框、 状态栏和导航栏，减少使用的内容视图的屏幕空间量。 在 iOS 7，内容设计为使用整个屏幕。

iOS 7 上引入了几个其他更改： 使用颜色来区分用户界面元素，而不是属性，如按钮边框。 许多元素，如导航栏和状态栏，现在是模糊和透明或透明的与记录在其下的区域的内容视图。 通过和经过模糊处理的条，传达用户界面中的深度的感觉呈现这些内容的视图。

本文介绍如何对用户界面元素在 iOS 7 以及各种 Api 与新的用户界面设计中的更改的几个。

## <a name="view-and-control-changes"></a>查看和控制更改

UIKit 中的视图的所有符合的 iOS 7 新的外观和感觉。 本部分重点介绍一些对这些视图，以及已更改，以支持新的用户界面的相关的 Api 的更改。

### <a name="uibutton"></a>UIButton

从创建的按钮`UIButton`类现边框，默认情况下，没有背景，如下所示：

 ![](ios7-ui-images/button.png "示例 UIButton")

`UIButtonType.RoundedRect`样式已弃用。 如果在 iOS 7 中, 使用`UIButtonType.RoundedRect`将导致`UIButtonType.System`正在使用中，由此产生的默认按钮样式没有后台或可见边缘，如上所示。

### <a name="uibarbuttonitem"></a>UIBarButtonItem

类似于`UIButton`，栏按钮也是边框，默认设置为新`UIBarButtonItemStyle.Plain`样式如下所示：

 ![](ios7-ui-images/barbuttonplain.png "示例 UIBarButtonItem")

此外，`UIBarButtonItemStyle.Bordered`样式已弃用。 设置`UIBarButtonItemStyle.Bordered`在 iOS 7 将导致`UIBarButtonItemStyle.Plain`正在使用的样式。

`UIBarButtonItemStyle.Done`样式不已弃用。 但是，它还将创建无边框的按钮，只能使用一种显示为粗体文本样式，如所示：

 ![](ios7-ui-images/barbuttondone.png "在完成样式示例 UIBarButtonItem")

### <a name="uialertview"></a>UIAlertView

除了新的 iOS 7 外观和感觉的样式更改，警报视图将不再支持通过子视图的自定义。 即使`UIAlertView`继承自`UIView`，则调用`AddSubview`上`UIAlertView`不起作用。 例如，考虑以下代码：

```csharp
UIBarButtonItem button = new UIBarButtonItem ("Bar Button", UIBarButtonItemStyle.Plain, (s,e) =>
{
    UIAlertView alert = new UIAlertView ("Title", "Message", null, "Cancel", "OK");

    alert.AddSubview (new UIView () {
        Frame = new CGRect(50, 50,100, 100),
        BackgroundColor = UIColor.Green
    });

    alert.Show ();
});
```

这将产生的标准的警报视图，与被忽略，则子视图如下所示：

 ![](ios7-ui-images/alert.png "示例 UIAlertView")
 
 注意： UIAlertView iOS 8 中已弃用。 视图[警报控制器](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/)上使用警报视图，在 iOS 8 及以上版本的配方。

### <a name="uisegmentedcontrol"></a>UISegmentedControl

在 iOS 7 中的分段的控件都是透明的并且支持色调颜色。 色调颜色用于文本和边框颜色。 选中段后，就会颜色交换之间背景和文本，并与用来突出显示所选的段中，如下所示的色调颜色：

 ![](ios7-ui-images/segmentedcontrol.png "示例 UISegmentedControl")

此外， `UISegmentedControlStyle` iOS 7 中已弃用。

### <a name="picker-views"></a>选取器视图

选取器视图的 API 是很大程度上不变;但是，iOS 7 的设计准则现在规定选取器视图应该会看到内联，而不是根据输入的视图进行动画处理从底部屏幕或通过新的控制器推送到导航控制器的堆栈，如下所示以前的 iOS 版本。 这可以在系统日历应用中看到：

 ![](ios7-ui-images/inlinepicker.png "这可以在系统日历应用中看到")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

搜索栏现在显示在导航栏时`UISearchDisplayController.DisplaysSearchBarInNavigationBar`属性设置为 true。 当设置为 false 的默认值-当将显示搜索控制器时，会隐藏导航栏。

以下屏幕截图显示搜索栏中的`UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "示例 UISearchDisplayController")

### <a name="uitableview"></a>UITableView

围绕 Api`UITableView`主要是保持不变; 但是，该样式已发生显著变化以符合新的用户界面设计。 内部视图层次结构也是稍有不同的。 此更改不会影响大多数应用程序，但是这是一个需要注意。

#### <a name="grouped-table-style"></a>分组的表样式

更新已更改的分组的样式，现在将扩展到屏幕如下所示的边缘的内容：

 ![](ios7-ui-images/table1.png "示例的分组的表样式")

#### <a name="separatorinset"></a>SeparatorInset

现在可以通过设置缩进行分隔符`UITableVIewCell.SeparatorInset`属性。 例如，下面的代码将用于缩进距左边缘的单元格：

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

这将产生与缩进的单元格的表视图中如下所示：

 ![](ios7-ui-images/separatorinset.png "示例 UITableView SeparatorInset")

#### <a name="table-button-styles"></a>表按钮样式

所有已更改的表视图中使用的各种按钮。 下面的屏幕截图显示在编辑模式下的表视图：

 ![](ios7-ui-images/table2.png "此屏幕截图中在编辑模式下提供的表视图")

### <a name="additional-control-changes"></a>更多控制更改

其他 UIKit 控件也已更改，包括滑块、 交换机和分档器。 这些更改是纯粹 visual。 有关详细信息，请参阅 Apple 的[iOS 7 UI 过渡指导](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html)。

## <a name="general-user-interface-changes"></a>常规用户界面更改

除了 UIKit 更改，iOS 7 引入了各种 visual 更改向用户界面，包括：

-  全屏显示内容
-  条形图外观
-  色调颜色

<a name="fullscreen" />

### <a name="full-screen-content"></a>全屏幕内容

iOS 7 旨在让应用程序能够充分利用整个屏幕。 如果存在-而不是下的状态和导航栏出现查看控制器现在显示通过的状态栏和导航栏的重叠。

为准备 iOS 7 所需的应用程序时，你可以重新调整子视图以可视方式使用*接口生成器*或*Xamarin iOS 设计器*。 你还可以使用新的 Api 之一以帮助以编程方式处理全屏幕内容。 这些 Api 引入下面。

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide 和 BottomLayoutGuide

 `TopLayoutGuide` 和`BottomLayoutGuide`用作视图应开始或结束，其中的引用，以便内容不重叠的半透明`UIKit`栏中的，如以下示例所示：

 [![](ios7-ui-images/clipped.png "不重叠的半透明的 UIKit 栏的示例内容")](ios7-ui-images/clipped.png#lightbox)

这些 Api 可以用于计算视图的偏移量的顶部或底部屏幕，并相应地调整内容放置：

```csharp
public override void ViewDidLayoutSubviews ()
{
    base.ViewDidLayoutSubviews ();

    if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
        nfloat displacement_y = this.TopLayoutGuide.Length;

        //load subviews with displacement
    }

}
```

我们可以使用设置上面计算得出的值我们`ImageView`的顶部的屏幕，因此整个图像是可见的偏移量：

 [![](ios7-ui-images/good2.png "从屏幕顶部的示例 ImageViews 偏移量")](ios7-ui-images/good2.png#lightbox)

请参阅[ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates)有关工作示例。

该视图已添加到层次结构，因此尝试读取后，动态生成的偏移量值`TopLayoutGuide`和`BottomLayoutGuide`中值`ViewDidLoad`将返回 0。 该视图加载之后-例如，在计算值`ViewDidLayoutSubviews`。

> [!IMPORTANT]
> `TopLayoutGuide` 和`BottomLayoutGuide`为了支持新的安全区域布局 iOS 11 中已弃用。 Apple 具有所述，使用安全区域是与 iOS 版本早于 iOS 11 兼容。 有关详细信息，请参阅[更新应用程序以 iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen)指南。

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

此 API 指定视图的哪些边应扩展到全屏显示，而不考虑栏透明度。 IOS 7，在导航栏和工具栏将出现位于上面的控制器视图-与不同在以前的 iOS 版本中，未占用相同的空间。 IOS 7 照片应用程序演示默认`UIViewController.EdgesForExtendedLayout`值， `UIRectEdge.All`。 此设置填充的内容，视图中的所有四个边缘创建的重叠和全屏幕的效果：

 [![](ios7-ui-images/photos.png "Sample EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

点击映像中删除条形图，并演示映像全屏幕：

 [![](ios7-ui-images/photos2.png "删除在条形图 EdgesForExtendedLayout")](ios7-ui-images/photos2.png#lightbox)

由于全屏幕内容是默认值，则配置适用于 iOS 6 的应用程序将具有剪切，如下面的屏幕截图所示的视图的一部分：

 [![](ios7-ui-images/clipped.png "配置适用于 iOS 6 应用将已剪切，如以下屏幕截图所示的视图的一部分")](ios7-ui-images/clipped.png#lightbox)

修改`UIViewController.EdgesForExtendedLayout`属性调整为该行为。 我们可以指定视图不填充任何边缘，因此我们视图会避免通过导航或 （在每个方向上） 的工具栏占用的空间中显示内容：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

我们的应用程序，我们将看到视图再次重新定位，因此，整个图像不可见：

 [![](ios7-ui-images/good.png "使用可见的整个映像的示例")](ios7-ui-images/good.png#lightbox)

请注意，尽管的效果`TopLayoutGuide/BottomLayoutGuide`和`EdgesForExtendedLayout`Api 很相似，它们为了填充不同类型的目标。 更改`EdgesForExtendedLayout`从默认的设置可能会在为 iOS 6，设计的应用程序中修复裁剪后的视图，但良好 iOS 7 设计应接受全屏幕美观并提供全屏幕查看体验，依赖于`TopLayoutGuide`和`BottomLayoutGuide`来正确定位目的是要操作到适合用户的位置的内容。

请参阅[ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates)有关工作示例。

### <a name="status-and-navigation-bars"></a>状态和导航栏

状态栏和导航栏的透明度呈现。 状态栏是深度的透明的，而工具栏和导航栏是深度的透明的模糊传达用户界面中的感觉。 下面的屏幕截图显示此模糊处理和透明度，其中集合视图的蓝色背景色显示通过的状态和导航栏，使它们具有浅蓝色外观：

 ![](ios7-ui-images/transparent-navbar.png "示例状态和导航栏模糊")

#### <a name="status-bar-styles"></a>状态栏样式

以及模糊和透明度，状态栏前景可以 light 或深色 （深色正在默认值）。 可以从视图控制器设置的状态栏样式。 是否隐藏或显示状态栏，还可以设置的视图控制器。

例如，下面的代码替代`PreferredStatusBarStyle`视图控制器，以使显示浅色前景的状态栏的方法：

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

这将导致状态栏中显示如下：

 ![](ios7-ui-images/light-status-bar.png "示例状态栏")

若要隐藏视图控制器代码中的状态栏，重写`PrefersStatusBarHidden`，如下所示：

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

这会隐藏状态栏：

 ![](ios7-ui-images/status-bar-hidden.png "隐藏的状态栏")

### <a name="tint-color"></a>色调颜色

按钮现在显示为 chrome 免文本中。 可以使用新进行控制的文本颜色`TintColor`属性`UIView`。 设置`TintColor`将颜色应用于将其设置的视图的整个视图层次结构。 要应用`TintColor`整个应用程序，请将其设置上`Window`。 你还可以检测何时的色调颜色更改通过`UIView.TintColorDidChange`方法。

例如，下面的屏幕截图显示更改对导航控制器的视图的色调颜色的效果为紫色：

 ![](ios7-ui-images/tint-color.png "对导航控制器视图紫色浅色颜色")

色调颜色可以应用于图像以及当`RenderingMode`设置为`UIImageRenderingMode.AlwaysTemplate`。

> [!IMPORTANT]
> 无法使用设置色调颜色`UIAppearance`。


### <a name="dynamic-type"></a>动态类型

在 iOS 7 中，用户可以指定系统设置中的文本大小。 使用动态类型、 字体将进行动态调整以显示良好而不考虑大小。 `UIFont.PreferredFontForTextStyle` 应该用于获取的用户控制的大小进行了优化的字体。

## <a name="summary"></a>总结

本文介绍如何对用户界面元素在 iOS 7 中的更改。 它检查多个 UIKit，突出显示这两个 visual 更改中的视图和控件所做的更改以及将更改为相关的 Api。 最后，它引入了新的 Api 来使用全屏显示内容、 新的色调颜色支持和动态类型。

## <a name="related-links"></a>相关链接

- [ImageViewer （示例）](https://developer.xamarin.com/samples/monotouch/iOS7-ui-updates)
