---
title: "消息应用扩展基础知识"
description: "本文演示如何在 Xamarin.iOS 解决方案与邮件应用集成并向用户呈现的新功能包括消息应用扩展。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d9b6b5a778e0e4d5092d1036109f82896acf639b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="message-app-extension-basics"></a>消息应用扩展基础知识

_本文演示如何在 Xamarin.iOS 解决方案与邮件应用集成并向用户呈现的新功能包括消息应用扩展。_

新与消息应用扩展集成到 iOS 10、**消息**应用并显示向用户的新功能。 扩展可以发送文本、 标签、 媒体文件和交互式消息。

## <a name="about-message-app-extensions"></a>有关消息应用扩展

与如上所述，集成消息应用扩展**消息**应用并显示向用户的新功能。 扩展可以发送文本、 标签、 媒体文件和交互式消息。 两种类型的消息应用扩展有：

- **不干胶标签包**-包含用户可以向消息中添加的不干胶标签的集合。 不干胶标签包可以创建无需编写任何代码。
- **iMessage 应用**-可以显示自定义用户界面中选择不干胶标签、 输入文本，包括 （带可选类型转换） 的媒体文件和创建、 编辑和发送交互消息的消息应用。

消息应用扩展提供了三种主要的内容类型：

- **交互式消息**-是一种类型的应用程序生成的自定义消息内容，当用户点击在消息中，应用程序将在前台中启动。
- **无需标贴**-是可以包含的用户之间发送的消息在该应用程序生成的图像。
- **其他支持的内容**-应用程序可提供内容，例如照片、 视频、 文本或任何其他内容的链接类型始终通过邮件应用受支持。

新到 iOS 10，消息应用程序现在包括其自己专用的内置应用商店。 包含消息应用扩展任何应用将显示，并且可以提升此存储中。 新的消息应用下拉列表将显示已从要向用户提供快速访问的消息应用程序存储区已下载的任何应用。

此外新在 iOS 10，Apple 添加了内联应用归属这样用户就可以轻松地发现应用程序。 例如，如果一个用户将内容发送到另一个，从第二个用户不具有的应用安装 （如例如标签） 发送的应用的名称被列在下面的消息历史记录中的内容。 如果用户点击应用程序的名称，我们打开消息应用商店和存储区中选择的应用程序。

消息应用扩展是类似于现有 iOS 应用，开发人员是熟悉创建，并且它们将有权访问所有标准框架和一个标准的 iOS 应用的功能。 例如:

- 他们有权应用内购买。
- 它们有权访问 Apple Pay。
- 他们有权设备硬件，如相机。

在 iOS 10 上才支持消息应用扩展，但是，这些扩展发送的内容是可在 watchOS 和 macOS 设备上查看。 新_最近页_添加到 watchOS 3 时，将显示最近不干胶标签已发送从手机上，包括从消息应用扩展，并允许用户从手表发送这些不干胶标签。

## <a name="about-the-messages-framework"></a>有关消息 Framework

新到 iOS 10，消息 framework 所提供的用户的 iOS 设备上的消息应用程序扩展和消息应用程序之间的接口。 当用户启动应用程序从内部邮件应用时，此框架允许应用被发现，并提供数据和布局其 UI 所需的上下文。

一旦启动应用时，在用户交互用来创建新的内容共享通过一条消息。 然后，该应用使用消息 framework 来将新创建的内容传输到适用于处理的消息应用。

消息框架和消息应用扩展的构建基于预先存在的 iOS 应用程序扩展技术。 有关应用程序扩展的详细信息，请参阅 Apple 的[应用程序扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)。

与其他 Apple 提供整个系统的扩展点，开发人员不必为其消息应用扩展提供主机应用程序，因为该消息应用程序自身充当容器。 但是，开发人员可以包括一个新的或现有的 iOS 应用内消息应用程序扩展并将其传送以及捆绑包的选项。

如果消息的应用程序扩展包含在 iOS 应用的捆绑包，在设备的主屏幕和从消息应用内消息应用抽屉中，将显示应用程序的图标。 如果它未包含在应用捆绑包，消息应用抽屉中将仅显示消息应用程序扩展。

即使不包括主机应用程序捆绑中的消息应用扩展，开发人员将需要提供消息应用程序扩展捆绑中的应用程序图标，因为这是将在诸如消息应用抽屉或设置对系统其他部分中显示的图标为扩展。

## <a name="about-stickers"></a>有关不干胶标签

可以将它们附加到会话内的上一个消息气泡或 Apple 设计不干胶标签作为要发送消息的任何其他内容作为内联 iMessage 用户通信通过允许不干胶标签的新方法。

不干胶标签有哪些？

- 它们是消息应用程序扩展提供的映像。
- 它们可以是动态或静态映像。
- 它们提供共享映像的内容从应用程序内的新方法。

有两种方法创建不干胶标签：

1. 不干胶标签包消息应用扩展可从创建在 Xcode 内而不包含任何代码。 只需要是适用于所有标签和应用程序图标的资产。
2. 通过创建标准的消息应用程序扩展，它提供通过消息 framework 代码中的标签。

### <a name="creating-sticker-packs"></a>创建不干胶标签包

不干胶标签包从一个特定的模板在 Xcode 内创建，只需提供一组静态图像资产可用作不干胶标签。 如上面所述，它们不需要的任何代码，开发人员只需将图像文件拖动到不干胶标签资产目录内的不干胶标签包文件夹。

要在不干胶标签包中包含的图像，它必须满足以下要求：

- 映像必须为 PNG、 APNG、 GIF 或 JPEG 格式。 Apple 提供的建议提供不干胶标签资产时使用仅的 PNG 和 APNG 格式。
- 动画不干胶标签仅支持的 APNG 和 GIF 格式。
- 由于可以将它们放置在会话中的消息气泡用户，不干胶标签映像应提供透明背景。
- 单独的图像文件必须是少于 500 kb。
- 映像不能小于 100 x 100 点或更大，它 206 x 206 指向。

> [!IMPORTANT]
> **注意：**不干胶标签映像应始终提供在`@3x`300 x 300 到 618 x 618 像素范围内的解决方法。 系统将自动生成`@2x`和`@1x`在根据需要运行时的版本。

Apple 提供的建议测试针对各种不同 （如空白、 黑色、 红色、 黄色和多颜色） 的彩色的背景和超过照片不干胶标签图像资产，以确保它们在所有可能的情况下显示最佳。

不干胶标签包可以提供以下三种可用大小之一不干胶标签：

- **小**-100 x 100 点。
- **中等**-136 x 136 点。 这是默认大小。
- **大型**-206 x 206 点。

使用 Xcode 的属性检查器将大小设置为整个不干胶标签包，仅向提供匹配的请求的大小，在邮件应用不干胶标签浏览器中的最佳结果的图像资产。

有关详细信息，请参阅我们[冰激凌生成器](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)应用和 Apple 的[消息引用](https://developer.apple.com/reference/messages)。

## <a name="creating-a-custom-sticker-experience"></a>创建自定义不干胶标签体验

如果应用需要更多控制或灵活性比不干胶标签包提供，它可以包括消息应用扩展，并提供自定义不干胶标签体验通过消息 framework 不干胶标签。

创建自定义不干胶标签体验的好处是什么？

1. 允许应用自定义标签到应用程序的用户的显示方式。 例如，若要在非标准的网格布局或不同的彩色背景上的格式中的存在不干胶标签。
2. 允许代码而不是作为静态图像资产包含从动态创建无需标贴。
3. 允许不干胶标签图像资产动态要下载从开发人员的 web 服务器而无需发布到应用商店的新版本。
4. 允许创建不干胶标签上快速访问的设备的照相机。
5. 允许应用内购买的因此用户可以购买从应用程序内的多个标签。

创建自定义不干胶标签体验，请执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 启动 Visual Studio for mac。
2. 打开消息应用程序将扩展添加到解决方案。 
3. 选择**iOS** > **扩展** > **iMessage 扩展**单击**下一步**按钮： 

    [![](intro-to-message-app-extensions-images/message01.png "选择 iMessage 扩展")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. 输入**扩展名**单击**下一步**按钮： 

    [![](intro-to-message-app-extensions-images/message02.png "输入扩展名称")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. 单击**创建**按钮以生成扩展： 

    [![](intro-to-message-app-extensions-images/message03.png "单击创建按钮")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 启动 Visual Studio。
2. 打开消息应用程序将扩展添加到解决方案。 
3. 选择**iOS** > **扩展** > **iMessage 扩展**单击**下一步**按钮： 

    [![](intro-to-message-app-extensions-images/message01w.png "选择 iMessage 扩展")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. 输入**扩展名**单击**确定**按钮

-----

默认情况下，`MessagesViewController.cs`文件将添加到解决方案。 这是到扩展的主入口点，它继承自`MSMessageAppViewController`类。

消息框架提供了用于向用户显示可用不干胶标签的类：

- `MSStickerBrowserViewController` -控制不干胶标签显示的视图。 它还符合`IMSStickerBrowserViewDataSource`接口返回不干胶标签数和给定的浏览器索引不干胶标签。
- `MSStickerBrowserView` -这是可用不干胶标签将显示在视图。
- `MSStickerSize` -决定不干胶标签显示在浏览器视图的网格的各个单元格大小。

### <a name="creating-a-custom-sticker-browser"></a>创建自定义不干胶标签浏览器

开发人员可以进一步自定义的不干胶标签体验用户通过提供自定义不干胶标签浏览器 (`MSMessageAppBrowserViewController`) 消息应用程序扩展中。 自定义不干胶标签浏览器更改如何不干胶标签时它们要选择要包含在消息流中不干胶标签会呈现给用户。

请执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**解决方案 Pad**，右键单击扩展的项目名称，然后选择**添加** > **新文件...**  >  **iOS |Apple Watch** > **接口控制器**。
2. 输入`StickerBrowserViewController`为**名称**单击**新建**按钮： 

    [![](intro-to-message-app-extensions-images/browser01.png "为名称输入 StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. 打开`StickerBrowserViewController.cs`文件进行编辑。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在**解决方案资源管理器**，右键单击扩展的项目名称，然后选择**添加** > **新文件...**  >  **iOS |Apple Watch** > **接口控制器**。
2. 输入`StickerBrowserViewController`为**名称**单击**新建**按钮： 

    [![](intro-to-message-app-extensions-images/browser01w.png "为名称输入 StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. 打开`StickerBrowserViewController.cs`文件进行编辑。

-----

请`StickerBrowserViewController.cs`如下所示：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MonkeyStickers
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MSStickerSize stickerSize) : base (stickerSize)
        {
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            ...
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();
        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            return Stickers[(int)index];
        }
        #endregion
    }
}
```

详细了解上面的代码。 它为扩展提供了不干胶标签创建一个存储区：

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

并重写两个方法的`MSStickerBrowserViewController`类以提供此数据存储从浏览器的数据：

```csharp
public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
{
    return Stickers.Count;
}

public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
{
    return Stickers[(int)index];
}
```

`CreateSticker`方法获取从扩展的捆绑的图像资产的路径，并使用它来创建的新实例`MSSticker`从此资产，它将添加到集合：

```csharp
private void CreateSticker (string assetName, string localizedDescription)
{

    // Get path to asset
    var path = NSBundle.MainBundle.PathForResource (assetName, "png");
    if (path == null) {
        Console.WriteLine ("Couldn't create sticker {0}.", assetName);
        return;
    }

    // Build new sticker
    var stickerURL = new NSUrl (path);
    NSError error = null;
    var sticker = new MSSticker (stickerURL, localizedDescription, out error);
    if (error == null) {
        // Add to collection
        Stickers.Add (sticker);
    } else {
        // Report error
        Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
    }
}
```

`LoadSticker`方法调用从`ViewDidLoad`从命名的图像资产 （包括在应用的捆绑包） 创建不干胶标签并将其添加到不干胶标签的集合。

若要实现自定义不干胶标签浏览器，编辑`MessagesViewController.cs`文件并使其如下所示：

```csharp
using System;
using UIKit;
using Messages;

namespace MonkeyStickers
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public StickerBrowserViewController BrowserViewController { get; set;}
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();


            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);
        }
        #endregion
    }
}
```

它先详细了解一下此代码，创建一个自定义浏览器的存储区：

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

然后在`ViewDidLoad`方法，它实例化并配置新的浏览器：

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

然后它将浏览器添加到视图以显示它：

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>进一步不干胶标签自定义

进一步不干胶标签自定义可通过在消息应用扩展包括仅使用两个类：

- `MSStickerView`
- `MSSticker`

使用上述方法，扩展插件可以支持不干胶标签不依赖于标准不干胶标签浏览器方法的所选内容。 此外，可以两种不同的视图模式之间切换不干胶标签显示：

- **Compact** -这是默认模式不干胶标签视图在哪里采用消息视图底部 25%。
- **展开**-不干胶标签视图填充整个消息视图。

此不干胶标签视图可以通过来切换这模式之间以编程方式或手动用户。

看看下面的示例的处理两个不同的视图模式之间进行切换。 两个不同的视图控制器将需要为每个状态。 `StickerBrowserViewController`句柄**Compact**视图，如下所示类似于以下：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MessageExtension
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set; }
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MessagesViewController messagesAppViewController, MSStickerSize stickerSize) : base (stickerSize)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("add", "Add New Sticker");
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();

        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            // Wanting to add a new sticker?
            if (index == 0) {
                // Yes, ask controller to present add sticker interface
                MessagesAppViewController.AddNewSticker ();
                return null;
            } else {
                // No, return existing sticker
                return Stickers [(int)index];
            }
        }
        #endregion
    }
}
```

`AddStickerViewController`将处理**扩展**不干胶标签视图和外观如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using Messages;

namespace MessageExtension
{
    public class AddStickerViewController : UIViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set;}
        public MSSticker NewSticker { get; set;}
        #endregion

        #region Constructors
        public AddStickerViewController (MessagesViewController messagesAppViewController)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Override Method
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Build interface to create new sticker
            var cancelButton = new UIButton (UIButtonType.RoundedRect);
            cancelButton.TouchDown += (sender, e) => {
                // Cancel add new sticker
                MessagesAppViewController.CancelAddNewSticker ();
            };
            View.AddSubview (cancelButton);

            var doneButton = new UIButton (UIButtonType.RoundedRect);
            doneButton.TouchDown += (sender, e) => {
                // Add new sticker to collection
                MessagesAppViewController.AddStickerToCollection (NewSticker);
            };
            View.AddSubview (doneButton);
            
            ...
        }
        #endregion
    }
}
```

`MessageViewController`实现这些视图控制器来驱动请求的状态：

```csharp
using System;
using UIKit;
using Messages;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public bool IsAddingSticker { get; set;}
        public StickerBrowserViewController BrowserViewController { get; set; }
        public AddStickerViewController AddStickerController { get; set;}
        #endregion

        #region Constructors
        protected MessagesViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Public Methods
        public void PresentStickerBrowser ()
        {
            // Is the Add sticker view being displayed?
            if (IsAddingSticker) {
                // Yes, remove it from view
                AddStickerController.RemoveFromParentViewController ();
                AddStickerController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);

            // Save mode
            IsAddingSticker = false;
        }

        public void PresentAddSticker ()
        {
            // Is the sticker browser being displayed?
            if (!IsAddingSticker) {
                // Yes, remove it from view
                BrowserViewController.RemoveFromParentViewController ();
                BrowserViewController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (AddStickerController);
            AddStickerController.DidMoveToParentViewController (this);
            View.AddSubview (AddStickerController.View);

            // Save mode
            IsAddingSticker = true;
        }

        public void AddNewSticker ()
        {
            // Switch to expanded view mode
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void CancelAddNewSticker ()
        {
            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }

        public void AddStickerToCollection (MSSticker sticker)
        {
            // Add sticker to collection
            BrowserViewController.Stickers.Add (sticker);

            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (this, MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Create new Add controller and configure it as well
            AddStickerController = new AddStickerViewController (this);
            AddStickerController.View.Frame = View.Frame;

            // Initially present the sticker browser
            PresentStickerBrowser ();
        }

        public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            base.DidTransition (presentationStyle);

            // Take action based on style
            switch (presentationStyle) {
            case MSMessagesAppPresentationStyle.Compact:
                PresentStickerBrowser ();
                break;
            case MSMessagesAppPresentationStyle.Expanded:
                PresentAddSticker ();
                break;
            }
        }
        #endregion
    }
}
```

当用户请求将新不干胶标签添加到其可用的集合，一个新`AddStickerViewController`可见控制器和不干胶标签视图进入**扩展**视图：

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

当用户，选择要添加不干胶标签时，则将它添加到其可用的集合和**Compact**请求视图：

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
}
```

`DidTransition`重写方法以处理以下两种模式之间切换：

```csharp
public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
{
    base.DidTransition (presentationStyle);

    // Take action based on style
    switch (presentationStyle) {
    case MSMessagesAppPresentationStyle.Compact:
        PresentStickerBrowser ();
        break;
    case MSMessagesAppPresentationStyle.Expanded:
        PresentAddSticker ();
        break;
    }
}
``` 

## <a name="summary"></a>摘要

本文已覆盖与集成的 Xamarin.iOS 解决方案中包括消息应用扩展**消息**应用程序和向用户存在新功能。 它涵盖使用扩展发送文本、 标签、 媒体文件和交互式消息。



## <a name="related-links"></a>相关链接

- [冰激凌生成器 （示例）](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [消息引用](https://developer.apple.com/reference/messages)
- [应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
