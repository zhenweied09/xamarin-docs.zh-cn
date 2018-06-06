---
title: 在 Xamarin.Mac OpenTK 简介
description: 本文提供与使用 OpenTK Xamarin.Mac 应用程序中的介绍。 它涵盖创建和维护游戏窗口、 呈现一个简单的对象和向用户显示该对象。
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 448b8bdba8ccedbb732a73a265d0ce76bb589190
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792601"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>在 Xamarin.Mac OpenTK 简介

OpenTK （打开工具包） 是一个高级的、 低级别 C# 库，使用 OpenGL 和 OpenCL OpenAL 更加轻松。 OpenTK 可以用于游戏、 科学应用程序或其他需要 3D 图形的项目、 音频或计算功能。 本文提供了对使用 OpenTK Xamarin.Mac 应用中的简短介绍。

[![](opentk-images/intro01.png "运行示例应用程序")](opentk-images/intro01.png#lightbox)

在本文中，我们将介绍 OpenTK Xamarin.Mac 应用程序中的基础知识。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在本文中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于网络-最多 C# 类 OBJECTIVE-C 的对象和 UI 元素。

<a name="About_OpenTK" />

## <a name="about-opentk"></a>有关 OpenTK

如上面所述，OpenTK （打开工具包） 是一个高级的、 低级别 C# 库，使用 OpenGL 和 OpenCL OpenAL 更加轻松。 使用 OpenTK Xamarin.Mac 应用中提供以下功能：

- **快速开发**-OpenTK 提供强大的数据类型和内联文档能够提高编码流，并变得更容易和更快地捕获错误。
- **轻松集成**-OpenTK 旨在与.NET 应用程序轻松集成。
- **宽松许可证**-OpenTK 根据 MIT/X11 许可证分发，是完全免费的。
- **丰富的、 类型安全绑定**-OpenTK 支持最新版本的 OpenGL，OpenGL | ES、 OpenAL 和 OpenCL 自动扩展加载错误检查和内联文档。
- **灵活的 GUI 选项**-OpenTK 提供本机、 高性能游戏窗口专为游戏和 Xamarin.Mac。
- **完全托管的符合 Cls 的代码**-OpenTK 支持 32 位和 64 位版本的 macOS 与任何非托管库。
- **3D 数学工具包**OpenTK 提供`Vector`， `Matrix`，`Quaternion`和`Bezier`通过其 3D 数学工具包的结构。

OpenTK 可以用于游戏、 科学应用程序或其他需要 3D 图形的项目、 音频或计算功能。

有关详细信息，请参阅[打开工具包](http://www.opentk.com)网站。

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>OpenTK 快速入门

作为使用 OpenTK Xamarin.Mac 应用中的快速介绍，我们将要创建的简单应用程序将打开的游戏视图，将在该视图和 attachs 游戏视图中的一个简单的三角形呈现到 Mac 应用程序的主窗口向用户显示的三角形。

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>开始一个新项目

适用于 Mac 启动 Visual Studio 并创建新的 Xamarin.Mac 解决方案。 选择**Mac** > **应用** > **常规** > **Cocoa 应用**:

[![](opentk-images/sample01.png "添加新 Cocoa 应用程序")](opentk-images/sample01.png#lightbox)

输入`MacOpenTK`为**项目名称**:

[![](opentk-images/sample02.png "设置项目名称")](opentk-images/sample02.png#lightbox)

单击**创建**按钮以生成新项目。

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>包括 OpenTK

你可以将打开 TK 用于 Xamarin.Mac 应用之前，你需要包括 OpenTK 程序集的引用。 在**解决方案资源管理器**，右键单击**引用**文件夹，然后选择**编辑引用...**.

通过选中`OpenTK`单击**确定**按钮：

[![](opentk-images/sample03.png "编辑项目引用")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>使用 OpenTK

创建新项目后，双击`MainWindow.cs`文件中**解决方案资源管理器**以将其打开以进行编辑。 请`MainWindow`类外观如下所示：

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacOpenTK
{
    public partial class MainWindow : NSWindow
    {
        #region Computed Properties
        public MonoMacGameView Game { get; set; }
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create new Game View and replace the window content with it
            Game = new MonoMacGameView(ContentView.Frame);
            ContentView = Game;

            // Wire-up any required Game events
            Game.Load += (sender, e) =>
            {
                // TODO: Initialize settings, load textures and sounds here
            };

            Game.Resize += (sender, e) =>
            {
                // Adjust the GL view to be the same size as the window
                GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
            };

            Game.UpdateFrame += (sender, e) =>
            {
                // TODO: Add any game logic or physics
            };

            Game.RenderFrame += (sender, e) =>
            {
                // Setup buffer
                GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
                GL.MatrixMode(MatrixMode.Projection);

                // Draw a simple triangle
                GL.LoadIdentity();
                GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
                GL.Begin(BeginMode.Triangles);
                GL.Color3(Color.MidnightBlue);
                GL.Vertex2(-1.0f, 1.0f);
                GL.Color3(Color.SpringGreen);
                GL.Vertex2(0.0f, -1.0f);
                GL.Color3(Color.Ivory);
                GL.Vertex2(1.0f, 1.0f);
                GL.End();

            };

            // Run the game at 60 updates per second
            Game.Run(60.0);
        }
        #endregion
    }
}
```

现在我们将讨论中详细地此代码。

<a name="Required_APIs" />

### <a name="required-apis"></a>所需的 Api

多个引用所需使用 OpenTK Xamarin.Mac 类中。 在开始时定义中，我们已包括以下`using`语句：

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using CoreGraphics;
```
此最小集将需要使用 OpenTK 任何类。

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>添加游戏视图

接下来，我们需要创建游戏视图，以包含所有与 OpenTK 我们交互并显示结果。 我们使用以下代码：

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

此处我们犯游戏视图我们 Main Mac 窗口相同的大小并更换为新的窗口的内容视图`MonoMacGameView`。 因为我们替换现有的窗口内容，在 Main Windows 调整大小时，将自动调整此处提供的视图。

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>对事件作出响应

有几个每个游戏视图应响应的默认事件。 在本部分将介绍所需的主要事件。

<a name="The_Load_Event" />

### <a name="the-load-event"></a>加载事件

`Load`事件是从磁盘如图像、 纹理或音乐加载资源的位置。 对于我们的简单测试应用程序，我们不使用`Load`事件，但具有包含它的引用：

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>调整大小事件

`Resize`每次调整游戏视图时，应调用事件。 对于我们的示例应用，我们将把 GL 视区与我们游戏视图 （这是自动调整 Mac 主窗口的大小） 的大小相同替换为以下代码：

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>UpdateFrame 事件

`UpdateFrame`事件用于处理用户输入，更新对象位置、 运行的物理或 AI 计算。 对于我们的简单测试应用程序，我们不使用`UpdateFrame`事件，但具有包含它的引用： 

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> OpenTK Xamarin.Mac 实现不包括`Input API`，因此你将需要使用 Apple 提供的 Api 来添加键盘和鼠标支持。 或者，您可以创建的自定义实例`MonoMacGameView`，并重写`KeyDown`和`KeyUp`方法。

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>RenderFrame 事件

`RenderFrame`事件包含用于呈现 （绘制） 的代码图形。 对于我们的示例应用程序中，我们将使用一个简单的三角形填充游戏视图： 

```csharp
Game.RenderFrame += (sender, e) =>
{
    // Setup buffer
    GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
    GL.MatrixMode(MatrixMode.Projection);

    // Draw a simple triangle
    GL.LoadIdentity();
    GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
    GL.Begin(BeginMode.Triangles);
    GL.Color3(Color.MidnightBlue);
    GL.Vertex2(-1.0f, 1.0f);
    GL.Color3(Color.SpringGreen);
    GL.Vertex2(0.0f, -1.0f);
    GL.Color3(Color.Ivory);
    GL.Vertex2(1.0f, 1.0f);
    GL.End();

};
```

呈现器代码通常将通过调用正在`GL.Clear`若要删除的任何现有元素之前绘制新的元素。

> [!IMPORTANT]
> OpenTK Xamarin.Mac 版本**不这样做**调用`SwapBuffers`方法你`MonoMacGameView`呈现代码末尾的实例。 这样将导致快速而不是显示在呈现的视图的 strobe 游戏视图。

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>运行的游戏的视图

所有所需的事件定义和游戏视图附加到我们的应用程序的 Main Mac 窗口，我们读取以运行游戏视图并显示我们图形。 使用以下代码：

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

我们传递所需的帧速率我们想要在更新的游戏视图中，对于我们的示例，我们已选择`60`每秒帧数 （刷新与相同的费率正常的电视）。

让我们运行我们的应用程序并查看输出：

[![](opentk-images/intro01.png "一个示例应用程序输出")](opentk-images/intro01.png#lightbox)

如果我们调整我们窗口，游戏视图也将驻留和将大小调整和更新实时以及三角形。

<a name="Where_to_Next" />

### <a name="where-to-next"></a>到下一步在哪里？

与使用 OpenTk 完成 Xamarin.mac 应用程序中的基础知识，此处是若要尝试下一步的一些建议：

- 请尝试更改的三角形的颜色和中的游戏视图的背景色`Load`和`RenderFrame`事件。
- 请更改颜色，当用户按下某个键中的三角形`UpdateFrame`和`RenderFrame`事件或使自己的自定义`MonoMacGameView`类并重写`KeyUp`和`KeyDown`方法。
- 请在使用中的感知键屏幕上移动的三角形`UpdateFrame`事件。 提示： 使用`Matrix4.CreateTranslation`方法来创建平移矩阵并调用`GL.LoadMatrix`方法以加载在`RenderFrame`事件。
- 使用`for`循环来呈现中的多个三角形`RenderFrame`事件。
- 旋转相机来提供不同的视图在三维空间中的三角形。 提示： 使用`Matrix4.CreateTranslation`方法来创建平移矩阵并调用`GL.LoadMatrix`方法以加载它。 你还可以使用`Vector2`， `Vector3`，`Vector4`和`Matrix4`相机操作的类。

有关更多示例，请参阅[OpenTK 示例 GitHub](https://github.com/opentk/opentk/tree/master/Source/Examples)存储库。 它包含的使用 OpenTK 示例的官方列表。 你将需要根据修改这些示例中使用的 OpenTK Xamarin.Mac 版本。

有关 OpenTK 实现的更复杂 Xamarin.Mac 示例，请参阅我们[MonoMacGameView](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)示例。

<a name="Summary" />

## <a name="summary"></a>总结

本文已采取了快速了解一下 OpenTK Xamarin.Mac 应用程序中使用。 我们已了解如何创建一个游戏窗口中，如何将游戏窗口附加到 Mac 窗口以及如何呈现游戏窗口中的一个简单形状。

## <a name="related-links"></a>相关链接

- [MacOpenTK （示例）](https://developer.xamarin.com/samples/mac/MacOpenTK/)
- [MonoMacGameView （示例）](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [打开工具包](http://www.opentk.com)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
