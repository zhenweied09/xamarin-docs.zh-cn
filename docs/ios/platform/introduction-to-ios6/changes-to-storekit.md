---
title: 到 StoreKit iOS 6 中的更改
description: iOS 6 到应用商店工具包 API 上引入了两个更改： 显示 iTunes （和应用商店/iBookstore） 功能从您的应用程序和一个新，在应用中的产品购买 Apple 将可下载文件的选项。 本文档说明如何实现使用 Xamarin.iOS 这些功能。
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: ff717d1e4ea7da947d5534f1ce790b58d84fdfd4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787687"
---
# <a name="changes-to-storekit-in-ios-6"></a>到 StoreKit iOS 6 中的更改

_iOS 6 到应用商店工具包 API 上引入了两个更改： 显示 iTunes （和应用商店/iBookstore） 功能从您的应用程序和一个新，在应用中的产品购买 Apple 将可下载文件的选项。本文档说明如何实现使用 Xamarin.iOS 这些功能。_

这些更改中 iOS6 主要到应用商店工具包包括这两个新功能：

-   **在应用程序内容显示与购买**– 用户可以购买和下载应用程序、 音乐、 丛书和其他 iTunes 内容无需离开你的应用程序。 你也可以链接到你自己的应用以提升购买或只是鼓励评审和级别。 
-   **应用内购买托管内容**– Apple 将存储和传送内容与您的应用内购买产品，无需单独的服务器来承载你的文件，自动支持后台下载，允许你编写更少的代码。 


建议，使用现有的 Xamarin.iOS 一起阅读本文档[应用内购买](~/ios/platform/in-app-purchasing/index.md)文档。

## <a name="requirements"></a>要求

本文档中讨论的应用商店工具包功能需要 iOS 6 和 Xcode 4.5，以及 Xamarin.iOS 6.0。


## <a name="in-app-content-display--purchasing"></a>在应用程序内容显示与购买

在 iOS 中新的应用内购买功能允许用户查看产品信息和购买或下载从你的应用程序中的产品。
以前的应用程序都触发 iTunes、 应用商店或 iBookstore 可能会导致用户离开原始应用程序。 此新功能它们完成时，会自动将用户返回到你的应用。

 [![](changes-to-storekit-images/image1.png "在购买之后自动返回到应用程序")](changes-to-storekit-images/image1.png#lightbox)

有大量方案这可能会很有用，包括 （但不是限于）：

-   **鼓励速率您的应用程序的用户**– 你可以打开应用商店页上，以便用户可以速率，并检查你的应用，无需将其保留。 
-   **跨提升应用**-允许用户查看发布，能够立即购买/下载其他应用。 
-   **帮助用户查找和下载内容**– 帮助用户购买你的应用程序查找、 管理或 （如聚合的内容 音乐相关的应用程序无法提供歌曲的播放列表，并允许播放每首歌曲，若要从在应用内购买）。 


一次`SKStoreProductViewController`显示后，就好像它们是在 iTunes、 应用商店或 iBookstore 可以进行交互的产品信息。 这包括：

-  （对于应用程序），查看屏幕快照
-  采样歌曲或者 （对于音乐、 电视节目和电影） 的视频
-  读取 （和写入） 评审、
-  购买和下载，该优化发生完全在视图控制器和应用商店工具包。 你的应用程序不必提供任何附加代码来执行这种方式。 


请注意，某些选项内`SKStoreProductViewController`仍将强制用户离开你的应用程序并打开相关的应用商店应用，例如单击**相关产品**或应用的**支持**链接。

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

要显示在任何应用内的产品的 API 是非常简单： 它只要求你创建和显示`SKStoreProductViewController`。 请按照下列步骤以创建和显示产品：

1.  创建`StoreProductParameters`对象将参数传递给视图控制器，包括`productId`构造函数中。 
1.  实例化`SKProductViewController`。 将其分配给类级字段。 
1.  到视图控制器中分配一个处理程序`Finished`事件，应关闭视图控制器。 此事件时调用，当用户按可实现取消;或否则完成内视图控制器事务。 
1.  调用`LoadProduct`方法并传入`StoreProductParameters`和完成处理程序。 完成处理程序应该检查产品请求已成功，并因此，如果存在`SKProductViewController`以模式方式。 无法检索产品的情况下，应添加相应的错误处理。 

### <a name="example"></a>示例

*ProductView*项目中*StoreKit*本文的示例代码实现`Buy`接受任何产品的方法的 Apple ID，并将显示`SKStoreProductViewController`。 下面的代码显示任何给定的 Apple ID 的产品信息：

```csharp
void Buy (int productId)
{
    var spp = new StoreProductParameters(productId);
    var productViewController = new SKStoreProductViewController ();
    // must set the Finished handler before displaying the view controller
    productViewController.Finished += (sender, err) => {
        // Apple's docs says to use this method to close the view controller
        this.DismissModalViewControllerAnimated (true);
    };
    productViewController.LoadProduct (spp, (ok, err) => { // ASYNC !!!
        if (ok) {
            PresentModalViewController (productViewController, true);
        } else {
            Console.WriteLine (" failed ");
            if (err != null)
                Console.WriteLine (" with error " + err);
        }
    });
}
```

运行 – 完全内发生下载或购买时，则应用程序类似如下所示`SKStoreProductViewController`:

 [![](changes-to-storekit-images/image2.png "应用程序如下所示，运行时")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>支持较早的操作系统

示例应用程序包括演示如何在 iOS 的早期版本中打开应用商店、 iTunes 或 iBookstore 的代码。 使用`OpenUrl`方法打开正确设计**itunes.com** URL。

你可以实现版本检查，以确定要运行如下的代码：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (6,0)) {
    // do iOS6+ stuff, using SKStoreProductViewController as shown above
} else {
    // don't do stuff requiring iOS 6.0, use the old syntax 
    // (which will take the user out of your app)
    var nsurl = new NSUrl("http://itunes.apple.com/us/app/angry-birds/id343200656?mt=8");
    UIApplication.SharedApplication.OpenUrl (nsurl);
}
```

### <a name="errors"></a>错误

如果你使用的 Apple ID 不是有效的因为它可以是令人困惑表示某种类型的网络或身份验证问题，将会发生以下错误。

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>阅读 OBJECTIVE-C 的文档

有关在 Apple 的开发人员门户上的应用商店工具包读取的开发人员将查看协议 – [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) – 讨论相对于此新功能。 委托协议有只有一个方法 – productViewControllerDidFinish – 已作为公开`Finished`上的事件`SKStoreProductViewController`Xamarin.iOS 中。


## <a name="determining-apple-ids"></a>确定 Apple Id

通过所需的 Apple ID`SKStoreProductViewController`是*数*（无法将其与程序包 Id 类似"com.xamarin.mwc2012"混淆）。 有多种不同的方式，你可以找出你要显示，并将下面列出的产品 Apple ID:

### <a name="itunesconnect"></a>iTunesConnect

对于你发布的应用程序，它很容易地找到**Apple ID**在 iTunes Connect 中：

 [![](changes-to-storekit-images/image3.png "在 iTunes Connect 中查找 Apple ID")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />


### <a name="search-api"></a>搜索 API

Apple 提供一个动态搜索 API 来查询应用商店和 iTunes iBookstore 中的所有产品。 有关如何访问搜索 API 的信息可在[Apple 的 Affiliate 资源](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)，但 API 公开给任何人 （不只是已注册的关联公司）。 可以分析生成的 JSON 来发现`trackId`，它是 Apple ID 用于`SKStoreProductViewController`。

结果还包含其他元数据，包括显示信息和可用于呈现的产品中你的应用的图片 Url。

下面是一些可能的恶意活动：

-   **iBooks 应用*- [http://itunes.apple.com/search?term=ibooks&amp; 实体 = 软件&amp;国家/地区 = us](http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us) 
-   **圆点和 Kangaroo iBook*- [http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp; 实体 = 电子书&amp;国家/地区 = us](http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us) 


### <a name="enterprise-partner-feed"></a>企业合作伙伴源

Apple 提供的可下载数据库准备就绪，可以平面文件的形式的已批准的合作伙伴与所有其产品的完整数据转储。 如果你符合访问[企业合作伙伴馈送](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html)然后在该数据集找不到任何产品 Apple ID。

请注意，许多用户的企业合作伙伴源是的成员[Affiliate 程序](http://www.apple.com/itunes/affiliates)允许以获得有关产品销售的佣金。 `SKStoreProductViewController` 不支持 Affiliate Id （在撰写本文之际; 这可能由 Apple 在将来会添加）。

### <a name="direct-product-links"></a>直接产品链接

可以从其 iTunes 预览 URL 链接推断出产品的 Apple ID。
在任何 iTunes （适用于应用程序、 音乐或丛书） 的产品链接找到以开头的 URL 的一部分`id`并使用后面的数字。

例如，到 iBooks 的直接链接是

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

Apple ID 为**364709193**。 同样对于 MWC2012 应用，直接链接是

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

Apple ID 为**496963922**。

## <a name="in-app-purchase-hosted-content"></a>应用内购买托管内容

如果你的应用内购买包含可下载内容 （如丛书或其他媒体、 游戏级别艺术作品和配置或其他大型文件中） 然后这些文件用于在你的 web 服务器上托管且应用程序必须将合并代码以安全地下载其之后购买。 在 iOS 中 6 Apple 引入了一个选项，他们将在其中托管其服务器上的文件无需单独的服务器。 该功能才适用于非耗材产品 （不耗材或订阅）。 使用 Apple 的托管服务的优点包括：

-  保存承载与带宽成本。
-  可能更具伸缩性比你当前使用的任何服务器主机。 
-  若要编写，因为无需构建任何服务器端处理更少代码。 
-  后台下载已为您实现。


注意： 测试托管应用内购买内容在 iOS 模拟器不支持，因此，必须测试与实际的设备。

### <a name="hosted-content-basics"></a>托管内容的基础知识

在 iOS 6、 之前，没有两种方法可以提供产品 (中的更多详细信息中所述[Xamarin 的应用内购买](~/ios/platform/in-app-purchasing/index.md)文档):

-   **内置产品**– 功能，，才能解除锁定购买，但这是内置到应用程序 （不管是为代码或嵌入的资源）。 内置的产品的示例包括解锁的照片筛选器或中的游戏电源 ups。 
-   **服务器传递产品**– 之后购买，应用程序必须下载内容从运行的服务器。 在购买过程中下载此内容、 将其存储在设备上，然后呈现为提供产品的一部分。 示例包括丛书、 杂志问题或游戏包含背景艺术作品和配置文件的级别。 


在 iOS 6 Apple 提供的服务器传递产品变体： 他们将托管其服务器上的内容文件。 这使得生成服务器传递产品，因为你无需运行单独的服务器，并存储工具包提供你以前必须自己编写的后台下载功能要简单得多。 若要利用 Apple 的承载，启用内容承载新的应用内购买产品的并修改你的应用商店工具包代码以充分利用它。 然后再使用 Xcode 生成并上载到 Apple 的服务器查看和版本的产品内容文件。

 [![](changes-to-storekit-images/image4.png "生成和传递过程")](changes-to-storekit-images/image4.png#lightbox)

使用应用商店提供的应用内购买*托管内容*需要以下设置和配置：

-   **iTunes Connect** – 你*必须*提供你的银行和税务信息向 Apple 以便它们可以汇款替你收集中涉及的金额。 然后，你可以配置要销售，并将沙盒用户帐户设置为测试购买产品。  *你还必须配置承载的内容**为你想要存储在 Apple 这些非耗材产品* *。* 
-   **iOS 设置门户**– 创建捆绑标识符并启用你的应用的应用商店访问的支持的应用内购买任何应用程序一样。 
-   **存储工具包**– 将代码添加到显示产品、 购买产品和还原的事务的应用程序。  *在 iOS 6 中存储工具包还将管理的产品内容，在后台，进度更新和下载。* 
-   **自定义代码**– 若要将跟踪客户的购买情况和提供的产品或它们已购买的服务。 利用新的 iOS 6 存储工具包类，如`SKDownload`检索 apple 承载的内容。 


以下各节说明如何实现承载的内容，从创建并将包上载到管理购买和下载过程中，本文使用的示例代码。


### <a name="sample-code"></a>代码示例

示例项目*HostedNonConsumables* （在 StoreKitiOS6.zip) 演示如何生成该使用承载内容的应用。 应用提供两个"章节"推销、 Apple 的服务器上托管的内容。 内容包括文本文件和一个图像，但无法在实际应用中使用复杂得多的内容。

在安装之前，安装期间和之后购买，应用程序如下所示：

 [![](changes-to-storekit-images/image5.png "应用程序如下所示，在安装之前，安装期间和之后购买")](changes-to-storekit-images/image5.png#lightbox)

下载并复制到应用程序的文档目录中的文本文件和图像。 请参阅[使用文件系统文档](~/ios/app-fundamentals/file-system.md)有关可用于应用程序存储的不同目录的详细信息。

## <a name="itunes-connect"></a>iTunes Connect

创建将使用 Apple 的新产品的内容时请务必选择承载**非耗材**产品类型。 其他产品类型不支持内容承载。 此外，你不应启用的内容承载*现有*销售; 仅打开内容承载新的产品的产品。

 [![](changes-to-storekit-images/image6.png "选择非耗材产品类型")](changes-to-storekit-images/image6.png#lightbox)

输入**产品 ID**。 这将需要更高版本时创建此产品的内容。

 [![](changes-to-storekit-images/image7.png "输入产品 ID")](changes-to-storekit-images/image7.png#lightbox)

内容 hosting 详细信息部分中设置。 在应用内购买正常生效之前只需取消选中"第内容与 Apple 主机"复选框如果你想要取消 （即使你已上载一些测试内容）。 但是不能删除后，在应用内购买已进入实时内容承载。

 [![](changes-to-storekit-images/image8.png "承载 apple 的内容")](changes-to-storekit-images/image8.png#lightbox)

一旦你已启用承载内容，将输入产品**等待上载**状态和显示此消息：

 [![](changes-to-storekit-images/image9.png "产品将等待输入上载状态并显示此消息")](changes-to-storekit-images/image9.png#lightbox)

内容现在必须使用 Xcode 创建并上载使用存档工具。 下一节中提供了有关创建内容包的说明**创建。PKG 文件**。

## <a name="creating-pkg-files"></a>创建。PKG 文件

向 Apple 上载的内容文件必须满足以下限制：

-  大小不能超过 2 GB。
-  不能包含可执行代码 （或点的内容之外的符号链接）。 
-  必须采用正确的格式 （包括.plist 文件），并且具有.pkg 文件扩展名。 这将在遵循这些说明使用 Xcode 如果自动完成。 


只要它们满足这些限制，可以添加多个不同的文件和类型的文件。 压缩之前传递给你的应用程序和你的代码访问它之前，通过应用商店工具包解压缩内容。

在上载后的内容包，它可以替换为较新的内容。 必须上载新内容，并将其提交对其进行查看/核准一般过程。 必须递增`ContentVersion`字段中更新后的内容包。

### <a name="xcode-in-app-purchase-content-projects"></a>Xcode 应用内购买内容的项目

当前创建的应用内购买产品的内容包需要 Xcode。 没有需要; 否 OBJECTIVE-C 的编码Xcode 具有新的项目类型这包仅包含你的文件和一个 plist。

我们的示例应用程序已销售的章节 – 每个章节的内容包将包含：

-  文本文件，并
-  表示章图像。


通过选择启动**文件 > 新建项目**从菜单中，并选择**应用内购买内容**:

 [![](changes-to-storekit-images/image10.png "选择应用内购买内容")](changes-to-storekit-images/image10.png#lightbox)

输入**产品名称**和**公司标识符**以便**捆绑标识符**匹配**产品 ID**在 iTunes 中输入此产品的连接。

 [![](changes-to-storekit-images/image11.png "输入的名称和标识符")](changes-to-storekit-images/image11.png#lightbox)

现在将具有一个空**应用内购买内容**项目。 你可以右键单击和**添加文件...** 或拖到**项目导航器**。 确保**ContentVersion**是否正确 （它应开始 1.0，但如果你以后选择将更新你的内容，请记住以将其递增）。

此屏幕截图显示了包含在项目和主窗口中的可见 plist 条目中的内容文件的 Xcode:

 [![](changes-to-storekit-images/image12.png "此屏幕截图显示 Xcode 使用在项目和主窗口中的可见 plist 条目中包含的内容文件")](changes-to-storekit-images/image12.png#lightbox)

已添加你的所有内容文件后可保存此项目和编辑它再次更高版本，或开始上载过程。

## <a name="uploading-pkg-files"></a>正在上载。PKG 文件

若要上载内容的包的最简单方法是使用**Xcode 存档工具**。 选择**产品 > 存档**从开始菜单：

 ![](changes-to-storekit-images/image13.png "选择 Archiven")

如下所示的内容的包将出现在存档。 请注意的存档类型和图标显示这是**应用内购买内容存档**。 单击**验证...** 检查有错误我们内容包在不实际 preforming 上载。

 [![](changes-to-storekit-images/image14.png "验证包")](changes-to-storekit-images/image14.png#lightbox)

与你 iTunes Connect 凭据的登录名：

 [![](changes-to-storekit-images/image15.png "使用你 iTunes Connect 凭据登录")](changes-to-storekit-images/image15.png#lightbox)

选择要将此内容与关联的正确的应用程序和应用内购买：

 [![](changes-to-storekit-images/image16.png "选择要将此内容与关联的正确的应用程序和应用内购买")](changes-to-storekit-images/image16.png#lightbox)

你应看到如下消息：

 ![](changes-to-storekit-images/image17.png "示例没有问题消息")

现在通过类似的过程，但单击**分布...** 将实际上载内容。

 [![](changes-to-storekit-images/image18.png "分发应用程序")](changes-to-storekit-images/image18.png#lightbox)

选择要上载内容的第一个选项：

 ![](changes-to-storekit-images/image19.png "将内容上载")

再次登录：

 [![](changes-to-storekit-images/image15.png "登录名")](changes-to-storekit-images/image15.png#lightbox)

选择要上载到的内容的正确的应用程序和应用内购买记录：

 [![](changes-to-storekit-images/image20.png "选择应用程序和应用内购买记录")](changes-to-storekit-images/image20.png#lightbox)

上载你的文件时等待：

 [![](changes-to-storekit-images/image21.png "将内容加载对话框")](changes-to-storekit-images/image21.png#lightbox)

上载完成后，将显示一条消息，告知内容的已提交到应用商店。

 [![](changes-to-storekit-images/image22.png "示例成功上载消息")](changes-to-storekit-images/image22.png#lightbox)

一旦这样做了，当您返回到产品页面上 iTunes Connect 它将显示包的详细信息，并处于**已准备好提交**状态。 此状态产品时，你可以开始在沙盒环境中测试。 不需要 '提交' 在沙盒中用于测试的产品。

 [![](changes-to-storekit-images/image23.png "iTunes Connect 它将显示包的详细信息，并处于到提交状态已准备")](changes-to-storekit-images/image23.png#lightbox)

它可能需要一些时间 （如。 几分钟） 之间上载存档和 iTunes 正在更新的连接状态。 您可以单独，提交供其评审产品，或者与应用程序二进制文件一起提交它。 仅 Apple 正式已审批内容后将它会在生产应用商店购买你的应用程序中可用。

### <a name="pkg-file-format"></a>PKG 文件格式

使用 Xcode 和存档工具创建并上载托管的内容包意味着，你永远不会看到包本身的内容。 文件和目录中为示例应用程序创建的包如下所示，使用`plist`根和中的产品文件中的文件`Contents`子目录：

 [![](changes-to-storekit-images/image24.png "根和内容子目录中的产品文件中的 plist 文件")](changes-to-storekit-images/image24.png#lightbox)

请注意包的目录结构 (尤其是中的文件的位置`Contents`子目录) 因为你将需要了解此信息来从设备上的包中提取文件。

### <a name="updating-package-content"></a>更新包内容

在已批准之后才能更新内容的步骤：

-  编辑在应用购买内容 Xcode 的项目中。
-  增大版本号。
-  重新将上载到 iTunes Connect。 后续购买者将自动获取最新版本，但尚未安装旧版本的用户将不会收到任何通知。 
-  你的应用程序负责将通知用户并鼓励它们检索内容的较新版本。 应用程序也必须生成下载最新版本的函数。 此操作必须执行使用应用商店工具包的还原功能。 
-  若要确定你是否存在较新版本可以生成一项功能到你应用中以提取 SKProducts （如。 用于检索产品价格的相同进程） 和比较的 ContentVersion 属性。 

## <a name="purchasing-overview"></a>购买概述

在阅读本部分之前, 查看现有[应用内购买文档](~/ios/platform/in-app-purchasing/index.md)。

事件发生时具有承载的内容的产品的顺序购买和下载此图所示：

 [![](changes-to-storekit-images/image25.png "事件发生时具有承载的内容的产品的顺序购买和下载")](changes-to-storekit-images/image25.png#lightbox)

1.  可以在 iTunes Connect 与所承载的内容启用中创建新产品。 实际在 Xcode 中单独构造 （如只需为拖动到的文件夹的文件），然后存档和内容上载到 iTunes （无需进行编码，则需要）。 每个产品然后提交以供审核，此后，它可供购买。 示例代码中这些产品 Id 是硬编码，但承载 apple 的内容是如果你远程服务器上存储的可用产品列表，以便它可以进行更新时提交新产品和内容分发到 iTunes Connect 更灵活。 
1.  当用户购买产品时，事务将置于付款队列中等待处理。 
1.  应用商店工具包将转发到 iTunes 服务器以处理采购请求。 
1.  事务 （如 iTunes 服务器上完成 向客户收取） 并回执返回到应用，包括它是可下载附加的产品信息 (如果有，文件大小和其他元数据)。 
1.  你的代码应检查产品是否 downloadble，并如果这样将还在付款队列放置的内容下载请求。 应用商店工具包将此请求发送到 iTunes 服务器。 
1.  服务器将内容文件返回到应用商店工具包，提供回调返回下载进度和剩余到你的代码的估计时间。 
1.  完成后，获取通知，并且在缓存文件夹中传递的文件位置。 
1.  你的代码应该复制文件，并验证它们，保存你需记住已购买产品的任何状态。 利用此机会来对新文件正确设置备份的标志 (提示： 如果它们来自一个服务器，并且用户永远不会编辑，则应可能跳过这些备份，因为用户可以始终在检索它们从 Apple 的服务器在将来)。 
1.  调用 FinishTransaction。 这是重要事项，因为它从付款队列将删除该事务。 还有一点后已复制出缓存目录的内容不会调用直到 FinishTransaction。 一旦调用 FinishTransaction，很可能快速清除缓存文件。 


## <a name="implementing-hosted-content-purchase"></a>实现托管内容购买

完整的一起应阅读以下信息[应用内购买文档](~/ios/platform/in-app-purchasing/index.md)。 本文档中的信息主要针对承载的内容和以前的实现之间的差异。


### <a name="classes"></a>类

已添加或更改为支持托管内容在 iOS 6 中的以下类：

-   **SKDownload** – 表示正在进行的下载的新类。 该 API 允许多个产品，但是最初只有一个已实现。 
-   **SKProduct** – 添加的新属性： `Downloadable` ， `ContentVersion` ，`ContentLengths`数组。 
-   **SKPaymentTransaction** – 添加的新属性： `Downloads` ，其中包含一套`SKDownload`对象本产品已承载内容可供下载。 
-   **SKPaymentQueue** – 添加的新方法： `StartDownloads` 。 调用此方法与`SKDownload`要提取其托管的内容的对象。 下载可以在后台发生。 
-   **SKPaymentTransactionObserver** – 新方法： `UpdateDownloads` 。 应用商店工具包调用此方法使用当前下载操作的进度信息。 


新的详细信息`SKDownload`类：

-   **进度**– 可用于向用户显示完成百分比的指示器的 0-1 之间的值。 不使用进度 = = 1，若要检测是否下载已完成，请检查其状态 = = 已完成。 
-   **TimeRemaining** – 下载剩余时间，以秒为单位的估计值。 为-1 意味着它仍在计算估计值。 
-   **状态**– 处于活动状态，等待、 完成、 失败、 暂停和已取消。 
-   **ContentURL** – 文件内容已放置的位置在磁盘上，在`Cache`目录。 下载完毕后，才填充。 
-   **错误**– 检查此属性，如果的状态失败。 


中的示例代码的类之间的交互 （特定于托管内容购买的代码显示为绿色） 此图所示：

 [![](changes-to-storekit-images/image26.png "托管内容购买显示为在此关系图的绿色")](changes-to-storekit-images/image26.png#lightbox)

本部分的其余部分中显示了具有已使用这些类的示例代码：

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

更改现有`UpdatedTransactions`重写以检查可下载的内容，并调用`StartDownloads`如果需要：

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
    foreach (SKPaymentTransaction transaction in transactions) {
        switch (transaction.TransactionState) {
        case SKPaymentTransactionState.Purchased:
            // UPDATED FOR iOS 6
            if (transaction.Downloads != null && transaction.Downloads.Length > 0) {
                // Purchase complete, and it has downloads... so download them!
                SKPaymentQueue.DefaultQueue.StartDownloads (transaction.Downloads);
                // CompleteTransaction() call has moved after downloads complete
            } else {
                // complete the transaction now
                theManager.CompleteTransaction(transaction);
            }
            break;
        case SKPaymentTransactionState.Failed:
            theManager.FailedTransaction(transaction);
            break;
        case SKPaymentTransactionState.Restored:
            // TODO: you must decide how to handle restored transactions.
            // Triggering all the downloads at once is not advisable.
            theManager.RestoreTransaction(transaction);
            break;
        default:
            break;
        }
    }
}
```

新的重写的方法`UpdatedDownloads`如下所示。 应用商店工具包调用此方法之后`StartDownloads`中触发`UpdatedTransactions`。 调用此方法*多次*按不确定的时间间隔，以提供您提供下载进度，然后再次下载已完成时。 请注意该方法接受一个数组`SKDownload`对象，以便每个方法调用可以为你提供的队列中的多个下载的状态。 中所示的下载状态为下面的实现将检查每个时间和采取相应的措施。

```csharp
// ENTIRELY NEW METHOD IN iOS6
public override void PaymentQueueUpdatedDownloads (SKPaymentQueue queue, SKDownload[] downloads)
{
    Console.WriteLine (" -- PaymentQueueUpdatedDownloads");
    foreach (SKDownload download in downloads) {
        switch (download.DownloadState) {
        case SKDownloadState.Active:
            // TODO: implement a notification to the UI (progress bar or something?)
            Console.WriteLine ("Download progress:" + download.Progress);
            Console.WriteLine ("Time remaining:   " + download.TimeRemaining); // -1 means 'still calculating'
            break;
        case SKDownloadState.Finished:
            Console.WriteLine ("Finished!!!!");
            Console.WriteLine ("Content URL:" + download.ContentUrl);

            // UNPACK HERE! Calls FinishTransaction when it's done
            theManager.SaveDownload (download);

            break;
        case SKDownloadState.Failed:
            Console.WriteLine ("Failed"); // TODO: UI?
            break;
        case SKDownloadState.Cancelled:
            Console.WriteLine ("Cancelled"); // TODO: UI?
            break;
        case SKDownloadState.Paused:
        case SKDownloadState.Waiting:
            break;
        default:
            break;
        }
    }
}
```

### <a name="inapppurchasemanager-skproductsrequestdelegate"></a>InAppPurchaseManager (SKProductsRequestDelegate)

此类包含一个新方法`SaveDownload`，每个下载已成功完成后调用。

所承载的内容已成功下载并解压缩到`Cache`目录。 结构。PKG 文件所需的所有文件中保存`Contents`子目录，因此下面的代码中提取文件`Contents`子目录。

该代码循环通过内容包中的所有文件，并将它们到复制`Documents`目录中，为命名的子文件夹中`ProductIdentifier`。 最后，它调用`CompleteTransaction`，哪些调用`FinishTransaction`从付款队列中删除该事务。

```csharp
// ENTIRELY NEW METHOD IN iOS 6
public void SaveDownload (SKDownload download)
{
    var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
    var targetfolder = System.IO.Path.Combine (documentsPath, download.Transaction.Payment.ProductIdentifier);
    // targetfolder will be "/Documents/com.xamarin.storekitdoc.montouchimages/" or something like that
    if (!System.IO.Directory.Exists (targetfolder))
        System.IO.Directory.CreateDirectory (targetfolder);
    foreach (var file in System.IO.Directory.EnumerateFiles 
             (System.IO.Path.Combine(download.ContentUrl.Path, "Contents"))) { // Contents directory is the default in .PKG files
        var fileName = file.Substring (file.LastIndexOf ("/") + 1);
        var newFilePath = System.IO.Path.Combine(targetfolder, fileName);
        if (!System.IO.File.Exists(newFilePath)) // HACK: this won't support new versions...
            System.IO.File.Copy (file, newFilePath);
        else
            Console.WriteLine ("already exists " + newFilePath);
    }
    CompleteTransaction (download.Transaction); // so it gets 'finished'
}
```

当`FinishTransaction`称为下载不再保证文件都是在`Cache`目录。 应将所有文件都复制，然后再调`FinishTransaction`。


## <a name="other-considerations"></a>其他注意事项

上面的代码示例演示托管内容购买一个相当简单的实现。 有一些必须考虑的其他事项：

### <a name="detecting-updated-content"></a>检测更新的内容

虽然可能更新你托管的内容包，但存储工具包不提供任何机制，这些更新推送至已经下载和购买产品的用户。 要实现此功能，你的代码可能会检查新`SKProduct.ContentVersion`属性 (如果`SKProduct`是`Downloadable`) 定期，并检测该值递增。 或者，你可以生成的推送通知系统。

### <a name="installing-updated-content-versions"></a>安装更新的内容版本

上面的示例代码如果跳过文件复制该文件已存在。 如果你想要支持较新版本的下载，这不是内容的一个不错的主意。

另一个方法可能是将内容复制到的版本中，名为的文件夹，并跟踪它是当前版本 （例如。 在`NSUserDefaults`或存储已完成的购买记录的任何位置)。

### <a name="restoring-transactions"></a>还原事务

当`SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions`是调用，存储工具包返回用户的所有以前的事务。 如果它们已购买大量的项，或每个购买了非常大的内容包，然后还原可能导致大量的网络流量如所有内容获取排队下载一次。

是否产品已从关联的内容包的实际下载单独购买，请考虑跟踪。

### <a name="pausing-restarting-and-canceling-downloads"></a>暂停、 重新启动和取消下载

尽管示例代码不演示此功能，就可以暂停和重新启动托管的内容下载。 `SKPaymentQueue.DefaultQueue`具有方法`PauseDownloads`，`ResumeDownloads`和`CancelDownloads`。

如果代码调用`FinishTransaction`下载之前付款队列`Finished`然后自动取消该下载。

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>在下载内容上设置跳过备份标志

Apple 的 iCloud 备份准则建议从服务器轻松还原的非用户内容应*不*要备份的 （因为它将不必要地使用 iCloud 存储）。 请参阅[使用文件系统](~/ios/app-fundamentals/file-system.md)文档上设置备份属性的更多详细信息。

## <a name="summary"></a>总结

这篇文章引入了两个存储工具包 iOS6 中的新功能： 购买 iTunes 和从其他内容中你的应用，并利用 Apple 的服务器来托管你自己的应用内购买。 应结合现有阅读本简介[应用内购买文档](~/ios/platform/in-app-purchasing/index.md)完成覆盖率实现存储工具包功能。

## <a name="related-links"></a>相关链接

- [StoreKit （示例）](https://developer.xamarin.com/samples/StoreKit/)
- [应用内购买](~/ios/platform/in-app-purchasing/index.md)
- [StoreKit Framework 参考](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [SKStoreProductViewController 类引用](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [iTunes 搜索 API 参考](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [WWDC 视频： 畅销的产品使用应用商店工具包](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
