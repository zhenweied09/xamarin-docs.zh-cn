---
title: 选项卡条和选项卡栏控制器
description: 使用 UITabBarController 类生成 iOS 应用程序使用 tab 键导航用户界面。 在本文中我们将演练如何设置的选项卡式应用程序包含若干个控制器和视图。 然后，我们将查看如何加载 UITabBarController 时它不是根控制器，如在登录屏幕后。
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: c3c57cceed7271ebbe707172db892a246003426b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="tab-bars-and-tab-bar-controllers"></a>选项卡条和选项卡栏控制器

_使用 UITabBarController 类生成 iOS 应用程序使用 tab 键导航用户界面。在本文中我们将演练如何设置的选项卡式应用程序包含若干个控制器和视图。然后，我们将查看如何加载 UITabBarController 时它不是根控制器，如在登录屏幕后。_

在 iOS 中使用选项卡式应用程序以支持用户界面，然后可以访问多个屏幕顺序不分先后。 通过`UITabBarController`类，应用程序可以轻松地将对这种多屏幕方案的支持。 `UITabBarController` 负责多屏幕管理，从而使应用程序开发人员可以专注于每个屏幕的详细信息。

通常情况下，选项卡式应用程序都用生成`UITabBarController`正在`RootViewController`的主窗口。 但是，稍加附加代码，选项卡式应用程序还可连续到其他一些初始屏幕，例如其中应用程序首先提供登录屏幕，跟选项卡式界面的方案。

我们将检查通过简单的应用程序的演练一起使用此处的选项卡。 然后，我们将探讨如何使用选项卡中非`RootViewController`方案。

## <a name="introducing-uitabbarcontroller"></a>引入 UITabBarController

`UITabBarController`支持通过以下选项卡式应用程序开发：

-  允许多个控制器添加到它。
-  通过提供选项卡式的用户界面，`UITabBar`类中，以允许用户控制器和其视图之间切换。 


控制器将被添加到`UITabBarController`通过其`ViewControllers`属性，它是`UIViewController`数组。 `UITabBarController`本身处理加载正确的控制器并将提供其基于选定的选项卡的视图。

选项卡为的实例`UITabBarItem`类，该类中包含`UITabBar`实例。 每个`UITabBar`实例是可通过访问`TabBarItem`的每个选项卡中的控制器的属性。

若要了解如何使用`UITabBarController`，让我们演练一下生成的简单应用程序使用一个。

## <a name="tabbed-application-walkthrough"></a>选项卡式应用程序演练

在本演练中我们将创建以下应用程序：

[![](creating-tabbed-applications-images/00-app.png "示例选项卡式应用程序")](creating-tabbed-applications-images/00-app.png#lightbox)

尽管没有已选项卡式应用程序模板可适用于 Mac，Visual Studio 中用于此示例中，我们将从一个空的项目，以获得更好地理解的应用程序的构造方式工作。

 <a name="Creating_the_Application" />


### <a name="creating-the-application"></a>创建应用程序

让我们首先创建新的应用程序。

选择**文件 > 新建 > 解决方案**Visual Studio 中用于 Mac 和选择的菜单项**iOS > 应用程序 > 空项目**模板，将项目`TabbedApplication`，如下所示：

[![](creating-tabbed-applications-images/newsolution1.png "选择空项目模板")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "将项目 TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)



### <a name="adding-the-uitabbarcontroller"></a>添加 UITabBarController

接下来，通过选择添加一个空类**文件 > 新建文件**并选择**常规： 空类**模板。 命名该文件`TabController`如下所示：

[![](creating-tabbed-applications-images/02-newclass.png "添加 TabController 类")](creating-tabbed-applications-images/02-newclass.png#lightbox)

`TabController`类将包含的实现`UITabBarController`用于将管理的数组`UIViewControllers`。 当用户选择一个选项卡，`UITabBarController`将会负责处理提供适当的视图控制器的视图。

若要实现`UITabBarController`我们需要执行以下操作：

1.  设置的基本类`TabController`到`UITabBarController`。 
1.  创建`UIViewController`要添加到实例`TabController`。 
1.  添加`UIViewController`到数组分配给实例`ViewControllers`属性`TabController`。 


以下代码添加到`TabController`类来实现这些步骤：

```csharp
using System;
using UIKit;

namespace TabbedApplication {
        public class TabController : UITabBarController {

                UIViewController tab1, tab2, tab3;

                public TabController ()
                {
                        tab1 = new UIViewController();
                        tab1.Title = "Green";
                        tab1.View.BackgroundColor = UIColor.Green;

                        tab2 = new UIViewController();
                        tab2.Title = "Orange";
                        tab2.View.BackgroundColor = UIColor.Orange;

                        tab3 = new UIViewController();
                        tab3.Title = "Red";
                        tab3.View.BackgroundColor = UIColor.Red;

                        var tabs = new UIViewController[] {
                                tab1, tab2, tab3
                        };

                        ViewControllers = tabs;
                }
        }
}
```

请注意，每个`UIViewController`实例，我们将设置`Title`属性`UIViewController`。 当控制器将被添加到`UITabBarController`、`UITabBarController`将读取`Title`针对每个控制器并将其关联选项卡的标签上显示，如下所示：

[![](creating-tabbed-applications-images/00-app.png "运行示例应用程序")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>作为 RootViewController 设置 TabController

控制器位于选项卡的顺序对应于将它们添加到的顺序`ViewControllers`数组。

若要获取`UITabController`若要加载作为第一个屏幕中，我们需要使其窗口的`RootViewController`中的以下代码所示`AppDelegate`:

```csharp
[Register ("AppDelegate")]
        public partial class AppDelegate : UIApplicationDelegate
        {
                UIWindow window;
                TabController tabController;

                public override bool FinishedLaunching (UIApplication app, NSDictionary options)
                {
                        window = new UIWindow (UIScreen.MainScreen.Bounds);

                        var tabController = new TabController ();
                        window.RootViewController = tabController;

                        window.MakeKeyAndVisible ();
            
                        return true;
                }
        }
```

如果我们运行应用程序现在，`UITabBarController`将使用默认情况下选择第一个选项卡的加载。 导致关联的控制器选择任何其他选项卡查看提供的`UITabBarController,`其中最终用户选择了第二个选项卡如下所示：

[![](creating-tabbed-applications-images/03-secondtab.png "显示的第二个选项卡")](creating-tabbed-applications-images/03-secondtab.png#lightbox)

 <a name="Modifying_TabBarItems" />


### <a name="modifying-tabbaritems"></a>修改 TabBarItems

现在，我们已经运行选项卡上的应用程序，让我们修改`TabBarItem`若要更改的图像和文本显示，以及将一个标记添加到选项卡之一。

 <a name="Setting_a_System_Item" />


#### <a name="setting-a-system-item"></a>设置系统项

首先，让我们设置要使用系统项的第一个选项卡。 构造函数中`TabController`，删除设置的控制器的行`Title`为`tab1`实例，并将其替换为以下代码以将控制器的设置`TabBarItem`属性：

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

在创建时`UITabBarItem`使用`UITabBarSystemItem`，标题和图像自动提供 ios，如下面显示的屏幕截图中所示**收藏夹**图标和标题的第一个选项卡上：

 ![](creating-tabbed-applications-images/04a-tabimage.png "具有星形图标的第一个选项卡")

 <a name="Setting_the_Title_and_Image" />


#### <a name="setting-the-title-and-image"></a>设置标题和图像

除了使用系统项的标题和映像的`UITabBarItem`可以将设置为自定义值。 例如，更改设置的代码`TabBarItem`属性命名的控制器`tab2`，如下所示：

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

上面的代码假定了名为映像`second.png`for mac。 添加到 Visual Studio 中项目的根目录 实际上，我们已添加三个图像对我们项目中，以涵盖所有的设备解决方法，如下所示：

 [![](creating-tabbed-applications-images/tabbedimages7new.png "添加到项目的映像")](creating-tabbed-applications-images/tabbedimages7new.png#lightbox)

选项卡图像应为与正常解析，60 x 60 作为高分辨率和 iPhone 6 90 x 90 的透明度 30 x 30 png 加上解析。 在代码中，我们只需加载名为的文件`second.png`和 iOS 将自动加载一个具有 Retina 显示的设备上的高分辨率。 你可以阅读有关此信息[处理映像](~/ios/app-fundamentals/images-icons/index.md)指南。 默认情况下选项卡栏项显示为灰色，用蓝色浅色当选中。

**注意**

上述图像还将添加到**资源**目录，它是一个专门的目录，其内容将自动复制到应用程序捆绑包的根目录：

[![](creating-tabbed-applications-images/tabbedapplication8.png "映像作为资源")](creating-tabbed-applications-images/tabbedapplication8.png#lightbox)

此外，当我们设置`Title`属性直接基于`TabBarItem`，它将重写为设置任何值`Title`本身在控制器上。

当我们运行应用程序现在时，第二个选项卡显示我们的自定义标题和图像如下所示：

[![](creating-tabbed-applications-images/05-customtab.png "具有一个正方形图标的第二个选项卡")](creating-tabbed-applications-images/05-customtab.png#lightbox)

 <a name="Setting_the_Badge_Value" />


#### <a name="setting-the-badge-value"></a>设置徽章值

选项卡还可以显示一个标记。 例如，添加代码以将徽章设置第三个选项卡上的以下行：

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

运行此命令会导致具有字符串"Hi"选项卡如下所示的左上角的红色标签：

[![](creating-tabbed-applications-images/06-badge.png "具有 Hi 徽章的第二个选项卡")](creating-tabbed-applications-images/06-badge.png#lightbox)

徽章通常用于显示未读的数字指示新项。 若要删除徽章，设置`BadgeValue`为 null，如下所示：

```csharp
tab3.TabBarItem.BadgeValue = null;
```

 <a name="Tabs_in_Non-RootViewController_Scenarios" />


## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>在非 RootViewController 方案中的选项卡

在上面的示例中，我们介绍了如何使用`UITabBarController`时将其`RootViewController`的窗口。 在此示例中我们将学习如何使用`UITabBarController`它不是`RootViewController`和显示如何创建此使用情节提要。

 <a name="Initial_Screen_Example" />


### <a name="initial-screen-example"></a>初始屏幕示例

对于此方案，初始屏幕加载从控制器不是`UITabBarController`。 用户交互与屏幕通过点击按钮时，相同的视图控制器将被加载到`UITabBarController`，其随后呈现给用户。 以下屏幕截图显示应用程序流：

[![](creating-tabbed-applications-images/inital-screen-application.png "此屏幕截图中显示的应用程序流")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

让我们开始新的应用程序对于此示例。 同样，我们将使用**iPhone > 应用程序 > 空项目 (C#)**模板，此时命名项目`InitialScreenDemo`。


在此示例中我们将需要以保存我们查看控制器情节提要。 若要添加情节提要：

- 右键单击项目名称，然后选择**添加 > 新文件**。

- 新文件对话框出现时，导航到**iOS > 空 iPhone 情节提要**。

让我们调用此新情节提要**MainStoryboard** ，如下所示： 

[![](creating-tabbed-applications-images/new-file-dialog.png "向项目添加 MainStoryboard 文件")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

有几个重要的步骤时将添加情节提要到以前非-情节提要文件，如中所述，要注意[简介情节提要](~/ios/user-interface/storyboards/index.md)指南。 这些是：

 
1. 添加到你的情节提要名称**主界面**部分`Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "将主接口设置为 MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. 在你`App Delegate`，重写窗口方法，替换为以下代码：

    ```csharp
    public override UIWindow Window {
      get;
      set;
    }
    ```

我们将此示例需要三个视图控制器。 一个名为`ViewController1`，将用于为我们初始视图控制器，并在第一个选项卡中。与其他两个，名为`ViewController2`和`ViewController3`，将使用它在第二个和第三个选项卡中分别。

双击 MainStoryboard.storyboard 文件中，打开设计器拖到设计图面的三个视图控制器。 我们希望这些视图控制器具有其自身与更高版本，名称对应的类的每个这样的情况下，在**标识 > 类**，键入其名称，如下面的屏幕截图中所示：

[![](creating-tabbed-applications-images/class-name.png "将类设置为 ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

适用于 Mac 的 visual Studio 将自动生成的类和设计器所需的文件，这可以看到在解决方案区中，如下所示：

[![](creating-tabbed-applications-images/solution-pad2.png "自动生成的项目中的文件")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

 <a name="Creating_the_UI" />


#### <a name="creating-the-ui"></a>创建 UI

接下来，我们将为每个 ViewController 的视图，使用 Xamarin iOS 设计器创建简单的用户界面。

我们想要拖动`Label`和`Button`到从 ViewController1**工具箱**右侧。 接下来我们将使用属性填充编辑名称及以下的控件的文本：

-  **标签**: `Text`  = **一个**
-  **按钮**: `Title`  = **用户可以采取一些初始操作**


我们将控制在我们按钮的可见性`TouchUpInside`事件，，我们需要在后面的代码中引用它。 让我们识别与它**名称**`aButton`在属性区中，如下面的屏幕截图中所示：

[![](creating-tabbed-applications-images/abutton-properties.png "将名称设置为在属性本 aButton")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

设计图面应类似于下面的屏幕截图：

[![](creating-tabbed-applications-images/design-surface1.png "设计图面现在应类似于此屏幕截图")](creating-tabbed-applications-images/design-surface1.png#lightbox)

让我们添加到少量的更详细地`ViewController2`和`ViewController3`，通过将标签添加到每台，并分别将文本更改为两个和三个。 这样可以突出显示给用户我们正在查看的选项卡/视图。

#### <a name="wiring-up-the-button"></a>方式设置按钮

我们将加载`ViewController1`应用程序的第一次启动。 当用户点击按钮时，我们将隐藏按钮，并加载`UITabBarController`与`ViewController1`实例中第一个选项卡。

当用户释放`aButton`，我们希望 TouchUpInside 事件来触发。 让我们选择按钮，然后在**事件选项卡**的属性填充，声明事件处理程序 – `InitialActionCompleted` – 因此可以在代码中引用。 下面的屏幕截图所示：

[![](creating-tabbed-applications-images/event-handler.png "当用户释放 aButton 时，触发 TouchUpInside 事件")](creating-tabbed-applications-images/event-handler.png#lightbox)

现在，我们需要告诉视图控制器时，事件将激发隐藏按钮`InitialActionCompleted`。 在`ViewController1`，添加下面的分部方法：

```csharp
partial void InitialActionCompleted (UIButton sender)
    {
      aButton.Hidden = true;  
    }
```

保存该文件，并运行应用程序。 我们将看到的屏幕其中一个出现和消失修饰上的按钮。

#### <a name="adding-the-tab-bar-controller"></a>添加选项卡栏控制器

我们现在有我们按预期方式工作的初始视图。 接下来，我们想要将其添加到`UITabBarController`，以及视图 2 和 3。 让我们在设计器中打开情节提要。

在**工具箱**，搜索**选项卡栏控制器**控制器和对象下并将其拖到设计图面上。 如下面的屏幕截图所示，选项卡栏控制器是无用户界面并因此使两个与其默认情况下的视图控制器：

[![](creating-tabbed-applications-images/tabbarcontroller.png "将选项卡栏控制器添加到布局")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

通过选择底部的黑色栏，然后按 delete 来删除这些新视图控制器。

在我们情节提要，我们可以使用 Segues 处理 TabBarController 和我们的视图控制器之间的转换。 后与初始视图进行交互，我们想要将其装载到 TabBarController 呈现给用户。 让我们对此进行设置设计器中。

**Ctrl 键并单击**和**拖**从 TabBarController 到按钮。 在鼠标弹起上将显示上下文菜单。 我们想要使用模式 segue。 
 
若要将每个我们选项卡上，设置**ctrl 键并单击**从一到三个，然后选择关系到每个顺序我们查看控制器 TabBarController**选项卡**从上下文菜单，如下所示：

[![](creating-tabbed-applications-images/context-menu.png "选择的选项卡关系")](creating-tabbed-applications-images/context-menu.png#lightbox)

将情节提要应类似于下面的屏幕截图：

[![](creating-tabbed-applications-images/segue-layout.png "情节提要应类似于此屏幕截图")](creating-tabbed-applications-images/segue-layout.png#lightbox)

如果我们单击其中一个选项卡栏项，浏览属性面板中，你可以看到大量的不同选项，如下所示：

[![](creating-tabbed-applications-images/properties-panel.png "在属性资源管理器中设置的选项卡选项")](creating-tabbed-applications-images/properties-panel.png#lightbox)

我们可以使用此编辑某些属性，如徽章、 标题和 iOS[标识符](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html)，其他

如果我们保存并运行应用程序现在，我们将找到按钮随即 ViewController1 实例加载到 TabBarController 时再次出现。 让我们解决此问题通过检查以查看当前的视图是否有父视图控制器。 如果是这样，我们知道我们位于 TabBarController，并因此按钮应被隐藏。 让我们将以下代码添加到 ViewController1 类：

```csharp
public override void ViewDidLoad ()
{
     if (ParentViewController != null){
       aButton.Hidden = true;
     }

}
```

加载时在应用程序运行和用户点击按钮在第一个屏幕上，UITabBarController，与从放置在第一个选项卡，如下所示的第一个屏幕的视图：

[![](creating-tabbed-applications-images/first-view.png "示例应用程序输出")](creating-tabbed-applications-images/first-view.png#lightbox)

<!--Save the files and run the application:

[![](creating-tabbed-applications-images/inital-screen-application.png "Save the files and run the application")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)-->

## <a name="summary"></a>总结

本文介绍了如何使用`UITabBarController`应用程序中。 我们在遍历如何载入每个选项卡上的控制器，以及如何在选项卡上设置属性，此类的标题、 映像和徽章。 我们然后检查，使用情节提要，如何加载`UITabBarController`在运行时不时`RootViewController`的窗口。


## <a name="related-links"></a>相关链接

- [创建选项卡式应用程序 （示例）](https://developer.xamarin.com/samples/monotouch/CreatingTabbedApplications/)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [UITabBarController 类引用](http://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
