---
title: Xamarin.Mac 中的菜单
description: 本文介绍如何使用 Xamarin.Mac 应用程序中的菜单。 它介绍创建和维护菜单和菜单项在 Xcode 和接口生成器和以编程方式使用它们。
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: cb89d1df60bafe14dcc989666f0eeb5d757e4017
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792916"
---
# <a name="menus-in-xamarinmac"></a>Xamarin.Mac 中的菜单

_本文介绍如何使用 Xamarin.Mac 应用程序中的菜单。它介绍创建和维护菜单和菜单项在 Xcode 和接口生成器和以编程方式使用它们。_

在使用 C# 和.NET Xamarin.Mac 应用程序中，你有权访问相同 Cocoa 菜单执行开发人员在 OBJECTIVE-C 和 Xcode 中工作。 因为 Xamarin.Mac 与 Xcode 直接集成，你可以使用 Xcode 的接口生成器来创建并维护你的菜单栏、 菜单和菜单项 （或根据需要在 C# 代码中直接创建它们）。

菜单是不可或缺的组成部分 Mac 应用程序的用户体验，通常显示在用户界面的各个部分：

- **应用程序的菜单栏**-这是显示在每个 Mac 应用程序的屏幕顶部的主菜单。
- **上下文菜单**-这些包显示在用户右键单击或控件的单击窗口中的项目时。
- **状态栏**-这是在最右侧将显示在屏幕 （左侧的菜单栏时钟） 的顶部，随着项目添加到左侧而增长的应用程序菜单栏区域。
- **停靠菜单**-显示的菜单在停靠中每个应用程序，当用户单击鼠标右键或控件的单击应用程序的图标，或当用户左键单击图标，并按住鼠标按钮。
- **弹出按钮和下拉列表**-弹出按钮显示选定的项，并显示一个列表要被用户单击后从选择的选项。 下拉列表是一种通常用于选择命令特定于当前任务的上下文的弹出按钮。 这两项可以出现任何位置在窗口中。

[![一个示例菜单](menu-images/intro01.png "示例菜单")](menu-images/intro01-large.png#lightbox)

在本文中，我们将介绍使用 Cocoa 菜单栏、 菜单和菜单项在 Xamarin.Mac 应用程序的基础知识。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在本文中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`属性用于网络-最多 C# 类 OBJECTIVE-C 的对象和 UI 元素。

## <a name="the-applications-menu-bar"></a>应用程序的菜单栏 

与在其中每个窗口可以有其自身附加到它的菜单栏 Windows 操作系统上运行的应用程序，不同在 macOS 上运行每个应用程序具有用于该应用程序中的每个窗口的屏幕的顶部运行的单个菜单栏：

[![菜单栏](menu-images/appmenu01.png "菜单栏")](menu-images/appmenu01-large.png#lightbox)

激活或停用此菜单栏上的项在任何给定时刻，根据当前上下文或应用程序和其用户界面的状态。 例如： 如果用户选择的文本字段，项上**编辑**菜单将出现启用如**复制**和**剪切**。

根据 Apple 和默认情况下，所有 macOS 应用程序都具有一组标准的菜单和显示在应用程序的菜单栏中的菜单项：

- **Apple 菜单**-此菜单提供对系统的访问在任何时候，而不考虑运行哪些应用程序都向用户提供的宽项。 不能由开发人员修改这些项目。
- **应用程序菜单**-此菜单以粗体显示应用程序的名称，并可帮助用户确定哪个应用程序当前正在运行。 它包含将应用于应用程序作为一个整体和而不是给定的文档或进程如退出应用程序处理的项。
- **文件菜单**-项用于创建、 打开、 或保存文档，它与你的应用程序。 如果你的应用程序不是基于文档的则可以重命名或移除此菜单。
- **编辑菜单**-保留命令如**剪切**，**复制**，和**粘贴**用于编辑或修改应用程序的用户界面中的元素。
- **格式菜单**-如果应用程序适用于文本，此菜单保留命令，以调整文本的格式设置。
- **视图菜单**-包含会影响显示内容如何在应用程序的用户界面中 （查看） 的命令。
- **特定于应用程序菜单**-这些是任何特定于你的应用程序 （如 web 浏览器书签菜单中） 的菜单。 它们应该显示之间**视图**和**窗口**菜单栏上的。
- **窗口菜单**-包含用于处理在你的应用程序，以及当前打开的窗口的列表的窗口的命令。
- **帮助菜单**-如果你的应用程序提供了在屏幕上的帮助，帮助菜单应的最右侧的菜单栏上。 

有关应用程序菜单栏和标准菜单和菜单项的详细信息，请参阅 Apple 的[人机接口指南](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)。

### <a name="the-default-application-menu-bar"></a>默认应用程序菜单栏

每当创建新的 Xamarin.Mac 项目时，你会自动收到了典型的项 （如以上部分中所述） 通常已 macOS 应用程序的标准，默认应用程序菜单栏。 在中定义应用程序的默认菜单栏**Main.storyboard** （以及应用程序的 UI 的其余部分） 中的项目下的文件**解决方案 Pad**:  

![选择主要 storyboard](menu-images/appmenu02.png "选择主情节提要")

双击**Main.storyboard**文件以打开它以供使用菜单编辑器界面将显示在 Xcode 的接口生成器和你中进行编辑：

[![编辑在 Xcode 中的 UI](menu-images/defaultbar01.png "编辑 Xcode 中的用户界面")](menu-images/defaultbar01-large.png#lightbox)

从此处我们可以单击项如**打开**中的菜单项**文件**菜单和编辑或调整其属性在**属性检查器**:

[![编辑菜单属性](menu-images/defaultbar02.png "编辑菜单的属性")](menu-images/defaultbar02-large.png#lightbox)

我们将深入探讨添加、 编辑和删除菜单和本文后面的项。 现在我们只想要看到默认情况下提供了哪些菜单和菜单项和如何将它们自动公开给通过一组预定义的容器和操作的代码 (有关详细信息请参阅我们[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)文档）。

例如，如果我们单击**连接检查器**为**打开**我们可以看到自动达有线的菜单项`openDocument:`操作： 

[![查看附加的操作](menu-images/defaultbar03.png "查看附加的操作")](menu-images/defaultbar03-large.png#lightbox)

如果你选择**第一个响应方**中**接口层次结构**和向下滚动**连接检查器**，并且你将看到的定义`openDocument:`操作的**打开**菜单项附加到 （以及其他几个默认操作为应用程序并不自动有线最多控件）：

[![查看所有已附连的操作](menu-images/defaultbar04.png "查看所有附加的操作")](menu-images/defaultbar04-large.png#lightbox) 

为什么这很重要？ 接下来的部分将看到这些自动定义的操作与其他 Cocoa 用户界面元素，以自动启用和禁用菜单项，以及，为项提供内置功能的工作方式。

稍后我们将使用这些内置操作启用和禁用代码中的项，并提供自己的功能，在选中时。

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>内置菜单功能

如果你运行新创建的 Xamarin.Mac 应用程序，然后添加任何 UI 项或代码，你会注意到某些项是自动有线向上并启用了为你 （具有完全的功能自动内置），如**Quit**中项**应用**菜单：

![已启用的菜单项](menu-images/appmenu03.png "启用的菜单项")

而其他菜单项，如**剪切**，**复制**，和**粘贴**不：

![禁用菜单项](menu-images/appmenu04.png "禁用菜单项")

让我们停止应用程序，并双击**Main.storyboard**文件中**解决方案 Pad**打开以进行编辑在 Xcode 中的接口生成器。 接下来，拖动**文本视图**从**库**到中的窗口的视图控制器**界面编辑器**:

[![从库中选择文本视图](menu-images/appmenu05.png "从库中选择文本视图")](menu-images/appmenu05-large.png#lightbox)

在**约束编辑器**让我们固定到窗口的边缘的文本视图并将其其中增长和收缩的窗口，通过单击所有四个红色我-无线数据交换内容在编辑器的顶部，单击设置**添加 4 约束**按钮：

[![编辑约束](menu-images/appmenu06.png "编辑约束")](menu-images/appmenu06-large.png#lightbox)

将所做的更改保存到用户界面设计并切换回 Visual Studio for Mac 要与你 Xamarin.Mac 项目同步所做的更改。 现在启动应用程序，在文本视图中键入一些文本，选中它，并打开**编辑**菜单：

![菜单项将自动启用/禁用](menu-images/appmenu07.png "菜单项将自动启用/禁用")

请注意如何**剪切**，**复制**，和**粘贴**项将自动启用并且功能齐全，所有这些操作都无需编写一行代码。 

这是怎么回事？ 请记住内置预定义的操作 （如上面介绍） 附带有线到默认菜单项，对特定操作的挂钩内置了大部分属于 macOS Cocoa 用户界面元素 (如`copy:`)。 因此当添加到一个窗口，处于活动状态，并且选择，相应的菜单项或附加到的项会自动启用该操作。 如果用户选择该菜单项，UI 元素的内置功能调用该方法并执行所有操作开发人员介入。

### <a name="enabling-and-disabling-menus-and-items"></a>启用和禁用菜单和项

默认情况下，每次用户事件发生时，`NSMenu`自动启用和禁用每个显示的菜单和菜单项基于应用程序的上下文。 有三种方法，以启用/禁用一项：

- **自动菜单启用**-如果启用菜单项`NSMenu`可以查找响应的操作项是有线-最多的相应对象。 例如，文本在上面的视图具有到内置挂钩`copy:`操作。
- **自定义操作和 validateMenuItem:** -用于绑定到任何菜单项[窗口或视图控制器自定义操作](#Working-with-Custom-Window-Actions)，你可以添加`validateMenuItem:`操作手动启用或禁用菜单项。
- **手动菜单启用**-手动设置`Enabled`每个属性`NSMenuItem`以单独启用或禁用菜单中的每个项。

若要选择一个系统，设置`AutoEnablesItems`属性`NSMenu`。 `true` 是自动 （默认行为） 和`false`是否为手动。 

> [!IMPORTANT]
> 如果你选择使用手动菜单启用，任何菜单项，甚至那些由 AppKit 的类，如控制`NSTextView`，自动更新。 你将负责启用和禁用手动在代码中的所有项。

#### <a name="using-validatemenuitem"></a>使用 validateMenuItem

如前所述，绑定到任何菜单项[窗口或视图控制器自定义操作](#Working-with-Custom-Window-Actions)，你可以添加`validateMenuItem:`操作手动启用或禁用菜单项。

在下面的示例中，`Tag`属性将用于决定的菜单项，将启用/禁用通过类型`validateMenuItem:`操作基于中选定的文本的状态`NSTextView`。 `Tag`为每个菜单项在接口生成器中设置属性：

![设置标记属性](menu-images/validate01.png "设置标记属性")

并添加以下代码添加到视图控制器：

```csharp
[Action("validateMenuItem:")]
public bool ValidateMenuItem (NSMenuItem item) {

    // Take action based on the menu item type
    // (As specified in its Tag)
    switch (item.Tag) {
    case 1:
        // Wrap menu items should only be available if
        // a range of text is selected
        return (TextEditor.SelectedRange.Length > 0);
    case 2:
        // Quote menu items should only be available if
        // a range is NOT selected.
        return (TextEditor.SelectedRange.Length == 0);
    }

    return true;
}
```

当运行此代码，并在未选定任何文本`NSTextView`，两个包装菜单项被禁用 （即使它们有线到视图控制器上的操作）：

![显示禁用项](menu-images/validate02.png "显示禁用项")

如果选择了文本的部分，并且重新打开菜单，两个包装菜单项将可用：

![启用的显示项](menu-images/validate03.png "显示启用项")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>启用和响应的代码中的菜单项

正如我们所看到更高版本，只需通过将特定 Cocoa 用户界面元素添加到 （如文本字段），我们 UI 设计几个默认菜单项将启用，并且自动工作，而无需编写任何代码。 下一步让我们看一下将自己的 C# 代码添加到我们 Xamarin.Mac 项目以启用菜单项，并提供功能，当用户选择它。

例如，让我们希望用户能够使用的 say**打开**中项**文件**菜单以选择文件夹。 由于我们希望这是应用程序级函数并不局限于给定窗口或 UI 元素，我们将添加代码以处理此给我们的应用程序委托。

在**解决方案 Pad**，双击`AppDelegate.CS`文件以打开进行编辑：

![选择应用程序委托](menu-images/appmenu08.png "选择应用程序委托")

添加下面的代码下面`DidFinishLaunching`方法：

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

让我们运行应用程序现在并打开**文件**菜单： 

![文件菜单](menu-images/appmenu09.png "文件菜单")

请注意，**打开**菜单项现已启用。 如果我们选择它，将显示打开对话框：

![打开对话框](menu-images/appmenu10.png "打开对话框")

如果我们单击**打开**按钮，将显示我们警报消息：

![一个示例消息对话框](menu-images/appmenu11.png "示例对话框消息")

键的行已`[Export ("openDocument:")]`，它告诉`NSMenu`，我们**AppDelegate**有一个方法`void OpenDialog (NSObject sender)`响应`openDocument:`操作。 如果你将从上面记得**打开**菜单项是自动有线-高达此操作在接口生成器默认情况下：

[![查看附加的操作](menu-images/defaultbar03.png "查看附加的操作")](menu-images/defaultbar03-large.png#lightbox)

下一步让我们看一下创建我们自己的菜单、 菜单项和操作和响应到它们在代码中。

### <a name="working-with-the-open-recent-menu"></a>使用打开新菜单

默认情况下，**文件**菜单包含**最近打开**项，用于跟踪的用户已打开与你的应用的最后几个文件。 如果要创建`NSDocument`基于 Xamarin.Mac 应用此菜单将自动为你处理。 对于其他类型的 Xamarin.Mac 应用，你将负责管理并手动响应此菜单项。

若要手动处理**最近打开**菜单上，你首先需要新的文件已打开或保存使用以下通知它：

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

即使你的应用不使用`NSDocuments`，仍使用`NSDocumentController`维护**最近打开**通过发送菜单`NSUrl`到文件的位置`NoteNewRecentDocumentURL`方法`SharedDocumentController`。

接下来，您需要重写`OpenFile`要打开用户从选择的任何文件的应用程序委托的方法**最近打开**菜单。 例如：

```csharp
public override bool OpenFile (NSApplication sender, string filename)
{
    // Trap all errors
    try {
        filename = filename.Replace (" ", "%20");
        var url = new NSUrl ("file://"+filename);
        return OpenFile(url);
    } catch {
        return false;
    }
}
```

返回`true`如果可以打开该文件，否则返回`false`和内置警告将显示给用户无法打开该文件。

因为从返回的文件名和路径**最近打开**菜单上，可能包含空格，我们需要在创建之前会正确地转义此字符`NSUrl`或我们将收到一个错误。 我们实现替换为以下代码：

```csharp
filename = filename.Replace (" ", "%20");
```

最后，我们创建`NSUrl`指向的文件和应用程序中的一个帮助器方法委托，以便打开新窗口，并将文件加载到它的使用：

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

若要将所有内容集中到一起，让我们看看中的示例实现**AppDelegate.cs**文件：

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace MacHyperlink
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }

        public override bool OpenFile (NSApplication sender, string filename)
        {
            // Trap all errors
            try {
                filename = filename.Replace (" ", "%20");
                var url = new NSUrl ("file://"+filename);
                return OpenFile(url);
            } catch {
                return false;
            }
        }
        #endregion

        #region Private Methods
        private bool OpenFile(NSUrl url) {
            var good = false;

            // Trap all errors
            try {
                var path = url.Path;

                // Is the file already open?
                for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
                    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
                    if (content != null && path == content.FilePath) {
                        // Bring window to front
                        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
                        return true;
                    }
                }

                // Get new window
                var storyboard = NSStoryboard.FromName ("Main", null);
                var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

                // Display
                controller.ShowWindow(this);

                // Load the text into the window
                var viewController = controller.Window.ContentViewController as ViewController;
                viewController.Text = File.ReadAllText(path);
                viewController.SetLanguageFromPath(path);
                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                viewController.View.Window.RepresentedUrl = url;

                // Add document to the Open Recent menu
                NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);

                // Make as successful
                good = true;
            } catch {
                // Mark as bad file on error
                good = false;
            }

            // Return results
            return good;
        }
        #endregion

        #region actions
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
                    // Open the document in a new window
                    OpenFile (url);
                }
            }
        }
        #endregion
    }
}
```

根据你的应用程序的要求，可能不想要同时在多个窗口中打开同一个文件的用户。 我们的示例应用程序，如果用户选择已打开的文件 (从**最近打开**或**打开...** 菜单项），包含该文件的窗口移到了前面。

若要实现此目的，我们在我们的帮助程序方法中使用下面的代码：

```csharp
var path = url.Path;

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

我们设计了我们`ViewController`类以容纳到中的文件的路径其`Path`属性。 接下来，我们循环访问应用程序中的所有当前打开的窗口。 如果在一个窗口中的文件已打开，将会进入前面的所有其他 windows 中使用：

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

如果未找到匹配的新窗口将打开，其中加载的文件，该文件将其记录在**最近打开**菜单：

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);

// Load the text into the window
var viewController = controller.Window.ContentViewController as ViewController;
viewController.Text = File.ReadAllText(path);
viewController.SetLanguageFromPath(path);
viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
viewController.View.Window.RepresentedUrl = url;

// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

<a name="Working-with-Custom-Window-Actions" />

### <a name="working-with-custom-window-actions"></a>使用自定义窗口操作

就像内置**第一个响应方**预有线进入标准菜单项的操作，你可以创建新的自定义操作并将它们连接到接口生成器中的菜单项。

首先，您的应用程序窗口控制器之一上定义的自定义操作。 例如：

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

接下来，双击中的应用程序的情节提要文件**解决方案 Pad**打开以进行编辑在 Xcode 中的接口生成器。 选择**第一个响应方**下**应用场景**，然后切换到**属性检查器**:

![属性检查器](menu-images/action01.png "属性检查器")

单击**+** 底部的按钮**属性检查器**添加新的自定义操作：

![添加新操作](menu-images/action02.png "添加新的操作")

为其提供自定义窗口控制器创建的操作与相同的名称：

![编辑操作名称](menu-images/action03.png "编辑操作名称")

控件单击并拖动到的菜单项从**第一个响应方**下**应用场景**。 从弹出列表中，选择刚创建的新操作 (`defineKeyword:`在此示例中):

![附加操作](menu-images/action04.png "附加操作")

将所做的更改保存到情节提要，并返回到 Visual Studio for Mac 以同步更改。 如果你运行该应用，使用连接对指定的自定义操作的菜单项将自动启用/禁用 （基于窗口使用开放的操作） 和关闭操作选择菜单项将激发：

[![测试新的操作](menu-images/action05.png "测试新的操作")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>添加、 编辑和删除菜单

正如我们所看到前面部分中，Xamarin.Mac 应用程序附带的默认菜单和菜单项将特定的 UI 控件将自动激活并响应了预设大量。 我们已了解如何将代码添加到我们将应用程序还启用并响应这些默认项。

在本部分中，我们将了解在删除我们不需要的菜单项、 重新组织菜单并添加新菜单、 菜单项和操作。

双击**Main.storyboard**文件中**解决方案 Pad**以将其打开以进行编辑：

[![编辑在 Xcode 中的 UI](menu-images/maint01.png "编辑 Xcode 中的用户界面")](menu-images/maint01-large.png#lightbox)

我们不打算我们特定 Xamarin.Mac 应用程序使用默认值**视图**菜单上，因此我们要将其删除。 在**接口层次结构**选择**视图**属于的主菜单栏的菜单项：

![选择视图菜单项](menu-images/maint02.png "选择视图菜单项")

按 delete 键或退格键删除菜单。 接下来，我们不打算使用中的项的所有**格式**菜单上，我们想要移动的项，我们将允许使用 out 从子菜单。 在**接口层次结构**选择以下菜单项：

![突出显示多个项](menu-images/maint03.png "突出显示多个项")

父级下的项**菜单**从其当前所在的子菜单：

[![将菜单项拖动到父菜单](menu-images/maint04.png "拖动到父菜单的菜单项")](menu-images/maint04-large.png#lightbox)

你的菜单现在应如下所示：

[![中的新位置的项](menu-images/maint05.png "中的新位置的项")](menu-images/maint05-large.png#lightbox)

下一步让我们来拖动**文本**出从下的子菜单**格式**菜单并将其放在主菜单栏之间上**格式**和**窗口**菜单：

[![文本菜单](menu-images/maint06.png "文本菜单")](menu-images/maint06-large.png#lightbox)

让我们回下**格式**菜单和删除**字体**子菜单项。 接下来，选择**格式**菜单和将其重命名"字体":

[![字体菜单](menu-images/maint07.png "字体菜单")](menu-images/maint07-large.png#lightbox)

接下来，让我们来创建将自动获取追加到文本视图中的文本在选中时的预定义短语的自定义菜单。 在底部的搜索框中**库检查器**"菜单。"中的类型 这将使更轻松地查找和使用的所有菜单 UI 元素：

![库检查器](menu-images/maint08.png "库检查器")

现在让我们执行以下操作来创建我们菜单：

1. 拖动**菜单项**从**库检查器**到之间的菜单栏上**文本**和**窗口**菜单： 

    ![在库中选择新的菜单项](menu-images/maint10.png "在库中选择新的菜单项")
2. 重命名的项"短语": 

    [![设置菜单名称](menu-images/maint09.png "设置菜单名称")](menu-images/maint09-large.png#lightbox)
3. 接下来拖动**菜单**从**库检查器**: 

    ![从库中选择一个菜单](menu-images/maint11.png "从库中选择一个菜单")
4. 然后删除**菜单**对新**菜单项**我们只需创建并将其名称更改为"短语": 

    [![编辑菜单名称](menu-images/maint12.png "编辑菜单名称")](menu-images/maint12-large.png#lightbox)
5. 现在让我们重命名三个默认**菜单项**"Address"、"日期"和"Greeting": 

    [![短语菜单](menu-images/maint13.png "短语菜单")](menu-images/maint13-large.png#lightbox)
6. 让我们添加第四个**菜单项**通过拖动**菜单项**从**库检查器**并调用它"签名": 

    [![编辑菜单项名称](menu-images/maint14.png "编辑菜单项名称")](menu-images/maint14-large.png#lightbox)
7. 将所做的更改保存到的菜单栏。

现在让我们创建一组自定义操作，以便我们新的菜单项公开给 C# 代码。 在 Xcode 中让我们切换到**助手**视图：

[![创建所需的操作](menu-images/maint15.png "创建所需的操作")](menu-images/maint15-large.png#lightbox)

让我们执行以下操作：

1. 从控件拖动**地址**到的菜单项**AppDelegate.h**文件。
2. 交换机**连接**键入到**操作**: 

    [![选择操作类型](menu-images/maint17.png "选择操作类型")](menu-images/maint17-large.png#lightbox)
3. 输入**名称**"phraseAddress"并按**连接**按钮以创建新的操作： 

    [![配置操作](menu-images/maint18.png "配置操作")](menu-images/maint18-large.png#lightbox)
4. 重复上述步骤为**日期**，**问候**，和**签名**菜单项： 

    [![已完成的操作](menu-images/maint19.png "已完成的操作")](menu-images/maint19-large.png#lightbox)
5. 将所做的更改保存到的菜单栏。

接下来，我们需要创建我们文本视图上的电源插座，以便我们可以调整代码中的其内容。 选择**ViewController.h**文件中**助手编辑器中**并创建一个名为的新插座`documentText`:

[![创建 outlet](menu-images/maint20.png "创建 outlet")](menu-images/maint20-large.png#lightbox)

返回到 Visual Studio for Mac 同步 Xcode 中的更改。 接下来编辑**ViewController.cs**文件并使其如下所示：

```csharp
using System;

using AppKit;
using Foundation;

namespace MacMenus
{
    public partial class ViewController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }

        public string Text {
            get { return documentText.Value; }
            set { documentText.Value = value; }
        } 
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            App.textEditor = this;
        }

        public override void ViewWillDisappear ()
        {
            base.ViewDidDisappear ();

            App.textEditor = null;
        }
        #endregion
    }
}
```

这会公开我们文本视图之外的文本`ViewController`类，并通知应用程序委托窗口时获得或失去焦点。 现在编辑**AppDelegate.cs**文件并使其如下所示：

```csharp
using AppKit;
using Foundation;
using System;

namespace MacMenus
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public ViewController textEditor { get; set;} = null;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom actions
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

        partial void phrasesAddress (Foundation.NSObject sender) {

            textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
        }

        partial void phrasesDate (Foundation.NSObject sender) {

            textEditor.Text += DateTime.Now.ToString("D");
        }

        partial void phrasesGreeting (Foundation.NSObject sender) {

            textEditor.Text += "Dear Sirs,\n\n";
        }

        partial void phrasesSignature (Foundation.NSObject sender) {

            textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
        }
        #endregion
    }
}
```

此处我们犯`AppDelegate`分部类，以便我们可以使用操作和我们定义接口生成器中的 outlet。 我们还公开`textEditor`来跟踪哪个窗口当前具有焦点。

以下方法用于处理我们的自定义菜单和菜单项：

```csharp
partial void phrasesAddress (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
}

partial void phrasesDate (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += DateTime.Now.ToString("D");
}

partial void phrasesGreeting (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Dear Sirs,\n\n";
}

partial void phrasesSignature (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
}
```

现在，如果我们运行我们的应用程序中的项的所有**短语**菜单将处于活动状态，并且将添加到文本视图时选择让短语：

![运行应用程序示例](menu-images/maint21.png "运行的应用程序示例")

现在，我们已经使用应用程序菜单栏下的基础知识，我们来看创建自定义的上下文菜单。

### <a name="creating-menus-from-code"></a>从代码中创建菜单

除了使用 Xcode 的接口生成器中创建菜单和菜单项，可能有 Xamarin.Mac 应用需要创建、 修改或从代码中删除菜单、 子菜单或菜单项的时间。

在下面的示例中，创建类来保存有关菜单项和将动态创建即时上的子菜单的信息：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace AppKit.TextKit.Formatter
{
    public class LanguageFormatCommand : NSObject
    {
        #region Computed Properties
        public string Title { get; set; } = "";
        public string Prefix { get; set; } = "";
        public string Postfix { get; set; } = "";
        public List<LanguageFormatCommand> SubCommands { get; set; } = new List<LanguageFormatCommand>();
        #endregion

        #region Constructors
        public LanguageFormatCommand () {

        }

        public LanguageFormatCommand (string title)
        {
            // Initialize
            this.Title = title;
        }

        public LanguageFormatCommand (string title, string prefix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
        }

        public LanguageFormatCommand (string title, string prefix, string postfix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
            this.Postfix = postfix;
        }
        #endregion
    }
}
```

#### <a name="adding-menus-and-items"></a>添加菜单和项

以下例程将与此类定义，分析的集合`LanguageFormatCommand`对象，并依次逐步生成新的菜单和菜单项将它们追加到已传入的 （在接口生成器中创建） 的现有菜单的底部：

```csharp
private void AssembleMenu(NSMenu menu, List<LanguageFormatCommand> commands) {
    NSMenuItem menuItem;

    // Add any formatting commands to the Formatting menu
    foreach (LanguageFormatCommand command in commands) {
        // Add separator or item?
        if (command.Title == "") {
            menuItem = NSMenuItem.SeparatorItem;
        } else {
            menuItem = new NSMenuItem (command.Title);

            // Submenu?
            if (command.SubCommands.Count > 0) {
                // Yes, populate submenu
                menuItem.Submenu = new NSMenu (command.Title);
                AssembleMenu (menuItem.Submenu, command.SubCommands);
            } else {
                // No, add normal menu item
                menuItem.Activated += (sender, e) => {
                    // Apply the command on the selected text
                    TextEditor.PerformFormattingCommand (command);
                };
            }
        }
        menu.AddItem (menuItem);
    }
}
``` 

对于任何`LanguageFormatCommand`具有空白对象`Title`属性，此例程创建**分隔符菜单项**（精简灰线） 之间菜单部分：

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

如果提供一个标题，则创建新的菜单项与该标题：

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

如果`LanguageFormatCommand`对象包含子`LanguageFormatCommand`对象，创建子菜单和`AssembleMenu`方法是以递归方式调用以构建出该菜单：

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

对于任何新的菜单项没有子菜单，添加代码以处理用户选定的菜单项：

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>测试菜单创建

所有上述代码中就地情况下，如果以下集合`LanguageFormatCommand`已创建了对象：

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Stong","**","**"));
FormattingCommands.Add(new LanguageFormatCommand("Emphasize","_","_"));
FormattingCommands.Add(new LanguageFormatCommand("Inline Code","`","`"));
FormattingCommands.Add(new LanguageFormatCommand("Code Block","```\n","\n```"));
FormattingCommands.Add(new LanguageFormatCommand("Comment","<!--","-->"));
FormattingCommands.Add (new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Unordered List","* "));
FormattingCommands.Add(new LanguageFormatCommand("Ordered List","1. "));
FormattingCommands.Add(new LanguageFormatCommand("Block Quote","> "));
FormattingCommands.Add (new LanguageFormatCommand ());

var Headings = new LanguageFormatCommand ("Headings");
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 1","# "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 2","## "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 3","### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 4","#### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 5","##### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 6","###### "));
FormattingCommands.Add (Headings);

FormattingCommands.Add(new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Link","[","]()"));
FormattingCommands.Add(new LanguageFormatCommand("Image","![]("-")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[ ![]("-")](linkimagehere/index.md)"));
```

并将集合传递给`AssembleMenu`函数 (与**格式**菜单设置作为基础)，将创建以下动态菜单和菜单项：

![正在运行的应用中的新菜单项](menu-images/dynamic01.png "正在运行的应用中的新菜单项")

#### <a name="removing-menus-and-items"></a>删除菜单和项

如果你需要从应用程序的用户界面中删除任何菜单或菜单项，则可以使用`RemoveItemAt`方法`NSMenu`只需向其提供零基于类的要移除的项的索引。

例如，若要删除的菜单和菜单项创建的上述的例程，可以使用下面的代码：

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

对于上面的代码中，因此它们不动态删除第一批的四个菜单项在 Xcode 的接口生成器和在应用中，记住的要点中创建。

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>上下文菜单

用户单击鼠标右键或控件的单击窗口中的项目时，会显示上下文菜单。 默认情况下，多个已内置于 macOS 的 UI 元素具有 （如文本视图中） 连接到它们的上下文菜单。 但是，可能有的时候我们想要创建我们自己自定义的上下文菜单，我们已添加到窗口的用户界面元素。

让我们编辑我们**Main.storyboard**文件在 Xcode 中，并添加**窗口**我们设计窗口设置其**类**到中的"NSPanel"**标识检查器**，添加新**助手**项以**窗口**菜单上，并将其附加到新的窗口使用**显示 Segue**:

[![设置 segue 类型](menu-images/context01.png "设置 segue 类型")](menu-images/context01-large.png#lightbox)

让我们执行以下操作：

1. 拖动**标签**从**库检查器**到**面板**窗口和其将文本设置为"Property": 

    [![编辑标签的值](menu-images/context03.png "编辑标签的值")](menu-images/context03-large.png#lightbox)
2. 接下来拖动**菜单**从**库检查器**到中的视图层次结构和重命名这三个默认菜单项的视图控制器**文档**，**文本**和**字体**:

    [![所需的菜单项](menu-images/context02.png "所需的菜单项")](menu-images/context02-large.png#lightbox)
3. 现在控件拖动从**属性标签**到**菜单**:

    [![拖动创建 segue](menu-images/context04.png "拖动创建 segue")](menu-images/context04-large.png#lightbox)
4. 从弹出对话框中，选择**菜单**: 

    ![设置 segue 类型](menu-images/context05.png "设置 segue 类型")
5. 从**标识检查器**，设置为"PanelViewController"的视图控制器类： 

    [![设置 segue 类](menu-images/context10.png "设置 segue 类")](menu-images/context10-large.png#lightbox)
6. 切换回 Visual Studio for Mac 才能同步，然后返回到接口生成器。
7. 切换到**助手编辑器中**和选择**PanelViewController.h**文件。
8. 创建的一项操作**文档**菜单项调用`propertyDocument`: 

    [![配置操作](menu-images/context06.png "配置操作")](menu-images/context06-large.png#lightbox)
9. 剩余的菜单项的重复创建操作： 

    [![所需的操作](menu-images/context07.png "所需的操作")](menu-images/context07-large.png#lightbox)
10. 最后创建插座**属性标签**调用`propertyLabel`: 

    [![配置电源插座](menu-images/context08.png "配置电源插座")](menu-images/context08-large.png#lightbox)
11. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

编辑**PanelViewController.cs**文件并添加以下代码：

```csharp
partial void propertyDocument (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Document";
}

partial void propertyFont (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Font";
}

partial void propertyText (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Text";
}
```

现在如果我们运行该应用程序，右键单击面板中的属性标签，我们将看到我们自定义的上下文菜单。 如果选择，从菜单项，将更改标签的值：

![运行的上下文菜单](menu-images/context09.png "运行的上下文菜单")

下一步让我们看一下创建状态栏菜单。

## <a name="status-bar-menus"></a>状态栏菜单

状态栏菜单显示给用户，如菜单或反映应用程序的状态图像的集合提供与之间交互的状态菜单项或反馈。 即使在后台运行该应用程序，应用程序的状态栏菜单是已启用并处于活动状态。 系统级状态栏驻留在应用程序菜单栏的右侧，并为唯一状态栏 macOS 中的当前可用。

让我们编辑我们**AppDelegate.cs**文件并进行`DidFinishLaunching`方法外观如下所示：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Create a status bar menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Text";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Text");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        PhraseAddress(address);
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        PhraseDate(date);
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        PhraseGreeting(greeting);
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        PhraseSignature(signature);
    };
    item.Menu.AddItem (signature);
}
```

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` 为我们提供访问到整个系统的状态栏。 `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` 创建新的状态栏项。 从此处我们将创建一个菜单和菜单项的数目，并将菜单附加到我们刚刚创建的状态栏项。 

如果我们运行应用程序时，将显示新的状态栏项。 从菜单中选择项将更改文本视图中的文本： 

![运行状态栏菜单](menu-images/statusbar01.png "运行状态栏菜单")

接下来，让我们看一下创建自定义停靠的菜单项。

## <a name="custom-dock-menus"></a>自定义停靠菜单

当用户单击鼠标右键或控件的单击停靠在应用程序的图标，则将显示停靠菜单为你的 Mac 应用程序：

![自定义停靠菜单](menu-images/dock01.png "自定义停靠菜单")

让我们创建我们的应用程序的自定义停靠菜单通过执行以下操作：

1. 在适用于 Mac 的 Visual Studio，右键单击应用程序的项目并选择**添加** > **新文件...** 从新的文件对话框中，选择**Xamarin.Mac** > **空接口定义**，用于"DockMenu"**名称**单击**新建**按钮以创建新**DockMenu.xib**文件：

    ![添加空的接口定义](menu-images/dock02.png "添加空的接口定义")
2. 在**解决方案 Pad**，双击**DockMenu.xib**文件以进行编辑在 Xcode 中打开它。 创建一个新**菜单**具有以下项：**地址**，**日期**，**问候**，和**签名** 

    [![布局 UI](menu-images/dock03.png "布局 UI")](menu-images/dock03-large.png#lightbox)
3. 接下来，让我们将我们新的菜单项连接到我们在我们自定义菜单为创建的现有操作[添加、 编辑和删除菜单](#Adding,_Editing_and_Deleting_Menus)上面一节。 切换到**连接检查器**和选择**第一个响应方**中**接口层次结构**。 向下滚动并查找`phraseAddress:`操作。 将行从圆拖到该操作**地址**菜单项：

    [![拖动到操作连接](menu-images/dock04.png "拖动到连接操作")](menu-images/dock04-large.png#lightbox)
4. 附加到其相应的操作的其他菜单项的所有重复： 

    [![所需的操作](menu-images/dock05.png "所需的操作")](menu-images/dock05-large.png#lightbox)
5. 接下来，选择**应用程序**中**接口层次结构**。 在**连接检查器**，从圆拖动线条`dockMenu`outlet 到我们刚刚创建的菜单：

    [![拖动 outlet 连接](menu-images/dock06.png "拖动 outlet 连接")](menu-images/dock06-large.png#lightbox)
6. 保存所做的更改并切换回 Visual Studio for Mac 与 Xcode 同步。
7. 双击**Info.plist**文件以打开进行编辑： 

    [![编辑 Info.plist 文件](menu-images/dock07.png "编辑 Info.plist 文件")](menu-images/dock07-large.png#lightbox)
8. 单击**源**在屏幕底部的选项卡： 

    [![选择源视图](menu-images/dock08.png "选择源视图")](menu-images/dock08-large.png#lightbox)
9. 单击**添加新条目**、 单击绿色加号按钮、 设置属性名称设置为"AppleDockMenu"和"DockMenu"（不含扩展名我们新.xib 文件的名称） 的值： 

    [![添加 DockMenu 项](menu-images/dock09.png "添加 DockMenu 项")](menu-images/dock09-large.png#lightbox)

现在如果我们运行我们的应用程序，右键单击停靠在其图标，将显示我们新的菜单项：

![停靠菜单运行的一个示例](menu-images/dock10.png "停靠菜单运行的示例")

如果我们从菜单中选择某一自定义项，我们文本视图中的文本将被修改。

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>弹出按钮和下拉列表

弹出按钮显示选定的项，并显示一个列表要被用户单击后从选择的选项。 下拉列表是一种通常用于选择命令特定于当前任务的上下文的弹出按钮。 这两项可以出现任何位置在窗口中。

让我们创建我们的应用程序的自定义弹出按钮通过执行以下操作：

1. 编辑**Main.storyboard**中 Xcode 和拖动文件**弹出窗口按钮**从**库检查器**到**面板**我们在中创建的窗口[上下文菜单](#Contextual_Menus)部分： 

    [![添加弹出项按钮](menu-images/popup01.png "添加弹出项按钮")](menu-images/popup01-large.png#lightbox)
2. 添加新的菜单项，并在弹出窗口中为设置项的标题：**地址**，**日期**，**问候**，和**签名** 

    [![配置菜单项](menu-images/popup02.png "配置菜单项")](menu-images/popup02-large.png#lightbox)
3. 接下来，让我们将我们新的菜单项连接到为我们自定义菜单内创建的现有操作[添加、 编辑和删除菜单](#Adding,_Editing_and_Deleting_Menus)上面一节。 切换到**连接检查器**和选择**第一个响应方**中**接口层次结构**。 向下滚动并查找`phraseAddress:`操作。 将行从圆拖到该操作**地址**菜单项： 

    [![拖动到操作连接](menu-images/popup03.png "拖动到连接操作")](menu-images/popup03-large.png#lightbox)
4. 附加到其相应的操作的其他菜单项的所有重复： 

    [![所有必需的操作](menu-images/popup04.png "所有必需的操作")](menu-images/popup04-large.png#lightbox)
5. 保存所做的更改并切换回 Visual Studio for Mac 与 Xcode 同步。

现在如果我们运行我们的应用程序，从弹出窗口中选择一个项，将更改我们文本视图中的文本：

![运行弹出窗口的示例](menu-images/popup05.png "的运行弹出窗口示例")

你可以创建和使用的下拉列表中为弹出按钮完全相同的方式。 而不是将附加到现有操作，你可以创建你自己的自定义操作，只需像我们在我们上下文菜单[上下文菜单](#Contextual_Menus)部分。

## <a name="summary"></a>总结

本文已了解使用菜单和菜单项在 Xamarin.Mac 应用程序的详细的信息。 首先我们检查应用程序的菜单栏，然后我们看创建上下文菜单，接下来，我们探讨了状态栏菜单并自定义停靠菜单。 最后，我们介绍弹出菜单和下拉列表。


## <a name="related-links"></a>相关链接

- [MacMenus （示例）](https://developer.xamarin.com/samples/mac/MacMenus/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [人机接口指南-菜单](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [应用程序菜单和弹出列表简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
