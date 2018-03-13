---
title: ".xib 文件"
description: "本文介绍如何使用在 Xcode 的接口生成器可以创建和维护 Xamarin.Mac 应用程序的用户界面中创建的.xib 文件的工作。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 9a4b8ac68da7bcb25ea55906e8fe060b7c7fdee9
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="xib-files"></a>.xib 文件

_本文介绍如何使用在 Xcode 的接口生成器可以创建和维护 Xamarin.Mac 应用程序的用户界面中创建的.xib 文件的工作。_

> [!NOTE]
> 创建 Xamarin.Mac 应用的用户界面的首选的方法是使用情节提要。 本文档已被保留在原处，出于历史原因和用于处理旧 Xamarin.Mac 项目。 有关详细信息，请参阅我们[简介情节提要](~/mac/platform/storyboards/index.md)文档。

## <a name="overview"></a>概述

在使用 C# 和.NET Xamarin.Mac 应用程序中，有权访问相同的用户界面元素和工具的开发人员*Objective C*和*Xcode*未。 因为 Xamarin.Mac 与 Xcode 直接集成，你可以使用 Xcode 的_接口生成器_来创建和维护你的用户界面 （或根据需要在 C# 代码中直接创建它们）。

.Xib 文件是 macOS 用于在 Xcode 的接口生成器中以图形方式定义应用程序的用户界面元素 （如菜单、 Windows、 视图、 标签、 文本字段中） 创建和维护。

[![正在运行的应用程序示例](xib-images/intro01.png "正在运行的应用程序示例")](xib-images/intro01-large.png#lightbox)

在本文中，我们将介绍使用 Xamarin.Mac 应用程序中的.xib 文件的基础知识。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章首先，因为它涉及关键概念以及我们将在本文中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`属性用于连接你的 C# 类 OBJECTIVE-C 的对象和 UI 元素。


## <a name="introduction-to-xcode-and-interface-builder"></a>Xcode 和 Interface Builder 简介

作为 Xcode 的一部分，Apple 已创建一个名为接口生成器中，可用于在设计器中以可视方式创建你的用户界面工具。 与接口生成器，使你可以使用 OBJECTIVE-C 的用户执行的相同工具创建你的 UI，Xamarin.Mac fluently 集成。


### <a name="components-of-xcode"></a>Xcode 组件

当您从Visual Studio for Mac在Xcode中打开.xib文件时，它将在左侧打开一个 **Project Navigator**，中间的 **Interface Hierarchy** 和 **Interface Editor** 以及右侧的 **Properties＆Utilities** 部分：

[![Xcode UI 中的各组成部分](xib-images/xcode03.png "Xcode UI 的组件")](xib-images/xcode03-large.png#lightbox)

让我们了解在每个 Xcode 部分功能以及如何将使用它们创建界面 Xamarin.Mac 应用程序。


#### <a name="project-navigation"></a>项目导航

当您打开一个用于在Xcode中进行编辑的.xib文件时，Visual Studio for Mac会在后台创建一个Xcode项目文件，以在其自身与Xcode之间传递更改。 更高版本，当你切换回 Visual Studio for Mac 从 Xcode，对此项目进行任何更改与同步 Xamarin.Mac 项目由 Visual Studio for Mac。

**项目导航**部分允许您之间的所有文件组成此都导航_填充码_Xcode 项目。 通常情况下，你只会对感兴趣的.xib 文件类似于此列表中**MainMenu.xib**和**MainWindow.xib**。


#### <a name="interface-hierarchy"></a>接口层次结构

**接口层次结构**部分允许您轻松地访问的用户界面的多个键属性，例如其**占位符**和 main**窗口**。 本部分还可用于你的用户界面和调整它们通过拖动围绕它们在层次结构嵌套的方式访问进行的各个元素 (views)。


#### <a name="interface-editor"></a>界面编辑器

**界面编辑器**部分提供有关其图面你布局你的用户界面以图形方式。 您要将元素从**库**部分**属性和实用程序**节以创建你的设计。 当用户界面元素 (views) 添加到设计图面上时，会将它们添加到**接口层次结构**部分的顺序，它们会显示在**界面编辑器**。


#### <a name="properties--utilities"></a>属性和实用程序

**属性和实用程序**部分 devided 为两个主要部分，我们将使用的**属性**（也称为检查器） 和**库**:

![属性检查器](xib-images/xcode04.png "属性检查器")

最初本节几乎是空的但是如果你选择中的某个元素**界面编辑器**或**接口层次结构**、**属性**将填充部分提供有关给定的元素和属性，你可以调整的信息。

“属性”部分内具有 8 个不同的检查器选项卡，如下所示：

[![所有检查器概述](xib-images/xcode05.png "的所有检查器概述")](xib-images/xcode05-large.png#lightbox)

由左至右，这些选项卡依次为：

-   **文件检查器** – 文件检查器显示文件信息，例如正在编辑的 Xib 文件的文件名称和位置。
-   **快速帮助** – 快速帮助选项卡基于 Xcode 中所选内容提供相应背景帮助。
-   **标识检查器** – 标识检查器提供有关所选控件/视图的信息。
-   **属性检查器**– 属性检查器允许你自定义的每个视图的控件所选的各种属性。
-   **大小检查器**– 大小检查器使你能够控制的大小和调整大小的每个视图的控件所选的行为。
-   **连接检查器**– 连接检查器显示所选控件中的 outlet，操作连接。 我们将稍后检查插座和操作。
-   **绑定检查器**– 绑定检查器允许你配置控件，以便它们的值会自动绑定到数据模型。
-   **查看效果检查器**– 视图效果检查器允许你指定的控件，例如动画效果。

在**库**部分中，可以查找控件和对象以图形方式放到设计器到生成你的用户界面：

![库检查器的示例](xib-images/xcode06.png "的库检查器示例")

既然你已经熟悉的 Xcode IDE 和接口生成器，我们来看使用它来创建用户界面。


## <a name="creating-and-maintaining-windows-in-xcode"></a>创建和维护在 Xcode 中的 windows

用于创建 Xamarin.Mac 应用程序的用户界面的首选的方法是借助情节提要 (请参阅我们[简介情节提要](~/mac/platform/storyboards/index.md)文档的详细信息)，因此，任何新的项目启动 Xamarin.Mac 将默认情况下使用情节提要。

若要切换到使用.xib 基于 UI，执行以下操作：

1. 打开 Visual Studio for Mac 并启动新的 Xamarin.Mac 项目。
2. 在**解决方案 Pad**，右键单击该项目并选择**添加** > **新文件...**
3. 选择**Mac** > **Windows 控制器**:

    ![添加新的窗口控制器](xib-images/setup00.png "添加新的窗口控制器")
4. 输入`MainWindow`的名称，然后单击**新建**按钮：

    ![添加新的主窗口](xib-images/setup01.png "添加新的主窗口")
5. 再次右键单击项目并选择**添加** > **新文件...**
6. 选择**Mac** > **主菜单**:

    ![添加新的主菜单](xib-images/setup02.png "添加新的主菜单")
7. 保留的名称作为`MainMenu`单击**新建**按钮。
8. 在**解决方案 Pad**选择**Main.storyboard**文件，右键单击并选择**删除**:

    ![选择主要 storyboard](xib-images/setup03.png "选择主情节提要")
9. 在删除对话框中，单击**删除**按钮：

    ![确认删除](xib-images/setup04.png "确认删除")
10. 在**解决方案 Pad**，双击**Info.plist**文件以打开进行编辑。
11. 选择`MainMenu`从**主界面**下拉列表中：

    [![设置主菜单](xib-images/setup05.png "设置主菜单")](xib-images/setup05-large.png#lightbox)
12. 在**解决方案 Pad**，双击**MainMenu.xib**文件以打开它以在 Xcode 的接口生成器中编辑。
13. 在**库检查器**，类型`object`搜索字段中将一个新**对象**拖到设计图面：

    [![编辑主菜单](xib-images/setup06.png "编辑主菜单")](xib-images/setup06-large.png#lightbox)
14. 在**标识检查器**，输入`AppDelegate`为**类**:

    [![选择应用程序委托](xib-images/setup07.png "选择应用程序委托")](xib-images/setup07-large.png#lightbox)
15. 选择**文件的所有者**从**接口层次结构**，切换到**连接检查器**拖动线条，将从委托到`AppDelegate` **对象**刚添加到项目：

    [![连接应用程序委托](xib-images/setup08.png "连接应用程序委托")](xib-images/setup08-large.png#lightbox)
16. 保存更改并返回到 Visual Studio for Mac。

所有这些更改就地，编辑**AppDelegate.cs**文件并使其如下所示：

```csharp
using AppKit;
using Foundation;

namespace MacXib
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public MainWindowController mainWindowController { get; set; }

        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
            mainWindowController = new MainWindowController ();
            mainWindowController.Window.MakeKeyAndOrderFront (this);
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

现在在中定义应用程序的主窗口**.xib**添加窗口控制器时，会自动包括在项目中的文件。 若要编辑您的 windows 设计、 在**解决方案 Pad**，双击**MainWindow.xib**文件：

![选择 MainWindow.xib 文件](xib-images/edit01.png "选择 MainWindow.xib 文件")

这将在 Xcode 的接口生成器中打开的窗口设计：

[![编辑 MainWindow.xib](xib-images/edit02.png "编辑 MainWindow.xib")](xib-images/edit02-large.png#lightbox)


### <a name="standard-window-workflow"></a>标准窗口工作流

为任何窗口中创建和使用 Xamarin.Mac 应用程序中，进程基本上是相同：

1. 对于不是默认值自动添加到你的项目的新 windows，向项目添加新的窗口定义。
2. 双击要打开在 Xcode 的接口生成器中编辑的窗口设计的.xib 文件。
3. 在中设置任何所需的窗口属性**属性检查器**和**大小检查器**。
4. 在生成你的接口并将其在配置所需的控件中拖动**属性检查器**。
5. 使用**大小检查器**来处理你的 UI 元素的大小调整。
6. 公开对通过插座和操作的 C# 代码的窗口的 UI 元素。
7. 保存所做的更改并切换回 Visual Studio for Mac 与 Xcode 同步。


### <a name="designing-a-window-layout"></a>设计窗口布局

用于布局出接口生成器中的用户界面进程基本上是相同的你添加的每个元素：

1. 查找中的所需的控件**库检查器**并将其拖到**界面编辑器**并将其位置。
2. 在中设置任何所需的窗口属性**属性检查器**。
3. 使用**大小检查器**来处理你的 UI 元素的大小调整。
4. 如果使用自定义类，则将其设置**标识检查器**。
5. 公开对通过插座和操作的 C# 代码的 UI 元素。
6. 保存所做的更改并切换回 Visual Studio for Mac 与 Xcode 同步。

例如：

1. 在 Xcode 中，从“库”部分拖动“Push Button”：

    [![从库中选择按钮](xib-images/xcode07.png "从库中选择按钮")](xib-images/xcode07-large.png#lightbox)
2. 删除按钮拖动到**窗口**中**界面编辑器**:

    [![向窗口中添加一个按钮](xib-images/xcode08.png "到窗口中添加按钮")](xib-images/xcode08-large.png#lightbox)
3. 单击“属性检查器”中的 **Title** 属性，将此按钮的标题更改为 `Click Me`：

    ![设置按钮的特性](xib-images/xcode09.png "设置按钮属性")
4. 从“库”部分拖动“标签”：

    [![在库中选择标签](xib-images/xcode10.png "在库中选择一个标签")](xib-images/xcode10-large.png#lightbox)
5. 将此标签拖放到“界面编辑器”中此按钮旁的“窗口”中：

    [![将标签添加到窗口](xib-images/xcode11.png "向窗口中添加标签")](xib-images/xcode11-large.png#lightbox)
6. 按住此标签上的右控点，将其拖动至靠近窗口边缘的位置：

    [![调整大小标签](xib-images/xcode12.png "调整大小标签")](xib-images/xcode12-large.png#lightbox)
7. 仍在中选择的标签**界面编辑器**，切换到**大小检查器**:

    ![选择大小检查器](xib-images/xcode13.png "选择大小检查器")
8. 在**自动调整大小框**单击**Dim 红色括号**右侧和**Dim 红色水平箭头**中心中：

    ![编辑自动调整大小属性](xib-images/xcode14.png "编辑自动调整大小属性")
9. 这可确保标签将拉伸以增加和减少在运行的应用程序中调整窗口的大小时。 **红色方括号**顶部和左侧的和**自动调整大小框**框告诉死机到其给定的 X 和 Y 位置的标签。
10. 将所做的更改保存到用户界面

已调整大小和移动控件的时候，你应已注意到，接口生成器使您可以在基于有用的管理单元提示[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 这些准则将帮助你创建高质量应用程序将具有 Mac 用户熟悉外观和感觉。

如果你查看**接口层次结构**部分中，请注意如何显示的布局和构成我们用户界面元素的层次结构：

![接口层次结构中选择元素](xib-images/xcode15.png "接口层次结构中选择元素")

从这里你可以选择要编辑或重新排序 UI 元素，如果需要通过拖动的项。 例如，如果正在由另一个元素覆盖 UI 元素，你无法将其拖到列表，使其窗口上的最顶层的项的底部。

有关使用 Windows Xamarin.Mac 应用程序中的详细信息，请参阅我们[Windows](~/mac/user-interface/window.md)文档。


## <a name="exposing-ui-elements-to-c-code"></a>对 C# 代码的公开 UI 元素

完成布局接口生成器中用户界面的外观和感觉，你将需要公开的 UI 元素，以便它们可以从 C# 代码进行访问。 若要执行此操作，你将使用操作和 outlet。


### <a name="setting-a-custom-main-window-controller"></a>设置自定义的主窗口控制器

若要能够创建 Outlet 和操作，以公开对 C# 代码的 UI 元素，Xamarin.Mac 应用将需要使用自定义窗口控制器。

请执行以下操作：

1. 在 Xcode 的接口生成器中打开应用程序的情节提要。
2. 选择`NSWindowController`在设计图面中。
3. 切换到**标识检查器**查看，并输入`WindowController`作为**类名**:

    [![编辑的类名称](xib-images/windowcontroller01.png "编辑的类名称")](xib-images/windowcontroller01-large.png#lightbox)
4. 保存所做的更改并返回到 Visual Studio for Mac 同步。
5. 一个 **WindowController.cs** 文件将被添加到Visual Studio for Mac中 **解决方案板** 中的项目中：

    ![Visual Studio for Mac中的新类名称](xib-images/windowcontroller02.png "Visual Studio for Mac") 中的新类名称
6. 重新打开在 Xcode 的接口生成器情节提要。
7. **WindowController.h**文件将可供使用：

    [![在 Xcode 中匹配的.h 文件](xib-images/windowcontroller03.png "在 Xcode 中匹配的.h 文件")](xib-images/windowcontroller03-large.png#lightbox)


### <a name="outlets-and-actions"></a>容器和操作

因此，什么是容器和操作？ 在传统的 .NET 用户界面编程中，用户界面中的控件在添加时会自动作为属性公开。 然而在 Mac 中情况则不同，如果仅将控件添加到视图，代码不可对其进行访问。 开发人员必须对代码显式公开 UI。 按顺序执行此操作，Apple 为我们提供了两个选项：

-  **输出口** – 输出口类似于属性。 如果你连接控件到插座，被公开到你的代码通过一个属性，因此你可以进行某些操作，如附加事件处理程序，在它等上调用方法。
-  **操作** - 操作类似于 WPF 中的命令模式。 例如时在控件上执行某个操作，, 单击按钮的假设，控件将自动在你的代码中调用方法。 操作是功能强大且方便，因为你可以许多控件绑定到相同的操作。

在 Xcode 中，容器和操作添加在通过的代码中直接*控件拖动*。 更具体地说，这意味着，若要创建 outlet 或操作的你选择你想要添加电源插座或操作，请按住的控件元素**控件**按钮在键盘上，并将该控件拖到你的代码的直接。

对于 Xamarin.Mac 开发人员而言，这意味着，你将拖动到对应的 OBJECTIVE-C 的存根 （stub） 文件到想要创建 outlet 或操作的 C# 文件。 Visual Studio for Mac创建了一个名为 **MainWindow.h** 的文件，作为使用Interface Builder生成的Shim Xcode项目的一部分：

[![在 Xcode 中的.h 文件的示例](xib-images/xcode16.png "在 Xcode 中的.h 文件的示例")](xib-images/xcode16-large.png#lightbox)

此存根 （stub）.h 文件镜像**MainWindow.designer.cs** ，它将自动添加到新的 Xamarin.Mac 项目`NSWindow`创建。 此文件可用于同步接口生成器所做的更改，且将在其中我们创建你的容器和操作，以便 UI 元素公开给 C# 代码。


#### <a name="adding-an-outlet"></a>添加上的电源插座

对于基本了解插座和操作的是，让我们看一下创建公开到 C# 代码的 UI 元素上的电源插座。

请执行以下操作：

1. 在 Xcode 中屏幕右上角，单击“双圆”按钮，打开“助手编辑器”：

    [![选择助手编辑器中](xib-images/outlet01.png "选择助手编辑器")](xib-images/outlet01-large.png#lightbox)
2. 此时 Xcode 会切换为拆分视图模式，“界面编辑器”位于一边，“代码编辑器”位于另一边。
3. 请注意，已自动选取 Xcode **MainWindowController.m**文件中**代码编辑器**，这是不正确。 如果你从上插座和操作哪些上面我们讨论请记住，我们需要将**MainWindow.h**选。
4. 在顶部**代码编辑器**单击**自动链接**和选择**MainWindow.h**文件：

    [![选择正确的.h 文件](xib-images/outlet02.png "选择正确的.h 文件")](xib-images/outlet02-large.png#lightbox)
5. 此时 Xcode 应选择了正确的文件：

    [![选择的正确文件](xib-images/outlet03.png "选择正确的文件")](xib-images/outlet03-large.png#lightbox)
6. **最后一步非常重要！** 如果你没有选择正确的文件，你将无法创建的容器和操作也会公开给 C# 中的错误类 ！
7. 在**界面编辑器**，按住**控件**键键盘上，单击并拖动到代码编辑器上面创建的标签正下方`@interface MainWindow : NSWindow { }`代码：

    [![拖动来创建一个新插座](xib-images/outlet04.png "拖动来创建一个新插座")](xib-images/outlet04-large.png#lightbox)
8. 会显示一个对话框。 保留**连接**将设置到插座并输入`ClickedLabel`为**名称**:

    [![设置电源插座属性](xib-images/outlet05.png "设置 Outlet 属性")](xib-images/outlet05-large.png#lightbox)
9. 单击**连接**按钮以创建 outlet:

    ![已完成的 Outlet](xib-images/outlet06.png "已完成的插座")
10. 保存对文件所做的更改。


#### <a name="adding-an-action"></a>添加操作

接下来，让我们看一下创建要公开与对 C# 代码的 UI 元素的用户交互的操作。

请执行以下操作：

1. 请确保我们仍位于**助手编辑器中**和**MainWindow.h**文件会显示在**代码编辑器**。
2. 在**界面编辑器**，按住**控件**键键盘上，单击并拖动到代码编辑器上面创建的按钮正下方`@property (assign) IBOutlet NSTextField *ClickedLabel;`代码：

    [![若要创建操作的拖动](xib-images/action01.png "拖动创建操作")](xib-images/action01-large.png#lightbox)
3. 更改**连接**类型设置为操作：

    [![选择操作类型](xib-images/action02.png "选择操作类型")](xib-images/action02-large.png#lightbox)
4. 输入 `ClickedButton` 作为**名称**：

    [![配置操作](xib-images/action03.png "配置操作")](xib-images/action03-large.png#lightbox)
5. 单击**连接**按钮以创建操作：

    ![已完成的操作](xib-images/action04.png "已完成的操作")
6. 保存对文件所做的更改。

使用用户界面有线向上和向 C# 代码公开，切换回 Visual Studio for Mac 并让它 Xcode 和接口生成器中的更改同步。


### <a name="writing-the-code"></a>编写代码

与你创建的用户界面公开给通过插座和操作代码及其 UI 元素，你就可以编写代码以将你的程序融入生活。 例如，打开**了构成**文件进行编辑，通过双击它在**解决方案 Pad**:

[![了构成文件](xib-images/code01.png "了构成文件")](xib-images/code01-large.png#lightbox)

并添加以下代码到`MainWindow`类以使用上述步骤中创建的示例插座：

```csharp
private int numberOfTimesClicked = 0;
...

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

请注意，`NSLabel`通过 C# 中的直接的名称，它在 Xcode 中创建时分配你其 outlet 在 Xcode 中，在这种情况下，调用`ClickedLabel`。 你可以访问任何方法或已公开对象的属性相同的方式将任何正常的 C# 类。

> [!IMPORTANT]
> 你需要使用`AwakeFromNib`，而不是另一种方法，如`Initialize`，这是因为`AwakeFromNib`称为_后_操作系统已加载和实例化用户界面与.xib 文件。 如果你尝试访问标签控件.xib 文件之前已完全加载并实例化，则会遇到`NullReferenceException`错误因为不会尚未创建标签控件。

接下来，添加到下面的分部类`MainWindow`类：

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

此代码将附加到你在 Xcode 和接口生成器中创建并将用户单击按钮时调用的操作。

一些 UI 元素自动具有内置的操作，例如，默认的菜单栏中的项如**打开...**菜单项 (`openDocument:`)。 在**解决方案 Pad**，双击`AppDelegate.CS`文件以打开进行编辑，并添加下面的代码下面`DidFinishLaunching`方法：

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = false;
    dlg.CanChooseDirectories = true;

    if (dlg.RunModal () == 1) {
        var alert = new NSAlert () {
            AlertStyle = NSAlertStyle.Informational,
            InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
            MessageText = "Folder Selected"
        };
        alert.RunModal ();
    }
}
```

键的行是`[Export ("openDocument:")]`，它告诉`NSMenu`， **AppDelegate**有一个方法`void OpenDialog (NSObject sender)`响应`openDocument:`操作。

有关使用菜单的详细信息，请参阅我们[菜单](~/mac/user-interface/menu.md)文档。


## <a name="synchronizing-changes-with-xcode"></a>使用 Xcode 同步更改

当你切换回 Visual Studio for Mac 从 Xcode 时，则在 Xcode 中所做的任何更改将自动同步与 Xamarin.Mac 项目中。

如果你选择**MainWindow.designer.cs**中**解决方案 Pad**你将能够看到如何我们 outlet，操作具有已有线了我们的 C# 代码中：

[![使用 Xcode 同步更改](xib-images/sync01.png "与 Xcode 同步更改")](xib-images/sync01-large.png#lightbox)

请注意如何在这两个定义**MainWindow.designer.cs**文件：

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

中的定义与对齐**MainWindow.h**在 Xcode 中的文件：

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

如您所见，Visual Studio for Mac会监听.h文件的更改，然后自动同步各个 **.designer.cs** 文件中的这些更改以将它们公开给您的应用程序。 你可能注意**MainWindow.designer.cs**是一个分部类，以便 Visual Studio for Mac 无需修改**了构成**这会覆盖我们具有对类进行了任何更改。

你通常将从不需要以打开**MainWindow.designer.cs**你自己，它此处提供出于教育目的仅。

> [!IMPORTANT]
> 在大多数情况下，Visual Studio for Mac将自动查看Xcode中所做的任何更改并将其同步到Xamarin.Mac项目。 如果同步不自动进行，请切换回 Xcode，然后再次切换到 Visual Studio for Mac。 这通常会开始同步周期。


## <a name="adding-a-new-window-to-a-project"></a>向项目中添加一个新窗口

除了主文档窗口中外, Xamarin.Mac 应用程序可能需要向用户，如首选项或检查器面板中显示其他类型的窗口。 将一个新窗口添加到你的项目时，你应始终使用**Cocoa 窗口与控制器**选项，因为这使得加载窗口从.xib 文件更容易的过程。

若要添加一个新窗口，请执行以下操作：

1. 在**解决方案 Pad**，右键单击该项目并选择**添加** > **新文件...**.
2. 在新的文件对话框中，选择**Xamarin.Mac** > **Cocoa 窗口与控制器**:

    ![添加新的窗口控制器](xib-images/new01.png "添加一个新的窗口控制器")
3. 对“名称”输入 `PreferencesWindow`，然后单击“新建”按钮。
4. 双击**PreferencesWindow.xib**文件以打开它以在接口生成器中编辑：

    [![编辑在 Xcode 中的窗口](xib-images/new02.png "编辑在 Xcode 中的窗口")](xib-images/new02-large.png#lightbox)
5. 设计你的接口：

    [![设计 windows 布局](xib-images/new03.png "设计窗口布局")](xib-images/new03-large.png#lightbox)
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

以下代码添加到**AppDelegate.cs**以显示新窗口：

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

`var preferences = new PreferencesWindowController ();`行创建的窗口控制器的.xib 文件从加载窗口，并将它的新实例。 `preferences.Window.MakeKeyAndOrderFront (this);`行向用户显示新窗口。

如果你运行代码并选择**首选项...**从**应用程序菜单**，将显示窗口：

![运行示例应用程序](xib-images/new04.png "运行示例应用程序")

有关使用 Windows Xamarin.Mac 应用程序中的详细信息，请参阅我们[Windows](~/mac/user-interface/window.md)文档。


## <a name="adding-a-new-view-to-a-project"></a>向项目中添加新视图

有的时间时更轻松地将你的窗口设计分解成多个，更易于管理的.xib 文件。 例如，如切出的主窗口的内容，选择中的工具栏项时**首选项窗口**换出响应中的内容或**源列表**选择。

将新视图添加到你的项目时，你应始终使用**Cocoa 视图与控制器**选项，因为这使得加载视图从.xib 文件更容易的过程。

若要添加新视图，请执行以下操作：

1. 在**解决方案 Pad**，右键单击该项目并选择**添加** > **新文件...**.
2. 在新的文件对话框中，选择**Xamarin.Mac** > **Cocoa 视图与控制器**:

    ![添加新视图](xib-images/view01.png "添加新视图")
3. 对“名称”输入 `SubviewTable`，然后单击“新建”按钮。
4. 双击**SubviewTable.xib**文件以打开以便进行接口生成器中编辑和设计用户界面：

    [![设计在 Xcode 中的新视图](xib-images/view02.png "设计在 Xcode 中的新视图")](xib-images/view02-large.png#lightbox)
5. 连接的任何所需的操作和 outlet。
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

接下来编辑**SubviewTable.cs**并添加以下代码到**AwakeFromNib**文件以在加载时填充新视图：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));
    DataSource.Sort ("Title", true);

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);

    // Auto select the first row
    ProductTable.SelectRow (0, false);
}
```

将枚举添加到项目来跟踪哪个视图当前正在显示。 例如， **SubviewType.cs**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

编辑的窗口中将使用该视图并显示它的.xib 文件。 添加**自定义视图**作为容器的视图后加载到内存的 C# 代码和调用它到插座公开`ViewContainer`:

[![创建所需的电源插座](xib-images/view03.png "创建所需的电源插座")](xib-images/view03-large.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

接下来，编辑将会显示新视图的窗口的.cs 文件 (例如，**了构成**) 并添加以下代码：

```csharp
private SubviewType ViewType = SubviewType.None;
private NSViewController SubviewController = null;
private NSView Subview = null;
...

private void DisplaySubview(NSViewController controller, SubviewType type) {

    // Is this view already displayed?
    if (ViewType == type) return;

    // Is there a view already being displayed?
    if (Subview != null) {
        // Yes, remove it from the view
        Subview.RemoveFromSuperview ();

        // Release memory
        Subview = null;
        SubviewController = null;
    }

    // Save values
    ViewType = type;
    SubviewController = controller;
    Subview = controller.View;

    // Define frame and display
    Subview.Frame = new CGRect (0, 0, ViewContainer.Frame.Width, ViewContainer.Frame.Height);
    ViewContainer.AddSubview (Subview);
}
```

当我们需要显示新视图从窗口的容器中的.xib 文件加载 (**自定义视图**前面添加)，此删除任何现有视图，然后将它进行交换个新的代码句柄。 它会查看它已有一个视图，显示，如果因此它从其移除屏幕。 它采用已传递的视图的下一步中 （如从视图控制器加载） 调整其大小以适合内容区域的大小，并将其添加到所显示的内容。

若要显示新视图，请使用下面的代码：

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

这将创建新视图的视图控制器，要显示的新实例，设置其类型 （按照指定的枚举添加到项目中） 并使用`DisplaySubview`方法添加到窗口的类，以实际显示的视图。 例如:

[![运行示例应用程序](xib-images/view04.png "运行示例应用程序")](xib-images/view04-large.png#lightbox)

有关使用 Windows Xamarin.Mac 应用程序中的详细信息，请参阅我们[Windows](~/mac/user-interface/window.md)和[对话框](~/mac/user-interface/dialog.md)文档。


## <a name="summary"></a>摘要

本文已详细的介绍了在使用 Xamarin.Mac 应用程序中的.xib 文件。 我们已了解了不同类型和用法的.xib 文件以创建应用程序的用户界面，如何创建和维护.xib 文件在 Xcode 的界面生成器以及如何使用 C# 代码中的.xib 文件。


## <a name="related-links"></a>相关链接

- [MacImages（示例）](https://developer.xamarin.com/samples/mac/MacImages/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [菜单](~/mac/user-interface/menu.md)
- [对话框](~/mac/user-interface/dialog.md)
- [处理映像](~/mac/app-fundamentals/image.md)
- [macOS 人工界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
