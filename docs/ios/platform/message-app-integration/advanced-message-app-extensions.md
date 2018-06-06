---
title: 在 Xamarin.iOS 的高级的消息应用扩展
description: 这篇文章演示用于使用与邮件应用集成并向用户呈现的新功能的 Xamarin.iOS 解决方案中的消息应用程序扩展的高级的技术。
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: eca3dcc19714251184092fbe136d0078f1ca8d54
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788010"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>在 Xamarin.iOS 的高级的消息应用扩展

_这篇文章演示用于使用与邮件应用集成并向用户呈现的新功能的 Xamarin.iOS 解决方案中的消息应用程序扩展的高级的技术。_


新与消息应用扩展集成到 iOS 10、**消息**应用并显示向用户的新功能。 扩展可以发送文本、 标签、 媒体文件和交互式消息。

## <a name="about-message-app-extensions"></a>有关消息应用扩展

与如上所述，集成消息应用扩展**消息**应用并显示向用户的新功能。 扩展可以发送文本、 标签、 媒体文件和交互式消息。 两种类型的消息应用扩展有：

- **不干胶标签包**-包含用户可以向消息中添加的不干胶标签的集合。 不干胶标签包可以创建无需编写任何代码。
- **iMessage 应用**-可以显示自定义用户界面中选择不干胶标签、 输入文本，包括 （带可选类型转换） 的媒体文件和创建、 编辑和发送交互消息的消息应用。

消息应用扩展提供了三种主要的内容类型：

- **交互式消息**-是一种类型的应用程序生成的自定义消息内容，当用户点击在消息中，应用程序将在前台中启动。
- **无需标贴**-是可以包含的用户之间发送的消息在该应用程序生成的图像。 请参阅我们[冰激凌生成器](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)不干胶标签包应用的示例实现的示例应用程序。
- **其他支持的内容**-应用程序可提供内容如照片、 视频、 文本或始终通过邮件应用已支持该类型的链接。

新到 iOS 10，消息应用程序现在包括其自己专用的内置应用商店。 包含消息应用扩展任何应用将显示，并且可以提升此存储中。 新的消息应用下拉列表将显示已从要向用户提供快速访问的消息应用程序存储区已下载的任何应用。

此外新在 iOS 10，Apple 添加了内联应用归属这样用户就可以轻松地发现应用程序。 例如，如果一个用户将内容发送到另一个，从第二个用户不具有的应用安装 （如例如标签） 发送的应用的名称被列在下面的消息历史记录中的内容。 如果用户点击应用程序的名称，我们打开消息应用商店和存储区中选择的应用程序。

消息应用扩展是类似于现有 iOS 应用，开发人员很熟悉创建，并且它们将有权访问所有标准框架和一个标准的 iOS 应用的功能。 例如：

- 他们有权应用内购买。
- 它们有权访问 Apple Pay。
- 他们有权设备硬件，如相机。

在 iOS 10 上才支持消息应用扩展，但是，这些扩展发送的内容是可在 watchOS 和 macOS 设备上查看。 新_最近页_添加到 watchOS 3 时，将显示最近不干胶标签已发送从手机上，包括从消息应用扩展，并允许用户从手表发送这些不干胶标签。

## <a name="about-interactive-messages"></a>有关交互式消息

交互式消息提供自定义消息气泡，以及由消息应用扩展。 它们允许用户创建交互式消息内容，此消息输入字段中插入它，并将其发送。

[![](advanced-message-app-extensions-images/interactive01.png "创建交互式消息内容")](advanced-message-app-extensions-images/interactive01.png#lightbox)

接收的用户可以答复交互式消息点击消息历史记录，若要创建消息应用程序扩展加载中其消息气泡。 该扩展将启动的全屏幕并允许用户撰写答复并将其发送回原始的用户。

[![](advanced-message-app-extensions-images/interactive02.png "扩展启动全屏幕")](advanced-message-app-extensions-images/interactive02.png#lightbox)


下面将详细介绍了以下主题：

- 消息 API 概述
- 扩展生命周期
- 编写一条消息
- 发送消息

## <a name="messages-api-overview"></a>消息 API 概述

当用户调用，消息应用扩展将显示在底部的紧凑视图模式中的消息历史记录：

[![](advanced-message-app-extensions-images/interactive03.png "消息 API 概述")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. `MSMessageAppViewController`消息应用程序扩展中的对象是向用户显示的扩展视图时调用的主类。
2. 向为用户显示会话`MSConversation`对象实例。
3. `MSMessage`类表示会话中给定的消息气泡。
4. `MSSession` 控制如何发送一条消息。
5. `MSMessageTemplateLayout` 控制消息的显示方式

## <a name="the-extension-lifecycle"></a>扩展生命周期

看一看变为活动状态消息应用扩展的过程：

[![](advanced-message-app-extensions-images/interactive04.png "变为活动状态消息应用扩展的过程")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. 扩展是在启动时 （例如从应用抽屉中），消息应用程序将启动的进程。
2. `DidBecomeActive`方法调用该方法并传递`MSConversation`表示会话中运行消息应用扩展。
3. 因为基于扩展`UIViewController`同时`ViewWillAppear`和`ViewDidAppear`调用。

接下来，看看变得停用消息应用扩展的过程：

[![](advanced-message-app-extensions-images/interactive05.png "在过程变得停用消息应用扩展")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. 当正在停用消息应用扩展时，`ViewWillDisappear`将首先调用方法。
2. 则`ViewDidDisappear`将调用方法。
3. `WillResignActive`方法调用该方法并传递`MSConversation`表示会话中运行消息应用扩展。 此时，消息应用和扩展之间的连接是将要发布。
4. 在某些更高版本的时候，进程将被终止的邮件应用。

扩展是一个短生存期的过程，因为它是主动地终止系统以节省处理和电池电量。 开发人员应记住这一点设计和实现消息应用扩展时。

## <a name="composing-a-message"></a>编写一条消息

一旦消息应用扩展的进程中运行，并具有显示其用户界面，可以使用下面的代码编写一条新消息：

```csharp
MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
{
    var components = new NSUrlComponents {
        QueryItems = iceCream.QueryItems
    };

    var layout = new MSMessageTemplateLayout {
        Image = iceCream.RenderSticker (true),
        Caption = caption
    };

    var message = new MSMessage (session ?? new MSSession()) {
        Url = components.Url,
        Layout = layout
    };

    return message;
}
```

此代码创建一个新`MSMessage`并设置几个属性 (如`Url`)。 虽然仅可以在 iOS 上创建消息，它可发送到 iOS 和 macOS 显示。

如果用户单击消息气泡在 macOS 上会话中，Mac 将尝试打开 web 浏览器中的 URL 中指定的地址。 因此，开发人员网站应能够显示某些消息表示形式在 macOS 上使用 web 浏览器基于机。

`AccessibilityLabel`屏幕阅读器使用属性来读取到用户的会话的脚本。 `Layout`属性指定如何将显示消息，当前仅`MSMessageTemplateLayout`支持和如下所示：

[![](advanced-message-app-extensions-images/interactive06.png "MSMessageTemplateLayout 模板")](advanced-message-app-extensions-images/interactive06.png#lightbox)

`Image`属性`MSMessageTemplateLayout`主屏幕上 MessageBubble 正文提供内容。 `MediaFileUrl`属性还消息气泡，正文提供内容，但不是支持的内容允许`UIImage`（如在后台将循环的视频文件）。 如果这两个`Image`和`MediaFileUrl`提供了属性，`Image`属性将优先。 `MediaFileUrl`支持 PNG、 JPEG、 GIF 和视频 （采用可以播放媒体播放器框架的任何格式） 的媒体格式。

建议的媒体大小为 3 x 分辨率 300 x 300 像素。 也可以接受略有更大、 更小的资产并 Apple 提供的建议测试了几个不同的大小，以获得最佳结果。 消息应用程序将向下示例和缩放根据需要此媒体。

当资产发送到接收方时，附加任何媒体，将自动消息应用程序以对其进行优化传输从通过网络转码操作。 因此，Apple 不鼓励包括的媒体，因为将按比例缩小媒体，并将其压缩进行传输，附加到消息中的文本这样可能会呈现文本模糊不清。

`ImageTitle`和`ImageSubtitle`属性提供消息气泡中提供的媒体的说明。 这些属性将作为发送文本到接收的设备，它们将清晰地呈现在映像左下角。

`Caption`， `SubCaption`，`TrailingCaption`和`TrailingSubcaption`属性进一步描述映像，并将在下图中的某个部分中呈现。 设置所有这些属性设置为`null`将创建一个消息气泡，而无需标题区域：

[![](advanced-message-app-extensions-images/interactive07.png "不标题区域的情况下一个消息气泡")](advanced-message-app-extensions-images/interactive07.png#lightbox)

最后一个需要注意的事项是，邮件应用将在消息气泡的左上角绘制消息应用程序扩展的图标。

## <a name="sending-a-message"></a>发送消息

一次`MSMessage`已组合，可以使用下面的代码向它发送：

```csharp
public void SendMessage (MSMessage message)
{
    // Insert the message into the conversation
    ActiveConversation.InsertMessage (message, (error) => {
        // Did the message send successfully?
        if (error == null) {
            // Handle successful send
        } else {
            // Report Error
            Console.WriteLine ("Error: {0}", error);
        }
    };

}
```

`ActiveConversation`属性`MSMessagesAppViewController`将保存当前消息应用扩展已在中启动的会话。

调用`InsertMessage`的`MSConversation`以包括会话中的消息并处理，可能会出现任何错误。 如果消息已成功包含，则将在输入字段中显示消息气泡。

此外，扩展可以将不同类型的数据发送到如会话：

- **文本** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **附件** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **无需标贴** -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});`其中`sticker`是`MSSticker`。

后新内容上输入字段中，用户就能够将消息发送通过点击蓝色**发送**按钮 （就像典型的任何消息）。 没有为要自动发送内容的消息应用程序扩展方法，此过程是完全受控制的用户。

## <a name="handling-the-compact-and-expanded-modes"></a>处理的压缩和展开模式

消息应用扩展可以显示在两种不同的视图模式之一：

[![](advanced-message-app-extensions-images/interactive08.png "在两个不同的视图模式中显示一个消息应用程序扩展： 压缩和扩展")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -这是默认模式消息应用扩展在哪里采用消息视图底部 25%。 在 Compact 模式下，应用程序没有访问键盘、 水平滚动或轻扫笔势识别器。 应用于输入字段的确有权限访问并调用`InsertMessage`立即将向用户显示。
- **展开**-消息应用扩展填充整个消息视图。 它不到输入字段中，具有访问权限，但确实有权访问键盘、 水平滚动和轻扫笔势识别器。

消息应用扩展可以切换这些模式之间以编程方式或手动由用户在任何时间，并且应该立即响应任何更改在视图模式。

看看下面的示例的处理两个不同的视图模式之间进行切换。 两个不同的视图控制器将需要为每个状态。 `StickerBrowserViewController`句柄**Compact**视图和`AddStickerViewController`将处理**扩展**视图：

```csharp
using System;

using Messages;
using Foundation;
using UIKit;

namespace MessagesExtension {
    public partial class MessagesViewController : MSMessagesAppViewController, IIceCreamsViewControllerDelegate, IBuildIceCreamViewControllerDelegate {
        public MessagesViewController (IntPtr handle) : base (handle)
        {
        }

        public override void WillBecomeActive (MSConversation conversation)
        {
            base.WillBecomeActive (conversation);

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, PresentationStyle);
        }

        public override void WillTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected an active converstation");

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, presentationStyle);
        }

        void PresentViewController (MSConversation conversation, MSMessagesAppPresentationStyle presentationStyle)
        {
            // Determine the controller to present.
            UIViewController controller;

            if (presentationStyle == MSMessagesAppPresentationStyle.Compact) {
                // Show a list of previously created ice creams.
                controller = InstantiateIceCreamsController ();
            } else {
                var iceCream = new IceCream (conversation.SelectedMessage);
                controller = iceCream.IsComplete ? InstantiateCompletedIceCreamController (iceCream) : InstantiateBuildIceCreamController (iceCream);
            }

            foreach (var child in ChildViewControllers) {
                child.WillMoveToParentViewController (null);
                child.View.RemoveFromSuperview ();
                child.RemoveFromParentViewController ();
            }

            AddChildViewController (controller);
            controller.View.Frame = View.Bounds;
            controller.View.TranslatesAutoresizingMaskIntoConstraints = false;
            View.AddSubview (controller.View);

            controller.View.LeftAnchor.ConstraintEqualTo (View.LeftAnchor).Active = true;
            controller.View.RightAnchor.ConstraintEqualTo (View.RightAnchor).Active = true;
            controller.View.TopAnchor.ConstraintEqualTo (View.TopAnchor).Active = true;
            controller.View.BottomAnchor.ConstraintEqualTo (View.BottomAnchor).Active = true;

            controller.DidMoveToParentViewController (this);
        }

        UIViewController InstantiateIceCreamsController ()
        {
            // Instantiate a `IceCreamsViewController` and present it.
            var controller = Storyboard.InstantiateViewController (IceCreamsViewController.StoryboardIdentifier) as IceCreamsViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an IceCreamsViewController from the storyboard");

            controller.Builder = this;
            return controller;
        }

        UIViewController InstantiateBuildIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (BuildIceCreamViewController.StoryboardIdentifier) as BuildIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an BuildIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            controller.Builder = this;

            return controller;
        }

        public UIViewController InstantiateCompletedIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (CompletedIceCreamViewController.StoryboardIdentifier) as CompletedIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an CompletedIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            return controller;
        }

        public void DidSelectAdd (IceCreamsViewController controller)
        {
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void Build (BuildIceCreamViewController controller, IceCreamPart iceCreamPart)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected a conversation");

            var iceCream = controller.IceCream;
            if (iceCream == null)
                throw new Exception ("Expected the controller to be displaying an ice cream");

            string messageCaption = string.Empty;
            var b = iceCreamPart as Base;
            var s = iceCreamPart as Scoops;
            var t = iceCreamPart as Topping;

            if (b != null) {
                iceCream.Base = b;
                messageCaption = "Let's build an ice cream";
            } else if (s != null) {
                iceCream.Scoops = s;
                messageCaption = "I added some scoops";
            } else if (t != null) {
                iceCream.Topping = t;
                messageCaption = "Our finished ice cream";
            } else {
                throw new Exception ("Unexpected type of ice cream part selected.");
            }

            // Create a new message with the same session as any currently selected message.
            var message = ComposeMessage (iceCream, messageCaption, conversation.SelectedMessage?.Session);

            // Add the message to the conversation.
            conversation.InsertMessage (message, null);

            // If the ice cream is complete, save it in the history.
            if (iceCream.IsComplete) {
                var history = IceCreamHistory.Load ();
                history.Append (iceCream);
                history.Save ();
            }

            Dismiss ();
        }

        MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
        {
            var components = new NSUrlComponents {
                QueryItems = iceCream.QueryItems
            };

            var layout = new MSMessageTemplateLayout {
                Image = iceCream.RenderSticker (true),
                Caption = caption
            };

            var message = new MSMessage (session ?? new MSSession()) {
                Url = components.Url,
                Layout = layout
            };

            return message;
        }
    }
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

（可选） 应用程序本来也可以使用`WillTransition`方法来处理视图模式更改之前 （如在上面的 Icecream 生成器示例完成时） 向用户显示。 有关详细信息，请参阅我们[进一步不干胶标签自定义](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md)文档。

## <a name="replying-to-a-message"></a>答复到一条消息

有两种消息应用扩展需要处理答复消息时的情况下：

[![](advanced-message-app-extensions-images/interactive09.png "中的非活动和活动模式的消息应用扩展")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **扩展为非活动**-还有一个在用户可以点击以激活扩展并继续交互式对话消息脚本中的消息应用扩展消息气泡。
- **扩展处于活动状态**-用户可以点击该消息脚本输入扩展视图模式，并继续从它们中断的交互式过程中的消息应用扩展消息气泡。

### <a name="the-extension-is-inactive"></a>扩展的非活动

在消息气泡点击该消息脚本中的用户和消息应用扩展处于非活动状态，将发生以下进程：

[![](advanced-message-app-extensions-images/interactive10.png "处理非活动状态的消息气泡")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. 用户点击扩展的消息气泡。
2. 当启动扩展时，消息应用程序将启动的进程。
3. `DidBecomeActive`方法调用该方法并传递`MSConversation`表示会话中运行消息应用扩展。
4. 因为基于扩展`UIViewController`同时`ViewWillAppear`和`ViewDidAppear`调用。

完成该过程时，消息应用扩展将显示在扩展视图模式中。

### <a name="the-extension-is-active"></a>扩展处于活动状态

在消息气泡点击该消息脚本中的用户，并且消息应用扩展处于活动状态，将发生以下进程：

[![](advanced-message-app-extensions-images/interactive11.png "处理活动的消息气泡")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. 用户点击扩展的消息气泡。
2. 因为消息应用扩展已处于活动状态，`WillTransition`方法`MSMessagesAppViewController`调用以处理从紧凑切换到扩展视图模式。
3. `DidSelectMessage`方法`MSMessagesAppViewController`调用该方法并传递`MSMessage`和`MSConversation`消息气泡属于。
4. `DidTransition`方法`MSMessagesAppViewController`调用以处理从紧凑切换到扩展视图模式。

同样，完成该过程时，消息应用扩展将显示在扩展视图模式中。

### <a name="accessing-the-selected-message"></a>访问选定的消息

在任一情况下，当用户点击消息气泡，属于消息应用扩展，它将需要访问`MSMessage`，点击的使用`SelectedMessage`属性`MSConversation`。

例如：

```csharp
using System;
using Foundation;
using Messages;
using UIKit;


namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        ...

        #region Override Methods
        public override void DidSelectMessage (MSMessage message, MSConversation conversation)
        {
            base.DidSelectMessage (message, conversation);

            // Get selected message
            var selected = conversation.SelectedMessage;

            // Present the user interface to continue editing
            // the conversation
            ...
        }
        #endregion
    }
}
```

应在消息应用程序扩展的用户界面中显示选定的消息，并且应允许用户编写响应。

## <a name="removing-partially-completed-messages"></a>删除部分完成消息

在发送会话中的两个用户之间的不同步骤的交互式会话，过程中部分已完成的消息气泡可以开始打乱消息脚本：

[![](advanced-message-app-extensions-images/interactive12.png "部分已完成的消息气泡可以塞满消息脚本")](advanced-message-app-extensions-images/interactive12.png#lightbox)

相反，消息应用扩展应折叠以前的消息气泡到消息脚本中的简洁注释：

[![](advanced-message-app-extensions-images/interactive13.png "折叠消息脚本中以前的消息气泡")](advanced-message-app-extensions-images/interactive13.png#lightbox)

这处理使用`MSSession`折叠的所有现有的步骤。 因此`DidSelectMessage`方法`MSMessagesAppViewController`类无法修改为如下所示：

```csharp
public override void DidSelectMessage (MSMessage message, MSConversation conversation)
{
    base.DidSelectMessage (message, conversation);

    MSSession session;
    var summary = "";

    // Get selected message
    var selected = conversation.SelectedMessage;

    // Does the selected message already have a session?
    if (selected.Session == null) {
        // No, create a new session
        session = new MSSession ();
        summary = "Let's build an ice cream";
    } else {
        // Yes, use the existing session
        session = selected.Session;
        summary = "I added some scoops";
    }

    // Create an instance of the message being composed
    var existingMessage = new MSMessage (session);
    message.SummaryText = summary;
    ...

    // Present the user interface to continue editing
    // the conversation
    ...
}
```

如果选定的消息已有退出`MSSession`，它其他使用一个新`MSSession`创建。 `SummaryText`属性`MSMessage`用于将标题添加到折叠前面的步骤。 如果`SummaryText`属性设置为`null`，会话中前面的步骤将被完全删除从该会话的脚本。

## <a name="advanced-message-api-features"></a>高级消息 API 功能

使用新的消息 API 详述上面的基本功能下, 一步检查某些 Apple 已内置于框架的更高级功能。

首先，有几个其他重写方法中的`MSMessagesAppViewController`提供对会话的更深入地访问的类：

- `DidStartSendingMessage` -这在用户点击发送按钮时调用。 这并不意味着确保消息实际上已传送到接收方，只需发送过程已启动。
- `DidCancelSendingMessage` -这种情况在用户点击*X*对话脚本中的消息气泡的右上角的按钮。
- `DidReceiveMessage` -消息应用扩展处于活动状态时调用此方法从一个会话中参与者收到一封新邮件。

### <a name="group-conversations"></a>组对话

时所涉及的组对话 （与 3 或更多的人） 用户，这将需要考虑到设计和实现消息应用扩展时，可以使用消息应用扩展。

在与三个用户的组对话中看一看以下交互：

[![](advanced-message-app-extensions-images/interactive14.png "与三个用户的组对话中的交互")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. 用户 1 会发送一组交互式消息询问用户 2 和用户 3 以选择 burger 列在最。
2. 用户 2 选择 tomatoes。
3. 用户 3 选择泡菜。
4. 用户 2 和用户 3 选择到达回用户 1 在几乎同一时间。 因此，用户 2 选择折叠至一个摘要行并且不可用。 这种情况下可能具有也已翻转、 与用户 2 的选择显示和用户 3 的正在折叠。

在任一情况下，此行为是不需要用户 1 应能够访问用户 2 和用户 3 的选项。 若要处理这种情况下，Apple 建议消息应用扩展将消息状态存储在云中使用的 URL 属性`MSMessage`（，获取用户之间发送的） 访问此状态。

当用户发送消息时，会话令牌生成，并推送到云与当前消息状态。 当用户点击该对话脚本中有消息气泡时，会话令牌用于从云中检索当前会话状态。

### <a name="sender-identifiers"></a>发件人标识符

若要讨论访问消息的发件人标识符，采用上面给出的组对话的示例：

[![](advanced-message-app-extensions-images/interactive15.png "发送标识符的组对话")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. 同样，用户 1 会发送一组交互式消息询问用户 2 和用户 3 以选择 burger 列在最。
2. 用户 3 选取泡菜。
3. 用户 3 的选择，选择能够到达回用户 1，并且用户 2 不进行了尚未答复。
4. 因为用户隐私非常关心的是 Apple，消息应用扩展仅知道唯一标识符 (作为`NSUUID`) 分配会话中的每位参与者。 在本地设备上，并且知道仅当前用户的标识符。
5. `MSMessage`具有`SenderIdentifier`用户之一匹配的属性的参与者列表中已知的扩展。
6. 每个用户设备都有其自己的其中同样，仅本地用户的标识符称为参与者列表副本。
7. 发送一条消息，其`SenderIdentifier`属性也简称为属于本地用户。

可以通过以下方式使用发件人标识符：

- 通过查看参与者列表扩展可以获取会话中的用户数。
- 当扩展接收来自用户的一条消息时，它可以跟踪的发件人标识符。 如果它收到具有相同的发件人标识符的另一条消息，该扩展知道它是从同一个用户。
- 它们可以用于帮助识别特定用户会话中。

发件人标识符可在任何文本字段的`MSMessageTemplateLayout`前缀美元符号 (`$`)。 例如：

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

当邮件应用显示时使用此类型的格式设置消息气泡图时，它将替换`$uuid...`与参与者发送消息的会话中的联系人姓名。

发件人标识符是在每个设备上唯一的因此再次注意上面的图示用户 1 的设备和用户 3 的设备查看会话中每个参与者都有一个不同的唯一发件人标识符。

发件人标识符的范围限于消息应用扩展安装。 因此如果用户卸载并重新安装消息应用扩展新发件人标识符将生成新的安装。

若要访问发件人标识符，该扩展可以使用以下代码：

```csharp
public override void DidStartSendingMessage (MSMessage message, MSConversation conversation)
{
    base.DidStartSendingMessage (message, conversation);

    // Get the sender's participant ID
    var senderID = message.SenderParticipantIdentifier;

    // Get the local participant ID
    var localID = conversation.LocalParticipantIdentifier;

    // Get the remote participant IDs
    var remoteIDs = conversation.RemoteParticipantIdentifiers;
}
```

## <a name="supported-platforms"></a>支持的平台

生成消息应用扩展的交互式消息将得出以下 Apple 平台上：

- watchOS 3
- macOS Sierra
- iOS 10

三个平台中，仅 iOS 10 将允许用户生成交互式消息。 在 macOS Sierra，如果用户单击交互式的消息气泡，URL 附加到`MSMessage`将在 Safari 中打开，并且应存在显示消息的表示形式。

WatchOS，在邮件应用可切换到附加的 iOS 设备，用户可以撰写答复的交互式消息。

如果较旧的 Apple 平台上收到交互式的消息时，新的消息 API 具有支持回退：

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

作为两个单独的消息，将回退格式传递它们：

- 提供的模板布局，一个将映像。
- 中提供另一个将成为 URL `MSMessage`。

## <a name="summary"></a>总结

本文介绍了用于处理消息的应用程序扩展与集成的 Xamarin.iOS 解决方案中的高级的技术**消息**应用程序和向用户存在新功能。


## <a name="related-links"></a>相关链接

- [冰激凌生成器 （示例）](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [消息引用](https://developer.apple.com/reference/messages)
- [应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
