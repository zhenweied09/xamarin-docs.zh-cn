---
title: "部署和测试"
description: "在设备上测试并上载到应用商店"
ms.topic: article
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: fdd4311072efd5571724fbe00d12a96921054fa2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="deployment-and-testing"></a>部署和测试

## <a name="deployment-checklist"></a>部署清单

是否要将部署到测试监视，或上载到应用商店，你需要完成此页上的步骤：

- 在**iOS 开发人员中心**:
  - [应用 Id](#App_IDs)已创建。
  - [应用组](#App_Groups)配置 （如果需要）。
  - [*分发*预配配置文件](#Provisioning_Profiles)创建。

- 在你的解决方案：

  - 验证[程序包 Id 和项目引用](~/ios/watchos/get-started/installation.md)设置。
  - 请检查您的图标是否[正确配置](~/ios/watchos/app-fundamentals/icons.md)。
  - 检查所有项目中的捆绑包版本数字匹配项。
  - 配置**Entitlements.plist**为应用程序组 （如果需要）。

* 然后按照说明：
  - [用于测试，将部署到 Apple Watch](~/ios/watchos/deploy-test/device.md)，或
  - [将上载到应用商店](~/ios/watchos/deploy-test/appstore.md)。


## <a name="app-ids"></a>应用 Id

中所述[安装说明进行操作](~/ios/watchos/get-started/installation.md)，在监视应用程序中的所有三个项目具有相关程序包 Id，如：

- Xamarin.iOS 统一项目- `com.xamarin.WatchKitCatalog`
- WatchKit 扩展项目- `com.xamarin.WatchKitCatalog.watchkitextension`
- 监视应用程序项目的 `com.xamarin.WatchKitCatalog.watchkitapp`

所有三个项目需要匹配分发预配配置文件，使用显式应用 Id 对于每个或通配符应用 id。

### <a name="explicit-app-ids"></a>显式应用 Id

创建**应用程序 ID**为每个每个项目的捆绑 ID （它将在 iOS 开发人员中心上如下所示）：

![IOS 开发人员中心中的捆绑 Id](images/appids-specific-sml.png)

在创建或配置应用程序 Id，请记得以启用你的应用需要的特定功能。 这可能包括推送通知和应用程序组。

你将需要为每个应用程序 id。 创建分发预配配置文件

### <a name="wildcard-app-id"></a>通配符应用 ID

或者，可以创建通配符**应用程序 ID**匹配所有三个项目，如`com.xamarin.*`。

请注意，某些功能不能使用通配符应用程序 ID （如推送通知）。 如果你的应用需要这些功能，则应创建显式应用 Id。

对于分发，你只需要创建一个分发预配配置文件的通配符应用 id。

<a name="app-groups" />

## <a name="app-groups"></a>应用组

应用组可用于监视扩展你的 iOS 应用之间共享数据。 你应确保你的解决方案有：

- 配置**应用程序组**Apple 开发人员门户中**证书、 标识符和配置文件**部分。

- 启用**应用组**(和提供**应用组 ID**) 中*同时*iOS 应用和监视扩展**应用程序 ID**和**Entitlements.plist**。

### <a name="certificates-identifiers--profiles"></a>证书、 标识符和配置文件

若要使用应用程序组，创建将项记入**应用组**屏幕。 在以下示例中的组名与常用的应用程序 Id，但使用的相同反向 DNS 样式`group.`前缀 （这是必需）：

![标识符](images/appgroups-new-sml.png)

然后，应用程序组将显示在列表中：

![标识符列表](images/appgroups-setup-sml.png)

它在中创建组后，可以将引用你**应用程序 ID**配置。 请记住将它包含两个 iOS 应用程序和监视扩展**应用 Id**。

![可用 consifurations](images/appgroups-sml.png)

执行**不**启用应用组在 Apple Watch 应用 id 中。 不需要在手表上启用。

### <a name="entitlementsplist"></a>Entitlements.plist

某些应用程序功能 （如。 应用程序组） 要求你设置你的权利。
双击可编辑**Entitlements.plist**这些项目文件中：

- iOS 应用程序项目
- 监视扩展项目

.![Entitlements.plist 编辑器](images/entitlements-plist-sml.png)

执行**不**启用监视应用程序项目中的权利。 不需要在手表上启用。



## <a name="related-links"></a>相关链接

- [Apple WatchKit 提交指南](https://developer.apple.com/app-store/watch/)
