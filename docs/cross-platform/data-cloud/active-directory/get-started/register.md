---
title: 步骤 1。 注册应用程序，以便使用 Azure Active Directory
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c9a44a35e91e6368522f8632e185bd8a554d8593
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>步骤 1。 注册应用程序，以便使用 Azure Active Directory

1. 导航到[windowsazure.com](https://manage.windowsazure.com)并使用你的 Microsoft 帐户或在 Azure 门户中的组织帐户登录。 如果你没有 Azure 订阅，你可以获取从试用版[azure.com](http://www.azure.com)

2. 在登录后，请转到**Active Directory** （1） 部分，然后选择想要注册的应用程序 (2) 的目录

  [ ![](register-images/01.-active-directory-in-azure-portal-sml.jpg "部分，然后选择要注册该应用程序的目录")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. 单击**添加**若要创建新的应用程序，然后选择**添加我的组织正在开发的应用程序**

  [ ![](register-images/02.-add-new-application-sml.jpg "添加我的组织正在开发的应用程序")](register-images/02.-add-new-application.jpg#lightbox)

4. 在下一个屏幕上，为你的应用程序指定名称 （例如。 XAM-DEMO)。
  请确保选择**本机客户端应用程序**作为应用程序的类型。

  ![](register-images/03.-app-name.jpg "请确保你选择作为应用程序的类型的本机客户端应用程序")

5. 在最终屏幕上，提供 **重定向 URI*作为身份验证完成时它将返回到此 URI，这是唯一的应用程序。

  ![](register-images/04.-app-redirect.jpg "在最终屏幕上，提供身份验证完成时，将返回到此 URI 是唯一的应用程序的重定向 URI")

6. 创建应用程序后，导航到**配置**选项卡。记下**客户端 ID**我们将用在我们的应用程序更高版本。 此外，在此屏幕上可以为 Active Directory 提供你的移动应用程序访问权限或添加另一个应用程序，如 Web API 或 Office365，身份验证完成后，移动应用程序可以使用。

    ![](register-images/05.-configure.jpg "此外，在此屏幕上你可以为 Active Directory 提供你的移动应用程序访问权限或添加另一个应用程序，如 Web API 或 Office365")



## <a name="related-links"></a>相关链接

- [Microsoft NativeClient 示例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
