---
title: 免费预配
description: 随着 Apple Xcode 7 的发布，为所有 iOS 和 Mac 开发人员带来了重大改变 - 免费预配。
ms.prod: xamarin
ms.assetid: A5CE2ECF-8057-49ED-8393-EB0C5977FE4C
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 03/19/2017
ms.openlocfilehash: 09244ebaefedb991289e5a1f67b59491ee84ed28
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="free-provisioning"></a>免费预配

_随着 Apple Xcode 7 的发布，为所有 iOS 和 Mac 开发人员带来了重大改变 - 免费预配。_

免费预配允许开发人员将 Xamarin.iOS 应用程序部署到 iOS 设备，**无需**成为**Apple 开发者计划**的一员。 这对开发人员而言非常有益，因为在设备测试比在模拟器上测试优点更多，包括但不仅限于内存、存储、网络连接等。

如果不使用 Apple 开发人员帐户进行设置，则必须通过 Xcode 执行，这会创建签名标识（包含开发人员证书和私钥）和配置文件（包含显式应用程序 ID 和连接的 iOS 设备的 UDID）。

## <a name="requirements"></a>惠?

若要利用通过免费预配将 Xamarin.iOS 应用程序部署到设备这一点，必须使用 Xcode 7 或更高版本。

**正在使用的 Apple ID 不得连接任何 Apple 开发者计划。**

应用中使用的捆绑 ID 必须唯一，且不能曾在其他应用中使用过。 使用免费预配的任何捆绑 ID 都不能再次使用。 如果已分配某应用，将无法通过免费预配对该应用进行预配。 

有关详细信息，请访问[应用分发指南](~/ios/deploy-test/app-distribution/index.md)。

如果应用使用应用程序服务，则需要创建配置文件，如[设备预配](~/ios/get-started/installation/device-provisioning/index.md#appservices)指南中详述。 可在以下[相关部分](#limitations)查看更多限制。


## <a name="a-namelaunching--launching-your-app"></a><a name="launching" /> 启动应用

若要使用免费预配将应用程序部署到设备，需使用 Xcode 创建签名标识和配置文件，然后使用 Visual Studio for Mac 或 Visual Studio 选择正确的配置文件对应用进行签名。 要执行此操作，请遵循以下分步演练操作：

1. 如果没有 Apple ID，请在 [appleid.apple.com](https://appleid.apple.com/account) 处创建一个。
2. 打开 Xcode 并浏览到“Xcode”>“首选项”。
3. 在“帐户”下，使用 **+** 按钮添加现有 Apple ID。 它应类似于下面的屏幕截图：

  [![](free-provisioning-images/launchapp1.png "Xcode 首选项帐户")](free-provisioning-images/launchapp1.png#lightbox)

4. 插入想要部署到的 iOS 设备，并在 Xcode 中创建新的单视图空 iOS 项目。 将“团队”下拉菜单设置为刚添加的 Apple ID。 它应该是类似于 `your name (Personal Team - your Apple ID)` 的格式：

  [![](free-provisioning-images/launchapp2.png "创建签名标识")](free-provisioning-images/launchapp2.png#lightbox)

5. 在“常规”>“标识”部分，确保捆绑标识符完全匹配 Xamarin.iOS 应用捆绑标识符，并确保部署目标匹配或低于连接的 iOS 设备。 由于 Xcode 只通过显式应用程序 ID 创建配置文件，因此这一步非常重要：

  [![](free-provisioning-images/launchapp5.png "使用显式应用 ID 创建预配配置文件")](free-provisioning-images/launchapp5.png#lightbox)

6. 在“签名”部分，选择“自动管理签名”并从下拉列表中选择团队：

  [![](free-provisioning-images/launchapp6.png "选择“自动管理签名”，并从下拉列表中选择团队")](free-provisioning-images/launchapp6.png#lightbox)

7. 上一步将自动生成预配配置文件和签名标识。 可通过单击预配配置文件旁的信息图标查看该文件：

  [![](free-provisioning-images/launchapp7.png "查看预配配置文件")](free-provisioning-images/launchapp7.png#lightbox)

8. 若要在 Xcode 中测试，通过单击运行按钮将空应用程序部署到设备。

9. 返回到 IDE，插入同一台设备，右键单击 Xamarin.iOS 项目名称，打开“项目选项”对话框。 浏览到“iOS 捆绑签名”部分，并显式设置签名标识和配置文件：

  [![](free-provisioning-images/launchapp8.png "设置签名标识和预配配置文件")](free-provisioning-images/launchapp8.png#lightbox)

如果不能在 IDE 中看到签名标识或正确的配置文件，可能需要重启它。


## <a name="a-namelimitations-limitations"></a><a name="limitations" />限制

Apple 施加了一些限制，限制什么时候、以什么方式才能使用免费预配在 iOS 设备上运行应用程序，确保你只能部署到你自己的设备。 本部分中列出了这些限制。

还限制了对 iTunes Connect 的访问，因此，未对免费预配应用程序的开发人员提供某些服务（如发布到 App Store 和 TestFlight）。 要通过 Ad Hoc 和 In-House 方式进行分配，需要 Apple 开发人员帐户（企业或个人）。

以这种方式创建的配置文件一周后过期，签名标识一年后过期。 此外，只有使用显式应用程序 ID 才能创建配置文件，因此对于每个希望安装的应用，都需遵循[上述](#launching)说明。

也不可能通过免费预配对大多数应用程序服务进行预配。 这包括：

- Apple Pay
- Game Center
- iCloud
- 应用内购买
- 推送通知
- 电子钱包（以前是 Passbook）

在[支持的功能](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/SupportedCapabilities/SupportedCapabilities.html#//apple_ref/doc/uid/TP40012582-CH38-SW1)指南中，Apple 对此提供了完整的列表。 若要预配应用，将其用于应用程序服务，请访问[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。


## <a name="summary"></a>总结

本指南探讨了使用免费预配在 iOS 设备上安装应用程序的优点和限制。 还逐步介绍了如何使用免费预配安装 Xamarin.iOS 应用。

## <a name="related-links"></a>相关链接

- [设备预配](~/ios/get-started/installation/device-provisioning/index.md)
- [为应用程序服务进行预配](~/ios/get-started/installation/device-provisioning/index.md#appservices)
