---
title: 步骤 2。 为移动应用程序配置服务访问
description: 本文档介绍如何向 Xamarin 应用程序提供访问由 Azure Active Directory 保护的 Azure 应用程序。
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 2a9baab9215ae2d30e4daf6800a116c95165da42
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780095"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>步骤 2。 为移动应用程序配置服务访问

每当任何资源例如 web 应用程序、 web 服务等需要由 Azure Active Directory 保护，它需要注册。 所有安全应用程序或服务，可以查看下**应用程序**选项卡。在此处你可以选择从移动应用程序访问，并为其赋予访问所需要的应用程序。

1. 上**配置**选项卡上，找到**其他应用程序的权限**部分：

  ![](configure-images/2.1-configure.png "在配置选项卡，找到对其他应用程序的部分的权限")

2.  单击**添加应用程序**按钮。 在弹出的下一个屏幕中，你会看到所有由 Azure Active Directory 保护的应用程序的列表。 选择的应用程序需要从移动应用程序访问。

  ![](configure-images/2.2-add-application.png "选择的应用程序需要从移动应用程序访问")

3. 选择应用程序之后, 再一次选择中的新增的应用程序**其他应用程序的权限**部分，并提供适当的权限。

  ![](configure-images/2.3-permissions.png "选择应用程序后, 再一次选择新添加的应用程序中对其他应用程序的部分的权限，然后指定适当的权限")

4. 最后，**保存**配置。 这些服务应可在移动应用程序 ！



## <a name="related-links"></a>相关链接

- [Microsoft NativeClient 示例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
