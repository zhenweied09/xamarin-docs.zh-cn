---
title: "情节提要快速入门"
description: "获取已启动的生成 macOS 情节提要具有的用户界面。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2017
ms.openlocfilehash: fe3a93557509aba4b33b1470879cd2504ed0f2a2
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="starting-a-new-storyboard-based-project"></a>启动新的情节提要基于项目

作为使用情节提要定义 Xamarin.Mac 应用程序的用户界面的快速介绍，让我们开始新的 Xamarin.Mac 项目。 选择“Mac” > “App” > “Cocoa 应用”，然后单击“下一步”按钮：

[![](quickstart-images/qs01.png "添加新 Cocoa 应用程序")](quickstart-images/qs01.png#lightbox)

使用**应用名称**的`MacStoryboard`单击**下一步**按钮：

[![](quickstart-images/qs02.png "设置应用程序名称")](quickstart-images/qs02.png#lightbox)

使用默认**项目名称**和**解决方案名称**单击**创建**按钮：

[![](quickstart-images/qs03.png "项目和解决方案名称")](quickstart-images/qs03.png#lightbox)

在**解决方案资源管理器**，双击`Main.storyboard`文件以打开它以在 Xcode 的接口生成器中编辑：

[![](quickstart-images/qs04.png "编辑在 Xcode 中的情节提要")](quickstart-images/qs04.png#lightbox)

如你所见上面，默认情节提要定义应用程序的菜单栏和与之其主窗口视图控制器和视图。 对于我们的示例应用，我们将需要创建的用户界面可具有一个 main_内容视图_一侧和_检查器视图_在第二个。

若要执行此操作，我们将需要首先删除默认视图控制器和附带的情节提要的视图选择它在接口生成器，然后按**删除**密钥：

[![](quickstart-images/qs05.png "删除默认视图控制器")](quickstart-images/qs05.png#lightbox)

接下来，键入`split`到**筛选器**区域中，选择垂直拆分视图控制器，并将其拖动到_设计图面_:

[![](quickstart-images/qs06.png "搜索拆分视图控制器")](quickstart-images/qs06.png#lightbox)

请注意，控制器将自动包含两个子视图控制器 （和其相关的视图），有线-最多的左侧和右侧的拆分视图。 若要将绑定到它的父窗口的拆分视图，请按**控件**键，单击窗口控制器 （蓝色圆圈窗口控制器的帧中），然后拖动线条到拆分视图控制器。 选择**窗口内容**从弹出窗口：

[![](quickstart-images/qs07.png "设置 windows 内容视图")](quickstart-images/qs07.png#lightbox)

这将关联在一起使用 Segue 两个界面元素：

[![](quickstart-images/qs08.png "窗口和内容之间 Segue")](quickstart-images/qs08.png#lightbox)

我们想要将文本视图放在左侧的拆分视图，并将其自动填充可用区域时调整大小的窗口或拆分视图。 将文本视图拖到视图控制器附加到拆分视图的顶部，单击**Pin**自动布局约束 （从底部的设计图面右侧的第二个图标）。

[![](quickstart-images/qs09.png "配置约束")](quickstart-images/qs09.png#lightbox)

从此处我们将单击的所有四个**i 形**周围的边界的图标在约束 Popover 顶部框中，单击**添加 4 约束**底部添加所需的约束的按钮。

如果我们返回到 Visual Studio for Mac 并运行该项目，请注意，文本视图自动调整大小以填充为窗口或拆分左侧的拆分视图是调整大小：

[![](quickstart-images/qs10.png "运行应用程序示例")](quickstart-images/qs10.png#lightbox)

由于我们要用作检查器区域使用拆分视图的右侧，我们希望其具有较小的大小并允许其折叠。 返回到 Xcode 并在设计图面中选择它并单击编辑右侧的视图**大小检查器**。 从此处输入**宽度**的`250`:

[![](quickstart-images/qs11.png "设置宽度")](quickstart-images/qs11.png#lightbox)

下一步选择表示右侧，拆分项设置更高**持有优先级**单击**用户可以折叠**复选框：

[![](quickstart-images/qs12.png "编辑控股优先级")](quickstart-images/qs12.png#lightbox)

如果我们回到 Visual Studio for Mac 并立即运行该项目，请注意右侧保留它是较小大小和窗口大小调整：

[![](quickstart-images/qs13.png "运行应用程序示例")](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>定义演示文稿 Segue

我们将为布局拆分视图，使其作为所选的文本的属性检查器的右侧。 我们会将某些控件拖放到底部视图拖到布局该检查器的 UI。 对于最后一个控件，我们想要显示 popover，用户可以从四个预设的字符样式选择。

我们将向该检查器与视图控制器到设计图面添加一个按钮。 我们将调整大小视图控制器的大小，我们希望我们 Popover 并向其添加四个按钮。 接下来我们将**控件**密钥-检查器视图中的按钮单击并将其拖到视图控制器，它将代表我们 popover:

[![](quickstart-images/qs14.png "拖动以创建新 segue")](quickstart-images/qs14.png#lightbox)

从弹出菜单中，我们将选择**Popover**: 

[![](quickstart-images/qs15.png "选择 segue 类型")](quickstart-images/qs15.png#lightbox)

最后，我们将在设计图面中选择 Segue，并设置**首选边缘**到**左**。 然后，我们将拖动中的一行**定位点视图**到我们想要附加到 popover 的按钮：

[![](quickstart-images/qs16.png "拖动以创建新 segue")](quickstart-images/qs16.png#lightbox)

如果我们回到 Visual Studio for Mac，请运行应用程序，然后单击**无**popover 检查器中的按钮才会显示：

[![](quickstart-images/qs17.png "下面举例说明运行 segue")](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>创建应用程序首选项

大多数标准 macOS 应用提供_首选项对话框_，它允许用户以定义控制应用程序，如外观或用户帐户的各个方面的几个选项。

若要定义一个标准的首选项对话框窗口，第一次拖动到设计图面选项卡视图控制器：

[![](quickstart-images/qs18.png "编辑在 Xcode 中的情节提要")](quickstart-images/qs18.png#lightbox)

同样，这将自动附带两个子查看控制器附加。 例如起见，我们将向添加标签将居中在每个视图：

[![](quickstart-images/qs19.png "设置约束")](quickstart-images/qs19.png#lightbox)

接下来，我们想要显示首选项窗口，当用户选择**首选项...**菜单项。 从菜单栏中，选择首选项菜单项，**控件**密钥单击和拖动线条到我们的选项卡视图控制器：

[![](quickstart-images/qs20.png "拖动以创建 segue")](quickstart-images/qs20.png#lightbox)

从弹出窗口中，我们将选择**模式**以显示为模式对话框此窗口：

[![](quickstart-images/qs21.png "选择 segue 类型")](quickstart-images/qs21.png#lightbox)

如果我们保存更改，返回到适用于 Mac，Visual Studio 运行应用并选择**首选项...**菜单项，将显示对话框我们新首选项：

[![](quickstart-images/qs22.png "下面举例说明运行 segue")](quickstart-images/qs22.png#lightbox)

你可能注意到，这看上去不像标准 macOS 应用首选项对话框窗口。 若要解决此问题，Xamarin.Mac 应用中包含两个图像文件`Resources`文件夹中的**解决方案资源管理器**并返回到 Xcode 的接口生成器。

选择的选项卡视图控制器和交换机其**样式**到**工具栏**: 

[![](quickstart-images/qs23.png "设置选项卡栏样式")](quickstart-images/qs23.png#lightbox)

选择每个选项卡并为其提供**标签**和选择一个映像来表示它：

[![](quickstart-images/qs24.png "在 Xcode 中配置每个选项卡")](quickstart-images/qs24.png#lightbox)

如果我们保存更改，返回到适用于 Mac，Visual Studio 运行应用并选择**首选项...**菜单项，现在将显示如标准 macOS 应用的对话框：

[![](quickstart-images/qs25.png "正在运行的首选项窗口的一个示例")](quickstart-images/qs25.png#lightbox)

有关详细信息，请参阅我们[处理映像](~/mac/app-fundamentals/image.md)，[菜单](~/mac/user-interface/menu.md)， [Windows](~/mac/user-interface/window.md)和[对话框](~/mac/user-interface/dialog.md)文档。

## <a name="related-links"></a>相关链接

- [MacStoryboard （示例）](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
