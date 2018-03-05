---
title: "创建自定义控件"
description: "本文介绍如何创建自定义控件和在接口生成器中使用它们。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 675B9405-D9A7-49F0-94AD-417F10A71D11
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: f3d6301bc2c0237a268669fff437801bfb2657d1
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="creating-custom-controls"></a>创建自定义控件

_本文介绍如何创建自定义控件和在接口生成器中使用它们。_

在使用 C# 和.NET Xamarin.Mac 应用程序中，你有权访问相同用户控件，开发人员使用*Objective C*， *Swift*和*Xcode*未. 因为 Xamarin.Mac 与 Xcode 直接集成，你可以使用 Xcode 的_接口生成器_来创建和维护你的用户控件 （或根据需要在 C# 代码中直接创建它们）。

尽管 macOS 提供了丰富的内置用户控件，则表明可能存在你需要创建自定义控件提供功能不提供现成可用，或以匹配 （如游戏的接口） 的自定义用户界面主题的时间。

[ ![](custom-controls-images/intro01.png "自定义 UI 控件的示例")](custom-controls-images/intro01.png)

在本文中，我们将介绍在 Xamarin.Mac 应用程序中创建可重用的自定义用户界面控件的基础知识。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在本文中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于网络-最多 C# 类 OBJECTIVE-C 的对象和 UI 元素。

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>自定义控件简介

如上所述，可能需要来创建可重用，自定义用户界面控件为 Xamarin.Mac 应用程序的 UI 提供唯一的功能，或创建自定义用户界面主题 （如游戏的接口） 的时间。

在这些情况下，你可以轻松地从继承`NSControl`并创建一个自定义工具，可以将其添加到你的应用程序的 UI，通过 C# 代码或通过 Xcode 的接口生成器。 通过继承`NSControl`自定义控件将自动拥有的所有内置用户界面控件具有标准功能 (如`NSButton`)。

如果自定义用户界面控件只显示信息 （如自定义图表和图形工具），你可能想要从其继承`NSView`而不是`NSControl`。

无论使用哪一基类，用于创建自定义控件的基本步骤是相同的。

在本文将，创建一个自定义翻转交换机组件提供唯一的用户界面主题和生成完全正常运行的自定义用户界面控件的示例。

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>生成自定义控件

由于我们要创建自定义控件将响应与用户输入 （鼠标左键的按钮单击），我们将从继承`NSControl`。 这种方式，我们自定义控件将自动具有所有内置用户界面控件具有标准功能，并且响应和标准 macOS 控件一样。

在适用于 Mac 的 Visual Studio，打开你想要创建的自定义用户界面控件 （或创建一个新） Xamarin.Mac 项目。 添加新类，并调用它`NSFlipSwitch`:

[ ![](custom-controls-images/custom01.png "添加新类")](custom-controls-images/custom01.png)

接下来，编辑`NSFlipSwitch.cs`类并使其如下所示：

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using AppKit;
using CoreGraphics;

namespace MacCustomControl
{
    [Register("NSFlipSwitch")]
    public class NSFlipSwitch : NSControl
    {
        #region Private Variables
        private bool _value = false;
        #endregion

        #region Computed Properties
        public bool Value {
            get { return _value; }
            set {
                // Save value and force a redraw
                _value = value;
                NeedsDisplay = true;
            }
        }
        #endregion

        #region Constructors
        public NSFlipSwitch ()
        {
            // Init
            Initialize();
        }

        public NSFlipSwitch (IntPtr handle) : base (handle)
        {
            // Init
            Initialize();
        }

        [Export ("initWithFrame:")]
        public NSFlipSwitch (CGRect frameRect) : base(frameRect) {
            // Init
            Initialize();
        }

        private void Initialize() {
            this.WantsLayer = true;
            this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
        }
        #endregion

        #region Draw Methods
        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Use Core Graphic routines to draw our UI
            ...

        }
        #endregion

        #region Private Methods
        private void FlipSwitchState() {
            // Update state
            Value = !Value;
        }
        #endregion

    }
}
```

第一件事要注意有关自定义类，因为我们继承自`NSControl`和使用**注册**命令来公开此类与 OBJECTIVE-C 和 Xcode 的接口生成器：

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

在以下部分中，我们将看看详述上面的代码的其余部分。

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>跟踪控件的状态

由于我们自定义控件是一个开关，我们需要一种方法来跟踪该交换机的打开/关闭状态。 我们处理这种情况中的以下代码使用`NSFlipSwitch`:

```csharp
private bool _value = false;
...

public bool Value {
    get { return _value; }
    set {
        // Save value and force a redraw
        _value = value;
        NeedsDisplay = true;
    }
}
```

交换机的状态更改时，我们需要一种方法来更新 UI。 我们执行此操作通过强制控件重绘其 UI `NeedsDisplay = true`。

如果我们的控制需要超过单个/关闭状态 （例如具有 3 的位置的多状态交换机），我们本来也可以使用**枚举**跟踪的状态。 对于我们的示例，一个简单**bool**将执行操作。

我们还添加了一个帮助器方法来打开和关闭交换之间进行切换的状态：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

更高版本，我们将展开此帮助器类，以通知调用方的交换机状态已更改。

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>绘制控件的接口

我们将使用核心图形绘制例程来绘制在运行时我们自定义控件的用户界面。 我们可以这样做之前，我们需要在我们的控制层上打开。 我们使用以下私有方法执行此操作：

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

从每个控件的构造函数来确保已正确配置该控件获取调用此方法。 例如:

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

接下来，我们需要重写`DrawRect`方法并添加要绘制控件的核心图形例程：

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

我们将会调整控件的可视表示其状态更改时 (例如从**上**到**关闭**)。 任何时候，只要的状态更改，我们可以使用`NeedsDisplay = true`命令来强制控件重绘的新状态。

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>响应用户输入

有两个基本的方法，我们可以添加到我们的自定义控件的用户输入：**重写鼠标处理例程**或**笔势识别器**。 我们使用哪种方法将基于我们的控制所需的功能。

> [!IMPORTANT]
> 为你创建的任何自定义控件，你应使用**重写方法**_或_**笔势识别器**，但不是能同时在同一时间，它们可以相互冲突。

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>处理用户输入与重写方法

继承的对象`NSControl`(或`NSView`) 具有多个重写方法为处理鼠标或键盘输入。 对于我们的示例的控制，我们希望翻转之间进行切换的状态**上**和**关闭**当用户单击鼠标左键与控件上。 我们可以添加以下重写方法到`NSFliwSwitch`类来处理这种情况：

```csharp
#region Mouse Handling Methods
// --------------------------------------------------------------------------------
// Handle mouse with Override Methods.
// NOTE: Use either this method or Gesture Recognizers, NOT both!
// --------------------------------------------------------------------------------
public override void MouseDown (NSEvent theEvent)
{
    base.MouseDown (theEvent);

    FlipSwitchState ();
}

public override void MouseDragged (NSEvent theEvent)
{
    base.MouseDragged (theEvent);
}

public override void MouseUp (NSEvent theEvent)
{
    base.MouseUp (theEvent);
}

public override void MouseMoved (NSEvent theEvent)
{
    base.MouseMoved (theEvent);
}
## endregion
```

在上述代码中，我们调用`FlipSwitchState`方法 （上面定义） 来翻转 On/Off 状态中的交换机`MouseDown`方法。 这将强制该控件重绘以反映当前状态。

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>处理用户输入具有笔势识别器

（可选） 笔势识别器可用于处理与控件交互的用户。 删除前面添加的替代，请编辑`Initialize`方法并使其如下所示：

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;

    // --------------------------------------------------------------------------------
    // Handle mouse with Gesture Recognizers.
    // NOTE: Use either this method or the Override Methods, NOT both!
    // --------------------------------------------------------------------------------
    var click = new NSClickGestureRecognizer (() => {
        FlipSwitchState();
    });
    AddGestureRecognizer (click);
}
```

在这里，我们将创建一个新`NSClickGestureRecognizer`并调用我们`FlipSwitchState`方法可以更改开关的状态，当用户使用鼠标左键单击它。 `AddGestureRecognizer (click)`方法将笔势识别器添加到控件。

同样，我们使用哪种方法取决于我们尝试完成我们自定义控件。 如果我们需要低级别的访问权限对用户交互，使用重写方法。 如果我们需要预定义的功能，例如单击鼠标就使用手势识别器。

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>响应状态更改事件

当用户更改我们的自定义控件的状态时，我们需要一种方法，以响应的代码中的状态更改 (如执行某项操作时的自定义按钮单击)。 

若要提供此功能，编辑`NSFlipSwitch`类并添加以下代码：

```csharp
#region Events
public event EventHandler ValueChanged;

internal void RaiseValueChanged() {
    if (this.ValueChanged != null)
        this.ValueChanged (this, EventArgs.Empty);

    // Perform any action bound to the control from Interface Builder
    // via an Action.
    if (this.Action !=null) 
        NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
}
## endregion
```

接下来，编辑`FlipSwitchState`方法并使其如下所示：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

首先，我们提供`ValueChanged`事件，我们可以添加的处理程序在 C# 代码，以便我们可以在用户更改的交换机的状态时执行的操作。

第二，由于我们自定义控件都继承自`NSControl`，它会自动拥有**操作**，可以将其分配在 Xcode 的接口生成器。 若要调用此**操作**当状态更改时，我们使用以下代码：

```csharp
if (this.Action !=null) 
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

首先，我们检查以查看是否**操作**已分配给该控件。 接下来，我们调用**操作**如果定义了。

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>使用自定义控件

与我们完全定义的自定义控件，我们可以或者将其添加到我们 Xamarin.Mac 应用程序的 UI 使用 C# 代码或在 Xcode 的接口生成器。

若要添加使用接口生成器的控件，首先执行干净的生成的 Xamarin.Mac 项目，然后双击`Main.storyboard`文件以在接口生成器中将其打开，以进行编辑：

[ ![](custom-controls-images/custom02.png "编辑在 Xcode 中的情节提要")](custom-controls-images/custom02.png)

接下来，拖动`Custom View`在用户界面设计：

[ ![](custom-controls-images/custom03.png "从库中选择的自定义视图")](custom-controls-images/custom03.png)

使用自定义视图仍处于选中状态，切换到**标识检查器**和更改视图的**类**到`NSFlipSwitch`:

[ ![](custom-controls-images/custom04.png "设置视图的类")](custom-controls-images/custom04.png)

切换到**助手编辑器中**并创建**Outlet**为自定义控件 (同时确保绑定在`ViewControler.h`文件而不`.m`文件):

[ ![](custom-controls-images/custom05.png "配置新插座")](custom-controls-images/custom05.png)

保存所做的更改，返回到 Visual Studio for Mac 并允许更改同步。编辑`ViewController.cs`文件并进行`ViewDidLoad`方法外观如下所示：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Do any additional setup after loading the view.
    OptionTwo.ValueChanged += (sender, e) => {
        // Display the state of the option switch
        Console.WriteLine("Option Two: {0}", OptionTwo.Value);
    };
}
``` 

在这里，我们响应`ValueChanged`我们在上面定义的事件`NSFlipSwitch`类并将写出当前**值**当用户单击控件上。

或者，我们无法返回到接口生成器，并定义**操作**在控件上：

[ ![](custom-controls-images/custom06.png "配置新的操作")](custom-controls-images/custom06.png)

同样，编辑`ViewController.cs`文件并添加以下方法：

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> 您应使用两个**事件**或定义**操作**在接口生成器中，但不是应同时使用这两种方法或它们可以相互冲突。

<a name="Summary" />

## <a name="summary"></a>摘要

本文已了解 Xamarin.Mac 应用程序中创建可重用的自定义用户界面控件的详细的信息。 我们已了解如何绘制自定义控件 UI 中，两种主要方法响应鼠标和用户输入以及如何将公开新的控件于 Xcode 的接口生成器中的操作。

## <a name="related-links"></a>相关链接

- [MacCustomControl （示例）](https://developer.xamarin.com/samples/mac/MacCustomControl/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 用户界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [处理鼠标事件](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
