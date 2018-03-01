---
title: "MacOS Sierra 简介"
description: "本文介绍的所有新增和更改 Api 和 macOS Sierra 中可用的功能为 Xamarin.Mac 开发人员。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: d81776dcb48d33927ffde4087ceb3f408b38d16f
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="introduction-to-macos-sierra"></a>MacOS Sierra 简介

与新 macOS Sierra，开发人员可以充分利用新的 Api，它允许最终用户在其应用和网站与交互以前不可用的方式。 例如，Apple 现在允许网站能够为客户提供的选项付费安全地通过 Apple Pay 和增强功能的裸机 framework 提升应用的图形和执行计算的潜在。 

有关在 macOS Sierra 上的详细信息，请参阅 Apple 的[macOS + 应用](https://developer.apple.com/macos/)文档。

<a name="Whats-New-in-macOS-Sierra" />

## <a name="whats-new-in-macos-sierra"></a>什么是新建 macOS Sierra

Apple 具有在 macOS Sierra 以及对现有功能，包括许多增强功能中添加几个新的 Api 和服务：

<a name="Apple-File-System" />

### <a name="apple-file-system"></a>Apple 文件系统

MacOS Sierra，与 Apple 已作为现代文件系统为 iOS、 macOS、 tvOS 和 watchOS 发布新的 Apple 文件系统。 Apple 文件系统闪存设备或 SSD 存储进行了优化，并提供以下功能： 强加密，写入时复制元数据，空间共享、 文件和目录、 快照、 快速目录大小调整和原子安全保存基元的克隆。

有关详细信息，请参阅 Apple 的[Apple 文件系统指南](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999)。

<a name="Apple-Pay-Enhancements" />

### <a name="apple-pay-enhancements"></a>Apple 支付增强功能

Apple 具有对 Apple Pay 进行多项增强功能，允许用户从网站的安全付款的 macOS Sierra。

使用 macOS Sierra，采用了 macOS Sierra、 iOS 和 watchOS 以支持动态付款网络和一个新的沙盒测试环境已添加了几个新的 Api。

macOS Sierra 包括允许开发人员将合并直接到 iOS 和 macOS 基于 Safari 的网站的 Apple Pay 新 ApplePay Javascript 框架。 对于支持 Apple Pay 的网站，用户可以授权使用其 iPhone 或 Apple Watch 的付款。

有关详细信息，请参阅 Apple 的[ApplePay JS Framework](https://developer.apple.com/reference/applepayjs)引用。

<a name="Building-Modern-macOS-Apps" />

### <a name="building-modern-macos-apps"></a>生成现代 macOS 应用

如 Apple 的 Safari web 浏览器、 页字处理器和许多新的技术来显示统一、 上下文相关的用户界面执行传统的 UI 元素，如浮动面板消失和多个打开的数字 spread 表使用现代 macOS 应用windows。

[ ![下面举例说明选项卡式 Mac 窗口](images/content08.png)](images/content08.png)

我们[生成现代 macOS 应用](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md)指南介绍多个提示、 功能和技术开发人员可以使用生成在 Xamarin.Mac 现代 macOS 应用程序。

<a name="CloudKit-Data-Sharing" />

### <a name="cloudkit-data-sharing"></a>CloudKit 数据共享

在 macOS Sierra 可允许用户快速轻松地共享记录或从其私有 iCloud 数据库记录集已展开 CloudKit framework。

CloudKit 提供用于发送和接受共享记录邀请完成 UI，而且用户必须完成的读/写控制有权访问记录的人员。

<!--To find out more, please see our [CloudKit Data Sharing](~/mac/platform-features/introduction-to-macos-sierra/cloudkit-data-sharing/) guide.-->

有关详细信息，请参阅 Apple 的[CloudKit Framework 参考](https://developer.apple.com/reference/clockkit)和[CloudKit JS Framework 参考](https://developer.apple.com/reference/cloudkitjs)。

<a name="Safari-App-Extensions-Support" />

### <a name="safari-app-extensions-support"></a>Safari 应用程序扩展支持

Safari 应用程序扩展允许应用程序以将扩展的 Safari web 浏览器的行为，同时与 macOS Sierra 紧密集成。 由于 macOS Safari 应用扩展的作用类似于 iOS 的 Safari 应用扩展，因此可以轻松到端口从到另一个系统。

有关详细信息，请参阅 Apple 的[Safari 应用程序扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319)。

<a name="Security-and-Privacy-Enhancements" />

### <a name="security-and-privacy-enhancements"></a>安全和隐私增强功能

Apple 已对安全和隐私 macOS Sierra，有助于提高应用程序的安全性，并确保最终用户的隐私，其中包括应用程序中进行了几项增强功能：

- 新`NSAllowsArbitraryLoadsInWebContent`密钥可以添加到应用程序的`Info.plist`文件并将允许 web 页时为应用程序的其余部分仍启用 Apple 传输安全 (ATS) 保护正确加载。
- 常见数据安全体系结构 (CDSA) API 已弃用，因此应替换为要生成非对称密钥的 SecKey API。
- 对于所有 SSL/TLS 连接，RC4 对称密码现在默认处于禁用状态。 此外，安全传输 API 不再支持 SSLv3 和建议应用程序停止使用 sha-1 和 3DES 加密越早越好。
- 由于新剪贴板中 iOS 10 和 macOS Sierra 允许用户设备之间复制和粘贴，该 API 已扩展为允许剪贴板限制为特定设备和加盖时间戳给定时刻自动清除。 此外，命名的选不再保留，而应替换共享粘贴板容器。
- 如果应用程序访问受保护的数据 （如用户的日历），它_必须_正确目的字符串值中具有键声明该意图其`Info.plist`文件 (`NSCalendarUsageDescription`在日历的情况下)。
- 未传递通过 Mac 应用商店的开发人员已签名应用现在可以充分利用 CloudKit、 iCloud 密钥链、 iCloud 驱动器、 远程推送通知、 MapKit 和 VPN 的权利。
- macOS Sierra 不再支持交付外部代码或数据以及在其 zip 存档或无符号的磁盘映像中的代码签名者应用，如之前运行时的运行时路径未知。

此外，在 macOS Sierra 上运行的应用程序 （或更高版本） 必须以静态方式声明其想要通过输入中一个或多个隐私特定键访问特定功能或用户信息其`Info.plist`向用户应用程序希望获得的原因说明的文件访问权限。

由于 macOS Sierra 与 iOS 10 共享这些更改，请参阅我们的 iOS 10[安全和隐私增强功能](~/ios/app-fundamentals/security-privacy.md)指南以获取详细信息。

<a name="Smart-Card-Driver-Extension-Support" />

### <a name="smart-card-driver-extension-support"></a>智能卡驱动程序扩展支持

使用 macOS Sierra，应用程序可以创建`NSExtension`基于内容的只读访问允许来自某些类型的智能卡的智能卡驱动程序。 （替换不推荐使用的常见数据安全体系结构方法） 的系统密钥链内，然后显示此信息。

有关详细信息，请参阅 Apple [CryptoTokenKit Framework 参考](https://developer.apple.com/reference/cryptotokenkit)。

<a name="Unified-Logging" />

### <a name="unified-logging"></a>统一日志记录

统一日志记录提供有效的消息传递系统的所有级别跨应用程序与单个 API。 使用统一日志记录应用程序出现细粒度的控制包括隐私控件和为方便调试跟踪的活动的多个级别的日志记录。 

活动跟踪和日志记录一起使用时，日志记录提供自动消息关联。

macOS Sierra 包括新的控制台应用 （在应用程序/实用程序） 能够显示来自多个源包括连接的设备的日志数据。 它还支持标记并保存的搜索，并显示跨多个进程的相关消息之间的连接。

此外，日志消息可以查看和维护使用命令行工具。

有关详细信息，请参阅 Apple 的[日志记录引用](https://developer.apple.com/reference/os/1891852-logging)。

<a name="Wide-Color" />

### <a name="wide-color"></a>广泛的颜色

macOS Sierra 扩展对扩展范围像素格式和整个系统包括框架，例如核心图形、 Core 映像、 金属和 AVFoundation 宽色域颜色空间的支持。 通过提供在整个图形堆栈整个此行为可以进一步简化对具有广泛的颜色显示设备的支持。

此外，`AppKit`已修改的新工作扩展**sRGB**色彩空间，从而更便于混合中而不会显著的性能降低的广泛的颜色色域的颜色。

在处理宽颜色时，Apple 将提供下列最佳方案：

- `NSColor` 现在使用 sRGB 颜色空间，将不再 clamp 值`0.0`到`1.0`范围。 如果应用程序依赖于以前的夹具行为，它将需要为 macOS Sierra 进行修改。
- 当使用低级别的 API，如核心图形或裸机提供图像处理，则应用程序应使用支持 16 位浮点值扩展的范围颜色空间和像素格式。 在必要时，应用程序将需要手动 clamp 颜色组件值。
- 核心图形、 Core 映像和金属性能着色器都提供两个颜色空间之间进行转换的新方法。

若要了解详细信息，请参阅我们[简介广泛的颜色](~/ios/platform/wide-color.md)指南。

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>附加的框架的更改

除了的主要 framework 更改和上面列出的添加件，Apple 已作出 macOS Sierra 中的许多其他的次要 framework 更改。

若要了解详细信息，请参阅我们[其他 Framework 更改](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md)指南。

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>弃用的 API

在 macOS Sierra 不建议使用以下 Api:

- 不再支持 HFS 标准文件系统。

请参阅 Apple 的[OS X v10.12 API 差异](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/APIDiffsMacOS10_12/index.html)的弃用功能和更改的文档的完整列表。

## <a name="related-links"></a>相关链接

- [Mac 示例](https://developer.xamarin.com/samples/mac/)
- [OS X 10.12 的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
