---
title: 适用于 Mac 连接 Visual Studio 中的服务
description: 添加从 Visual Studio 中的移动应用中添加适用于 Mac Azure 数据存储、 身份验证和推送通知
ms.prod: xamarin
ms.assetid: ADDFB3A5-DB6A-417C-8ACE-33D107FB75C2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: b9aaa197ccf01c59d6e4bbb0476d10295a108f89
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="connected-services-walkthrough"></a>连接的服务演练

因此，无需离开你项目中以增加服务后，连接服务工作流将 Azure 门户的工作流到 Visual Studio 引入适用于 Mac 的。

本演练演示如何添加 Azure 后端服务，这将提供云数据存储、 身份验证和将通知推送到跨平台 Xamarin.Forms 可移植类库 (PCL) 应用程序。


1.  通过双击上启动**连接服务**解决方案，将显示中的节点**服务库**。
  这是此应用程序类型的所有可用服务的列表。 选择一项服务 (如**移动后的端使用 Azure App Service**) 通过单击它。

  [![](connected-services-images/image001-sml.png "适用于 Mac 连接 Visual Studio 中的服务节点")](connected-services-images/image001.png#lightbox)

2. 服务详细信息页提供服务和依赖项，以安装了的描述。
  单击**添加**按钮添加到应用程序的依赖项：

  [![](connected-services-images/image002-sml.png "使用 Azure 移动后端")](connected-services-images/image002.png#lightbox)

3. 依赖项需要添加到 PCL 和特定于平台的项目才能起作用。
  选择复选框以将服务添加到 （直接或间接） 引用它的每个项目：

  [![](connected-services-images/image003-sml.png "检查应引用该服务的所有项目")](connected-services-images/image003.png#lightbox)

4. 选择**接受**上**许可证接受**对话框用于 NuGet 包。
  可能有两个对话框，以便接受，一个 MobileClient 和依赖项，而另一个 sqlitestore，这是进行脱机数据同步的要求：

  [![](connected-services-images/image004-sml.png "接受许可协议")](connected-services-images/image004.png#lightbox)

  ![](connected-services-images/image005.png "许可证接受窗口")

5. 添加依赖关系后，你将需要你想要使用与 Azure 进行通信的帐户登录。
  如果已使用 Microsoft ID 登录，适用于 Mac 的 Visual Studio 将尝试提取你的 Azure 订阅和与其关联的任何应用程序服务。 如果你没有任何订阅，你可以通过注册免费试用版或购买订阅计划在 Azure 门户中的添加。

6. 从列表中选择应用程序服务。 这将填充的模板代码`MobileServiceClient`替换在 Azure 上的应用程序服务的相应 URL 的对象：

  [![](connected-services-images/image006-sml.png "从列表中选择应用程序服务")](connected-services-images/image006.png#lightbox)

  如果不没有列出任何服务，请单击**新建**按钮 （请参阅步骤 9。）

7. 将复制的模板代码`MobileServiceClient`到 PCL 中。 中的文件位置应用不重要，只要只有一个实例。
  建议的方法是创建`AzureService`类处理所有 Azure 交互并使用`MobileServiceClient`:

  ![](connected-services-images/image007.png "将配置代码复制到应用程序")

8. 请按照中的文档**后续步骤**添加数据、 脱机同步、 身份验证，并将通知推送到你的应用程序：

  [![](connected-services-images/image008-sml.png "查看下一步的步骤说明")](connected-services-images/image008.png#lightbox)

10. 如果你没有任何现有的应用程序服务，可以创建从 Visual Studio 中的新服务的 mac。
  单击**新建**按钮在左下角的服务列表以打开**新 App Service**对话框：

  [![](connected-services-images/image009-sml.png "Mac 的 Visual Studio 中创建新的应用程序服务")](connected-services-images/image009.png#lightbox)

新的服务需要以下参数：

-   **App service 名称**– 计划的唯一名称 /id
-   **订阅**– 你想要用来为服务付款的订阅
-   **资源组**– 方式还是组织项目的所有 Azure 资源。 若要使用现有或创建一个新的选项。 如果这是第一个 Azure 服务，创建一个新。
-   **服务计划**– 确定的位置和使用它的任何资源的成本。 若要使用现有或创建一个新的选项。 如果这是第一个 Azure 服务，使用默认或创建一个新免费层 (F1)。

请访问[Azure App Service 文档](https://docs.microsoft.com/azure/app-service/)有关详细信息


## <a name="related-links"></a>相关链接

- [Azure 应用服务](https://docs.microsoft.com/en-us/azure/app-service/)
- [发行说明](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Connected_Services)
