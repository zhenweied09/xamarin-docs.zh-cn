---
title: 在 Xamarin.iOS 的文档选取器
description: 本文档介绍 iOS 文档选取器，并讨论如何在 Xamarin.iOS 中使用它。 它将探讨 iCloud、 文档、 公共安装程序代码、 文档提供程序扩展，和的详细信息。
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: efa1b589ad4afe26d8b87c3db1777660b3e27be6
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786881"
---
# <a name="document-picker-in-xamarinios"></a>在 Xamarin.iOS 的文档选取器

文档选取器允许应用之间共享的文档。 这些文档可能 iCloud 中或以不同应用程序的目录存储。 通过的一套共享文档[文档提供程序扩展](~/ios/platform/extensions.md)用户在已安装在其设备上。 

由于保留跨应用和云同步的文档的难度，它们引入了一定数量的必要的复杂性。

## <a name="requirements"></a>要求

以下是所需完成这篇文章中提供的步骤：

-  **Xcode 7 和 iOS 8 或更高版本**– Apple 的 Xcode 7 和 iOS 8 或更高版本的 Api 需要安装并配置开发人员计算机上。
-  **Visual Studio 或 Visual Studio for Mac** – 应安装适用于 Mac 的 Visual Studio 的最新版本。
-  **iOS 设备**– 运行 iOS 8 的 iOS 设备或更高版本。

## <a name="changes-to-icloud"></a>更改到 iCloud

若要实现文档选取器的新功能，具有对 Apple 的 iCloud 服务已进行以下更改：

-  后台程序已经过完全重新编写使用 CloudKit iCloud。
-  现有 iCloud 功能已被重命名 iCloud 驱动器。
-  Microsoft Windows 操作系统的支持已添加到 iCloud。
-  已在 Mac OS 查找工具中添加 iCloud 文件夹。
-  iOS 设备可以访问的 Mac OS iCloud 文件夹的内容。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

## <a name="what-is-a-document"></a>什么是文档？

当引用 iCloud 中的文档，它是单个独立的实体，并应会感觉到这种情况下用户。 用户可能想要修改文档或 （通过使用电子邮件，例如） 与其他用户共享它。

有几种类型的文件，用户将立即将识别为文档，如页面，Keynote 或数字的文件。 但是，iCloud 并不局限于这一概念。 例如，可以视为文档 （如 Chess 匹配项） 的游戏的状态并将其存储在 iCloud。 此文件可以在用户的设备之间传递，并让他们可以选取游戏上次退出的位置不同设备上。

## <a name="dealing-with-documents"></a>处理文档

之前深入 Xamarin 中使用文档选取器所需的代码，本文将介绍使用 iCloud 文档的最佳做法，并且支持文档选取器对现有的 Api 进行修改的几个所必需。

### <a name="using-file-coordination"></a>使用文件协调

因为从多个不同的位置，可以修改文件，必须使用协调，以防止数据丢失。

 [![](document-picker-images/image1.png "使用文件协调")](document-picker-images/image1.png#lightbox)

让我们看看上图中：

1.  使用文件协调的 iOS 设备创建一个新文档，并将其保存到 iCloud 文件夹。
2.  iCloud 将修改后的文件保存到云的分发给每个设备。
3.  附加的 Mac 看到 iCloud 文件夹中的已修改的文件，并使用文件协调下所做的更改将复制到文件。
4.  设备不使用文件协调到文件中做出更改，并将其保存到 iCloud 文件夹。 这些更改会立即复制到其他设备。

假设原始 iOS 设备或 Mac 已编辑该文件，其更改现在丢失和覆盖来自不协调的设备的文件的版本。 若要防止数据丢失，文件协调时必须使用基于云的文档。

### <a name="using-uidocument"></a>使用 UIDocument

 `UIDocument` 使操作变得简单 (或`NSDocument`在 macOS 上) 通过使开发人员执行所有完成的繁重任务。 它提供了背景队列，以避免被阻止应用程序的 UI 使用生成文件配合使用。

 `UIDocument` 公开了多个，需要为任何目的开发人员可以简化开发工作中的 Xamarin 应用程序的高级别 Api。

下面的代码创建的一个子类`UIDocument`实现可以用于存储和从 iCloud 中检索文本的泛型基于文本的文档：

```csharp
using System;
using Foundation;
using UIKit;

namespace DocPicker
{
    public class GenericTextDocument : UIDocument
    {
        #region Private Variable Storage
        private NSString _dataModel;
        #endregion

        #region Computed Properties
        public string Contents {
            get { return _dataModel.ToString (); }
            set { _dataModel = new NSString(value); }
        }
        #endregion

        #region Constructors
        public GenericTextDocument (NSUrl url) : base (url)
        {
            // Set the default document text
            this.Contents = "";
        }

        public GenericTextDocument (NSUrl url, string contents) : base (url)
        {
            // Set the default document text
            this.Contents = contents;
        }
        #endregion

        #region Override Methods
        public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Were any contents passed to the document?
            if (contents != null) {
                _dataModel = NSString.FromData( (NSData)contents, NSStringEncoding.UTF8 );
            }

            // Inform caller that the document has been modified
            RaiseDocumentModified (this);

            // Return success
            return true;
        }

        public override NSObject ContentsForType (string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Convert the contents to a NSData object and return it
            NSData docData = _dataModel.Encode(NSStringEncoding.UTF8);
            return docData;
        }
        #endregion

        #region Events
        public delegate void DocumentModifiedDelegate(GenericTextDocument document);
        public event DocumentModifiedDelegate DocumentModified;

        internal void RaiseDocumentModified(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentModified != null) {
                this.DocumentModified (document);
            }
        }
        #endregion
    }
}
```

`GenericTextDocument`使用文档选取器和在 Xamarin.iOS 8 应用程序中的外部文档时将本文通篇使用上面介绍的类。

## <a name="asynchronous-file-coordination"></a>异步文件协调

iOS 8 提供了一些新的异步文件协调功能，通过新的文件协调 Api。 在 iOS 8 之前所有的现有文件协调 Api 已完全同步。 这意味着开发人员负责实现其自己的背景队列以防止文件协调阻止应用程序的 UI。

新`NSFileAccessIntent`类包含指向文件和多个选项来控制的一种协调所需的 URL。 下面的代码演示将文件从一个位置移动到另一个使用意向：

```csharp
// Get source options
var srcURL = NSUrl.FromFilename ("FromFile.txt");
var srcIntent = NSFileAccessIntent.CreateReadingIntent (srcURL, NSFileCoordinatorReadingOptions.ForUploading);

// Get destination options
var dstURL = NSUrl.FromFilename ("ToFile.txt");
var dstIntent = NSFileAccessIntent.CreateReadingIntent (dstURL, NSFileCoordinatorReadingOptions.ForUploading);

// Create an array
var intents = new NSFileAccessIntent[] {
    srcIntent,
    dstIntent
};

// Initialize a file coordination with intents
var queue = new NSOperationQueue ();
var fileCoordinator = new NSFileCoordinator ();
fileCoordinator.CoordinateAccess (intents, queue, (err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}",err.LocalizedDescription);
    }
});
```

## <a name="discovering-and-listing-documents"></a>发现和列出文档

发现并列出文档的方法是通过使用现有`NSMetadataQuery`Api。 本部分将介绍添加到新功能`NSMetadataQuery`，请使用文档比以往更方便。

### <a name="existing-behavior"></a>现有行为

在 iOS 8 之前`NSMetadataQuery`如速度太慢于拾取本地文件更改： 删除、 创建和重命名。

 [![](document-picker-images/image2.png "NSMetadataQuery 本地文件更改概述")](document-picker-images/image2.png#lightbox)

在上面的图中：

1.  文件已存在于应用程序容器，`NSMetadataQuery`具有现有`NSMetadata`记录预先创建，并且假脱机这样便可立即使用应用程序。
1.  应用程序在应用程序容器中创建一个新文件。
1.  没有前的延迟`NSMetadataQuery`看到对应用程序容器修改和创建所需`NSMetadata`记录。


由于创建延迟`NSMetadata`记录，该应用程序有具有两个数据源打开： 一个本地文件更改，一个用于云基于更改。

### <a name="stitching"></a>拼接

在 iOS 8，`NSMetadataQuery`可以更轻松地直接使用一种称为进行拼结的新功能：

 [![](document-picker-images/image3.png "新功能的 NSMetadataQuery 调用进行拼结")](document-picker-images/image3.png#lightbox)

使用了上图中进行拼结：

1.  和前面一样，为应用程序容器中已存在文件`NSMetadataQuery`具有现有`NSMetadata`记录预先创建，并且假脱机。
1.  应用程序中使用文件协调应用程序容器创建一个新文件。
1.  应用程序容器中的挂钩可以看到的修改和调用`NSMetadataQuery`以创建所需`NSMetadata`记录。
1.  `NSMetadata`记录后该文件直接创建，并提供给应用程序。


通过使用进行拼结的应用程序不再必须打开数据源来监视本地和基于云的文件更改。 现在应用程序可以依赖于`NSMetadataQuery`直接。

> [!IMPORTANT]
> 拼接仅适用如果应用程序使用文件协调，如上面的部分中显示。 如果未使用文件协调，Api 将默认为现有的预 iOS 8 行为。




### <a name="new-ios-8-metadata-features"></a>新的 iOS 8 元数据功能

以下新功能已添加到`NSMetadataQuery`iOS 8 中：

-   `NSMetatadataQuery` 可以现在列出在云中存储的非本地文档。
-  添加了新的 Api 以访问基于云的文档的元数据信息。 
-  新增了一个`NSUrl_PromisedItems`将访问文件，也可能没有可用其内容本地文件属性的 API。
-  使用`GetPromisedItemResourceValue`方法以获取有关给定文件的信息，或者使用`GetPromisedItemResourceValues`方法以获取有关多个文件一次的信息。


用于处理元数据添加了两个新文件协调标志：

-   `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
-   `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


使用上面的标志，文档文件的内容不需要为可供其可以用于本地。

下面的代码段演示如何使用`NSMetadataQuery`查询是否存在特定文件和生成文件，如果它不存在：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;


#region Static Properties
public const string TestFilename = "test.txt"; 
#endregion

#region Computed Properties
public bool HasiCloud { get; set; }
public bool CheckingForiCloud { get; set; }
public NSUrl iCloudUrl { get; set; }

public GenericTextDocument Document { get; set; }
public NSMetadataQuery Query { get; set; }
#endregion

#region Private Methods
private void FindDocument () {
    Console.WriteLine ("Finding Document...");

    // Create a new query and set it's scope
    Query = new NSMetadataQuery();
    Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

    // Build a predicate to locate the file by name and attach it to the query
    var pred = NSPredicate.FromFormat ("%K == %@"
        , new NSObject[] {
            NSMetadataQuery.ItemFSNameKey
            , new NSString(TestFilename)});
    Query.Predicate = pred;

    // Register a notification for when the query returns
    NSNotificationCenter.DefaultCenter.AddObserver (this,
            new Selector("queryDidFinishGathering:"),           NSMetadataQuery.DidFinishGatheringNotification,
            Query);

    // Start looking for the file
    Query.StartQuery ();
    Console.WriteLine ("Querying: {0}", Query.IsGathering);
}

[Export("queryDidFinishGathering:")]
public void DidFinishGathering (NSNotification notification) {
    Console.WriteLine ("Finish Gathering Documents.");

    // Access the query and stop it from running
    var query = (NSMetadataQuery)notification.Object;
    query.DisableUpdates();
    query.StopQuery();

    // Release the notification
    NSNotificationCenter.DefaultCenter.RemoveObserver (this
        , NSMetadataQuery.DidFinishGatheringNotification
        , query);

    // Load the document that the query returned
    LoadDocument(query);
}

private void LoadDocument (NSMetadataQuery query) {
    Console.WriteLine ("Loading Document...");    

    // Take action based on the returned record count
    switch (query.ResultCount) {
    case 0:
        // Create a new document
        CreateNewDocument ();
        break;
    case 1:
        // Gain access to the url and create a new document from
        // that instance
        NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

        // Load the document
        OpenDocument (url);
        break;
    default:
        // There has been an issue
        Console.WriteLine ("Issue: More than one document found...");
        break;
    }
}
#endregion

#region Public Methods
public void OpenDocument(NSUrl url) {

    Console.WriteLine ("Attempting to open: {0}", url);
    Document = new GenericTextDocument (url);

    // Open the document
    Document.Open ( (success) => {
        if (success) {
            Console.WriteLine ("Document Opened");
        } else
            Console.WriteLine ("Failed to Open Document");
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public void CreateNewDocument() {
    // Create path to new file
    // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
    var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
    var docPath = Path.Combine (docsFolder, TestFilename);
    var ubiq = new NSUrl (docPath, false);

    // Create new document at path 
    Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
    Document = new GenericTextDocument (ubiq);

    // Set the default value
    Document.Contents = "(default value)";

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
        Console.WriteLine ("Save completion:" + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
        } else {
            Console.WriteLine ("Unable to Save Document");
        }
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public bool SaveDocument() {
    bool successful = false;

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
        Console.WriteLine ("Save completion: " + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
            successful = true;
        } else {
            Console.WriteLine ("Unable to Save Document");
            successful=false;
        }
    });

    // Return results
    return successful;
}
#endregion

#region Events
public delegate void DocumentLoadedDelegate(GenericTextDocument document);
public event DocumentLoadedDelegate DocumentLoaded;

internal void RaiseDocumentLoaded(GenericTextDocument document) {
    // Inform caller
    if (this.DocumentLoaded != null) {
        this.DocumentLoaded (document);
    }
}
#endregion
```

### <a name="document-thumbnails"></a>文档缩略图

Apple 的外观，列出应用程序的文档时的最佳用户体验是使用预览版。 这使得最终用户的上下文，以便它们能够快速确定他们想要使用的文档。

在 iOS 8 之前显示文档预览所需的自定义实现。 新到 iOS 8 是允许开发人员可以快速使用文档缩略图的文件系统属性。

#### <a name="retrieving-document-thumbnails"></a>检索文档缩略图 

通过调用`GetPromisedItemResourceValue`或`GetPromisedItemResourceValues`方法， `NSUrl_PromisedItems` API， `NSUrlThumbnailDictionary`，则返回。 此字典中当前的唯一键是`NSThumbnial1024X1024SizeKey`和其匹配`UIImage`。

#### <a name="saving-document-thumbnails"></a>保存文档缩略图

保存缩略图的最简单方法是使用`UIDocument`。 通过调用`GetFileAttributesToWrite`方法`UIDocument`和设置缩略图，它将自动保存的文档文件时。 ICloud 后台程序将看到此更改，并将其传播到 iCloud。 Mac OS X 上缩略图是通过自动生成为开发人员快速查找插件。

与使用就地情况下，以及对现有 API 进行修改的 iCloud 基于文档的基础知识，我们已准备好在 Xamarin iOS 8 中实现文档选取器视图控制器移动应用程序。


## <a name="enabling-icloud-in-xamarin"></a>在 Xamarin 中启用 iCloud

可以在 Xamarin.iOS 应用程序中使用文档选取器之前，iCloud 支持需要在你的应用程序和通过 Apple 来启用。 

下面的步骤演练 iCloud 设置的过程中。

1. 创建容器 iCloud。
2. 创建包含 iCloud App Service 应用程序 ID。
3. 创建预配配置文件，其中包含此应用程序 id。

[使用功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)指南会指导完成前两个步骤。 若要创建预配配置文件，按照中的步骤[预配配置文件](~/ios/get-started/installation/device-provisioning/index.md#Provisioning_Profile)指南。



下面的步骤演练 iCloud 将应用程序配置的过程中：

请执行以下操作：

1.  用于 Mac 或 Visual Studio，在 Visual Studio 中打开项目。
2.  在**解决方案资源管理器**，右键单击该项目并选择选项。
3.  在选项对话框选择**iOS 应用程序**，确保**捆绑标识符**匹配已在中定义的一个**应用程序 ID**上面创建的应用程序。 
4.  选择**iOS 捆绑签名**，选择**开发人员标识**和**预配配置文件**上述步骤中创建。
5.  单击**确定**按钮以保存所做的更改并关闭对话框。
6.  右键单击`Entitlements.plist`中**解决方案资源管理器**以在编辑器中打开它。

    > [!IMPORTANT]
    > Visual Studio 中你可能需要打开权利编辑器通过右键单击它，选择**打开方式...** 并选择属性列表编辑器

7.  检查**启用 iCloud** ， **iCloud 文档**，**键-值存储**和**CloudKit** 。
8.  确保**容器**应用程序 （如上面创建） 中存在。 示例：`iCloud.com.your-company.AppName`
9.  保存对文件所做的更改。

有关授权的详细信息请参阅[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。

借助就地上面的安装，应用程序现在可以使用基于云的文档和新的文档选取器视图控制器。

## <a name="common-setup-code"></a>常见安装代码

入门文档选取器视图控制器之前, 没有所需的某些标准安装程序代码。 通过修改应用程序的启动`AppDelegate.cs`文件并使其如下所示：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;

namespace DocPicker
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Static Properties
        public const string TestFilename = "test.txt"; 
        #endregion

        #region Computed Properties
        public override UIWindow Window { get; set; }
        public bool HasiCloud { get; set; }
        public bool CheckingForiCloud { get; set; }
        public NSUrl iCloudUrl { get; set; }

        public GenericTextDocument Document { get; set; }
        public NSMetadataQuery Query { get; set; }
        public NSData Bookmark { get; set; }
        #endregion

        #region Private Methods
        private void FindDocument () {
            Console.WriteLine ("Finding Document...");

            // Create a new query and set it's scope
            Query = new NSMetadataQuery();
            Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

            // Build a predicate to locate the file by name and attach it to the query
            var pred = NSPredicate.FromFormat ("%K == %@",
                new NSObject[] {NSMetadataQuery.ItemFSNameKey
                , new NSString(TestFilename)});
            Query.Predicate = pred;

            // Register a notification for when the query returns
            NSNotificationCenter.DefaultCenter.AddObserver (this
                , new Selector("queryDidFinishGathering:")
                , NSMetadataQuery.DidFinishGatheringNotification
                , Query);

            // Start looking for the file
            Query.StartQuery ();
            Console.WriteLine ("Querying: {0}", Query.IsGathering);
        }


        [Export("queryDidFinishGathering:")]
        public void DidFinishGathering (NSNotification notification) {
            Console.WriteLine ("Finish Gathering Documents.");

            // Access the query and stop it from running
            var query = (NSMetadataQuery)notification.Object;
            query.DisableUpdates();
            query.StopQuery();

            // Release the notification
            NSNotificationCenter.DefaultCenter.RemoveObserver (this
                , NSMetadataQuery.DidFinishGatheringNotification
                , query);

            // Load the document that the query returned
            LoadDocument(query);
        }

        private void LoadDocument (NSMetadataQuery query) {
            Console.WriteLine ("Loading Document...");  

            // Take action based on the returned record count
            switch (query.ResultCount) {
            case 0:
                // Create a new document
                CreateNewDocument ();
                break;
            case 1:
                // Gain access to the url and create a new document from
                // that instance
                NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
                var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

                // Load the document
                OpenDocument (url);
                break;
            default:
                // There has been an issue
                Console.WriteLine ("Issue: More than one document found...");
                break;
            }
        }
        #endregion

        #region Public Methods

        public void OpenDocument(NSUrl url) {

            Console.WriteLine ("Attempting to open: {0}", url);
            Document = new GenericTextDocument (url);

            // Open the document
            Document.Open ( (success) => {
                if (success) {
                    Console.WriteLine ("Document Opened");
                } else
                    Console.WriteLine ("Failed to Open Document");
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        public void CreateNewDocument() {
            // Create path to new file
            // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
            var docPath = Path.Combine (docsFolder, TestFilename);
            var ubiq = new NSUrl (docPath, false);

            // Create new document at path 
            Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
            Document = new GenericTextDocument (ubiq);

            // Set the default value
            Document.Contents = "(default value)";

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
                Console.WriteLine ("Save completion:" + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                } else {
                    Console.WriteLine ("Unable to Save Document");
                }
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        /// <summary>
        /// Saves the document.
        /// </summary>
        /// <returns><c>true</c>, if document was saved, <c>false</c> otherwise.</returns>
        public bool SaveDocument() {
            bool successful = false;

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
                Console.WriteLine ("Save completion: " + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                    successful = true;
                } else {
                    Console.WriteLine ("Unable to Save Document");
                    successful=false;
                }
            });

            // Return results
            return successful;
        }
        #endregion

        #region Override Methods
        public override void FinishedLaunching (UIApplication application)
        {

            // Start a new thread to check and see if the user has iCloud
            // enabled.
            new Thread(new ThreadStart(() => {
                // Inform caller that we are checking for iCloud
                CheckingForiCloud = true;

                // Checks to see if the user of this device has iCloud
                // enabled
                var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer(null);

                // Connected to iCloud?
                if (uburl == null)
                {
                    // No, inform caller
                    HasiCloud = false;
                    iCloudUrl =null;
                    Console.WriteLine("Unable to connect to iCloud");
                    InvokeOnMainThread(()=>{
                        var okAlertController = UIAlertController.Create ("iCloud Not Available", "Developer, please check your Entitlements.plist, Bundle ID and Provisioning Profiles.", UIAlertControllerStyle.Alert);
                        okAlertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        Window.RootViewController.PresentViewController (okAlertController, true, null);
                    });
                }
                else
                {   
                    // Yes, inform caller and save location the the Application Container
                    HasiCloud = true;
                    iCloudUrl = uburl;
                    Console.WriteLine("Connected to iCloud");

                    // If we have made the connection with iCloud, start looking for documents
                    InvokeOnMainThread(()=>{
                        // Search for the default document
                        FindDocument ();
                    });
                }

                // Inform caller that we are no longer looking for iCloud
                CheckingForiCloud = false;

            })).Start();
                
        }
        
        // This method is invoked when the application is about to move from active to inactive state.
        // OpenGL applications should use this method to pause.
        public override void OnResignActivation (UIApplication application)
        {
        }
        
        // This method should be used to release shared resources and it should store the application state.
        // If your application supports background execution this method is called instead of WillTerminate
        // when the user quits.
        public override void DidEnterBackground (UIApplication application)
        {
            // Trap all errors
            try {
                // Values to include in the bookmark packet
                var resources = new string[] {
                    NSUrl.FileSecurityKey,
                    NSUrl.ContentModificationDateKey,
                    NSUrl.FileResourceIdentifierKey,
                    NSUrl.FileResourceTypeKey,
                    NSUrl.LocalizedNameKey
                };

                // Create the bookmark
                NSError err;
                Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

                // Was there an error?
                if (err != null) {
                    // Yes, report it
                    Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
                }
            }
            catch (Exception e) {
                // Report error
                Console.WriteLine ("Error: {0}", e.Message);
            }
        }
        
        // This method is called as part of the transition from background to active state.
        public override void WillEnterForeground (UIApplication application)
        {
            // Is there any bookmark data?
            if (Bookmark != null) {
                // Trap all errors
                try {
                    // Yes, attempt to restore it
                    bool isBookmarkStale;
                    NSError err;
                    var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

                    // Was there an error?
                    if (err != null) {
                        // Yes, report it
                        Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
                    } else {
                        // Load document from bookmark
                        OpenDocument (srcUrl);
                    }
                }
                catch (Exception e) {
                    // Report error
                    Console.WriteLine ("Error: {0}", e.Message);
                }
            }

        }
        
        // This method is called when the application is about to terminate. Save data, if needed.
        public override void WillTerminate (UIApplication application)
        {
        }
        #endregion

        #region Events
        public delegate void DocumentLoadedDelegate(GenericTextDocument document);
        public event DocumentLoadedDelegate DocumentLoaded;

        internal void RaiseDocumentLoaded(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentLoaded != null) {
                this.DocumentLoaded (document);
            }
        }
        #endregion
    }
}

```

> [!IMPORTANT]
> 上面的代码包括上面的发现和列出文档部分中的代码。 它显示此处整个，它将在实际的应用程序中出现。 为简单起见，此示例适用于一种硬编码的单个文件 (`test.txt`) 仅。

上面的代码公开多个 iCloud 驱动器快捷方式，使它们更轻松地使用应用程序的其余部分。

接下来，将以下代码添加到任何视图或视图容器将使用文档选取器或使用基于云的文档：

```csharp
using CloudKit;
...

#region Computed Properties
/// <summary>
/// Returns the delegate of the current running application
/// </summary>
/// <value>The this app.</value>
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

这将添加的快捷方式可用于访问`AppDelegate`和访问上面创建的 iCloud 快捷方式。

使用此代码中的位置，让我们看看在 Xamarin iOS 8 应用程序中实现文档选取器视图控制器。

## <a name="using-the-document-picker-view-controller"></a>使用文档选取器视图控制器

在 iOS 8 之前, 已从另一个应用程序访问文档，因为没有方法来发现从应用中在应用程序外部的文档非常困难。

### <a name="existing-behavior"></a>现有行为

 [![](document-picker-images/image31.png "现有行为概述")](document-picker-images/image31.png#lightbox)

让我们看一看访问 iOS 8 之前的外部文档：

1.  第一次用户将必须打开最初创建文档的应用程序。
1.  选择文档和`UIDocumentInteractionController`用于将文档发送到新的应用程序。
1.  最后，原始文档的副本放置在新应用程序的容器中。


从此处是第二个应用程序中，可以打开并编辑文档。

### <a name="discovering-documents-outside-of-an-apps-container"></a>发现在应用程序的容器之外的文档

在 iOS 8 中，应用程序是能够轻松地访问在其自己的应用程序容器之外的文档：

 [![](document-picker-images/image32.png "发现在应用程序的容器之外的文档")](document-picker-images/image32.png#lightbox)

使用新 iCloud 文档选取器 ( `UIDocumentPickerViewController`)，iOS 应用程序能够直接发现和访问该应用程序容器以外。 `UIDocumentPickerViewController`提供一种机制，用户可以授予访问权限和编辑这些权限通过发现文档。

应用程序必须选择加入以具有其文档显示在 iCloud 文档选取器，并且可供其他应用程序发现和使用它们。 若要有一个 Xamarin iOS 8 应用程序共享其应用程序容器，请对其进行编辑`Info.plist`文件位于标准文本编辑器，并将以下两行添加到字典中的底部 (之间`<dict>...</dict>`标记):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

`UIDocumentPickerViewController`提供的出色的新用户界面，用户可以选择文档。 若要在 Xamarin iOS 8 应用程序中显示文档选取器视图控制器，执行以下操作：

```csharp
using MobileCoreServices;
...

// Allow the Document picker to select a range of document types
        var allowedUTIs = new string[] {
            UTType.UTF8PlainText,
            UTType.PlainText,
            UTType.RTF,
            UTType.PNG,
            UTType.Text,
            UTType.PDF,
            UTType.Image
        };

        // Display the picker
        //var picker = new UIDocumentPickerViewController (allowedUTIs, UIDocumentPickerMode.Open);
        var pickerMenu = new UIDocumentMenuViewController(allowedUTIs, UIDocumentPickerMode.Open);
        pickerMenu.DidPickDocumentPicker += (sender, args) => {

            // Wireup Document Picker
            args.DocumentPicker.DidPickDocument += (sndr, pArgs) => {

                // IMPORTANT! You must lock the security scope before you can
                // access this file
                var securityEnabled = pArgs.Url.StartAccessingSecurityScopedResource();

                // Open the document
                ThisApp.OpenDocument(pArgs.Url);

                // IMPORTANT! You must release the security lock established
                // above.
                pArgs.Url.StopAccessingSecurityScopedResource();
            };

            // Display the document picker
            PresentViewController(args.DocumentPicker,true,null);
        };

pickerMenu.ModalPresentationStyle = UIModalPresentationStyle.Popover;
PresentViewController(pickerMenu,true,null);
UIPopoverPresentationController presentationPopover = pickerMenu.PopoverPresentationController;
if (presentationPopover!=null) {
    presentationPopover.SourceView = this.View;
    presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Down;
    presentationPopover.SourceRect = ((UIButton)s).Frame;
}
```

> [!IMPORTANT]
> 开发人员必须调用`StartAccessingSecurityScopedResource`方法`NSUrl`可以访问的外部文档前。 `StopAccessingSecurityScopedResource`必须调用方法以加载文档时，就会立即释放安全锁。

### <a name="sample-output"></a>示例输出

下面是上面的代码显示了当在 iPhone 设备上运行的文档选取器的方式的一个示例：

1.  用户启动应用程序，并且显示主接口：   
 
    [![](document-picker-images/image33.png "显示的主接口")](document-picker-images/image33.png#lightbox)
1.  用户点击**操作**屏幕顶部的按钮并要求选择**文档提供程序**从可用提供程序的列表：   
 
    [![](document-picker-images/image34.png "从可用提供程序列表中选择文档提供程序")](document-picker-images/image34.png#lightbox)
1.  **文档选取器视图控制器**显示所选**文档提供程序**:   
 
    [![](document-picker-images/image35.png "将显示的文档选取器视图控制器")](document-picker-images/image35.png#lightbox)
1.  用户点击**文档文件夹**以显示其内容：   
 
    [![](document-picker-images/image36.png "文档文件夹内容")](document-picker-images/image36.png#lightbox)
1.  用户选择**文档**和**文档选取器**已关闭。
1.  主界面将重新显示，**文档**是从外部容器并显示其内容加载。


文档选取器视图控制器的实际显示依赖于用户在已安装在设备上和哪些文档选取器模式下已实现文档提供程序。 上面的示例使用打开模式时，将下面将详细讨论的其他模式类型。

## <a name="managing-external-documents"></a>管理外部文档

如上所述之前 iOS 8，, 应用程序仅可以访问属于其应用程序容器的文档。 在 iOS 8 应用程序可以访问文档，从外部源：

 [![](document-picker-images/image37.png "管理外部文档概述")](document-picker-images/image37.png#lightbox)

当用户选择从外部源的文档时，引用文档是写入到指向原始文档的应用程序容器中。

若要帮助你添加到现有应用程序此新功能，几项新功能已添加到`NSMetadataQuery`API。 通常情况下，应用程序使用无处不在文档作用域，限制为在其应用程序容器内的实时列出文档。 使用此作用域，仅在应用程序容器内的文档将继续显示。

使用新的无处不在外部文档作用域将返回实时外部应用程序容器并为其返回的元数据文档。 `NSMetadataItemUrlKey`将指向 URL 文档所在的位置实际。

有时应用程序不需要处理所指向的 th 引用的文档。 相反，应用程序想要直接使用参考文档。 例如，应用程序可能想要显示在 UI 中，应用程序的文件夹中的文档或要允许用户进行的文件夹中来回移动引用。

在 iOS 8，新`NSMetadataItemUrlInLocalContainerKey`已提供直接访问参考文档。 此关键点实际引用添加到应用程序容器中的外部文档。

`NSMetadataUbiquitousItemIsExternalDocumentKey`用于测试是否文档是外部的应用程序的容器。 `NSMetadataUbiquitousItemContainerDisplayNameKey`用于访问外壳的外部文档的原始副本的容器的名称。

### <a name="why-document-references-are-required"></a>文档引用所需的原因

该 iOS 8 使用引用来访问外部文档的主要原因是安全的。 没有应用程序是授予对任何其他应用程序的容器的访问权限。 仅文档选取器可以这样做，因为是正在运行进程外并且具有系统级访问。

可用于访问外部应用程序容器文档的唯一方法是使用文档选取器，并且如果返回 URL 选取器会安全作用域。 安全作用域 URL 包含刚好够用的信息来选择该文档以及指定了作用域将授予对文档的应用程序访问权限所需的权限。

请务必要注意，是否安全作用域 URL 序列化为字符串，且然后反序列化，安全信息将会丢失，并且该文件将无法访问的 url。 文档引用功能提供了一种机制，即可返回到这些 Url 指向的文件。

因此，如果应用程序获取`NSUrl`从一个参考文档，它已经具有附加的安全作用域，而且可以用于访问该文件。 因此，强烈建议开发人员使用`UIDocument`因为它为其处理所有这些信息和过程。

### <a name="using-bookmarks"></a>使用书签

它并不总是可行枚举应用程序的文档，即可返回到特定文档，例如，在执行还原状态。 iOS 8 提供一种机制来创建直接针对给定的文档的书签。

下面的代码将创建从书签`UIDocument`的`FileUrl`属性：

```csharp
// Trap all errors
try {
    // Values to include in the bookmark packet
    var resources = new string[] {
        NSUrl.FileSecurityKey,
        NSUrl.ContentModificationDateKey,
        NSUrl.FileResourceIdentifierKey,
        NSUrl.FileResourceTypeKey,
        NSUrl.LocalizedNameKey
    };

    // Create the bookmark
    NSError err;
    Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

    // Was there an error?
    if (err != null) {
        // Yes, report it
        Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
    }
}
catch (Exception e) {
    // Report error
    Console.WriteLine ("Error: {0}", e.Message);
}
```

使用现有的书签 API 来创建针对现有书签`NSUrl`，可以保存和加载为提供的外部文件的直接访问。 下面的代码会将上面创建的书签还原：

```csharp
if (Bookmark != null) {
    // Trap all errors
    try {
        // Yes, attempt to restore it
        bool isBookmarkStale;
        NSError err;
        var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

        // Was there an error?
        if (err != null) {
            // Yes, report it
            Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
        } else {
            // Load document from bookmark
            OpenDocument (srcUrl);
        }
    }
    catch (Exception e) {
        // Report error
        Console.WriteLine ("Error: {0}", e.Message);
    }
}
```

## <a name="open-vs-import-mode-and-the-document-picker"></a>打开 vs。导入模式和文档选取器

文档选取器视图控制器特点是具有两个不同的操作模式：

1.  **打开模式**– 在此模式下，当用户选择和外部的文档，文档选取器将创建一个安全作用域书签在应用程序容器中。   
 
    [![](document-picker-images/image37.png "安全作用域的应用程序容器中的书签")](document-picker-images/image37.png#lightbox)
1.  **导入模式**– 在此模式下，当用户选择和外部的文档、 文档选取器将不创建书签，但相反，将文件复制到临时位置并提供对此位置上的文档的应用程序访问权限：   
 
    [![](document-picker-images/image38.png "文档选取器将将文件复制到临时位置，并提供将应用程序访问此位置上的文档")](document-picker-images/image38.png#lightbox)   
 一旦应用程序终止出于任何原因，清空临时位置，并删除该文件。 如果应用程序需要维护对文件的访问，它应制作副本，并将其放在其应用程序容器中。


当应用程序想要与另一个应用程序进行协作和共享对与该应用程序文档进行任何更改，打开的模式十分有用。 当应用程序不想与其他应用程序共享文档对其进行修改时，使用导入模式。

## <a name="making-a-document-external"></a>使文档成为外部

如上所述，iOS 8 应用程序没有对在其自己的应用程序容器之外的容器的访问。 该应用程序可以写入其自己的容器，本地或到临时位置，然后使用特殊的文档模式将在应用程序容器之外生成的文档移到用户选择位置。

要移动到外部位置的文档，请执行以下操作：

1.  第一次在本地或临时位置创建一个新的文档。
1.  创建`NSUrl`指向新的文档。
1.  打开新的文档选取器视图控制器并将其传递`NSUrl`模式`MoveToService`。 
1.  一旦用户选择新的位置，该文档将从移动其当前位置到新位置。
1.  参考文档，以便仍可以通过创建应用程序访问该文件将写入应用程序的应用程序容器。


下面的代码可以用于将文档移到外部位置： `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

通过上述过程返回的引用文档正是其中一个由文档选取器将打开模式相同。 但是，在应用程序可能想要移动而不保留对它的引用的文档的时间。

若要移动而不生成引用的文档，使用`ExportToService`模式。 示例：`var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

使用时`ExportToService`模式，则文档将复制到外部容器和现有的副本保留在其原始位置。

## <a name="document-provider-extensions"></a>文档提供程序扩展

IOS 8，与 Apple 想要能够访问其基于云的文档，无论其中它们实际存在的任何最终用户。 若要实现此目标，iOS 8，请提供新的文档提供程序扩展机制。

### <a name="what-is-a-document-provider-extension"></a>什么是文档的提供程序扩展？

文档提供程序扩展简单地说，是为开发人员或第三方的方法提供到可在现有的 iCloud 存储位置完全相同的方式访问应用程序备用文档存储。

用户可以从文档选取器选择其中一个备选存储位置，并且它们可以使用完全相同访问模式 （打开、 导入、 移动或导出） 来处理该位置中的文件。

这是使用两个不同的扩展来实现的：

-  **文档选取器扩展**– 提供`UIViewController`提供用户选择了备选存储位置从文档的图形接口的子类。 此子类将显示为文档选取器视图控制器的一部分。
-  **文件提供扩展名**– 这是一个处理实际提供的文件内容的非 UI 扩展。 这些扩展通过文件协调提供 ( `NSFileCoordinator` )。 这是另一个重要的用例要求文件协调的位置。


使用文档提供程序扩展时下, 图说明了典型的数据流：

 [![](document-picker-images/image39.png "此关系图说明了典型的数据流时使用文档提供程序扩展")](document-picker-images/image39.png#lightbox)

执行以下过程：

1.  应用程序显示要允许用户选择要使用的文件的文档选取器控制器。
1.  用户选择了备选文件位置和自定义`UIViewController`扩展调用以显示用户界面。
1.  用户从该位置选择一个文件并将 URL 传递回文档选取器。
1.  文档选取器选择文件的 URL，并将其返回给使用者处理的应用程序。
1.  将 URL 传递到文件协调器，以返回到应用程序的文件内容。
1.  文件处理协调器调用自定义的文件提供程序扩展，以检索的文件。
1.  文件的内容将返回到文件处理协调器。
1.  文件的内容返回到应用程序。


### <a name="security-and-bookmarks"></a>安全和书签

本部分将需要快速了解一下通过书签适用于文档提供程序扩展的访问安全性和永久文件的方式。 与 iCloud 文档的提供程序，自动将安全和书签保存到应用程序容器，因为它们不是文档参考系统的一部分，将不文档提供程序扩展。

例如： 在企业环境中提供其自己的公司范围内安全数据存储，管理员不想访问或由公共 iCloud 服务器处理的机密公司信息。 因此，不能使用内置的文档引用系统。

仍然可以使用书签系统和负责要正确处理标有书签的 URL，并返回指向由该文档的内容的文件提供程序扩展。

出于安全考虑，iOS 8 具有一个隔离层，它仍然存在哪些应用程序有权访问哪些文件提供程序的标识符的信息。 应注意的是，所有文件访问都受此隔离层。

使用书签和文档提供程序扩展时下, 图说明了数据流：

 [![](document-picker-images/image40.png "使用书签和文档提供程序扩展时，此关系图说明了数据流")](document-picker-images/image40.png#lightbox)

执行以下过程：

1.  应用程序即将进入后台，并且需要持久保存其状态。 它调用`NSUrl`备用存储区中创建文件书签。
1.  `NSUrl` 调用要获取指向文档的持久性 URL 的文件提供程序扩展。 
1.  文件提供程序扩展到字符串形式返回的 URL `NSUrl` 。
1.  `NSUrl`捆绑到书签的 URL 并将其返回给应用程序。
1.  当应用程序从已在后台中被唤醒，并且需要还原状态时，它将传递到书签`NSUrl`。
1.  `NSUrl` 调用文件提供程序扩展名与文件的 URL。
1.  文件的扩展提供程序访问该文件，并返回到文件的位置`NSUrl`。
1.  文件位置是与安全信息捆绑在一起，并返回到应用程序。


从此处，应用程序可以访问该文件，并像平时一样使用它。

### <a name="writing-files"></a>写入文件

本部分将需要快速了解一下如何写入文件到另一个位置与文档提供程序扩展配合使用。 IOS 应用程序将使用文件协调将信息保存到应用程序容器中的磁盘。 很快文件已成功写后，将更改的通知文件提供程序扩展。

此时，文件提供程序扩展可以启动将文件上载到替代位置 （或将文件标记为脏和需要上载）。

### <a name="creating-new-document-provider-extensions"></a>创建新文档提供程序扩展

创建新文档提供程序扩展是本介绍性文章的范围之外。 此处显示，根据用户在其 iOS 设备中已加载的扩展，应用程序可能具有访问对 Apple 提供 iCloud 位置之外的文档存储位置提供此信息时。

使用文档选取器时，应注意这一事实的开发人员和外部文档使用。 它们不应假定 iCloud 中托管这些文档。

有关创建的存储提供程序或文档选取器扩展的详细信息，请参阅[应用扩展简介](~/ios/platform/extensions.md)文档。

## <a name="migrating-to-icloud-drive"></a>迁移到 iCloud 驱动器

在 iOS 8 中，用户可以选择继续使用现有的 iCloud iOS 7 （和以前的系统） 中使用文档系统或他们可以选择将现有文档迁移到新的 iCloud 驱动器机制。

在 Mac OS X Yosemite，Apple 不提供向后兼容性因此所有文档必须都迁移到 iCloud 驱动器或它们将不再更新跨设备。

用户的帐户已迁移到 iCloud 驱动器后，只有使用 iCloud 驱动器的设备将能够将更改传播到文档跨这些设备。

> [!IMPORTANT]
> 开发人员应注意这篇文章中涉及的新功能才可用用户的帐户已迁移到 iCloud 驱动器。 

## <a name="summary"></a>总结

本文已覆盖到现有 iCloud Api 支持 iCloud 驱动器和新的文档选取器视图控制器所需的更改。 它已覆盖文件协调和使用基于云的文档时的重要性。 它涵盖启用基于云的 Xamarin.iOS 应用程序中的文档所需的设置并提供使用应用程序的应用程序容器使用文档选取器视图控制器外部的文档的简介。

此外，本文简要介绍文档提供程序扩展和为什么开发人员时，应注意到它们编写的应用程序可以处理基于云的文档。

## <a name="related-links"></a>相关链接

- [DocPicker （示例）](https://developer.xamarin.com/samples/monotouch/ios8/DocPicker/)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)
- [应用程序扩展简介](~/ios/platform/extensions.md)
