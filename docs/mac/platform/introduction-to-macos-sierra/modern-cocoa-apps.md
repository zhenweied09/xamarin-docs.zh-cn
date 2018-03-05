---
title: "生成现代 macOS 应用"
description: "本文介绍几个提示、 功能和技术开发人员可以使用生成在 Xamarin.Mac 现代 macOS 应用程序。"
ms.topic: article
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 9073d64c43c6817b45dca02b870fcfe093ebf46d
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="building-modern-macos-apps"></a>生成现代 macOS 应用

_本文介绍几个提示、 功能和技术开发人员可以使用生成在 Xamarin.Mac 现代 macOS 应用程序。_

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>生成现代看起来与现代视图

现代的外观将包括现代的窗口和工具栏外观例如，如下所示的示例应用程序：

[ ![](modern-cocoa-apps-images/content08.png "下面举例说明现代的 Mac 应用程序 UI")](modern-cocoa-apps-images/content08.png)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>启用完整大小内容视图

若要实现此值看上去 Xamarin.Mac 应用中的，开发人员将想要使用_完整大小内容视图_，这意味着在工具和标题栏区域下扩展内容和将通过 macOS 自动模糊。

若要启用此功能在代码中的，创建的自定义类`NSWindowController`并使其如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Set window to use Full Size Content View
            Window.StyleMask = NSWindowStyle.FullSizeContentView;
        }
        #endregion
    }
}
```

此功能也在 Xcode 的接口生成器通过选择窗口并检查启用**完整大小的内容视图**:

[ ![](modern-cocoa-apps-images/content01.png "编辑在 Xcode 的接口生成器主要情节提要")](modern-cocoa-apps-images/content01.png)

当使用完整的大小内容视图，开发人员可能需要偏移下的标题和工具栏区域的内容，以便特定内容 （如标签） 不滑动下它们。

若要使此问题变得复杂的标题和工具栏区域可以具有动态高度基于用户当前正在执行，该操作的用户在已安装的 macOS 和/或 Mac 硬件上运行应用程序的版本。

因此，将无法工作时对用户界面的只是硬编码偏移量。 开发人员将需要采用动态方法。

包含的 Apple[键值对的可观察](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes)`ContentLayoutRect`属性`NSWindow`类，以获取在代码中的当前内容区域。 开发人员可以使用此值时内容区域更改手动位置所需的元素。

更好的解决方案是使用自动布局和大小类以放置在代码或接口生成器中的 UI 元素。

如下例所示的代码可以用于将用户界面元素在应用程序的视图控制器中使用自动布局和大小类：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        #region Computed Properties
        public NSLayoutConstraint topConstraint { get; set; }
        #endregion

        ...

        #region Override Methods
        public override void UpdateViewConstraints ()
        {
            // Has the constraint already been set?
            if (topConstraint == null) {
                // Get the top anchor point
                var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
                var topAnchor = contentLayoutGuide.TopAnchor;

                // Found?
                if (topAnchor != null) {
                    // Assemble constraint and activate it
                    topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
                    topConstraint.Active = true;
                }
            }

            base.UpdateViewConstraints ();
        }
        #endregion
    }
}
```

此代码创建一个存储区顶部的约束将应用于一个标签 (`ItemTitle`) 以确保它不会单标题和工具栏区域下：

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

通过重写视图控制器`UpdateViewConstraints`方法，开发人员可以进行测试以查看是否已生成所需的约束，如果需要创建它。

如果需要生成，新的约束`ContentLayoutGuide`窗口访问需要要约束的控件并将其转换为属性`NSLayoutGuide`:

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

TopAnchor 属性`NSLayoutGuide`访问，并且如果可用，它用于生成新的约束具有所需的偏移量和新约束进行活动状态，以将其应用：

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>启用简化的工具栏

正常 macOS 窗口包含到窗口的上边缘的标题栏会在运行一个标准。 如果该窗口还包括一个工具条，则它将显示此标题栏区域下：

[ ![](modern-cocoa-apps-images/content02.png "一个标准的 Mac 工具栏")](modern-cocoa-apps-images/content02.png)

当使用简化的工具栏，标题区消失，工具栏移到标题栏的位置时，以串联的窗口关闭、 最小化和最大化按钮：

[ ![](modern-cocoa-apps-images/content03.png "简化的 Mac 工具栏")](modern-cocoa-apps-images/content03.png)

简化工具栏通过重写`ViewWillAppear`方法`NSViewController`和使其看起来类似于以下：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

这种效果通常用于_鞋盒应用程序_（一个窗口应用），如图、 日历、 说明和系统首选项。 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>使用附件视图控制器

根据应用程序的设计，开发人员可能还想要补充恰好标题/工具栏区域，以提供上下文相关控件添加到用户的活动上基于它们的下方显示的附件视图控制器的区域是标题栏当前参与：

[ ![](modern-cocoa-apps-images/content04.png "示例附件视图控制器")](modern-cocoa-apps-images/content04.png)

将自动模糊附件视图控制器，并为其无需开发人员干预系统调整大小。

若要添加附件视图控制器，请执行以下操作：

1. 在“解决方案资源管理器”中，双击 `Main.storyboard` 文件，将其打开进行编辑。
2. 拖动**自定义视图控制器**到窗口的层次结构中： 

    [ ![](modern-cocoa-apps-images/content05.png "添加新的自定义视图控制器")](modern-cocoa-apps-images/content05.png)
3. 布局附件视图 UI: 

    [ ![](modern-cocoa-apps-images/content06.png "设计新视图")](modern-cocoa-apps-images/content06.png)
4. 公开附件视图作为**Outlet**和任何其他**操作**或**插座**有关其 UI: 

    [ ![](modern-cocoa-apps-images/content07.png "添加所需的电源插座")](modern-cocoa-apps-images/content07.png)
5. 保存更改。
6. 返回到 Visual Studio for Mac 以同步更改。

编辑`NSWindowController`并使其如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }
        #endregion
    }
}
```

此代码的关键点是其中视图设置为自定义视图，它已在接口生成器中定义并公开为**Outlet**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

与`LayoutAttribute`定义_其中_访问器将显示：

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

因为 macOS 现在已完全本地化，`Left`和`Right``NSLayoutAttribute`属性已弃用，应使用替换`Leading`和`Trailing`。

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>使用选项卡式的窗口

此外，macOS 系统可能将附件视图控制器添加到应用程序的窗口中。 例如，若要创建多个应用程序的 Windows 将合并到一个虚拟窗口选项卡式窗口：

[ ![](modern-cocoa-apps-images/content08.png "下面举例说明选项卡式 Mac 窗口")](modern-cocoa-apps-images/content08.png)

通常情况下，开发人员将需要采取有限的操作使用其 Xamarin.Mac 应用中的选项卡式窗口，系统将自动按以下方式处理它们：

- Windows 将自动为选项卡式时`OrderFront`调用方法。
- Windows 将自动为 Untabbed 时`OrderOut`调用方法。
- 在代码中所有选项卡式窗口仍被视为"可见"，但是任何非前面选项卡是隐藏的使用 CoreGraphics 的系统。
- 使用`TabbingIdentifier`属性`NSWindow`到组合在一起成为选项卡的组窗口。
- 如果它是`NSDocument`基于应用程序，其中有些功能将不需要任何开发人员操作启用自动 （如正在添加到选项卡栏的加号按钮）。
- 非`NSDocument`基于应用程序可以启用要通过重写中添加新的文档的选项卡组中的"+"按钮`GetNewWindowForTab`方法`NSWindowsController`。

将所有资源片段集合在一起，`AppDelegate`想要使用的应用程序基于系统选项卡式窗口可能如下所示：

```csharp
using AppKit;
using Foundation;

namespace MacModern
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewDocumentNumber { get; set; } = 0;
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

        #region Custom Actions
        [Export ("newDocument:")]
        public void NewDocument (NSObject sender)
        {
            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow (this);
        } 
        #endregion
    }
}
```

其中`NewDocumentNumber`属性将跟踪的新创建的文档数和`NewDocument`方法创建一个新文档，并将其显示。

`NSWindowController`然后如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Application Access
        /// <summary>
        /// A helper shortcut to the app delegate.
        /// </summary>
        /// <value>The app.</value>
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void SetDefaultDocumentTitle ()
        {
            // Is this the first document?
            if (App.NewDocumentNumber == 0) {
                // Yes, set title and increment
                Window.Title = "Untitled";
                ++App.NewDocumentNumber;
            } else {
                // No, show title and count
                Window.Title = $"Untitled {App.NewDocumentNumber++}";
            }
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Prefer Tabbed Windows
            Window.TabbingMode = NSWindowTabbingMode.Preferred;
            Window.TabbingIdentifier = "Main";

            // Set default window title
            SetDefaultDocumentTitle ();

            // Set window to use Full Size Content View
            // Window.StyleMask = NSWindowStyle.FullSizeContentView;

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }

        public override void GetNewWindowForTab (NSObject sender)
        {
            // Ask app to open a new document window
            App.NewDocument (this);
        }
        #endregion
    }
}
```

其中静态`App`属性提供的快捷方式可用于访问`AppDelegate`。 `SetDefaultDocumentTitle`方法设置的新创建的文档数所基于的新文档标题。

下面的代码中，告知 macOS 应用倾向于使用选项卡，并提供一个字符串，允许应用程序的 Windows 来划分为选项卡：

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

然后下面的重写方法将加号按钮添加到将创建新文档时被用户单击选项卡栏：

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>使用核心动画

核心动画，则内置 macOS 高功率的图形呈现引擎。 核心动画已经过优化，以利用现代 macOS 硬件，而不是运行上的 CPU，可能会降低计算机的图形操作中可用 GPU （图形处理单元）。

`CALayer`、 提供的核心动画，可以使用快速、 流畅滚动和动画之类的任务。 应用程序的用户界面应由多个子视图和层，若要充分利用核心动画组成。

A`CALayer`对象提供允许开发人员可以控制呈现的内容使用如向用户在屏幕上的多个属性：

- `Content` -可以是`NSImage`或`CGImage`提供层的内容。
- `BackgroundColor` -设置为层的背景色 `CGColor`
- `BorderWidth` -设置边框宽度。
- `BorderColor` -设置的边框颜色。

若要使用应用程序的 UI 中的核心图形，它必须使用_层支持_Apple 提供的建议开发人员应始终启用窗口的内容视图中的视图。 这样一来，所有子视图将自动都继承层备份以及。

Apple 提供的此外，建议使用而不添加新的层支持视图`CALayer`作为子层因为系统将自动处理多个所需的设置 （如所需要的 Retina 显示的那些）。

可以通过设置启用层备份`WantsLayer`的`NSView`到`true`在 Xcode 的接口生成器的内部或**视图效果检查器**通过检查**核心动画层**:

[ ![](modern-cocoa-apps-images/content09.png "该视图效果检查器")](modern-cocoa-apps-images/content09.png)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>重绘具有层的视图

另一个重要的步骤设置 Xamarin.Mac 应用中使用层支持的视图时`LayerContentsRedrawPolicy`的`NSView`到`OnSetNeedsDisplay`中`NSViewController`。 例如:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

当其帧源发生更改时，如果开发人员不设置此属性，该视图将重绘其不需要为了提高性能。 此属性设置为`OnSetNeedsDisplay`开发人员手动将需要设置`NeedsDisplay`到`true`以强制重绘，但是的内容。

当视图被标记为已更新时，系统将检查`WantsUpdateLayer`在视图的属性。 如果它返回`true`则`UpdateLayer`调用方法，否则`DrawRect`视图的方法调用更新视图的内容。

Apple 具有更新视图内容时所需的以下建议：

- Apple 首选使用`UpdateLater`通过`DrawRect`每当因为它可能提供显著提高了性能。
- 使用相同`layer.Contents`的类似的 UI 元素。
- Apple 还首选开发人员可以编写使用标准视图类似于其 UI `NSTextField`、 再次尽可能。

若要使用`UpdateLayer`，创建一个自定义类，`NSView`和使代码如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView
    {
        #region Computed Properties
        public override bool WantsLayer {
            get { return true; }
        }

        public override bool WantsUpdateLayer {
            get { return true; }
        }
        #endregion

        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DrawRect (CoreGraphics.CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

        }

        public override void UpdateLayer ()
        {
            base.UpdateLayer ();

            // Draw view 
            Layer.BackgroundColor = NSColor.Red.CGColor;
        }
        #endregion
    }
}
```

<a name="Using-Modern-Drag-and-Drop" />

## <a name="using-modern-drag-and-drop"></a>使用现代的拖放

要将呈现的用户的现代拖放体验，开发人员应采用_拖群_其应用中拖放操作。 拖动 Flocking 是数目的作为单个元素 flocks （组一起光标下的项的计数），如用户继续滚动时拖动操作，其中显示每个单独的文件或最初要拖动的项。

如果用户终止拖动操作时，将 Unflock 的各个元素并将其返回到其原始位置。

下面的示例代码使拖动群对自定义视图：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView, INSDraggingSource, INSDraggingDestination
    {
        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void MouseDragged (NSEvent theEvent)
        {
            // Create group of string to be dragged
            var string1 = new NSDraggingItem ((NSString)"Item 1");
            var string2 = new NSDraggingItem ((NSString)"Item 2");
            var string3 = new NSDraggingItem ((NSString)"Item 3");

            // Drag a cluster of items
            BeginDraggingSession (new [] { string1, string2, string3 }, theEvent, this);
        }
        #endregion
    }
}
```

Flocking 效果通过发送拖到每个项来实现`BeginDraggingSession`方法`NSView`作为一个单独的元素数组中。

使用时`NSTableView`或`NSOutlineView`，使用`PastboardWriterForRow`方法`NSTableViewDataSource`类，然后开始拖动操作：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDataSource: NSTableViewDataSource
    {
        #region Constructors
        public ContentsTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override INSPasteboardWriting GetPasteboardWriterForRow (NSTableView tableView, nint row)
        {
            // Return required pasteboard writer
            ...
            
            // Pasteboard writer failed
            return null;
        }
        #endregion
    }
}
```

这允许开发人员提供个人`NSDraggingItem`而不是较旧方法所拖动的表中每一项`WriteRowsWith`，写入的所有行作为单个组粘贴板。

使用时`NSCollectionViews`，再次使用`PasteboardWriterForItemAt`方法而非`WriteItemsAt`方法中拖动时开始。

开发人员应始终避免使粘贴板上的大型文件。 新手 macOS Sierra，_文件承诺_允许开发人员可以将引用为给定的用户完成使用新的 Drop 操作时更高版本将会完成粘贴板上的文件`NSFilePromiseProvider`和`NSFilePromiseReceiver`类。

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>使用现代事件跟踪

用户界面元素 (如`NSButton`) 已添加到标题或工具栏区域中，用户应该能够单击元素，并将其触发正常 （例如，显示一个弹出窗口） 事件。 但是，由于项还在标题或工具栏区域中，用户应该能够单击并拖动要移动窗口也的元素。

若要在代码中实现此目的，创建一个自定义元素 (如`NSButton`) 和重写`MouseDown`事件，如下所示：

```csharp
public override void MouseDown (NSEvent theEvent)
{
    var shouldCallSuper = false;

    Window.TrackEventsMatching (NSEventMask.LeftMouseUp, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Handle event as normal
        stop = true;
        shouldCallSuper = true;
    });

    Window.TrackEventsMatching(NSEventMask.LeftMouseDragged, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Pass drag event to window
        stop = true;
        Window.PerformWindowDrag (evt);
    });

    // Call super to handle mousedown
    if (shouldCallSuper) {
        base.MouseDown (theEvent);
    }
}
```

此代码使用`TrackEventsMatching`方法`NSWindow`UI 元素附加到以截获`LeftMouseUp`和`LeftMouseDragged`事件。 有关`LeftMouseUp`事件，像平时一样进行响应的 UI 元素。 有关`LeftMouseDragged`事件，事件传递到`NSWindow`的`PerformWindowDrag`方法在屏幕上移动该窗口。

调用`PerformWindowDrag`方法`NSWindow`类具有以下优点：

- 它允许窗口后，若要移动，即使在挂起应用程序 (例如，当处理深层循环)。
- 空间切换将按预期方式工作。
- 空间栏将显示为正常。
- 窗口对齐和对齐方式能正常工作。

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>使用现代容器视图控件

macOS Sierra 到以前版本的操作系统中可用的现有容器视图控件提供许多现代的改进。

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>表视图增强功能

开发人员应始终使用新`NSView`如基于容器视图控件版本`NSTableView`。 例如:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }
        #endregion
    }
}
```

这使自定义的表行操作，以便将附加到给定的 （如轻扫权限才能删除该行） 表中的行。 若要启用此行为，重写`RowActions`方法`NSTableViewDelegate`:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }

        public override NSTableViewRowAction [] RowActions (NSTableView tableView, nint row, NSTableRowActionEdge edge)
        {
            // Take action based on the edge
            if (edge == NSTableRowActionEdge.Trailing) {
                // Create row actions
                var editAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Regular, "Edit", (action, rowNum) => {
                    // Handle row being edited
                    ...
                });

                var deleteAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Destructive, "Delete", (action, rowNum) => {
                    // Handle row being deleted
                    ...
                });

                // Return actions
                return new [] { editAction, deleteAction };
            } else {
                // No matching actions
                return null;
            }
        }
        #endregion
    }
}
```

静态`NSTableViewRowAction.FromStyle`用于创建新的表行操作的以下样式：

- `Regular` -执行标准的非破坏性操作，如编辑行的内容。
- `Destructive` -执行破坏性操作例如删除行从表。 这些操作将呈现包含一个红色背景。

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>滚动视图增强功能 

使用滚动视图时 (`NSScrollView`) 直接，或另一个控件的一部分 (如`NSTableView`)，在使用现代的外观和视图的 Xamarin.Mac 应用程序的标题和工具栏区域下可以滑动滚动视图的内容。

因此，可以按标题和工具栏区域部分遮盖滚动视图内容区域中的第一项。

若要更正此问题，Apple 已添加到两个新属性`NSScrollView`类：

- `ContentInsets` -允许开发人员提供`NSEdgeInsets`对象定义将应用到滚动视图顶部的偏移量。
- `AutomaticallyAdjustsContentInsets` -如果`true`滚动视图将自动处理`ContentInsets`使开发人员。

通过使用`ContentInsets`开发人员可以调整要允许的附件包含如的滚动视图开始：

- 排序指示器，如邮件应用中所示。
- 搜索字段中。
- 一个刷新或更新的按钮。

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>自动布局和现代应用的本地化

Apple 在 Xcode 中允许开发人员可以轻松地创建国际化的 macOS 应用中包含多种技术。 Xcode 现在允许开发人员从其情节提要文件中的应用程序的用户界面设计分隔面向用户的文本，并提供工具以在 UI 更改时维护这种分离。

有关详细信息，请参阅 Apple 的[国际化和本地化指南](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)。

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>实现基国际化 

通过实现基本国际化，开发人员可以提供一个单一的情节提要文件来表示应用程序的 UI 和分离出所有面向用户的字符串。 

当开发人员正在创建的初始情节提要文件 （或文件），用于定义应用程序的用户界面，它们将生成基本国际化 （开发人员使用的语言）。

接下来，开发人员可以导出本地化信息和基本国际化 （的情节提要 UI 设计） 中字符串可以转换为多种语言。

更高版本，可以导入这些本地化信息和 Xcode 将情节提要为生成特定于语言的字符串的文件。

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>实现自动布局以支持本地化

由于本地化版本的字符串值可具有完全不同的大小，和/或读取方向，开发人员应使用自动布局位置和大小在情节提要文件的应用程序的用户界面。

Apple 提供建议执行以下操作：

- **删除固定宽度约束**-所有基于文本的视图应允许调整大小基于其内容。 固定宽度视图可能裁剪特定语言中的其内容。
- **使用内部函数的内容大小**-由默认基于文本的视图将自动调整大小以适应其内容。 不会正确调整大小的基于文本的视图，在 Xcode 的接口生成器中选择它们，然后选择**编辑** > **大小为适合内容**。
- **应用前导和尾随属性**-因为文本的方向可以更改基于用户的语言，使用新`Leading`和`Trailing`约束属性，而不是现有`Right`和`Left`属性。 `Leading` 和`Trailing`将自动调整根据语言方向。
- **到相邻的视图的 pin 视图**-这样，要重新定位并调整其大小如围绕它们的视图更改为所选的语言的响应中的视图。
- **未设置的 Windows 最小值和/或最大大小**-允许 Windows，若要更改大小随着所选的语言重设其内容的区域。
- **布局不断地更改测试**内应该在不同语言中不断测试应用程序开发。 请参阅 Apple 的[测试您国际化应用](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1)更多详细信息的文档。
- **使用到一起 Pin 视图 NSStackViews**  -  `NSStackViews`允许它们的内容以便按住 shift 并以可预测方式增长和内容更改基于所选的语言的大小。

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>在 Xcode 的接口生成器本地化

Apple 提供 Xcode 的接口生成器中的一些功能，开发人员可用于在设计或编辑应用程序的 UI 时支持的本地化。 **文本方向**部分**属性检查器**允许开发人员提供有关如何使用和更新在选择基于文本的视图方向的提示 (如`NSTextField`):

[ ![](modern-cocoa-apps-images/content10.png "文本方向选项")](modern-cocoa-apps-images/content10.png)

有三个可能值**文本方向**:

- **自然**-布局基于分配给控件的字符串。
- **从左到右**-布局始终强制为从左到右。
- **从右到左**-布局始终强制设为从右向左。

有两个可能值**布局**:

- **从左到右**-布局始终从左到右。
- **从右到左**-的布局是始终从右到左。

通常这些不应更改除非特定的一致性是必需的。

**镜像**属性会通知系统 （例如单元格映像位置） 翻转特定的控件属性。 有三个可能值：

- **自动**-的位置将自动更改基于所选的语言的方向。
- **在右到左界面**-仅将更改从右到左的基于语言中的位置。
- **永远不会**-的位置将永远不会更改。

如果开发人员指定**Center**，**两端对齐**或**完整**的内容的基于文本的视图的对齐方式，这些都不会选择的翻转根据的语言。

在 macOS Sierra 之前, 在代码中创建的控件将不能镜像自动。 开发人员必须使用类似于下面的代码来处理镜像：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Setting a button's mirroring based on the layout direction
    var button = new NSButton ();
    if (button.UserInterfaceLayoutDirection == NSUserInterfaceLayoutDirection.LeftToRight) {
        button.Alignment = NSTextAlignment.Right;
        button.ImagePosition = NSCellImagePosition.ImageLeft;
    } else {
        button.Alignment = NSTextAlignment.Left;
        button.ImagePosition = NSCellImagePosition.ImageRight;
    }
}
```

其中`Alignment`和`ImagePosition`正在设置基于`UserInterfaceLayoutDirection`的控件。

macOS Sierra 添加多个新的方便构造函数 (通过静态`CreateButton`方法) 采用多个参数 （例如标题、 映像和操作） 并自动将正确镜像。 例如:

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>使用系统外观

现代 macOS 应用可以采用非常适用于映像创建、 编辑或演示文稿应用新深色接口外观：

[ ![](modern-cocoa-apps-images/content11.png "下面举例说明深色 Mac 窗口 UI")](modern-cocoa-apps-images/content11.png)

这可以通过添加一行代码之前显示窗口。 例如:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        ...
    
        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Apply the Dark Interface Appearance
            View.Window.Appearance = NSAppearance.GetAppearance (NSAppearance.NameVibrantDark);

            ...
        }
        #endregion
    }
}
```

静态`GetAppearance`方法`NSAppearance`类用于从系统获取命名的外观 (在这种情况下`NSAppearance.NameVibrantDark`)。

Apple 具有使用系统出现的以下建议：

- 更愿意已命名的颜色，而包含硬编码值 (如`LabelColor`和`SelectedControlColor`)。
- 请尽可能使用系统标准控件样式。

对于已启用从系统首选项应用的辅助功能的用户，使用系统外观的 macOS 应用将自动正常工作。 因此，Apple 提供的建议，开发人员始终应在其 macOS 应用中使用系统外观。

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>使用情节提要设计用户界面

情节提要允许开发人员不仅使启动应用程序的用户界面，但若要实现可视化效果并设计用户界面的各个元素流的设计和给定元素的层次结构。

控制器允许开发人员可以将元素收集到一组组合以及 Segues 抽象删除典型"粘附代码"移动整个查看层次结构需要：

[ ![](modern-cocoa-apps-images/content12.png "编辑 Xcode 的接口生成器中的用户界面")](modern-cocoa-apps-images/content12.png)

有关详细信息，请参阅我们[简介情节提要](~/mac/platform/storyboards/index.md)文档。

有多个实例定义在情节提要中的给定的场景需要从以前场景视图层次结构中的数据。 Apple 具有的场景之间传递信息的以下建议：

- 在层次结构中，应始终向下层叠数据相关性。
- 避免硬编码的 UI 结构依赖事项，因为这就限制了 UI 灵活性。
- 使用 C# 接口来提供一般数据相关性。

充当 Segue，源视图控制器可以重写`PrepareForSegue`方法并执行前 Segue （如传递数据） 所需的任何初始化执行，以显示目标视图控制器。 例如:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

有关详细信息，请参阅我们[Segues](~/mac/platform/storyboards/indepth.md#Segues)文档。

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>传播操作

根据 macOS 应用程序的设计，可能存在时的最佳处理程序的 UI 控件上的某个操作可能在其他位置 UI 层次结构中的时间。 这种情况的菜单和位于其自己的场景中，独立于应用程序的用户界面的其余部分的菜单项。

若要处理这种情况下，开发人员可以创建自定义操作并将传递响应方链向上的操作。 有关详细信息请参阅我们[使用自定义窗口操作](~/mac/user-interface/menu.md)文档。

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>现代 Mac 功能

Apple 在 macOS Sierra 允许开发人员可以充分利用 Mac 平台，如中包含多个面向用户的功能：

- **NSUserActivity** -这样，应用程序以描述用户当前参与的活动。 `NSUserActivity` 最初创建以支持的 HandOff，无法选取而在另一台设备上继续执行启动其中一个用户的设备上的活动。 `NSUserActivity` 在 iOS 中的中作为它的 macOS 相同作用的工作原理，因此请请参阅我们[简介 Handoff](~/ios/platform/handoff.md) iOS 文档的更多详细信息。
- **在 Mac 上的使用 Siri** -Siri 使用当前的活动 (`NSUserActivity`) 提供的用户可以发出的命令的上下文。
- **状态还原**-当用户退出应用程序在 macOS 上并且以后 relaunches 它，该应用将自动返回到以前的状态。 开发人员可以使用状态还原 API 进行编码，并将还原之前向用户显示用户界面的暂时性 UI 状态。 如果在应用程序处于`NSDocument`基于，还原状态会自动处理。 若要启用的状态还原非`NSDocument`基于应用程序，设置`Restorable`的`NSWindow`类到`true`。
- **在云中的文档**-在 macOS Sierra 之前, 必须显式选择性加入到在用户的 iCloud 驱动器中使用文档应用程序。 在 macOS Sierra 用户**桌面**和**文档**文件夹可能会与同步其 iCloud 驱动器自动由系统。 因此，可能会删除文档的本地副本以释放空间用户的计算机上。 `NSDocument` 基于应用程序将自动处理此更改。 所有其他应用程序类型将需要使用`NSFileCoordinator`同步读取和写入的文档。

<a name="Summary" />

## <a name="summary"></a>摘要

本文已覆盖多个提示、 功能和技术开发人员可以使用生成在 Xamarin.Mac 现代 macOS 应用程序。



## <a name="related-links"></a>相关链接

- [macOS 示例](https://developer.xamarin.com/samples/mac/)
