---
title: Windows
description: 本文介绍如何使用 windows 和 Xamarin.Mac 应用程序中的面板。 它介绍创建 windows 以及在 Xcode 和接口生成器中，从情节提要和.xib 文件加载它们和以编程方式使用它们的面板。
ms.topic: article
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 4b8de30cecb738fecb13616a3b796c0b4fa5a51a
ms.sourcegitcommit: 7b88081a979381094c771421253d8a388b2afc16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="windows"></a>Windows

_本文介绍如何使用 windows 和 Xamarin.Mac 应用程序中的面板。它介绍创建 windows 以及在 Xcode 和接口生成器中，从情节提要和.xib 文件加载它们和以编程方式使用它们的面板。_

在使用 C# 和.NET Xamarin.Mac 应用程序中，有权访问相同的窗口和面板，开发人员使用*Objective C*和*Xcode*未。 因为 Xamarin.Mac 与 Xcode 直接集成，你可以使用 Xcode 的_接口生成器_来创建和维护您的 Windows 和面板 （或根据需要在 C# 代码中直接创建它们）。

根据其用途，Xamarin.Mac 应用程序可以提供一个或多个窗口在屏幕上进行管理并协调它显示，并且可以配合工作的信息。 窗口的主要功能是：

1. 若要提供的视图和控件中的某个区域可以放置和管理。
2. 若要接受和响应事件以响应用户交互使用键盘和鼠标。

Windows 可以是无模式状态 （例如文本编辑器，可以同时打开多个文档） 中使用或模式 （如应用程序可以继续操作之前必须关闭导出对话框）。

面板是一种特殊的窗口 (基的一个子类`NSWindow`类)，，通常提供提供的辅助功能应用程序，如文本格式检查器和系统颜色选取器之类的实用程序窗口中。

[![](window-images/intro01.png "编辑在 Xcode 中的窗口")](window-images/intro01.png#lightbox)

在本文中，我们将介绍使用 Xamarin.Mac 应用程序中的 Windows 和面板的基础知识。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在本文中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于网络-最多 C# 类 OBJECTIVE-C 的对象和 UI 元素。

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Windows 简介

如上面所述，一个窗口提供了一个的区域中的视图和控件可以放置和管理，对基于用户交互 （无论通过键盘或鼠标指针） 的事件做出响应。

根据 Apple，有 macOS 应用中的 Windows 的五种主要类型：

- **文档窗口**的文档窗口包含基于文件的用户数据，例如电子表格或文本文档。
- **应用程序窗口**-应用程序窗口是不是基于文档的 （如 Mac 上的日历应用） 的应用程序的主窗口。
- **面板**-面板浮动其他窗口之上，并且提供了工具或打开文档时，用户可使用的控件。 在某些情况下，一个面板可以是半透明 (例如，当处理大型图形)。
- **对话框**-一个对话框将出现在响应用户操作，并且通常会使方式用户可以完成操作。 在关闭之前，对话框将需要从用户的响应。 (请参阅[使用对话框](~/mac/user-interface/dialog.md))
- **警报**-警报是严重问题发生 （如错误） 时，将显示的对话框的特殊类型或为警告 （例如，准备要删除的文件）。 由于警报是一个对话框，它还要求用户响应可以在关闭之前。 (请参阅[使用警报](~/mac/user-interface/alert.md))

有关详细信息，请参阅[有关 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Main、 密钥和非活动窗口

Xamarin.Mac 应用程序中的 Windows 可以外观和行为以不同的方式基于用户与它们的当前交互方式。 最重要的原因的文档或用户的关注的重点是当前的应用程序窗口称为_主窗口_。 在大多数情况下此窗口也将_密钥窗口_（当前正在接受用户输入的窗口）。 但这并非总是如此，例如，颜色选取器无法处于打开状态，而是在用户交互与文档窗口 （它将仍是主窗口） 中的项的状态更改的密钥窗口。

主程序集和密钥 Windows （如果它们是单独） 始终处于活动状态，_非活动窗口_是打开的窗口，不在前台。 例如，文本编辑器应用程序可能有多个文档打开状态时，仅主窗口将处于活动状态，所有其他用户将处于非活动状态。 

有关详细信息，请参阅[有关 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>命名 Windows

窗口可以显示标题栏和标题显示时，它通常是应用程序的名称、 处理文档的名称或窗口 （如检查器） 的函数。 某些应用程序不显示标题栏，因为它们是可以识别使其不可见，不处理的文档。

Apple 提供建议的以下准则：

- 使用主要、 非文档窗口的标题你应用程序名称。 
- 命名新的文档窗口`untitled`。 对于第一个新文档，不将追加一个数字到标题 (例如`untitled 1`)。 如果用户创建保存和标题第一个之前的另一个新文档时，调用该窗口`untitled 2`， `untitled 3`，等等。

有关详细信息，请参阅[命名 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>全屏幕 Windows

在 macOS，应用程序的窗口可以转全屏显示隐藏的所有内容包括应用程序菜单栏 （这可以通过将光标移动到屏幕顶部显示的） 提供受干扰可用与之进行交互内容。

Apple 提供的建议以下准则：

- 确定是否有意义窗口转全屏显示。 提供简短交互，（例如一个计算器） 的应用程序不应提供全屏幕模式。
- 显示工具栏全屏幕任务需要它。 通常在全屏显示模式隐藏工具栏。
- 全屏窗口应具有完成任务所需要的所有功能用户。
- 用户处于全屏窗口时，如果可能，避免 Finder 交互。
- 利用的增加的屏幕空间而无需切换焦点离开的主要任务。

有关详细信息，请参阅[全屏幕 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Panels" />

### <a name="panels"></a>面板

面板是一个辅助窗口，其中包含控件和影响的活动文档或选择 （如系统的颜色选取器中） 的选项：

[![](window-images/panel01.png "颜色面板")](window-images/panel01.png#lightbox)

面板可以是_应用特定_或_系统级_。 特定于应用程序面板 float 通过应用程序的文档窗口的顶部，并在应用程序是在后台时消失。 系统级面板 (如**字体**面板)，float 位于无论应用程序的所有打开窗口之上。 

Apple 提供的建议以下准则：

- 一般情况下，使用标准面板，应仅使用透明面板，并谨慎地图形密集型任务。
- 请考虑使用一个面板来向用户授予对重要的控件或直接影响其任务的信息的轻松访问。
- 隐藏和显示所需的面板。
- 面板应始终包含标题栏。
- 面板不应包含一个活动的最小化按钮。

#### <a name="inspectors"></a>检查器

大多数现代 macOS 应用程序提供辅助控件和选项会影响的活动文档或所选内容为_检查器_属于主窗口 (如**页**应用所示)，而不是使用面板 Windows:

[![](window-images/panel02.png "示例检查器")](window-images/panel02.png#lightbox)

有关详细信息，请参阅[面板](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1)Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)和我们[MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/)示例应用程序的完整实现**检查器界面**Xamarin.Mac 应用中。

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>创建和维护在 Xcode 中的 Windows

时创建新的 Xamarin.Mac Cocoa 应用程序时，默认情况下将获得标准保留为空，窗口。 在中定义此 windows`.storyboard`自动包括在项目中的文件。 若要编辑您的 windows 设计、 在**解决方案资源管理器**，双击`Main.storyboard`文件：

[![](window-images/edit01.png "选择主情节提要")](window-images/edit01.png#lightbox)

这将在 Xcode 的接口生成器中打开的窗口设计：

[![](window-images/edit02.png "编辑在 Xcode 中 UI")](window-images/edit02.png#lightbox)

在**属性检查器**，有几个可用于定义和控制您的窗口的属性：

- **标题**-这是将显示在窗口的标题栏的文本。
- **自动保存**-这是_密钥_将用于 ID 窗口时自动保存的位置和设置。
- **标题栏**-该窗口是否显示的标题栏。
- **统一的标题和工具栏**-如果该窗口包括一个工具栏，应设置为标题栏的一部分。
- **完整大小的内容视图**-允许的内容区域的窗口标题栏下面。
- **卷影**-窗口是否有阴影。
- **纹理**-纹理的 windows 可以使用效果，（例如活力） 和可通过在其正文上任意位置拖动转移。
- **关闭**-窗口是否具有关闭按钮。
- **最大程度减少**-窗口是否具有最小化按钮。
- **调整大小**-窗口是否具有调整控件。
- **工具栏按钮**-窗口是否具有隐藏/显示工具栏按钮。
- **可还原**-是窗口的位置和自动保存和还原设置。
- **可见在启动**-自动显示窗口时`.xib`加载文件。
- **隐藏在停用**-隐藏的窗口时应用程序进入背景。
- **释放时，关闭**-为窗口关闭时从内存中清除。
- **始终显示工具提示**-是不断显示工具提示。
- **重新计算视图循环**-是重新计算绘制窗口之前查看订单。
- **空间**， **Exposé**和**Cycling** -所有定义窗口中这些 macOS 环境的行为方式。 
- **全屏**-确定此窗口可以输入全屏显示模式。 
- **动画**-控制动画可用于窗口的类型。
- **外观**-控制窗口的外观。 现在是只有一个外观，浅绿色。

请参阅 Apple 的[简介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)和[NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow)更多详细信息的文档。

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>设置的默认大小和位置

若要设置你的窗口的初始位置并控制它的大小，切换到**大小检查器**:

[![](window-images/edit07.png "默认大小和位置")](window-images/edit07.png#lightbox)

从此处你可以设置窗口的初始大小，使其最小和最大大小，屏幕上设置的初始位置并控制窗口周围的边框。

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>设置自定义的主窗口控制器

若要能够创建 Outlet 和操作，以公开对 C# 代码的 UI 元素，Xamarin.Mac 应用将需要使用自定义窗口控制器。

请执行以下操作：

1. 在 Xcode 的接口生成器中打开应用程序的情节提要。
2. 选择`NSWindowController`在设计图面中。
3. 切换到**标识检查器**查看，并输入`WindowController`作为**类名**: 

    [![](window-images/windowcontroller01.png "设置的类名称")](window-images/windowcontroller01.png#lightbox)
4. 保存所做的更改并返回到 Visual Studio for Mac 同步。
5. A`WindowController.cs`文件将添加到项目中**解决方案资源管理器**适用于 Mac 的 Visual Studio 中： 

    [![](window-images/windowcontroller02.png "选择 windows 控制器")](window-images/windowcontroller02.png#lightbox)
6. 重新打开在 Xcode 的接口生成器情节提要。
7. `WindowController.h`文件将可供使用： 

    [![](window-images/windowcontroller03.png "编辑 WindowController.h 文件")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>添加 UI 元素

若要定义窗口的内容，将控件从**库检查器**到**界面编辑器**。 请参阅我们[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)文档了解有关使用接口生成器来创建和启用控件的详细信息。

例如，让我们来拖动从工具栏**库检查器**到中的窗口**界面编辑器**:

[![](window-images/edit03.png "从库中选择一个工具栏")](window-images/edit03.png#lightbox)

接下来，在中拖动**文本视图**并调整其以填充工具栏下方区域大小：

[![](window-images/edit04.png "添加文本视图")](window-images/edit04.png#lightbox)

由于我们希望**文本视图**若要收缩和增长随窗口的大小的更改时，让我们切换到**约束编辑器**并添加以下约束：

[![](window-images/edit05.png "编辑约束")](window-images/edit05.png#lightbox)

通过单击为**红色我-无线数据交换内容**在编辑器的顶端和单击**添加 4 约束**，我们将指示文本视图粘在给定 X 和 Y 坐标和扩大或收缩水平和垂直作为调整窗口大小时。

最后，让我们公开**文本视图**编写的代码使用**Outlet** (同时确保选择`ViewController.h`文件):

[![](window-images/edit06.png "配置上的电源插座")](window-images/edit06.png#lightbox)

保存所做的更改并切换回 Visual Studio for Mac 与 Xcode 同步。

有关使用**Outlet**和**操作**，请参阅我们[Outlet，操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)文档。

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>标准窗口工作流

为任何窗口中创建和使用 Xamarin.Mac 应用程序中，进程基本上是与我们做了什么只上面相同：

1. 对于不是默认值自动添加到你的项目的新 windows，向项目添加新的窗口定义。 这将在下面将详细讨论。
2. 双击`Main.storyboard`文件以打开在 Xcode 的接口生成器中编辑的窗口设计。
3. 将一个新窗口拖到用户界面的设计和挂钩到主窗口中使用的窗口_Segues_ (有关详细信息请参阅[Segues](~/mac/platform/storyboards/indepth.md#Segues)一部分我们[使用情节提要](~/mac/platform/storyboards/indepth.md)文档)。
3. 在中设置任何所需的窗口属性**属性检查器**和**大小检查器**。
4. 在生成你的接口并将其在配置所需的控件中拖动**属性检查器**。
5. 使用**大小检查器**来处理你的 UI 元素的大小调整。
6. 公开对通过 C# 代码的窗口的 UI 元素**Outlet**和**操作**。
7. 保存所做的更改并切换回 Visual Studio for Mac 与 Xcode 同步。

现在，我们已创建的基本窗口，我们来看看典型的进程应用程序使用 windows 时 Xamarin.Mac。 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>显示的默认窗口

默认情况下，新的 Xamarin.Mac 应用程序将自动显示中定义的窗口`MainWindow.xib`文件已启动：

[![](window-images/display01.png "运行示例窗口")](window-images/display01.png#lightbox)

由于我们已修改该窗口上面的设计，它现在包括默认工具栏和**文本视图**控件。 以下主题中`Info.plist`文件负责显示此窗口：

[![](window-images/display00.png "编辑 Info.plist")](window-images/display00.png#lightbox)

**主界面**下拉列表中用于选择将用作为主应用程序 UI 的情节提要 (在这种情况下`Main.storyboard`)。

视图控制器自动添加到项目中以控制 （以及其主视图） 显示该主窗口。 在中定义`ViewController.cs`文件并附加到**文件的所有者**中在接口生成器**标识检查器**:

[![](window-images/display02.png "设置文件的所有者")](window-images/display02.png#lightbox)

对于我们的窗口中，我们想要其具有的标题，`untitled`它首次打开时因此让我们替代`ViewWillAppear`中的方法`ViewController.cs`以如下所示：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
``` 

> [!NOTE]
> 我们正在设置的值的窗口的`Title`中的属性`ViewWillAppear`方法而不是`ViewDidLoad`方法因为该视图可能会加载到内存，而不尚未完全实例化。 如果我们尝试访问`Title`中的属性`ViewDidLoad`方法则会得到`null`由于窗口尚未已构造和高有线-达属性尚未的异常。

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>以编程方式关闭窗口

可能想要以编程方式关闭 Xamarin.Mac 应用程序中的窗口的时间，而不具有用户单击窗口的**关闭**按钮或使用菜单项。 macOS 提供两个不同的方法来关闭`NSWindow`以编程方式：`PerformClose`和`Close`。

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

调用`PerformClose`方法`NSWindow`模拟用户单击窗口的**关闭**按钮通过暂时突出显示按钮，然后关闭窗口。

如果应用程序实施`NSWindow`的`WillClose`事件，将会引发之前窗口已关闭。 如果事件返回`false`，则将不会关闭窗口。 如果不具有窗口**关闭**按钮，或者无法关闭出于任何原因，操作系统将发出警报的声音。

例如：

```csharp
MyWindow.PerformClose(this);
```

将尝试关闭`MyWindow``NSWindow`实例。 如果成功，将关闭窗口、 否则将发出警报声音和将将保持打开状态。

<a name="Close" />

### <a name="close"></a>关闭

调用`Close`方法`NSWindow`不模拟用户单击窗口的**关闭**按钮通过暂时突出显示按钮，它只需关闭该窗口。

窗口没有要显示要关闭和`NSWindowWillCloseNotification`通知将发布到正在关闭窗口的默认通知中心。

`Close`方法在两个重要方面与不同`PerformClose`方法：

1. 它不会尝试引发`WillClose`事件。
2. 它不模拟用户单击**关闭**通过暂时突出显示按钮的按钮。

例如：

```csharp
MyWindow.Close();
```

将以关闭`MyWindow``NSWindow`实例。

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>修改的 Windows 内容

在 macOS，Apple 提供了一种方法通知用户，窗口中的内容 (`NSWindow`) 已由用户修改，并需要保存。 如果该窗口包含将修改的内容，将显示在它的一个小的黑色点**关闭**小组件：

[![](window-images/close01.png "具有已修改的标记的窗口")](window-images/close01.png#lightbox)

如果用户尝试关闭窗口或退出窗口的内容的 Mac 应用程序时有未保存更改，你应呈现[对话框](~/mac/user-interface/dialog.md)或[模式表](~/mac/user-interface/dialog.md)和允许用户保存他们的更改第一个：

[![](window-images/close02.png "保存工作表关闭窗口时显示的一个")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>将标记为已修改的窗口

若要将标记为具有修改内容的窗口，请使用下面的代码：

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

和后已保存更改，则清除修改的标志使用：

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>在关闭窗口之前保存更改

若要监视用户关闭窗口，并允许其将事先保存修改的内容，你将需要创建的一个子类`NSWindowDelegate`，并重写其`WindowShouldClose`方法。 例如：

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWidowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWidowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            // is the window dirty?
            if (Window.DocumentEdited) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Critical,
                    InformativeText = "Save changes to document before closing window?",
                    MessageText = "Save Document",
                };
                alert.AddButton ("Save");
                alert.AddButton ("Lose Changes");
                alert.AddButton ("Cancel");
                var result = alert.RunSheetModal (Window);

                // Take action based on resu;t
                switch (result) {
                case 1000:
                    // Grab controller
                    var viewController = Window.ContentViewController as ViewController;

                    // Already saved?
                    if (Window.RepresentedUrl != null) {
                        var path = Window.RepresentedUrl.Path;

                        // Save changes to file
                        File.WriteAllText (path, viewController.Text);
                        return true;
                    } else {
                        var dlg = new NSSavePanel ();
                        dlg.Title = "Save Document";
                        dlg.BeginSheet (Window, (rslt) => {
                            // File selected?
                            if (rslt == 1) {
                                var path = dlg.Url.Path;
                                File.WriteAllText (path, viewController.Text);
                                Window.DocumentEdited = false;
                                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                                viewController.View.Window.RepresentedUrl = dlg.Url;
                                Window.Close();
                            }
                        });
                        return true;
                    }
                    return false;
                case 1001:
                    // Lose Changes
                    return true;
                case 1002:
                    // Cancel
                    return false;
                }
            }

            return true;
        }
        #endregion
    }
}
```

下面的代码用于将此委托的一个实例附加到你的窗口：

```csharp
// Set delegate
Window.Delegate = new EditorWidowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>在关闭应用之前保存更改

最后，您 Xamarin.Mac 的应用程序应检查以查看是否的所有窗口包含将修改的内容，并允许用户在退出前保存所做的更改。 若要执行此操作，编辑你`AppDelegate.cs`文件，请重写`ApplicationShouldTerminate`方法并使其如下所示：

```csharp
public override NSApplicationTerminateReply ApplicationShouldTerminate (NSApplication sender)
{
    // See if any window needs to be saved first
    foreach (NSWindow window in NSApplication.SharedApplication.Windows) {
        if (window.Delegate != null && !window.Delegate.WindowShouldClose (this)) {
            // Did the window terminate the close?
            return NSApplicationTerminateReply.Cancel;
        }
    }

    // Allow normal termination
    return NSApplicationTerminateReply.Now;
}
```

<a name="Working_with_Multiple_Windows" />

## <a name="working-with-multiple-windows"></a>使用多个窗口

大多数文档基于 Mac 应用程序可以同时编辑多个文档。 例如，文本编辑器可以打开以进行同时编辑多个文本文件。 默认情况下，我们新 Xamarin.Mac 应用程序有**文件**菜单，其中包含**新建**项自动有线-最多`newDocument:`**操作**。

我们将以激活此新项并允许用户打开我们主窗口同时编辑多个文档的多个副本。

让我们编辑我们`AppDelegate.cs`文件并添加以下计算属性：

```csharp
public int UntitledWindowCount { get; set;} =1;
```

我们将使用此跟踪的未保存的文件数，以便我们可以向用户 （每 Apple 的准则如上文所述） 的反馈。

接下来，添加以下方法：

```csharp
[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

此代码创建我们窗口控制器的新版本，加载新窗口，使它 Main 和密钥窗口中，并将其设置标题。 现在，如果我们运行我们的应用程序，并选择**新建**从**文件**菜单将打开一个新的编辑器窗口，并将其显示：

[![](window-images/display04.png "已添加新的未命名的窗口")](window-images/display04.png#lightbox)

如果我们打开**Windows**菜单上，你可以看到应用程序自动跟踪和处理我们打开的窗口：

[![](window-images/display05.png "Widows 菜单")](window-images/display05.png#lightbox)

有关使用 Xamarin.Mac 应用程序中的菜单的详细信息，请参阅我们[与菜单一起使用](~/mac/user-interface/menu.md)文档。

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>获取当前处于活动状态的窗口

在的 Xamarin.Mac 应用程序可以打开多个窗口 （文档），有你将需要以获取当前的最顶端窗口 （密钥窗口） 的时间。 下面的代码将返回密钥的窗口：

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

它可以在任何类或需要访问当前的密钥窗口的方法调用。 如果没有窗口是当前打开的它将返回`null`。

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>访问所有应用程序窗口

有时可能需要访问所有 Xamarin.Mac 应用当前已经打开的窗口。 例如，若要查看用户想要打开的文件是否是已在正在退出的窗口中打开。

`NSApplication.SharedApplication`维护`Windows`包含你的应用程序在所有打开的窗口的数组的属性。 你可以循环访问要访问所有应用程序的当前 windows 此数组。 例如：

```csharp
// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

代码示例中，我们要强制转换到自定义每个返回的窗口`ViewController`我们的应用程序和测试自定义的值中的类`Path`根据用户想要打开的文件路径的属性。 如果文件已打开，我们将该窗口带到靠前位置。

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>调整代码中的窗口大小

有该应用程序需要调整大小的窗口中代码的时间。 若要调整大小和重新定位一个窗口，你调整它的`Frame`属性。 在调整窗口的大小，你通常需要还调整它的来源，以使窗口保持在相同的位置，由于 macOS 的坐标系统。

与 iOS 左上角其中表示 (0，0)，不同 macOS 使用数学坐标系屏幕左下角其中表示 (0，0)。 在 iOS 中坐标时增加将向右侧的向下移动。 在 macOS，坐标增加向上右侧的值中。 

下面的代码示例调整窗口大小时：

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> 当你调整窗口大小和代码中的位置时，你需要确保遵从接口生成器中设置的最小和最大大小。 这将不会自动生效，你将能够显示窗口进行放大或缩小数超过这些限制。

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>监视窗口大小的更改

有时可能需要监视你 Xamarin.Mac 的应用程序内的窗口大小更改。 例如，若要重绘内容以适合的新大小。

若要监视大小更改，请首先确保你已在 Xcode 的接口生成器窗口控制器分配的自定义类。 例如，`MasterWindowController`在下面的示例：

[![](window-images/resize01.png "标识检查器")](window-images/resize01.png#lightbox)

接下来，编辑自定义窗口控制器类和监视器`DidResize`控制器的窗口接收通知的实时大小的更改上的事件。 例如：

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

或者，你可以使用`DidEndLiveResize`事件，以仅后收到通知用户已完成更改窗口的大小。 例如：

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

        Window.DidEndLiveResize += (sender, e) => {
        // Do something after the user's finished resizing
        // the window
    };
}
```

<a name="Setting_a_Window’s_Title_and_Represented_File" />

## <a name="setting-a-windows-title-and-represented-file"></a>设置窗口的标题和表示文件

使用表示文档的 windows 时`NSWindow`具有`DocumentEdited`属性，如果设置为`true`显示关闭按钮可以向用户授予指示文件已修改，并在关闭前应保存在一个小的点。

让我们编辑我们`ViewController.cs`文件并进行以下更改：

```csharp
public bool DocumentEdited {
    get { return View.Window.DocumentEdited; }
    set { View.Window.DocumentEdited = value; }
}
...

public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";

    View.Window.WillClose += (sender, e) => {
        // is the window dirty?
        if (DocumentEdited) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to give the user the ability to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    };
}

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Show when the document is edited
    DocumentEditor.TextDidChange += (sender, e) => {
        // Mark the document as dirty
        DocumentEdited = true;
    };

    // Overriding this delegate is required to monitor the TextDidChange event
    DocumentEditor.ShouldChangeTextInRanges += (NSTextView view, NSValue[] values, string[] replacements) => {
        return true;
    };

}
```

我们还监视`WillClose`窗口和检查的状态的事件`DocumentEdited`属性。 如果它是`true`我们需要使用户能够将所做的更改保存到文件。 如果我们运行我们的应用程序，并输入一些文本，将显示该点：

[![](window-images/file01.png "更改的窗口")](window-images/file01.png#lightbox)

如果我们尝试关闭窗口，我们将获取警报：

[![](window-images/file02.png "显示保存对话框")](window-images/file02.png#lightbox)

如果我们要从文件加载文档我们可以将该窗口的标题设置为文件的名称使用`window.SetTitleWithRepresentedFilename (Path.GetFileName(path));`方法 (假设`path`是一个字符串，表示所打开的文件)。 此外，我们可以设置的文件使用的 URL`window.RepresentedUrl = url;`方法。

如果 URL 指向已知操作系统的文件类型，将在标题栏中显示的图标。 如果在用户右键单击图标，将显示文件的路径。

让我们编辑`AppDelegate.cs`文件并添加以下方法：

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = true;
    dlg.CanChooseDirectories = false;

    if (dlg.RunModal () == 1) {
        // Nab the first file
        var url = dlg.Urls [0];

        if (url != null) {
            var path = url.Path;

            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow(this);

            // Load the text into the window
            var viewController = controller.Window.ContentViewController as ViewController;
            viewController.Text = File.ReadAllText(path);
                    viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
            viewController.View.Window.RepresentedUrl = url;

        }
    }
}
```

现在我们运行我们的应用程序时，如果选择**打开...**从**文件**菜单中，选择从文本文件**打开**对话框框中，并将其打开：

[![](window-images/file03.png "打开的对话框")](window-images/file03.png#lightbox)

将显示该文件并将使用该文件的图标设置标题：

[![](window-images/file04.png "加载文件的内容")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>向项目中添加一个新窗口

除了主文档窗口中外, Xamarin.Mac 应用程序可能需要向用户，如首选项或检查器面板中显示其他类型的窗口。

若要添加一个新窗口，请执行以下操作：

1. 在**解决方案资源管理器**，双击`Main.storyboard`文件以打开它以在 Xcode 的接口生成器中编辑。
2. 将一个新**窗口控制器**从**库**拖放到**设计图面**:

    [![](window-images/new01.png "在库中选择新的窗口控制器")](window-images/new01.png#lightbox)
3. 在**标识检查器**，输入`PreferencesWindow`为**情节提要 ID**: 

    [![](window-images/new02.png "设置情节提要 ID。")](window-images/new02.png#lightbox)
5. 设计你的接口： 

    [![](window-images/new03.png "设计用户界面")](window-images/new03.png#lightbox)
6. 打开应用程序菜单 (`MacWindows`)，选择**首选项...**，单击并拖动到新的窗口： 

    [![](window-images/new05.png "创建 segue")](window-images/new05.png#lightbox)
7. 选择**显示**从弹出菜单中。
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

如果我们运行代码并选择**首选项...**从**应用程序菜单**，将显示窗口：

[![](window-images/new04.png "示例首选项菜单")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>使用面板

如本文开头所述，面板将浮动其他窗口之上，并提供工具或打开文档时，用户可使用的控件。 

就像任何其他类型的创建和使用 Xamarin.Mac 应用程序中，一样的过程基本上是窗口的相同：

1. 向项目添加新的窗口定义。
2. 双击`.xib`文件以打开在 Xcode 的接口生成器中编辑的窗口设计。
2. 在中设置任何所需的窗口属性**属性检查器**和**大小检查器**。
4. 在生成你的接口并将其在配置所需的控件中拖动**属性检查器**。
5. 使用**大小检查器**来处理你的 UI 元素的大小调整。
6. 公开对通过 C# 代码的窗口的 UI 元素**Outlet**和**操作**。
7. 保存所做的更改并切换回 Visual Studio for Mac 与 Xcode 同步。

在**属性检查器**，可以使用特定于面板的以下选项：

[![](window-images/panel03.png "属性检查器")](window-images/panel03.png#lightbox)

- **样式**-，可以调整从面板的样式： 正则面板 （如下所示的标准窗口）、 实用工具面板 （具有较小的标题栏），HUD 面板 (是半透明并且标题栏背景的一部分)。
- **非正在激活**-确定中面板成为密钥窗口。
- **文档模式**-如果文档模式，面板将仅浮动高于应用程序的 windows，否则它浮动最重要的是。


若要添加新面板，请执行以下操作：

1. 在**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...**.
2. 在新的文件对话框中，选择**Xamarin.Mac** > **Cocoa 窗口与控制器**:

    [![](window-images/panels00.png "添加新的窗口控制器")](window-images/panels00.png#lightbox)
3. 对“名称”输入 `DocumentPanel`，然后单击“新建”按钮。
4. 双击`DocumentPanel.xib`文件以打开它以在接口生成器中编辑： 

    [![](window-images/new02.png "编辑 pannel")](window-images/new02.png#lightbox)
5. 删除现有窗口并将从面板拖**库检查器**中**界面编辑器**: 

    [![](window-images/panels01.png "删除现有窗口")](window-images/panels01.png#lightbox)
6. 最多挂钩面板 **文件的所有者*-**窗口*- **Outlet**: 

    [![](window-images/panels02.png "拖动到面板连接")](window-images/panels02.png#lightbox)
7. 切换到**标识检查器**和设置面板的类为`DocumentPanel`: 

    [![](window-images/panels03.png "设置面板的类")](window-images/panels03.png#lightbox)
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。
7. 编辑`DocumentPanel.cs`文件并将类定义更改为以下： 

    `public partial class DocumentPanel : NSPanel`
8. 保存对文件所做的更改。

编辑`AppDelegate.cs`文件并进行`DidFinishLaunching`方法外观如下所示：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

如果我们运行我们的应用程序时，面板将显示：

[![](window-images/panels04.png "正在运行的应用中的面板")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> 面板 Windows 已被否决通过 Apple 和应替换为**检查器**。 有关创建的完整示例**检查器**在 Xamarin.Mac 应用中，请参阅我们[MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/)示例应用程序。

<a name="Summary" />

## <a name="summary"></a>总结

本文已了解使用 Xamarin.Mac 应用程序中的 Windows 和面板的详细的信息。 我们看到不同的类型，并使用的 Windows 和面板、 如何创建和维护 Windows 和 Xcode 的接口生成器中的面板和如何使用 C# 代码中的 Windows 和面板。

## <a name="related-links"></a>相关链接

- [MacWindows （示例）](https://developer.xamarin.com/samples/mac/MacWindows/)
- [MacInspector （示例）](https://developer.xamarin.com/samples/mac/MacInspector/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用菜单](~/mac/user-interface/menu.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
