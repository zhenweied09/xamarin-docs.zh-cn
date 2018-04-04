---
title: 使用选项卡栏控制器
description: 本文介绍如何设计和在 Xamarin.tvOS 应用内部使用的选项卡栏控制器。
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 1536e37830f3b2a1e2a83c7bf5039909062d092b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-tab-bar-controller"></a>使用选项卡栏控制器

_本文介绍如何设计和在 Xamarin.tvOS 应用内部使用的选项卡栏控制器。_

对于许多类型的 tvOS 应用中，主导航显示为选项卡栏在屏幕顶部之间运行。 用户刷左侧和右侧的可能的类别和下方所做的更改的内容区域，以反映用户的选择列表中。

[![](tab-bars-images/tab01.png "示例选项卡栏")](tab-bars-images/tab01.png#lightbox)

选项卡栏是半透明默认情况下，并始终显示在屏幕的顶部。 具有焦点，选项卡栏将介绍在屏幕顶部 140 像素，但焦点切换到下面的内容区域时将快速滑动消失。

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>选项卡条中 tvOS

`UITabViewController`适用于类似的方式，而且有用途类似上 tvOS 方式与其在 iOS 中，具有以下主要差异：

- 不同于在该区域显示在屏幕底部的 iOS 上的选项卡栏中，选项卡条中 tvOS 占用屏幕顶部 140 像素，并是半透明默认情况下。
- 当焦点离开下面的内容区域在选项卡栏时，选项卡栏会快速滑关闭屏幕顶部，并隐藏。 用户可以一次点击的菜单按钮或上向上轻扫[Siri 远程](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)要再次显示选项卡栏。
- 向下轻扫上使用 Siri 远程，会将焦点移动到第一个选项卡栏下方的内容区域[可获得焦点的项](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)中正在显示的内容。 同样，这将隐藏选项卡栏，一旦焦点切换。
- 单击以选择的选项卡栏中显示的类别会切换到该类别的内容和焦点将切换为该视图中的第一个可获得焦点项。
- 应修复的选项卡栏中显示的类别的数目和应始终可访问所有类别，应永远不会禁用给定的类别。
- 选项卡条不支持上 tvOS 的自定义项。 此外，它们不会显示**详细**类别 （如 iOS) 是否存在于多个类别中可以容纳该值选项卡栏。

Apple 具有用于处理选项卡条以下建议：

- **使用逻辑上组织内容的选项卡条**-使用选项卡条来按照逻辑组织 tvOS 应用适用于的内容。 例如，特色、 顶部图表、 已采购和搜索。
- **将徽章添加到新内容的通知用户**-您可以选择显示徽章 (红色 oval 以白色数字或感叹号) 通知的类别中的新内容的用户。
- **徽章尽可能少地使用**-不使徽章与选项卡栏混乱不堪，并仅显示它们它们向用户提供关键信息。
- **限制类别数**-若要将降低复杂性和使易于管理应用程序，不重载类别你选项卡栏，并确保所有类别是否可见且不拥挤。 简单、 短标题工作效率最佳。
- **不禁用类别**-所有选项卡 （类别） 应始终为可见且已启用在所有时间。 如果给定的选项卡上没有任何内容，原因为用户提供说明。 例如，购买选项卡将用户所作的任何购买的情况下为空。

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>选项卡工具栏项

由选项卡栏项表示的选项卡栏中的每个类别 （选项卡） (`UITabBarItem`)。 Apple 具有以下建议用于使用选项卡栏项：

- **使用基于文本的选项卡**-时的选项卡栏项是可以表示为一个图标，Apple 提供的建议使用文本，只是因为简洁的标题是更轻松地解释比图标。
- **使用短、 有意义的名词或谓词**-A 选项卡栏项应清楚地中继它包含并且适合时 （例如照片、 电影或音乐） 的简单名词或谓词 （如搜索或 Play） 的内容。

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>选项卡条和情节提要

使用 Xamarin.tvOS 应用中的选项卡条的最简单方法是将它们添加到使用 iOS 设计器的应用程序的 UI。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
1. 启动新的 Xamarin.tvOS 应用程序并选择**tvOS** > **应用** > **选项卡式应用**: 

    [![](tab-bars-images/tab02.png "选择选项卡式的应用")](tab-bars-images/tab02.png#lightbox)
1. 按照提示操作以创建新的 Xamarin.tvOS 解决方案的所有操作。
1. 在**解决方案 Pad**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 若要更改**图标**或**标题**对于给定的类别，选择**选项卡栏项**为**视图控制器**中**文档大纲**:

    [![](tab-bars-images/tab03a.png "选项卡栏项文档大纲中的视图控制器")](tab-bars-images/tab03a.png#lightbox)
1. 然后在中设置所需的属性**小组件选项卡**的**属性资源管理器**: 

    [![](tab-bars-images/tab03.png "小组件选项卡")](tab-bars-images/tab03.png#lightbox)
1. 若要添加新类别 （选项卡），删除**视图控制器**拖到设计图面： 

    [![](tab-bars-images/tab04.png "视图控制器")](tab-bars-images/tab04.png#lightbox)
1. 控件单击并拖动从**选项卡视图控制器**对新**视图控制器**。
1. 从弹出窗口中，选择**查看控制器**为选项卡 （类别） 中添加新视图： 

    [![](tab-bars-images/tab05.png "选择选项卡")](tab-bars-images/tab05.png#lightbox)
1. 通过 iOS 设计器中添加 UI 元素设计用于正常工作，每个 Caterogies 内容区域的 ui 布局。
1. 公开用于 UI 控件在 C# 代码中任何所需的事件。
1. 命名你想要在 C# 代码中公开的任何 UI 控件。
1. 保存更改。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. 启动新的 Xamarin.tvOS 应用程序并选择**tvOS** > **应用** > **选项卡式应用**: 

    [![](tab-bars-images/tab02vs.png "选择选项卡式的应用")](tab-bars-images/tab02vs.png#lightbox)
1. 按照提示操作以创建新的 Xamarin.tvOS 解决方案的所有操作。
1. 在**解决方案资源管理器**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 若要更改**图标**或**标题**对于给定的类别，选择**选项卡栏项**为**视图控制器**中**文档大纲**:

    [![](tab-bars-images/tab03avs.png "文档大纲中的视图控制器")](tab-bars-images/tab03avs.png#lightbox)
1. 然后在中设置所需的属性**小组件选项卡**的**属性资源管理器**: 

    [![](tab-bars-images/tab03vs.png "小组件选项卡")](tab-bars-images/tab03vs.png#lightbox)
1. 若要添加新类别 （选项卡），拖动**视图控制器**从**工具箱**并将其放到设计图面上： 

    [![](tab-bars-images/tab04vs.png "视图控制器")](tab-bars-images/tab04vs.png#lightbox)
1. 控件单击并拖动从**选项卡视图控制器**对新**视图控制器**。
1. 从弹出窗口中，选择**查看控制器**为选项卡 （类别） 中添加新视图： 

    [![](tab-bars-images/tab05vs.png "选择选项卡")](tab-bars-images/tab05vs.png#lightbox)
1. 通过在 iOS 设计器中添加 UI 元素设计用于正常工作，每个 Caterogies 内容区域的 ui 布局。
1. 公开用于 UI 控件在 C# 代码中任何所需的事件。
1. 命名你想要在 C# 代码中公开的任何 UI 控件。
1. 保存更改。
    
-----

> [!IMPORTANT]
> 尽管可以将事件分配如`TouchUpInside`到 UI 元素 (如`UIButton`) 在 iOS 设计器中，它将永远不会调用因为 Apple TV 没有触摸屏幕或支持触控事件。 应始终使用`Primary Action `事件时创建用户界面元素的对 tvOS 的事件处理程序。

有关使用情节提要的详细信息，请参阅我们[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>使用选项卡条

使用`Items`属性`UITabBar`若要访问的集合`UITabBarItems`它包含零 (0) 索引数组的形式。 `SelectedItem`属性将返回当前所选的选项卡 （类别） 作为`UITabBarItem`。


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>使用选项卡栏项

若要在给定的选项卡上 (包含白色文本的红色 oval) 显示一个标记，请使用下面的代码：

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

这会产生运行时的以下结果：

[![](tab-bars-images/tab06.png "选项卡栏项带有徽标")](tab-bars-images/tab06.png#lightbox)

使用`Title`属性`UITabBarItem`更改标题和`Image`属性可更改的图标。

<a name="Summary" />

## <a name="summary"></a>总结

本文已覆盖设计和在 Xamarin.tvOS 应用内部使用的选项卡栏控制器。




## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
