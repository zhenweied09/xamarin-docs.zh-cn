---
title: 在 Xamarin.iOS 的统一情节提要
description: 本文档介绍中 Xamarin.iOS 的统一情节提要。 统一的情节提要允许开发人员支持具有单个接口定义的多个屏幕大小。
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 6d3324a6485f2d240ec339f6ce7f03aafe51c80c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792016"
---
# <a name="unified-storyboards-in-xamarinios"></a>在 Xamarin.iOS 的统一情节提要

iOS 8 包括用于创建用户界面的新的、 更简单易用的机制 — 统一情节提要。 使用单个演示图板以涵盖所有不同的硬件的屏幕大小，可以在创建快速且高度可响应视图"设计的一次，使用多"样式。

因为开发人员不再需要创建 iPhone 和 iPad 设备单独和特定的情节提要，它们可以灵活地设计具有一个公共接口的应用程序，然后自定义不同的大小类该接口。 这种方式，应用程序可以适应的每个外形规格的优势，并可以调整每个用户界面以提供最佳体验。

<a name="size-classes" />

## <a name="size-classes"></a>大小类

在 iOS 8 之前，开发人员使用`UIInterfaceOrientation`和`UIInterfaceIdiom`来区分纵向和横向模式之间和 iPhone 和 iPad 设备之间。 在 iOS8，确定方向和设备是使用*大小类*。

设备由大小类定义中垂直和水平轴，并有两种类型的大小在 iOS 8 中的类：

-  **正则**– 这是为较大屏幕大小 （如 iPad) 或使较大的大小的效果的小工具 (如 `UIScrollView`
-  **Compact** – 这是为较小的设备 （如 iPhone)。 此大小会考虑设备的方向。


如果同时使用了这两个概念，则结果将是 2 x 2 网格定义可以在这两个不同的方向中, 使用的不同可能大小，如下面的关系图中所示：

 [![](unified-storyboards-images/sizeclassgrid.png "定义可以使用的不同可能大小按常规模式和压缩的方向 2 x 2 网格")](unified-storyboards-images/sizeclassgrid.png#lightbox)

开发人员可以创建使用任何四个可能的匹配项 （如上面的图形中所示），会导致不同的布局视图控制器。

### <a name="ipad-size-classes"></a>iPad 大小类

IPad，大小，由于具有**正则**类这两个方向的大小。

 [![](unified-storyboards-images/image1.png "iPad 大小类")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>iPhone 大小类

IPhone 具有不同的大小类基于设备的方向：

 [![](unified-storyboards-images/iphonesizeclasses.png "iPhone 大小类")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  当设备处于纵向模式时，屏幕上有**压缩**水平类和**正则**垂直
-  当设备处于横向模式中时，屏幕类从纵向模式恰好相反。

### <a name="iphone-6-plus-size-classes"></a>iPhone 6 Plus 大小类

大小是在纵向，但具有不同环境中更早版本的 Iphone 相同：

[![](unified-storyboards-images/iphone6sizeclasses.png "iPhone 6 Plus 大小类")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

因为 iPhone 6 Plus 具有足够大的屏幕，它就可以在横向模式中有一个常规的宽度大小类。

### <a name="support-for-a-new-screen-scale"></a>支持为一个新的屏幕缩放的

IPhone 6 Plus 使用新的 Retina HD 显示 3.0 （三次原始 iPhone 屏幕分辨率） 的屏幕缩放比例。 若要在这些设备上提供可能的最佳体验，包括为此屏幕缩放设计的新图片。 在 Xcode 6 和更高，资产目录可以包括映像 1 x、 2 x 和 3 x 大小;只需将添加新的图像资产和 iOS 在 iPhone 6 上运行时，将选择正确的资产加上。

此外加载在 iOS 中的行为的图像识别`@3x`上图像文件后缀。 例如，如果开发人员使用以下文件名称的应用程序的包中包括图像资产 （在不同的解决方法）： `MonkeyIcon.png`， `MonkeyIcon@2x.png`，和`MonkeyIcon@3x.png`。 在 iPhone 6 Plus`MonkeyIcon@3x.png`映像将自动使用，如果开发人员加载图像使用以下代码：

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
如果它们分配给使用 iOS UI 元素的图像或设计器作为`MonkeyIcon.png`、`MonkeyIcon@3x.png`将使用，自动重新 iPhone 6 上加上。

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>动态启动屏幕

当 iOS 应用程序启动程序应用程序是实际开始向上的用户提供反馈，启动屏幕文件将显示为初始屏幕。 在 iOS 8 之前开发人员将不得不包括多个`Default.png`图像将在运行应用程序的每个设备类型、 方向和屏幕分辨率的资产。

新到 iOS 8，开发人员可以创建单个原子`.xib`文件中使用自动布局和大小类来创建的 Xcode*动态启动屏幕*，将适用于每个设备、 分辨率和方向。 这不仅减少开发人员可以创建和维护的所有所需的图像资产，所需的工作量，但它减少了应用程序的安装包的大小。

## <a name="traits"></a>特征

特征是可以用于确定如何布局随着其环境的变化而发生变化的属性。 它们包含的一组属性 (`HorizontalSizeClass`和`VerticalSizeClass`基于`UIUserInterfaceSizeClass`)，以及接口惯用语法 ( `UIUserInterfaceIdiom`) 和显示小数位数。

所有上面的状态将封装在 Apple 指特征集合的容器 ( `UITraitCollection`)，其中包含属性不仅以及它们的值。

## <a name="trait-environment"></a>特征环境

特征环境中 iOS 8 的新接口，并且能够返回特征集合以下对象：

-  屏幕 ( `UIScreens` )。
-  Windows ( `UIWindows` )。
-  查看控制器 ( `UIViewController` )。
-  视图 ( `UIView` )。
-  演示文稿控制器 ( `UIPresentationController` )。


开发人员使用返回特征环境的特征集合来确定如何应布局的用户界面。

所有特征环境使层次结构，如下面的关系图中所示：

 [![](unified-storyboards-images/viewhierarchy.png "特征环境层次结构关系图")](unified-storyboards-images/viewhierarchy.png#lightbox)

特性集合，每个以上的特征环境包含将进行流处理，默认情况下，是从父级到子级环境。

获取当前的特征集合，除了特征环境具有`TraitCollectionDidChange`方法，可以在视图或视图控制器子类中重写该方法。 开发人员可以使用此方法来修改任何依赖于特征，当更改这些特征的 UI 元素。

## <a name="typical-trait-collections"></a>典型的特征集合

本部分将介绍的典型类型的用户体验时使用 iOS 8 的特性集合。

以下是开发人员可能会看到在 iPhone 的典型特征集合：

|属性|“值”|
|--- |--- |
|`HorizontalSizeClass`|压缩|
|`VerticalSizeClass`|规则|
|`UserInterfaceIdom`|电话|
|`DisplayScale`|2.0|

设置了上述将表示的完全限定特征集合，因为它具有所有其特征属性的值。

还有可能有一个缺少它的一些值的特征集合 (它是 Apple 指作为*未指定*):

|属性|“值”|
|--- |--- |
|`HorizontalSizeClass`|压缩|
|`VerticalSizeClass`|未指定|
|`UserInterfaceIdom`|未指定|
|`DisplayScale`|未指定|

通常情况下，但是，当开发人员请求其特征集合特征环境，它将返回的完全限定的集合在上面的示例所示。

如果特征环境 （如视图或视图控制器） 不是在当前视图层次结构内部，开发人员可能会返回未指定的值的一个或多个特征属性。

如果它们使用提供由 Apple，如创建方法之一，开发人员还将获得部分限定的特征集合`UITraitCollection.FromHorizontalSizeClass`，以创建新的集合。

一个可以在多个特征集合执行的操作比较它们到对方，涉及到要求一个特征集合是否包含另一个。 通过意思*包含*是，对于第二个集合中指定任何特征，值必须匹配完全与第一个集合中的值。

若要测试两个特征，请使用`Contains`方法`UITraitCollection`传入要测试的特性的值。

开发人员可以手动执行比较，代码中用于确定如何布局视图或视图控制器。 但是，`UIKit`内部使用此方法来提供其部分功能，如下所示外观代理，例如。

## <a name="appearance-proxy"></a>外观代理

若要允许开发人员自定义其视图的属性的 iOS 的早期版本中引入了外观代理。 它已在 iOS 8 以支持特征集合中扩展。

外观代理现在包含新方法， `AppearanceForTraitCollection`，，为已传入的给定特征集合返回一个新的外观代理。 开发人员执行的任何自定义外观代理将到指定的特征集合中只会影响符合的视图。

通常会开发人员传入部分指定的特征集合与`AppearanceForTraitCollection`方法，例如刚刚指定水平大小类的压缩，以便它们可以自定义的任意视图中的应用程序在 compact 水平方向。

## <a name="uiimage"></a>UIImage

Apple 已添加到特征集合是的另一个类`UIImage`。 在过去的开发人员必须指定@1X和@2x它们，我们想在应用程序 （例如图标） 中包含任何位图图形资产版本。

iOS 8 已扩展为允许开发人员可以在基于特征集合的映像目录中包含多个版本的映像。 例如，开发人员可能包括用于处理 Compact 的特征类和任何其他集合的完整大小的映像较小的图像。

映像之一内的使用时`UIImageView`类，图像视图将自动显示为其特征集合的正确版本的映像。 如果特征环境更改 （如用户切换纵向的设备为横向） 时，图像视图将自动选择新的映像大小，以匹配新的特征集合并更改其大小以匹配的当前版本的映像正在显示。

## <a name="uiimageasset"></a>UIImageAsset

Apple 已添加到 iOS 8 名的新类`UIImageAsset`以便开发人员更多控制映像选择。

图像资产以上所有映像的不同版本，并允许开发人员寻求匹配已传入的特征集合的特定映像。 映像可以无法添加或删除图像资产，即时上。

图像资产的详细信息，请参阅 Apple 的[UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)文档。

## <a name="combining-trait-collections"></a>组合特征集合

开发人员可以执行特征的集合中的另一个功能是添加两个在一起，将导致组合后的集合，其中一个集合中的未指定的值将替换为在第二个指定的值。 这是使用`FromTraitsFromCollections`方法`UITraitCollection`类。

如前所述，如果任何特征的一个特征集合中未指定，在另一个指定，则值将设置为指定的版本。 但是，如果没有指定的给定值的多个版本，值从上一个特征集合将使用的值。

## <a name="adaptive-view-controllers"></a>自适应的视图控制器

本部分将介绍如何 iOS 视图和视图控制器已采用的特征和大小类自动为开发人员应用程序中更自适应的概念的详细信息。

### <a name="split-view-controller"></a>拆分视图控制器

最多在 iOS 8 中已更改的视图控制器类之一是`UISplitViewController`类。 在过去，开发人员通常都会使用拆分视图控制器的 iPad 版应用程序，然后他们可能需要为 iPhone 版本的应用程序提供其视图层次结构的完全不同版本。

在 iOS 8，`UISplitViewController`它允许开发人员可以创建将用于 iPhone 和 iPad 的功能的一个视图控制器层次结构，类是在 （iPad 和 iPhone），这两个平台上可用。

横向 iPhone 时，拆分视图控制器将提供其视图的并行安装，就像将在 iPad 上显示时。

### <a name="overriding-traits"></a>重写特征

从下的子容器，如下所示下图显示拆分视图控制器以横向方式 iPad 上的父容器级联特征环境：

 [![](unified-storyboards-images/cascadingclasses01.png "横向 iPad 上拆分视图控制器")](unified-storyboards-images/cascadingclasses01.png#lightbox)

因为 iPad 具有常规大小类中的水平和垂直方向，拆分视图会显示 master 和详细信息视图。

在 iPhone 上，其中大小类是在这两个方向 compact，拆分视图控制器仅显示详细信息视图中，如下所示：

 [![](unified-storyboards-images/cascadingclasses02.png "拆分视图控制器仅显示详细信息视图")](unified-storyboards-images/cascadingclasses02.png#lightbox)

在应用程序开发人员要横向显示在 iPhone 上的 master 和详细信息视图，开发人员必须为拆分视图控制器插入父容器和重写特征集合。 下图中所示：

 [![](unified-storyboards-images/cascadingclasses03.png "开发人员必须为拆分视图控制器插入父容器，并重写特征集合")](unified-storyboards-images/cascadingclasses03.png#lightbox)

A`UIView`设置为拆分视图控制器的父级和`SetOverrideTraitCollection`视图中新的特征集合传递和目标确定拆分视图控制器上调用方法。 替代新特征收集`HorizontalSizeClass`，将其设置为`Regular`，以便拆分视图控制器将以横向方式显示在 iPhone 上的 master 和详细信息视图。

请注意，`VerticalSizeClass`已设置为`unspecified`，这允许新的特征集合，要添加到父代、 的特征集合导致`Compact VerticalSizeClass`子拆分视图控制器。

### <a name="trait-changes"></a>特征更改

本部分将需要了解的信息，详细的介绍，特征集合时特征环境发生变化的转换。 例如，当将设备旋转从纵向改为布局。

 [![](unified-storyboards-images/traittransitions01.png "纵向改为横向特征更改概述")](unified-storyboards-images/traittransitions01.png#lightbox)

首先，iOS 8 执行某些安装程序，以准备进行转换。 接下来，系统进行动画处理的转换状态。 最后，iOS 8 中清除转换的过程所需的任何临时状态。

iOS 8 提供开发人员可以使用下表中所示参与特征更改的多个回调：

|Phase|回调|描述|
|--- |--- |--- |
|安装|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>特征集合获取设置为其新值之前，获取特征更改开头调用此方法。</li><li>特性集合的值更改时，但任何动画发生之前，获取调用该方法。</li></ul>|
|动画|`WillTransitionToTraitCollection`|获取传递给此方法转换处理协调器具有`AnimateAlongside`允许开发人员添加将执行以及默认动画的动画的属性。|
|清理|`WillTransitionToTraitCollection`|提供要转换发生后包括自己清理代码的开发人员的方法。|

`WillTransitionToTraitCollection`方法非常适合进行查看控制器以及特征集合更改动画处理。 `WillTransitionToTraitCollection`方法仅可在上找到视图控制器 ( `UIViewController`) 而不是在其他特征环境，如`UIViews`。

`TraitCollectionDidChange`非常适合使用`UIView`类，开发人员希望更新 UI 中的特点是发生更改。

### <a name="collapsing-the-split-view-controllers"></a>折叠拆分视图控制器

现在让我们采用什么进一步查看时发生拆分视图控制器折叠为一个列视图从两个列。 作为此更改的一部分，有两个需要执行的进程：

-  默认情况下，拆分视图控制器将使用主视图控制器作为视图折叠发生后。 开发人员可以在通过重写的情况下重写此行为`GetPrimaryViewControllerForCollapsingSplitViewController`方法`UISplitViewControllerDelegate`并提供任何他们想要在折叠状态中显示的视图控制器。
-  辅助的视图控制器必须获取合并到主视图控制器。 通常开发人员不需要执行任何操作，此步骤;拆分视图控制器包括硬件设备上基于此阶段的自动处理。 但是，可能有一些特殊情况下，开发人员将需要进行此更改后进行交互。 调用`CollapseSecondViewController`方法`UISplitViewControllerDelegate`允许母版视图控制器时出现崩溃，而不是详细信息视图显示。


### <a name="expanding-the-split-view-controller"></a>展开拆分视图控制器

现在让我们采用什么进一步查看发生拆分视图控制器为折叠状态从扩展。 同样，有需要执行的两个阶段：

-  首先，定义新的主视图控制器。 默认情况下，拆分视图控制器将自动使用来自折叠的视图的主视图控制器。 同样，开发人员可以替代此行为使用`GetPrimaryViewControllerForExpandingSplitViewController`方法`UISplitViewControllerDelegate`。
-  一旦已选择主视图控制器，则必须重新创建辅助的视图控制器。 同样，拆分视图控制器包括硬件设备上基于此阶段的自动处理。 开发人员可以通过调用重写此行为`SeparateSecondaryViewController`方法`UISplitViewControllerDelegate`。


在拆分视图控制器中，主视图控制器所起的作用中同时展开和折叠的视图，通过实现`CollapseSecondViewController`和`SeparateSecondaryViewController`方法`UISplitViewControllerDelegate`。 `UINavigationController` 实现这些方法来自动推送和弹出辅助视图控制器。

### <a name="showing-view-controllers"></a>显示视图控制器

另一个 Apple iOS 8 对所做的更改是在开发人员，显示查看控制器的方式。 在过去，如果应用程序必须在叶视图控制器 （例如表视图中的控制器），并且开发人员介绍了一个不同 （例如，在对上一个单元格用户点击响应），应用程序将访问返回到控制器层次结构导航视图控制器和调用`PushViewController`针对它，以显示新视图的方法。

这就造成导航控制器和它正在其中运行的环境之间紧密耦合。 在 iOS 8 中，Apple 已分离这通过提供两个新方法：

-  `ShowViewController` – 适应显示基于其环境的新视图控制器。 例如，在`UINavigationController`只需将推送到堆栈上的新视图。 在拆分视图控制器中，新视图控制器将在左侧显示为新的主视图控制器。 如果存在任何容器视图控制器，不则新视图将显示为模式的视图控制器。
-  `ShowDetailViewController` – 一种类似的方式在工作`ShowViewController`，但在拆分视图控制器的详细信息视图将替换为正在传递新视图控制器上实现。 调用 （如可能在 iPhone 应用程序中所示），拆分视图控制器处于折叠状态，如果将重定向到`ShowViewController`方法和新视图将显示为主视图控制器。 同样，如果存在任何容器视图控制器，不则新视图将显示为模式的视图控制器。


这些方法用于开始叶视图控制器和遍历查看层次结构，直到找到正确的容器视图控制器，以处理新视图的显示。

开发人员可以实现`ShowViewController`和`ShowDetailViewController`若要获取其自己自定义视图控制器中相同自动功能的`UINavigationController`和`UISplitViewController`提供。

### <a name="how-it-works"></a>它是如何工作

本部分中我们将看看如何这些方法实际实现 iOS 8 中。 第一个让我们看一下新`GetTargetForAction`方法：

 [![](unified-storyboards-images/gettargetforaction.png "新的 GetTargetForAction 方法")](unified-storyboards-images/gettargetforaction.png#lightbox)

此方法指导的层次结构链，直到找到正确的容器视图控制器。 例如：

1.  如果`ShowViewController`方法被调用时，实现此方法的链中的第一个视图控制器是导航控制器中，因此它被用作新视图的父级。
1.  如果`ShowDetailViewController`方法已改为调用时，拆分视图控制器是第一个的视图控制器，来实现，因此它使用与父项。


`GetTargetForAction`方法的工作原理是查找实现某个给定的操作的视图控制器，然后询问该视图控制器，如果想要接收该操作。 此方法是公共的因为开发人员可以创建其自己自定义方法来函数就像内置在`ShowViewController`和`ShowDetailViewController`方法。

## <a name="adaptive-presentation"></a>自适应 Presentation

在 iOS 8 中，Apple 所作的 Popover 演示文稿 ( `UIPopoverPresentationController`) 自适应以及。 因此 Popover 演示文稿视图控制器将自动出现在正则大小类正常 Popover 视图，但会将其显示全屏水平 compact 大小类中 (如在 iPhone 上)。

若要适应统一情节提要系统中的更改，已被新的控制器对象创建管理提供的视图控制器- `UIPresentationController`。 此控制器被创建的视图控制器看到，除非它被关闭的时间。 由于它是管理类，它可以被视为超级类通过视图控制器响应会影响哪些然后放的回视图控制器演示文稿控制器控制视图控制器 （如方向） 的设备更改。

开发人员提供视图控制器使用`PresentViewController`演示文稿过程的管理方法，传递给`UIKit`。 UIKit 处理 （还有其它用途） 正在创建的样式的正确控制器，唯一的例外是时与视图控制器已设置为样式`UIModalPresentationCustom`。 在这里，应用程序可以提供它是自己 PresentationController，而不是使用`UIKit`控制器。

### <a name="custom-presentation-styles"></a>自定义的演示文稿样式

具有自定义的演示文稿样式、 开发人员可以选择使用自定义的演示文稿控制器。 此自定义控制器可以用于修改的外观和行为，它是 allied 到视图。

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>使用大小类

本文包含自适应照片 Xamarin 项目提供在 iOS 8 统一接口应用程序中使用大小类和自适应的视图控制器的工作示例。

虽然应用程序创建其 UI 完全代码，而不是使用 IOS 设计器中，并且创建一个统一的情节提要，同样的技术应用。 更高版本在本文中，我们将演示如何使用统一的情节提要和 iOS 的 Xamarin 应用程序中的设计器的大小类。

现在让我们看一下如何自适应照片项目正在实现的几项大小类功能 iOS 8 以创建自适应的应用程序中。

### <a name="adapting-to-trait-environment-changes"></a>适应特征环境更改

运行时自适应的照片应用程序在 iPhone 上，用户旋转纵向改为横向的设备时，拆分视图控制器将显示的 master 和详细信息视图：

 [![](unified-storyboards-images/rotation.png "拆分视图控制器将显示这两个母版和详细信息视图如下所示")](unified-storyboards-images/rotation.png#lightbox)

这通过重写实现`UpdateConstraintsForTraitCollection`的值基于方法的视图控制器和调整约束`VerticalSizeClass`。 例如：

```csharp
public void UpdateConstraintsForTraitCollection (UITraitCollection collection)
{
    var views = NSDictionary.FromObjectsAndKeys (
        new object[] { TopLayoutGuide, ImageView, NameLabel, ConversationsLabel, PhotosLabel },
        new object[] { "topLayoutGuide", "imageView", "nameLabel", "conversationsLabel", "photosLabel" }
    );

    var newConstraints = new List<NSLayoutConstraint> ();
    if (collection.VerticalSizeClass == UIUserInterfaceSizeClass.Compact) {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide][imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.Add (NSLayoutConstraint.Create (ImageView, NSLayoutAttribute.Width, NSLayoutRelation.Equal,
            View, NSLayoutAttribute.Width, 0.5f, 0.0f));
    } else {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]-20-[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));
    }

    if (constraints != null)
        View.RemoveConstraints (constraints.ToArray ());

    constraints = newConstraints;
    View.AddConstraints (constraints.ToArray ());
}
```

### <a name="adding-transition-animations"></a>添加过渡动画

当应用程序将从自适应照片中的拆分视图控制器折叠状态为展开时，动画将添加到默认动画通过重写`WillTransitionToTraitCollection`视图控制器的方法。 例如：

```csharp
public override void WillTransitionToTraitCollection (UITraitCollection traitCollection, IUIViewControllerTransitionCoordinator coordinator)
{
    base.WillTransitionToTraitCollection (traitCollection, coordinator);
    coordinator.AnimateAlongsideTransition ((UIViewControllerTransitionCoordinatorContext) => {
        UpdateConstraintsForTraitCollection (traitCollection);
        View.SetNeedsLayout ();
    }, (UIViewControllerTransitionCoordinatorContext) => {
    });
}
```

### <a name="overriding-the-trait-environment"></a>重写特征环境

如上面所示，自适应的照片应用程序就会强制拆分视图控制器，以显示详细信息和母版视图 iPhone 设备处于横向视图时。

这是通过与视图控制器中使用下面的代码来实现的：

```csharp
private UITraitCollection forcedTraitCollection = new UITraitCollection ();
...

public UITraitCollection ForcedTraitCollection {
    get {
        return forcedTraitCollection;
    }

    set {
        if (value != forcedTraitCollection) {
            forcedTraitCollection = value;
            UpdateForcedTraitCollection ();
        }
    }
}
...

public override void ViewWillTransitionToSize (SizeF toSize, IUIViewControllerTransitionCoordinator coordinator)
{
    ForcedTraitCollection = toSize.Width > 320.0f ?
         UITraitCollection.FromHorizontalSizeClass (UIUserInterfaceSizeClass.Regular) :
         new UITraitCollection ();

    base.ViewWillTransitionToSize (toSize, coordinator);
}

public void UpdateForcedTraitCollection ()
{
    SetOverrideTraitCollection (forcedTraitCollection, viewController);
}
```

### <a name="expanding-and-collapsing-the-split-view-controller"></a>展开和折叠拆分视图控制器

下一步如何在 Xamarin 中实现的扩展和折叠行为的拆分视图控制器已让我们看一下。 在`AppDelegate`、 创建拆分视图控制器后，其委托分配来处理这些更改：

```csharp
public class SplitViewControllerDelegate : UISplitViewControllerDelegate
{
    public override bool CollapseSecondViewController (UISplitViewController splitViewController,
        UIViewController secondaryViewController, UIViewController primaryViewController)
    {
        AAPLPhoto photo = ((CustomViewController)secondaryViewController).Aapl_containedPhoto (null);
        if (photo == null) {
            return true;
        }

        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            var viewControllers = new List<UIViewController> ();
            foreach (var controller in ((UINavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containsPhoto");

                if ((bool)method.Invoke (controller, new object[] { null })) {
                    viewControllers.Add (controller);
                }
            }

            ((UINavigationController)primaryViewController).ViewControllers = viewControllers.ToArray<UIViewController> ();
        }

        return false;
    }

    public override UIViewController SeparateSecondaryViewController (UISplitViewController splitViewController,
        UIViewController primaryViewController)
    {
        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            foreach (var controller in ((CustomNavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containedPhoto");

                if (method.Invoke (controller, new object[] { null }) != null) {
                    return null;
                }
            }
        }

        return new AAPLEmptyViewController ();
    }
}
```

`SeparateSecondaryViewController`方法测试一张照片如果显示的是，以及采用基于该状态操作。 没有照片是否正在显示其折叠辅助的视图控制器，以便显示母版视图控制器。

`CollapseSecondViewController`方法用于当展开拆分视图控制器时如果因此它折叠回该视图，请参阅在堆栈上，是否存在任何照片。

### <a name="moving-between-view-controllers"></a>查看控制器之间移动

接下来，让我们看看如何查看控制器之间移动自适应的照片应用程序。 在`AAPLConversationViewController`类当用户从表格中，选择一个单元格`ShowDetailViewController`调用方法以显示详细信息视图：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    var photo = PhotoForIndexPath (indexPath);
    var controller = new AAPLPhotoViewController ();
    controller.Photo = photo;

    int photoNumber = indexPath.Row + 1;
    int photoCount = (int)Conversation.Photos.Count;
    controller.Title = string.Format ("{0} of {1}", photoNumber, photoCount);
    ShowDetailViewController (controller, this);
}
```

### <a name="displaying-disclosure-indicators"></a>显示泄露指示器

在自适应的照片应用程序有多个位置隐藏或显示基于更改到特征环境泄露指示器的位置。 这被处理替换为以下代码：

```csharp
public bool Aapl_willShowingViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}

public bool Aapl_willShowingDetailViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingDetailViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}
```

这些功能实现使用`GetTargetViewControllerForAction`详细上面讨论的方法。

当表视图控制器所显示数据时，它使用上面的实现以查看推送即将发生这种情况，以及以显示或隐藏相应地泄露指示器的方法：

```csharp
public override void WillDisplay (UITableView tableView, UITableViewCell cell, NSIndexPath indexPath)
{
    bool pushes = ShouldShowConversationViewForIndexPath (indexPath) ?
         Aapl_willShowingViewControllerPushWithSender () :
         Aapl_willShowingDetailViewControllerPushWithSender ();

    cell.Accessory = pushes ? UITableViewCellAccessory.DisclosureIndicator : UITableViewCellAccessory.None;
    var conversation = ConversationForIndexPath (indexPath);
    cell.TextLabel.Text = conversation.Name;
}
```

### <a name="new-showdetailtargetdidchangenotification-type"></a>新`ShowDetailTargetDidChangeNotification`类型

Apple 已使用大小类和拆分视图控制器中的特征环境添加新的通知类型`ShowDetailTargetDidChangeNotification`。 当目标为拆分视图控制器的详细信息视图发生更改时，如控制器展开或折叠时，获取发送此通知。

自适应的照片应用程序使用此通知更新泄露指示器的状态详细信息视图控制器更改时：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    TableView.RegisterClassForCellReuse (typeof(UITableViewCell), AAPLListTableViewControllerCellIdentifier);
    NSNotificationCenter.DefaultCenter.AddObserver (this, new Selector ("showDetailTargetDidChange:"),
        UIViewController.ShowDetailTargetDidChangeNotification, null);
    ClearsSelectionOnViewWillAppear = false;
}
```

深入了解一下自适应照片的应用程序以各种方式查看该大小类，可以使用特征集合和自适应查看控制器在 Xamarin.iOS 轻松创建一个统一的应用程序。

## <a name="unified-storyboards"></a>统一的情节提要

新统一情节提要允许开发人员可以创建一个，到 iOS 8，统一可由面向多个大小类显示在 iPhone 和 iPad 设备的情节提要文件。 通过使用统一的情节提要，开发人员编写更少 UI 的特定代码，只有一个接口设计，可以创建和维护。

统一的情节提要的主要优点有：

-  将相同的情节提要文件用于 iPhone 和 iPad。
-  将部署到 iOS 6 和 iOS 7 的向后。
-  预览不同的设备、 方向和所有从 Xamarin iOS 设计器中的操作系统版本的布局。

对于 Mac 中，将此功能完全支持在 Visual Studio 中

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>启用大小类

默认情况下，任何新的 Xamarin.iOS 项目将我们大小类。 若要使用内部从旧项目情节提要的大小类和自适应 Segue，它必须首先将转换为 Xcode 6 统一情节提要格式从 iOS 设计器内。

若要执行此打开情节提要设计器并检查 iOS 中要转换**使用大小类**复选框：

 [![](unified-storyboards-images/sizeclass01.png "使用大小类复选框")](unified-storyboards-images/sizeclass01.png#lightbox)

IOS 设计器将确认开发人员想要转换的情节提要即可使用大小类格式：

 [![](unified-storyboards-images/sizeclass02.png "使用大小类警报")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> 自动布局还必须检查大小类才能正常工作。

### <a name="generic-device-types"></a>泛型的设备类型

一旦转换后的情节提要，若要使用大小类，它将重新显示在设计图面和**视图作为**设备将泛型：

 [![](unified-storyboards-images/sizeclass03.png "查看作为泛型的设备类型")](unified-storyboards-images/sizeclass03.png#lightbox)

当选中泛型的设备类型时，所有视图控制器将都调整到 600 x 600 正方形。 此正方形表示任何宽度和任何高度的大小。 当 iOS 设计器在此模式下，任何编辑将应用于的所有大小类。

开发人员还可以查看设计图面作为 iPhone 的选项：

 [![](unified-storyboards-images/sizeclass04.png "以 iPhone 形式查看设计图面")](unified-storyboards-images/sizeclass04.png#lightbox)

或以 iPad 形式查看它：

 [![](unified-storyboards-images/sizeclass05.png "以 iPad 形式查看设计图面")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>选择大小类

大小类选择器按钮位于设计图面 （附近查看方式下拉列表） 的左上角。 它允许开发人员选择的大小类当前正在编辑：

 [![](unified-storyboards-images/sizeclass06.png "选择大小类")](unified-storyboards-images/sizeclass06.png#lightbox)

选择器显示为 3 x 3 网格大小类选项。 在网格中的平方的每一个表示宽度类和一个高度类的组合。 中心方形选择的任何 Width/Any 高度大小类 （这是一个统一的情节提要的默认视图）。 当选中此方块时，开发人员正在编辑的所有其他配置都将继承的默认布局。

网格的左上角正方形表示 Compact Compact 宽度/高度大小类：

 [![](unified-storyboards-images/sizeclass07.png "Compact 宽度/Compact 高度大小类")](unified-storyboards-images/sizeclass07.png#lightbox)

此模式下对应于横向 iPhone。 在右下角的网格方形表示正则常规宽度/高度大小类，该类表示 iPad:

 [![](unified-storyboards-images/sizeclass08.png "正则宽度/常规高度大小类")](unified-storyboards-images/sizeclass08.png#lightbox)

若要编辑纵向 iPhone 的布局，选择在左下角的正方形。 这表示 Compact 的正则宽度/高度大小类：

 [![](unified-storyboards-images/sizeclass09.png "Compact 宽度/常规高度大小类")](unified-storyboards-images/sizeclass09.png#lightbox)

单击中方块以选择它并在设计图面将更改要匹配的新选择的视图控制器的大小：

 [![](unified-storyboards-images/sizeclass10.png "设计图面将更改视图控制器，以匹配新的选择，如所示的大小")](unified-storyboards-images/sizeclass10.png#lightbox)

大小类以及它们如何影响布局 Iphone 和 Ipad 上，请参阅此文章了解详细信息的大小类部分。

### <a name="adaptive-segue-types"></a>自适应 Segue 类型

如果开发人员已使用情节提要之前，则它们将熟悉的现有 segue 类型**推送**，**模式**和**Popover**。 以下自适应 Segue 类型 （对应于新的视图控制器 API 上文所述） 时在统一情节提要文件上启用了大小类，可供：**显示**和**显示详细信息**.

> [!IMPORTANT]
> 启用大小类后，任何现有 segue 将转换为新类型。

需要的 iOS 示例 8 应用程序使用母版视图中有一个简单的游戏导航菜单，拆分视图控制器统一的情节提要。 如果用户单击菜单按钮上时，选定的项的视图控制器应显示在拆分视图控制器的详细信息部分中，在 iPad 上运行时。 在 iPhone 上的项视图控制器应推送到导航堆栈上。

若要实现此效果，请在 iOS 设计器中控件的单击的按钮并拖动线条到要显示的视图控制器。 当释放鼠标按钮时，选择`Show Detail`从 Segue 类型弹出菜单：

 [![](unified-storyboards-images/segue01.png "从 Segue 类型弹出菜单中选择显示详细信息")](unified-storyboards-images/segue01.png#lightbox)

将按钮和视图控制器之间创建新的 segue。 现在在 iPhone 模拟器中运行应用程序，将显示主菜单上：

 [![](unified-storyboards-images/segue02.png "主菜单")](unified-storyboards-images/segue02.png#lightbox)

单击**选择游戏**按钮和项的视图控制器将推送到导航堆栈上：

 [![](unified-storyboards-images/segue03.png "项视图控制器将推送到导航堆栈上，如所示")](unified-storyboards-images/segue03.png#lightbox)

停止模拟器 iPhone 和 iPad 模拟器中运行应用程序。 切换到横向和 main 再次显示菜单：

 [![](unified-storyboards-images/segue04.png "显示在主菜单")](unified-storyboards-images/segue04.png#lightbox)

再次单击**选择游戏**拆分视图控制器的详细信息部分中将显示按钮和项的视图控制器：

 [![](unified-storyboards-images/segue05.png "项的拆分视图控制器详细信息部分中显示的视图控制器")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>从大小类中排除元素

没有给定的元素 （如视图、 控件或约束） 不在特定的大小类所需的时间。 若要从大小类中排除元素，选择要排除在所需的项**设计图面**。 滚动到底部**属性资源管理器**单击**齿轮**下拉菜单。 选择的组合**宽度**和**高度**要排除的项：

[![](unified-storyboards-images/exclude-a.png "选择宽度和高度的组合")](unified-storyboards-images/exclude-a.png#lightbox)

一个新*排除用例*将添加到底部中的元素**属性资源管理器**。 接下来，取消选中**已安装**复选框为给定的大小类：

[![](unified-storyboards-images/exclude-b.png "取消选中已安装复选框")](unified-storyboards-images/exclude-b.png#lightbox)

切换到的宽度和高度的项已从排除的设计图面，已从给定的大小类，但不是整个的 UI 设计：

 [![](unified-storyboards-images/exclude02.png "切换到的宽度和高度的项已从排除的设计图面")](unified-storyboards-images/exclude02.png#lightbox)

切换回 Any Width/Any 高度大小类和的元素已仍到位：

 [![](unified-storyboards-images/exclude03.png "切换回 Any Width/Any 高度大小类")](unified-storyboards-images/exclude03.png#lightbox)

当在 iPad 模拟器中运行应用程序时，在元素出现：

 [![](unified-storyboards-images/exclude04.png "时显示的元素在 iPad 模拟器正在运行的应用")](unified-storyboards-images/exclude04.png#lightbox)

并在 iPhone 模拟器上运行应用程序时，该元素是缺少：

 [![](unified-storyboards-images/exclude05.png "元素缺少时在 iPhone 模拟器中正在运行的应用")](unified-storyboards-images/exclude05.png#lightbox)

若要从元素中删除为排除用例，只需选择中的元素**设计图面**，滚动到底部**属性资源管理器**单击**-** 要删除的用例旁边的按钮。

若要查看 Unified 情节提要的实现，查看`UnifiedStoryboard`示例 Xamarin iOS 8 应用程序附加到此文档。

## <a name="dynamic-launch-screens"></a>动态启动屏幕

当 iOS 应用程序启动程序应用程序是实际开始向上的用户提供反馈，启动屏幕文件将显示为初始屏幕。 在 iOS 8 之前开发人员将不得不包括多个`Default.png`图像将在运行应用程序的每个设备类型、 方向和屏幕分辨率的资产。 例如， `Default@2x.png`， `Default-Landscape@2x~ipad.png`， `Default-Portrait@2x~ipad.png`，等等。

将分解在新的 iPhone 6 和 iPhone 6 Plus 设备 （和即将进行的 Apple Watch） 与所有现有的 iPhone 和 iPad 设备，这表示不同的大小、 方向和解决方法的一个大数组`Default.png`必须启动屏幕图像资产创建和维护。 此外，这些文件可能会很大，并且将"膨胀"可交付结果的应用程序捆绑，增加从 iTunes 应用商店 （可能阻止将其能够通过移动电话网络中传递） 下载应用程序所需的时间量和增加最终用户的设备上所需的存储量。

新到 iOS 8，开发人员可以创建单个原子`.xib`文件中使用自动布局和大小类来创建的 Xcode*动态启动屏幕*，将适用于每个设备、 分辨率和方向。 这不仅减少开发人员可以创建和维护的所有所需的图像资产，所需的工作量，但它可以大大减少应用程序的安装包的大小。


动态启动屏幕具有以下限制和注意事项：

 - 仅使用`UIKit`类。
 - 使用的单个根视图`UIView`或`UIViewController`对象。
 - 不要进行任何连接到应用程序的代码 (未添加**操作**或**Outlet**)。
 - 未添加`UIWebView`对象。
 - 不要使用任何自定义类。
 - 不要使用运行时特性。

记住上面的指南，让我们看一下将动态启动屏幕添加到现有的 Xamarin iOS 8 项目。

请执行以下操作：

1. 打开**Visual Studio for Mac**和加载**解决方案**添加到动态启动屏幕。
2. 在**解决方案资源管理器**，右键单击`MainStoryboard.storyboard`文件，然后选择**打开** > **Xcode 接口生成器**:

    [![](unified-storyboards-images/dls01.png "使用 Xcode 接口生成器打开")](unified-storyboards-images/dls01.png#lightbox)
3. 在 Xcode 中，选择**文件** > **新建** > **文件...**:

    [![](unified-storyboards-images/dls02.png "选择文件 / 新建")](unified-storyboards-images/dls02.png#lightbox)
4. 选择**iOS** > **用户界面** > **启动屏幕**单击**下一步**按钮：

    [![](unified-storyboards-images/dls03.png "选择 iOS / 用户界面 / 启动屏幕")](unified-storyboards-images/dls03.png#lightbox)
5. 命名该文件`LaunchScreen.xib`单击**创建**按钮：

    [![](unified-storyboards-images/dls04.png "将文件 LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. 通过添加图形元素，使用布局约束来将它们放置在给定的设备、 方向和屏幕大小编辑启动屏幕的设计：

    [![](unified-storyboards-images/dls05.png "编辑启动屏幕的设计")](unified-storyboards-images/dls05.png#lightbox)
7. 保存对`LaunchScreen.xib`。
8. 选择**应用程序目标**和**常规**选项卡：

    [![](unified-storyboards-images/dls06.png "选择应用程序目标和常规选项卡")](unified-storyboards-images/dls06.png#lightbox)
9. 单击**选择 Info.plist**按钮，选择`Info.plist`的 Xamarin 应用程序并单击**选择**按钮：

    [![](unified-storyboards-images/dls07.png "选择 Xamarin 应用的 Info.plist")](unified-storyboards-images/dls07.png#lightbox)
10. 在**应用图标和启动映像**部分中，打开**启动屏幕文件**下拉列表中选择`LaunchScreen.xib`上面创建：

    [![](unified-storyboards-images/dls08.png "选择 LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. 将所做的更改保存到文件并返回到 Visual Studio for mac。
12. 等待 Visual Studio for Mac 以完成同步使用 Xcode 的更改。
13. 在**解决方案资源管理器**，右键单击**资源**文件夹，然后选择**添加** > **添加文件...**:

    [![](unified-storyboards-images/dls09.png "选择添加 / 添加文件...")](unified-storyboards-images/dls09.png#lightbox)
14. 选择`LaunchScreen.xib`上面创建的文件，然后单击**打开**按钮：

    [![](unified-storyboards-images/dls10.png "选择 LaunchScreen.xib 文件")](unified-storyboards-images/dls10.png#lightbox)
15. 生成应用程序。

### <a name="testing-the-dynamic-launch-screen"></a>测试动态启动屏幕

在适用于 Mac 的 Visual Studio，选择 iPhone 4 Retina 模拟器并运行应用程序。 中的正确的格式和方向，将显示动态的启动屏幕：

[![](unified-storyboards-images/dls11.png "显示在垂直方向的动态启动屏幕")](unified-storyboards-images/dls11.png#lightbox)

停止 Mac Visual Studio 中的应用程序并选择 iPad iOS 8 设备。 运行应用程序和启动屏幕将为此设备和方向正确地格式化：

[![](unified-storyboards-images/dls12.png "显示在水平方向的动态启动屏幕")](unified-storyboards-images/dls12.png#lightbox)

返回到 Visual Studio for Mac 并停止运行该应用程序。

### <a name="working-with-ios-7"></a>使用 iOS 7

若要保持与 iOS 7 的向后兼容性，包括常规`Default.png`图像作为正常 iOS 8 应用程序中的资产。 iOS 将返回到以前的行为，并在 iOS 7 设备上运行时将这些文件用作启动屏幕。

若要查看的动态的启动屏幕 Xamarin 中实现，查看[动态启动屏幕](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)示例 iOS 8 应用程序附加到此文档。

## <a name="summary"></a>总结

本文所花费的快速了解一下大小类以及它们如何影响 iPhone 和 iPad 设备中的布局。 它讨论特征、 特征环境和特征集合如何与大小类来创建统一接口工作。 此操作耗时简要看自适应查看控制器和如何与大小类统一接口内的工作方式。 在从 C# 代码在 Xamarin iOS 8 应用程序内完全实现大小类和统一接口，它看起来。

最后，本文介绍了使用 Xamarin iOS 即可适用于 iOS 设备的设计器创建统一情节提要的基础知识，并创建单个动态启动屏幕将显示为在每个 iOS 8 设备上启动屏幕。

## <a name="related-links"></a>相关链接

- [自适应照片 （示例）](https://developer.xamarin.com/samples/monotouch/ios8/AdaptivePhotos/)
- [StoryboardIntro 示例](https://developer.xamarin.com/samples/monotouch/StoryboardIntro/)
- [动态启动屏幕 （示例）](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)
- [在 iOS8-发展 2014 （视频） 中的动态布局](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
