---
title: 使用情节提要中 Xamarin.Mac
description: 本文档介绍如何使用 Xamarin.Mac，检查如何将它们加载从代码、 视图控制器生命周期、 响应方链中的情节提要，segue，窗口控制器、 手势识别器和的详细信息。
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 72986ed4247c3b6f66f6f1813d74bf0a95d0de53
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792835"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>使用情节提要中 Xamarin.Mac

情节提要定义所有分解为其查看控制器的功能概述给定应用程序的 UI。 在 Xcode 的接口生成器中，每个这些控制器居住在自己的场景。

[![](indepth-images/intro01.png "在 Xcode 的接口生成器情节提要")](indepth-images/intro01.png#lightbox)

情节提要是一个资源文件 (扩展名为`.storyboard`) 编译和发运时，获取包含在 Xamarin.Mac 应用捆绑包。 若要定义您的应用程序起始情节提要，对其进行编辑的`Info.plist`文件，然后选择**主界面**从下拉列表框中： 

[![](indepth-images/sb01.png "Info.plist 编辑器")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>从代码加载

可能需要从代码加载特定的情节提要并手动创建视图控制器的时间。 下面的代码可用于执行此操作：

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

`FromName`加载具有已包括在应用的捆绑包的给定名称的情节提要文件。 `InstantiateControllerWithIdentifier`与给定标识创建的视图控制器实例。 设计用户界面时，可以在 Xcode 的接口生成器中设置标识：

[![](indepth-images/sb02.png "设置情节提要 ID。")](indepth-images/sb02.png#lightbox)

或者，你可以使用`InstantiateInitialController`方法以加载已分配接口生成器中的初始控制器视图控制器：

[![](indepth-images/sb03.png "设置初始控制器")](indepth-images/sb03.png#lightbox)

标记的**情节提要入口点**和上面的打开结束箭头。

<a name="View-Controllers" />

## <a name="view-controllers"></a>视图控制器

查看控制器定义给定的 Mac 应用中的信息的视图和数据模型，它提供该信息之间的关系。 情节提要中的每个顶级场景表示 Xamarin.Mac 应用代码中的一个视图控制器。

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>视图控制器生命周期

几种新方法已添加到`NSViewController`类，以支持在 macOS 情节提要。 最重要的是将以下方法用于响应由给定的视图控制器受控的视图的生命周期：

- `ViewDidLoad` -从情节提要文件加载视图时，调用此方法。
- `ViewWillAppear` -在屏幕上显示的视图之前调用此方法。
- `ViewDidAppear` -已在屏幕上显示的视图后，将直接调用此方法。
- `ViewWillDisappear` -视图从屏幕上删除之前调用此方法。
- `ViewDidDisappear` -已从屏幕中删除该视图后，将直接调用此方法。
- `UpdateViewConstraints` -定义视图的约束自动布局的位置和大小需要更新时，被调用此方法。
- `ViewWillLayout` -在屏幕上的此视图在子视图布局之前调用此方法。
- `ViewDidLayout` -在屏幕上布局视图的子视图后，将直接调用此方法。

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>响应方链

此外，`NSViewControllers`现在是窗口的一部分_响应方链_:

[![](indepth-images/vc01.png "响应方链")](indepth-images/vc01.png#lightbox)

和，因此它们是有线向上来接收并响应事件，例如剪切、 复制和粘贴菜单项选择。 此自动的视图控制器网络型只发生在 macOS Sierra (10.12) 上运行的应用和更高版本。

<a name="Containment" />

### <a name="containment"></a>包含

在情节提要，（例如拆分视图控制器和选项卡视图控制器） 的视图控制器可以现在实现_包含_，以便它们可以"包含"其他子视图控制器：

[![](indepth-images/vc02.png "视图控制器包含示例")](indepth-images/vc02.png#lightbox)

子视图控制器包含方法和属性将它们恢复到其父视图控制器和用于显示和从屏幕中删除视图。

内置于 macOS 的所有容器视图控制器都具有 Apple 建议你遵循是否创建自己的自定义容器视图控制器的特定布局：

[![](indepth-images/vc03.png "视图控制器布局")](indepth-images/vc03.png#lightbox)

集合视图控制器包含项数组的集合视图，其中每个包含一个或多个视图控制器包含其自己的视图。

<a name="Segues" />

## <a name="segues"></a>Segue

Segue 提供所有定义您的应用程序 UI 的场景之间的关系。 如果你熟悉在 iOS 中在情节提要中工作，你知道 Segue 的 iOS 通常定义全屏视图之间的转换。 这不同于 macOS，Segues 通常定义时"[包含](#Containment)"，其中一个场景是父级场景的子级。

在 macOS，大多数应用程序往往组与其一起使用 UI 元素，例如拆分视图和选项卡的相同窗口中的视图。 与 iOS，其中视图需要打开和关闭屏幕转换，由于有限物理显示空间。

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>Segue 的演示文稿

给定向包含 macOS 的趋势，在一些情况下其中_Segue 的演示文稿_使用，如模式窗口、 表视图和 Popovers。 macOS 提供以下内置 segue 类型：

- **显示**-非模式窗口作为显示 Segue 的目标。 例如，使用这种类型的 Segue 以显示在你的应用程序的文档窗口的另一个实例。
- **模式**-显示一个模式窗口作为 Segue 的目标。 例如，使用这种类型的 Segue 以显示你的应用程序首选项窗口。
- **表**-显示 Segue 的目标，如一张附加到父窗口。 例如，使用这种类型的 segue 能够提供查找和替换表。
- **Popover** -如 popover 窗口显示 Segue 的目标。 例如，使用此 Segue 类型被用户单击 UI 元素后呈现选项。
- **自定义**-显示 Segue 使用由开发人员定义的自定义 Segue 类型的目标。 请参阅[创建自定义 Segue](#Creating-Custom-Segues)下面部分以了解更多详细信息。

当使用 Segue 的演示文稿，你可以重写`PrepareForSegue`演示以初始化和变量的父视图控制器方法并提供到要呈现的视图控制器的任何数据。

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>触发 Segue

触发的 Segues 允许你指定命名的 Segues (通过其**标识符**接口生成器中的属性) 并将它们触发的事件，例如用户单击的按钮或通过调用`PerformSegue`代码中的方法：

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

当排放应用程序的 UI，在 Xcode 的接口生成器内部定义 Segue ID:

[![](indepth-images/sg02.png "输入 Segue 名称")](indepth-images/sg02.png#lightbox)

在充当 Segue 的源视图控制器，应重写`PrepareForSegue`会显示方法和执行之前执行 Segue 必需执行的任何初始化和指定的视图控制器：

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

（可选） 你可以重写`ShouldPerfromSegue`方法和控件是否 Segue 实际执行通过 C# 代码。 对于手动提供视图控制器，调用其`DismissController`方法在不再需要时，从显示移除它们。

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>创建自定义 Segue

可能你的应用程序时要求 Segue 类型不是由生成在 Segues macOS 中定义的时间。 如果出现这种情况，你可以可以分配自定义 Segue Xcode 的接口在生成器中创建时对应用程序的 UI 进行布局。

例如，若要创建新的 Segue 类型，用于替换当前的视图控制器 （而不是在新窗口中打开目标场景） 窗口中，我们可以使用下面的代码：

```csharp
using System;
using AppKit;
using Foundation;

namespace OnCardMac
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Swap the controllers
            source.View.Window.ContentViewController = destination;

            // Release memory
            source.RemoveFromParentViewController ();
        }
        #endregion

    }
        
}
```

几个事项需要注意的一点：

- 我们将使用`Register`属性可以将此类为目标的 C/macOS 公开。
- 我们要重写`Perform`方法来实际执行我们的自定义 Segue 的操作。
- 我们正在替换窗口的`ContentViewController`Segue 目标 （目标） 定义的一个控制器。
- 我们将删除原始的视图控制器，以释放内存使用`RemoveFromParentViewController`方法。

若要在 Xcode 的接口生成器中使用这种新型 Segue，我们需要首先，编译应用程序，然后切换到 Xcode 并添加两个后台之间新 Segue。 设置**样式**到**自定义**和**Segue 类**到`ReplaceViewSegue`（自定义 Segue 类的名称）：

[![](indepth-images/sg01.png "设置 Segue 类")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>窗口控制器

窗口控制器包含和控制 macOS 应用程序可以创建不同的窗口类型。 对于情节提要，它们具有以下功能：

1. 他们必须提供内容的视图控制器。 这将是子窗口具有相同的内容视图控制器。
2. `Storyboard`属性将包含窗口控制器从加载的否则情节提要`null`如果未从情节提要中加载。
3. 你可以调用`DismissController`方法来关闭给定的窗口并从视图中删除它。

查看控制器，如窗口控制器实现`PerformSegue`，`PrepareForSegue`和`ShouldPerfromSegue`方法，可以用作 Segue 操作的源。

窗口控制器负责 macOS 应用程序的以下功能：

- 它们管理特定的窗口。
- 它们管理窗口的标题栏和工具栏 （如果可用）。
- 它们管理内容的视图控制器，以显示窗口中的内容。

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>笔势识别器

笔势识别器 macOS 都在 iOS 中的对应到几乎完全相同，并允许开发人员可以轻松地添加手势 （例如单击鼠标按钮） 到应用程序的 UI 中的元素。

但是，其中在 iOS 中的手势由确定 （如点击两个手指屏幕） 的应用程序的设计最手势 macOS 中的由硬件。

通过使用手势识别器，你可以极大地降低向在 UI 中的项添加自定义的交互所需的代码量。 因为它们可以自动决定 double 和单一点击之间，单击并拖动事件，等等。

而不是重写`MouseDown`视图控制器中的事件，你应使用手势识别器使用情节提要时对用户输入的事件进行处理。

在 macOS，以下笔势识别器有：

- `NSClickGestureRecognizer` -注册鼠标按下并释放事件。
- `NSPanGestureRecognizer` 寄存器按下鼠标左键、 拖动和发布事件。
- `NSPressGestureRecognizer` -为给定的时间事件按住鼠标按钮的寄存器。
- `NSMagnificationGestureRecognizer` -注册 trackpad 硬件中的放大倍数事件。
- `NSRotationGestureRecognizer` -注册 trackpad 硬件中的旋转事件。

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>使用情节提要引用

情节提要引用，您可以采用大型、 复杂的情节提要设计并将其分解为较小的情节提要获取从原始引用，因此删除删除复杂性并使生成单个演示图板变得更容易设计和维护。

此外，可以提供情节提要引用_定位点_到相同的情节提要或上一个不同的特定场景中的另一个场景。

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>引用外部情节提要

若要添加对外部的情节提要的引用，请执行以下操作：

1. 在**解决方案资源管理器**，右键单击项目名称并选择**添加** > **新文件...**  >  **Mac** > **情节提要**。 输入**名称**为新的情节提要单击**新建**按钮： 

    [![](indepth-images/ref01.png "添加新的情节提要")](indepth-images/ref01.png#lightbox)
2. 在**解决方案资源管理器**，双击要打开进行编辑在 Xcode 的接口生成器中的新情节提要名称。
2. 当你通常将并保存所做的更改，请设计新情节提要的场景的布局： 

    [![](indepth-images/ref02.png "设计的界面")](indepth-images/ref02.png#lightbox)
3. 切换到想要添加到引用接口生成器中的情节提要。
4. 拖动**情节提要引用**从**对象库**拖到设计图面： 

    [![](indepth-images/ref03.png "在库中选择的情节提要引用")](indepth-images/ref03.png#lightbox)
5. 在**属性检查器**，选择的名称**情节提要**上面创建的： 

    [![](indepth-images/ref04.png "配置引用")](indepth-images/ref04.png#lightbox)
6. 控件的单击 UI 上的小组件 （如按钮） 现有场景并创建到新 Segue**情节提要引用**你刚刚创建。  从弹出菜单中选择**显示**完成 Segue: 

    [![](indepth-images/ref06.png "设置 Segue 类型")](indepth-images/ref06.png#lightbox) 
8. 将所做的更改保存到情节提要。
9. 返回到 Visual Studio for Mac 以同步所做的更改。

将显示当运行该应用程序，用户单击 UI 元素上从来自外部的情节提要情节提要引用中指定的初始窗口控制器创建 Segue。

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>引用外部情节提要中的特定场景

要添加对特定场景的引用外部情节提要，（而不是初始的窗口控制器），执行以下操作：

1. 在**解决方案资源管理器**，双击以打开它以在 Xcode 的接口生成器中编辑外部情节提要。
2. 添加新的场景，并像通常那样设计其布局： 

    [![](indepth-images/ref07.png "设计在 Xcode 中的布局")](indepth-images/ref07.png#lightbox)
3. 在**标识检查器**，输入**情节提要 ID**新场景窗口控制器： 

    [![](indepth-images/ref08.png "设置情节提要 ID。")](indepth-images/ref08.png#lightbox)
3. 打开想要添加到引用接口生成器中的情节提要。
4. 拖动**情节提要引用**从**对象库**拖到设计图面： 

    [![](indepth-images/ref03.png "从库中选择的情节提要引用")](indepth-images/ref03.png#lightbox)
5. 在**标识检查器**，选择的名称**情节提要**和**引用 ID** (情节提要 ID) 的上述步骤中创建的场景： 

    [![](indepth-images/ref09.png "设置引用 ID。")](indepth-images/ref09.png#lightbox)
6. 控件的单击 UI 上的小组件 （如按钮） 现有场景并创建到新 Segue**情节提要引用**你刚刚创建。 从弹出菜单中选择**显示**完成 Segue: 

    [![](indepth-images/ref06.png "设置 Segue 类型")](indepth-images/ref06.png#lightbox) 
8. 将所做的更改保存到情节提要。
9. 返回到 Visual Studio for Mac 以同步所做的更改。

当在应用正在运行且用户单击的场景从创建 Segue 在 UI 元素上给定**情节提要 ID**将显示从情节提要引用中指定外部情节提要。

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>引用相同的情节提要中的特定场景

若要添加到特定的场景相同情节提要的引用，请执行以下操作：

1. 在**解决方案资源管理器**，双击情节提要可以打开进行编辑。
2. 添加新的场景，并像通常那样设计其布局： 

    [![](indepth-images/ref11.png "编辑在 Xcode 中的情节提要")](indepth-images/ref11.png#lightbox)
3. 在**标识检查器**，输入**情节提要 ID**新场景窗口控制器： 

    [![](indepth-images/ref12.png "设置情节提要 ID。")](indepth-images/ref12.png#lightbox)
3. 拖动**情节提要引用**从**工具箱**拖到设计图面： 

    [![](indepth-images/ref03.png "从库中选择的情节提要引用")](indepth-images/ref03.png#lightbox)
5. 在**属性检查器**，选择**引用 ID** (情节提要 ID) 的上述步骤中创建的场景： 

    [![](indepth-images/ref13.png "设置引用 ID。")](indepth-images/ref13.png#lightbox)
6. 控件的单击 UI 上的小组件 （如按钮） 现有场景并创建到新 Segue**情节提要引用**你刚刚创建。 从弹出菜单中选择**显示**完成 Segue: 

    [![](indepth-images/ref06.png "选择 Segue 类型")](indepth-images/ref06.png#lightbox) 
8. 将所做的更改保存到情节提要。
9. 返回到 Visual Studio for Mac 以同步所做的更改。

当在应用正在运行且用户单击的场景从创建 Segue 在 UI 元素上给定**情节提要 ID**将显示在相同的情节提要情节提要引用中指定。

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>复杂的情节提要示例

有关使用情节提要 Xamarin.Mac 应用中的复杂示例，请参阅[SourceWriter 示例应用程序](https://developer.xamarin.com/samples/mac/SourceWriter/)。 SourceWriter 是一个非常简单的源代码编辑器，提供代码补全和简单语法突出显示支持。

SourceWriter 代码已经完全注释，且在可用时，提供了相关链接，链接涵盖了从关键技术或方法到 Xamarin.Mac 指南文档中的相关信息。

## <a name="related-links"></a>相关链接

- [MacStoryboard （示例）](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
