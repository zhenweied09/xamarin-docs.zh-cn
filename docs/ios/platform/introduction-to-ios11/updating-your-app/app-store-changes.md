---
title: 应用商店更改
description: 浏览 iOS 11 的新功能
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 9c9355c047d2e7083ca787f473515163d8e6f5f1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="app-store-changes"></a>应用商店更改

_浏览 iOS 11 的新功能_

IOS 应用商店已完成的重新设计，这不仅使用户能够高效地导航应用商店中，而且还允许你，作为开发人员，以提升用户你的应用。 这些提升包括应用内购买的更新和更新产品页。 iOS 11 还将添加有关如何与用户进行通信、 如何添加你的应用程序图标，以及如何发布你的应用为公共的更新。

![新的应用商店布局](app-store-changes-images/image3.jpg)

经过重新设计的应用商店包含以下部分：

- **今天**– 此选项卡包含"编辑器的 pick"或重要应用的应用程序。 若要将提升应用程序中填写信息上[提升](https://developer.apple.com//contact/app-store/promote/)页。
- **游戏**– 设置为任何应用**游戏**类别在 iTunes Connect 中的可以找到此选项卡下。
- **应用**– 此选项卡功能所有其他应用。 用户可以浏览的特色应用程序、 类别、 顶部付费/释放。
- **更新**– 此应用程序向你的应用显示的更新。 即使在发布的应用通过[分阶段发布](#Phased_Release)，用户仍可转到此选项卡，以及下载最新版本。
- **搜索**– 此选项卡允许用户能够搜索您的应用程序。

## <a name="store-icon"></a>存储图标

商店图标 （或市场营销图标） 不再管理在 iTunes Connect，并改为必须包含作为[资产目录](~/ios/app-fundamentals/images-icons/app-icons.md)中你的应用程序二进制文件，而类似于应用程序图标。 PNG 格式的 1024 x 1024 存储图标必须包含在 iOS 11 应用的成功提交一个资产目录。

应用细化可以确保此附加资产目录不会增加应用程序大小。


## <a name="in-app-purchases-promoted-in-the-app-store"></a>在应用商店中提升的应用内购买

Apple 已应用内购买更容易地发现在应用商店中。 你现在可以添加最多 20 个_提升的应用商店_现在可以在应用页面上，在你的应用的产品页上，或通过搜索找到的应用内购买。

若要具有你在应用商店中显示的应用内购买必须包含以下数据：

- **映像**– 你需要提供专门设计的映像描述应用内购买所执行的操作的图标。 此映像必须是不同的应用程序图标，并且不能为屏幕快照。
- **名称**– 名称只能是最多 30 个字符。
- **说明**– 该说明仅可包含的最多 45 字符。

任何应用内购买提升受到应用程序存储区评审，才可以发布。

若要使你可用于将提升的应用内购买，打开你的应用程序，并浏览到**功能 > 应用内购买**。 转到**应用存储升级 （可选）**部分，然后添加 1024 x 1024 映像和**保存**，如以下图像中所示：

![应用商店中 iTune 连接的提升部分](app-store-changes-images/image4.png)

你还需要添加`ShouldAddStorePayment`方法`SKPaymentTransactionObserver`应用程序中的协议。

应用内购买提升的详细信息，请参阅 Apple 的[升级你的应用内购买](https://developer.apple.com/app-store/promoting-in-app-purchases/)页。

## <a name="redesigned-product-page"></a>经过重新设计的产品页

到产品页进行了以下更改：

- 标题现在都设置为最多 30 个字符
- 已添加副标题
    - 它应该是补充标题的简短摘要。
    - 副标题应具有最多 30 个字符
- 应用预览
    - 你现在可以具有三个视频或屏幕快照。
    - 在用户访问页时，自动视频播放。
    - 有关详细信息，请参阅 Apple 的[应用预览版](https://developer.apple.com/app-store/app-previews/)页。
- 促销文本
    - 此新功能提供 170 个字符的文本，从而可以描述有关该应用程序经常发生变化的信息。
    - 它可以更新在任何时间而无需提交的应用程序的新版本。

## <a name="customer-communication"></a>客户通信

10.3，Apple 启动开发人员可以直接与通过响应评审的能力的应用程序用户进行通信的新方法。 你可以访问在 iTunes 中的此新功能连接通过浏览到**应用 > 活动 > 评级和评论**，如以下图像中所示：

![对话框显示用于输入批注答复区域](app-store-changes-images/image5.png)

有几个事项需要注意的在答复用户时：

- 您可以仅一次，回复但双方可以根据他们希望编辑注释。
- 响应批注时，用户将收到通知。
- 连接新客户支持已在 iTunes 中创建角色。 与此角色或管理员角色的用户可以响应的注释。

有关详细信息，请参阅 Apple 的[响应评审](https://developer.apple.com/app-store/responding-to-reviews/)页。

<a name="Phased_Release"/>

## <a name="phased-release"></a>分阶段的发布

与 iOS 11，一起 Apple 已实现分阶段发布更新向应用程序的选项。 你可以启用分阶段的版本，允许你应用更新逐渐发布给客户，确保此要求不会重载生产环境。

在 iTunes Connect 中启用了分阶段的版本。 单击你的应用程序在侧栏中，滚动至**分阶段的自动更新的发布**在的底部，部分，并选择**通过 7 天期间使用的版本更新分阶段发布**:

![选项显示分阶段为自动更新的版本](app-store-changes-images/image6.png)

更新后可立即用于应用商店的更新选项卡中的下载。 分阶段的版本才可供选择的自动下载的用户。


## <a name="related-links"></a>相关链接

- [什么是新建 iOS 11 (Apple)](https://developer.apple.com/ios/)
- [更新的应用商店产品页 (Apple)](https://developer.apple.com/app-store/product-page/)
- [更新你的应用程序适用于 iOS 11 (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/204/)
