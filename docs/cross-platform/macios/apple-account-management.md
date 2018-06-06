---
title: Apple 帐户管理
description: 本文档介绍如何在 Visual Studio 中使用的 Apple 帐户管理功能，针对 Mac 和 Visual Studio 2017。
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: asb3993
ms.author: amburns
ms.date: 05/06/2018
ms.openlocfilehash: f77ab1c48e3200088d8c582634921df1ecf1001c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781502"
---
# <a name="apple-account-management"></a>Apple 帐户管理

Apple 帐户管理界面使您能够查看所有开发团队与 Apple id。 它还允许你通过显示的列表中查看有关每个团队的更多详细信息_的签名标识_和_预配配置文件_您的计算机上安装。

在命令行执行身份验证的你的 Apple ID [fastlane](https://fastlane.tools/)。 fastlane 必须安装在计算机中，以便你可以成功进行身份验证。 Fastlane 以及如何安装它的详细信息进行了详细[fastlane](~/ios/deploy-test/provisioning/fastlane/index.md)指南。

Apple 帐户对话框允许你执行以下操作：

* **创建和管理证书** 
* **创建和管理预配配置文件** 

在本指南中介绍了如何执行此操作的信息。

你还可用于 iOS 自动预配工具自动创建和管理你的签名标识、 应用 Id 和预配配置文件。

有关使用这些功能的详细信息，请参阅[设备资源调配](~/ios/get-started/installation/device-provisioning/index.md)指南。
️
## <a name="requirements"></a>要求

Apple 帐户管理位于 Visual Studio for Mac 和 Visual Studio 2017 （15.7 和更高版本）

您必须拥有 Apple 开发人员帐户，才能使用此功能。 中提供了对 Apple 开发人员帐户的详细信息[设备资源调配](~/ios/get-started/installation/device-provisioning/index.md)指南。

- 确保已连接到 internet。 这是因为 fastlane 直接与 Apple 开发人员门户进行通信。
- 确保你具有[fastlane 工具安装](~/ios/deploy-test/provisioning/fastlane/index.md#Installation)。
- 确保你具有的最新 fastlane 工具[ https://download.fastlane.tools ](https://download.fastlane.tools)。
- 在开始之前，请确保接受中的任何用户许可协议[开发人员门户](https://developer.apple.com/account/)。

## <a name="adding-an-apple-developer-account"></a>添加 Apple 开发人员帐户

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 若要打开帐户管理对话框，请转到**Visual Studio > 首选项 > Apple 开发人员帐户**:

    ![Apple 开发人员帐户选项](apple-account-management-images/image1.png)

2. 按**+** 按钮以显示登录对话框中，如下所示： 

    ![fastlane 对话框。](apple-account-management-images/image2.png)

4. 输入你的 Apple ID 和密码，然后单击**登录**按钮。 这将在此计算机上将你的凭据保存在安全密钥链中。 [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md)用于安全地处理你的凭据并将它们传递给 Apple 的开发人员门户。
 
5. 选择**始终允许**在警报对话框以允许 Visual Studio 以使用你的凭据：

    ![始终允许警报对话框](apple-account-management-images/image4.png)

6. 一旦已成功添加你的帐户，你将看到你的 Apple ID 和你的 Apple ID 属于任何团队。

    ![与添加的帐户的 Apple 开发人员帐户对话框](apple-account-management-images/image5.png)

7. 选择任何团队和按**查看详细信息...** 按钮。 此时将显示所有的签名标识和您的计算机安装预配配置文件的列表：

    ![视图的详细信息屏幕显示签名标识和预配您的计算机上的配置文件](apple-account-management-images/image6.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在开始将你的 Apple ID 添加到 Visual Studio 2017 之前，请确保您开发的环境是否[与 Mac 生成主机配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 若要打开帐户管理窗口，请转到**工具 > 选项 > Xamarin > Apple 帐户**:

    ![Apple 帐户选项屏幕](apple-account-management-images/prov1.png)

1. 选择**添加**按钮，然后输入你的 Apple ID 和密码：

    ![用户名和密码的对话框](apple-account-management-images/prov1a.png)

1. 一旦已成功添加你的帐户，你将看到你的 Apple ID 和你的 Apple ID 属于任何团队。
 
1. 选择任何团队和按**查看详细信息...** 按钮。 此时将显示所有的签名标识和您的计算机安装预配配置文件的列表：

    ![用户名和密码的对话框](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>管理的签名标识和预配配置文件

团队详细信息对话框中显示签名标识，按类型的列表。 **状态**列建议您证书是否： 

* **有效**– 在计算机上安装的签名标识 （证书和私钥），并且它尚未过期。

* **不在 Keychain** – Apple 的服务器上没有有效的签名标识。 您的计算机上安装此，它必须导出从另一台计算机。 你无法从 Apple 开发人员门户下载的签名标识，因为它将不包含私钥。

* **私钥是缺少**– 在 keychain 中安装与没有私钥的证书。

* **过期**– 证书已过期。 你应从你的密钥链中删除此。

  ![团队详细信息对话框信息](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>创建签名标识

若要创建新的签名标识，选择**创建证书**下拉列表按钮，然后选择你需要的类型。 如果您具有正确权限新的签名标识将显示几秒钟后。

如果下拉列表中的一个选项是灰显，并且未选定该属性，这意味着你没有正确的团队权限来创建此类型的证书。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![创建证书选项](apple-account-management-images/image8.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![创建证书选项](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>下载预配配置文件

团队详细信息对话框还显示连接到你的开发人员帐户的所有预配配置文件的列表。 你可以通过按所有预配配置文件下载到本地计算机**下载所有配置文件**按钮

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![下载预配配置文件部分](apple-account-management-images/image9.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![下载预配配置文件部分](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>iOS 捆绑签名

有关将应用程序部署到设备的信息，请参阅[设备资源调配](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="troubleshooting"></a>疑难解答

### <a name="view-details-dialog-is-empty"></a>视图的详细信息对话框为空

这当前是一个已知的问题，与 bug 相关[#53906](https://bugzilla.xamarin.com/show_bug.cgi?id=53906)。 请确保 Mac 使用 Visual Studio 的最新稳定版本

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>如果你遇到的问题在你的帐户登录，请尝试以下操作：

* 打开 keychain 应用程序，然后在类别下选择*密码*。 搜索`deliver.`，并删除所有条目。

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>"添加帐户时出错。 请使用特定于应用程序密码登录"

这是因为在你的帐户上启用 2 因素身份验证。 请确保 Mac 使用 Visual Studio 的最新稳定版本

### <a name="failed-to-create-new-certificate"></a>无法创建新证书
"你已达到为此类型的证书限制"

![限制证书对话框](apple-account-management-images/image10.png)

已生成证书允许的最大数目。 若要解决此问题，浏览到[Apple 开发人员中心](https://developer.apple.com/account/ios/certificate/distribution)和撤消生产证书之一。

## <a name="known-issues"></a>已知问题

* 默认情况下，分发预配配置文件将面向应用商店。 应手动创建内部配置文件或临时配置文件。
