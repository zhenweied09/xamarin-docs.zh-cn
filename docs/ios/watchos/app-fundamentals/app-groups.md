---
title: "使用应用组"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 9365bc2707876816419bb5d136a6a1011cf129d7
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="working-with-app-groups"></a>使用应用组


应用组允许不同的应用程序（或一个应用程序及其扩展）访问共享文件存储位置。 应用组可以用于如下所示的数据：

- Apple Watch[设置](~/ios/watchos/app-fundamentals/settings.md)。
- 共享[使用 NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults)。
- 共享[文件](~/ios/watchos/app-fundamentals/parent-app.md#files)。

## <a name="configure-an-app-group"></a>配置应用组

通过[应用组](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)配置共享位置，这是在 [iOS 开发中心](https://developer.apple.com/devcenter/ios/)的**证书、标识符和描述文件**部分配置的。 还必须在每个项目的“Entitlements.plist”中引用此值。

### <a name="provisioning"></a>预配

应用程序组将具有的标识符，这通常是捆绑包 ID 与`group.`前缀。 例如，我们可以使用捆绑 ID`com.xamarin.WatchSettings`和应用程序组`group.com.xamarin.WatchSettings`。

[![](app-groups-images/app-group-sml.png "使用捆绑 ID com.xamarin.WatchSettings 和应用程序组 group.com.xamarin.WatchSettings")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

配置预配配置文件，以及**启用应用组**中**Entitlements.plist**并输入你选择的 ID:

[![](app-groups-images/entitlements-sml.png "配置 plist 和输入的 ID")](app-groups-images/entitlements.png#lightbox)


### <a name="deployment"></a>部署

确保配置正确在应用程序组你[部署](~/ios/watchos/deploy-test/index.md#App_Groups)设置。


有关详细信息，请参阅[应用组功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文档。


## <a name="related-links"></a>相关链接

- [Apple 的与应用程序包含共享数据](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Apple 的应用程序组文档](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
