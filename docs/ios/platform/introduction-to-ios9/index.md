---
title: "IOS 9 简介"
description: "本文介绍的所有新增和更改 Api 和 iOS 9 中可用的功能为 Xamarin.iOS 开发人员。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 5b26989603695cfb309fba5a5318f7ef4d2460e2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-ios-9"></a>IOS 9 简介

_本文介绍的所有新增和更改 Api 和 iOS 9 中可用的功能为 Xamarin.iOS 开发人员。_

![](images/ios9-sml.png "IOS 9 徽标")

Apple 已在 iOS 9 以及对现有功能的许多增强功能中添加几个新的 Api 和服务。

## <a name="3d-touch"></a>3D Touch

新 iOS 9 iPhone 6s 和 iPhone 6s Plus，3D Touch 压力敏感手势添加到你的 iOS 应用。 使用 3D 触摸，iPhone 应用现已可以不仅知道用户影响设备的屏幕上，它可以感测 exerting 用户多少压力和响应不同压力级别。

3D Touch 提供了向应用程序的以下功能：

- **压力敏感度**-现在可以硬测量应用程序或用户轻接触屏幕并采取利用该信息。 例如，绘制应用可以进行行之更宽或更薄根据用户硬触摸屏幕。
- **查看和 Pop** -您的应用程序现在可以让用户使用其数据进行交互而无需导航超出其当前上下文。 通过按硬在屏幕上，他们可以*查看*在他们感兴趣 （如预览一条消息） 的项。 通过按更难，他们可以*弹出*到项。
- **快速操作**-认为的快速之类的操作的上下文菜单，可以将弹出型时用户右键单击桌面应用程序中的项。 使用快速操作，您可以添加常见、 快速而简单到函数的访问快捷方式应用程序中从在 iOS 设备上的主页屏幕图标。

若要了解详细信息，请参阅我们[简介 3D Touch](~/ios/platform/3d-touch.md)指南。

## <a name="app-transport-security"></a>应用程序传输安全

新应用程序传输安全 (ATS) 向 iOS 9，强制执行 internet 资源 （如应用程序的后端服务器） 和你的应用程序之间的安全连接。 ATS 可确保所有 internet 通信都符合以确保连接安全的最佳实践，从而防止意外泄露的敏感信息直接通过你的应用程序或正在使用的库。

由于默认情况下，在生成适用于 iOS 9 和 OS X 10.11 (El Capitan) 使用的所有连接的应用程序中启用了 ATS [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)， [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/)或[NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/)依据ATS 安全要求。 如果你的连接不满足这些要求，则会失败并出现异常。

若要了解有关 ATS 的详细信息，请参阅我们[应用传输安全](~/ios/app-fundamentals/ats.md)指南。

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>适用于 iPad 的多任务

IOS 9，与 Apple 添加了对在特定 iPad 硬件上同时运行两个应用程序的多任务支持。 因此，Xamarin.iOS 应用程序可以不再假定它们是在任何给定时间运行的唯一应用程序或他们有权全屏或设备的资源。

适用于 iPad 的多任务支持通过以下功能：

- **幻灯片通过**-允许用户暂时滑出面板 （不管是基于语言方向屏幕的右或向左一端） 涵盖大约 25%的当前运行的主应用程序中运行的第二个 iOS 应用程序。 滑动通过仅上 iPad Pro、 iPad 无线、 iPad 无线 2、 iPad 迷你 2、 iPad 迷你 3 或 iPad 迷你 4 提供了。
- **拆分视图**-支持的 iPad 的硬件上 (iPad 无线 2，iPad 迷你 4 和 iPad Pro 仅)，用户可以选取第二个应用程序并使用当前正在运行的应用在拆分屏幕模式下运行它的并排显示。 用户可以控制主屏幕每个应用所占的百分比。
- **在图中的图片**-用于播放视频的内容，现在视频可以播放该浮动通过其他应用在 iOS 设备上当前运行的可移动和可调整大小窗口中的应用程序。 用户可以完全控制的大小和位置的此窗口。 在图中的图片都仅提供 iPad Pro、 iPad 无线、 iPad 无线 2、 iPad 迷你 2、 iPad 迷你 3 或 iPad 迷你 4。

若要了解有关 iOS 9 的新多任务功能的详细信息，请参阅我们[适用于 iPad 的多任务](~/ios/platform/multitasking.md)指南。

## <a name="new-contacts-and-contacts-ui-frameworks"></a>新的联系人和联系人 UI 框架

IOS 9 的引入，与 Apple 已发布两个新的框架，[联系人](https://developer.xamarin.com/api/namespace/Contacts/)和[ContactsUI](https://developer.xamarin.com/api/namespace/ContactsUI/)、 替换现有的通讯簿和地址簿 UI 框架由 iOS 8 及更早。

这些新的、 面向对象的框架提供以下功能：

- **联系人**– 提供 Xamarin.iOS 访问用户的联系人信息。 因为大多数应用程序只需要只读访问权限，此框架经过优化的线程安全的只读访问。
- **ContactsUI** – 提供 Xamarin.iOS UI 元素，若要显示，编辑、 选择和创建在 iOS 设备上的联系人。

有关详细信息，请参阅我们[联系人和联系人 UI](~/ios/platform/contacts.md)文档。


## <a name="new-search-apis"></a>新的搜索 Api

在 iOS 9，以提供了全新的方法来访问你的 Xamarin.iOS 应用程序内的信息中，搜索已展开。 使用新的搜索 Api，你可以使你的应用的内容可供搜索通过 Spotlight 和 Safari 搜索结果，Handoff 和 Siri 提醒和建议。 这允许用户对活动和深层应用中的信息的快速访问。

此外，新的搜索 Api 更加轻松地集成应用程序而无需在前一搜索实现体验中的搜索。 因此，Apple 声明它通常用几个小时，使 iOS 9 应用的内容普遍可供搜索，使用应用程序的搜索。

有关详细信息，请参阅我们[搜索增强功能](~/ios/platform/search/index.md)文档。

## <a name="new-stack-view"></a>新堆栈视图

堆栈视图控件 ([UIStackView](https://developer.xamarin.com/api/type/UIKit.UIStackView/)) 利用动态响应 iOS 设备的方向和屏幕大小自动布局和大小类 （水平或垂直） 管理的子视图堆栈电源。

通过使用堆栈视图控件，大大减少用户界面的布局所需的工作量。 附加到堆栈视图的所有子视图的布局是自动基于开发人员定义属性，例如轴、 分发、 对齐和间距管理的。

有关详细信息，请参阅我们[简介堆栈视图](~/ios/user-interface/controls/uistackview.md)文档。


## <a name="collection-view-changes"></a>集合视图更改

在 iOS 9，集合视图中 ([UICollectionView](https://developer.xamarin.com/api/type/UIKit.UICollectionView/)) 现在支持将通过添加新的默认笔势识别器和几个新的支持方法在初始状态下的项重新排序。

使用这些新方法，你可以轻松地在你的集合视图中实现拖到重新排序和具有自定义项重新排序过程的任何阶段期间外观的选项。

若要了解有关 iOS 9 的集合视图更改的详细信息，请参阅我们[集合视图更改](~/ios/user-interface/controls/uicollectionview.md)指南。

## <a name="game-enhancements"></a>游戏的增强功能

IOS 9，与 Apple 已进行了一些技术改进对更加轻松地在你的 Xamarin.iOS 应用程序中实现游戏图形和音频游戏 Api。 其中包括轻松地通过高级框架和借助的 iOS 设备的 GPU 提高的速度和低级别的增强功能的图形功能强大的开发。

这包括 GameplayKit、 ReplayKit、 模型 I/O、 MetalKit 和金属性能着色器以及金属、 SceneKit 和 SpriteKit 的新的增强功能。

有关详细信息，请参阅我们[游戏增强功能](~/ios/platform/gaming/index.md)文档。

## <a name="homekit-framework-changes"></a>HomeKit 框架将更改

[HomeKit](https://developer.xamarin.com/api/namespace/HomeKit/)在 iOS 8 中引入的 framework 提供的功能来设置和控制 （如自动的灯、 门锁和车库门器供电） 从 Xamarin.iOS 应用程序的各种 HomeKit 启用附件。 除了易于安装和配置外，可以通过使用 Siri 命令控制 HomeKit 附件。

在 iOS 9，Apple 有更加轻松地在安装程序，展开附件支持和提供 （如控制远程通过 iCloud 附件） 的多个附件交互的类型。

有关详细信息，请参阅我们[简介 HomeKit](~/ios/platform/homekit.md)， [HomeKitIntro iOS 示例应用程序](https://developer.xamarin.com/samples/monotouch/HomeKit/HomeKitIntro/)和 Apple 的[HomeKit](https://developer.apple.com/homekit/)文档。

## <a name="handoff-framework-changes"></a>Handoff Framework 更改

Handoff （也称为连续性） 的引入了通过 Apple 在 iOS 8 和 OS X Yosemite (10.10) 作为要在其中一个自己的设备 （iOS 或 Mac） 上开始活动并在他们的设备 （如由用户的 iClou 标识的另一台继续该同一活动中的用户的方法d 帐户)。

Handoff 已扩展在 iOS 9，以还支持新的、 增强的搜索功能。 有关详细信息，请参阅我们[搜索增强功能](~/ios/platform/search/index.md)文档。 有关使用切换的详细信息，请参阅我们[简介 Handoff](~/ios/platform/handoff.md)文档。

## <a name="new-extension-points"></a>新扩展点

Apple 在 iOS 8 中，引入了扩展-库提供的操作系统在标准上下文中，如在通知中心中，当用户请求键盘、 或时他们正在编辑的照片。

IOS 9，与 Apple 通过提供几个新扩展扩展支持_扩展点_，定义使用策略和提供的 Api 来处理给定区域内，如下所示：

- **新的音频单元扩展点**– 使用此扩展点以提供其他音频单元承载应用程序 （如 GarageBand) 中使用的音频效果、 音乐检测、 声音生成器、 等。 此扩展点，可销售_音频单位_（音频插件） 上应用商店。
- **新索引维护扩展点**-使用此扩展点来支持重新编制的应用程序数据，而无需应用程序重新发布。
- **新的网络扩展点**（这些需要从 Apple 的特殊权限）：
    - **应用程序代理提供程序扩展**-使用此扩展点来实现自定义透明的客户端网络代理。
    - **筛选数据提供程序 / 筛选器控件提供程序扩展**-使用这些扩展点来实现筛选设备上的动态网络内容。
    - **数据包隧道提供程序扩展**-使用此扩展点来实现自定义 VPN 隧道协议客户端。
- **新的 Safari 扩展点**:
    - **内容阻止扩展**-使用此扩展点来定义了被阻止的用户浏览 web 时将不会显示的内容的列表。
    - **共享链接扩展**-使用此扩展点启用 Safari 的共享链接中的应用程序的内容查看。

有关详细信息，请参阅我们[扩展简介](~/ios/platform/extensions.md)和 Apple 的[应用程序扩展编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)文档。

## <a name="keychain-enhancements"></a>密钥链的增强功能

在 iOS 9，Apple 已增强密钥链以便为新的加密密钥类型安全 Enclave 和更多的项保护选项，如下所示：

- 一个新的 Touch ID 约束使 Keychain 项无效修改指纹数据库时。
- 允许仅使用 Touch ID 或密码创建访问控制列表项的新约束。
- 使您能够调用独立于身份验证的新身份验证上下文`SecItem`调用。
- 访问控制列表 （使用应用程序密码选项） 的平均信息量适用于应用程序提供 keychain 项目加密。
- 支持生成和使用内部安全 enclave 密钥 (通过`kSecAttrTokenIDSecureEnclave`属性)。

有关详细信息，请参阅我们[简介 Touch ID](~/ios/platform/touchid.md)文档。


## <a name="right-to-left-language-support"></a>从右到左语言支持

在 iOS 9，Apple 已提供翻转的用户界面比以往通过为从右向左的语言提供完整支持。 这包括：

- 标准[UIKit](https://developer.xamarin.com/api/namespace/UIKit/)控件将自动翻转右到左基于的 iOS 设备的区域设置和语言设置。
- [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)类提供了可用于定义给定的视图应如何显示时的特性翻转右到左。
- 能够以编程方式使用翻转图像[FlipsForRightToLeftLayoutDirection](https://developer.xamarin.com/api/property/UIKit.UIImage.FlipsForRightToLeftLayoutDirection/)属性[UIImage](https://developer.xamarin.com/api/type/UIKit.UIImage/)类。

有关详细信息，请参阅 Apple 的[支持右到左的语言](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17)文档。



## <a name="additional-framework-changes"></a>附加的框架的更改

我们已介绍上面的主要更改，除了 Apple 已修改和对多个现有框架 ios 9 包括以下改进：

- AV Foundation Framework
- AVKit Framework
- CloudKit Framework
- Foundation Framework
- Handoff Framework
- HealthKit Framework
- HomeKit Framework
- 本地身份验证框架
- MapKit Framework
- PassKit Framework
- Safari Services Framework
- UIKit Framework

有关详细信息，请参阅我们[其他 iOS 9 Framework 更改](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)文档。

## <a name="deprecated-apis-and-functions"></a>弃用的 Api 和函数

以下 Api 和在 iOS 9 中的函数已弃用 Apple:

- **地址簿 （&） 地址簿 UI**的联系人和联系人 UI 框架已替换为这些 Api。 有关详细信息，请参阅我们[联系人和联系人 UI](~/ios/platform/contacts.md)文档。
- **CBCentralManager** -`RetrievePeripherals`和`RetrieveConnectedPeripherals`方法`CBCentralManager`类具有已在 iOS 9 中删除。 调用这些方法将导致应用崩溃时配对附件或应用程序启动。
- **FetchAllChanges** -`FetchAllChanges`的`CKFetchRecordChangesOperation`类已过时，将在 iOS 9 中删除。
- **Media Player** -iOS 9 中已弃用的媒体播放器框架。 改为使用 AVKit 或 AV Foundation Api。

有关特定 API 弃用功能的完整列表，请参阅 Apple 的[iOS 9.0 API 差异](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222)文档。

## <a name="ios-9-sample-apps"></a>iOS 9 示例应用

我们在一定程度上[iOS 9 特定示例](https://developer.xamarin.com/samples/ios/iOS9/)若要开始：

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://developer.xamarin.com/samples/monotouch/ios9/MetalPerformanceShadersHelloWorld/)
- [MusicMotion](https://developer.xamarin.com/samples/monotouch/ios9/MusicMotion/)
- [PhotoProgress](https://developer.xamarin.com/samples/monotouch/ios9/PhotoProgress/)
- [SegueCatalog](https://developer.xamarin.com/samples/monotouch/ios9/SegueCatalog/)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

同时查看这些示例 （助理 Mac OS X 版本即将 ！） 的 iOS 部分：

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [3D Touch 简介](~/ios/platform/3d-touch.md)
- [应用传输安全性](~/ios/app-fundamentals/ats.md)
- [适用于 iPad 的多任务](~/ios/platform/multitasking.md)
- [联系人和联系人 UI](~/ios/platform/contacts.md)
- [新的搜索 Api](~/ios/platform/search/index.md)
- [简介堆栈视图](~/ios/user-interface/controls/uistackview.md)
- [集合视图更改](~/ios/user-interface/controls/uicollectionview.md)
- [游戏增强功能](~/ios/platform/gaming/index.md)
- [HomeKit 简介](~/ios/platform/homekit.md)
- [Handoff 简介](~/ios/platform/handoff.md)
- [其他 iOS 9 框架更改](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [疑难解答](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [什么是在 iOS 9.0 新增](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [更新你的 Xamarin.iOS 应用程序到 iOS9 （视频）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
