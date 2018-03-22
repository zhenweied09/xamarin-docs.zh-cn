---
title: "3D Touch 简介"
description: "本文介绍如何使用新应用程序中的 iPhone 6s 和 iPhone 6s Plus 3D Touch 笔势。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: c5cd2671bb66aa89117012fe394bb724f7e22e1a
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="introduction-to-3d-touch"></a>3D Touch 简介

_本文介绍如何使用新应用程序中的 iPhone 6s 和 iPhone 6s Plus 3D Touch 笔势。_

[![](3d-touch-images/info01.png "3D Touch 的示例启用的应用")](3d-touch-images/info01.png#lightbox)

本文将提供并介绍了如何使用新的 3D Touch Api 将压力敏感手势添加到你的 Xamarin.iOS 应用运行新的 iPhone 上 6s 和 iPhone 6s Plus 设备。

与 3D Touch iPhone 应用现能够不仅告诉用户处理设备的屏幕，但它就可以感测 exerting 用户多少压力和响应的不同压力级别。

3D Touch 提供了向应用程序的以下功能：

- [压力敏感度](#Pressure-Sensitivity)-现在可以硬测量应用程序或用户轻接触屏幕并采取利用该信息。
  例如，绘制应用可以进行行之更宽或更薄根据用户硬触摸屏幕。
- [查看和 Pop](#Peek-and-Pop) -您的应用程序现在可以让用户使用其数据进行交互而无需导航超出其当前上下文。 通过按硬屏幕屏幕上，它们可以查看他们感兴趣 （如预览一条消息） 的项。 通过按更难，它们可以弹出到项。
- [快速操作](#Quick-Actions)-认为的快速之类的操作的上下文菜单，可以将弹出型时用户右键单击桌面应用程序中的项。
  使用快速操作，你可以添加快捷方式函数中你的应用程序直接从主页屏幕上的应用图标。
- [在模拟器中测试 3D Touch](#Testing-3D-Touch-in-the-Simulator) -与正确的 Mac 硬件，你可以在 iOS 模拟器中测试 3D Touch 启用应用程序。

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>压力敏感度

如前所述，使用新属性[UITouch](https://developer.xamarin.com/api/type/UIKit.UITouch/)类可以测量的用户是否将应用于 iOS 设备的屏幕的压力，还可以在你的用户界面中使用此信息。 例如，使画笔笔画更半透明或不透明基于压力的量。

[![](3d-touch-images/pressure01.png "呈现为多半透明或不透明画笔笔画基于压力量")](3d-touch-images/pressure01.png#lightbox)

由于 3D Touch，如果在 iOS 9 （或更高版本） 上运行你的应用和 iOS 设备是否可以支持 3D Touch，压力中的更改将导致`TouchesMoved`引发事件。

例如，当监视`TouchesMoved`事件[UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)，你可以使用以下代码获取用户是否将应用于屏幕的当前压力：

```csharp
public override void TouchesMoved (NSSet touches, UIEvent evt)
{
    base.TouchesMoved (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        // Get the pressure
        var force = touch.Force;
        var maxForce = touch.MaximumPossibleForce;

        // Do something with the touch and the pressure
        ...
    }
}
```

`MaximumPossibleForce`属性返回的最大可能值`Force`属性[UITouch](https://developer.xamarin.com/api/type/UIKit.UITouch/)根据运行应用程序的 iOS 设备。

> [!IMPORTANT]
> 压力中的更改将导致`TouchesMoved`事件被引发，即使 X / Y 坐标，未更改。 由于此更改的行为，而您 iOS 应用程序应准备为`TouchesMoved`事件要调用比较频繁，以及 x / Y 坐标与上一相同`TouchesMoved`调用。




有关详细信息，请参阅 Apple 的[TouchCanvas： 高效且有效地使用 UITouch](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/)示例应用程序和[UITouch 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/)。

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>查看和弹出

3D Touch 提供了用户在应用程序比以往任何时候，更快的信息与交互而无需从其当前位置导航的新方式。

例如，如果你的应用程序显示的消息表，用户可以按硬上一个项可以预览中覆盖区上查看其内容 (它是 Apple 指作为*查看*)。

[![](3d-touch-images/peekandpop01.png "下面举例说明扫视内容")](3d-touch-images/peekandpop01.png#lightbox)

如果用户按下更难，他们将输入正则消息视图 (这称为*弹出*-到视图的 ping)。

### <a name="checking-for-3d-touch-availability"></a>3D Touch 可用性检查

使用时[UIViewController]()可以使用下面的代码 iOS 设备运行应用程序是否支持 3D Touch:

```csharp
public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
{
    //Important: call the base function
    base.TraitCollectionDidChange(previousTraitCollection);

    //See if the new TraitCollection value includes force touch
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        //Do something with 3D touch, for instance...
        RegisterForPreviewingWithDelegate (this, View);
        ...
```

可能在之前调用此方法*后或* `ViewDidLoad()`。 

### <a name="handling-peek-and-pop"></a>处理扫视和 Pop

可以处理 3D Touch 到 iOS 设备，我们可以使用的实例`UIViewControllerPreviewingDelegate`类以处理显示**查看**和**弹出**项详细信息。 例如，如果我们有表视图控制器调用`MasterViewController `我们可以使用以下代码来支持**查看**和**弹出**:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace DTouch
{
    public class PreviewingDelegate : UIViewControllerPreviewingDelegate
    {
        #region Computed Properties
        public MasterViewController MasterController { get; set; }
        #endregion

        #region Constructors
        public PreviewingDelegate (MasterViewController masterController)
        {
            // Initialize
            this.MasterController = masterController;
        }

        public PreviewingDelegate (NSObjectFlag t) : base(t)
        {
        }

        public PreviewingDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        /// Present the view controller for the "Pop" action.
        public override void CommitViewController (IUIViewControllerPreviewing previewingContext, UIViewController viewControllerToCommit)
        {
            // Reuse Peek view controller for details presentation
            MasterController.ShowViewController(viewControllerToCommit,this);
        }

        /// Create a previewing view controller to be shown at "Peek".
        public override UIViewController GetViewControllerForPreview (IUIViewControllerPreviewing previewingContext, CGPoint location)
        {
            // Grab the item to preview
            var indexPath = MasterController.TableView.IndexPathForRowAtPoint (location);
            var cell = MasterController.TableView.CellAt (indexPath);
            var item = MasterController.dataSource.Objects [indexPath.Row];

            // Grab a controller and set it to the default sizes
            var detailViewController = MasterController.Storyboard.InstantiateViewController ("DetailViewController") as DetailViewController;
            detailViewController.PreferredContentSize = new CGSize (0, 0);

            // Set the data for the display
            detailViewController.SetDetailItem (item);
            detailViewController.NavigationItem.LeftBarButtonItem = MasterController.SplitViewController.DisplayModeButtonItem;
            detailViewController.NavigationItem.LeftItemsSupplementBackButton = true;

            // Set the source rect to the cell frame, so everything else is blurred.
            previewingContext.SourceRect = cell.Frame;

            return detailViewController;
        }
        #endregion
    }
}
```

`GetViewControllerForPreview`方法用于执行**查看**操作。 它可以访问的表单元格和备份数据，然后加载`DetailViewController`从当前情节提要。 通过设置`PreferredContentSize`为 (0，0) 中，我们会要求默认**查看**视图大小。 最后，我们模糊除我们显示与该单元格的所有内容`previewingContext.SourceRect = cell.Frame`和我们返回显示的新视图。

`CommitViewController`重复使用我们在中创建的视图**查看**为**弹出**查看当用户按下更难。

### <a name="registering-for-peek-and-pop"></a>查看和 Pop 注册

从我们想要允许用户视图控制器**查看**和**弹出**中的项，我们需要此服务注册。 在上述示例中的表视图控制器 (`MasterViewController`)，我们将使用以下代码：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Regiser for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

此处我们正在呼叫`RegisterForPreviewingWithDelegate`与的实例方法`PreviewingDelegate`我们在上面创建。 在支持 3D Touch 的 iOS 设备，用户可以按硬上查看它一个项。 如果他们按甚至更难，项将弹出到标准其中显示视图。

有关详细信息，请参阅我们[iOS 9 ApplicationShortcuts 示例](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/)和 Apple 的[ViewControllerPreviews： 使用预览 Api UIViewController](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html)示例应用， [UIPreviewAction 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/)， [UIPreviewActionGroup 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/)和[UIPreviewActionItem 协议参考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/)。

<a name="Quick-Actions" />

## <a name="quick-actions"></a>快速操作

使用 3D Touch 和快速操作，你可以添加常见、 快速而简单函数的访问快捷方式应用程序中从在 iOS 设备上的主页屏幕图标。

如上面所述，你可以快速操作将类似，可以将弹出型时用户右键单击桌面应用程序中的项的上下文菜单。 你应使用快速操作提供的最常见的函数或你的应用程序的功能的快捷方式。

[![](3d-touch-images/quickactions01.png "下面举例说明的快速操作菜单")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>定义静态快速操作

如果一个或多个你的应用所需的快速操作都是静态的并且不需要更改，您可以定义它们中应用的`Info.plist`文件。 此编辑器中编辑文件外部并添加以下项：

```xml
<key>UIApplicationShortcutItems</key>
<array>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeSearch</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will search for an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Search</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.000</string>
    </dict>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeShare</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will share an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Share</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.001</string>
    </dict>
</array>
```

此处我们要包含以下键定义两个静态快速操作项：

- `UIApplicationShortcutItemIconType` -定义将由快速操作项显示为以下值之一的图标：
  - `UIApplicationShortcutIconTypeAdd`
  - `UIApplicationShortcutIconTypeAlarm`
  - `UIApplicationShortcutIconTypeAudio`
  - `UIApplicationShortcutIconTypeBookmark`
  - `UIApplicationShortcutIconTypeCapturePhoto`
  - `UIApplicationShortcutIconTypeCaptureVideo`
  - `UIApplicationShortcutIconTypeCloud`
  - `UIApplicationShortcutIconTypeCompose`
  - `UIApplicationShortcutIconTypeConfirmation`
  - `UIApplicationShortcutIconTypeContact`
  - `UIApplicationShortcutIconTypeDate`
  - `UIApplicationShortcutIconTypeFavorite`
  - `UIApplicationShortcutIconTypeHome`
  - `UIApplicationShortcutIconTypeInvitation`
  - `UIApplicationShortcutIconTypeLocation`
  - `UIApplicationShortcutIconTypeLove`
  - `UIApplicationShortcutIconTypeMail`
  - `UIApplicationShortcutIconTypeMarkLocation`
  - `UIApplicationShortcutIconTypeMessage`
  - `UIApplicationShortcutIconTypePause`
  - `UIApplicationShortcutIconTypePlay`
  - `UIApplicationShortcutIconTypeProhibit`
  - `UIApplicationShortcutIconTypeSearch`
  - `UIApplicationShortcutIconTypeShare`
  - `UIApplicationShortcutIconTypeShuffle`
  - `UIApplicationShortcutIconTypeTask`
  - `UIApplicationShortcutIconTypeTaskCompleted`
  - `UIApplicationShortcutIconTypeTime`
  - `UIApplicationShortcutIconTypeUpdate`

        ![](3d-touch-images/uiapplicationshortcuticontype.png "UIApplicationShortcutIconType imagery")

* `UIApplicationShortcutItemSubtitle` -定义项副标题。
* `UIApplicationShortcutItemTitle` -定义项的标题。
* `UIApplicationShortcutItemType` -是一个字符串值，我们将使用它来标识我们的应用程序中的项。 有关详细信息，请参阅下一节。

> [!IMPORTANT]
> 在中设置的快速操作快捷项目`Info.plist`文件无法访问与`Application.ShortcutItems`属性。 它们仅传入到`HandleShortcutItem`事件处理程序。 





### <a name="identifying-quick-action-items"></a>标识快速操作项

根据前面的当你快速操作项中定义你的应用`Info.plist`，分配到一个字符串值`UIApplicationShortcutItemType`键来标识它们。

为了更轻松地在代码中使用这些标识符，将添加名为的类`ShortcutIdentifier`到应用程序的项目，并使其如下所示：

```csharp
using System;

namespace AppSearch
{
    public static class ShortcutIdentifier
    {
        public const string First = "com.company.appname.000";
        public const string Second = "com.company.appname.001";
        public const string Third = "com.company.appname.002";
        public const string Fourth = "com.company.appname.003";
    }
}
```

<a name="Handling-a-Quick-Action" />

### <a name="handling-a-quick-action"></a>处理快速操作

接下来，你需要修改应用程序的`AppDelegate.cs`文件，以处理用户从主页屏幕上的应用程序的图标选择快速操作项。

请进行以下编辑：

```csharp
using System;
...

public UIApplicationShortcutItem LaunchedShortcutItem { get; set; }

public bool HandleShortcutItem(UIApplicationShortcutItem shortcutItem) {
    var handled = false;

    // Anything to process?
    if (shortcutItem == null) return false;

    // Take action based on the shortcut type
    switch (shortcutItem.Type) {
    case ShortcutIdentifier.First:
        Console.WriteLine ("First shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Second:
        Console.WriteLine ("Second shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Third:
        Console.WriteLine ("Third shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Fourth:
        Console.WriteLine ("Forth shortcut selected");
        handled = true;
        break;
    }

    // Return results
    return handled;
}

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    return shouldPerformAdditionalDelegateHandling;
}

public override void OnActivated (UIApplication application)
{
    // Handle any shortcut item being selected
    HandleShortcutItem(LaunchedShortcutItem);

    // Clear shortcut after it's been handled
    LaunchedShortcutItem = null;
}

public override void PerformActionForShortcutItem (UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
{
    // Perform action
    completionHandler(HandleShortcutItem(shortcutItem));
}
```

首先，我们定义一个公共`LaunchedShortcutItem`属性来对用户跟踪所选的最后一快速操作项。 然后，我们重写`FinishedLaunching`方法和，请参阅如果`launchOptions`已传递和如果它们包含快速操作项。 如果他们这样做，我们将存储中的快速操作`LaunchedShortcutItem`属性。

接下来，我们重写`OnActivated`方法并传入到快速启动项选择任何`HandleShortcutItem`要处理的方法。 我们仅写入到的消息当前**控制台**。 在实际应用中，将处理任何操作是必需的。 执行操作后，`LaunchedShortcutItem`属性处于未选中状态。

最后，如果你的应用程序已经在运行，`PerformActionForShortcutItem`将调用方法来处理快速操作项，因此我们需要重写此方法并调用我们`HandleShortcutItem`方法以及此处。


### <a name="creating-dynamic-quick-action-items"></a>创建动态快速操作项

除了定义静态快速操作项应用程序中`Info.plist`文件，你可以创建动态上快速快速操作。 若要定义两个新的动态快速操作，编辑你`AppDelegate.cs`再次文件并修改`FinishedLaunching`方法看起来类似于以下：

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    // Add dynamic shortcut items
    if (application.ShortcutItems.Length == 0) {
        var shortcut3 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Third, "Play") {
            LocalizedSubtitle = "Will play an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Play)
        };

        var shortcut4 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Fourth, "Pause") {
            LocalizedSubtitle = "Will pause an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Pause)
        };

        // Update the application providing the initial 'dynamic' shortcut items.
        application.ShortcutItems = new UIApplicationShortcutItem[]{shortcut3, shortcut4};
    }

    return shouldPerformAdditionalDelegateHandling;
}
```

现在我们将检查以查看是否`application`已包含的一组动态创建`ShortcutItems`，如果不是，我们将创建两个新`UIMutableApplicationShortcutItem`对象以定义新项目并将其添加到`ShortcutItems`数组。

我们已在中添加代码[处理快速操作](#Handling-a-Quick-Action)上面一节将处理这些动态快速操作一样静态的。

应注意的是，你可以创建静态和动态快速操作项的组合 （如这里我们要做），你并不局限于一个或另一个。

有关详细信息，请我们[iOS 9 ViewControllerPreview 示例](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/)，请参阅 Apple 的[ApplicationShortcuts： 使用 UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/)示例应用， [UIApplicationShortcutItem 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/)， [UIMutableApplicationShortcutItem 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/)和[UIApplicationShortcutIcon 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/)。

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>在模拟器中测试的 3D Touch

当在具有强制 Touch 的兼容 Mac 上使用最新版本 Xcode 以及 iOS 模拟器启用 trackpad 时，你可以在模拟器中测试 3D Touch 功能。

若要启用此功能，请在支持 3D Touch 的模拟的 iPhone 硬件运行任何应用程序 (iPhone 6s 和更高版本)。 接下来，选择**硬件**在 iOS 模拟器和启用菜单**3D touch 的使用 Trackpad Force**菜单项：

[![](3d-touch-images/simulator01.png "选择 iOS 模拟器中的硬件菜单并启用 3D touch 菜单项使用 Trackpad Force")](3d-touch-images/simulator01.png#lightbox)

使用此活动的功能，你可以按更难 Mac 的 trackpad 上启用 3D Touch 就像在真实 iPhone 硬件上。

## <a name="summary"></a>总结

这篇文章引入了新 3D Touch 的 Api 可在 iOS 9 iPhone 6s 和 iPhone 6s Plus。 它涉及到应用程序; 的添加压力敏感度使用扫视和 Pop 来快速显示从而无需导航; 当前上下文中应用程序信息以及使用快速操作以提供您的应用程序的快捷方式的最常用的功能。



## <a name="related-links"></a>相关链接

- [iOS 9 ViewControllerPreview 示例](https://developer.xamarin.com/samples/monotouch/ios9/ViewControllerPreview/)
- [iOS 9 ApplicationShortcuts 示例](https://developer.xamarin.com/samples/monotouch/ios9/ApplicationShortcuts/)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [使你的 iPhone 应用可供 3D Touch](https://developer.apple.com/ios/3d-touch/)
