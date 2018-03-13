---
title: "Hello，tvOS 快速入门指南"
description: "本指南将指导完成创建第一个 Xamarin.tvOS 应用和其开发工具链。 它还引入了 Xamarin 设计器中，公开到代码中，UI 控件，并演示如何生成、 运行和测试 Xamarin.tvOS 应用程序。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/02/2018
ms.openlocfilehash: 5eccb36b3c6a437ddc1ec055e779d8f78460643e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="hello-tvos-quick-start-guide"></a>Hello，tvOS 快速入门指南

_本指南将指导完成创建第一个 Xamarin.tvOS 应用和其开发工具链。它还引入了 Xamarin 设计器中，公开到代码中，UI 控件，并演示如何生成、 运行和测试 Xamarin.tvOS 应用程序。_

Apple 已发布 Apple TV，Apple TV 4k，它运行 tvOS 11 第五个的生成。

Apple TV 平台是打开开发人员，使他们能够创建丰富、 引人入胜应用并通过 Apple 电视内置应用商店中发布它们。

如果你熟悉 Xamarin.iOS 开发，你应查找转换到 tvOS 相当简单。 Api 和功能的大部分都是相同，但是，许多常见的 Api （如 WebKit) 不可用。 此外，使用与 Siri 远程会带来在触摸屏基于 iOS 设备中不存在一些设计难题。

本指南将提供使用 tvOS 中的 Xamarin 应用的简介。 有关 tvOS 的详细信息，请参阅 Apple 的[准备 Apple tv 4k](https://developer.apple.com/tvos/)文档。

## <a name="overview"></a>概述

Xamarin.tvOS 允许你开发 C# 和.NET 使用相同的 OS X 库和时开发中使用的界面控件中的完全本机 Apple TV 应用*Swift* (或*Objective C*) 和*Xcode*。

此外，由于 Xamarin.tvOS 应用程序用 C# 和.NET，常见后, 端代码可与共享 Xamarin.iOS 和 Xamarin.Android Xamarin.Mac 应用;同时还在每个平台实现本机的体验。

本文将向你介绍创建 Apple 电视应用通过逐步引导您完成生成一个基本的过程中使用 Xamarin.tvOS 和 Visual Studio 所需的关键概念**Hello，tvOS**的次数进行计数的应用程序按钮有已单击：

[![](hello-tvos-images/run05.png "运行示例应用程序")](hello-tvos-images/run05.png#lightbox)

我们将介绍以下概念：

-  **Visual Studio for Mac** – 简介 Visual Studio for Mac 以及如何使用它创建 Xamarin.tvOS 应用程序。
-  **剖析 Xamarin.tvOS 应用**– 什么 Xamarin.tvOS 应用组成。
-  **创建用户界面**– 如何使用适用于 iOS 的 Xamarin 设计器用于创建用户界面。
-  **部署和测试**– 如何运行和测试你的应用程序，在 tvOS 模拟器和实际 tvOS 硬件上。

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>Visual Studio 中启动新的 Xamarin.tvOS 应用程序，适用于 Mac

如上面所述，我们将创建 Apple TV 应用调用`Hello-tvOS`，它将一个按钮和标签到主屏幕。 点击此按钮后，标签会显示已点击次数。

若要开始，让我们执行以下操作：

1. 启动 Visual Studio for Mac：

    [![](hello-tvos-images/setup01.png "Visual Studio for Mac")](hello-tvos-images/setup01.png#lightbox)
2. 单击**新解决方案...**链接以打开屏幕的左上角**新项目**对话框。
3. 选择**tvOS** > **应用** > **单视图应用**单击**下一步**按钮：

    [![](hello-tvos-images/setup02.png "选择单一视图应用")](hello-tvos-images/setup02.png#lightbox)
4. 输入`Hello, tvOS`为**应用名称**，输入你**组织标识符**单击**下一步**按钮：

    [![](hello-tvos-images/setup04.png "输入 Hello，tvOS")](hello-tvos-images/setup04.png#lightbox)
5. 输入`Hello_tvOS`为**项目名称**单击**创建**按钮：

    [![](hello-tvos-images/setup03.png "输入 HellotvOS")](hello-tvos-images/setup03.png#lightbox)

适用于 Mac 的 visual Studio 将创建新的 Xamarin.tvOS 应用并显示添加到你的应用程序解决方案的默认文件：

 [![](hello-tvos-images/project01.png "默认文件视图")](hello-tvos-images/project01.png#lightbox)

Visual Studio for Mac 使用**解决方案**和**项目**，在 Visual Studio 可进行完全相同的方式。 解决方案是可包含一个或多个项目的容器；项目可包含应用程序、支持库和测试应用程序等。在这种情况下，适用于 Mac 的 Visual Studio 解决方案和应用程序项目所创建的。

如果愿意，你可以创建一个或多个代码包含通用、 共享代码库项目。 可以使用的应用程序项目或与其他 Xamarin.tvOS 应用项目共享这些库项目 （或 Xamarin.iOS 和 Xamarin.Android Xamarin.Mac 基于代码的类型） 时，就像你将在生成标准的.NET 应用程序时一样。

## <a name="anatomy-of-a-xamarintvos-app"></a>Xamarin.tvOS 应用的剖析

如果你熟悉 iOS 编程，你会注意到大量的此处的相似之处。 事实上，tvOS 9 是 iOS 9 的子集，因此在这里将跨大量的概念。

让我们看看在项目中的文件：

-   `Main.cs` – 此文件包含应用的主入口点。 启动应用时，其中会包含运行的首个类和方法。
-   `AppDelegate.cs` – 此文件包含负责侦听事件从操作系统的主应用程序类。
-   `Info.plist` – 此文件包含应用程序属性，例如应用程序名称、 图标，等等。
-   `ViewController.cs` -这是表示主窗口并控制它的生命周期的类。
-   `ViewController.designer.cs` – 此文件包含联结代码，可帮助你将与主屏幕的用户界面集成。
-  `Main.storyboard` – 主窗口 UI。 此文件可创建和维护的 Xamarin 设计器中为 iOS。

在以下部分中，我们将快速查看通过其中某些文件。 我们将探讨它们更详细地更高版本，但它是一个好办法现在了解其基础知识。

### <a name="maincs"></a>Main.cs

`Main.cs`文件包含一个静态`Main`方法创建新的 Xamarin.tvOS 应用实例并传递类的名称，该方法将处理 OS 事件，这在我们的示例是`AppDelegate`类：

```csharp
using UIKit;

namespace Hello_tvOS
{
    public class Application
    {
        // This is the main entry point of the application.
        static void Main (string[] args)
        {
            // if you want to use a different Application Delegate class from "AppDelegate"
            // you can specify it here.
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

`AppDelegate.cs`文件包含我们`AppDelegate`类，它是负责创建我们窗口和侦听 OS 事件：

```csharp
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    // The UIApplicationDelegate for the application. This class is responsible for launching the
    // User Interface of the application, as well as listening (and optionally responding) to application events from iOS.
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        // class-level declarations

        public override UIWindow Window {
            get;
            set;
        }

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Override point for customization after application launch.
            // If not required for your application you can safely delete this method

            return true;
        }

        public override void OnResignActivation (UIApplication application)
        {
            // Invoked when the application is about to move from active to inactive state.
            // This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message)
            // or when the user quits the application and it begins the transition to the background state.
            // Games should use this method to pause the game.
        }

        public override void DidEnterBackground (UIApplication application)
        {
            // Use this method to release shared resources, save user data, invalidate timers and store the application state.
            // If your application supports background execution this method is called instead of WillTerminate when the user quits.
        }

        public override void WillEnterForeground (UIApplication application)
        {
            // Called as part of the transition from background to active state.
            // Here you can undo many of the changes made on entering the background.
        }

        public override void OnActivated (UIApplication application)
        {
            // Restart any tasks that were paused (or not yet started) while the application was inactive.
            // If the application was previously in the background, optionally refresh the user interface.
        }

        public override void WillTerminate (UIApplication application)
        {
            // Called when the application is about to terminate. Save data, if needed. See also DidEnterBackground.
        }
    }
}
```

此代码是可能不熟悉，除非已由 iOS 应用程序之前，但它是非常简单。 让我们看一下重要的行。

首先，让我们看看类级变量声明：

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

`Window`属性提供访问权限的主窗口。 tvOS 使用所谓的*模型视图控制器*(MVC) 模式。 通常，为每个窗口，您创建中 （以及在 windows 中的许多其他操作的），则控制器，它负责窗口的生命周期，例如显示它，将新视图 （控件） 添加到它，等等。

接下来，我们必须`FinishedLaunching`方法。 此方法将运行后已实例化应用程序，并且它负责实际创建应用程序窗口和开始在其中显示视图的过程。 由于我们的应用程序使用情节提要以定义其 UI，其他不不需要任何代码此处。

如与模板中提供的许多其他方法`DidEnterBackground`和`WillEnterForeground`。 可以会安全地删除这些应用程序事件不用在应用程序中。

### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController`类是我们主窗口的控制器。 这意味着它所负责的主窗口的生命周期。 我们将仔细地检查这更高版本，现在让我们看快速了解一下它：

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
    }
}
```

#### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

主窗口类的设计器文件为空稍后再试，但它会自动填充由 Visual Studio for Mac 如我们使用 iOS 设计器创建我们用户界面：

```csharp
using Foundation;

namespace HellotvOS
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

我们不通常与相关设计器文件，因为它们只需自动托管由 Visual Studio for Mac 并只需提供允许对控件的访问，我们将添加到任何窗口或查看我们的应用程序中的必备项联结代码。

现在，我们已创建我们 Xamarin.tvOS 的应用程序，并且我们拥有及其组件的一个基本的了解，我们来看创建 UI。

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>创建用户界面

无需使用 Xamarin iOS 的设计器来创建 Xamarin.tvOS 应用程序的用户界面，可直接从 C# 代码创建 UI，但这超出了本文的范围是。 为简单起见，我们将使用 iOS 设计器来创建本教程的其余我们 UI。

若要开始创建你的 UI，让我们双击`Main.storyboard`文件中**解决方案资源管理器**以将其打开以进行编辑 iOS 设计器中：

[![](hello-tvos-images/designer01.png "在解决方案资源管理器 Main.storyboard 文件")](hello-tvos-images/designer01.png#lightbox)

这应启动设计器，并如下所示：

[![](hello-tvos-images/designer02.png "设计器")](hello-tvos-images/designer02.png#lightbox)

有关 iOS 设计器的详细信息以及它如何工作，请参阅[简介 Xamarin 设计器中为 iOS](~/ios/user-interface/designer/introduction.md)指南。

我们现在可以启动将控件添加到我们 Xamarin.tvOS 的应用程序的设计图面。

请执行以下操作：

1. 找到**工具箱**，设计图面右侧应为：

    [![](hello-tvos-images/designer03.png "工具箱")](hello-tvos-images/designer03.png#lightbox)

    如果你不能在此处找到它，浏览到**视图 > 填充 > 工具箱**以便进行查看。
2. 拖动**标签**从**工具箱**到设计图面：

    [![](hello-tvos-images/designer04.png "从工具箱中拖动标签")](hello-tvos-images/designer04.png#lightbox)
3. 单击**标题**中的属性**属性填充**和按钮的标题更改为`Hello, tvOS`并设置**字体大小**128 到：

    [![](hello-tvos-images/designer05.png "将标题设置为 Hello，tvOS 并将字体大小设置为 128")](hello-tvos-images/designer05.png#lightbox)
4. 调整标签的大小，以便的所有单词可见，并将其居中窗口的顶部附近：

    [![](hello-tvos-images/designer06.png "调整大小和中心标签")](hello-tvos-images/designer06.png#lightbox)
5. 标签现在都将需要要约束到它的位置，以使其显示按预期。 无论屏幕的大小。 若要执行此操作，请单击标签之前*T 形句柄*显示：

    [![](hello-tvos-images/designer07.png "T 形句柄")](hello-tvos-images/designer07.png#lightbox)
6. 若要水平限定标签，请选择中心方形，并将其拖到垂直虚线：

    [![](hello-tvos-images/designer08.png "选择中心方形")](hello-tvos-images/designer08zoom.png#lightbox)

     标签应打开橙色。
7. 选择顶部的标签，T 句柄并将其拖到窗口的顶部：

    [![](hello-tvos-images/designer09.png "将句柄拖到窗口的上边缘")](hello-tvos-images/designer09.png#lightbox)
8. 接下来，单击宽度，然后选择高度*骨骼句柄*如下所示：

    [![](hello-tvos-images/designer10.png "宽度和高度骨骼句柄")](hello-tvos-images/designer10.png#lightbox)

     当每个*骨骼句柄*是单击，同时选择宽度和高度分别设置固定的维度。
9. 完成后，你的约束应查找类似于属性填充的布局选项卡中：

    [![](hello-tvos-images/designer11.png "示例约束")](hello-tvos-images/designer11.png#lightbox)
8. 拖动**按钮**从**工具箱**并将其放在标签下。
9. 单击**标题**中的属性**属性填充**和按钮的标题更改为`Click Me`:

    [![](hello-tvos-images/designer12.png "将按钮标题更改为 Click Me")](hello-tvos-images/designer12.png#lightbox)
10. 重复步骤 5 至 8 上述约束 tvOS 窗口中的按钮。 但是，而不是将 T 句柄拖到窗口 （如步骤 7） 的顶部，将其拖到底部的标签：

    [![](hello-tvos-images/designer14.png "约束按钮")](hello-tvos-images/designer14.png#lightbox)
11. 拖动鼠标按钮下的另一个标签，要作为第一个标签并将设置相同的宽度将其大小调整其**对齐**到**Center**:

    [![](hello-tvos-images/designer15.png "拖动鼠标按钮下的另一个标签，调整它的第一个标签与相同的宽度和到中心设置其对齐方式的大小")](hello-tvos-images/designer15.png#lightbox)
12. 如第一个标签和按钮，设置此标签以居中，并将其固定到的位置和大小：

    [![](hello-tvos-images/designer16.png "将标签固定到的位置和大小")](hello-tvos-images/designer16.png#lightbox)
13. 将所做的更改保存到用户界面。

已调整大小和移动控件的时候，你应已注意到，设计器使您可以在基于有用的管理单元提示[Apple 电视人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)。 这些准则将帮助你创建高质量应用程序将具有有关 Apple TV 用户熟悉的外观和感觉。

如果你查看**文档大纲**部分中，请注意如何显示的布局和构成我们用户界面元素的层次结构：

[![](hello-tvos-images/designer17.png "文档大纲部分")](hello-tvos-images/designer17.png#lightbox)

从这里你可以选择要编辑或重新排序 UI 元素，如果需要通过拖动的项。 例如，如果正在由另一个元素覆盖 UI 元素，你无法将其拖到列表，使其窗口上的最顶层的项的底部。

现在，我们已创建我们用户界面，我们需要公开 UI 项，以便让 Xamarin.tvOS 可以访问并与它们在 C# 代码进行交互。

### <a name="accessing-the-controls-in-the-code-behind"></a>访问背后的代码中的控件

有两种主要方法来访问你在代码中的 iOS 设计器中添加的控件：

* 在控件上创建一个事件处理程序。
* 为控件提供一个名称，以便我们可以更高版本引用它。

任何一种方法添加时中的分部类`ViewController.designer.cs`将更新以反映所做的更改。 这样，你可以然后访问视图控制器中的控件。

### <a name="creating-an-event-handler"></a>创建一个事件处理程序

在此示例应用程序中，单击该按钮我们希望_内容_发生，因此，事件处理程序需要要添加到按钮上的特定事件。 若要对此进行设置，请执行以下操作：

1. 在 Xamarin iOS 设计器中，选择视图控制器上的按钮。
2. 在属性区中，选择**事件**选项卡：

    [![](hello-tvos-images/event1.png "事件选项卡")](hello-tvos-images/event1.png#lightbox)
3. 找到 TouchUpInside 事件，并为其提供名为一个事件处理程序`Clicked`:

    [![](hello-tvos-images/event2.png "TouchUpInside 事件")](hello-tvos-images/event2.png#lightbox)
4. 当你按**Enter**、 **ViewController**.cs 文件将打开，建议你在代码中的事件处理程序的位置。 使用你键盘上箭头键来设置的位置：

    [![](hello-tvos-images/event3.png "设置位置")](hello-tvos-images/event3.png#lightbox)
5. 这将创建分部方法，如下所示：

    [![](hello-tvos-images/event4.png "分部方法")](hello-tvos-images/event4.png#lightbox)

现在我们就可以开始添加一些代码以允许对函数的按钮。

### <a name="naming-a-control"></a>命名控件

当单击按钮时，应更新标签基于的点击数。 若要执行此操作，我们将需要访问代码中的标签。 这可通过为其指定名称。 请执行以下操作：

1. 打开情节提要，并选择底部的视图控制器的标签。
2. 在属性区中，选择**小组件**选项卡：

    [![](hello-tvos-images/name1.png "选择小组件选项卡")](hello-tvos-images/name1.png#lightbox)
3. 下**标识 > 名称**，添加`ClickedLabel`:

    [![](hello-tvos-images/name2.png "设置 ClickedLabel")](hello-tvos-images/name2.png#lightbox)

我们现在已准备好开始更新标签 ！

### <a name="how-controls-are-accessed"></a>如何访问控件

如果你选择`ViewController.designer.cs`中**解决方案资源管理器**你将能够看到如何`ClickedLabel`标签和`Clicked`事件处理程序已映射到**Outlet**和**操作**C# 中：

[![](hello-tvos-images/accesscontrol.png "容器和操作")](hello-tvos-images/accesscontrol.png#lightbox)

你可能注意`ViewController.designer.cs`是一个分部类，以便 Visual Studio for Mac 无需修改`ViewController.cs`这会覆盖我们具有对类进行了任何更改。

公开的 UI 元素，这种方式，可以在视图控制器中访问这些。

你通常将从不需要以打开`ViewController.designer.cs`你自己，它此处提供出于教育目的仅。

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>编写代码

与我们创建的用户界面公开给通过代码及其 UI 元素**Outlet**和**操作**，我们就最后可以编写代码来为提供程序功能。

在我们的应用程序，每次单击第一个按钮时，我们将更新我们的标签，以显示单击该按钮多少次。 若要实现此目的，我们需要打开`ViewController.cs`文件进行编辑，通过双击它在**解决方案 Pad**:

[![](hello-tvos-images/code01.png "解决方案填充")](hello-tvos-images/code01.png#lightbox)

首先，我们需要创建中的类级变量我们`ViewController`类用于跟踪的发生的点击数。 编辑类定义，使其类似于如下所示：

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

接下来，在同一个类 (`ViewController`)，我们需要重写**ViewDidLoad**方法然后添加某些代码来设置我们的标签的初始消息：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

我们需要使用`ViewDidLoad `，而不是另一种方法，如`Initialize`，这是因为`ViewDidLoad `称为*后*操作系统已加载和实例化用户界面与`.storyboard`文件。 如果我们尝试访问标签控件之前`.storyboard`文件已完全加载并实例化，我们会遇到`NullReferenceException`错误因为不会尚未创建标签控件。

接下来，我们需要添加代码以响应用户单击按钮。 将以下代码添加到分部类，我们将创建：

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

此代码将调用的每当用户单击我们的按钮。

使用位置中的所有内容，我们现已生成并测试我们 Xamarin.tvOS 应用程序。

## <a name="testing-the-application"></a>测试应用程序

它是时间生成并运行应用程序以确保它按预期运行。 我们可以生成并运行所有在一个步骤中，或者我们可以生成它而无需运行它。

在我们生成应用程序时，我们可以选择我们希望生成哪种类型：

-   **调试**– 调试版本编译为 （应用程序） 文件的可让我们来调试应用程序运行时所发生的额外元数据。
-   **释放**– 发布版本还会创建文件，但它不包括调试信息，因此它更小并且执行得更快。  

你可以选择从生成的类型**配置选择器**左上角的 Visual Studio for Mac 屏幕：

[![](hello-tvos-images/run01.png "选择的生成的类型")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>生成应用程序

在本例中，我们只是想调试版本，因此我们必须确保**调试**选择。 让我们首先生成我们的应用程序生成任一按**⌘B**，或从**生成**菜单上，选择**生成所有**。

如果没有任何错误，你将看到**生成成功**Visual Studio 中的 Mac 的状态栏的消息。 如果没有错误，查看你的项目，并确保你已正确遵循的步骤。 首先确认你的代码 （同时在 Xcode 中和适用于 Mac 的 Visual Studio 中） 与代码匹配在本教程中。

### <a name="running-the-application"></a>运行应用程序

若要运行应用程序，我们有三个选项：

-  按 **⌘+Enter**。
-  在“运行”菜单中，选择“调试”。
-  单击 Visual Studio for Mac 工具栏中的“播放”按钮（位于“解决方案资源管理器”上方）。

（如果它尚未已生成），将生成应用程序，开始在调试模式下，tvOS 模拟器将启动而且应用程序将启动并显示它的主要界面窗口：

[![示例应用程序主屏幕](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

从**硬件**菜单中选择**显示 Apple 电视远程**以便您可以控制模拟器。

[![](hello-tvos-images/run04.png "选择显示 Apple 电视远程")](hello-tvos-images/run04.png#lightbox)

如果你单击按钮标签多次使用模拟器的远程应更新的数量：

[![](hello-tvos-images/run05.png "具有更新计数标签")](hello-tvos-images/run05.png#lightbox)

祝贺你！ 我们介绍了很多在这里，内容，但如果您从开始到结束遵循本教程，你现在应具有的组成部分 Xamarin.tvOS 应用，以及用来创建它们的工具深入了解。

## <a name="where-to-next"></a>到下一步在哪里？

开发 Apple TV 应用程序存在一些难题由于用户界面 （它是在同一房间不在用户的现有） 和限制 tvOS 之间断开连接时，将放置在应用程序大小和存储。

因此，我们强烈建议，你阅读以下文档之前跳转到 Xamarin.tvOS 应用程序的设计：

- [简介 tvOS 9](~/ios/tvos/platform/tvos9.md) – 这篇文章介绍的新增和更改 Api 和 tvOS 9 中可用的功能的所有 Xamarin.tvOS 开发人员。
- [使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md)– Xamarin.tvOS 应用程序的用户将不与它的接口直接作为 ios 其中他们点击映像在设备的屏幕上，但间接从跨房间使用 Siri 远程交互。 本文介绍如何焦点以及如何使用它来处理在 Xamarin.tvOS 应用程序的用户界面中进行导航的概念。
- [Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)– 用户将与 Apple TV 和 Xamarin.tvOS 应用程序进行交互，是通过包含 Siri 远程的主要方式。 如果您的应用程序是一个游戏，你可以 （可选） 生成的第三方，所做的支持 iOS (MFI) 蓝牙游戏控制器中你的应用程序。 本文介绍如何在你 Xamarin.tvOS 应用中支持新的 Siri 远程和蓝牙游戏控制器。
- [资源和数据存储](~/ios/tvos/app-fundamentals/resources-data-storage.md)– 与 iOS 设备不同新 Apple TV 不提供针对 tvOS 应用持久的本地存储。 因此，如果您 Xamarin.tvOS 的应用程序需要保留信息 （如用户首选项） 该存储并从 iCloud 检索数据。 本文介绍如何使用资源和 Xamarin.tvOS 应用中的持久数据存储。
- [处理图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)– 创建迷人视觉图标和图像是开发你的 Apple TV 应用的沉浸式用户体验的关键部分。 本指南将介绍创建和包括 Xamarin.tvOS 应用的必要图形资产所需的步骤。
- [用户界面](~/ios/tvos/user-interface/index.md)– 包括用户界面 (UI) 控件的常规用户体验 (UX) 覆盖率 Xamarin.tvOS 处理时使用 Xcode 的接口生成器和 UX 设计原则。
- [部署和测试](~/ios/tvos/deploy-test/index.md)– 本部分介绍用于测试应用程序以及如何以将其分发的主题。 此处的主题包括等工具用于调试，测试人员以及如何发布到 Apple 电视应用商店应用程序的部署。

如果你运行使用 Xamarin.tvOS 遇到问题，请参阅我们[故障排除](~/ios/tvos/troubleshooting.md)文档上的列表的已知问题和解决方案。

## <a name="summary"></a>摘要

本文章提供了通过创建一个简单 Hello，tvOS 应用开发的 Mac 的使用 Visual Studio 对 tvOS 的应用程序的快速入门。 它涵盖 tvOS 设备资源调配，接口创建、 编码 tvOS 和 tvOS 模拟器上测试的基础知识。


## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [构建应用程序对 tvOS 使用 Xamarin （视频）](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
