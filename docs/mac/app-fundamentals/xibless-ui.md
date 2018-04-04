---
title: .storyboard/.xib-less 用户界面设计
description: 本文介绍如何直接从 C# 代码，而无需.storyboard 文件、.xib 文件或接口生成器创建 Xamarin.Mac 应用程序的用户界面。
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 66725b02d3e351e74fa79ae5336a7db3a9f2b534
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="storyboardxib-less-user-interface-design"></a>.storyboard/.xib-less 用户界面设计

_本文介绍如何直接从 C# 代码，而无需.storyboard 文件、.xib 文件或接口生成器创建 Xamarin.Mac 应用程序的用户界面。_


## <a name="overview"></a>概述

在使用 C# 和.NET Xamarin.Mac 应用程序中，有权访问相同的用户界面元素和工具的开发人员*Objective C*和*Xcode*未。 通常，当创建 Xamarin.Mac 应用程序，你将使用 Xcode 的接口生成器.storyboard 或.xib 文件可以创建和维护应用程序的用户界面。

此外可以直接在 C# 代码中创建部分或全部 Xamarin.Mac 应用程序的 UI。 在本文中，我们将介绍在 C# 代码中创建用户界面和用户界面元素的基础知识。

[![Visual Studio for Mac 代码编辑器](xibless-ui-images/intro01.png "Visual Studio for Mac 代码编辑器")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>若要使用代码窗口切换

时创建新的 Xamarin.Mac Cocoa 应用程序时，默认情况下将获得标准保留为空，窗口。 在中定义此 windows **Main.storyboard** (或传统上**MainWindow.xib**) 自动包括在项目中的文件。 这还包括**ViewController.cs**管理应用程序的主视图的文件 (或试传统上**了构成**和**MainWindowController.cs**文件)。

若要切换到应用程序的 Xibless 窗口，请执行以下操作：

1. 打开你想要停止使用的应用程序`.stroyboard`或 for mac。 在 Visual Studio 中定义的用户界面的.xib 文件
2. 在**解决方案 Pad**，右键单击**Main.storyboard**或**MainWindow.xib**文件，然后选择**删除**: 

    ![删除的主要 storyboard 或窗口](xibless-ui-images/switch01.png "删除的主要 storyboard 或窗口")
3. 从**删除对话框**，单击**删除**按钮可从项目中完全删除.storyboard 或.xib: 

    ![确认删除](xibless-ui-images/switch02.png "确认删除")

现在我们将需要修改**了构成**文件定义窗口布局和修改**ViewController.cs**或**MainWindowController.cs**文件以创建实例我们`MainWindow`类因为我们要不再使用的.storyboard 或.xib 文件。

使用情节提要的自己的用户界面可能会自动包括的现代 Xamarin.Mac 应用**了构成**， **ViewController.cs**或**MainWindowController.cs**文件。 根据需要，只需将添加新的空 C# 类到项目 (**添加** > **新文件...**  > **常规** > **空类**) 并将其命名为缺少的文件相同。 


### <a name="defining-the-window-in-code"></a>在代码中定义窗口

接下来，编辑**了构成**文件并使其如下所示：

```csharp
using System;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindow : NSWindow
    {
        #region Private Variables
        private int NumberOfTimesClicked = 0;
        #endregion

        #region Computed Properties
        public NSButton ClickMeButton { get; set;}
        public NSTextField ClickMeLabel { get ; set;}
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }

        public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
            // Define the user interface of the window here
            Title = "Window From Code";

            // Create the content view for the window and make it fill the window
            ContentView = new NSView (Frame);

            // Add UI elements to window
            ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
                AutoresizingMask = NSViewResizingMask.MinYMargin
            };
            ContentView.AddSubview (ClickMeButton);

            ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
                BackgroundColor = NSColor.Clear,
                TextColor = NSColor.Black,
                Editable = false,
                Bezeled = false,
                AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
                StringValue = "Button has not been clicked yet."
            };
            ContentView.AddSubview (ClickMeLabel);
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Wireup events
            ClickMeButton.Activated += (sender, e) => {
                // Update count
                ClickMeLabel.StringValue = (++NumberOfTimesClicked == 1) ? "Button clicked one time." : string.Format("Button clicked {0} times.",NumberOfTimesClicked);
            };
        }
        #endregion

    }
}
```

让我们讨论几个关键元素。

首先，我们添加一些_计算属性_将充当容器 （就像该窗口.storyboard 或.xib 文件中创建的）：

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

这些将使得我们能够访问我们将在窗口中显示的 UI 元素。 由于从.storyboard 或.xib 文件未被放大窗口，我们需要其进行实例化一种方法 (正如我们将更高版本中看到`MainWindowController`类)。 这就是此新的构造函数方法的作用：

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

这是我们将在其中设计窗口的布局并将创建所需的用户界面所需的任何 UI 元素。 我们可以将任何 UI 元素添加到窗口之前，它需要_内容视图_以便包含的元素：

```csharp
ContentView = new NSView (Frame);
```

这将创建一个将填满窗口的内容视图。 现在我们添加我们的第一个用户界面元素， `NSButton`，到窗口：

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

首先要注意的一点是，与 iOS，不同 macOS 使用数学表示法来定义其窗口的坐标系统。 因此原点是在窗口中，使用值增加窗口的右上角的右部和左下角。 当我们创建新`NSButton`，我们考虑到这种我们在屏幕上定义其位置和大小。

`AutoresizingMask = NSViewResizingMask.MinYMargin`属性会告知按钮我们希望其调整窗口的大小时垂直保留在从窗口顶部的相同位置。 同样，这是必需，因为 (0，0) 位于左下角的窗口。

最后，`ContentView.AddSubview (ClickMeButton)`方法将添加`NSButton`到的内容视图，因此它将显示在屏幕上，应用程序时运行和显示窗口。

接下来将标签添加到的窗口中将显示的次数，`NSButton`单击后： 

```csharp
ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
    BackgroundColor = NSColor.Clear,
    TextColor = NSColor.Black,
    Editable = false,
    Bezeled = false,
    AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
    StringValue = "Button has not been clicked yet."
};
ContentView.AddSubview (ClickMeLabel);
``` 

由于 macOS 不包含特定_标签_的 UI 元素，我们添加了特殊样式非可编辑`NSTextField`充当标签。 就像按钮之前，大小和位置会考虑该 (0，0) 位于左下角的窗口。 `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin`属性使用**或**运算符以组合两个`NSViewResizingMask`功能。 这将使调整窗口的大小时垂直保留在从窗口顶部的相同位置和收缩并调整窗口的大小时水平宽度增长的标签。

同样，`ContentView.AddSubview (ClickMeLabel)`方法将添加`NSTextField`到的内容视图，因此它将显示在屏幕上，运行应用程序和窗口打开时。


### <a name="adjusting-the-window-controller"></a>调整窗口控制器

由于的设计`MainWindow`不再加载从.storyboard 或.xib 文件，我们将需要进行一些调整到窗口控制器。 编辑**MainWindowController.cs**文件并使其如下所示：

```csharp
using System;

using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindowController : NSWindowController
    {
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindowController (NSCoder coder) : base (coder)
        {
        }

        public MainWindowController () : base ("MainWindow")
        {
            // Construct the window from code here
            CGRect contentRect = new CGRect (0, 0, 1000, 500);
            base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);

            // Simulate Awaking from Nib
            Window.AwakeFromNib ();
        }

        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }

        public new MainWindow Window {
            get { return (MainWindow)base.Window; }
        }
    }
}

```

让讨论此修改的主要元素。

首先，我们定义的新实例`MainWindow`类并将其分配给基窗口控制器`Window`属性：

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

我们定义了的屏幕窗口的位置`CGRect`。 就像窗口的坐标系统，屏幕左下角以定义 (0，0)。 接下来，我们使用定义的窗口样式**或者**运算符合并两个或多`NSWindowStyle`功能：

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
``` 

以下`NSWindowStyle`，均可使用：

- **边框**-窗口将具有无边框。
- **标题为**-窗口将具有标题栏。
- **Closable** -有关闭按钮，关闭此窗口。
- **Miniaturizable** -窗口具有 Miniaturize 按钮，可以最小化。
- **可调整大小**-窗口将具有调整大小的按钮，并可以调整大小。
- **实用工具**-窗口是一个实用工具样式窗口 （面板）。
- **DocModal** -如果该窗口是一个面板，它将模式而不是系统模式上的文档。 
- **NonactivatingPanel** -如果该窗口是一个面板，它将不会执行的主窗口。
- **TexturedBackground** -窗口将具有带纹理的背景。
- **无比例**-不会扩展窗口。
- **UnifiedTitleAndToolbar** -将加入窗口的标题和工具栏区域。
- **Hud** -窗口将显示为警告显示面板。
- **FullScreenWindow** -窗口可以输入全屏显示模式。
- **FullSizeContentView** -窗口的内容视图位于标题和工具栏区域。

最后两个属性定义_缓冲类型_窗口和如果将延迟的窗口的绘制。 有关详细信息`NSWindows`，请参阅 Apple 的[简介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)文档。

最后，由于从.storyboard 或.xib 文件未被放大窗口，我们需要模拟在我们**MainWindowController.cs**通过调用 windows`AwakeFromNib`方法：

```csharp
Window.AwakeFromNib ();
```

这将允许你针对只是窗口的代码到喜欢从.storyboard 或.xib 文件加载一个标准窗口。


### <a name="displaying-the-window"></a>显示窗口

.Storyboard 或.xib 文件删除和**了构成**和**MainWindowController.cs**文件修改，你将使用窗口就像你必须已在中创建任何正常窗口Xcode 的.xib 文件与接口生成器。

以下将创建的窗口和其控制器的新实例并在屏幕上显示窗口：

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

此时，如果运行应用程序并且几次单击该按钮时，以下将显示：

![运行示例应用程序](xibless-ui-images/run01.png "运行示例应用程序")


## <a name="adding-a-code-only-window"></a>添加代码仅窗口

如果我们想要添加到现有 Xamarin.Mac 应用程序的代码仅、 xibless 窗口，请右键单击中的项目**解决方案 Pad**和选择**添加** > **新文件...**.在**新文件**对话框中，选择**Xamarin.Mac** > **Cocoa 窗口与控制器**，如下所示：

![添加新的窗口控制器](xibless-ui-images/add01.png "添加新的窗口控制器") 

就像之前，我们将从项目中删除默认.storyboard 或.xib 文件 (在这种情况下**SecondWindow.xib**) 并按照中的步骤[切换要使用代码的窗口](#Switching_a_Window_to_use_Code)上面介绍部分到代码窗口的定义。


## <a name="adding-a-ui-element-to-a-window-in-code"></a>将 UI 元素添加到代码中的窗口

是否在代码中创建或从.storyboard 或.xib 文件加载一个窗口，可能有时间我们想要添加到窗口的代码中的 UI 元素。 例如：

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

上面的代码创建一个新`NSButton`和将其添加到`MyWindow`显示的窗口实例。 基本上可以在代码中创建可以在 Xcode 的接口生成器.storyboard 或.xib 文件中定义的任何 UI 元素并将其显示在窗口中。


## <a name="defining-the-menu-bar-in-code"></a>在代码中定义的菜单栏

由于 Xamarin.Mac 中的当前限制，不建议你创建的 Xamarin.Mac 应用程序的菜单栏 –`NSMenuBar`– 中的代码，但继续使用**Main.storyboard**或**MainMenu.xib**文件以定义它。 也就是说，你可以添加和删除在 C# 代码中的菜单和菜单项。

例如，编辑**AppDelegate.cs**文件并进行`DidFinishLaunching`方法外观如下所示：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    // Create a Status Bar Menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Phrases";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Phrases");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        Console.WriteLine("Address Selected");
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        Console.WriteLine("Date Selected");
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        Console.WriteLine("Greetings Selected");
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        Console.WriteLine("Signature Selected");
    };
    item.Menu.AddItem (signature);
}
```

上述代码中创建一个状态栏菜单和应用程序启动时显示它。 有关使用菜单的详细信息，请参阅我们[菜单](~/mac/user-interface/menu.md)文档。


## <a name="summary"></a>总结

本文已了解在 C# 代码而不是与.storyboard 或.xib 文件使用 Xcode 的接口生成器中创建 Xamarin.Mac 应用程序的用户界面的详细的信息。



## <a name="related-links"></a>相关链接

- [MacXibless （示例）](https://developer.xamarin.com/samples/mac/MacXibless/)
- [Windows](~/mac/user-interface/window.md)
- [菜单](~/mac/user-interface/menu.md)
- [macOS 人工界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Windows 简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
