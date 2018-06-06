---
title: 将 watchOS 应用部署到应用商店
description: 本文档介绍如何部署 watchOS 应用使用应用商店的 Xamarin 生成。 花查看分发预配配置文件和 iTunes 连接，并且它还提供了一些故障排除技巧。
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 70613c79c2ec0c81f1dbdc218b747f809f859767
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790979"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>将 watchOS 应用部署到应用商店

> [!IMPORTANT]
> 请务必查看[Apple 的监视工具包提交指南](https://developer.apple.com/app-store/watch/)，请参阅和[故障排除](#Troubleshooting)部分以了解可能会有任何问题。

- 确保其中有：
  - [**分发预配配置文件**](#provisioning)创建你的项目。
  - **部署目标**(`MinimumOSVersion`) 为 iOS 父应用程序设置为**8.2**或更早版本 （不支持 8.3）。

- 在[ **iTunes Connect**](#iTunes_Connect):

  - 创建你的 iOS 应用程序条目 (或添加**新版本**到现有应用程序)。
  - 添加监视图标和屏幕快照。

- 然后在[Visual Studio for Mac](#xamarin_studio) （Visual Studio 目前不支持）：

  - 右键单击 iOS 应用程序并选择**设为启动项目**。
  - 将更改为**应用商店**配置。
  - 使用**存档**功能创建一个应用程序存档。

- 最后，切换到[Xcode 6.2 +](#xcode)

  - 转到**窗口 > 管理器**选择**存档**。
  - 从列表中选择的应用程序和存档。
  - （可选）**验证...** 存档。
  - **提交...** 存档并遵循的步骤将上载到 iTunes 连接用于审查和批准。

阅读以下这些项与相关的特定提示。 请参阅[故障排除](#Troubleshooting)部分中如果遇到问题。

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>预配配置文件的分发

若要生成适用于你需要创建的应用商店部署**分发预配配置文件**你的解决方案中每个应用程序 id。

如果你有通配符应用程序 ID，*只有一个预配配置文件将需要*; 但是，如果你有独立的应用程序 ID 的每个项目都需要预配配置文件为每个应用程序 ID:

![](appstore-images/provisioningprofile-distribution-sml.png "应用商店分发配置文件")

一旦你已创建所有三个配置文件，它们将显示在列表中。 请记住下载并安装每个 （通过双击它）：

![](appstore-images/provisioningprofiles-sml.png "可用的配置文件的列表")

你可以验证在预配配置文件**项目选项**通过选择**生成 > iOS 捆绑签名**屏幕并选择**AppStore | iPhone**配置。

**预配配置文件**列表将显示所有匹配的配置文件-您应看到已创建此下拉列表中的匹配配置文件。

![](appstore-images/options-selectprofile-sml.png "IOS 捆绑签名对话框")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

请按照[应用分发概述](~/ios/deploy-test/app-distribution/index.md)，具体而言：

- [在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [发布到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

在配置应用程序在 iTunes Connect 中时，不要忘记添加监视图标和屏幕快照：

![](appstore-images/itunesconnect-watch-sml.png "监视图标和在 iTunes Connect 中的屏幕截图")

图标文件应为 1024 x 1024 像素，并且将具有显示时会被应用于它的循环掩码。 图标不应具有 alpha 通道。

至少一个屏幕截图是必需的可能会提交最多五个。
它们应为 312 x 390 像素，并演示在操作中您监视的应用程序。
可以使用 42 mm 监视模拟器要进行的此大小的屏幕快照。


<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 请确保 iOS 应用程序启动项目。 如果没有，请右键单击以将其设置：

  ![](appstore-images/xs-startup.png "设置启动项目")

2. 选择**AppStore**生成配置：

  ![](appstore-images/xs-appstore.png "AppStore 生成配置")

3. 选择**生成 > 存档**菜单项以启动存档过程：

  ![](appstore-images/xs-archive.png "生成菜单")

你还可以选择**视图 > 存档...** 菜单项以查看前面创建的存档。

  ![](appstore-images/xs-archives-sml.png "存档视图")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode 将自动显示存档在 Visual Studio 中创建的 mac。

1. 启动 Xcode，然后选择**窗口 > 管理器**:

  ![](appstore-images/xc-organizer.png "窗口菜单")

2. 切换到**存档**选项卡并选择使用 Visual Studio 创建的 Mac 的存档：

  ![](appstore-images/xc-archives.png "存档选项卡")

3. （可选)**验证...** 存档，然后选择**提交...** 若要将应用上载到 iTunes Connect。

4. （如果你属于多个），请选择开发团队然后确认提交：

  ![](appstore-images/xc-submit1.png "开发团队部分")

5. 请访问 iTunes Connect 试以查看上载的二进制文件。 转到应用程序配置页上，选择**预发行版**从顶部菜单中，若要查看**生成**列表：

  [![](appstore-images/itc-prerelease-sml.png "在 iTunes Connect 中应用配置页")](appstore-images/itc-prerelease.png#lightbox)

然后可以在提交以供审核应用**版本**页。 请参阅[iOS 应用程序分发概述](~/ios/deploy-test/app-distribution/index.md)有关详细信息。


## <a name="troubleshooting"></a>疑难解答

以下是将提交到应用商店中，以及可以采取用来修复它们的步骤时，你可能会遇到一些错误。

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>存档菜单选项将不适用于 Mac 的 Visual Studio 中可见

请按照[上述步骤](#xamarin_studio)配置存档的解决方案。 如果你不能正确设置启动项目，请确保生成配置首先设置为调试或发布之前尝试更改启动项目。 请将设置生成配置回**AppStore**。

### <a name="invalid-icon"></a>“无效”图标

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcons27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

请按照[说明删除 alpha 通道](~/ios/watchos/troubleshooting.md)从您的图标。

### <a name="cfbundleversion-mismatch"></a>CFBundleVersion 不匹配

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

IOS 应用、 监视扩展中和监视应用程序的-解决方案中的所有项目应都使用相同的版本号。 编辑每个**Info.plist**文件，以便的版本号与完全匹配。

### <a name="missing-icons"></a>缺少图标

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

按照中的说明[使用图标](~/ios/watchos/app-fundamentals/icons.md)将所有所需的映像添加到监视应用程序项目。

### <a name="missing-icon"></a>缺少图标

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

确保你有 Visual Studio 的最新版本的 Mac 上，并且你**AppIcons.appiconset**包含一组完整的映像。 如果你仍看到此错误，查看的源**Contents.json**以确定它是否包含所有所需的映像的条目。 或者，你可确保你在使用 Xamarin 最新版本后, 删除并重新创建**AppIcons.appiconset**。

> [!IMPORTANT]
> 是一个已知的 bug，在 Visual Studio 中的 Mac 的监视图标支持： 期望 88 x 88 像素图像**29x29@3x**映像 （它应是 87 x 87 像素为单位）。


无法为 Mac-编辑在 Xcode 中的图像资产解决此问题在 Visual Studio 中，或手动编辑**Contents.json**文件 (以匹配[此示例](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132))。



### <a name="invalid-watchkit-support"></a>无效的 WatchKit 支持

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

出现此消息可能期间验证和提交，或在的自动电子邮件从 iTunes Connect 显然是成功上载后。
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> 你必须**存档**Visual Studio for Mac 并切换到 Xcode 6.2 + 验证并将上载到 iTunes Connect 中的应用。


使用稳定 Xamarin 通道和 Xcode 6.2 +。



### <a name="invalid-provisioning-profile"></a>无效的预配配置文件

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

**分发预配配置文件**必须提供监视应用程序解决方案中的所有三个项目的： iOS 应用程序、 监视扩展中，和监视应用程序的任一显式 （三个配置文件） 或通过单个通配符配置文件。 检查预配配置文件存在 iOS 开发人员中心中并且你已下载并将其添加到你的 mac。

### <a name="invalid-code-signing-entitlements"></a>无效的代码签名授权

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

确保你预配配置文件设置在 Apple 开发人员中心上正常并且你已下载并安装它们。 此外请检查在 Visual Studio 中的 Mac 的每个项目的属性窗口设置。

### <a name="invalid-architecture"></a>无效的体系结构

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

你仅可以添加监视应用程序[统一 API （64 位）](~/cross-platform/macios/unified/index.md) Xamarin.iOS 应用程序。
右键单击 iOS 应用项目，然后转到**选项 > 生成 > iOS 生成 > 高级选项卡**并确保**支持体系结构**对于 AppStore iPhone 配置包括**ARM64** （如。 **ARMv7 + ARM64**)。

### <a name="this-bundle-is-invalid"></a>此捆绑包无效。

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

父 iOS 应用程序必须设置为"8.2"或更低 MinimumOSVersion。

### <a name="non-public-api-usage"></a>非公共 API 使用情况

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

确保使用最新版本的 Xcode 和 Xamarin 的工具。
你的代码不应访问的任何非公共 Api。

### <a name="build-error-mt5309"></a>生成错误 MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

此错误可能是你具有重命名从 Xcode 安装的结果**Xcode.app**。 例如，将出现此错误，如果重命名您的安装与**XCode 6.2.app**。



## <a name="related-links"></a>相关链接

- [Apple WatchKit 提交指南](https://developer.apple.com/app-store/watch/)
