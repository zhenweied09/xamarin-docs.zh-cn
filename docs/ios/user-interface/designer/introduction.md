---
title: "iOS 设计器基础知识"
description: "本指南介绍了 Xamarin 设计器中为 iOS。 它演示了如何使用 iOS 设计器直观地控件进行布局、 如何访问这些控件在代码中，以及如何编辑属性。"
ms.topic: article
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/31/2018
ms.openlocfilehash: 3046d779239076098a8b2fb74fc87e2f211074e9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="ios-designer-basics"></a>iOS 设计器基础知识

_本指南介绍了 Xamarin 设计器中为 iOS。它演示了如何使用 iOS 设计器直观地控件进行布局、 如何访问这些控件在代码中，以及如何编辑属性。_

Xamarin 设计器中为 iOS 是类似于 Xcode 的接口生成器的可视界面设计器和 Android 的设计器。 一些其许多功能包括与 Visual Studio for Mac 和 Visual Studio 2015 和自 2017 年、 拖放编辑、 设置事件处理程序的接口和的功能来呈现自定义控件的无缝集成。

## <a name="requirements"></a>惠?

IOS 设计器是适用于 Mac 的 Visual Studio 中，在 Visual Studio 2015 和 2017年可用，在 Windows 上。 在 Visual Studio 2015 或自 2017 年 1 中，iOS 设计器必须连接到已正确配置的 Mac 生成主机，但 Xcode 需要未运行。

本指南假定你熟悉中介绍的内容[入门指导](~/ios/get-started/index.md)。

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>IOS 设计器的工作原理

本部分介绍 iOS 设计器创建用户界面并连接到代码的帮助。

IOS 设计器允许开发人员能够以可视方式设计应用程序的用户界面。 中所述[简介情节提要](~/ios/user-interface/storyboards/index.md)指南，情节提要介绍的屏幕 （查看控制器） 构成应用程序，这些视图控制器和应用程序的总体导航流程上放置的界面元素 (views). 

视图控制器由两部分组成： iOS 设计器中的可视表示形式和一个关联的 C# 类：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![在 iOS 设计器中的视图控制器](introduction-images/1-storyboardwithviewcontroller-vsmac.png "iOS 设计器中的视图控制器")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png)

[![为视图控制器代码](introduction-images/2-viewcontrollercode-vsmac.png "的视图控制器的代码")](introduction-images/2-viewcontrollercode-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![在 iOS 设计器中的视图控制器](introduction-images/1-storyboardwithviewcontroller-vs.png "iOS 设计器中的视图控制器")](introduction-images/1-storyboardwithviewcontroller-vs-large.png)

[![为视图控制器代码](introduction-images/2-viewcontrollercode-vs.png "的视图控制器的代码")](introduction-images/2-viewcontrollercode-vs-large.png)

-----

在其默认状态下，视图控制器不提供任何这样的功能。它必须使用控件填充。 这些控件都将置于视图控制器视图中，包含所有屏幕的内容的矩形区域。 大多数视图控制器包含常见的控件，如按钮、 标签和文本字段，如以下屏幕截图中，其中显示了包含一个按钮的视图控制器中所示： 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![包含一个按钮的视图控制器](introduction-images/3-viewcontrollerwithbutton-vsmac.png "包含一个按钮的视图控制器")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![包含一个按钮的视图控制器](introduction-images/3-viewcontrollerwithbutton-vs.png "包含一个按钮的视图控制器")](introduction-images/3-viewcontrollerwithbutton-vs-large.png)

-----

可以添加到视图控制器并保留单独某些控件，例如包含静态文本标签。 但是，时常，控件必须进行自定义以编程方式。 例如，前面添加的按钮应执行某些操作时分流，因此必须在代码中添加事件处理程序。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

为了访问和操作代码中的按钮，它必须具有唯一标识符。 通过选择按钮，打开提供的唯一标识符**属性填充**，并设置其**名称**字段到一个值，如"提交"按钮：

[![设置按钮的名称属性填充中](introduction-images/4-settingbuttonname-vsmac.png "属性板中设置按钮的名称")](introduction-images/4-settingbuttonname-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

为了访问和操作代码中的按钮，它必须具有唯一标识符。 通过选择按钮，打开提供的唯一标识符**属性窗口**，并设置其**名称**字段到一个值，如"提交"按钮：

[![在属性窗口中设置按钮的名称](introduction-images/4-settingbuttonname-vs.png "属性窗口中设置按钮的名称")](introduction-images/4-settingbuttonname-vs-large.png)

-----

现在，该按钮具有一个名称，它可以在代码中访问它们。 但这如何工作？

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在**解决方案 Pad**、 导航至**ViewController.cs**和单击泄露指示器显示视图控制器`ViewController`类定义跨度两个文件，其中每个包含[分部类](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)定义：

[![这两个文件组成 ViewController 类： ViewController.cs 和 ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "这两个文件组成 ViewController 类： ViewController.cs 和 ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在**解决方案资源管理器**、 导航至**ViewController.cs**和单击泄露指示器显示视图控制器`ViewController`类定义跨两个文件，每个其中包含[分部类](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)定义：

[![这两个文件组成 ViewController 类： ViewController.cs 和 ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "这两个文件组成 ViewController 类： ViewController.cs 和 ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png)

-----

- **ViewController.cs**应使用与相关的自定义代码来填充`ViewController`类。 在此文件中，`ViewController`类可以响应各种 iOS 视图控制器生命周期方法，自定义 UI，并响应用户输入如点击按钮。

- **ViewController.designer.cs**是由 iOS 将直观地构造接口映射到代码设计器创建的生成的文件。 对此文件的更改将被覆盖，因为它不应修改。 此文件中的属性声明使代码在`ViewController`通过类以访问，**名称**，iOS 设计器中向上控制组。 打开**ViewController.designer.cs**显示下面的代码：

```csharp
namespace Designer
{
    [Register ("ViewController")]
    partial class ViewController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        UIKit.UIButton SubmitButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (SubmitButton != null) {
                SubmitButton.Dispose ();
                SubmitButton = null;
            }
        }
    }
}
```

`SubmitButton`属性声明连接整个`ViewController`类-不只是**ViewController.designer.cs**文件 – 到情节提要中定义的按钮。 由于**ViewController.cs**定义的一部分`ViewController`类，它有权访问`SubmitButton`。

下面的屏幕截图演示 IntelliSense 现在识别`SubmitButton`中引用**ViewController.cs**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![IntelliSense 识别的提交按钮引用](introduction-images/6-submitbuttonintellisense-vsmac.png "识别的提交按钮引用的 IntelliSense")](introduction-images/6-submitbuttonintellisense-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![IntelliSense 识别的提交按钮引用](introduction-images/6-submitbuttonintellisense-vs.png "识别的提交按钮引用的 IntelliSense")](introduction-images/6-submitbuttonintellisense-vs-large.png)

-----

本部分已经演示了如何在 iOS 设计器中创建一个按钮并访问代码中的该按钮。

此文档的其余部分进一步概述 iOS 设计器。

## <a name="ios-designer-basics"></a>iOS 设计器基础知识

本部分介绍 iOS 设计器的组成部分，并提供了其功能的教程。

### <a name="launching-the-ios-designer"></a>启动 iOS 设计器

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

使用 Visual Studio 创建的 Mac 的 Xamarin.iOS 项目包括情节提要。 若要查看情节提要的内容，请双击中的.storyboard 文件**解决方案 Pad**:

[![在 iOS 设计器中打开情节提要](introduction-images/7-storyboardopen-vsmac.png "iOS 设计器中打开情节提要")](introduction-images/7-storyboardopen-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

使用 Visual Studio 2015 或自 2017 年 1 创建的大多数 Xamarin.iOS 项目包括情节提要。 若要查看情节提要的内容，请双击中的.storyboard 文件**解决方案资源管理器**:

[![在 iOS 设计器中打开情节提要](introduction-images/7-storyboardopen-vs.png "iOS 设计器中打开情节提要")](introduction-images/7-storyboardopen-vs-large.png)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>iOS 设计器功能

IOS 设计器具有六个主要部分：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![设计器的 ios 部分](introduction-images/8-sixpartsofiosdesigner-vsmac.png "iOS 设计器的部分")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png)

1. **设计图面**– iOS 设计器的主要工作区。 它显示在文档区域，使用户界面的直观地构建。
2. **约束工具栏**– 允许编辑模式和约束编辑模式，两种不同方式来在用户界面中定位元素的帧之间切换。
3. **工具箱**– 列出控制器、 对象、 控件、 数据视图、 笔势识别器、 windows，并条，可以拖动到设计图面上并添加到用户界面。
4. **属性填充**– 显示为选定控件，包括标识、 可视样式、 辅助功能、 布局和行为的属性。
5. **文档大纲**– 显示组成正在编辑的接口的布局的控件的树。 单击树中的项在 iOS 设计器中选择它，并显示在其属性**属性填充**。 这是便于在深度嵌套的用户界面中选择一个特定的控件。
6. **底部工具栏**– 包含用于更改 iOS 设计器如何显示.storyboard 或.xib 文件，包括设备、 方向和缩放选项。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![设计器的 ios 部分](introduction-images/8-sixpartsofiosdesigner-vs.png "iOS 设计器的部分")](introduction-images/8-sixpartsofiosdesigner-vs-large.png)

1. **设计图面**– iOS 设计器的主要工作区。 它显示在文档区域，使用户界面的直观地构建。
2. **约束工具栏**– 允许编辑模式和约束编辑模式，两种不同方式来在用户界面中定位元素的帧之间切换。
3. **工具箱**– 列出控制器、 对象、 控件、 数据视图、 笔势识别器、 windows，并条，可以拖动到设计图面上并添加到用户界面。
4. **属性窗口**– 显示为选定控件，包括标识、 可视样式、 辅助功能、 布局和行为的属性。
5. **文档大纲**– 显示组成正在编辑的接口的布局的控件的树。 单击树中的项在 iOS 设计器中选择它，并显示在其属性**属性窗口**。 这是便于在深度嵌套的用户界面中选择一个特定的控件。
6. **底部工具栏**– 包含用于更改 iOS 设计器如何显示.storyboard 或.xib 文件，包括设备、 方向和缩放选项。

-----

### <a name="design-workflow"></a>设计工作流

#### <a name="adding-a-control-to-the-interface"></a>将控件添加到接口

若要将控件添加到接口，将其从**工具箱**并将其放在设计图面上。 当添加或定位控件的控件时，垂直和水平准则突出显示如垂直 center、 水平居中和边距的常用布局位置：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
 
![设计图面上准则突出显示常用布局位置](introduction-images/9-layoutguides-vsmac.png "设计图面上准则突出显示常用布局位置")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![设计图面上准则突出显示常用布局位置](introduction-images/9-layoutguides-vs.png "设计图面上准则突出显示常用布局位置")

-----

上面的示例中的蓝色虚线提供水平居中 visual 对齐指导，帮助按钮放置。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

#### <a name="context-menu-commands"></a>上下文菜单命令

上下文菜单是设计图面上，在可用**文档大纲**。 此菜单提供了命令，为所选的控件和其父级，在使用中的嵌套层次结构视图时非常有用：

[![在设计图面上的上下文菜单](introduction-images/10-contextmenudesignsurface-vsmac.png "设计图面上的上下文菜单")](introduction-images/10-contextmenudesignsurface-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-----

### <a name="constraints-toolbar"></a>约束工具栏

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
 
[![约束工具栏](introduction-images/11-constraintstoolbar-vsmac.png "约束工具栏")](introduction-images/11-constraintstoolbar-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![约束工具栏](introduction-images/11-constraintstoolbar-vs.png "约束工具栏")](introduction-images/11-constraintstoolbar-vs-large.png)

-----

约束工具栏已更新，并且现在包含两个控件： 编辑模式的帧 / 约束编辑模式切换和更新约束 / 更新帧按钮。

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>编辑模式的帧 / 约束编辑模式切换

在以前版本的 iOS 设计器中，单击设计图面上的已选择视图切换编辑模式和约束编辑模式的帧之间。 现在，这些编辑模式之间进行切换约束工具栏中的切换控件。

- 帧编辑模式：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![编辑模式按钮框架](introduction-images/12a-frameeditingmode-vsmac.png "帧编辑模式按钮")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![编辑模式按钮框架](introduction-images/12a-frameeditingmode-vs.png "帧编辑模式按钮")

-----

- 约束编辑模式：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![编辑模式按钮的约束](introduction-images/12b-constrainteditingmode-vsmac.png "约束编辑模式按钮")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![编辑模式按钮的约束](introduction-images/12b-constrainteditingmode-vs.png "约束编辑模式按钮")

-----

#### <a name="update-constraints--update-frames-button"></a>更新约束/更新帧按钮

更新约束 / 更新帧编辑模式的框架右侧的按钮位于 / 约束编辑模式切换。

- 在编辑模式的框架中，单击此按钮调整任何所选的元素以匹配其约束的帧。
- 在约束编辑模式下，单击此按钮调整任何所选的元素以匹配其框架的约束。

### <a name="bottom-toolbar"></a>底部工具栏

底部工具栏提供了一个方法来选择设备、 方向和缩放用于 iOS 设计器中查看的情节提要或.xib 文件：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![底部工具栏上，用于选择的设备和方向设计图面的](introduction-images/13-bottomtoolbar-vsmac.png "底部工具栏上，用于选择设备和设计图面的的方向")](introduction-images/13-bottomtoolbar-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![底部工具栏上，用于选择的设备和方向设计图面的](introduction-images/13-bottomtoolbar-vs.png "底部工具栏上，用于选择设备和设计图面的的方向")](introduction-images/13-bottomtoolbar-vs-large.png)

-----

#### <a name="device-and-orientation"></a>设备和方向

展开后，底部工具栏上将显示所有设备、 方向，和/或改编适用于当前文档。 单击它们更改设计图面上显示的视图。 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![底部工具栏上，展开以显示设备和方向](introduction-images/14-bottomtoolbarexpanded-vsmac.png "底部工具栏上，展开以显示设备和方向")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![底部工具栏上，展开以显示设备和方向](introduction-images/14-bottomtoolbarexpanded-vs.png "底部工具栏上，展开以显示设备和方向")](introduction-images/14-bottomtoolbarexpanded-vs-large.png)

-----

请注意选择的设备和方向更改仅如何 iOS 设计器预览的设计。 无论当前所选内容，新添加的约束应用跨所有设备和方向，除非**编辑特征**已使用按钮来指定，否则。

当[大小类](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes)是[启用](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes)、**编辑特征**按钮将显示扩展的底部工具栏中。  单击**编辑特征**按钮将显示用于创建基于表示所选的设备和方向的大小类接口变体的选项。 请开考虑以下示例：

- 如果**iPhone SE** / **纵向**，是选择，popover 将提供选项来创建的简洁的宽度，正则高度大小类接口变体。 
- 如果**iPad Pro 9.7"** / **横向** / **全屏**是选择，popover 将提供选项来创建为接口变体正则宽度、 正则高度大小类中。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![正在改变接口大小类使用底部工具栏](introduction-images/15-edittraitsbutton-vsmac.png "底部工具栏上正由改变接口大小类")](introduction-images/15-edittraitsbutton-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![正在改变接口大小类使用底部工具栏](introduction-images/15-edittraitsbutton-vs.png "底部工具栏上正由改变接口大小类")](introduction-images/15-edittraitsbutton-vs-large.png)

-----

#### <a name="zoom-controls"></a>缩放控件

设计图面支持通过多个控件缩放：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
 
![在底部工具栏上的缩放控件](introduction-images/16-zoomcontrols-vsmac.png "底部工具栏中的缩放控件")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![在底部工具栏上的缩放控件](introduction-images/16-zoomcontrols-vs.png "底部工具栏中的缩放控件")

-----

控件包括：

1. 缩放以适合
2. 缩小
3. 放大
4. 实际大小 （1:1 像素大小）

这些控件调整缩放设计图面上。 它们不会影响在运行时应用程序的用户界面。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

### <a name="properties-pad"></a>属性填充

使用**属性填充**编辑标识、 视觉样式、 可访问性和控件行为。 下面的屏幕截图演示**属性填充**按钮的选项：

[![按钮属性填充](introduction-images/17-buttonpropertiespad-vsmac.png "按钮的属性填充")](introduction-images/17-buttonpropertiespad-vsmac-large.png)
#### <a name="properties-pad-sections"></a>属性填充部分

**属性填充**包含三个部分：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="properties-window"></a>属性窗口

使用**属性窗口**编辑标识、 视觉样式、 可访问性和控件行为。 下面的屏幕截图演示**属性窗口**按钮的选项：

[![属性窗口按钮](introduction-images/17-buttonpropertieswindow-vs.png "按钮的属性窗口")](introduction-images/17-buttonpropertieswindow-vs-large.png)

#### <a name="properties-window-sections"></a>属性窗口部分

**属性窗口**包含三个部分：

-----

1.  **小组件**– 的主要属性的控件，例如名称、 类、 样式属性，等等。用于管理控件的内容的属性通常放在此处。
2.  **布局**– 此处列出的位置和大小的控件，包括的约束和帧，跟踪的属性。
3.  **事件**– 此处指定事件和事件处理程序。 用于处理如触摸、 点击、 拖动和等的事件。此外可以直接在代码中处理事件。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

#### <a name="editing-properties-in-the-properties-pad"></a>编辑属性板中的属性

可视化编辑设计图面上，除了 iOS 设计器还支持在编辑属性**属性填充**。 可用的属性更改基于所选的控件，如下面的屏幕截图所示：

[![按钮属性](introduction-images/18a-buttonpropertiespad-vsmac.png "按钮属性")](introduction-images/18a-buttonpropertiespad-vsmac-large.png)

[![查看控制器属性](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "查看控制器属性")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

#### <a name="editing-properties-in-the-properties-window"></a>在属性窗口中编辑属性

可视化编辑设计图面上，除了 iOS 设计器还支持在编辑属性**属性窗口**。 可用的属性更改基于所选的控件，如下面的屏幕截图所示：

[![按钮属性](introduction-images/18a-buttonpropertieswindow-vs.png "按钮属性")](introduction-images/18a-buttonpropertieswindow-vs-large.png)

[![查看控制器属性](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "查看控制器属性")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png)

-----

> [!IMPORTANT]
> 在标识节中的属性填充现在显示**模块**字段。 很有必要填充在本部分中，仅与 Swift 类互操作时。 使用它来输入 Swift 类，是名称空间的模块名称。

#### <a name="default-values"></a>默认值

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

中的许多属性**属性填充**上出现的任何值或默认值。 但是，应用程序的代码仍可以修改这些值。 **属性填充**不显示在代码中设置的值。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

中的许多属性**属性窗口**上出现的任何值或默认值。 但是，应用程序的代码仍可以修改这些值。 **属性窗口**不显示在代码中设置的值。

-----

#### <a name="event-handlers"></a>事件处理程序

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要指定自定义事件处理程序的各种事件，请使用**事件**选项卡**属性填充**。 例如，在下面的屏幕截图`HandleClick`方法可处理按钮的**Touch 内**事件：

[![属性填充，与为按钮设置一个事件处理程序](introduction-images/19-buttonpropertiespadevents-vsmac.png "属性填充，与为按钮设置一个事件处理程序")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要指定自定义事件处理程序的各种事件，请使用**事件**选项卡**属性窗口**。 例如，在下面的屏幕截图`HandleClick`方法可处理按钮的**Touch 内**事件：

[![属性窗口与按钮设置一个事件处理程序](introduction-images/19-buttonpropertieswindowevents-vs.png "属性窗口中，并为一个按钮设置事件处理程序")](introduction-images/19-buttonpropertieswindowevents-vs-large.png)

-----

后指定事件处理程序，具有相同名称的方法必须将添加到相应的视图控制器类。 否则为`unrecognized selector`点击按钮时，则会发生异常：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![无法识别的选择器异常](introduction-images/20-unrecognizedselector-vsmac.png "无法识别的选择器异常")](introduction-images/20-unrecognizedselector-vsmac-large.png)

请注意，事件处理程序之后已在中指定**属性填充**，iOS 设计器将立即打开相应的代码文件，并提供要插入方法声明。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![无法识别的选择器异常](introduction-images/20-unrecognizedselector-vs.png "无法识别的选择器异常")](introduction-images/20-unrecognizedselector-vs-large.png)

-----

有关使用自定义事件处理程序的示例，请参阅[Hello，iOS 入门指南 》](~/ios/get-started/hello-ios/index.md)。

### <a name="outline-view"></a>大纲视图

IOS 设计器还可以以大纲显示控件的接口的层次的结构。 大纲可通过选择**文档大纲**选项卡上，如下所示：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![文档大纲](introduction-images/21-buttonoutlineview-vsmac.png "文档大纲")](introduction-images/21-buttonoutlineview-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![文档大纲](introduction-images/21-buttonoutlineview-vs.png "文档大纲")](introduction-images/21-buttonoutlineview-vs-large.png)

-----

大纲视图中的所选的控件保持与设计图面上所选控件同步。  此功能可用于从深度嵌套的接口层次结构中选择项。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="revert-to-xcode"></a>恢复到 Xcode

它是可以互换使用 iOS 设计器和 Xcode 接口生成器。 若要在 Xcode 接口生成器中打开情节提要或.xib 文件，右键单击该文件并选择**打开 > Xcode 接口生成器**，如下面的屏幕截图所示：

[![在 Xcode 接口生成器中打开情节提要](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "在 Xcode 接口生成器中打开情节提要")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png)

在 Xcode 接口生成器中进行编辑后, 保存该文件，并返回到 Visual Studio for mac。 所做的更改将同步到 Xamarin.iOS 项目。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-----

## <a name="xib-support"></a>.xib 支持

IOS 设计器支持创建、 编辑和管理.xib 文件。 这些是该表示单个自定义视图可将其添加到应用程序的视图层次结构的 XML 文件。 .Xib 文件通常表示为单个视图或应用程序，在屏幕的接口，而情节提要表示许多屏幕和它们之间的转换。

有许多有关哪些解决方案 –.xib 文件、 情节提要或代码 – 最适合于创建和维护用户界面的意见。 实际上，没有完美的解决方案，并且它始终值得考虑作业手头的最佳工具。 话虽如此，但.xib 文件有通常最强大时用于表示在应用程序，如自定义的表视图单元格的多个位置中需要的自定义视图。 

可以在以下食谱中找到有关使用.xib 文件的更多文档：

- [使用视图.xib 模板](https://developer.xamarin.com/recipes/ios/general/templates/using_the_ios_view_xib_template/)
- [创建使用.xib 自定义 TableViewCell](https://developer.xamarin.com/recipes/ios/content_controls/tables/custom-tableviewcell/)
- [创建使用.xib 启动屏幕](https://developer.xamarin.com/recipes/ios/general/templates/launchscreen-xib/)

有关使用情节提要的详细信息，请参阅[简介情节提要](~/ios/user-interface/storyboards/index.md)。

这和其他 iOS 设计器相关指南是指用于生成用户界面，由于大多数 Xamarin.iOS 新项目模板提供情节提要，默认情况下，将情节提要用作标准的方法。

## <a name="summary"></a>摘要

本指南提供对 iOS 设计器中，用于描述它的功能并将以大纲方式显示的工具，它提供用于设计精美用户界面的介绍。

## <a name="related-links"></a>相关链接

- [情节提要简介](~/ios/user-interface/storyboards/index.md)
- [iOS 可设计控件演练](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello，iOS](~/ios/get-started/hello-ios/index.md)
- [Hello，iOS 多屏显示](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Android 设计器概述](~/android/user-interface/android-designer/index.md)
- [分部类和方法](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [深入分析 Xamarin 设计器中为 iOS-发展 2014 （视频）](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [使用 iOS 设计器来创建一个启动屏幕 （视频）](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
