---
title: 使用拆分视图控制器
description: 本文介绍如何设计和在 Xamarin.tvOS 应用内部使用拆分视图控制器。
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 25151eb2929e2bc61dba27a9937ffdf4ee224626
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-split-view-controllers"></a>使用拆分视图控制器

_本文介绍如何设计和在 Xamarin.tvOS 应用内部使用拆分视图控制器。_


拆分视图控制器显示和管理主机以及详细信息视图控制器-并行安装，在屏幕上一次。 拆分视图控制器的使用在母版视图 （左侧的较小的部分） 中呈现的永久性驱动器，可获得焦点的内容和相关详细信息视图 （右侧更大的部分） 中的详细信息。

[![](split-views-images/intro01.png "示例拆分视图")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>有关拆分视图控制器

如上面所述，拆分视图控制器管理 Master 和将出现在左侧，右侧较大的详细信息为较小视图主端通过端的详细信息视图控制器。 

显示或隐藏母版视图控制器可以已此外，所需的方式： 

[![](split-views-images/intro02.png "隐藏母版视图控制器")](split-views-images/intro02.png#lightbox)

拆分视图控制器通常用于使用母版视图中的类别和筛选的结果详细信息视图中提供了可筛选内容的列表。 这通常显示为在左侧，表视图和[集合视图](~/ios/tvos/user-interface/collection-views.md)右侧。

在设计时用户界面要求拆分视图控制器，Apple 提供的建议使用 Master 和不会更改 （只能是内容更改不是结构） 的详细信息视图控制器。 如果你确实需要交换扩展视图控制器，则最好导航控制器用作需要进行更改 （Master 或详细信息） 的视图控制器的基础。

Apple 具有以下建议用于使用拆分视图控制器：

- **使用正确的拆分百分比**-默认情况下拆分视图控制器使用的三分之一母版视图控制器的屏幕和 2 / 3 的详细信息视图控制器。 或者，你可以使用 50/50 拆分。 选择正确的百分比，以使你内容显示在屏幕上平衡。
- **保持 Main 选择**-时内容上详细信息视图可以更改是响应母版视图中的用户的选择，应修复母版视图内容。 此外，应在母版视图清楚地显示当前选定的项。
- **使用单个统一标题**-通常情况下，你将需要在详细信息视图，而不是详细信息和母版视图中的标题中使用单个的居中的标题。

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>拆分视图控制器和情节提要

使用 Xamarin.tvOS 应用中的拆分视图控制器的最简单方法是将它们添加到使用 iOS 设计器的应用程序的 UI。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**解决方案 Pad**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 拖动**拆分视图控制器**从**工具箱**并将其放在视图上： 

    [![](split-views-images/activity01.png "拆分视图控制器")](split-views-images/activity01.png#lightbox)
1. 默认情况下，iOS 设计器将在母版视图安装导航控制器和视图控制器。 如果这不适合您的应用程序的要求，只需删除它们。
1. 如果你删除默认母版视图中，拖动到设计图面上的新视图控制器： 

    [![](split-views-images/activity02.png "视图控制器")](split-views-images/activity02.png#lightbox)
1. 单击并将其从拆分视图控制器拖到新的主视图控制器。 
1. 选择**Master**从**弹出菜单**: 

    [![](split-views-images/activity03.png "从弹出菜单中选择主机")](split-views-images/activity03.png#lightbox)
1. 设计 Master 和详细信息视图中的内容： 

    [![](split-views-images/activity04.png "布局示例")](split-views-images/activity04.png#lightbox)
1. 分配**名称**中**小组件选项卡**的**属性填充**用于 UI 控件在 C# 代码。
1. 保存所做的更改并返回到 Visual Studio for mac。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在**解决方案资源管理器**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 拖动**拆分视图控制器**从**工具箱**并将其放在视图上： 

    [![](split-views-images/activity01-vs.png "拆分视图控制器")](split-views-images/activity01-vs.png#lightbox)
1. 默认情况下，iOS 设计器将在母版视图添加导航控制器和视图控制器。 如果这不适合您的应用程序的要求，只需删除它们。
1. 如果你删除默认母版视图中，拖动到设计图面上的新视图控制器： 

    [![](split-views-images/activity02-vs.png "视图控制器")](split-views-images/activity02-vs.png#lightbox)
1. 单击并将其从拆分视图控制器拖到新的主视图控制器。 
1. 选择**Master**从**弹出菜单**: 

    [![](split-views-images/activity03-vs.png "从弹出菜单中选择主机")](split-views-images/activity03-vs.png#lightbox)
1. 设计 Master 和详细信息视图中的内容： 

    [![](split-views-images/activity04.png "内容布局")](split-views-images/activity04.png#lightbox)
1. 分配**名称**中**小组件选项卡**的**属性资源管理器**用于 UI 控件在 C# 代码。
1. 保存更改。
    
-----

有关使用情节提要的详细信息，请参阅我们[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>使用拆分视图控制器

如上面所述，拆分视图控制器通常会使用在其中向用户显示经过筛选的内容的情况下。 主类别将显示在母版视图中，左侧和右侧的详细信息视图中筛选的结果基于用户的选择。

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>访问 Master 和详细信息

如果你需要以编程方式访问 Master 和详细信息视图控制器，请使用`ViewControllers `拆分视图控制器的属性。 例如：

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

它显示为一个数组，其中第一个元素母版视图控制器 (0) 和第二个元素 (1) 是的详细信息。

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>从主服务器的访问详细信息

因为你通常基于母版中的用户的选择的详细信息视图中显示的详细的信息，你将需要用于从主机访问的详细信息的方法。

若要这样做最简单方法是公开你母版视图控制器类、 一个属性，例如：

```csharp
public DetailViewController DetailController { get; set;}
```

在拆分视图控制器，重写`ViewDidLoad`方法和关系的两个视图在一起。 例如：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Gain access to master and detail view controllers
    var masterController = ViewControllers [0] as MasterViewController;
    var detailController = ViewControllers [1] as DetailViewController;

    // Wire-up views
    masterController.SplitViewController = this;
    masterController.DetailController = detailController;
    detailController.SplitViewController = this;
}
```

你可以使用主呈现所需的新数据的详细信息视图控制器上，可以公开属性和方法。

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>显示和隐藏母版

（可选） 你可以显示和隐藏母版视图控制器使用`PreferredDisplayMode`拆分视图控制器的属性。 例如：

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

`UISplitViewControllerDisplayMode`枚举定义如何母版视图控制器将显示为以下项之一：

- **自动**-tvOS 将控制表示法的 Master 和详细信息视图。
- **PrimaryHidden** -这会隐藏母版视图控制器。
- **AllVisible** -Master 和详细信息视图控制器-并排显示。 这是正常的默认的表示。
- **PrimaryOverlay** -详细信息视图控制器下，扩展，并由主机涵盖。

若要获取当前的演示文稿状态，请使用`DisplayMode`拆分视图控制器的属性。

<a name="Summary" />

## <a name="summary"></a>总结

本文已覆盖设计和在 Xamarin.tvOS 应用内部使用拆分视图控制器。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
