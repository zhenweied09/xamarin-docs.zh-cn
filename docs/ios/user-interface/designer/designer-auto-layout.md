---
title: "与 iOS 的 Xamarin 设计器的自动数据布局"
description: "本指南介绍 iOS 自动布局和新约束工作流 iOS Xamarin 设计器中可用。"
ms.topic: article
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: ff19048504ee76db614306adebb71b7237139091
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>与 iOS 的 Xamarin 设计器的自动数据布局

_本指南介绍 iOS 自动布局和新约束工作流 iOS Xamarin 设计器中可用。_

响应灵敏的设计方法 （也称为"自适应布局"） 的自动数据布局。 与过渡布局系统，其中每个元素的位置是硬编码到屏幕上的点，不同的是自动布局即将*关系*-相对于设计图面上的其他元素的元素的位置。 自动布局的核心是约束或在屏幕上的其他元素的上下文中定义的元素的位置或一组元素的规则的概念。 由于元素不会关联到特定位置在屏幕上，约束将帮助创建看起来上不同屏幕大小和设备的方向很好的自适应布局。

在本指南中，我们引入约束以及如何在 Xamarin iOS 设计器中使用它们。 本指南不涉及以编程方式使用约束。 有关以编程方式使用自动布局的信息，请参阅[Apple 文档](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html)。

## <a name="requirements"></a>要求

Xamarin 设计器中为 iOS 是适用于 Visual Studio 2015 和自 2017 年中的 Mac 在 Windows 上的 Visual Studio 中提供。

本指南假设的设计器的组件的知识[iOS 设计器简介](~/ios/user-interface/designer/introduction.md)指南。

## <a name="introduction-to-constraints"></a>约束简介

约束是关系的在屏幕上的两个元素之间的数学表示法。 表示 UI 元素的位置相同的数学关系解决了硬编码的 UI 元素的位置与关联的几个问题。 例如，如果要从屏幕底部的按钮 20px 置于纵向模式，该按钮的位置就不会进入横向模式中的屏幕。 若要避免此问题，我们无法设置将从视图的底部按钮 20px 的下边缘的约束。 按钮边缘的位置然后可计算为*button.bottom = view.bottom-20px*，这将在纵向与横向模式下置于按钮 20px 从视图的底部。 计算的数学关系所基于的放置的能力是什么使约束因此有用 UI 设计中。

当我们设置约束时，我们创建`NSLayoutConstraint`要约束的对象和属性，将作为自变量的对象或*属性*，约束将发挥作用。 在 iOS 设计器中属性包括边缘如*左*，*右*，*顶部*，和*底部*的元素。 它们还包括大小属性如*高度*和*宽度*，和中心点位置， *centerX*和*centerY*。 例如，当我们将添加两个按钮的左侧边界的位置的约束，设计器生成事实上下面的代码：

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

下一部分介绍如何使用约束使用 iOS 设计器中，包括启用和禁用自动布局和使用约束工具栏。

## <a name="enable-auto-layout"></a>启用自动布局

默认 iOS 设计器配置已启用的约束模式。 但是，你需要启用或禁用该手动，你可以在两个步骤的操作：

1.  单击设计图面上的空白区域。 这将取消选择任何元素，然后将情节提要文档的属性。
1.  选中或取消选中**使用自动布局**属性面板中的复选框：

    ![](designer-auto-layout-images/image01.png "属性面板中的使用自动布局复选框")


默认情况下，任何约束不是创建或面上可见。 相反，它们会自动从推断出在编译时的帧信息。 若要添加约束，我们需要选择设计图面上的某个元素并将约束添加到它。 我们可以执行操作，使用**约束工具栏**。

## <a name="constraints-toolbar"></a>约束工具栏

 [![](designer-auto-layout-images/toolbarnew.png "上下文菜单命令")](designer-auto-layout-images/toolbarnew.png#lightbox)

约束工具栏已更新，现在由两个主要部分组成：

- **约束模式按钮切换**： 先前，通过再次单击设计图面上所选视图输入约束模式。 你现在应在约束栏中使用此切换按钮：

  ![约束模式切换](designer-auto-layout-images/constraints.png)

- **"更新约束"按钮：**务必要注意的更改，具体取决于您处于编辑模式的约束。
  - 在约束编辑模式下此按钮调整要匹配的元素范围的约束。
  - 在编辑模式的框架中此按钮调整要匹配的约束定义的位置的元素帧。


## <a name="surface-based-constraint-editing"></a>编辑图面基于约束

在前面的部分中，我们已了解如何添加默认约束和删除使用约束工具栏的约束。 进行详细的细微调整的约束编辑，我们可以与直接在设计图面上的约束进行交互。 本部分介绍的编辑图面基于约束，包括 pin 间距控件、 拖放区域和使用不同类型的约束的基础知识。

### <a name="creating-constraints"></a>创建约束

IOS 设计器工具提供用于操作设计图面上的元素的控件的两种的类型。 *将控件拖*和*pin 间距控件*，如以下图像中所示：

![视图控件](designer-auto-layout-images/controls.png)

这些是通过在约束栏中选择限制模式按钮切换。

4 T 调整元素的每一侧句柄定义*顶部*，*右*，*底部*，和*左*边缘的元素约束。 两个 I 形右侧和底部元素句柄定义*高度*和*宽度*约束分别。 中间的正方形处理这两*centerX*和*centerY*约束。

若要创建的约束，选取一个句柄并拖动到设计图面上的某个位置。 当你开始拖动时，告诉你什么的面上将出现绿色的行框的一系列你可以将限制。 例如，在下面的屏幕截图中，我们为要约束的中间按钮的顶部：

 [![](designer-auto-layout-images/image07.png "约束的顶部中间的按钮")](designer-auto-layout-images/image07.png#lightbox)

在其他两个按钮，请注意三个虚线绿色线条。 绿色几行指明*拖放区域*，或向其我们可以将限制其他元素的属性。 在上面的屏幕截图，其他两个按钮提供 3 垂直拖放区域 (*底部*， *centerY*，*顶部*) 若要将限制我们的按钮。 在视图的顶部绿色虚线意味着视图控制器提供顶部的视图，约束和实心绿色的复选框则表示视图控制器提供下面顶部布局指南的约束。

> [!IMPORTANT]
> 布局参考线是特殊类型的约束的目标，使我们能够创建顶部和底部考虑系统条，如状态栏或工具栏存在的约束。 主要用途之一是具有 iOS 6 和 iOS 7 之间兼容的应用程序，因为最新版本具有状态栏下方延伸的容器视图。 有关顶级布局指南的详细信息，请参阅[Apple 文档](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2)。



接下来三个部分介绍使用不同类型的约束。

### <a name="size-constraints"></a>大小限制

具有大小约束-*高度*和*宽度*-有两个选项。 第一个选项是拖动手柄以约束为邻居元素大小，如上面的示例所示。 另一个选项是双击要创建自助约束的句柄。 这使得我们可以指定常量大小值，如下面的屏幕截图所示：

 [![](designer-auto-layout-images/sizec.png "拖动手柄以约束为元素大小的邻居，如此处所示")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Center 约束

将创建的正方形句柄*centerX*或*centerY*约束，具体取决于上下文。 拖动正方形控点将亮的其他元素提供这两个垂直和水平拖放区域中，如下面的屏幕截图所示：

 [![](designer-auto-layout-images/centerc.png "Center 约束")](designer-auto-layout-images/centerc.png#lightbox)

如果你选择垂直拖放区域， *centerY*将创建约束。 如果你选择水平拖放区域，约束将基于*centerX*。

### <a name="combinational-constraints"></a>Combinational 约束

若要创建对齐方式和两个元素间的大小相等约束，可以选择项从顶部的工具栏上，指定的顺序的水平对齐方式、 垂直对齐方式和大小相等，如下面的屏幕截图所示：

 [![](designer-auto-layout-images/image06.png "Combinational 约束")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>可视化和编辑约束

当您添加了约束时，它将显示在设计图面上为一条蓝线时选择一项：

 [![](designer-auto-layout-images/image09.png "可视化约束")](designer-auto-layout-images/image09.png#lightbox)

你可以通过单击一条蓝线并编辑直接在属性面板中的约束值选择一个约束。 或者，在一条蓝线上双击会弹出 popover，你可以编辑直接在设计图面上的值：

 [![](designer-auto-layout-images/image08.png "编辑约束")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>约束问题

当使用约束时，可能会出现几种类型的问题：

-  **冲突的约束**-多个约束强制要具有冲突的属性的值的元素和约束引擎是无法对帐它们时将发生这种情况。
-  **Underconstrained 项**— 元素的属性 （位置 + 大小） 必须完全涵盖通过其组的约束和内部函数的约束，才能有效大小。 如果这些值是不明确，项则称其为 underconstrained。
-  **帧放错位置**— 当元素的框架和其组的约束定义两个不同的生成矩形时将发生这种情况。


本部分基础进行阐述的上面，列出的三个问题，并提供有关如何处理它们的详细信息。

### <a name="conflicting-constraints"></a>冲突的约束

冲突的约束用红色标出，并且具有一个警告符号。 将鼠标悬停在警告符号将打开有关冲突的信息与 popover:

 [![](designer-auto-layout-images/image11.png "冲突的约束-警告")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Underconstrained 的项

Underconstrained 的项显示为橙色，并触发视图控制器对象栏中的橙色标记图标的外观：

 [![](designer-auto-layout-images/image02.png "Underconstrained 的项显示为橙色")](designer-auto-layout-images/image02.png#lightbox)

如果你单击该标记图标，可以场景中获取 underconstrained 项有关的信息，并解决问题，通过任一完全限定它们或通过删除其约束，如下面的屏幕截图所示：

 [![](designer-auto-layout-images/image10.png "修复 Underconstrained 的项")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>帧放错位置

帧放错位置作为 underconstrained 项使用相同的颜色代码。 将始终使用其本机框架，图面上呈现的项，但在帧放错位置的情况下红色矩形将标记其中项最终将得到应用程序运行时，如下面的屏幕截图所示：

 [![](designer-auto-layout-images/image05.png "示例帧放错位置视图")](designer-auto-layout-images/image05.png#lightbox)

若要解决帧放错位置错误，请选择**更新帧基于约束**从约束工具栏 （最右侧的按钮） 的按钮：

 [![](designer-auto-layout-images/image03.png "更新基于约束工具栏按钮的帧")](designer-auto-layout-images/image03.png#lightbox)

这将自动调整以匹配由控件定义的位置的元素帧。

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>修改在代码中的约束

根据你的应用程序的要求，有时可能需要来修改代码中的约束。 例如，若要调整大小或位置视图约束被附加到，若要更改约束的优先级或完全停用一个约束。

若要访问代码中的约束，首先需要将其公开 iOS 设计器中，通过执行以下操作：

1. 创建作为正常 （使用任何上面列出的方法） 的约束。
2. 在**文档大纲资源管理器**，查找所需的约束并进行选择：

    [![](designer-auto-layout-images/modify01.png "文档大纲资源管理器")](designer-auto-layout-images/modify01.png#lightbox)
3. 接下来，分配**名称**到中的约束**小组件**选项卡**属性资源管理器**:

    [![](designer-auto-layout-images/modify02.png "小组件选项卡")](designer-auto-layout-images/modify02.png#lightbox)
4. 保存更改。

与上述更改后，你可以访问代码中的约束，并修改其属性。 例如，可以使用以下附加视图为零的高度设置：

```csharp
ViewInfoHeight.Constant = 0;
```

在 iOS 设计器中给定的约束的以下设置：

[![](designer-auto-layout-images/modify03.png "编辑属性资源管理器中的约束")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>延迟的布局过程

自动布局引擎计划而不立即更新以响应更改限制的附加的视图，_已推迟布局过程_不久的将来的。 在此延迟阶段中，不只是给定的视图的约束更新，约束为层次结构中的每个视图将重新计算，更新针对新的布局进行调整。

任何时候中,，你可以计划已推迟布局过程通过调用`SetNeedsLayout`或`SetNeedsUpdateConstraints`父视图的方法。 

已推迟布局过程包括查看层次结构中的两个唯一传递：

- **更新传递**-在此阶段中，自动布局引擎遍历查看层次结构，并调用`UpdateViewConstraints`视图的所有控制器上的方法和`UpdateConstraints`上所有视图的方法。
- **在布局处理过程**-再次自动布局引擎遍历查看层次结构，但这一次调用`ViewWillLayoutSubviews`视图的所有控制器上的方法和`LayoutSubviews`上所有视图的方法。 `LayoutSubviews`方法将更新`Frame`自动布局引擎的每个矩形的子视图的属性计算。

### <a name="animating-constraint-changes"></a>约束更改进行动画处理

除了修改约束属性，可以使用核心动画更改视图的约束进行动画处理。 例如：

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

此处的关键调用`LayoutIfNeeded`父视图在动画块内部的方法。 这将告知视图后，若要绘制的动画的位置或大小更改每个"框架"。 没有此行，视图会快速跳到最终版本而无需进行动画处理。

## <a name="summary"></a>总结

本指南引入 iOS 自动 （或"自适应"） 布局和约束以的设计图面上的元素之间的关系的数学表示形式的概念。 它描述如何在使用 iOS 设计器中启用自动数据布局**约束工具栏**，和编辑单独在设计图面上的约束。 接下来，它解释如何三个常见的约束问题进行疑难解答。 最后，它还介绍了如何修改在代码中的约束。

## <a name="related-links"></a>相关链接

- [情节提要简介](~/ios/user-interface/storyboards/index.md)
- [iOS 可设计控件演练](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Android 设计器概述](~/android/user-interface/android-designer/index.md)
- [以编程方式的约束](~/ios/user-interface/programmatic-layout-constraints.md)
- [Apple-自动布局指南](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html#/apple_ref/doc/uid/TP40010853-CH13-SW1)
