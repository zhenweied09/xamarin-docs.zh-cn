---
title: 发布到 App Store
description: 本文介绍如何配置、生成和发布 Xamarin.iOS 应用程序，以通过 App Store 进行分发。 本文包含分布指南，其中涵盖如何准备用于分发的应用程序、如何使用 Apple 的工具提交应用程序以供审核，以及最后如何将应用程序发布到 App Store。
ms.prod: xamarin
ms.assetid: DFBCC0BA-D233-4DC4-8545-AFBD3768C3B9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/23/2017
ms.openlocfilehash: 5d78cb81f27ce7478719ff9f11f4eb38fddc3981
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="publishing-to-the-app-store"></a>发布到 App Store

本文介绍如何配置、生成和发布 Xamarin.iOS 应用程序，以通过 App Store 进行分发。_本文包含分步指南，介绍如何准备用于分发的应用程序、如何使用 Apple 的工具提交应用程序以供审核，以及最后如何将应用程序发布到 App Store。_

为了将应用程序分发到所有 iOS 设备，Apple 要求应用通过 App Store 发布，使 App Store 成为 iOS 应用程序的一站式购物位置。 App Store 中包括超过 500,000 种应用程序，许多类型应用程序的开发人员采用了这种非常成功的单点分发。 App Store 是一种统包解决方案，可同时为应用开发人员提供分发和支付系统。

将应用程序提交到 App Store 的流程包括：

1. 创建应用 ID，选择“权利”。
2. 创建“分发预配配置文件”。
3. 使用此配置文件生成应用程序。
4. 通过 iTunes Connect 提交应用程序。


本文涵盖了预配、生成和提交用于 App Store 分发的应用程序所需的所有步骤。

## <a name="before-you-submit-an-application"></a>提交应用程序前的准备

提交要发布到 App Store 的应用后，将经过 Apple 的审核流程，确保其符合 Apple 的质量和内容指南。 如果应用程序不符合这些指南，Apple 将拒绝此应用程序，此时需要处理 Apple 提出的不符合项，然后重新提交。
因此，如果熟悉这些指南并尽力使应用程序与其相符，应用程序通过 Apple 审核的几率较大。 可在 [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html)（App Store 审核指南）中查看 Apple 的指南。

提交应用时需要注意几点：

1. 请确保应用程序的说明与应用中包括的功能匹配。
2. 测试应用程序不会在正常使用时发生崩溃。 这包括在支持的每台 iOS 设备上使用。


Apple 还会保留 App Store 提交提示列表。 可在[在 App Store 上分发](https://developer.apple.com/appstore/resources/submission/tips.html)中阅读这些列表。

## <a name="configuring-your-application-in-itunes-connect"></a>在 iTunes Connect 中配置应用程序

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 是一系列基于 Web 的工具，它可在 App Store 上管理 iOS 应用程序等。 Xamarin.iOS 应用程序需要在 iTunes Connect 中进行恰当安装并配置，然后才能提交到 Apple 进行审核并最终在 App Store 作为付费或免费应用发布。

请执行以下操作：

1. 验证 iTunes Connect 的**协议、税款和银行**部分中是否具备恰当的最新协议，以便发布免费或付费的 iOS 应用程序。
2. 为应用程序创建新的 **iTunes Connect 记录**，并指定其在 App Store 中的**显示名称**。
3. 选择**售价**或指定免费发布应用程序。
5. 提供应用程序的简明**说明**，包括其功能和对最终用户的好处。
6. 提供**类别**、**子类别**和**关键字**，帮助用户在 App Store 中查找应用。
7. 提供 Apple 要求的你的网站的**联系人**和**支持人员** URL。
8. 设置应用程序的**评级**，供 App Store 上的家长控制使用。
9. 配置可选的 App Store 技术，如 **Game Center** 和**应用内购买**。

有关详细信息，请参阅[在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)文档。

## <a name="preparing-for-app-store-distribution"></a>准备 App Store 分发

若要将应用程序发布到 App Store，首先需要生成应用程序以进行分发，这涉及到许多步骤。 以下部分介绍了准备 Xamarin.iOS 应用程序以供发布所需的所有内容，以便可生成应用并提交到 App Store，然后进行审核和发布。

### <a name="provisioning-for-application-services"></a>对应用程序服务进行预配

Apple 提供一系列特殊的应用程序服务（也称为权利），为 iOS 应用程序创建唯一 ID 时，可为其激活这些服务。 无论是否使用自定义权利，都需要为 Xamarin.iOS 应用程序创建唯一 ID，然后才能在 App Store 上将其发布。

使用 Apple 的基于 Web 的 iOS 预配门户创建应用 ID 和（可选）选择权利，包括以下步骤：

1. 在“证书、标识符和描述文件”部分中，选择“标识符” > “应用 ID”。
2. 单击“+”按钮，并为新的应用程序提供“名称”和“捆绑 ID”。
3. 滚动到屏幕底部，选择 Xamarin.iOS 应用程序所需的任何**应用服务**。
4. 单击“继续”按钮，并按照屏幕上的说明创建新的应用程序 ID。

定义应用程序 ID 时，除了选择并配置所需的应用程序服务，还需通过编辑 `Info.plist` 和 `Entitlements.plist` 文件，在 Xamarin.iOS 项目中配置应用程序 ID 和权利。

请执行以下操作：

1. 在“解决方案资源管理器”中，双击 `Info.plist` 文件，将其打开进行编辑。
2. 在“iOS 应用程序目标”部分，填写应用程序的名称并输入定义应用程序 ID 时创建的“捆绑标识符”。
3. 保存对 `Info.plist` 文件所做的更改。
4. 在“解决方案资源管理器”中，双击 `Entitlements.plist` 文件，将其打开进行编辑。
5. 选择并配置 Xamarin.iOS 应用程序所需的权利，使其与定义应用程序 ID 时执行的设置相匹配。
6. 保存对 `Entitlements.plist` 文件所做的更改。

有关详细说明，请参阅[为应用程序服务进行预配](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices)文档。

### <a name="setting-the-store-icons"></a>设置存储图标

应用商店图标现在应由资产目录提供。 若要添加应用商店图标，首先找到项目的 Assets.xcassets 文件中的 AppIcon 映像设置。

资产目录中所需的图标名为应用商店，其大小应该是 1024 x 1024。 Apple 已声明，资产目录中的应用存储图标不能是透明的，也不能包含 alpha 通道。

有关设置应用商店图标的信息，请参阅[应用存储图标](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。

### <a name="setting-the-apps-icons-and-launch-screens"></a>设置应用图标和启动屏幕

如果希望 Apple 接受 iOS 应用程序并将其包含在 App Store 中，需要提供适用于将运行应用程序的所有 iOS 设备的恰当图标和启动屏幕。 应用图标通过 Assets.xcassets 文件中的 AppIcon 图像集添加到资产目录中的项目。 通过 Storyboard 添加启动屏幕。

有关创建应用程序图标和启动屏幕的详细说明，请参阅[应用程序图标](~/ios/app-fundamentals/images-icons/app-icons.md)和[启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)指南。

### <a name="creating-and-installing-a-distribution-profile"></a>创建并安装分发配置文件

iOS 使用预配配置文件来控制特定应用程序生成的部署方式。 这些文件包含有关用于应用签名的证书、应用程序 ID 和应用安装位置的信息。 对于开发和临时分发，预配配置文件还包括允许部署应用的设备列表。 但是，对于 App Store 分发，只包括证书和应用程序 ID 信息，因为公开发布的唯一机制是通过 App Store。

使用 Apple 的基于 Web 的 iOS 预配门户的预配包含以下步骤：

1.  选择“预配” > “分发”。
2.  单击“+”按钮，选择要创建为“App Store”的分发配置文件的类型。
3.  从要为其创建分发配置文件的下拉列表中，选择“应用程序 ID”。
4.  选择有效的生产（分发）证书，为应用程序签名。
5.  为新的**分发配置文件**输入**名称**，并生成配置文件。
6.  在 Mac 上，打开 Xcode 并浏览到“首选项”>[选择你的 Apple ID]>“查看详细信息...”。在 Mac 上下载 Xcode 中所有可用的配置文件。
7.  返回到 IDE，在“iOS 捆绑签名”选项下，为正确的_生成配置_选择分发预配配置文件（可以是 **App Store** 或**发布**）。

有关详细说明，请参阅[创建分发配置文件](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile)和[在 Xamarin.iOS 项目中选择分发配置文件](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile)。

## <a name="setting-the-build-configuration-for-your-application"></a>设置应用程序的生成配置

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

请执行以下操作：

1. 在“Solution Pad”中，右键单击“项目名称”，然后选择“选项”，将其打开并进行编辑。
2. 在“配置”下拉列表中，依次选择“iOS 版本”和“发布 | iPhone”：

    ![](publishing-to-the-app-store-images/configurevs01.png "从“配置”下拉列表选择“AppStore”")

3. 如果拥有特定的 iOS 版本，可从“SDK 版本”下拉列表中选择它，或将此值保留为默认设置。
4. 链接可通过去除无用的方法、属性、类等，减少应用程序的可分发文件的总大小，在大多数情况下，应保留为默认值“仅链接 SDK 程序集”。 在某些情况下，例如使用某些特定的第三方库时，可能会强制你将此值设置为“不链接”，以防止所需的元素被删除。 有关详细信息，请参阅 [iOS 生成机制](~/ios/deploy-test/ios-build-mechanics.md)指南。
5. 应选中“优化适用于 iOS 的 PNG 图像文件”复选框，因为这有助于进一步减少应用程序的可交付文件大小。
6. _不_应启用调试，因为这将使生成增加不必要的大小。
8. 对于 iOS 11，你将需要选择一个支持 ARM64 的设备体系结构。 若要深入了解适用于 64 位 iOS 设备的生成，请参阅 [32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md)文档中的**启用 Xamarin.iOS 应用的 64 位生成**部分。
9. 也可选择使用 **LLVM** 编译器，它可创建更小更快的代码，但这需要更长的编译时间。
10. 根据应用程序的需要，可能需要调整使用的**垃圾回收**的类型以及**国际化**的设置。
11. 保存对生成配置所做的更改。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

默认情况下，在 Visual Studio 中创建新的 Xamarin.iOS 应用程序时，将自动为 **Ad Hoc** 和 **App Store** 部署创建_生成配置_。 为将提交到 Apple 的应用程序执行最终生成之前，需要对基本配置进行一些修改。

请执行以下操作：

1. 在“解决方案资源管理器”中，右键单击“项目名称”，然后选择“属性”，将其打开并进行编辑。
2. 从“配置”下拉列表中选择“iOS 版本”和“AppStore”（如果 AppStore 不可用，则选择“版本”）：

    ![](publishing-to-the-app-store-images/configurevs01.png "从“配置”下拉列表选择“AppStore”")

3. 如果拥有特定的 iOS 版本，可从“SDK 版本”下拉列表中选择它，或将此值保留为默认设置。
4. 链接可通过去除无用的方法、属性、类等，减少应用程序的可分发文件的总大小，在大多数情况下，应保留为默认值“仅链接 SDK 程序集”。 在某些情况下，例如使用某些特定的第三方库时，可能会强制你将此值设置为“不链接”，以防止所需的元素被删除。 有关详细信息，请参阅 [iOS 生成机制](~/ios/deploy-test/ios-build-mechanics.md)指南。
5. 应选中“优化适用于 iOS 的 PNG 图像文件”复选框，因为这有助于进一步减少应用程序的可交付文件大小。
6. _不_应启用调试，因为这将使生成增加不必要的大小。
7. 单击“高级”选项卡：

    ![](publishing-to-the-app-store-images/configurevs02.png "“高级”选项卡")

8. 如果 Xamarin.iOS 应用程序面向 iOS 8 和 64 位 iOS 设备，需要选择支持 **ARM64** 的一种设备体系结构。 若要深入了解适用于 64 位 iOS 设备的生成，请参阅 [32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md)文档中的**启用 Xamarin.iOS 应用的 64 位生成**部分。
9. 也可选择使用 **LLVM** 编译器，它可创建更小更快的代码，但这需要更长的编译时间。
10. 根据应用程序的需要，可能需要调整使用的**垃圾回收**的类型以及**国际化**的设置。
11. 保存对生成配置所做的更改。

-----

## <a name="building-and-submitting-the-distributable"></a>生成并提交可分发文件

正确配置 Xamarin.iOS 应用程序后，可开始构建最终的分发生成，此生成将提交到 Apple 进行审核和发布。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

### <a name="build-your-archive"></a>生成存档

1. 在 Visual Studio for Mac 中选择“发布 | 设备”配置：

    ![](publishing-to-the-app-store-images/buildxs01new.png "选择“发布 | 设备”配置")
1. 在“生成”菜单中，选择“存档以供发布”：

    ![](publishing-to-the-app-store-images/buildxs02new.png "选择“存档以供发布”")

1. 存档创建完成后，将显示“存档”视图：

    ![](publishing-to-the-app-store-images/buildxs03new.png "将显示“存档”视图")


> [!NOTE]
> 尽管旧的 App Store 和临时配置现在已从所有 Visual Studio for Mac 模板项目中删除，但仍可找到包括这些配置的旧项目。 如果出现这种情况，可继续使用上面列表的步骤 1 中的“App Store | 设备”配置。

### <a name="sign-and-distribute-your-app"></a>签名和分发应用

 每次生成用于存档的应用程序时，都会自动打开“存档视图”，显示所有已存档的项目（按解决方案分组）。 默认情况下，此视图只显示当前打开的解决方案。 若要查看拥有存档的所有解决方案，请单击“显示所有存档”选项。

 建议保留部署到客户（App Store 或企业部署）的存档，以便可在稍后的日期表示生成的任何调试信息。

 对应用进行签名并准备分发：


1. 选择“签名并分发...”，如下所示：

    ![](publishing-to-the-app-store-images/buildxs04new.png "选择“签名和分发...”")

1. 这将打开发布向导。 选择“App Store”分发频道以创建包，再打开应用加载程序：

    ![](publishing-to-the-app-store-images/distribute01.png "打开应用程序加载程序")

1. 在“配置文件”屏幕上，选择签名标识和对应的配置文件，或使用其他标识重新签名：

    ![](publishing-to-the-app-store-images/distribute02.png "选择签名标识和相应的预配配置文件")

1. 验证包的详细信息，再单击“发布”以保存 `.ipa` 包：

    ![](publishing-to-the-app-store-images/distribute03.png "验证包的详细信息")

1. 保存 `.ipa` 后，应用即可通过应用加载程序上传到 iTunes Connect：

    ![](publishing-to-the-app-store-images/distribute04.png "“发布成功”屏幕")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

适用于 Visual Studio 的 Xamarin 插件当前不支持存档工作流将 iOS 应用程序发布到 App Store。 因此，必须上传通过 **Build Ad hoc IPA** 命令创建的 IPA，如下所述。


## <a name="build-an-ipa"></a>生成 IPA

 本部分介绍如何生成 IPA，类似于使用 Ad Hoc 或企业分发时的工作流。 但是，将使用前面创建的 App Store 配置文件对其进行签名。

 请执行以下操作：

1. 在“解决方案资源管理器”中，右键单击 Xamarin.iOS 项目名称，选择“属性”将其打开进行编辑：

    ![](publishing-to-the-app-store-images/imagevs01.png "选择“属性”")
1. 选择“iOS 捆绑签名”，将预配配置文件更改为 App Store 预配配置文件：

    ![](publishing-to-the-app-store-images/ipa01.png "选择“iOS 捆绑签名”，将预配配置文件更改为 App Store 预配配置文件")
1. 依次选择“iOS IPA 选项”和“配置”下拉列表中的“临时”（如果没有看到“临时”，请改选“发布”）：

    ![](publishing-to-the-app-store-images/imagevs02.png "从“配置”下拉列表选择“临时”")

1. 可选择指定 IPA 的**包名称**，若未指定，则将使用与 Xamarin.iOS 项目相同的名称。
1. 保存对“项目属性”所做的更改。
1. 选择 Visual Studio for Mac“生成配置”下拉列表中的“临时”：

    ![](publishing-to-the-app-store-images/imagevs05.png "从“生成配置”下拉列表选择“临时”")
1. 生成项目以创建 IPA 包。
1. 将在“`Bin` > iOS 设备 > `Ad Hoc`”文件夹中生成 IPA：

    ![](publishing-to-the-app-store-images/imagevs06.png "文件资源管理器中显示的 IPA")

-----


## <a name="customizing-the-ipa-location"></a>自定义 IPA 位置

添加了新的 **MSBuild** 属性 `IpaPackageDir`，可更轻松地自定义 `.ipa` 文件输出位置。 若将 `IpaPackageDir` 设置为自定义位置，`.ipa` 文件将放入该位置，而不是默认时间戳子目录。 创建依赖特定目录路径才能正常工作的自动生成，如用于持续集成 (CI) 生成的生成，此操作会很有帮助。

通过几种可能的方法使用新属性：

例如，若要将 `.ipa` 文件输出到旧的默认目录（如 Xamarin.iOS 9.6 和更低版本），可使用以下方法之一，将 `IpaPackageDir` 属性设置为 `$(OutputPath)`。 两种方法可兼容所有 Unified API Xamarin.iOS 生成，包括 IDE 生成，以及使用 `xbuild`、`msbuild` 和 `mdtool` 的命令行生成：

  - 第一个选项是，在 **MSBuild** 文件的 `<PropertyGroup>` 元素内，设置 `IpaPackageDir` 属性。 例如，可将以下 `<PropertyGroup>` 添加到 iOS 应用项目 `.csproj` 文件的底部（就在右 `</Project>` 标记前）：

      ```xml
        <PropertyGroup>
            <IpaPackageDir>$(OutputPath)</IpaPackageDir>
        </PropertyGroup>
      ```
  - 更好的方法是，将 `<IpaPackageDir>` 元素添加到现有 `<PropertyGroup>` 的底部，它对应用于生成 `.ipa` 文件的配置。 此方法更好，因为会在 iOS IPA 选项项目属性页上使用计划设置针对将来的兼容性准备项目。 如果当前使用 `Release|iPhone` 配置生成 `.ipa` 文件，完整的更新属性组可能如下所示：

      ```xml
      <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone' )
        <Optimize>true</Optimize>
        <OutputPath>bin\iPhone\Release</OutputPath>
        <ErrorReport>prompt</ErrorReport>
        <WarningLevel>4</WarningLevel>
        <ConsolePause>false</ConsolePause>
        <CodesignKey>iPhone Developer</CodesignKey>
        <MtouchUseSGen>true</MtouchUseSGen>
        <MtouchUseRefCounting>true</MtouchUseRefCounting>
        <MtouchFloat32>true</MtouchFloat32>
        <CodesignEntitlements>Entitlements.plist</CodesignEntitlements>
        <MtouchLink>SdkOnly</MtouchLink>
        <MtouchArch>;ARMv7, ARM64</MtouchArch>
        <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
        <MtouchTlsProvider>Default</MtouchTlsProvider>
        <PlatformTarget>x86&</PlatformTarget>
        <BuildIpa>true</BuildIpa>
        <IpaPackageDir>$(OutputPath</IpaPackageDir>
      </PropertyGroup>
      ```
另一种用于 `msbuild` 或 `xbuild` 命令行生成的方法是添加 `/p:` 命令行参数来设置 `IpaPackageDir` 属性。 注意，在这种情况下，`msbuild` 不会展开传入到命令行的 `$()` 表达式，因此不能使用 `$(OutputPath)` 语法。 必须改为提供完整路径名称。 Mono 的 `xbuild` 命令确实会展开 `$()` 表达式，但仍建议使用完整路径名称，因为在将来版本中，[跨平台版本的 `msbuild`](http://www.mono-project.com/docs/about-mono/releases/4.4.0/#msbuild-preview-for-os-x) 将取代 `xbuild`。 使用此方法的完整示例可能与 Windows 上的以下示例类似：

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

或 Mac 上以下示例：

```bash
xbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

创建分发生成并将其存档后，现可将应用程序提交到 iTunes Connect。

### <a name="automatically-copy-app-bundles-back-to-windows"></a>自动将 .app 应用程序包复制回 Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]

## <a name="submitting-your-app-to-apple"></a>将应用提交到 Apple

> [!NOTE]
> Apple 最近更改了 iOS 应用程序的验证流程，并可能拒绝 IPA 中包含 `iTunesMetadata.plist` 的应用。 如果遇到错误 `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`，[此处](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1)介绍的解决方法应能解决此问题。

分发生成完成后，即可将 iOS 应用程序提交到 Apple 以供审核，然后在 App Store 上发布。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

 请执行以下操作：

1. 启动 **Xcode**。
2. 在“窗口”菜单中，选择“组织者”。
3. 单击“存档”选项卡，选择上面生成的存档：

    ![](publishing-to-the-app-store-images/publishxs01.png "选择要提交的存档")
4. 单击“验证...”按钮。
5. 选择要验证的帐户，单击“选择”按钮：

    ![](publishing-to-the-app-store-images/publishxs02.png "选择要验证的帐户")
6. 单击“验证”按钮：

    ![](publishing-to-the-app-store-images/publishxs03.png "“文件摘要”对话框")
7. 如果存在捆绑包问题，会显示这些问题。
8. 更正问题，然后在 Visual Studio for Mac 中重新生成存档。
9. 单击“提交...”按钮。
10. 选择要验证的帐户，单击“选择”按钮：

    ![](publishing-to-the-app-store-images/publishxs04.png "选择要验证的帐户")
11. 单击“提交”按钮：

    ![](publishing-to-the-app-store-images/publishxs05.png "“文件摘要”对话框")
12. 将文件上传到 iTunes Connect 的操作完成后，Xcode 将通知你。


保存 .ipa 后，Visual Studio for Mac 中的存档工作流便会自动打开应用程序加载器：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

使用应用加载程序应用，将应用提交到 Apple 以供审核。 这些步骤必须在 Mac 生成主机上完成。 可从[此处](https://itunesconnect.apple.com/apploader/ApplicationLoader_3.0.dmg)下载应用程序加载程序的副本。

-----

1. 选择“提交应用”，单击“选择”按钮：

    [![](publishing-to-the-app-store-images/publishvs01.png "选择“提交应用”")](publishing-to-the-app-store-images/publishvs01.png#lightbox)

2. 选择前面创建的 zip 或 IPA 文件，单击“确定”按钮。

3. 应用加载程序将验证文件：

    [![](publishing-to-the-app-store-images/publishvs02.png "“验证”屏幕")](publishing-to-the-app-store-images/publishvs02.png#lightbox)
4. 单击“下一步”按钮，将按照 App Store 的要求验证应用：

    [![](publishing-to-the-app-store-images/publishvs03.png "对 App Store 进行验证")](publishing-to-the-app-store-images/publishvs03.png#lightbox)
5. 单击“发送”按钮，将应用程序发送到 Apple 以供审核。
6. 文件成功上传后，应用程序加载程序将通知你。

## <a name="itunes-connect-status"></a>iTunes Connect 状态

如果重新登录到 iTunes Connect，并从可用应用列表中选择你的应用程序，iTunes Connect 中的状态应显示为“等待审核”（处理过程中，可能暂时显示为“收到上传”）：

[![](publishing-to-the-app-store-images/image21.png "现在 iTunes Connect 中的状态应显示为“等待审核”")](publishing-to-the-app-store-images/image21.png#lightbox)

## <a name="summary"></a>总结

本文提供配置、生成和提交用于 App Store 发布的应用程序的分布指导。 首先，介绍创建并安装分发配置文件所需的步骤。 然后，介绍如何使用 Visual Studio 和 Visual Studio for Mac 创建分发生成。 最后，介绍如何使用 iTunes Connect 和工具，将应用程序提交到 App Store。


## <a name="related-links"></a>相关链接

- [使用图像](~/ios/app-fundamentals/images-icons/index.md)
- [iOS 应用开发工作流指南：分发应用程序](http://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/35-Distributing_Applications/distributing_applications.html)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [常见的应用拒绝](https://developer.apple.com/app-store/review/rejections/)
- [App Store 审核指南](https://developer.apple.com/appstore/resources/approval/guidelines.html)
