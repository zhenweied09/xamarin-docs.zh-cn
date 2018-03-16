---
title: "在代码中创建 iOS 用户界面"
description: "Xamarin.iOS 提供两种方法创建用户界面为你的应用 – 与 Xamarin 设计器或在代码的 iOS。 本文介绍如何在代码中完全创建 iOS 用户界面。 它演示如何从项目模板可通过从 UIKit 创建视图的层次结构生成控制器中的应用程序屏幕启动。 然后介绍了如何创建可在控制器中加载的自定义视图。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: d53dea1a46c6b42f901beb217eb00b3a3fa0fd92
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="creating-ios-user-interfaces-in-code"></a>在代码中创建 iOS 用户界面

_Xamarin.iOS 提供两种方法创建用户界面为你的应用 – 与 Xamarin 设计器或在代码的 iOS。本文介绍如何在代码中完全创建 iOS 用户界面。它演示如何从项目模板可通过从 UIKit 创建视图的层次结构生成控制器中的应用程序屏幕启动。然后，它还讨论了如何在控制器中创建可加载的自定义视图。_

IOS 应用程序的用户界面类似 storefront – 应用程序通常中获取一个窗口中，但它可以用填充窗口需要它的多个对象，以及可以根据应用程序想要显示更改的对象和布局。 此情形中的对象（用户看到的内容）称为视图。 若要生成应用程序中的单个屏幕，视图会堆叠在内容视图层次结构，并由单个视图控制器管理层次结构。 具有多个屏幕的应用程序具有多个内容视图层次结构（各自具有自己的视图控制器），应用程序会将视图置于窗口中以基于用户所处的屏幕创建不同的内容视图层次结构。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

下图显示了窗口、视图、子视图与视图控制器之间的关系，它们向设备屏幕提供了用户界面： 

[![](ios-code-only-images/image9.png "此图描述了窗口、 视图、 某些子视图和视图控制器之间的关系")](ios-code-only-images/image9.png#lightbox)

可以使用构造这些视图层次结构[Xamarin 设计器中为 iOS](~/ios/user-interface/designer/index.md)在 Visual Studio 中，但是最好是基本了解如何在代码中完全。 本文将指导完成一些基本的点，才能启动和运行，而仅限代码的用户界面开发。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

下图显示了窗口、视图、子视图与视图控制器之间的关系，它们向设备屏幕提供了用户界面： 

[![](ios-code-only-images/image9.png "此图描述了窗口、 视图、 某些子视图和视图控制器之间的关系")](ios-code-only-images/image9.png#lightbox)


可以使用构造这些视图层次结构[Xamarin 设计器中为 iOS](~/ios/user-interface/designer/index.md)中适用于 Mac 的 Visual Studio，但是最好是基本了解如何在代码中完全。 本文将指导完成一些基本的点，才能启动和运行，而仅限代码的用户界面开发。


-----

## <a name="creating-a-code-only-project"></a>创建仅限代码的项目

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="ios-blank-project-template"></a>iOS 空白项目模板

首先，使用 iPhone 的 Visual Studio 中创建 iOS 项目**空白项目**模板，如下所示，其中我们将扩展添加控制器和视图。


[![](ios-code-only-images/blankapp-vs.png "新建项目对话框")](ios-code-only-images/blankapp-vs.png#lightbox)


空项目模板将添加到项目的 4 个文件：


[![](ios-code-only-images/empty-project.png "项目文件")](ios-code-only-images/empty-project.png#lightbox)


1. **AppDelegate.cs** -包含`UIApplicationDelegate`子类化，请`AppDelegate`，用于处理从 iOS 的应用程序事件。 在中创建应用程序窗口`AppDelegate`的`FinishedLaunching`方法。
1. **Main.cs** -包含指定的类对应用程序的入口点为`AppDelegate`。
1. **Info.plist** -包含应用程序配置信息的属性列表文件。
1. **Entitlements.plist** – 包含有关功能和权限的应用程序信息的属性列表文件。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="ios-templates"></a>iOS 模板


适用于 Mac 的 visual Studio 不提供一个空的模板。 向 Apple 建议作为的主要方式创建 UI 的情节提要支持附带的所有模板。 但是，很可能在代码中完全创建你的 UI。 

下面的步骤将指导你完成删除从应用程序的情节提要： 


1. 使用单视图应用模板创建新的 iOS 项目：
    
    [![](ios-code-only-images/single-view-app.png "使用单个视图应用程序模板")](ios-code-only-images/single-view-app.png#lightbox)

1. 删除`Main.Storyboard`和`ViewController.cs`文件。 执行**不**删除`LaunchScreen.Storyboard`。 应删除视图控制器，因为它是在情节提要中创建的视图控制器背后的代码：
1. 请务必选择**删除**从弹出的对话框：
    
    [![](ios-code-only-images/delete.png "选择删除从弹出的对话框")](ios-code-only-images/delete.png#lightbox)

1. 在 Info.plist，删除内部信息**部署信息 > 主界面**选项：
    
    [![](ios-code-only-images/main-interface.png "删除内的主界面选项的信息")](ios-code-only-images/main-interface.png#lightbox)

1. 最后，以下代码添加到你`FinishedLaunching`AppDelegate 类中的方法：
        
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            // create a new window instance based on the screen size
            window = new UIWindow(UIScreen.MainScreen.Bounds);

            // make the window visible
            window.MakeKeyAndVisible();

            return true;
        }


-----

## <a name="creating-a-window"></a>创建的窗口

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

已添加到代码`FinishedLaunching`在步骤 3 更高版本，方法是最短为 iOS 应用程序创建窗口所需的代码。  

-----

使用生成 iOS 应用程序[MVC 模式](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#Model_View_Controller)。 从窗口的根视图控制器创建应用程序显示的第一个屏幕。 请参阅[Hello，iOS 多屏显示](~/ios/get-started/hello-ios-multiscreen/index.md)指导的 MVC 的详细信息模式本身。

实现`AppDelegate`添加通过模板创建应用程序窗口中，也不能那里仅有一个适用于每个 iOS 应用程序，使其可见替换为以下代码：

```csharp
public class AppDelegate : UIApplicationDelegate
{
    public override UIWindow Window
            {
                get;
                set;
            }

    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        window.MakeKeyAndVisible();

        return true;
    }
}
```

如果你打算运行此应用程序现在，你可能会获得消息，指出引发的异常`Application windows are expected to have a root view controller at the end of application launch`。 让我们添加控制器，并使其应用程序的根视图控制器。

## <a name="adding-a-controller"></a>添加控制器

你的应用程序可以包含许多视图控制器，但它需要有一个根视图控制器，以控制所有视图控制器。  将控制器添加到窗口中，通过创建`UIViewController`实例并将它设置为`window.RootViewController`属性：

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;

        Window.RootViewController = controller;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

每个控制器都有关联的视图，可从访问`View`属性。 上面的代码更改视图的`BackgroundColor`属性`UIColor.LightGray`，以便它将是可见的如下所示：

 [![](ios-code-only-images/image1.png "该视图的背景是可见的浅灰色")](ios-code-only-images/image1.png#lightbox)

我们无法设置任何`UIViewController`作为子类`RootViewController`以这种方式，包括从 UIKit，以及我们编写自己的控制器。 例如，下面的代码添加`UINavigationController`作为`RootViewController`:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;
        controller.Title = "My Controller";

        var navController = new UINavigationController(controller);

        Window.RootViewController = navController;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

这将产生嵌套在导航控制器，如下所示的控制器：

 [![](ios-code-only-images/image2.png "嵌套在导航控制器控制器")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>创建的视图控制器

现在，我们已了解如何添加与控制器`RootViewController`的窗口中，让我们了解如何在代码中创建的自定义视图控制器。

添加一个名为的新类`CustomViewController`如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](ios-code-only-images/customviewcontroller.png "添加一个名为 CustomViewController 的新类")](ios-code-only-images/customviewcontroller.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](ios-code-only-images/new-file.png "添加一个名为 CustomViewController 的新类")](ios-code-only-images/new-file.png#lightbox)

-----

此类应继承自`UIViewController`，后者位于`UIKit`命名空间，如所示：

```csharp
using System;
using UIKit;

namespace CodeOnlyDemo
{
    class CustomViewController : UIViewController
    {
    }
}
```

<a name="Initializing_the_View"/>

## <a name="initializing-the-view"></a>初始化视图

`UIViewController` 包含调用的方法`ViewDidLoad`视图控制器首次加载到内存时调用。 这是视图的一个适当的位置进行初始化，如设置它的属性。

例如，下面的代码将添加一个按钮和推送到导航堆栈新视图控制器，按下按钮的事件处理程序：

```csharp
using System;
using CoreGraphics;
using UIKit;

namespace CodyOnlyDemo
{
    public class CustomViewController : UIViewController
    {
        public CustomViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            View.BackgroundColor = UIColor.White;
            Title = "My Custom View Controller";

            var btn = UIButton.FromType (UIButtonType.System);
            btn.Frame = new CGRect (20, 200, 280, 44);
            btn.SetTitle ("Click Me", UIControlState.Normal);

            var user = new UIViewController ();
            user.View.BackgroundColor = UIColor.Magenta;

            btn.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (user, true);
            };

            View.AddSubview (btn);

        }
    }
}
```

若要加载在应用程序，此控制器，并演示的简单导航，创建的新实例`CustomViewController`。 创建新的导航控制器，传入你的视图控制器实例并设置新的导航控制器为窗口的`RootViewController`中`AppDelegate`像以前那样：

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

现在应用程序加载时，`CustomViewController`加载内导航控制器：

 [![](ios-code-only-images/customvc.png "CustomViewController 加载内导航控制器")](ios-code-only-images/customvc.png#lightbox)
 
单击此按钮时，将_推送_到导航堆栈新视图控制器：

[![](ios-code-only-images/customvca.png "在导航堆栈上推送新视图控制器")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>生成视图层次结构

在上面的示例中，我们已开始将在代码中创建用户界面，通过将按钮添加到视图控制器。

iOS 用户界面由组成查看层次结构。 作为子视图的某些父视图添加了其他视图，例如标签、 按钮、 滑块等。

例如，让我们编辑`CustomViewController`来创建用户可以在其中输入用户名和密码登录屏幕。 屏幕将包括两个文本字段和一个按钮。

### <a name="adding-the-text-fields"></a>添加文本字段

首先，删除按钮和事件处理程序中添加[初始化视图](#Initializing_the_View)部分。 

添加用于用户名的控件： 创建并初始化`UITextField`，然后将它添加到视图层次结构，，如下所示：

```csharp
class CustomViewController : UIViewController
{
    UITextField usernameField;

    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        View.BackgroundColor = UIColor.Gray;

        nfloat h = 31.0f;
        nfloat w = View.Bounds.Width;

        usernameField = new UITextField
        {
            Placeholder = "Enter your username",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 82, w - 20, h)
        };

        View.AddSubview(usernameField);
    }
}
```

当我们创建`UITextField`，我们设置`Frame`属性来定义其位置和大小。 在 iOS 0，0 坐标位于右侧与 + x 左上角和 + 向下的 y。 设置后`Frame`以及几个其他属性，我们调用`View.AddSubview`添加`UITextField`到视图层次结构。 这使得`usernameField`的子视图`UIView`实例`View`属性引用。 子视图添加具有高于其父视图，因此它显示在屏幕上的父视图的前面的 z 顺序。

与应用程序`UITextField`包含如下所示：

 [![](ios-code-only-images/image4.png "具有包含 UITextField 的应用程序")](ios-code-only-images/image4.png#lightbox)

我们可以添加`UITextField`以类似的方式的密码，仅此时间我们设置`SecureTextEntry`属性设置为 true，如下所示：

```csharp
public class CustomViewController : UIViewController
{
    UITextField usernameField, passwordField;
    public override void ViewDidLoad()
    {
       // keep the code the username UITextField
        passwordField = new UITextField
        {
            Placeholder = "Enter your password",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 114, w - 20, h),
            SecureTextEntry = true
        };

      View.AddSubview(usernameField); 
      View.AddSubview(passwordField);
   }
}

```

设置`SecureTextEntry = true`隐藏中输入的文本`UITextField`用户如下所示：

 [![](ios-code-only-images/image4a.png "设置 SecureTextEntry true 隐藏用户输入的文本")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>添加按钮

接下来，我们将添加一个按钮，以便用户可以提交的用户名和密码。 该按钮添加到任何其他控件，如查看层次结构上，通过将它作为自变量传递给父视图`AddSubview`再次方法。

下面的代码将添加按钮，并注册事件处理程序`TouchUpInside`事件：

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

与此就地，在登录屏幕现在出现如下所示：

 [![](ios-code-only-images/image5.png "登录屏幕")](ios-code-only-images/image5.png#lightbox)

与不同在以前版本的 iOS、 默认按钮背景是透明的。 更改按钮的`BackgroundColor`属性更改此：

```csharp
submitButton.BackgroundColor = UIColor.White;
```

这将导致正方形按钮，而不是典型的舍入有边的按钮。 若要获取圆角的边缘，请使用下面的代码段：

```csharp
submitButton.Layer.CornerRadius = 5f;
```

进行这些更改后，视图将如下所示：

[![](ios-code-only-images/image6.png "该视图将示例运行")](ios-code-only-images/image6.png#lightbox)
 
## <a name="adding-multiple-views-to-the-view-hierarchy"></a>将多个视图添加到视图层次结构

iOS 提供的工具，通过将多个视图添加到视图层次结构`AddSubviews`。

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton }); 
```

## <a name="adding-button-functionality"></a>添加按钮的功能

单击按钮时，你的用户会认为发生某些事件。 例如，会显示一个警告，或导航执行到另一个屏幕。 

让我们添加一些代码要推送到导航堆栈的第二个的视图控制器。

首先，创建第二个的视图控制器：

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

然后，将功能添加到`TouchUpInside`事件：

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

导航如下图所示：

[![](ios-code-only-images/navigation.png "在此图说明了导航")](ios-code-only-images/navigation.png#lightbox)

请注意，默认情况下，使用时是导航控制器时，iOS 使应用程序导航栏和后退按钮，可用于向后移动到堆栈。

## <a name="iterating-through-the-view-hierarchy"></a>循环访问查看层次结构

它可循环访问子视图层次结构并找出任何特定的视图。 例如，若要查找每个`UIButton`并为该按钮提供不同`BackgroundColor`，可以使用下面的代码段

```csharp
foreach(var subview in View.Subviews)
{
    if (subview is UIButton)
    {
         var btn = subview as UIButton;
         btn.BackgroundColor = UIColor.Green;
    }
}
```

此操作，但是将不起作用正在被循环的视图是否`UIView`如所有视图将都恢复为`UIView`为添加到父视图的对象本身继承`UIView`。

## <a name="handling-rotation"></a>处理旋转

如果用户旋转设备为横向，控件是否不相应地调整大小，如下面的屏幕截图所示：

 [![](ios-code-only-images/image7.png "如果用户旋转设备为横向，控件是否未相应地调整大小")](ios-code-only-images/image7.png#lightbox)

若要解决此问题的一种方法是通过设置`AutoresizingMask`上每个视图的属性。 在这种情况下，我们希望水平，拉伸的控件，因此我们将设置每个`AutoresizingMask`。 下面的示例为`usernameField`，但相同需要应用于每个视图层次结构中的小工具。

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

现在我们旋转的设备或模拟器时, 的所有内容拉伸以填充附加空间，如下所示：

 [![](ios-code-only-images/image8.png "所有控件都拉伸以填充额外的空间")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>创建自定义视图

除了使用属于 UIKit 的控件，还可以使用自定义视图。 可以通过继承创建自定义视图`UIView`和重写`Draw`。 让我们创建的自定义视图，并将其添加到视图层次结构，以演示。

### <a name="inheriting-from-uiview"></a>从 UIView 继承

我们需要做的第一件事是创建自定义视图的类。 我们将执行这使用**类**添加空类名为 Visual Studio 中的模板`CircleView`。 基类应设置为`UIView`，我们撤回它处于`UIKit`命名空间。 我们还需要`System.Drawing`以及命名空间。 其他各种`System.*`命名空间将不会在此示例中使用，因此请尝试删除它们。

类应如下所示：

```csharp
using System;

namespace CodeOnlyDemo
{
    class CircleView : UIView
    {
    }
}
```

### <a name="drawing-in-a-uiview"></a>在 UIView 中绘制

每个`UIView`具有`Draw`时它需要绘制由系统调用的方法。 `Draw` 应永远不会直接调用。 它是在运行的循环处理过程中由系统调用您的问题。 首次通过运行循环后视图添加到视图层次结构，其`Draw`调用方法。 后续调用`Draw`视图被标记为无需通过调用绘制时发生`SetNeedsDisplay`或`SetNeedsDisplayInRect`视图上。

我们可以通过添加内部重写此类代码绘制代码添加到我们视图`Draw`方法如下所示：

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    //get graphics context
    using (var g = UIGraphics.GetCurrentContext())
    {
        // set up drawing attributes
        g.SetLineWidth(10.0f);
        UIColor.Green.SetFill();
        UIColor.Blue.SetStroke();

        // create geometry
        var path = new CGPath();
        path.AddArc(Bounds.GetMidX(), Bounds.GetMidY(), 50f, 0, 2.0f * (float)Math.PI, true);

        // add geometry to graphics context and draw
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);
    }
}
```

由于`CircleView`是`UIView`，我们还可以设置`UIView`以及属性。 例如，我们可以设置`BackgroundColor`构造函数中：

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

若要使用`CircleView`我们刚刚创建，我们可以或者将其添加为子视图到视图层次结构中现有的控制器，正如我们做与`UILabels`和`UIButton`更早版本，或者我们可以为新的控制器视图直接加载它。 让我们执行后面的操作。

### <a name="loading-a-view"></a>加载视图

 `UIViewController` 具有一个名为方法`LoadView`由要创建其视图的控制器。 这是一个适当的位置，以创建视图，并将其分配到的控制器`View`属性。

首先，我们需要一个控制器，因此，创建一个名为的新的空类`CircleController`。

在`CircleController`添加以下代码以设置`View`到`CircleView`(不应调用`base`实现重写中的):

```csharp
using UIKit;

namespace CodeOnlyDemo
{
    class CircleController : UIViewController
    {
        CircleView view;

        public override void LoadView()
        {
            view = new CircleView();
            View = view;
        }
    }
}
```

最后，我们需要在运行时提供的控制器。 让我们执行此操作通过上我们添加了更早版本、，如下所示的提交按钮添加事件处理程序：

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

现在，我们运行该应用程序后，点击提交按钮时显示的圆圈该新视图：

 [![](ios-code-only-images/circles.png "显示与某一圆形的新视图")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>创建启动屏幕

A[启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)作为一种方式向其可以响应用户显示你的应用程序启动时显示。 加载你的应用程序时显示启动屏幕，因为它无法创建在代码中这是因为应用程序仍被加载到内存。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

当你创建的 iOS 项目在 Visual Studio 中，启动屏幕为你的.xib 文件中，这可在窗体中提供**资源**中你的项目文件夹。 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

当你在 Visual Studio 中创建 iOS 项目，为 Mac、 启动屏幕为你的情节提要文件的形式提供。 

-----

这可以通过 double 单击它并在 iOS 设计器中打开进行编辑。

Apple 建议的.xib 或情节提要文件适用于面向 iOS 8 的应用程序或更高版本，当你启动 iOS 设计器中的或者文件时，你将使用大小类和自动布局以适应你的布局，使其看起来很好，并为所有设备均正确，显示大小。 静态的启动映像可以除了.xib 或情节提要，用于允许对应用程序面向早期版本的支持。

有关创建启动屏幕的详细信息，请参阅下面的文档：

- [创建使用.xib 启动屏幕](https://developer.xamarin.com/recipes/ios/general/templates/launchscreen-xib/)
- [管理与情节提要的启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> **注意：**截至 iOS 9，Apple 建议情节提要应用作创建启动屏幕的主要方法。

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>创建启动映像的预 iOS 8 的应用程序

如果你的应用程序面向 iOS 8 之前的版本可以使用静态图像除了.xib 或情节提要启动屏幕。 

可以将设置此静态图像，在 Info.plist 文件中，或在你的应用程序 （适用于 iOS 7) 资产目录。 你将需要为你的应用程序可能运行在每个设备大小 （320 x 480、 640 x 960、 640 x 1136） 提供单独的图像。 在启动屏幕尺寸上的详细信息，请查看[启动屏幕图像](~/ios/app-fundamentals/images-icons/launch-screens.md)指南。

> [!IMPORTANT]
> **注意：**如果你的应用程序没有启动屏幕，你可能注意到，无法完全适合屏幕。 如果出现这种情况，你应确保至少，包含名为 640 x 1136 映像`Default-568@2x.png`到你 Info.plist。 



## <a name="summary"></a>摘要

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

本文讨论了如何开发以编程方式在 Visual Studio 中的 iOS 应用程序。 我们介绍了如何生成一个项目的空项目模板，讨论如何创建和根视图控制器添加到窗口。 然后，我们介绍了如何使用控件从 UIKit 创建视图中的层次结构控制器来开发应用程序屏幕。 接下来，我们探讨了如何以使视图布局相应地在不同的方向和我们已了解如何创建自定义视图，通过子类化`UIView`，以及加载控制器中的视图。 最后，我们探讨了如何将启动屏幕添加到应用程序。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

本文讨论了如何开发 iOS 应用程序以编程方式在 Visual Studio for mac。 我们介绍了如何从单一视图模板，将项目生成讨论如何创建和根视图控制器添加到窗口。 然后，我们介绍了如何使用控件从 UIKit 创建视图中的层次结构控制器来开发应用程序屏幕。 接下来，我们探讨了如何以使视图布局相应地在不同的方向和我们已了解如何创建自定义视图，通过子类化`UIView`，以及加载控制器中的视图。 最后，我们探讨了如何将启动屏幕添加到应用程序。

-----




## <a name="related-links"></a>相关链接

- [SimpleLogin （示例）](https://developer.xamarin.com/samples/monotouch/SimpleLogin)
