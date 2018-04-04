---
title: Handoff
description: 本文介绍使用在 Xamarin.iOS 应用程序中要传输的 Handoff 在用户上运行的应用之间的用户活动的其他设备。
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: bb665c7ffd4241fac14be13ebd8f113d11afd417
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="handoff"></a>Handoff

_本文介绍使用在 Xamarin.iOS 应用程序中要传输的 Handoff 在用户上运行的应用之间的用户活动的其他设备。_

Apple 到另一个设备运行的相同应用程序或另一个应用程序支持的相同活动中 iOS 8 和 OS X Yosemite (10.10) 提供常见的机制，用户可以传输活动在其设备之一上启动引入 Handoff。

[![](handoff-images/handoff02.png "下面举例说明执行提交操作")](handoff-images/handoff02.png#lightbox)

本文将采用快速了解一下启用的 Xamarin.iOS 应用程序共享的活动，并涵盖 Handoff 框架在详细信息：

## <a name="about-handoff"></a>有关 Handoff

Handoff （也称为连续性） 的引入了通过 Apple 在 iOS 8 和 OS X Yosemite (10.10) 作为要在其中一个自己的设备 （iOS 或 Mac） 上开始活动并在他们的设备 （如由用户的 iClou 标识的另一台继续该同一活动中的用户的方法d 帐户)。

Handoff 已扩展在 iOS 9，以还支持新的、 增强的搜索功能。 有关详细信息，请参阅我们[搜索增强功能](~/ios/platform/search/index.md)文档。

例如，用户可以在其 iPhone 上启动一封电子邮件和无缝地在其具有的所有相同的消息信息填充和光标位于同一位置中它们在 iOS 中保留的 Mac 上继续电子邮件。

你可以共享相同的应用的任何_团队 ID_ （适用于 Mac 的企业都符合条件的使用 Handoff，只要这些应用程序都是跨应用继续用户活动传递通过 iTunes 应用商店或通过注册开发者签名或即席应用）。

任何`NSDocument`或`UIDocument`基于的应用自动具有 Handoff 支持内置，并要求最小的更改才能支持 Handoff。

### <a name="continuing-user-activities"></a>继续用户活动

`NSUserActivity`类 (以及一些小改动`UIKit`和`AppKit`) 用于定义在用户的设备的另一台可以可能继续的用户的活动提供支持。

为活动通过传递到另一个用户的设备，它必须封装实例中`NSUserActivity`、 标记为_当前活动_，设置它的负载 （用于执行延续的数据） 和然后必须到该设备传输活动。

Handoff 将传递的信息，以定义要使用更大的数据包正在同步通过 iCloud 继续的活动的最低要求。

在接收设备上，用户将收到通知活动是可用于延续。 如果用户选择在新的设备上继续活动，指定的应用启动 （如果尚未运行） 和从负载`NSUserActivity`用于重启活动。

[![](handoff-images/handoffinteractions.png "继续用户活动的概述")](handoff-images/handoffinteractions.png#lightbox)

只有应用程序共享同一开发人员团队 ID 并响应给定_活动类型_均适合延续。 应用程序定义其下支持的活动类型`NSUserActivityTypes`键其**Info.plist**文件。 鉴于此，继续设备选择应用程序以执行延续任务根据团队 ID，活动类型和 （可选）_活动标题_。

接收应用程序使用信息从`NSUserActivity`的`UserInfo`字典来配置其用户界面和还原给定活动的状态，以便显示给最终用户无缝转换。

如果延续需要详细信息比可以有效地通过发送`NSUserActivity`，恢复应用程序可以发送到原始应用程序的调用并建立一个或多个流来传输所需的数据。 例如，如果活动已编辑具有多个映像的较大的文本文档，流式处理所需传输接收设备上继续活动所需的信息。 有关详细信息，请参阅[支持延续流](#Supporting-Continuation-Streams)下面一节。

如前所述，`NSDocument`或`UIDocument`基于的应用自动具有 Handoff 支持内置。 有关详细信息，请参阅[基于文档的应用中支持 Handoff](#Supporting-Handoff-in-Document-Based-Apps)下面一节。

### <a name="the-nsuseractivity-class"></a>NSUserActivity 类

`NSUserActivity`类是 Handoff exchange 中的主对象，用于封装可用延续的用户活动的状态。 应用程序将实例化的副本`NSUserActivity`对于它支持，并且希望继续在另一台设备上的任何活动。 例如，文档编辑器将创建用于每个文档的活动当前打开。 但是，只 （显示为顶层窗口或选项卡上） 在最前面文档是_当前活动_，因此可供延续。

实例`NSUserActivity`两个标识其`ActivityType`和`Title`属性。 `UserInfo`字典属性用于携带有关活动的状态的信息。 设置`NeedsSave`属性`true`如果你想为延迟加载状态信息通过`NSUserActivity`的委托。 使用`AddUserInfoEntries`方法合并到其他客户端中的新数据`UserInfo`字典需要保留的活动的状态。

### <a name="the-nsuseractivitydelegate-class"></a>NSUserActivityDelegate 类

`NSUserActivityDelegate`用于将信息保存在`NSUserActivity`的`UserInfo`字典最新状态并且与活动的当前状态同步。 当系统需要在活动中要更新的信息 (如之前在另一台设备上的延续)，它调用`UserActivityWillSave`方法的委托。

你需要实现`UserActivityWillSave`方法并对进行任何更改`NSUserActivity`(如`UserInfo`，`Title`等) 以确保它仍反映当前的活动的状态。 当系统调用`UserActivityWillSave`方法，`NeedsSave`将清除标志。 如果你修改任何活动的数据属性，你将需要设置`NeedsSave`到`true`试。

而不是使用`UserActivityWillSave`上面介绍的方法，可以选择让`UIKit`或`AppKit`自动管理的用户活动。 若要执行此操作，将设置响应方对象`UserActivity`属性和实现`UpdateUserActivityState`方法。 请参阅[响应程序中支持 Handoff](#Supporting-Handoff-in-Responders)下面部分以了解详细信息。

### <a name="app-framework-support"></a>应用程序框架支持

同时`UIKit`(iOS) 和`AppKit`(OS X) 提供内置支持 Handoff 在`NSDocument`，响应方 (`UIResponder`/`NSResponder`)，和`AppDelegate`类。 尽管每个操作系统实现 Handoff 略有不同，基本的机制和 Api 都是相同。

#### <a name="user-activities-in-document-based-apps"></a>基于文档的应用中的用户活动

基于文档的 iOS 和 OS X 应用自动具有内置的 Handoff 支持。 若要激活此支持，你将需要添加`NSUbiquitousDocumentUserActivityType`键和值为每个`CFBundleDocumentTypes`中应用的条目**Info.plist**文件。

如果此项存在，同时`NSDocument`和`UIDocument`自动创建`NSUserActivity`iCloud 基于文档指定的类型的实例。 你将需要提供的应用支持的文档的每个类型的活动类型，并且多个文档类型可以使用相同的活动类型。 同时`NSDocument`和`UIDocument`自动填充`UserInfo`属性`NSUserActivity`与其`FileURL`属性的值。

在 OS X 上`NSUserActivity`由`AppKit`和自动响应程序与关联成为当前活动文档的窗口时主窗口。 在 iOS 上为`NSUserActivity`管理的对象`UIKit`，必须调用`BecomeCurrent`方法显式或有文档的`UserActivity`属性设置的`UIViewController`时应用程序转入前台。

`AppKit` 将自动恢复到任何`UserActivity`采用此方式在 OS X 上创建的属性。如果发生这种情况`ContinueUserActivity`方法返回`false`或如果它未实现。 在此情况下，打开该文档与`OpenDocument`方法`NSDocumentController`和它随后会收到`RestoreUserActivityState`方法调用。

请参阅[基于文档的应用中支持 Handoff](#Supporting-Handoff-in-Document-Based-Apps)下面部分以了解详细信息。

#### <a name="user-activities-and-responders"></a>用户活动和响应程序

同时`UIKit`和`AppKit`可以自动管理用户活动，如果将其设置为响应方对象的`UserActivity`属性。 如果已修改状态，你将需要设置`NeedsSave`响应方的属性`UserActivity`到`true`。 系统会自动保存`UserActivity`在需要向授予通过调用来更新状态的响应方时间之后时其`UpdateUserActivityState`方法。

如果多个响应共享单个`NSUserActivity`实例，它们接收`UpdateUserActivityState`回调时系统会更新用户活动对象。 响应方需要调用`AddUserInfoEntries`方法来更新`NSUserActivity`的`UserInfo`字典以此时反映当前的活动状态。 `UserInfo`在每个之前清除字典`UpdateUserActivityState`调用。

若要解除本身与活动关联，响应方可以设置其`UserActivity`属性`null`。 应用程序框架的管理时`NSUserActivity`实例具有任何更关联响应程序或文档，它将自动失效。

请参阅[响应程序中支持 Handoff](#Supporting-Handoff-in-Responders)下面部分以了解详细信息。

#### <a name="user-activities-and-the-appdelegate"></a>用户活动和 AppDelegate

你的应用`AppDelegate`处理 Handoff 延续时其主入口点。 当用户响应 Handoff 通知，相应的应用启动 （如果不已在运行） 和`WillContinueUserActivityWithType`方法`AppDelegate`将调用。 此时，应用程序应通知延续正在启动的用户。

`NSUserActivity`实例传递时`AppDelegate`的`ContinueUserActivity`调用方法。 此时，你应配置应用程序的用户界面，并继续给定的活动。

请参阅[实现 Handoff](#Implementing-Handoff)下面部分以了解详细信息。

## <a name="enabling-handoff-in-a-xamarin-app"></a>在 Xamarin 应用程序中启用 Handoff

由于 Handoff 施加安全要求，必须正确配置的 Xamarin.iOS 应用程序使用 Handoff 框架和 Xamarin.iOS 项目文件中 Apple 开发人员门户。

请执行以下操作：

1. 登录到[Apple 开发人员门户](http://developer.apple.com)。
2. 单击**证书、 标识符和配置文件**。
3. 如果你尚未这样做，请单击**标识符**和创建的应用程序 ID (例如`com.company.appname`)，否则编辑你现有的 id。
4. 确保**iCloud**给定 id 已检查服务： 

    [![](handoff-images/provision01.png "启用给定 ID 的 iCloud 服务")](handoff-images/provision01.png#lightbox)
5. 保存更改。
4. 单击**预配配置文件** > **开发**并创建新的开发为你预配配置文件应用： 

    [![](handoff-images/provision02.png "创建新的开发设置应用程序配置文件")](handoff-images/provision02.png#lightbox)
5. 下载和安装新的预配配置文件或使用 Xcode 下载和安装配置文件。
6. 编辑你的 Xamarin.iOS 项目选项，并确保你正在使用你刚刚创建的预配配置文件： 

    [![](handoff-images/provision03.png "选择刚创建的预配配置文件")](handoff-images/provision03.png#lightbox)
7. 接下来，编辑你**Info.plist**文件，并确保你正在使用用于创建预配配置文件的应用程序 ID: 

    [![](handoff-images/provision04.png "设置应用程序 ID")](handoff-images/provision04.png#lightbox)
8. 滚动到**后台模式**部分，并检查以下各项： 

    [![](handoff-images/provision05.png "启用所需的后台模式")](handoff-images/provision05.png#lightbox)
9. 将所做的更改保存到的所有文件。

使用就地这些设置，应用程序现在已准备好访问 Handoff Framework Api。 有关设置的详细信息，请参阅我们[设备资源调配](~/ios/get-started/installation/device-provisioning/index.md)和[设置你的应用](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="implementing-handoff"></a>实现 Handoff

用户活动可以继续使用相同的开发人员团队 ID 进行签名并支持相同的活动类型的应用之间。 在 Xamarin.iOS 应用程序中实现 Handoff 要求你创建的用户活动对象 (在`UIKit`或`AppKit`)、 更新对象的状态来跟踪活动，并在接收设备上继续活动。

### <a name="identifying-user-activities"></a>标识用户活动

实现 Handoff 的第一步是确定你的应用程序支持的用户活动的类型和看到其中这些活动非常适合进行在另一台设备上的延续。 例如： ToDo 应用程序可能支持编辑的项作为一个_用户活动类型_，并且支持浏览可用的项目列表作为另一个。

应用程序可以创建任意多个用户活动类型是必需的一个用于应用程序会提供任何函数。 对于每个用户活动类型，该应用将需要跟踪类型的活动开始和结束，并且需要保持最新状态信息在另一台设备上继续该任务时。

用户活动可以继续使用相同的团队 ID，而无需任何发送和接收应用程序之间的一对一映射签名任何应用程序上。 例如，给定的应用程序可以创建四种不同类型的活动，是由另一个设备上的不同，其中的个别应用。 这是应用的一种常见情况之间 （程序可能具有的许多功能和函数） 的 Mac 版本和 iOS 应用，其中每个应用程序为较小，专注于特定任务。

### <a name="creating-activity-type-identifiers"></a>创建活动类型标识符

_活动类型标识符_短字符串添加到`NSUserActivityTypes`的应用程序的数组**Info.plist**用于唯一标识给定的用户活动类型的文件。 在数组的每个应用程序支持的活动中，将一个项。 Apple 提供的建议使用活动类型标识符的反向 DNS 样式表示法以避免冲突。 例如：`com.company-name.appname.activity`对于特定的应用程序基于活动或`com.company-name.activity`可以运行跨多个应用的活动。

在创建时使用活动类型标识符`NSUserActivity`实例标识活动的类型。 当在另一台设备上继续活动时, （以及团队的应用程序的 ID) 的活动类型将确定哪些应用程序以启动继续活动。

例如，我们将创建示例应用调用**MonkeyBrowser** ([可从此处下载](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/))。 此应用程序将提供四个选项卡，每个都有不同的 URL 打开在 web 浏览器视图中。 用户将能够继续运行应用程序的不同的 iOS 设备上的任何选项卡。

若要创建所需的活动类型标识符，以支持此行为，编辑**Info.plist**文件，并切换到**源**视图。 添加`NSUserActivityTypes`密钥，然后创建以下标识符：

[![](handoff-images/type01.png "NSUserActivityTypes 键和 plist 编辑器中的所需的标识符")](handoff-images/type01.png#lightbox)

我们创建四个新的活动类型标识符，一个用于每个示例中的选项卡**MonkeyBrowser**应用。 创建你自己的应用时, 的内容替换`NSUserActivityTypes`阵列特定于活动的活动类型标识符您的应用程序支持。

### <a name="tracking-user-activity-changes"></a>跟踪用户活动的更改

当我们创建的新实例`NSUserActivity`类，我们将指定`NSUserActivityDelegate`实例可以跟踪对活动的状态更改。 例如，以下代码可以用于跟踪状态更改：

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;

namespace MonkeyBrowse
{
    public class UserActivityDelegate : NSUserActivityDelegate
    {
        #region Constructors
        public UserActivityDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void UserActivityReceivedData (NSUserActivity userActivity, NSInputStream inputStream, NSOutputStream outputStream)
        {
            // Log
            Console.WriteLine ("User Activity Received Data: {0}", userActivity.Title);
        }

        public override void UserActivityWasContinued (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity Was Continued: {0}", userActivity.Title);
        }

        public override void UserActivityWillSave (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity will be Saved: {0}", userActivity.Title);
        }
        #endregion
    }
}
```

`UserActivityReceivedData`延续流从发送设备收到数据时，调用方法。 有关详细信息，请参阅[支持延续流](#Supporting-Continuation-Streams)下面一节。

`UserActivityWasContinued`另一台设备已通过将活动从当前设备时，调用方法。 具体取决于活动，例如向 ToDo 列表中，添加新项的类型应用程序可能需要中止发送设备上的活动。

`UserActivityWillSave`之前对活动的任何更改将保存并跨本地可用设备同步调用方法。 你可以使用此方法才能更改到任何最近一分钟`UserInfo`属性`NSUserActivity`实例发送之前。

### <a name="creating-a-nsuseractivity-instance"></a>创建 NSUserActivity 实例

你的应用程序想要提供的可能性继续在另一台设备上的每个活动必须封装在`NSUserActivity`实例。 应用程序可以创建所需的尽可能多的活动，并且这些活动的性质是依赖于实现的功能和功能的问题的应用。 例如，电子邮件应用可能会创建一个活动，以便创建新的邮件，和另一个用于读取消息。

对于我们的示例应用程序中，新`NSUserActivity`在每次用户在其中一个选项卡式的 web 浏览器视图中输入新的 URL 创建。 下面的代码将存储的给定的选项卡的状态：

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSUserActivity UserActivity { get; set; }
...

UserActivity = new NSUserActivity (UserActivityTab1);
UserActivity.Title = "Weather Tab";
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

它创建一个新`NSUserActivity`使用其中一用户活动类型的上述步骤中创建并且为活动提供的用户可读的标题。 附加到的实例`NSUserActivityDelegate`创建上面要监视状态更改，并通知 iOS 此用户活动是当前的活动。

### <a name="populating-the-userinfo-dictionary"></a>填充 UserInfo 字典

正如我们所看到更高版本，`UserInfo`属性`NSUserActivity`类是`NSDictionary`的用来定义给定活动的状态的键 / 值对。 中存储的值`UserInfo`必须是以下类型之一： `NSArray`， `NSData`， `NSDate`， `NSDictionary`， `NSNull`， `NSNumber`， `NSSet`， `NSString`，或`NSURL`。 `NSURL` 指向 iCloud 文档的数据值将自动进行调整，以使它们指向接收设备上相同的文档。

在上面的示例中，我们创建了`NSMutableDictionary`对象并填入一个密钥提供用户当前已在给定的选项卡上查看的 URL。`AddUserInfoEntries`用户活动的方法用于将用于还原接收设备上的活动的数据更新的活动：

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple 建议保持发送给要求最小值以确保活动发送及时到接收设备的信息。 如果像映像附加到文档对其进行编辑，更大的信息是必需的需要发送，则应使用延续流。 请参阅[支持延续流](#Supporting-Continuation-Streams)下面部分以了解更多详细信息。

### <a name="continuing-an-activity"></a>继续活动

本地 iOS 和 OS X 设备的物理方式进入原始设备，并且登录到相同的 iCloud 帐户，可持续用户活动的可用性，handoff 会自动通知。 如果用户选择在新的设备上继续活动，系统将启动的相应应用程序中 （基于团队 ID 和活动类型） 和信息其`AppDelegate`延续需要发生。

首先，`WillContinueUserActivityWithType`以便应用程序可以通知用户延续即将开始调用方法。 我们使用下面的代码**AppDelegate.cs**我们的示例应用程序来处理延续起点的文件：

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSString UserActivityTab2 = new NSString ("com.xamarin.monkeybrowser.tab2");
public NSString UserActivityTab3 = new NSString ("com.xamarin.monkeybrowser.tab3");
public NSString UserActivityTab4 = new NSString ("com.xamarin.monkeybrowser.tab4");
...

public FirstViewController Tab1 { get; set; }
public SecondViewController Tab2 { get; set;}
public ThirdViewController Tab3 { get; set; }
public FourthViewController Tab4 { get; set; }
...

public override bool WillContinueUserActivity (UIApplication application, string userActivityType)
{
    // Report Activity
    Console.WriteLine ("Will Continue Activity: {0}", userActivityType);

    // Take action based on the user activity type
    switch (userActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Inform view that it's going to be modified
        Tab1.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Inform view that it's going to be modified
        Tab2.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Inform view that it's going to be modified
        Tab3.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Inform view that it's going to be modified
        Tab4.PreparingToHandoff ();
        break;
    }

    // Inform system we handled this
    return true;
}
```

在上面的示例中，每个视图控制器注册`AppDelegate`和具有公共`PreparingToHandoff`显示活动指示器和消息让用户知道活动即将移交给当前设备的方法。 示例:

```csharp
private void ShowBusy(string reason) {

    // Display reason
    BusyText.Text = reason;

    //Define Animation
    UIView.BeginAnimations("Show");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0.5f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PreparingToHandoff() {
    // Inform caller
    ShowBusy ("Continuing Activity...");
}
```

`ContinueUserActivity`的`AppDelegate`将调用以实际继续给定的活动。 同样，从我们的示例应用程序：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

公共`PerformHandoff`方法的每个视图控制器实际 preforms handoff 和还原当前设备上的活动。 在示例中，它的用户已在另一台设备浏览的给定选项卡中显示的相同 URL。 示例:

```csharp
private void HideBusy() {

    //Define Animation
    UIView.BeginAnimations("Hide");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PerformHandoff(NSUserActivity activity) {

    // Hide busy indicator
    HideBusy ();

    // Extract URL from dictionary
    var url = activity.UserInfo ["Url"].ToString ();

    // Display value
    URL.Text = url;

    // Display the give webpage
    WebView.LoadRequest(new NSUrlRequest(NSUrl.FromString(url)));

    // Save activity
    UserActivity = activity;
    UserActivity.BecomeCurrent ();

}
```

`ContinueUserActivity`方法包括`UIApplicationRestorationHandler`，你可以调用文档或响应方基于活动恢复。 你将需要传递`NSArray`或到还原处理程序调用时可还原对象。 例如：

```csharp
completionHandler (new NSObject[]{Tab4});
```

每个对象传递，其`RestoreUserActivityState`将调用方法。 每个对象然后可以使用中的数据`UserInfo`字典，以还原其自己的状态。 例如：

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

对于基于文档的应用程序，如果你未实现`ContinueUserActivity`方法或其返回`false`，`UIKit`或`AppKit`自动恢复活动。 请参阅[基于文档的应用中支持 Handoff](#Supporting-Handoff-in-Document-Based-Apps)下面部分以了解详细信息。

### <a name="failing-handoff-gracefully"></a>正常失败 Handoff

由于提交依赖于传输之间松散连接集合 iOS 和 OS X 设备的信息，此传输过程有时可能会失败。 应设计您的应用程序适当地处理这些故障，并通知用户的任何出现的情况。

出现故障，`DidFailToContinueUserActivitiy`方法`AppDelegate`将调用。 例如：

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

你应使用所提供`NSError`以向与失败有关的用户提供信息。

## <a name="native-app-to-web-browser-handoff"></a>本机应用程序到 Web 浏览器提交

用户可能想要继续而无需的设备上安装相应本机应用程序的活动。 在某些情况下，基于 web 接口可能会提供所需的功能和活动仍可继续运行。 例如，用户的电子邮件帐户可能提供基于 web 的 UI，为撰写和读取消息。

如果原始的本机应用已知道 URL 的 web 接口 （以及用于标识给定的项正在继续执行所需的语法），它可以编码中的此信息`WebpageURL`属性`NSUserActivity`实例。 如果接收的设备没有安装来处理延续相应本机应用程序，可以调用提供的 web 接口。

## <a name="web-browser-to-native-app-handoff"></a>Web 浏览器到本机应用 Handoff

如果用户已在原始设备上，使用基于 web 的界面和接收的设备上的本机应用声明的域部分`WebpageURL`属性，则系统将使用该应用程序的句柄延续。 新设备将收到`NSUserActivity`将标记为活动类型的实例`BrowsingWeb`和`WebpageURL`将包含已访问用户，URL`UserInfo`字典将为空。

对于参与 Handoff 这种应用程序，它必须声明中的域`com.apple.developer.associated-domains`和格式的授权`<service>:<fully qualified domain name>`(例如： `activity continuation:company.com`)。

如果指定的域匹配`WebpageURL`属性的值，Handoff 从该域上的网站下载的已批准的应用程序 Id 的列表。 网站必须提供一个名为的有符号的 JSON 文件中的已批准 Id 的列表**apple 应用程序的站点关联**(例如， `https://company.com/apple-app-site-association`)。

此 JSON 文件包含一个字典，其中窗体中指定的应用程序 Id 的列表`<team identifier>.<bundle identifier>`。 例如：

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

对 JSON 文件进行签名 (以使其具有正确`Content-Type`的`application/pkcs7-mime`)，使用**终端**应用和`openssl`命令使用的证书和 iOS 的受信任的证书颁发机构颁发的密钥 (请参阅[http://support.apple.com/kb/ht5012 ](http://support.apple.com/kb/ht5012)有关的列表)。 例如：

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

`openssl`命令输出签名的 JSON 文件，你将放置在你网站上**apple 应用程序的站点关联**URL。 例如：

```csharp
https://example.com/apple-app-site-association.
```

应用程序将接收任何活动其`WebpageURL`域处于其`com.apple.developer.associated-domains`授权。 仅`http`和`https`协议支持，任何其他协议将引发的异常。

## <a name="supporting-handoff-in-document-based-apps"></a>在基于文档的应用程序中支持 Handoff

如果如上所述，在 iOS 和 OS X 上基于文档的应用将自动支持 Handoff iCloud 基于文档的应用程序的**Info.plist**文件包含`CFBundleDocumentTypes`键`NSUbiquitousDocumentUserActivityType`。 例如：

```xml
<key>CFBundleDocumentTypes</key>
<array>
    <dict>
        <key>CFBundleTypeName</key>
        <string>NSRTFDPboardType</string>
        . . .
        <key>LSItemContentTypes</key>
        <array>
        <string>com.myCompany.rtfd</string>
        </array>
        . . .
        <key>NSUbiquitousDocumentUserActivityType</key>
        <string>com.myCompany.myEditor.editing</string>
    </dict>
</array>
```

在此示例中的字符串为追加活动的名称与一个反向 DNS 应用指示符。 如果输入这种方式，不需要的活动类型条目中重复`NSUserActivityTypes`数组**Info.plist**文件。

自动创建用户活动对象 (可通过文档的`UserActivity`属性) 可以由应用程序中的其他对象引用和用于还原延续状态。 例如，若要跟踪项选择和文档位置。 你需要设置此活动`NeedsSave`属性`true`每当更改和更新的状态`UserInfo`字典中的`UpdateUserActivityState`方法。

`UserActivity`属性可以从任何线程使用，并且符合键-值观察 (KVO) 协议，因此它可以用于使文档保持同步，当它移入和签出的 iCloud。 `UserActivity`文档关闭时，都将失效属性。

有关详细信息，请参阅 Apple 的[基于文档的应用中的用户活动支持](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5)文档。

## <a name="supporting-handoff-in-responders"></a>在响应中支持 Handoff

您还可以将响应程序相关联 (从继承`UIResponder`在 iOS 上或`NSResponder`OS X 上) 与活动通过设置其`UserActivity`属性。 系统会自动将保存`UserActivity`属性在适当的时间，调用响应方的`UpdateUserActivityState`方法将当前数据添加到用户活动对象使用`AddUserInfoEntriesFromDictionary`方法。

## <a name="supporting-continuation-streams"></a>支持延续流

这样一种的情况其中继续活动所需信息的量不能高效地传输初始的 Handoff 负载。 在这些情况下，接收应用程序可以建立自身原始应用将数据传输之间的一个或多个流。

原始应用程序将设置`SupportsContinuationStreams`属性`NSUserActivity`到实例`true`。 例如：

```csharp
// Create a new user Activity to support this tab
UserActivity = new NSUserActivity (ThisApp.UserActivityTab1){
    Title = "Weather Tab",
    SupportsContinuationStreams = true
};
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

然后，接收应用程序可以调用`GetContinuationStreams`方法`NSUserActivity`中其`AppDelegate`建立流。 例如：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

在原始设备上，用户活动委托接收流通过调用其`DidReceiveInputStream`方法提供的数据，请求将在恢复设备上继续的用户活动。

你将使用`NSInputStream`提供流式传输数据的只读访问和`NSOutputStream`提供只写访问。 流应使用请求和响应的方式，接收应用程序请求更多数据，其中原始应用程序会提供它。 以便从继续在设备上，输入流中读取原始设备上写入到输出流的数据，反之亦然。

即使在延续流需要的情况下，应该也最小的后规定两个应用程序之间的通信。

有关详细信息，请参阅 Apple 的[使用延续流](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13)文档。

## <a name="handoff-best-practices"></a>Handoff 最佳做法

无缝的用户活动通过 Handoff 延续的成功实现所有各种所涉及的组件由于要求仔细设计。 Apple 提供的建议采用 Handoff 启用应用程序的以下最佳实践：

- 设计你的用户活动需要可能将能继续的活动状态的最小负载。 更大的负载，较长的时间延续启动。
- 如果您必须传输大量的成功延续的数据，考虑到成本参与配置和网络开销。
- 很常见的大型的 Mac 应用程序以创建将由多个较小，iOS 设备上的特定于任务的应用的用户活动。 不同的应用程序和操作系统版本应设计为很好地协同工作或优雅地失败。
- 指定活动类型时，使用反向 DNS 表示法以避免冲突。 如果某个活动是特定于给定的应用程序，其名称应包含类型定义中 (例如`com.myCompany.myEditor.editing`)。 如果活动可处理跨多个应用，从定义删除应用程序名称 (例如`com.myCompany.editing`)。
- 如果你的应用程序需要更新用户活动的状态 (`NSUserActivity`) 设置`NeedsSave`属性`true`。 在适当的时间，Handoff 将调用该委托的`UserActivityWillSave`方法，以便你可以更新`UserInfo`根据需要的字典。
- 因为 Handoff 进程可能会在接收设备上立即初始化，则应实现`AppDelegate`的`WillContinueUserActivity`，并通知用户，延续即将开始。

## <a name="example-handoff-app"></a>示例 Handoff 应用程序

作为使用 Handoff 在 Xamarin.iOS 应用程序中的示例，我们包括了[ **MonkeyBrowser** ](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)使用本指南的示例应用程序。 应用程序出现用户可用于浏览网页，每个给定的活动类型的四个选项卡： 天气信息、 收藏夹、 休息时间和工作。

在任何选项卡上，当用户进入某个新的 URL，然后点击**转**按钮，新`NSUserActivity`为该选项卡包含的用户当前浏览的 URL 创建：

[![](handoff-images/handoff01.png "示例 Handoff 应用程序")](handoff-images/handoff01.png#lightbox)

如果用户的设备的另一台只有**MonkeyBrowser**安装，应用程序登录到 iCloud 使用相同的用户帐户、 网络和在上面的设备的临近，Handoff 活动将显示在主页上位于相同屏幕 （在左下角）：

[![](handoff-images/handoff02.png "在左下角中的主屏幕上显示 Handoff 活动")](handoff-images/handoff02.png#lightbox)

如果用户向上拖动 Handoff 图标上时，将启动应用程序和用户活动中指定`NSUserActivity`将继续在新的设备上：

[![](handoff-images/handoff03.png "用户活动继续在新的设备上")](handoff-images/handoff03.png#lightbox)

当用户活动已成功发送到另一个 Apple 设备，发送的设备的`NSUserActivity`将接收调用`UserActivityWasContinued`方法其`NSUserActivityDelegate`使其知道用户活动已成功传输到另一个设备。

## <a name="summary"></a>总结

本文已授予使用继续之间的用户的 Apple 设备的多个用户活动的 Handoff framework 介绍。 接下来，它还介绍了如何启用并在 Xamarin.iOS 应用程序中实现 Handoff。 最后，它讨论的不同类型的可用 Handoff 延续和 Handoff 最佳做法。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [MonkeyBrowser 示例](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [什么是在 iOS 9.0 新增](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 用户界面指南](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit Framework 参考](https://developer.apple.com/library/ios/home_kit_framework_ref)
