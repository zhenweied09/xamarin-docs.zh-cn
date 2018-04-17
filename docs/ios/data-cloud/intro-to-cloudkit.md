---
title: CloudKit
description: iCloud Api 使 iOS 8 应用程序能够将数据存储在 iCloud，用于在用户的帐户的自动同步的支持。 使用 CloudKit 向用户提供一致的、 无缝体验跨 iCloud 启用的设备。 本文介绍如何在使用方便 API 的 iOS 8 应用程序中启用 CloudKit。
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/11/2016
ms.openlocfilehash: 4765b91bdc1f64be7e2ea67622db7af8634f1347
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="cloudkit"></a>CloudKit

_iCloud Api 使 iOS 8 应用程序能够将数据存储在 iCloud，用于在用户的帐户的自动同步的支持。使用 CloudKit 向用户提供一致的、 无缝体验跨 iCloud 启用的设备。本文介绍如何在使用方便 API 的 iOS 8 应用程序中启用 CloudKit。_

该访问 iCloud，CloudKit framework 简化了开发应用程序。 这包括应用程序数据和资产权限，以及能够安全地存储应用程序信息的检索。 此工具包提供用户的匿名层通过允许访问其 iCloud Id 与应用程序而不用共享个人信息。

开发人员可以专注于其客户端应用程序，并让 iCloud 时无需编写服务器端应用程序逻辑。 CloudKit 提供身份验证、 专用和公用数据库和结构化的数据和资产存储服务。

> [!IMPORTANT]
> Apple[提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)以帮助开发人员正确地处理欧盟的常规数据保护法规 (GDPR)。

## <a name="requirements"></a>要求

以下是所需完成这篇文章中提供的步骤：

-  **Xcode 和 iOS SDK** – Apple 的 Xcode 和 iOS 8 Api 需要进行安装和配置的开发人员计算机上。
-  **Visual Studio for Mac** – 应安装最新版本的适用于 Mac 的 Visual Studio，并将其配置用户设备上。
-  **iOS 8 设备**– 运行用于测试 iOS 8 的最新版本的 iOS 设备。

## <a name="what-is-cloudkit"></a>什么是 CloudKit？

CloudKit 是到 iCloud 服务器使开发人员可以访问的方法。 它为 iCloud 驱动器和 iCloud 照片库提供了基础。 CloudKit 支持在 Mac OS X 和 Apple iOS 设备上。

 [![](intro-to-cloudkit-images/image1.png "如何在 Mac OS X 和 Apple iOS 设备上支持 CloudKit")](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit 使用 iCloud 帐户基础结构。 如果没有用户登录到 iCloud 在设备上的帐户，CloudKit 将使用其 ID 来标识用户。 如果没有帐户可用，然后将提供有限的只读访问权限。

CloudKit 支持这两个公用和专用数据库的概念。 公共数据库提供"浓汤"的用户有权访问的所有数据。 专用数据库是为了存储绑定到特定用户的专用数据。

CloudKit 支持结构化和大容量数据。 它是能够无缝地处理大型文件传输。 CloudKit 负责高效地传输大型文件与 iCloud 服务器在后台，释放开发人员可以专注于其他任务。

> [!NOTE]
> 务必要注意 CloudKit 是_传输技术_。 它不提供任何持久性;它仅允许应用程序发送和有效地从服务器中接收信息。

截至撰写本文时，Apple 会最初向 CloudKit 免费提供带宽和存储容量较高的限制。 对于更大的项目或应用程序使用大型用户基本，Apple 具有提示将提供整个经济实惠的定价范围。


## <a name="enabling-cloudkit-in-a-xamarin-application"></a>在 Xamarin 应用程序中启用 CloudKit

Xamarin 应用程序可以利用 CloudKit Framework 之前，必须正确配置应用程序所详述[使用功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)和[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南

1.  用于 Mac 或 Visual Studio，在 Visual Studio 中打开项目。
2.  在**解决方案资源管理器**，打开**Info.plist**文件，并确保**捆绑标识符**匹配已在中定义的一个**应用程序 ID**创建，因为设置的一部分设置：
 
    [![](intro-to-cloudkit-images/image26a.png "输入的捆绑标识符")](intro-to-cloudkit-images/image26a-orig.png#lightbox "Info.plist file displaying Bundle Identifier")

3.  向下的滚动到底部**Info.plist**文件，然后选择**启用后台模式**，**位置更新**和**远程通知**:

    [![](intro-to-cloudkit-images/image27a.png "选择已启用的后台模式、 位置更新和远程通知")](intro-to-cloudkit-images/image27a-orig.png#lightbox "Info.plist file displaying background modes")
4.  右键单击解决方案并选择中的 iOS 项目**选项**。
5.  选择**iOS 捆绑签名**，选择**开发人员标识**和**预配配置文件**上述步骤中创建。
6.  确保**Entitlements.plist**包括**启用 iCloud** ，**键-值存储**和**CloudKit** 。
7.  确保**无处不容器**应用程序 （如上面创建） 中存在。 示例：`iCloud.com.your-company.CloudKitAtlas`
8.  保存对文件所做的更改。


使用就地这些设置，应用程序现在已准备好访问 CloudKit Framework Api。

## <a name="cloudkit-api-overview"></a>CloudKit API 概述

在实施之前 CloudKit Xamarin iOS 应用程序中，这篇文章要涵盖 CloudKit 框架，它将包括以下主题的基础知识：

1.  **容器**– 隔离接收器 iCloud 通信。
2.  **数据库**– 公共和私有对应用程序都可用。
3.  **记录**– 与 CloudKit 结构化的数据移动的机制。
4.  **记录区域**– 是一组记录。
5.  **记录标识符**– 完全标准化和表示记录的特定位置。
6.  **引用**– 提供父-子给定数据库内的相关记录之间的关系。
7.  **资产**– 允许的大型非结构化的数据将被上载到 iCloud 且与给定的记录关联的文件。


### <a name="containers"></a>容器

始终运行的 iOS 设备上运行的给定应用程序以及端其他应用程序和在该设备上的服务。 在客户端设备上，将应用程序孤立或以某种方式沙盒。 在某些情况下，这是文本沙盒，和其他情况下，应用程序只需在其中运行的是自己的内存空间。

获取客户端应用程序并运行它与其他客户端分离的概念是非常强大，提供以下优点：

1.  **安全**– 一个应用程序不能干扰其他客户端应用程序或操作系统本身。
1.  **稳定性**– 如果客户端应用程序崩溃，不能采用出的操作系统的其他应用。
1.  **隐私**– 每个客户端应用程序具有有限的访问存储设备中个人信息。


CloudKit 旨在提供相同的好处，如上面列出，并将它们应用于使用基于云的信息：

 [![](intro-to-cloudkit-images/image31.png "CloudKit 应用进行通信使用容器")](intro-to-cloudkit-images/image31.png#lightbox)

就像应用程序正在一样的一对多运行在设备上，因此是与 iCloud 一多的应用程序的通信。 每个这些不同的通信接收器称为容器。

容器都公开在通过 CloudKit Framework`CKContainer`类。 默认情况下，一个应用程序到一个容器和此容器的谈话的承受程度分离该应用程序的数据。 这意味着，多个应用程序可以存储信息与相同的 iCloud 帐户，同时还可将永远不会混合此信息。

ICloud 数据的容器化还允许 CloudKit 封装用户信息。 这种方式，应用程序将有一些有限的访问权限到 iCloud 帐户和用户信息存储在同时仍保护的私密性和安全性的用户。

通过 WWDR 门户应用程序的开发人员完全管理容器。 容器的命名空间是跨所有 Apple 开发人员，全局的因此容器必须不只是唯一的给定的开发人员应用程序内容，但所有 Apple 开发人员和应用程序。

Apple 提供的建议针对应用程序容器创建命名空间时使用反向 DNS 表示法。 示例:

```csharp
iCloud.com.company-name.application-name
```

虽然容器是的默认情况下，绑定一对一到给定的应用程序，它们可以应用程序间共享。 因此，在单个容器上可以协调多个应用程序。 单个应用程序还可与多个容器。

### <a name="databases"></a>数据库

CloudKit 的主要功能之一是需要应用程序的数据模型和复制最多 iCloud 服务器该模型。 某些信息适用于创建它的用户、 其他信息是以公共方式使用 （如餐馆评论），用户无法创建的公共数据或它可能是开发人员已发布应用程序的信息。 在任一情况下，受众不只是单个用户，但社区的人员。

 [![](intro-to-cloudkit-images/image32.png "CloudKit 容器关系图")](intro-to-cloudkit-images/image32.png#lightbox)

之内一个容器，首先是公共的数据库。 这是其中的所有公共信息存在，共同 mingles。 此外，还有一些应用程序的每个用户的多个单个专用数据库。

当 iOS 设备上运行，应用程序将仅具有对当前登录的 iCloud 用户的信息的访问。 因此应用程序的视图的容器将如下所示：

 [![](intro-to-cloudkit-images/image33.png "容器应用程序视图")](intro-to-cloudkit-images/image33.png#lightbox)

它仅可以查看公共数据库和专用数据库与当前登录的 iCloud 用户关联。

数据库都公开在通过 CloudKit Framework`CKDatabase`类。 每个应用程序有权访问两个数据库： 专用和公共数据库。

容器是 CloudKit 的初始入口点。 下面的代码可以用于从应用程序的默认容器访问的公共和专用数据库：

```csharp
using CloudKit;
...

public CKDatabase PublicDatabase { get; set; }
public CKDatabase PrivateDatabase { get; set; }
...

// Get the default public and private databases for
// the application
PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;
```

以下是数据库类型之间的差异：

||公共数据库|专用数据库|
|---|--- |--- |
|**数据类型**|共享的数据|当前用户的数据|
|**配额**|在开发人员配额中考虑的|在用户的配额中考虑的|
|**默认权限**|可读的 world|用户可读|
|**编辑权限**|通过记录类级别的 iCloud 仪表板角色|不可用|

### <a name="records"></a>记录

容器保存数据库，并且在数据库记录。 记录是与其他 CloudKit 结构化的数据移动的机制：

 [![](intro-to-cloudkit-images/image34.png "容器包含数据库，以及在数据库内是记录")](intro-to-cloudkit-images/image34.png#lightbox)

记录都公开在通过 CloudKit Framework`CKRecord`类，该类包装键-值对。 应用程序中的对象实例是否等效于`CKRecord`CloudKit 中。 此外，每个`CKRecord`拥有一个记录类型，这是相当于对象的类。

记录具有在实时架构，因此之前关闭在提交以进行处理的数据到 CloudKit 进行了描述。 从该点 CloudKit 将解释信息和处理的存储和检索记录的后勤工作。

`CKRecord`类还支持范围广泛的元数据。 例如，记录包含有关创建时和创建它的用户信息。 记录还包含有关最后修改时间和修改它的用户的信息。

记录包含更改标记的概念。 这是记录的以前版本的给定的修订版本。 更改标记用作确定如果客户端和服务器具有相同版本的给定的记录的轻量方法。

如前所述，`CKRecords`包装键-值对，并且可以在这种情况下，在记录中存储存储以下类型的数据：

1.   `NSString`
1.   `NSNumber`
1.   `NSData`
1.   `NSDate`
1.   `CLLocation`
1.   `CKReferences`
1.   `CKAssets`


一个值类型，除了记录可以包含任意上述列出类型的同构数组。

可以使用下面的代码创建一条新记录，并将其存储在数据库中：

```csharp
using CloudKit;
...

private const string ReferenceItemRecordName = "ReferenceItems";
...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>记录区域

记录给定数据库内不存在自行 – 在记录区域内一起存在的组的记录。 记录区域可以被想象成表传统的关系数据库中：

 [![](intro-to-cloudkit-images/image35.png "在记录区域内一起存在的记录的组")](intro-to-cloudkit-images/image35.png#lightbox)

可以在给定的记录区域内的多个记录和给定数据库内的多个记录区域。 每个数据库包含默认记录区域：

 [![](intro-to-cloudkit-images/image36.png "每个数据库包含的默认记录区域和自定义区域")](intro-to-cloudkit-images/image36.png#lightbox)

这是默认情况下存储记录的位置。 此外，可以创建自定义记录区域。 记录完成在哪些原子提交和更改跟踪的基粒度的区域表示。

## <a name="record-identifiers"></a>记录标识符

元组形式的记录标识符形式表示，二者都包含客户端提供记录名称以及存在该记录的区域。 记录标识符具有以下特征：

-  就会创建客户端应用程序。
-  它们完全标准化和表示记录的特定位置。
-  通过为记录名称分配外数据库中记录的唯一 ID，它们可以用于桥接未存储在 CloudKit 的本地数据库。


当开发人员创建新记录时，他们可以选择将记录标识符。 如果未指定记录标识符，将自动创建并分配给记录的 UUID。

当开发人员创建新的记录标识符时，他们可以选择指定每个记录将属于该记录区域。 如果未指定，则将使用默认记录区域。

记录标识符都公开在通过 CloudKit Framework`CKRecordID`类。 下面的代码可以用于创建新的记录标识符：

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>参考资料

引用提供给定数据库内的相关记录之间的关系：

 [![](intro-to-cloudkit-images/image37.png "引用提供给定数据库内的相关记录之间的关系")](intro-to-cloudkit-images/image37.png#lightbox)

在上面的示例中，父项，以便子记录的父记录，该子级则拥有子级。 关系从子记录将转到父记录，并称为*返回引用*。

引用都公开在通过 CloudKit Framework`CKReference`类。 它们是一种让 iCloud 服务器了解记录之间的关系。

引用提供后面级联删除的机制。 如果父记录删除从数据库中，从数据库也将自动删除任何子记录 （在关系中指定）。

> [!NOTE]
> 使用 CloudKit 时，无关联的指针不可能。 例如，提取的记录的指针的列表，选择一条记录并为记录然后系统要求你应用程序时，该记录可能不再存在数据库中。 若要适当地处理这种情况下，必须编码应用程序。

尽管不要求这样做，返回引用首选使用时都是 CloudKit Framework。 Apple 具有微调系统后，若要简化此引用的最有效类型。

当创建的引用，开发人员可以提供已在内存中的记录或创建对记录标识符的引用。 如果使用一个记录标识符和指定的引用数据库中不存在，则将创建无关联的指针。

下面是创建针对某个已知的记录的引用的示例：

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>资产

资产允许的大型非结构化的数据将被上载到 iCloud 且与给定的记录关联的文件：

 [![](intro-to-cloudkit-images/image38.png "资产允许的大型非结构化的数据将被上载到 iCloud 且与给定的记录关联的文件")](intro-to-cloudkit-images/image38.png#lightbox)

在客户端，`CKRecord`创建描述要上载到 iCloud 服务器上的文件。 A`CKAsset`创建了包含文件，并链接到描述该记录。

当文件上载到服务器时，记录都放在数据库中，并且将文件复制到特殊的大容量存储数据库。 指针的记录和上载的文件之间创建链接。

资产都公开在通过 CloudKit Framework`CKAsset`类，用于存储大型的、 非结构化数据。 由于开发人员永远不会想要在内存中具有大型的、 非结构化数据，资产被实现使用磁盘上的文件。

资产归记录，这允许从使用为指针的记录的 iCloud 要检索的资产。 以这种方式服务器可以垃圾收集的资产，拥有资产的记录被删除时。

因为`CKAssets`旨在处理大型数据文件，Apple 设计 CloudKit 高效上载和下载的资产。

下面的代码可以用于创建资产并将其与记录相关联：

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

现在，我们已介绍所有 CloudKit 中的基本对象。 容器与应用程序相关联，包含数据库。 数据库包含分组到记录区域，并指向由记录标识符的记录。 定义之间记录使用引用的父-子关系。 最后，可以上载大型文件并将其与使用资产的记录相关联。

## <a name="cloudkit-convenience-api"></a>CloudKit 方便 API

Apple 提供用于处理 CloudKit 中两个不同的 API 集：

-  **Operational API** – 提供 CloudKit 每个单个功能。 对于更复杂的应用程序，此 API 提供对 CloudKit 细化的控制。
-  **方便 API** – 提供一个常见的预配置 CloudKit 功能子集。 它可用于在 iOS 应用程序中包括 CloudKit 功能提供方便、 方便地访问解决方案。


方便 API 通常是大多数 iOS 应用程序的最佳选择，Apple 提供的建议开始使用它。 本部分的其余部分将介绍以下便利 API 主题：

-  正在保存一条记录。
-  获取记录。
-  更新记录。


### <a name="common-setup-code"></a>常见安装代码

入门 CloudKit 方便 API 之前, 没有所需的某些标准安装程序代码。 通过修改应用程序的启动`AppDelegate.cs`文件并使其如下所示：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using UIKit;
using CloudKit;

namespace CloudKitAtlas
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Computed Properties
        public override UIWindow Window { get; set;}
        public CKDatabase PublicDatabase { get; set; }
        public CKDatabase PrivateDatabase { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            application.RegisterForRemoteNotifications ();

            // Get the default public and private databases for
            // the application
            PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
            PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;

            return true;
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            Console.WriteLine ("Registered for Push notifications with token: {0}", deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications (UIApplication application, NSError error)
        {
            Console.WriteLine ("Push subscription failed");
        }

        public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
        {
            Console.WriteLine ("Push received");
        }
        #endregion
    }
}
```

上面的代码显示快捷方式，使它们更轻松地使用应用程序的其余部分中所占的公用和专用 CloudKit 数据库。

接下来，将以下代码添加到任何视图或视图将使用 CloudKit 的容器：

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

这将添加的快捷方式可用于访问`AppDelegate`和访问上面创建的公用和专用数据库快捷方式。

使用此代码中的位置，让我们看看在 Xamarin iOS 8 应用程序中实现 CloudKit 方便 API。

### <a name="saving-a-record"></a>保存一条记录

使用上面介绍讨论记录时模式时，下面的代码将创建一条新记录，并使用方便 API 将它保存到公共数据库：

```csharp
private const string ReferenceItemRecordName = "ReferenceItems";
...

// Create a new record
var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;

// Save it to the database
ThisApp.PublicDatabase.SaveRecord(newRecord, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

有关上述代码中，需要注意的三个操作：

1.  通过调用`SaveRecord`方法`PublicDatabase`，开发人员无需指定如何发送数据，它写入，则为等哪些区域。方便 API 是关注的那些详细信息的所有本身。
1.  调用是异步的与成功或失败而在调用完成时提供的回调例程。 如果调用失败，将提供一条错误消息。
1.  CloudKit 不提供本地存储持久性;它是仅一个传输媒介。 因此当发出请求以保存一条记录，它是立即发送到 iCloud 服务器。


> [!NOTE]
> 由于移动网络通信，其中连接不断地删除或中断，开发人员必须进行错误处理使用 CloudKit 时的第一个注意事项之一的"有损"特性。

### <a name="fetching-a-record"></a>获取记录

通过记录创建和成功存储 iCloud 服务器上，使用下面的代码以检索的记录：

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

一样保存该记录，上面的代码是异步的简单，要求很好的错误处理。

### <a name="updating-a-record"></a>更新记录

已从 iCloud 服务器提取的记录后，可以使用下面的代码，若要修改的记录并将所做的更改保存回数据库：

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {

    } else {
        // Modify the record
        record["name"] = (NSString)"New Name";

        // Save changes to database
        ThisApp.PublicDatabase.SaveRecord(record, (r, e) => {
            // Was there an error?
            if (e != null) {
                 ...
            }
        });
    }
});
```

`FetchRecord`方法`PublicDatabase`返回`CKRecord`如果调用是否成功。 然后，应用程序修改的记录和调用`SaveRecord`再次以便将所做的更改写回到数据库。

本部分说明了使用 CloudKit 方便 API 时，将使用应用程序的典型周期。 应用程序会将记录保存到 iCloud、 从 iCloud 检索这些记录，修改记录并将这些更改保存回 iCloud。

## <a name="designing-for-scalability"></a>设计为可伸缩性

到目前为止本文已经查看了存储和检索从 iCloud 服务器、 应用程序的整个对象模型，每次它要使用过。 尽管此方法适用于少量的数据和非常小的用户群，它可伸缩性差时的信息和/或用户量基增加。

### <a name="big-data-tiny-device"></a>大数据，微小的设备

一个应用程序更受欢迎变得，在数据库中的数据越多，而且很具有的设备上的整个数据缓存越低可行。 可以使用以下方法来解决此问题：

-  **将大型数据保留在云中**– CloudKit 旨在高效地处理大型数据。
-  **客户端应仅查看该数据的一个切片**– 关闭几乎是最少的所需数据来处理在给定时间的任何任务。
-  **客户端视图可以更改**– 由于每个用户具有不同的首选项、 正在显示的数据切片可以更改在用户和用户和用户的单个任何的视图给定的切片可能不同。
-  **客户端使用查询来专注角度来看**– 查询允许用户在云内查看存在较大数据集中的一个小子集。


### <a name="queries"></a>查询

如上面所述，查询将允许开发人员可以选择在云中存在较大数据集中的一个小子集。 查询都公开在通过 CloudKit Framework`CKQuery`类。

查询将合并三个不同的情况： 一种记录类型 ( `RecordType`)，谓词 ( `NSPredicate`) 和 （可选） 排序描述符 ( `NSSortDescriptors`)。 CloudKit 支持的大多数`NSPredicate`。

#### <a name="supported-predicates"></a>支持的谓词

CloudKit 支持以下类型的`NSPredicates`使用查询时：


1. 匹配名等于存储在变量的值的记录数：
    
    ```
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```
   
2. 允许匹配基于动态的密钥值，以便密钥无需在编译时知道：
    
    ```
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```
    
3. 匹配记录的值大于给定的值的记录数：
   
    ```
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. 匹配记录的位置位于 100 米的给定位置的记录数：
    
    ```
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit 支持标记的搜索。 此调用将创建两个令牌，一个用于`after`，另一个用于`session`。 它将返回包含这些两个令牌的记录：
    
    ```
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```
    
 6. CloudKit 支持复合使用联接谓词`AND`运算符。
    
    ```
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```
    


#### <a name="creating-queries"></a>创建查询

下面的代码可以用于创建`CKQuery`Xamarin iOS 8 应用程序中：

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

首先，它将创建一个谓词来选择匹配具有给定名称的记录。 然后它创建一个查询，将选择的给定记录类型的记录，该谓词相匹配。

#### <a name="performing-a-query"></a>执行查询

一旦创建查询后，使用下面的代码来执行查询和处理返回的记录：

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = {0}", recordName));
var query = new CKQuery("CloudRecords", predicate);

ThisApp.PublicDatabase.PerformQuery(query, CKRecordZone.DefaultRecordZone().ZoneId, (NSArray results, NSError err) => {
    // Was there an error?
    if (err != null) {
       ...
    } else {
        // Process the returned records
        for(nint i = 0; i < results.Count; ++i) {
            var record = (CKRecord)results[i];
        }
    }
});
```

上面的代码将获取上述步骤中创建查询，并针对公共数据库执行它。 由于未记录指定区域，来搜索所有区域。 如果不发生任何错误，数组`CKRecords`将匹配查询的参数返回。

考虑查询的方法是： 它们都是轮询，并非常适合于通过大型数据集进行切片。 查询，但是，不适合大型、 通常处于静态的数据集因以下原因：

-  它们是错误的设备的电池使用时间。
-  它们是错误的网络流量。
-  它们不好的用户体验，因为他们将看到的信息受到应用程序正在轮询数据库的频率。 发生更改时，用户现在预期推送通知。


### <a name="subscriptions"></a>订阅

在处理大型、 通常处于静态的数据集，不应该在客户端设备上执行查询，它应在代表客户端的服务器上运行。 查询应在后台运行，并在每个单个记录保存，不论是由于当前设备还是接触同一个数据库的另一台设备后应执行。

最后，一条推送通知应发送到服务器端查询运行时附加到数据库的每个设备。

订阅都公开在通过 CloudKit Framework`CKSubscription`类。 它们将记录类型的组合 ( `RecordType`)，谓词 ( `NSPredicate`) 和 Apple 推送通知 ( `Push`)。

> [!NOTE]
> 因为它们具有包含 CloudKit 特定的信息，如是什么原因导致推送发生的负载略有进一步扩展 CloudKit 推送。

#### <a name="how-subscriptions-work"></a>订阅的工作原理

在实施之前订阅在 C# 代码，让我们订阅的工作原理的快速的概述：

 [![](intro-to-cloudkit-images/image39.png "订阅如何工作的概述")](intro-to-cloudkit-images/image39.png#lightbox)

上图显示典型订阅过程，如下所示：

1.  客户端设备将创建新的订阅包含的将触发该订阅，该触发器发生时将发送推送通知的条件集。
2.  订阅发送到数据库添加到的现有订阅的集合下。
3.  另一台设备创建一条新记录，并将该记录保存到数据库。
4.  数据库中搜索其新的记录是否与任何其条件匹配的订阅的列表。
5.  如果找到匹配项，则推送通知发送到的设备的注册订阅使用有关导致触发的记录的信息。


就地了解这一点，让我们看一下在 Xamarin iOS 8 的应用程序创建订阅。

#### <a name="creating-subscriptions"></a>创建订阅

下面的代码可以用于创建订阅：

```csharp
// Create a new subscription
DateTime date;
var predicate = NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date));
var subscription = new CKSubscription("RecordType", predicate, CKSubscriptionOptions.FiresOnRecordCreation);

// Describe the type of notification
var notificationInfo = new CKNotificationInfo();
notificationInfo.AlertLocalizationKey = "LOCAL_NOTIFICATION_KEY";
notificationInfo.SoundName = "ping.aiff";
notificationInfo.ShouldBadge = true;

// Attach the notification info to the subscription
subscription.NotificationInfo = notificationInfo;
```

首先，它将创建一个谓词，提供条件来触发订阅。 接下来，它创建针对特定记录类型的订阅，并设置在测试该触发器时的选项。 最后，它定义订阅触发，并将其附加订阅时，将发生的通知的类型。

### <a name="saving-subscriptions"></a>正在保存订阅

创建的订阅，使用下面的代码会将其保存到数据库：

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

使用方便 API 时，调用是异步的简单，并提供方便的错误处理。

#### <a name="handling-push-notifications"></a>处理推送通知

如果开发人员具有以前使用 Apple 推送通知 (AP)，则应熟悉处理通过 CloudKit 生成的通知的过程。

在`AppDelegate.cs`，重写`ReceivedRemoteNotification`类，如下所示：

```csharp
public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
{
    // Parse the notification into a CloudKit Notification
    var notification = CKNotification.FromRemoteNotificationDictionary (userInfo);

    // Get the body of the message
    var alertBody = notification.AlertBody;

    // Was this a query?
    if (notification.NotificationType == CKNotificationType.Query) {
        // Yes, convert to a query notification and get the record ID
        var query = notification as CKQueryNotification;
        var recordID = query.RecordId;
    }
}
```

上面的代码要求 CloudKit 若要将 userInfo 解析到 CloudKit 通知。 接下来，将信息提取有关警报的信息。 最后，测试通知的类型，并相应地处理通知。

本部分说明了如何大数据、 查询和订阅使用上面介绍的小设备问题的回答。 应用程序将在云中，保留其大型数据，并使用这些技术来提供对此数据集。

## <a name="cloudkit-user-accounts"></a>CloudKit 用户帐户

如本文开头所述，CloudKit 构建在现有 iCloud 基础结构之上。 以下部分将介绍，在详细信息，帐户如何向开发人员使用 CloudKit API 公开的信息。

### <a name="authentication"></a>身份验证

在处理与用户帐户时，首先需要考虑是身份验证。 CloudKit 支持在设备上的当前登录的 iCloud 用户通过身份验证。 身份验证发生在后台，并由 iOS 进行处理。 这种方式，开发人员不必担心有关实现身份验证的详细信息。 它们只执行测试以确定是否将用户记录。

### <a name="user-account-information"></a>用户帐户信息

CloudKit 向开发人员提供了以下的用户信息：

-  **标识**– 一种唯一标识用户。
-  **元数据**– 能够保存和检索有关用户的信息。
-  **隐私**– 在隐私意识正常处理的所有信息。 除非用户已同意它公开执行任何操作。
-  **发现**– 使用户能够发现他们正在使用相同的应用程序的朋友。


接下来，我们将查看这些主题的详细信息。

#### <a name="identity"></a>标识

如上面所述，CloudKit 为提供一种要唯一标识给定的用户的应用程序：

 [![](intro-to-cloudkit-images/image40.png "唯一地确定给定的用户")](intro-to-cloudkit-images/image40.png#lightbox)

没有在用户的设备和所有 CloudKit 容器中特定的用户专用数据库上运行的客户端应用程序。 将客户端应用程序链接到这些特定用户之一进行。 这取决于用户登录到设备上本地 iCloud。

因为这来自 iCloud，没有丰富的后备存储的用户信息。 和 iCloud 实际上托管该容器，因为它可以关联用户。 在上面的图中，用户的 iCloud 帐户`user@icloud.com`链接到当前的客户端。

容器的容器，基于唯一，随机生成的用户 ID 进行创建，并与用户的 iCloud 帐户 （电子邮件地址） 关联。 此用户 ID 返回到应用程序，并可以使用任何开发人员认为合适的方式。

> [!NOTE]
> 同一 iCloud 用户在同一设备上运行的不同应用程序将有不同的用户 Id，因为它们已连接到不同 CloudKit 容器。

以下代码获取 CloudKit 用户 ID 的当前记录在设备上的 iCloud 用户：

```csharp
public CKRecordID UserID { get; set; }
...

// Get the CloudKit User ID
CKContainer.DefaultContainer.FetchUserRecordId ((recordID, err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}", err.LocalizedDescription);
    } else {
        // Save user ID
        UserID = recordID;
    }
});
```

上面的代码要求 CloudKit 容器提供当前已登录用户的 ID。 由于此信息来自 iCloud 服务器时，调用是异步的是必需的错误处理。

#### <a name="metadata"></a>元数据

CloudKit 每个用户具有特定的元数据描述了这些功能。 此元数据表示为 CloudKit 记录：

 [![](intro-to-cloudkit-images/image41.png "CloudKit 每个用户都具有描述了这些功能的特定元数据")](intro-to-cloudkit-images/image41.png#lightbox)

在专用数据库内寻找存在的特定用户是容器的定义该用户的一个记录。 有公共数据库的容器的每个用户一个内的多个用户记录。 以下方法之一将具有记录 ID 相匹配当前登录用户的记录 id。

公共数据库中的用户记录而言是完全可读。 它们将被视为，大多数情况下，为普通记录，并且具有一种`CKRecordTypeUserRecord`。 这些记录由系统保留，并不是可用于查询。

使用下面的代码来访问用户记录：

```csharp
public CKRecord UserRecord { get; set; }
...

// Get the user's record
PublicDatabase.FetchRecord(UserID, (record ,er) => {
    //was there an error?
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Save the user record
        UserRecord = record;
    }
});
```

上面的代码要求要返回的是我们访问上面的 ID 的用户的用户记录的公共数据库。 由于此信息来自 iCloud 服务器时，调用是异步的是必需的错误处理。

#### <a name="privacy"></a>隐私

CloudKit 是设计，默认情况下，若要保护的当前登录用户的隐私。 默认情况下公开了有关用户没有个人标识信息。 在某些情况下，应用程序需要有关用户的有限的信息。

在这些情况下，应用程序可以请求用户披露此信息。 向用户询问他们选择性加入公开其帐户信息，将显示一个对话框。

#### <a name="discovery"></a>发现

假设作为中选择允许应用程序用户限制到他们的用户帐户信息的访问，它们可检测到应用程序的其他用户：

 [![](intro-to-cloudkit-images/image42.png "用户可以向应用程序的其他用户可发现")](intro-to-cloudkit-images/image42.png#lightbox)

客户端应用程序与之对话容器和容器与之对话的 iCloud 访问用户信息。 用户可以提供电子邮件地址，并且发现可以用于获取返回有关用户的信息。 （可选） 用户 ID，也可以使用要发现关于用户信息。

CloudKit 还提供了一种方法来发现有关的任何用户都可能是当前登录的朋友的信息通过查询整个通讯簿 iCloud 用户。 CloudKit 过程将请求用户的联系人簿中，并使用的电子邮件地址来看它可以找到其他用户的应用程序与这些地址匹配。

这允许应用程序利用用户的联系人簿，而无需提供对它的访问或要求用户批准对联系人的访问。 在任何时间都的联系信息将提供给应用程序，仅 CloudKit 进程具有权限。

简单概述一下，有三种不同的输入可用于用户发现：

-  **用户记录 ID** – 发现可以进行针对用户的当前已登录用户 ID 在 CloudKit 用户。
-  **用户电子邮件地址**– 用户可以提供电子邮件地址，并且它可以用于发现。
-  **联系簿**– 用户的通讯簿可以用于发现的应用程序具有相同的电子邮件地址，如下所列他们的联系人的用户。


用户发现将返回以下信息：

-  **用户记录 ID** -公共数据库中的用户的唯一 ID。
-  **第一个和最后一个名称**-如存储在公共数据库。


此信息将仅返回为具有选择到发现的用户。

下面的代码将发现有关当前登录到 iCloud 在设备上的用户信息：

```csharp
public CKDiscoveredUserInfo UserInfo { get; set; }
...

// Get the user's metadata
CKContainer.DefaultContainer.DiscoverUserInfo(UserID, (info, e) => {
    // Was there an error?
    if (e != null) {
        Console.WriteLine("Error: {0}", e.LocalizedDescription);
    } else {
        // Save the user info
        UserInfo = info;
    }
});
```

使用下面的代码来查询联系人簿中的所有用户：

```csharp
// Ask CloudKit for all of the user's friends information
CKContainer.DefaultContainer.DiscoverAllContactUserInfos((info, er) => {
    // Was there an error
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Process all returned records
        for(int i = 0; i < info.Count(); ++i) {
            // Grab a user
            var userInfo = info[i];
        }
    }
});
```

在本部分中，我们已介绍 CloudKit 可以提供对应用程序的用户的帐户的访问权限的四个主要区域。 从获取用户的标识和元数据，对隐私策略，和中内置了 CloudKit 最后，发现应用程序的其他用户的能力。

## <a name="the-development-and-production-environments"></a>开发和生产环境

CloudKit 对于应用程序的记录类型和数据提供单独的开发和生产环境。 开发环境是一个更灵活的环境，仅供开发团队的成员。 当应用程序将新字段添加到记录，并将该记录保存在开发环境中时，服务器将自动更新的架构信息。

开发人员可以使用此功能在开发期间，从而节省了时间对架构进行更改。 值得注意的一点是，字段添加到记录后，以编程方式不能更改与该字段相关联的数据类型。 若要更改字段的类型，开发人员必须删除中的字段[CloudKit 仪表板](https://icloud.developer.apple.com/dashboard/)并再次将其添加新类型。

在部署应用程序之前, 开发人员可以将迁移他们的架构和数据到生产环境使用**CloudKit 仪表板**。 当运行在生产环境时，服务器将防止应用程序以编程方式更改架构。 开发人员仍可以进行的更改与**CloudKit 仪表板**，但尝试将字段添加到生产环境可能会导致错误中的记录。

> [!NOTE]
> IOS 模拟器仅适用于**开发环境**。 当开发人员已准备好测试中的应用程序**生产环境**，是必需的物理 iOS 设备。


## <a name="shipping-a-cloudkit-enabled-app"></a>传送 CloudKit 启用应用程序

在之前传送使用 CloudKit 的应用程序，它将需要配置到目标**生产 CloudKit 环境**或将由 Apple 拒绝应用程序。

请执行以下操作：

1. 在 Visual Studio for Ma，编译的应用程序**版本** > **iOS 设备**: 

    [![](intro-to-cloudkit-images/shipping01.png "编译的应用程序版本")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. 从**生成**菜单上，选择**存档**: 

    [![](intro-to-cloudkit-images/shipping02.png "选择存档")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. **存档**将创建并显示在 Visual Studio for Mac: 

    [![](intro-to-cloudkit-images/shipping03.png "存档将创建和显示")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. 启动 **Xcode**。
5. 从**窗口**菜单上，选择**组织程序**: 

    [![](intro-to-cloudkit-images/shipping04.png "选择组织程序")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. 选择应用程序的存档，然后单击**导出...**按钮： 

    [![](intro-to-cloudkit-images/shipping05.png "应用程序的存档")](intro-to-cloudkit-images/shipping05.png#lightbox)
    
7. 选择用于导出的方法，然后单击**下一步**按钮： 

    [![](intro-to-cloudkit-images/shipping06.png "选择用于导出的方法")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. 选择**开发团队**从该下拉列表并单击**选择**按钮： 

    [![](intro-to-cloudkit-images/shipping07.png "从下拉列表中选择开发团队")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. 选择**生产**从该下拉列表并单击**下一步**按钮： 

    [![](intro-to-cloudkit-images/shipping08.png "从下拉列表中选择生产")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. 查看设置，然后单击**导出**按钮： 

    [![](intro-to-cloudkit-images/shipping09.png "查看设置")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. 选择用于生成生成的应用程序的位置`.ipa`文件。

此过程是用于提交直接到 iTunes Connect 应用程序类似，只需单击**提交...**而不是在管理器窗口中选择存档后...导出的按钮。

## <a name="when-to-use-cloudkit"></a>何时使用 CloudKit

如我们所看到这篇文章中，CloudKit 提供用于轻松对应用程序存储和从 iCloud 服务器中检索信息。 就是说，CloudKit 不会过时或否决的任何现有工具或框架。

### <a name="use-cases"></a>用例

下面的用例应帮助开发人员决定何时使用特定 iCloud 框架或技术：

-  **iCloud 键-值存储**– 以异步方式保留的数据量最新，并且非常适合使用应用程序首选项。 但是，它会被限定为极少量的信息。
-  **iCloud 驱动器**– 基于现有 iCloud 文档 Api 并提供一个简单的 API，以同步从文件系统的非结构化的数据。 它提供在 Mac OS X 的完整脱机缓存，并非常适合文档中心的应用程序。
-  **iCloud 核心数据**-允许所有用户的设备之间都复制数据。 数据为单用户，而且非常适合保持私有的结构化数据同步。
-  **CloudKit** – 提供这两个结构的公共数据和大容量，并能够处理大型数据集和大型非结构化的文件。 其绑定到用户的 iCloud 帐户并提供客户端定向数据传输。


保留这些用例就记住、 开发人员应选取正确的 iCloud 技术提供当前的所需应用程序功能，并为未来的增长提供良好的可伸缩性。

## <a name="summary"></a>总结

本文已覆盖 CloudKit API 的快速介绍。 它说明了如何设置和配置用于 CloudKit Xamarin iOS 应用程序。 它已覆盖 CloudKit 方便 API 的功能。 它具有显示如何设计 CloudKit 启用应用程序使用查询和订阅的可伸缩性。 并且，它具有最后显示由 CloudKit 公开的应用程序的用户帐户信息。

## <a name="related-links"></a>相关链接

- [CloudKitAtlas （示例）](https://developer.xamarin.com/samples/monotouch/ios8/CloudKitAtlas/)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)
- [创建预配配置文件](~/ios/get-started/installation/device-provisioning/index.md)
