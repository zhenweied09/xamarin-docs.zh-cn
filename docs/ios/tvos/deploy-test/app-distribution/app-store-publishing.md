---
title: "发布到 Apple 电视应用商店"
description: "这篇文章演示如何配置、 生成和发布通过 Apple 电视应用商店分发 Xamarin.tvOS 应用。 它包括介绍如何准备应用以便分发、 如何使用 Apple 的工具来提交供查看，应用程序以及，最后，如何将应用发布到 Apple 电视应用商店的分步指南。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: abb8ee30828e5d9856b9fd72cca8adb669959818
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="publishing-to-the-apple-tv-app-store"></a>发布到 Apple 电视应用商店

_这篇文章演示如何配置、 生成和发布通过 Apple 电视应用商店分发 Xamarin.tvOS 应用。它包括介绍如何准备应用以便分发、 如何使用 Apple 的工具来提交供查看，应用程序以及，最后，如何将应用发布到 Apple 电视应用商店的分步指南。_

按顺序将分发到所有 Apple TV 设备的应用程序、 Apple 要求应用程序，以便通过发布*Apple 电视 App Store*，使应用商店 tvOS 应用的一站式购物位置。 许多类型的应用的开发人员可以分发此单点大规模成功采用大写形式。 Apple 电视应用商店是提供分发和支付系统的应用程序开发人员的成套解决方案。

提交到 Apple 电视应用商店应用程序的过程：

1. 创建应用 ID，选择“权利”。
2. 创建分发预配配置文件。
3. 使用此配置文件来生成你的应用程序。
4. 提交你的应用通过*iTunes Connect*。


这篇文章中我们将介绍设置、 生成和提交有关 Apple TV 应用商店分发应用程序所需的所有步骤。

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>提交应用程序前的准备

提交到 Apple 电视应用商店发布应用程序后，它将经历审阅过程由 Apple 以确保它满足质量和内容的 Apple 的准则。 如果应用程序不符合这些指南，Apple 将拒绝此应用程序，此时需要处理 Apple 提出的不符合项，然后重新提交。
因此，如果熟悉这些指南并尽力使应用程序与其相符，应用程序通过 Apple 审核的几率较大。 Apple 的准则位于[应用商店查看准则](https://developer.apple.com/appstore/resources/approval/guidelines.html)和[新 Apple tv 准备你的应用程序提交](https://developer.apple.com/tvos/submit/)。

提交应用时需要注意几点：

1. 请确保应用程序的说明与匹配的应用程序中包含的功能。
2. 测试应用程序不崩溃正常使用。 这包括你支持每个 Apple TV 设备上的使用情况。


Apple 还维护 Apple 电视应用商店提交提示的列表。 可在[在 App Store 上分发](https://developer.apple.com/appstore/resources/submission/tips.html)中阅读这些列表。

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>在 iTunes Connect 中配置应用程序

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa)是一套基于 web 的工具，除了别的之外管理 Apple 电视 App store 你 tvOS 应用。 Xamarin.tvOS 应用程序将需要进行正确设置，并可供查看提交到 Apple 和销售或作为 Apple 电视应用商店中免费的应用程序从根本上讲，释放前在 iTunes Connect 中配置。

请执行以下操作：

1. 验证 iTunes Connect 的**协议、税款和银行**部分中是否具备恰当的最新协议，以便发布免费或付费的 iOS 应用程序。
2. 创建一个新**iTunes Connect 记录**应用程序并指定其**显示名称**（如 Apple 电视应用商店中所示）。
3. 选择**售价**或指定免费发布应用程序。
4. 提供**应用存储图标**（大图标） 的操作，请在它支持的 Apple TV 设备中的应用程序和屏幕截图。 请参阅我们[处理图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)更多详细信息的指南。
5. 提供很明显，简洁**说明**的应用程序包括它的功能和向最终用户受益。
6. 提供**类别**，**子类别**，和**关键字**有助于在 Apple 电视 App Store 中找到你的应用程序的用户。
7. 提供 Apple 要求的你的网站的**联系人**和**支持人员** URL。
8. 设置你的应用程序**评级**，后者由 Apple 电视 App store 的家长控制。
9. 配置可选的 App Store 技术，如 **Game Center** 和**应用内购买**。

有关更多详细信息，请参阅我们[在 iTunes Connect 中配置你 tvOS 应用](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md)文档。

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>准备 App Store 分发

若要发布到 Apple 电视应用商店应用，首先需要生成分发，这涉及很多步骤。 下列各节介绍如何准备 Xamarin.tvOS 应用程序发布，以便它可以生成并将其提交到 Apple 电视应用商店评审和版本的所需的所有内容。

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>对应用程序服务进行预配

Apple 提供的特殊应用程序服务，也称为权利，为它创建一个唯一的 ID 时，则在您 tvOS 的应用程序时可以激活的选择。 无论你使用自定义授权，还是不，仍需要创建 Xamarin.tvOS 应用程序的唯一 ID，然后可以在 Apple 电视应用商店上发布它。

使用 Apple 的基于 Web 的 iOS 预配门户创建应用 ID 和（可选）选择权利，包括以下步骤：

1. 选择**设置** > **开发**。
2. 单击“+”按钮，并为新的应用程序提供“名称”和“捆绑 ID”。
3. 滚动到屏幕的底部，选择任何**应用程序服务**Xamarin.tvOS 应用程序将需要的。
4. 单击“继续”按钮，并按照屏幕上的说明创建新的应用程序 ID。

除了选择并配置所需的应用程序服务，定义你应用程序 ID 时，你还需要通过编辑同时 Xamarin.tvOS 项目中配置的应用程序 ID 和权利`Info.plist`和`Entitlements.plist`文件。

执行以下操作在 Visual Studio for Mac:

1. 在“解决方案资源管理器”中，双击 `Info.plist` 文件，将其打开进行编辑。
2. 在**tvOS 应用程序目标**部分，填写你的应用程序的名称，然后输入**捆绑标识符**创建时定义应用程序 id。
3. 保存对 `Info.plist` 文件所做的更改。
4. 在“解决方案资源管理器”中，双击 `Entitlements.plist` 文件，将其打开进行编辑。
5. 选择并配置所需的你 Xamarin.tvOS 应用程序，使其与安装程序时定义应用程序 id。 执行上面的权利
6. 保存对 `Entitlements.plist` 文件所做的更改。

有关详细说明，请参阅[为应用程序服务进行预配](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices)文档。 本文档已针对 iOS 编写的而相同的步骤用于设置的 Xamarin.tvOS 应用。

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>设置应用程序图标，启动映像和顶层架子映像

TvOS 可接受的 Apple，用于包含在 Apple 电视应用商店应用，需要正确的图标、 启动和所有它将在运行的 Apple TV 设备顶部架映像。 你将需要添加将编译到所需的图像资产`Assets.car`文件并包含在 Xamarin.tvOS 应用程序捆绑包，然后将其上载到 iTunes Connect。

有关详细说明，请参阅我们[处理图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)文档。

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>创建并安装分发配置文件

tvOS 使用*预配配置文件*以控制如何可以部署特定应用程序生成。 这些文件包含有关用于应用签名的证书、应用程序 ID 和应用安装位置的信息。 对于开发和临时分发，预配配置文件还包括允许部署应用的设备列表。 但是，有关 Apple TV 应用商店分发证书和应用程序的唯一 ID 信息都包括在内，因为公共分发的唯一机制是通过 Apple 电视应用商店。

使用 Apple 的基于 Web 的 iOS 预配门户的预配包含以下步骤：

1.  选择“预配” > “分发”。
2.  单击 **+** 按钮，然后选择你想要为创建的分布配置文件的类型**Apple 电视 App Store**。
3.  从要为其创建分发配置文件的下拉列表中，选择“应用程序 ID”。
4.  选择所需对应用程序签名证书。
5.  为新的**分发配置文件**输入**名称**，并生成配置文件。
6.  刷新在 Xcode 中可用的配置文件的列表。
7.  选择预配配置文件，Visual Studio 中用于分发**应用商店**_生成配置_。

有关详细说明，请参阅[创建分发配置文件](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile)和[在 Xamarin.iOS 项目中选择分发配置文件](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile)。 同样，这些文档都特定于 iOS 的但对于 tvOS 应用程序使用相同的方法。


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>设置应用程序的生成配置

默认情况下，当你创建一个新的 Xamarin.tvOS 应用_生成配置_自动创建两个**调试**和**版本**部署。 在向 Apple 进行将提交你的应用程序最终版本之前，有几个将需要对基进行的修改**版本**配置。

请执行以下操作：

1. 右键单击**项目名称**中**解决方案资源管理器**和选择**选项**可以打开进行编辑。
2. 如果你正面向 tvOS 的特定版本，请选择它下**tvOS 生成** > **iOS SDK 版本**。 对于 tvOS 支持预览版本，请将此值设置为**默认**。
3. 链接减小的总体大小的应用程序的可分发通过去除未使用的方法、 属性类，等等，并且在大多数情况下应保留为默认值的**仅链接 framework SDK**。 在某些情况下，例如使用某些特定第三方库时，可能会强制将此值设置为“不链接”，防止删除需要的元素。
4. 若要提供 Xamarin.tvOS 应用，你将需要使用 LLVM 优化编译器。 确保**使用优化编译器 LLVM**下选中复选框**版本**配置。
5. Apple 还 tvOS 应用使用 bitcode 必选项。 下面再次**版本**配置中，添加`--bitcode=asmonly`到**其他 mtouch 参数**框。
6. **适用于 iOS 的优化 PNG 图像文件**应选中复选框，因为这样做有助于进一步减少到你的应用的可交付结果大小。
7. *不*应启用调试，因为这将为生成增加不必要的大小。


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>生成并提交可分发文件

与你正确配置的 Xamarin.tvOS 应用，现在已准备进行将提交你的最终分发生成向 Apple 进行查看和版本。

#### <a name="build-your-archive"></a>生成存档

1. 在 Visual Studio for Mac 中选择“发布 | 设备”配置：

    ![](app-store-publishing-images/buildxs01new.png "选择发布配置")
2. 在“生成”菜单中，选择“存档以供发布”：

    [![](app-store-publishing-images/buildxs02new.png "选择用于发布的存档")](app-store-publishing-images/buildxs02new.png#lightbox)
3. 存档创建完成后，将显示“存档”视图：

    [![](app-store-publishing-images/buildxs03new.png "存档视图")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>签名和分发应用

每次生成用于存档的应用程序时，都会自动打开“存档视图”，显示所有已存档的项目（按解决方案分组）。 默认情况下，此视图只显示当前打开的解决方案。 若要查看拥有存档的所有解决方案，请单击“显示所有存档”选项。

建议保留部署到客户（App Store 或企业部署）的存档，以便可在稍后的日期表示生成的任何调试信息。

对应用进行签名并准备分发：

1. 选择**对签名并分配...**，如下所示：

    [![](app-store-publishing-images/buildxs04new.png "选择 theSign 和分发...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. 这将打开发布向导。 选择“App Store”分发频道以创建包，再打开应用加载程序：

    [![](app-store-publishing-images/distribute01.png "选择应用程序存储的分销渠道")](app-store-publishing-images/distribute01.png#lightbox)
3. 在预配配置文件屏幕上，选择你的签名标识和对应预配配置文件，或使用其他身份重新登录：

    [![](app-store-publishing-images/distribute02.png "选择的签名标识和对应预配配置文件")](app-store-publishing-images/distribute02.png#lightbox)
4. 验证包的详细信息，再单击“发布”以保存 `.ipa` 包：

    [![](app-store-publishing-images/distribute03.png "验证包的详细信息")](app-store-publishing-images/distribute03.png#lightbox)
5. 保存 `.ipa` 后，应用即可通过应用加载程序上传到 iTunes Connect：

    [![](app-store-publishing-images/distribute04.png "上载到 iTunes 应用程序加载程序通过连接")](app-store-publishing-images/distribute04.png#lightbox)

创建分发生成并将其存档后，现可将应用程序提交到 iTunes Connect。

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>将应用提交到 Apple

分发生成完成后，即可将 iOS 应用程序提交到 Apple 以供审核，然后在 App Store 上发布。


适用于 Mac 的 Visual Studio 中的存档工作流将应用程序加载程序会自动打开，保存后`.ipa`:

2. 选择“提交应用”，单击“选择”按钮：

    [![](app-store-publishing-images/publishvs01.png "选择提供你的应用程序")](app-store-publishing-images/publishvs01.png#lightbox)

3. 选择前面创建的 zip 或 IPA 文件，单击“确定”按钮。
4. 应用加载程序将验证文件：

    [![](app-store-publishing-images/publishvs02.png "应用程序加载程序验证屏幕")](app-store-publishing-images/publishvs02.png#lightbox)
5. 单击“下一步”按钮，将按照 App Store 的要求验证应用：

    [![](app-store-publishing-images/publishvs03.png "正在验证针对应用商店应用程序")](app-store-publishing-images/publishvs03.png#lightbox)
6. 单击“发送”按钮，将应用程序发送到 Apple 以供审核。
7. 文件成功上传后，应用程序加载程序将通知你。

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>iTunes Connect 状态

如果您重新登录到 iTunes Connect 并从可用的应用的列表中选择你的应用程序，在 iTunes Connect 中的状态现在应显示它是**等待查看**(它可能会暂时在读取**上载收到**它正在处理）：

[![](app-store-publishing-images/image21.png "在 iTunes 中的状态连接显示等待评审")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>疑难解答

如果你遇到的问题提交到 Apple 电视 App Store 你 Xamarin.tvOS 的应用，请参阅我们[故障排除](~/ios/tvos/troubleshooting.md)指南。 它包含可能会遇到的一些已知的问题和如何解决这些 Xamarin.tvOS 中。

<a name="Summary" />

## <a name="summary"></a>摘要

本文介绍配置、 生成和提交针对 Apple 电视应用商店发布的应用程序的分步指南。 首先，介绍创建并安装分发配置文件所需的步骤。 接下来，它在遍历如何使用适用于 Mac 的 Visual Studio 创建的分发版本。 最后，它向您展示如何使用 iTunes Connect 和 Xcode 存档工具来提交到 Apple 电视应用商店应用程序。


## <a name="related-links"></a>相关链接

- [使用图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)
- [为新的 Apple TV 准备你的应用程序提交](https://developer.apple.com/tvos/submit/)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [常见的应用拒绝](https://developer.apple.com/app-store/review/rejections/)
- [App Store 审核指南](https://developer.apple.com/appstore/resources/approval/guidelines.html)
