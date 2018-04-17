---
title: iCloud
description: Apple 引入在 iOS 5 中的 iCloud 了作为服务以允许应用程序在 Apple 的服务器上存储数据并让它在由同一个人 （通过其 Apple ID) 的所有设备上同步。 它还具有一个备份的组件，其中你的设备上的数据是备份到 Apple 的服务器。 本文档介绍如何使用某些 iCloud apple 提供 Api 来存储和从其服务器，但 C# 示例用于存储较小的键 / 值数据对以及用于存储文档中检索数据。 它还介绍了如何 iCloud 备份可能会影响你的应用程序的设计。
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/09/2016
ms.openlocfilehash: a62d4621a8f3ace64401d64e35c806317a591c03
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="icloud"></a>iCloud

_Apple 引入在 iOS 5 中的 iCloud 了作为服务以允许应用程序在 Apple 的服务器上存储数据并让它在由同一个人 （通过其 Apple ID) 的所有设备上同步。它还具有一个备份的组件，其中你的设备上的数据是备份到 Apple 的服务器。本文档介绍如何使用某些 iCloud apple 提供 Api 来存储和从其服务器，但 C# 示例用于存储较小的键 / 值数据对以及用于存储文档中检索数据。它还介绍了如何 iCloud 备份可能会影响你的应用程序的设计。_

在 iOS 5 中的 iCloud 存储 API 允许应用程序将用户文档和应用程序特定数据保存到一个中心位置，并从用户的所有设备访问这些项。

有四种类型的存储：

- **键 / 值存储**-若要共享上的少量数据与你的应用程序的用户的其他设备。

- **UIDocument 存储**-若要使用的 UIDocument 子类的用户的 iCloud 帐户中存储文档和其他数据。

- **出现 CoreData** -SQLite 数据库存储。

- **单独的文件和目录**-用于直接在文件系统中管理多个不同的文件。

本文档讨论的前两个类型的键 / 值对和 UIDocument 子类-以及如何在 Xamarin.iOS 中使用这些功能。

> [!IMPORTANT]
> Apple[提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)以帮助开发人员正确地处理欧盟的常规数据保护法规 (GDPR)。

## <a name="requirements"></a>要求

- Xamarin.iOS 最新稳定版本
- Xcode 8 或更高版本
- 用于 Mac 或 Visual Studio 2015 和更高版本的 visual Studio。

## <a name="preparing-for-icloud-development"></a>准备 iCloud 开发

应用程序必须配置为使用 iCloud 这两个在[Apple 预配门户](https://developer.apple.com/account/ios/overview.action)和项目本身。 之前开发的 iCloud （或试用示例） 按照下面的步骤。

若要正确配置应用程序访问 iCloud:

-   **查找你的团队 Id** -登录到[developer.apple.com](http://developer.apple.com) ，访问**成员中心 > 您的帐户 > 开发人员帐户摘要**若要获取你团队 ID （或单个 ID 单独的开发人员). 它将为 10 字符字符串 ( **A93A5CM278**例如)-此窗体的"容器标识符"的一部分。

-   **创建新的应用程序 ID** -若要创建应用程序 ID，请按照中概述的步骤[设置存储技术部分的设备设置指南](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)，并且请务必检查**iCloud**为允许的服务：

 [![](introduction-to-icloud-images/icloud-sml.png "检查 iCloud 作为允许的服务")](introduction-to-icloud-images/icloud.png#lightbox)

- **创建新的预配配置文件**-若要创建预配配置文件，请按照中概述的步骤[设备设置指南](~/ios/get-started/installation/device-provisioning/index.md#Provisioning_Profile)。

- **将该容器标识符添加到 Entitlements.plist** -容器标识符格式是`TeamID.BundleID`。 有关详细信息，请参阅[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。

- **配置项目属性**-在文件确保 Info.plist**捆绑标识符**匹配**捆绑 ID**时设置[创建应用程序 ID ](~/ios/deploy-test/provisioning/capabilities/index.md);IOS 捆绑签名使用**预配配置文件**包含与 iCloud App Service 应用程序 ID 和**自定义授权**文件处于选定状态。 这所有可以在 Visual Studio 在项目属性窗格。

- **启用你的设备上的 iCloud** -转到**设置 > iCloud**并确保设备已登录。
选择并打开**文档和数据**选项。

- **你必须使用设备测试 iCloud** -它将不会在模拟器上运行。
事实上，你实际上需要两个或多个设备所有相同的 Apple ID 登录以查看在操作中的 iCloud。


## <a name="key-value-storage"></a>键 / 值存储

键 / 值存储旨在用于少量的用户可能喜欢间保留的设备-如在书籍或杂志中查看它们的最后一页的数据。 键 / 值存储不应该用于备份数据。

有一些限制需要注意的使用键 / 值存储时：

- **最大密钥大小**-密钥名称不能超过 64 个字节。

- **最大值大小**-不能将多个 64 千字节为单位存储在单个值。

- **应用程序的最大键-值存储大小**-应用程序仅可在总计中存储最多为 64 千字节的键 / 值数据。 尝试设置超过该限制的项将会失败，会继续保留以前的值。

- **数据类型**-仅基本类型喜欢将数字的字符串，并可以存储布尔值。

**ICloudKeyValue**示例演示其工作原理。 该示例代码创建名为每个设备的项： 你可以在另一台设备上设置此密钥并观看获取传播给其他人的值。 它还将创建称为可以在任何设备进行编辑的"共享"，如果同时编辑多个设备上的密钥，iCloud 将确定哪个值"wins"（使用的更改上的时间戳），并获取传播。

此屏幕截图显示了正在使用的示例。 当从 iCloud 收到更改通知时它们是在屏幕底部的滚动文本视图中打印，在输入字段中更新。



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "设备之间的消息流")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>设置和检索数据

此代码演示如何设置一个字符串值。

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

调用同步可以确保值保存到本地磁盘存储空间。 同步到 iCloud 在后台发生，并且不能"强制"由应用程序代码。 与网络连接通畅同步通常也会在 5 秒内，但是，如果网络不佳 （或断开连接） 更新可能会花更长的时间。

你可以检索的值替换为以下代码：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

从本地数据存储检索的值-此方法不会尝试联系 iCloud 服务器来获取的"最新"的值。 iCloud 将更新根据自己的计划的本地数据存储区。

### <a name="deleting-data"></a>删除数据

若要完全删除的键-值对，请使用 Remove 方法如下：

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>遵循的更改

应用程序也可以通过添加到观察程序 iCloud 更改值时接收通知`NSNotificationCenter.DefaultCenter`。
下面的代码从**KeyValueViewController.cs** `ViewWillAppear`方法演示如何来侦听这些通知和创建密钥已更改的列表：

```csharp
keyValueNotification =
NSNotificationCenter.DefaultCenter.AddObserver (
    NSUbiquitousKeyValueStore.DidChangeExternallyNotification, notification => {
    Console.WriteLine ("Cloud notification received");
    NSDictionary userInfo = notification.UserInfo;

    var reasonNumber = (NSNumber)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangeReasonKey);
    nint reason = reasonNumber.NIntValue;

    var changedKeys = (NSArray)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangedKeysKey);
    var changedKeysList = new List<string> ();
    for (uint i = 0; i < changedKeys.Count; i++) {
        var key = changedKeys.GetItem<NSString> (i); // resolve key to a string
        changedKeysList.Add (key);
    }
    // now do something with the list...
});
```

然后，你的代码可执行与列表已更改的键，例如更新它们的本地副本或使用新值更新 UI 的某种操作。

可能有更改的原因是： ServerChange (0)、 InitialSyncChange (1) 或 QuotaViolationChange (2)。 你可以访问的原因并执行不同的处理，如果需要 (例如，你可能需要删除某些密钥的结果*QuotaViolationChange*)。

## <a name="document-storage"></a>文档存储

iCloud 文档存储设计用于管理向应用程序 （和用户） 非常重要的数据。 它可以用于管理文件和你的应用程序需要运行在同一时间提供基于 iCloud 备份和所有用户的设备之间共享功能的其他数据。

下图显示其所有如何相互配合。 每个设备已保存在本地存储区 (UbiquityContainer) 和操作系统的 iCloud 后台程序负责发送和接收数据在云中的数据。 所有文件访问权限 UbiquityContainer 必须都通过 FilePresenter/FileCoordinator 以防止并发访问。 `UIDocument`类实现为你的那些; 此示例演示如何使用 UIDocument。

 [![](introduction-to-icloud-images/icloud-overview.png "文档存储概述")](introduction-to-icloud-images/icloud-overview.png#lightbox)

ICloudUIDoc 示例实现一个简单`UIDocument`子类，其中包含单个文本字段。 在呈现文本`UITextView`并且编辑由其他设备到 iCloud 会传播包含以红色显示通知消息。 示例代码不处理更高级的 iCloud 功能，如冲突解决方法。

此屏幕截图显示了示例应用程序的后更改文本和按**UpdateChangeCount**文档将通过与其他设备的 iCloud 同步。

 [![](introduction-to-icloud-images/iclouduidoc.png "此屏幕快照显示后更改文本和按 UpdateChangeCount 的示例应用程序")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

有五个部分 iCloudUIDoc 示例：

1. **访问 UbiquityContainer** -确定如果启用了 iCloud，如果是应用程序的 iCloud 存储区域的路径。

1. **创建 UIDocument 子类**-创建到 iCloud 存储和模型对象之间的中间类。

1. **查找和打开 iCloud 文档**-使用`NSFileManager`和`NSPredicate`以查找 iCloud 文档并将其打开。

1. **显示 iCloud 文档**-公开属性从你`UIDocument`，以便你可以使用 UI 控件进行交互。

1. **保存 iCloud 文档**-确保在 UI 中所做的更改将永久保存到磁盘和 iCloud。

所有 iCloud 操作运行 （或应运行） 以异步方式，以便在不阻止等待发生某些事件时。 你将看到示例中实现此操作的三种不同的方式：

 **线程**-在`AppDelegate.FinishedLaunching`首次调用`GetUrlForUbiquityContainer`在另一个线程来防止阻塞主线程上完成。

 **NotificationCenter** -注册通知时异步操作，如`NSMetadataQuery.StartQuery`完成。

 **完成处理程序**-传入方法以完成异步操作，如运行`UIDocument.Open`。

### <a name="accessing-the-ubiquitycontainer"></a>访问 UbiquityContainer

使用 iCloud 文档存储的第一步是确定是否启用 iCloud，以及如果是这样的位置"无处不容器"（在设备存储 iCloud 启用文件的目录）。

此代码位于`AppDelegate.FinishedLaunching`方法中的示例。

```csharp
// GetUrlForUbiquityContainer is blocking, Apple recommends background thread or your UI will freeze
ThreadPool.QueueUserWorkItem (_ => {
    CheckingForiCloud = true;
    Console.WriteLine ("Checking for iCloud");
    var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer (null);
    // OR instead of null you can specify "TEAMID.com.your-company.ApplicationName"

    if (uburl == null) {
        HasiCloud = false;
        Console.WriteLine ("Can't find iCloud container, check your provisioning profile and entitlements");

        InvokeOnMainThread (() => {
            var alertController = UIAlertController.Create ("No \uE049 available",
            "Check your Entitlements.plist, BundleId, TeamId and Provisioning Profile!", UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Destructive, null));
            viewController.PresentViewController (alertController, false, null);
        });
    } else { // iCloud enabled, store the NSURL for later use
        HasiCloud = true;
        iCloudUrl = uburl;
        Console.WriteLine ("yyy Yes iCloud! {0}", uburl.AbsoluteUrl);
    }
    CheckingForiCloud = false;
});
```

尽管该示例不这样做，Apple 将建议调用 GetUrlForUbiquityContainer，每当应用程序转入前台。

### <a name="creating-a-uidocument-subclass"></a>创建 UIDocument 子类

所有 iCloud 文件和目录 (即。 存储在 UbiquityContainer 目录中的任何内容) 必须使用 NSFileManager 方法，实现 NSFilePresenter 协议和通过 NSFileCoordinator 写入管理。
执行所有这些操作的最简单方法是不编写它自己，但子类 UIDocument 它的作用的你所有。

必须在要使用 iCloud UIDocument 子类中实现的仅有两个方法有：

- **LoadFromContents** -将中的文件的内容，为你进行解包到你的模型类/es 外的传递。

- **ContentsForType** -为你提供的外表示形式模型类/es 请求保存到磁盘 （和云）。

此代码示例从**iCloudUIDoc\MonkeyDocument.cs**演示如何实现 UIDocument。

```csharp
public class MonkeyDocument : UIDocument
{
    // the 'model', just a chunk of text in this case; must easily convert to NSData
    NSString dataModel;
    // model is wrapped in a nice .NET-friendly property
    public string DocumentString {
        get {
            return dataModel.ToString ();
        }
        set {
            dataModel = new NSString (value);
        }
    }
    public MonkeyDocument (NSUrl url) : base (url)
    {
        DocumentString = "(default text)";
    }
    // contents supplied by iCloud to display, update local model and display (via notification)
    public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("LoadFromContents({0})", typeName);

        if (contents != null)
            dataModel = NSString.FromData ((NSData)contents, NSStringEncoding.UTF8);

        // LoadFromContents called when an update occurs
        NSNotificationCenter.DefaultCenter.PostNotificationName ("monkeyDocumentModified", this);
        return true;
    }
    // return contents for iCloud to save (from the local model)
    public override NSObject ContentsForType (string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("ContentsForType({0})", typeName);
        Console.WriteLine ("DocumentText:{0}",dataModel);

        NSData docData = dataModel.Encode (NSStringEncoding.UTF8);
        return docData;
    }
}
```

在这种情况下，数据模型是非常简单-单个文本字段。 你的数据模型可以很复杂，比如必需的如 Xml 文档或二进制数据。 UIDocument 实现的主要目的是为了模型类和可以是磁盘上保存/加载外表示形式之间进行转换。

### <a name="finding-and-opening-icloud-documents"></a>查找和打开 iCloud 文档

示例应用仅处理单个文件-test.txt-因此中的代码**AppDelegate.cs**创建`NSPredicate`和`NSMetadataQuery`专门查找该文件名。 `NSMetadataQuery`以异步方式运行和它完成时发送通知。 `DidFinishGathering` 获取由通知观察者，停止的查询，并且调用 LoadDocument，使用`UIDocument.Open`方法完成处理程序尝试加载文件，并将其显示在`MonkeyDocumentViewController`。

```csharp
string monkeyDocFilename = "test.txt";
void FindDocument ()
{
    Console.WriteLine ("FindDocument");
    query = new NSMetadataQuery {
        SearchScopes = new NSObject [] { NSMetadataQuery.UbiquitousDocumentsScope }
    };

    var pred = NSPredicate.FromFormat ("%K == %@", new NSObject[] {
        NSMetadataQuery.ItemFSNameKey, new NSString (MonkeyDocFilename)
    });

    Console.WriteLine ("Predicate:{0}", pred.PredicateFormat);
    query.Predicate = pred;

    NSNotificationCenter.DefaultCenter.AddObserver (
        this,
        new Selector ("queryDidFinishGathering:"),
        NSMetadataQuery.DidFinishGatheringNotification,
        query
    );

    query.StartQuery ();
}

[Export ("queryDidFinishGathering:")]
void DidFinishGathering (NSNotification notification)
{
    Console.WriteLine ("DidFinishGathering");
    var metadataQuery = (NSMetadataQuery)notification.Object;
    metadataQuery.DisableUpdates ();
    metadataQuery.StopQuery ();

    NSNotificationCenter.DefaultCenter.RemoveObserver (this, NSMetadataQuery.DidFinishGatheringNotification, metadataQuery);
    LoadDocument (metadataQuery);
}

void LoadDocument (NSMetadataQuery metadataQuery)
{
    Console.WriteLine ("LoadDocument");

    if (metadataQuery.ResultCount == 1) {
        var item = (NSMetadataItem)metadataQuery.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);
        doc = new MonkeyDocument (url);

        doc.Open (success => {
            if (success) {
                Console.WriteLine ("iCloud document opened");
                Console.WriteLine (" -- {0}", doc.DocumentString);
                viewController.DisplayDocument (doc);
            } else {
                Console.WriteLine ("failed to open iCloud document");
            }
        });
    } // TODO: if no document, we need to create one
}
```

### <a name="displaying-icloud-documents"></a>显示 iCloud 文档

显示 UIDocument 不应任何不同于其他模型类
- 属性将显示在 UI 控件，用户可能编辑，然后写回模型。

在示例中**iCloudUIDoc\MonkeyDocumentViewController.cs**显示 MonkeyDocument 中文`UITextView`。 `ViewDidLoad` 侦听发送的通知`MonkeyDocument.LoadFromContents`方法。 `LoadFromContents` 必须时调用 iCloud，为文件的新数据，以便通知指示文档已更新。

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

该示例代码通知处理程序调用的方法，在这种情况下更新 UI-，而无需任何冲突检测或解决方法。

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>保存 iCloud 文档

若要添加到 iCloud 可以调用 UIDocument`UIDocument.Save`直接 （为新的文档） 或移动现有的文件使用`NSFileManager.DefaultManager.SetUbiquitious`。 示例代码替换为以下代码覆盖容器中直接创建一个新文档 (有两个完成处理程序在这里，一个用于`Save`操作，另一个用于打开):

```csharp
var docsFolder = Path.Combine (iCloudUrl.Path, "Documents"); // NOTE: Documents folder is user-accessible in Settings
var docPath = Path.Combine (docsFolder, MonkeyDocFilename);
var ubiq = new NSUrl (docPath, false);
var monkeyDoc = new MonkeyDocument (ubiq);
monkeyDoc.Save (monkeyDoc.FileUrl, UIDocumentSaveOperation.ForCreating, saveSuccess => {
Console.WriteLine ("Save completion:" + saveSuccess);
if (saveSuccess) {
    monkeyDoc.Open (openSuccess => {
        Console.WriteLine ("Open completion:" + openSuccess);
        if (openSuccess) {
            Console.WriteLine ("new document for iCloud");
            Console.WriteLine (" == " + monkeyDoc.DocumentString);
            viewController.DisplayDocument (monkeyDoc);
        } else {
            Console.WriteLine ("couldn't open");
        }
    });
} else {
    Console.WriteLine ("couldn't save");
}
```

对文档的后续更改都未"保存"直接，而是我们将告诉`UIDocument`它已更改与`UpdateChangeCount`，并且它将自动计划保存到磁盘操作：

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>管理 iCloud 文档

用户可以管理中的 iCloud 文档**文档**"无处不容器"通过设置; 你应用程序之外的目录他们可以查看文件列表和轻扫以删除。 应用程序代码应该能够处理这种情况，用户删除文档的位置。 不存储中的内部应用程序数据**文档**目录。

 [![](introduction-to-icloud-images/icloudstorage.png "管理 iCloud 文档工作流")](introduction-to-icloud-images/icloudstorage.png#lightbox)



在尝试从其设备，以通知他们该应用程序与相关的 iCloud 文档的状态删除 iCloud 启用应用程序时，用户也会收到不同的警告。

 [![](introduction-to-icloud-images/icloud-delete1.png "当用户尝试从其设备中删除 iCloud 启用应用程序时的示例对话框")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "当用户尝试从其设备中删除 iCloud 启用应用程序时的示例对话框")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>iCloud 备份

备份到 iCloud 不直接访问由开发人员的功能，而设计你的应用程序的方式可能会影响用户体验。
Apple 提供[iOS 数据存储准则](http://developer.apple.com/icloud/documentation/data-storage/)要在其 iOS 应用程序中遵循的开发人员。

最重要的注意事项是内容的你的应用程序是否存储大型文件不是内容的用户生成的 （例如杂志读取器存储应用程序的每个问题 hundred-plus 兆字节为单位）。 Apple 首选不存储此类型的数据，其中它将为备份到 iCloud 和不必要地填充用户的 iCloud 配额。

存储大量类似的数据的应用程序应要么将其存储在一个备份 （如不是用户目录 缓存或 tmp） 或使用`NSFileManager.SetSkipBackupAttribute`要应用于这些文件的一个标志，以便 iCloud 备份操作过程中忽略它们。

## <a name="summary"></a>总结

这篇文章引入了新的 iCloud 功能包含在 iOS 5。 它会检查配置项目以使用 iCloud 所必需的然后提供如何实现 iCloud 功能的示例的步骤。

键 / 值存储示例演示了如何使用 iCloud 存储少量的数据存储 NSUserPreferences 工作方式相似。 UIDocument 示例介绍了可以存储多个复杂的数据，并将其在 iCloud 通过多个设备上同步。

最后，它包含有关如何添加 iCloud 备份应影响应用程序设计的简要讨论。



## <a name="related-links"></a>相关链接

- [介绍到 iCloud （示例）](https://developer.xamarin.com/samples/monotouch/IntroductionToiCloud)
- [iCloud 研讨会示例代码](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [iCloud 研讨会幻灯片](http://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [iCloud 存储](http://support.apple.com/kb/HT4847)
